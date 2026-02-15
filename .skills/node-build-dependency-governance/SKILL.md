---
name: node-build-dependency-governance
description: Enforce dependency declaration and reproducible build policy for Node projects. Use when reviewing package manifests, CI build pipelines, Dockerfiles, lockfiles, or any build or deploy readiness task involving dependencies and runtime exposure.
---

# Node Build Dependency Governance

Use this skill to guarantee that builds are reproducible and runtime dependencies are declared correctly.

## Mandatory Rules

1. Keep runtime dependencies in `dependencies`.
- Any package imported by runtime code must be in `dependencies`.
- Packages used only for lint, test, typecheck, and build tooling must stay in `devDependencies`.

2. Keep one package manager and one lockfile.
- Use one of `package-lock.json`, `pnpm-lock.yaml`, or `yarn.lock`.
- Do not mix lockfiles in the same project.

3. Use deterministic install commands in CI.
- npm: `npm ci`
- pnpm: `pnpm install --frozen-lockfile`
- yarn: `yarn install --frozen-lockfile`

4. Validate build in clean environment.
- Run install from lockfile.
- Run build command.
- Run production install simulation (`--omit=dev` or equivalent) for runtime targets.

5. Enforce deployment-ready artifact checks.
- Build must succeed without implicit global packages.
- Runtime startup must not depend on `devDependencies`.
- Lockfile must be committed and synchronized with manifest.

## CI Gate Checklist

- Lockfile present and up to date.
- No missing package in manifest.
- Build command succeeds after clean install.
- Production-only dependency install succeeds.
- Startup smoke test passes with production-only dependencies.

## Implementation Workflow

1. Detect package manager from lockfile.
2. Run deterministic install.
3. Run lint, typecheck, and build.
4. Simulate production install and startup.
5. Move any runtime package from `devDependencies` to `dependencies` when needed.

## References

- Read `references/node-dependency-policy.md` for concrete commands and Docker patterns.
