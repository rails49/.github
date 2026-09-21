# An installation is a name, and every UI is a label under it

Resolves [How a person reaches a UI](https://github.com/rails49/.github/issues/9)
on the [UI map](https://github.com/rails49/.github/issues/1).

A person reaching this project's UIs today remembers four names and a port:
`rails49.org` for the landing page, `occupancy.rails49.org`,
`layout.rails49.org` for the control UI, `dev.rails49.org` when it is being
worked on, and `http://192.168.178.56:6901` for JMRI. The dcc-ex UI would add
a fifth.

The list is the smaller half of the problem. The larger half is that
`rails49.org` belongs to this project. Another person running this software
has a different domain, or none, so a scheme that works only for a zone this
project owns is not a scheme.
[ADR-0004](0004-the-installation-serves-the-uis-that-are-about-it.md) already
put every box-served UI behind one door and said an installation brings a
name; it left what that name looks like, and what replaces JMRI's port, here.

## The fact that decides the shape

The origin is the whole of what this system's rules have to work with.
[ADR-0002](0002-a-ui-talks-to-the-bus-the-store-and-its-own-apps-face.md)
makes a private face one reached only by the UI it belongs to, on that UI's
own origin; ADR-0004 puts every counterparty on the UI's own origin; the
broker's refusal in
[`control/deploy/routes/layout/site.yaml`](https://github.com/rails49/control/blob/main/deploy/routes/layout/site.yaml)
is a comparison of one host, and
[`lib/origin.py`](https://github.com/rails49/control/blob/main/src/tc49/lib/origin.py)
is the same comparison at the store's face.

So a scheme that gives each UI a path under one hostname — `/control`,
`/dccex`, `/jmri` — is not a shorter way of writing this one. It puts every UI
on a single origin, and from that moment the bus rule, the store's rule and
the line between a shared face and a private one cannot tell the control UI
from the dcc-ex UI. A page from either is the other, to every check the system
has.

## Decision

**An installation is reached at a name of its own, and each UI it serves is
one label under that name.** The installation's own page is at the name
itself. Nothing is reached by a path under a sibling's origin, and no UI shares
an origin with another.

**The name is any name in a zone whose TXT records the operator can write.**
A domain bought for the box, so the box is that domain's apex; or a label in a
zone the operator already runs. Both are supported, both are in the install
instructions, and nothing downstream can tell which was chosen. An operator
who already owns a domain should add a label to it rather than buy a second
one.

**No zone, no name.** DNS-01 is the only path to a certificate here, and
ADR-0004 already declined to engineer around its absence: no certificate means
no secure context, so no camera and a degraded run view. The instructions say
what to do about it — a domain at a registrar with a DNS API, about ten
dollars a year — rather than shrugging. An mDNS `.local` name is documented as
a way to reach a box without a certificate, and is not part of the scheme.

**Every installation has a page of its own, listing the UIs that installation
serves.** It is a UI by ADR-0001's test, because its subject is the
installation rather than the loaded railroad, and box-served by ADR-0004's.
It is a plain list of links under the [look
rules](../LOOK.md): no band, no rail, nothing live. What a person wants from
it is a link, and a band on a page whose only job is to be left is ceremony.

**A box says once what it is called and what it serves.** The installation's
name is one parameter, set when the box is installed and written `BOX_DOMAIN`
in this document; the installation's page and the proxy's routers both read it,
and no file names a host. The exact spelling belongs to the repository that
reads it — `control`'s environment is prefixed `TC49_` already — and only the
shape is decided here. Today the
hostname is written into six places in one route file — five router rules and
the foreign-origin regex — and the file is chosen by `TC49_SITE`, so a
stranger's only path is to fork it. A scheme that is stated but cannot be
configured is not a scheme, and this is the difference between the two.

**A UI the project does not build is reached the same way.** JMRI gets a label
like any other and goes behind the same door, which is what ADR-0004's one
door per box already required of its noVNC. It stays exempt from the band and
the look rules; a name and a certificate are not the beginning of adopting the
rest.

**The name is the box's, not just the browser's.** Where the box is reached by
ssh, by a deploy script or by anything else that names it, it is reached at the
installation's name. A box must not need the project's zone to be deployed.

**The apex of `rails49.org` is the project's page and knows about no
installation.** What this project is, why you would use it, how you use it,
the `occupancy` demo a stranger can try with nothing installed, and the user
documentation. It links to no installation, this project's own included: an
installation is reachable only on its own LAN, so the link is dead for every
reader but one, and linking ours is exactly the privilege the map forbids.
The page is written once the system is built and tested against real hardware.

## What this makes of the names

Every box, written against its own `BOX_DOMAIN`:

| | |
| --- | --- |
| `$BOX_DOMAIN` | the installation's page, listing what that box serves |
| `control.$BOX_DOMAIN` | the control UI, with `/mqtt` and the store's routes on that origin |
| `dccex.$BOX_DOMAIN` | the dcc-ex UI, where the command station is |
| `jmri.$BOX_DOMAIN` | JMRI's noVNC, where JMRI runs |
| `occupancy.$BOX_DOMAIN` | the box copy of the detector, where a camera is |

What an operator does: set `BOX_DOMAIN` at installation, hold a token with
`DNS:Edit` on that zone, and point a DNS-only A record at the box's LAN
address for the name and each label. Someone who owns `my-railway.org` sets
`BOX_DOMAIN=my-railway.org`; someone who runs `example.org` already sets
`BOX_DOMAIN=attic.example.org`. Nothing downstream can tell which they did.

Two names are the project's rather than any installation's and are not under a
`BOX_DOMAIN`: `rails49.org`, the project's page in the cloud, and
`occupancy.rails49.org`, the cloud copy of the detector.

### This project's own boxes

Recorded here as an instance of the scheme and nothing more. The layout box
sets `BOX_DOMAIN=gleis49.org`, a domain bought for it; the dev box sets
`BOX_DOMAIN=dev.rails49.org`, a label in the project's zone, so the control UI
in development is `control.dev.rails49.org`. Both shapes therefore run here,
which is the only honest test that both are supported.

## Consequences

`layout.rails49.org` goes. The routers, the A record, the ssh target in
[`scripts/deploy.sh`](https://github.com/rails49/control/blob/main/scripts/deploy.sh),
the `rails49` stanza in `~/.ssh/config.d/`, and every mention in
`control/docs/DEPLOY.md`. No redirect: ssh cannot be redirected anyway, and a
web redirect would buy bookmarks held by two people a router and a certificate.
Native clients on 1883 and 2560 are untouched, because they use addresses.

`scripts/dns.sh` stops being this project's. `ZONE_NAME`, `ZONE_ID` and
`op://rails49/Cloudflare DNS` are in it today, and 1Password with them, which
is our tooling and not an operator's. What replaces it reads the box's own
declaration, and a stranger with no 1Password can still make their records.

One certificate per name, and no wildcard. Traefik asks for a certificate per
router host over DNS-01 automatically, as it already does for two names, so
four names cost four certificates and no configuration. A wildcard is an
option and not a requirement.

The broker's origin rule is already becoming a list under ADR-0004, one router
per UI origin. This decision fixes what those origins are.

A box whose `BOX_DOMAIN` is a whole domain consumes that domain. Its apex A
record points at a LAN address, so nothing else of it can be on the web. That
is what buying a domain for a box means, and it is fine when the domain was
bought for the box. An operator who wants their domain for other things sets
`BOX_DOMAIN` to a label under it instead.

The cutover is [control#550](https://github.com/rails49/control/issues/550):
the records, the route file, JMRI behind the door, this box's page, the
`BOX_DOMAIN` that replaces five copies of a hostname, and ssh with them. The
dev box moves in the same issue, to `control.dev.rails49.org`.

Where the door and the installation's page finally live is not settled here.
Both sit in `control`'s compose today, so a box that runs the dcc-ex UI and no
railroad still installs `control` to have a door and a page. That is
[its own ticket](https://github.com/rails49/.github/issues/12) on the map.

## Considered

**One hostname, a path per UI.** Shorter to type and fatal to the origin
rules, as above. The typing it saves is already saved by the installation's
page, which a person opens once and follows.

**Flat labels in the operator's zone** — `control.example.org`,
`dccex.example.org`. It breaks the moment one zone holds two installations,
and it makes the installation an implicit thing rather than a named one.

**Keeping `layout.rails49.org` and nesting under it.** Free, and it leaves the
project's own box as the one installation living inside the project's zone,
with UI names a label longer than anyone else's. The map's rule is that this
installation gets no answer another cannot have; it should also take no shape
another would not choose.

**Names handed out by this project**, under something like
`installations.rails49.org`. It would put this project in the business of
running DNS for strangers and make an installation depend on the uplink for
its own name.

**An installation page generated from what is running**, by asking the proxy
or the container daemon. It makes a static page into a small program to buy
freshness a list of links does not need. One declaration cannot disagree with
the routers, because the routers come from it.

**A hand-written page per box**, kept beside the routers. It disagrees with
them eventually, and the disagreement shows up as a dead link on the page a
person uses when something is already wrong.

**The apex linking to this project's box**, as a worked example. The link is
dead for everyone who is not on that LAN, and a reader cannot tell that from
the page.
