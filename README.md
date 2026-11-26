# Movies1 Frontend

React + Vite frontend. The dependency metadata has been repaired: Vite is pinned to the Vite 6 line supported by the existing Tailwind Vite plugin, and `package-lock.json` has been regenerated to match `package.json`.

## Run locally

Requirements: Node.js 20.19+ or 22.12+ and npm.

```bash
npm ci
cp .env.example .env
# Edit .env and set VITE_BACKEND_URL to your backend's public HTTPS URL.
npm run dev
```

To verify a production build:

```bash
npm ci
npm run build
```

The production files are written to `dist/`.

## Deploy to Render

This repository includes `render.yaml` for a Render Static Site. The site uses `npm ci && npm run build`, publishes `dist/`, and rewrites app routes to `index.html` so React Router links work when opened directly.

1. Put this project in a GitHub repository. Do not commit `.env`; it is ignored by Git. Commit `.env.example` instead.
2. In Render, choose **New + → Blueprint**, connect the GitHub repository, and apply the Blueprint. Alternatively, choose **New + → Static Site** and enter:
   - Build command: `npm ci && npm run build`
   - Publish directory: `dist`
3. When Render asks for `VITE_BACKEND_URL`, enter the public HTTPS origin of the API/backend that serves this frontend (for example, `https://your-api.onrender.com`). Do not include `/api` unless your backend is specifically configured that way. This variable is embedded during the build, so redeploy after changing it.
4. Wait for the deploy to finish, then open the `onrender.com` URL Render provides. Test the home page and a nested route by refreshing it directly.
5. If the backend restricts browser origins, add the new Render frontend URL to the backend's CORS allowlist. The frontend and backend are separate services; deploying this static site does not deploy the API.

`VITE_SITE_ORIGIN` is optional. Set it to the final canonical site origin (your Render URL or custom domain) in Render's environment settings if you want SEO canonical URLs to use a particular domain, then redeploy. The app otherwise uses the browser's current origin.

## Notes

- Never upload or commit a real `.env` file. Vite variables prefixed with `VITE_` are included in browser code and must not contain private credentials. Keep server-side secrets on the backend.
- `VITE_API_URL`, `VITE_SUPABASE_URL`, and `VITE_SUPABASE_ANON_KEY` from the original local environment file are not referenced by the current frontend source; only add variables that the code actually uses. If the Supabase key is needed in the future, use Supabase's public anon key and secure data with Row Level Security; never put a service-role key in a frontend variable.
- `middleware.js` and `vercel.json` are Vercel-specific and are not executed by Render Static Sites. The Render route fallback is configured separately in `render.yaml`.
- The verified production build succeeds. Vite currently reports a large JavaScript bundle warning; it is an optimization warning, not a build failure.
