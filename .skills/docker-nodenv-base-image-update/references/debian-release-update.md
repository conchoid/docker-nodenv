# Dockerfile base image update reference

Repository: `docker-nodenv`

## Standard pattern

- Update Dockerfile stages from `node:<version>-bookworm-slim` to `node:<version>-trixie-slim`
- Apply the same suite change to the final `FROM` line
- Build with the repository root as context

## Example from the original procedure

- Source Dockerfile: `24.2.0-bookworm/Dockerfile`
- Target Dockerfile: `24.2.0-trixie/Dockerfile`
- Example build:

```bash
cd docker-nodenv
docker build -t conchoid/docker-nodenv:v1.5.0-1-24.2.0-trixie -f 24.2.0-trixie/Dockerfile .
```

## Checklist

- Update all stage images and the final image to the new Debian suite.
- Preserve required `apt-get` libraries unless incompatibility is confirmed.
- Verify the default system Node version matches the latest official LTS line.
- Verify `nodenv` and `node-build` still function.
- Verify all intended Node.js versions install correctly.
- Verify `npm` and `pnpm` are available.
- Verify locale-related behavior.
- Verify a real Node.js project can install dependencies, build, and run.

## Cautions

- Debian release changes can affect package names and versions.
- The "latest LTS" Node.js line changes over time, so do not hardcode it in the skill workflow.
- If the update fails, check package availability, shell/profile setup, and Node version pin validity first.
