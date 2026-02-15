# Node Dependency Policy

## Dependency Classification

Place in `dependencies`:
- Framework and runtime libs used in running app code.
- Server adapters and SDKs used at runtime.

Place in `devDependencies`:
- TypeScript, ESLint, Prettier, test frameworks, build-only plugins.

## Minimum CI Commands

### npm

```bash
npm ci
npm run build
npm ci --omit=dev
npm run start
```

### pnpm

```bash
pnpm install --frozen-lockfile
pnpm run build
pnpm install --frozen-lockfile --prod
pnpm run start
```

### yarn

```bash
yarn install --frozen-lockfile
yarn build
yarn install --frozen-lockfile --production=true
yarn start
```

## Docker Multi-stage Pattern

```dockerfile
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:20-alpine AS runtime
WORKDIR /app
COPY package*.json ./
RUN npm ci --omit=dev
COPY --from=builder /app/dist ./dist
CMD ["node", "dist/index.js"]
```

## Fast Validation Commands

```bash
# Confirm runtime tree
npm ls --omit=dev --depth=0

# Optional audit gate
npm audit --omit=dev
```

## Common Failure Signals

- `Cannot find module ...` in production startup means dependency classification is wrong.
- Build works locally but fails in CI usually means lockfile drift or undeclared transitive dependency reliance.
- Runtime image crash after prune means runtime package was incorrectly placed in dev dependencies.
