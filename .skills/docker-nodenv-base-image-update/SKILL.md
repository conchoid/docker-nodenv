---
name: docker-nodenv-base-image-update
description: Updates Dockerfiles in the docker-nodenv repository for a new Debian base image release by switching all node:<version>-<suite>-slim stages to the new Debian suite, keeping required apt packages, checking the default Node version against the latest official LTS release, and validating nodenv/node-build/npm/pnpm behavior. Use when asked to move docker-nodenv from bookworm to trixie or add a new Debian-based image variant.
---

# docker-nodenv-base-image-update

Use this skill when updating `docker-nodenv` to a new Debian release such as `bookworm` to `trixie`.

## Workflow

1. Identify the target Dockerfile or target version directory.
   Example: `24.2.0-bookworm/Dockerfile` -> `24.2.0-trixie/Dockerfile`.
2. If the new directory does not exist, create it and copy the previous Dockerfile into it.
3. Update every Node stage and the final base image from `node:<version>-<old-suite>-slim` to `node:<version>-<new-suite>-slim`.
4. Keep the installed `apt-get` packages unless a compatibility issue is confirmed. Do not silently remove libraries.
5. Determine the default system Node version from the latest official Node.js LTS line at the time of the change.
   - Verify against the official Node.js releases page.
   - If the current default is not the latest LTS, update it deliberately.
6. Build the image locally.
   ```bash
   cd docker-nodenv
   docker build -t conchoid/docker-nodenv:<target-tag> -f <target-dir>/Dockerfile .
   ```
7. Validate compatibility:
   - confirm all `apt-get` packages still resolve
   - confirm `nodenv` works
   - confirm `node-build` works
   - confirm each bundled Node.js version installs correctly
   - confirm `npm` and `pnpm` are available
   - confirm locale settings still work
8. If the repository has project-level or sample builds, run them with the new image and verify dependency installation, build success, runtime behavior, and `nodenv` version switching.

## Notes

- The "latest LTS" requirement is time-sensitive. Always verify it from the official Node.js site when performing the update.
- Prefer changing only the Debian suite first. Update Node version pins only when required by the user's request or the LTS policy.
- Read [references/debian-release-update.md](references/debian-release-update.md) for the repo-specific checklist and current example.
