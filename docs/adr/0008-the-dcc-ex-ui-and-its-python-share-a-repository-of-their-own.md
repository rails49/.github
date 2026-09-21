# The dcc-ex UI and its Python share a repository of their own

Resolves [Which repository holds the dcc-ex UI, and what builds
it](https://github.com/rails49/.github/issues/10) on the [UI
map](https://github.com/rails49/.github/issues/1).

The dcc-ex UI does not exist yet, and by now a good deal about it is settled.
It is a UI of its own rather than a fifth view of the control UI
([ADR-0001](0001-a-ui-of-its-own-is-about-something-other-than-the-loaded-railroad.md)).
It is served by the box the command station is plugged into, at an origin of
its own ([ADR-0004](0004-the-installation-serves-the-uis-that-are-about-it.md)),
reached at `dccex.$BOX_DOMAIN`
([ADR-0007](0007-an-installation-is-a-name-and-every-ui-is-a-label-under-it.md)).
It talks to the bus, the store's face and its own app's face, and nothing else
([ADR-0002](0002-a-ui-talks-to-the-bus-the-store-and-its-own-apps-face.md)).
It is bound by the look rules from its first commit
([ADR-0003](0003-the-look-rules-bind-place-colour-and-small-screens-not-code.md)),
which travel as a copied file rather than a package
([ADR-0005](0005-the-look-rules-travel-as-a-copied-file-not-a-package.md)).

What is left is where the source lives. Nothing shared constrains it, because
nothing is shared: ADR-0005 found six values and no plumbing worth moving. So
the question is open, and the map's standing rule applies — a new repository
is a cost and not a default, and an answer that adds one says why every
existing one fails.

## The two facts that decide it

**The Python is meant to leave `control`.** Moving `src/tc49/dccex` and
`src/tc49/dccex_usb` out is the owner's intent and an effort of its own, out
of scope for this map. It is not hypothetical, and a UI parked somewhere
convenient in the meantime would be moved by that same effort — two moves
where one will do.

**A box with a command station and no railroad is a real installation.** It is
the first thing someone owns: a command station on a cable, no cameras, no
railroad loaded. The whole point of ADR-0001's refusal to make this a control
view is that such a box exists. So whatever holds the UI must build and serve
it on a box with no `control` clone, or that box is a fiction and the refusal
bought nothing.

Licences then narrow the field, because they decide what can share a
repository with what. `control` is MIT, `occupancy` is AGPL-3.0, and
`CommandStation-EX` is GPLv3 — upstream's, not ours to choose.

## Decision

**One repository holds the dcc-ex project: `rails49/dccex`, MIT.** The UI
first; `src/tc49/dccex` and `src/tc49/dccex_usb` when their own effort moves
them. They are one project — one device on one cable, one box, and the UI's
entire subject is what those two processes do. MIT so the Python moves in
unchanged. The name matches the packages those two already carry.

**Nothing is created by this decision.** No repository, no directory, no
placeholder. An empty repository is a maintenance object with no content — a
README, a licence and a CI file to keep green — and the effort that writes the
first line of the UI is the one that should stand it up. This document is the
record until then.

**A multi-stage image builds it and serves it.** A `Dockerfile` in `dccex`: a
node stage builds, an nginx stage serves the built files. The box runs `docker
compose up --build` from the clone, so it needs Docker and nothing else — no
node toolchain on a machine whose job is a command station. How a browser
reaches that server is [its own ticket](https://github.com/rails49/.github/issues/12);
this decides only what answers when it is reached.

**A box that runs both runs two compose projects.** `dccex`'s compose declares
its UI's server and nothing more; `control` keeps `dccex-usb` and `dccex`
under its `hardware` profile until the Python actually moves. Definitions do
not move ahead of the code they describe — doing so would leave `control`'s
hardware profile broken in a repository that cannot fix it. Two projects is
the honest picture of a move that is half done, and the Python's own effort
collapses them into one.

**What the UI is written with is not fixed here.** The project's habit is Lit,
Shoelace, Vite, Vitest, TypeScript and pnpm, which is what `control` and
`occupancy` both run, and it is the obvious place to start. It is not binding:
ADR-0003 keeps components out of the look rules and ADR-0005 leaves tool
versions free, and contradicting both here would buy nothing.

**The look rules arrive with the first commit.** `dccex` is the first
repository created since ADR-0005, so on its first day it copies
[`docs/tokens.css`](../tokens.css) verbatim, records the commit it came from,
and tests its own values against that copy. It fetches nothing.

## Consequences

A fifth repository to install, release and keep green. That is the cost the
map warns about and it is real. It is paid once, here, because every
alternative either relicenses the project under terms we do not own or puts it
in a repository the target box must not have.

`dccex` and `CommandStation-EX` sit close enough to be confused, and someone
will have to be told which is which: `CommandStation-EX` is the firmware, a
fork of upstream's; `dccex` is ours, the UI and the two processes that talk to
the station. The alternative, `dccex-ui`, would be wrong the day the Python
arrives.

The Python's move becomes a move into a named place rather than a choice. That
effort amends `control`'s
[ADR-0043](https://github.com/rails49/control/blob/main/docs/adr/0043-the-layout-interface-is-a-core-app-and-hardware-hangs-under-it-by-address.md)
and collapses the two compose projects; nothing in this document waits on it.
`control` already keeps those two packages isolated —
`tests/system/test_app_boundaries.py` holds them to `tc49.lib` and themselves
— so what moves is a cut, not an extraction.

No image is published anywhere. If boxes ever outnumber the people maintaining
them, CI builds the same `Dockerfile` and the box pulls instead of cloning.
Nothing decided here is thrown away by that.

The door and the installation's page are still `control`'s, so the
command-station-only box installs `control` for them today even though it now
builds its UI without it. That is [ticket
12](https://github.com/rails49/.github/issues/12), which this decision
unblocks.

## Considered

**Inside `control`.** The UI would arrive with a clone the target box is not
supposed to have, and the Python's move would move it again. There is a
smaller reason with the same answer: `control` has no root `package.json`, and
the pnpm workspace root is `control/ui` itself, so a second UI package means
standing up a workspace that does not exist — work built only to be thrown
away.

**Inside `CommandStation-EX`, under the existing `rails49/` directory.** That
directory already holds a `Dockerfile` and a flash script, so the shape looks
proven. It is not: those three files are *about building the firmware*, which
is what a fork should carry. A project of our own is different. GPLv3 means
our MIT Python could not move in unchanged, every upstream merge would touch a
tree carrying our work, and our releases would ride the firmware's tags.

**Inside `occupancy`.** AGPL-3.0 and an unrelated subject. ADR-0005 already
refused to let that licence reach `control`.

**A repository for the UI alone, with the Python's home left open.** It keeps
this document clear of an out-of-scope effort, at the price of a UI whose only
counterparty may end up in a different repository. Naming a destination is not
making the move, and the destination is the part that has to be agreed.

**Building on the box with pnpm and bind-mounting `dist` into stock nginx**,
which is what `control` does today. It works, and it puts a node toolchain on
every box that shows this UI. The multi-stage image costs one file and removes
that.

**Publishing an image from CI from day one.** The box would need no clone at
all. It also needs tags, a registry and a release process, for one box shape
and no users yet. It is the next step, not the first.

**Creating the repository now, empty.** It would stop the name drifting. It
would also be a repository with nothing in it and a CI badge to explain, and
the name is written down here either way.
