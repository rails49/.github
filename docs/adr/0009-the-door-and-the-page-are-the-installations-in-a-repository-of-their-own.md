# The door and the page are the installation's, in a repository of their own

Resolves [Where the installation's door and page live](https://github.com/rails49/.github/issues/12)
on the [UI map](https://github.com/rails49/.github/issues/1).

[ADR-0007](0007-an-installation-is-a-name-and-every-ui-is-a-label-under-it.md)
gave every installation a name, a label per UI under it, one door in front of
all of them, and a page listing what that box serves. It left where the door
and the page live.

Both sit in `control` today. `proxy` is a service in
[`deploy/compose.yaml`](https://github.com/rails49/control/blob/main/deploy/compose.yaml),
it holds 80 and 443 and the certificate, the route table is
`deploy/routes/<site>/site.yaml`, and the page would land beside them. `proxy`
and `broker` are the two services with no profile, so a box that clones
`control` and starts it with no profile gets a door and a bus.

So a box with a command station and no railroad installs `control` to be
reachable. That is the defect. It is not that `control` is a bad host for a
proxy; it is that being reachable is not a railroad's business, and the box
that proves it — a command station, a flashing UI, no layout, no store, no
trains — is an ordinary box rather than a corner case.

## What an installation turns out to be

Four things, and they are the same four on every box: the name it is reached
at, the certificate for that name, the door in front of everything a browser
reaches, and the page listing what the box serves. Nothing in that list is
about a railroad, a camera or a command station. It is what a box is before
anything is installed on it.

## Decision

**The door and the page move to a repository of their own,
`rails49/installation`, MIT.** It holds the door, the page, the box's
declaration of itself and the install instructions. It is the base: a box
installs it first, and the railroad, the detector, the command station and
JMRI are what an operator adds to it.

The map's rule is that a proposal for a new repository says why the
alternative inside an existing one fails. `control` fails because a box with
no railroad must not install a railroad to be reachable, which is this
ticket's whole subject. `.github` fails because GitHub special-cases paths in
a repository of that name and it holds no code. `occupancy` fails on licence
and subject — AGPL-3.0, and about a camera. `dccex` fails because it is one UI
among several and cannot own the door in front of its siblings. The cost is
real: one more install, one more version, one more release. It buys a box that
is reachable without being a railroad.

**The installation is the name, the certificate, the door and the page, and
nothing else.** The bus is `control`'s
([ADR-0006](0006-the-bus-contract-is-controls-and-travels-when-something-reads-it.md)),
so a box with dcc-ex and no railroad runs no broker, and the dcc-ex UI reaches
its app on that app's own private face
([ADR-0002](0002-a-ui-talks-to-the-bus-the-store-and-its-own-apps-face.md)).
The store is `control`'s. Every UI brings the container that serves it —
`control` already runs its own nginx, the dcc-ex UI serves itself
([ADR-0008](0008-the-dcc-ex-ui-and-its-python-share-a-repository-of-their-own.md)),
and `occupancy`'s box copy needs one it has never had. The installation knows
names and nothing about builds.

**Each stack declares its own routers, on its own containers.** Traefik's
docker provider rather than the file provider: a UI's paths are that UI's
business and change with it in one commit, which is ADR-0002's line for a
private face applied to routing. `control`'s five router rules, its six store
path prefixes and its foreign-origin regex become labels on `control`'s own
containers and stay `control`'s. Compose substitutes `${BOX_DOMAIN}` in a
label, so no template is rendered and no route file is copied anywhere.

**A box says what it is called and what it serves in one file at a fixed
path,** `/etc/rails49/box.env`: `BOX_DOMAIN`, the name from ADR-0007, and
`BOX_UIS`, the labels this box serves. A fixed path rather than a file in the
installation's clone, because a value that lives in one clone makes that clone
a prerequisite of every other stack — the dependency this decision exists to
remove. Every stack is started against it.

**The page is one link per label, rendered once when its container starts.**
The text is the label, the address is `https://<label>.$BOX_DOMAIN`. No table
of titles: the installation holds no fact about any UI, so a UI that did not
exist when the installation was written needs no change here. Rendering
happens at start, so the page stays a file rather than becoming a program.

**One external docker network, created by the installation and joined by every
stack that serves a UI.** Separate compose projects get separate networks, so
without it the door cannot resolve `web` or `store` at all. This is an
obligation on `control`, `occupancy` and `dccex`, and it belongs in the
install instructions beside the declaration.

**The certificate's DNS provider is a parameter, and the credential comes from
the box's environment.** Cloudflare is the worked example and not the scheme;
Traefik's stock binary carries every provider already. No script writes DNS
records: ADR-0007 retired `scripts/dns.sh` for naming a zone, an account and a
1Password vault that are ours rather than an operator's, and "point a DNS-only
A record at the box's LAN address" is an instruction rather than a program.

**JMRI's compose rides here as an optional extra, and is not part of the
installation.** It is the one UI no repository of this project owns. Leaving it
in `control` reintroduces exactly the defect above, since JMRI drives a command
station and a command-station box is the case at issue. Hanging it off `dccex`
makes that project responsible for a tool it does not build, and JMRI is used
against the layout too. The installation is the only place with no opinion
about railroads or command stations, and the README carries the line that it is
not part of the installation.

**`control` keeps no proxy.** Not even a development one. `localhost` is a
secure context, so a laptop with no name loses nothing by reaching vite and the
apps on ports, and a second route table is a second copy of the routing that
will disagree with the labels. A developer who wants the real thing runs the
installation.

## What this amends in ADR-0007

ADR-0007 rejected a page generated by asking the proxy or the container
daemon, and wrote: "One declaration cannot disagree with the routers, because
the routers come from it." The routers no longer come from it. They come from
each stack's labels, and what the page and the routers share is `BOX_DOMAIN`
rather than a common source.

So a label in `BOX_UIS` whose stack is not running is a dead link. That is the
price of each UI owning its own routes, and it is paid in the open: the link
fails in front of the person who listed it, on their own box, with the fix
being to start the stack or drop the label. The rest of ADR-0007 stands — the
page asks neither the proxy nor the daemon, and is a file.

## Consequences

`control` loses the `proxy` service, ports 80 and 443, the ACME environment,
`deploy/routes/` and `TC49_SITE`, the Cloudflare token in `deploy/op.env`, and
the `jmri` service. It gains labels on the containers it serves, the external
network, and being started against `/etc/rails49/box.env`.
[`scripts/deploy.sh`](https://github.com/rails49/control/blob/main/scripts/deploy.sh),
`scripts/dev.sh`, `scripts/dns.sh` and `docs/DEPLOY.md` all change.

[control#550](https://github.com/rails49/control/issues/550) is absorbed rather
than done. It is written to rewrite `deploy/routes/layout/site.yaml` with the
new hostnames, and this decision deletes that file. Doing it as written means
testing an intermediate shape of the routing that exists for one ticket. The
name cutover happens once, when the door moves.

`occupancy` gains a compose file for its box copy, which it has never had — it
is a static build served from the cloud today and nothing serves it on a box.

A box with a command station and no railroad installs the installation and
then `dccex`. No broker, no store, no railroad, and a page listing what it has.

Nothing here is created. Standing the repository up, moving the door out of
`control` and cutting the names over is its own effort, as the dcc-ex
repository is under ADR-0008.

## Considered

**Leaving both in `control`.** Today's answer, and the one the ticket exists to
replace. It was accepted as the cost of ADR-0007 rather than as right.

**A route table per box, held by the installation and rendered from the
declaration.** It keeps ADR-0007's sentence intact. It also puts `control`'s
six store path prefixes and its foreign-origin regex in a repository that does
not change when `control` does, and Traefik's file provider does no variable
substitution, so something has to render `BOX_DOMAIN` into every rule before
the proxy starts. A render step and a drift, to keep a guarantee whose failure
is a dead link.

**A route fragment shipped by each app and dropped into the door's
directory.** Each repository owns its own paths, which is right, but it is a
copy on the box with the same render step, and
[ADR-0005](0005-the-look-rules-travel-as-a-copied-file-not-a-package.md)
accepts a copy only for six values that a test can compare.

**Each UI's stack bringing its own proxy and its own certificate.** No shared
door, no shared anything. Two stacks cannot both hold 443, which ends it.

**A table of titles for the page**, so it reads "Control" rather than
`control`. One fact about every UI, in the one repository that otherwise has no
reason to know any, edited every time a UI is added. The label is the name.

**`rails49/box` as the name.** Shorter, and it reads well in an instruction.
But CONTEXT.md has just made *installation* mean something precise, and a
second word for the same thing is how a glossary rots. A box is one part of
what an installation is.

**JMRI staying in `control`, or riding with `dccex`.** Above.

**`control` keeping a development-only proxy**, so one compose still stands the
whole thing up on a laptop. A second route table that disagrees with the labels
the first time either changes.
