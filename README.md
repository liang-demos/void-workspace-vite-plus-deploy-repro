# Void deploy misses Vite+ at the pnpm workspace root

The workspace root declares Vite+ and the app declares Void. From the app directory, its build script resolves the workspace's `vp` executable and builds a Worker. With the default build configuration, `void deploy` tries to run `vite build` instead.

## Reproduce

Use Node.js 26.13.1 and pnpm 12.21.0, with no global `vite` on PATH. From the repository root, run:

```sh
pnpm install --frozen-lockfile
cd packages/app
pnpm run build
CI=1 \
  CLOUDFLARE_ACCOUNT_ID=00000000000000000000000000000000 \
  CLOUDFLARE_API_TOKEN=repro-placeholder \
  pnpm exec void deploy --platform cloudflare
```

The account ID and token are placeholders. No Cloudflare account is needed to reach the failing build step.

## Actual result

`pnpm run build` succeeds. With Void 1.11.0 and Vite+ 1.7.0, the deploy command exits with status 127 on macOS:

```text
void-repro-app → Cloudflare

/bin/sh: vite: command not found
■ deploy: build failed — Command failed: vite build
```

## Expected result

Deploy detects the workspace-root Vite+ installation and builds the same Worker using `vp build`.
