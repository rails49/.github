# The installation serves the UIs that are about it

Resolves [What serves each UI, and what that forces](https://github.com/rails49/.github/issues/6)
on the [UI map](https://github.com/rails49/.github/issues/1).

Four UIs are served four different ways today, and written out as a table it
reads like four preferences. `occupancy` is a static build on Cloudflare Pages
at `occupancy.rails49.org`. `control` is a built UI served by nginx behind
Traefik on the layout box, under a DNS-01 certificate for
`layout.rails49.org`. The landing page is a static site at the apex of
`rails49.org`, from a repository of its own. The dcc-ex UI does not exist. A
table of four rows says nothing about the fifth UI, and it hides the fact that
makes this a design question at all.

That fact is in
[`control/deploy/routes/layout/site.yaml`](https://github.com/rails49/control/blob/main/deploy/routes/layout/site.yaml):
the `layout-mqtt-foreign` router refuses the broker's WebSocket to any page
whose `Origin` is not the layout's own name, and a page served over TLS cannot
open a plain `ws://` to the broker either. A page served from the cloud
therefore cannot reach an installation's bus, whatever anyone intends. Where a
UI is served is not a deployment convenience. It decides what that UI is able
to talk to at all, which makes it the other half of
[ADR-0002](0002-a-ui-talks-to-the-bus-the-store-and-its-own-apps-face.md).

## Decision

**A UI is served by whatever it is about.** A UI about a particular
[installation](../../CONTEXT.md) — its railroad, its cameras, its command
station — is served by that installation's own box. A UI about the project
rather than any installation is served from the cloud. This is
[ADR-0001](0001-a-ui-of-its-own-is-about-something-other-than-the-loaded-railroad.md)'s
test applied to hosting: the subject decides, here as there.

**A UI may also have a cloud copy, but only if it is useful with no
installation behind it.** `occupancy` passes — a camera and a browser is the
whole of it, and a stranger trying detection on their own railroad with
nothing installed is the reason the project has a public face. `control`
fails: with no store and no bus there is nothing to show. The dcc-ex UI fails:
with no serial line there is nothing to talk to. The landing page needs no
exception, because the rule already puts it in the cloud.

**`occupancy` has two homes and one build.** The box copy is the
installation's detector, the one that publishes
`tc49/layout/state/device/sensor/<block>.<end>`. The cloud copy is a demo. No
build flag separates them: the page builds its broker URL out of its own
origin, the way `control`'s run view already does, and the cloud origin has no
`/mqtt`, so the demo finds no bus without being told it is a demo.

**Every counterparty a UI has is reached on that UI's own origin**, proxied
there by the box. The store's face already says exactly this —
[`lib/origin.py`](https://github.com/rails49/control/blob/main/src/tc49/lib/origin.py)
compares `Origin`'s host to `Host` and names nothing — while the broker's rule
has one name written into a Traefik regex. That asymmetry is an accident of
where each rule is written, not a difference in intent, so the broker gets a
router per origin and the two come to match. `occupancy` on the box gets an
origin of its own and an `/mqtt` route of its own; it gets store routes when
it needs the store, and not before.

**One door per box.** Everything a browser reaches on a box goes behind the
edge proxy, JMRI's noVNC included, which today publishes 6901 in plaintext and
is the one thing on the layout server contradicting
[ADR-0042](https://github.com/rails49/control/blob/main/docs/adr/0042-the-edge-terminates-tls-and-the-lan-is-the-trust-boundary.md)'s
claim that the edge owns the only socket anyone off the box can reach. It
costs a route file and it buys a uniform secure context and one directory that
answers what is reachable. Native clients are untouched: 1883 and 2560 are not
browsers and that ruling never covered them.

**An installation brings a name.** The certificate comes from DNS-01 over a
zone the operator controls, which is the supported path and is what this
installation does. There is no fallback that keeps everything: without a
certificate there is no secure context, so `getUserMedia` is gone and
`occupancy` with it, and `control`'s run view loses its socket. That cost is
stated rather than engineered around.

**An installation runs with no internet.** Certificate renewal needs outbound
HTTPS, which is fine — it happens at about a third of the certificate's life
and never during an operating session. Name resolution must not need the
uplink, and ADR-0042 already admits the weakness: an outage takes the
hand-held throttles off the layout. Where the router can map a name to a LAN
address, doing so is a documented hardening; where it cannot, a long record
TTL is the fallback. This project's router cannot, which is why the fallback
is the path on record.

**What serves the dcc-ex UI is the box the command station is plugged into.**
That is the layout box today by circumstance and not by design. The bus is on
the LAN and nothing forces them together, but a second box costs a second
certificate and a second edge, so one box is the supported arrangement and two
are merely not forbidden.

**A UI never pretends an absent counterparty is there.** The band shows one
state per counterparty that UI actually has, named in ADR-0002's words — the
bus, the store, this app's own face. A view that needs something absent is
disabled with a plain sentence saying what is missing. No error page, no
modal, no retry spinner. One aggregate indicator is not enough, because the
case that matters is the store answering while the bus is gone: the drawings
are editable and no train can move, and a single dot cannot say that. JMRI is
exempt from all of it — ADR-0001 has it reached like the others and obeying
nothing else, and giving it a certificate and a route does not begin adopting
the rest.

## What this makes of the four UIs

| UI | served by | works away from the layout | works with the box down |
| --- | --- | --- | --- |
| landing page | the cloud, at the apex | yes | yes |
| `occupancy`, cloud copy | the cloud | yes, as a demo with no bus | yes |
| `occupancy`, box copy | the installation's box | no | no |
| `control` | the installation's box | no | no |
| dcc-ex UI | the box the station is on | no | no |
| JMRI | the installation's box, behind the edge | no | no |

Every box-served row needs the LAN and the certificate; the two cloud rows
need the uplink and nothing else. Nothing in the project is served from the
cloud and reaches an installation's data, and by the origin rule nothing can
be.

## Consequences

`occupancy` grows a second deployment. It ships an image and a Traefik route
file, and the box runs it as a compose project of its own rather than as a
service inside `control`'s. `control`'s deploy must not learn about a
repository it may not depend on, and the AGPL-3.0 boundary stays where the
licence put it. The cost is a second command in the deploy page, because
Traefik's file provider already watches a directory and a route dropped in is
the whole of the integration. `occupancy`'s cross-origin isolation headers
come from its own container on the box, where the Cloudflare Pages `_headers`
file does not reach.

The broker's origin rule becomes a list. One Traefik router per UI origin,
each naming its own, replacing the single `layout-mqtt-foreign` regex. The
store needs no change at all.

JMRI moves. `http://192.168.178.56:6901` stops being how it is reached, and
what replaces it is [How a person reaches a
UI](https://github.com/rails49/.github/issues/9) to name.

The install story has a domain in it. Anyone running this software must
control a DNS zone and hand the box an API token for it, or accept a stack
with no camera and a degraded run view. That is a real barrier and this
decision does not pretend otherwise.

The landing page question is answered rather than open. The apex page is about
the project, so it stays in the cloud. A page listing *this* installation's
UIs would be a UI about the installation, and therefore box-served; whether
there is one is issue #9's.

## Considered

**Four answers and no rule**, the table left as it stands. Rejected because it
cannot be extended: it says nothing about where a fifth UI goes, and it was
what let the first attempt put the dcc-ex UI inside the control UI.

**`occupancy` in the cloud only**, never publishing to the bus. It contradicts
[`SYSTEM.md`](https://github.com/rails49/control/blob/main/docs/SYSTEM.md),
which already specifies the detector's topic, and it would leave [Where the bus
contract lives](https://github.com/rails49/.github/issues/2) with nothing to
decide for the wrong reason.

**`occupancy` on the box only**, dropping the cloud copy. It gives up the one
UI a stranger can use, which is how anyone meets this project at all.

**The box copy on a path under `control`'s origin** rather than an origin of
its own. It needs no change to the broker's rule, and it re-creates the
`Cross-Origin-Embedder-Policy` path-scoping that splitting the origins existed
to dissolve, while making `occupancy` a tenant of `control`.

**A build flag telling each copy which it is.** Two artifacts where one will
do, and a flag that can be set wrong. The absence of a route is a signal that
cannot disagree with reality.

**A certificate authority on the box**, so an installation needs no domain. It
works offline and needs nothing public, and it asks every phone that will ever
drive the railroad to install a root certificate. That step is where people
stop.

**A local DNS server** for resolution without the uplink. Already rejected by
ADR-0042, on the ground that the resolver becomes a single point of failure
for the internet and not merely for the railroad. A router's own host mapping
is not that: no service is run, and the router is a single point of failure
for the LAN already.
