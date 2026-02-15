# Vite Proxy Standard

This reference captures the secure pattern where React or Axios calls Vite on `/api`, and Vite forwards requests to backend in development.

## Canonical Dev Configuration

```ts
import { defineConfig, loadEnv } from 'vite';
import react from '@vitejs/plugin-react-swc';

export default defineConfig(({ mode }) => {
  const env = loadEnv(mode, process.cwd(), '');

  return {
    plugins: [react()],
    server: {
      proxy: {
        '/api': {
          target: env.VITE_API_PROXY_TARGET,
          changeOrigin: true,
          secure: false,
          ws: true,
          // Optional if backend does not expect /api prefix:
          // rewrite: (path) => path.replace(/^\/api/, ''),
        },
      },
    },
  };
});
```

## Canonical HTTP Client Pattern

```ts
import axios from 'axios';

export const api = axios.create({
  baseURL: '/api',
  timeout: 15000,
  withCredentials: true,
});

// Good
await api.post('/auth/login', payload);

// Avoid
// await axios.post(`${API_BASE_URL}/api/auth/login`, payload);
```

## Environment File Pattern

```bash
# .env.development
VITE_API_PROXY_TARGET=http://10.10.20.15:8080

# .env.production
VITE_PUBLIC_API_PREFIX=/api
```

Notes:
- In production, frontend should still call `/api`.
- Backend host mapping belongs to reverse proxy or gateway, not browser code.

## Production Routing Pattern

Use a reverse proxy to keep one public origin.

Example Nginx snippet:

```nginx
location /api/ {
  proxy_pass http://backend-internal:8080/;
  proxy_set_header Host $host;
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_set_header X-Forwarded-Proto $scheme;
}
```

## Intranet Hardening Notes

- Expose only frontend and reverse proxy ports to users.
- Keep backend service ports private.
- Restrict backend ingress to proxy host IPs.
- Avoid policy based only on hidden URLs; enforce network and auth controls.

## Source Links

- Vite env and mode docs: https://vite.dev/guide/env-and-mode
- Vite server proxy docs: https://vite.dev/config/server-options.html#server-proxy
- Context article used in this pattern: https://medium.com/@eric_abell/simplifying-api-proxies-in-vite-a-guide-to-vite-config-js-a5cc3a091a2f
