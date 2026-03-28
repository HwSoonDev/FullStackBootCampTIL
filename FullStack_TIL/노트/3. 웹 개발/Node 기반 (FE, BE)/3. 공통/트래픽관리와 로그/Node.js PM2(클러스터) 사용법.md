**Node.js는 기본적으로 클러스터링을 사용하지 않습니다.**

📌 기본 동작

- Node.js는 **싱글 스레드, 싱글 프로세스**로 동작합니다.
- 따라서 아무 설정을 하지 않으면, **하나의 CPU 코어만 사용**해요.
- 이게 Node.js의 특징이자 장점(단순성, 이벤트 기반 처리)인데, 동시에 성능 확장에는 제약이 됩니다.

⚙️ 클러스터링을 쓰려면?

- Node.js 자체에 **`cluster` 모듈**이 내장되어 있습니다.
- 하지만 이 모듈은 **자동으로 활성화되지 않고**, 개발자가 직접 코드에서 설정해야 합니다.

```js
import cluster from 'cluster';
import os from 'os';
import http from 'http';

if (cluster.isPrimary) {
  const numCPUs = os.cpus().length;
  console.log(`마스터 프로세스 ${process.pid} 실행 중`);

  // CPU 코어 수만큼 워커 생성
  for (let i = 0; i < numCPUs; i++) {
    cluster.fork();
  }

  // 워커 종료 시 새로 생성
  cluster.on('exit', (worker, code, signal) => {
    console.log(`워커 ${worker.process.pid} 종료됨`);
    console.log('새 워커 생성');
    cluster.fork();
  });
} else {
  // 워커 프로세스에서 서버 실행
  http.createServer((req, res) => {
    res.writeHead(200);
    res.end(`Hello from Worker ${process.pid}`);
  }).listen(8000);

  console.log(`워커 프로세스 ${process.pid} 실행 중`);
}
```

- 또는 **PM2 같은 프로세스 매니저**를 사용하면 `pm2 start app.js -i max` 명령어 한 줄로 클러스터 모드를 켤 수 있습니다.

✅ 정리

- Node.js는 기본적으로 **클러스터링을 사용하지 않는다**.
- 클러스터링을 쓰려면 **직접 설정하거나 PM2 같은 툴을 사용해야 한다**.
- 그래서 실무에서는 보통 **PM2 + 클러스터 모드**로 멀티코어 활용을 쉽게 적용합니다.

#### **express 적용**

```js
import cluster from 'cluster';
import os from 'os';
import express from 'express';

if (cluster.isPrimary) {
  const numCPUs = os.cpus().length;
  console.log(`마스터 프로세스 ${process.pid} 실행 중`);

  // CPU 코어 수만큼 워커 생성
  for (let i = 0; i < numCPUs; i++) {
    cluster.fork();
  }

  // 워커 종료 시 새로 생성
  cluster.on('exit', (worker, code, signal) => {
    console.log(`워커 ${worker.process.pid} 종료됨`);
    console.log('새 워커 생성');
    cluster.fork();
  });
} else {
  // 워커 프로세스에서 Express 서버 실행
  const app = express();

  app.get('/', (req, res) => {
    res.send(`Hello from Worker ${process.pid}`);
  });

  app.listen(8000, () => {
    console.log(`워커 프로세스 ${process.pid}에서 서버 실행 중`);
  });
}
```


# 동작 흐름 🔥

```js
import cluster from 'cluster';
import os from 'os';

if (cluster.isPrimary) {
  // Primary 프로세스: 워커 생성
  const numCPUs = os.cpus().length;
  for (let i = 0; i < numCPUs; i++) {
    cluster.fork();
  }
} else {
  // Worker 프로세스: 실제 서버 실행
  console.log(`워커 프로세스 ${process.pid} 실행 중`);
}
```

1) `cluster.isPrimary` 는 현재 클러스터가 마스터인지 판단한다.
2) 마스터면 하위 워커를 생성하고, (CPU 코어가 8개 -> 워커 8개 생성)
3) 각각의 워커에서 다시 `app.js`를 실행하면,else문으로 들어가서 실제 서버(express)를 실행하게 된다.

`app.js`는 `클러스터 마스터(Primary)`와 `하위 워커(Worker)`에서 동일하게 실행되는 데, 마스터에서는 워커 생성용으로 쓰이고, 워커에서는 실제 서버 실행용으로 쓰인다.