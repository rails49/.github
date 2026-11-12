# A box runs an installation release of published images

Resolves [#71](https://github.com/rails49/.github/issues/71). Supersedes
[rails49/control ADR-0067](https://github.com/rails49/control/blob/main/docs/adr/0067-the-image-names-the-store-uid-at-build.md).

Every stack was built on the box: `deploy.sh` ran `git pull` and
`docker compose up --build` over ssh from a development machine. A person with
no programming experience cannot install or update a box that way, and a box
needed a clone, git and build tools for every stack. #71 settled how images
are built and released on 2026-11-11. The first full release, `2026.11.1`, was
checked on a 2018 Mac mini (Ubuntu 26.04, amd64) and a Raspberry Pi (arm64)
and marked on 2026-11-12.

## Decision

1. **A box runs one installation release.** It pins every stack's images by
   digest and carries each stack's box files with those digests written in. A
   box runs the stacks it declares from one release, and no stack is released
   on its own. Stacks are tested together, and a box needs no clone, git, node
   or build tool.
2. **Images are built in CI only when a release is made,** natively for amd64
   and arm64. No pull request or merge builds or pushes an image. A broken
   build is found at the next release, not at the change that broke it.
3. **Images are published, public, on GHCR.** Each is named by the commit it
   was built from, never moves, and carries the commit as
   `org.opencontainers.image.revision`. This is
   [dccex ADR-0005](https://github.com/rails49/dccex/blob/main/docs/adr/0005-the-image-is-named-by-the-commit-it-was-built-from.md)
   d.1 and d.4 for every stack, with the GHCR name. A box pulls without a
   credential.
4. **One image serves every box.** Nothing about a box is built into an image.
   control's store gets its passwd entry at start from `nss_wrapper`, for
   whatever uid it runs as, so the system tables stay root's and nothing runs
   with privilege. ADR-0067 built the deploying account's uid into the image,
   which held only while images were built on the box and never pushed. JMRI
   is still built on the box, over a base pinned by digest.
5. **Each stack owns its build.** The installation's release workflow calls
   each stack's own image workflow at the chosen commit, and reuses an image
   already pushed for that commit.
6. **A release is checked twice before a box is offered it.** CI starts it
   from its own files on both architectures, with the simulator and no
   hardware; a failure means no release. Then it is a pre-release, installed
   by hand on a 2018 Mac mini and a Raspberry Pi and run with trains. Marking
   it a full release is what offers it to every other box. CI cannot run trains.
7. **The development loop still builds on the box.** `deploy.sh` stays in
   every stack, and each compose file carries both `image:` and `build:`. A
   local build is named by its commit like a published one and reaches a box
   only by being built there. A CI outage blocks a release, never a deploy.

## Consequences

- control's ADR-0067 gets a superseded line.
- dccex's ADR-0005 is not superseded. Its deploy record and going back (d.5
  to d.7) still govern `deploy.sh`. Where its d.1 says `dccex:<commit>`, the
  name is now the GHCR one.
- How a box installs, updates and goes back to a release is
  [installation#65](https://github.com/rails49/installation/issues/65)'s.
  Until that is built, the hand check installs a release by hand.
- The image names, the release's assets and the CI check are in installation's
  [README](https://github.com/rails49/installation#making-a-release).

## Considered

- **Build on every merge to `main`.** It finds a broken build sooner, and
  spends runner time on images no box runs.
- **Release each stack on its own.** A box would run combinations of stacks
  nobody tested together.
- **Keep ADR-0067's build argument.** Each box would need its own images,
  built on it.
- **Private packages.** Every box would hold a credential to pull.
- **Ship local builds with `docker save` or a local registry.** More to run
  and learn, for what building on the box already does.
