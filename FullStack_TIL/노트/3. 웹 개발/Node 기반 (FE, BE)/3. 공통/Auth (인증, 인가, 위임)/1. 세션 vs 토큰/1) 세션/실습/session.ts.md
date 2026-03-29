
```ts
//session.ts
import connectPgSimple from "connect-pg-simple";
import session from "express-session";
import { dbPool } from "../lib/db-pool.js";

const PgSession = connectPgSimple(session);

const sessionSecret = process.env.SESSION_SECRET;
if (process.env.NODE_ENV === "production" && !sessionSecret) {
  throw new Error("SESSION_SECRET is required in production");
}

type SameSiteOption = "lax" | "strict" | "none";

function parseSameSite(): SameSiteOption {
  const raw = process.env.SESSION_COOKIE_SAME_SITE?.toLowerCase();
  if (raw === "strict" || raw === "none" || raw === "lax") return raw;
  return "lax";
}

const sameSite = parseSameSite();
/** SameSite=None일 때는 반드시 Secure 필요 (로컬호스트는 브라우저가 예외적으로 허용하는 경우가 많음) */
const secureCookie =
  sameSite === "none" ? true : process.env.NODE_ENV === "production";

const cookieDomain = process.env.SESSION_COOKIE_DOMAIN?.trim() || undefined;

export const sessionMiddleware = session({
  store: new PgSession({
    pool: dbPool,
    tableName: "session",
    createTableIfMissing: true,
  }),
  name: "sid",
  secret: sessionSecret ?? "dev-session-secret",
  resave: false,
  saveUninitialized: false,
  proxy: process.env.TRUST_PROXY === "1" || process.env.TRUST_PROXY === "true",
  cookie: {
    httpOnly: true,
    secure: secureCookie,
    sameSite,
    maxAge: Number(process.env.SESSION_MAX_AGE_MS) || 1000 * 60 * 60 * 24 * 7,
    path: "/",
    ...(cookieDomain ? { domain: cookieDomain } : {}),
  },
});
```