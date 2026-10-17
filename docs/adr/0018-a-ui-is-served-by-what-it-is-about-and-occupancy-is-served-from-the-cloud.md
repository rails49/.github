# A UI is served by what it is about, and occupancy is served from the cloud

Resolves [#40](https://github.com/rails49/.github/issues/40). Supersedes
[ADR-0004](0004-the-installation-serves-the-uis-that-are-about-it.md).

ADR-0004 gave `occupancy` two homes: a demo in the cloud, and a copy on the
box that publishes `tc49/layout/state/device/sensor/<block>.<end>`.
[ADR-0006](0006-the-bus-contract-is-controls-and-travels-when-something-reads-it.md)
says the opposite: `occupancy`'s code names nothing of `tc49`, and a
translator maps its sensor ids to `<block>.<end>`. Neither has happened.
`occupancy` is a static build at `occupancy.rails49.org`, and nothing serves
it on a box.

What `occupancy` reports is not settled. It may send a user-defined message
over MQTT, and it may report where cars are. The page may report, or an app on
a server may take the images, run the model and report, which avoids the
browser's limits on camera resolution and processing speed. Which one waits on
experience with the model.

The rest of ADR-0004 stands and is restated here, so that one ADR holds it.

## Decision

1. **A UI is served by what it is about.** A UI about an installation — its
   railroad, its command station — is served by that installation's box. A UI
   about the project, or one that needs no installation, is served from the
   cloud. This is
   [ADR-0001](0001-a-ui-of-its-own-is-about-something-other-than-the-loaded-railroad.md)'s
   test applied to hosting.
2. **A box-served UI may also have a cloud copy, but only if it is useful with
   no installation behind it.** `control` fails: with no store and no bus
   there is nothing to show. The dcc-ex UI fails: with no serial line there is
   nothing to talk to. No UI has a cloud copy today.
3. **`occupancy` is served from the cloud, at `occupancy.rails49.org`, and has
   no box copy.** It labels and calibrates layouts and runs the model in a
   live view, which may report nothing. Its code names nothing of `control`'s
   bus contract (ADR-0006). What it sends, in what form and over what, is
   `occupancy`'s to decide, and is not decided.
4. **What reports detection results to a railroad is not decided.** It may be
   the page, an app on a server, or something else. An app that reports is not
   a UI, so rule 1 does not place it. When anything sends `occupancy`'s
   results to something on a LAN, where it runs is decided then. A page served
   from the cloud may not reach it: an HTTPS page cannot open `ws://`,
   `wss://` needs a certificate the browser trusts, and `control`'s broker
   refuses a page on another origin.
5. **Every counterparty a UI has is reached on that UI's own origin**,
   proxied there by the box. The broker and the store's face apply one origin
   rule: a page's `Origin` must be the UI's own
   ([`lib/origin.py`](https://github.com/rails49/control/blob/main/src/tc49/lib/origin.py)).
   A UI gets store routes when it needs the store, and not before.
6. **One door per box.** Everything a browser reaches on a box goes behind
   the door, JMRI's noVNC included, so the door owns the only socket a browser
   off the box can reach
   ([ADR-0042](https://github.com/rails49/control/blob/main/docs/adr/0042-the-edge-terminates-tls-and-the-lan-is-the-trust-boundary.md)).
   Native clients on 1883 and 2560 are not browsers and are untouched.
7. **An installation brings a name.** The certificate comes from DNS-01 over a
   zone the operator controls. Without a certificate there is no secure
   context, so a box-served page has no camera and `control`'s run view loses
   its socket. That cost is stated rather than engineered around.
8. **An installation runs with no internet.** Certificate renewal needs
   outbound HTTPS at about a third of the certificate's life, never during an
   operating session. Name resolution must not need the uplink. Where the
   router can map a name to a LAN address, doing so is a documented hardening;
   where it cannot, a long record TTL is the fallback.
9. **The dcc-ex UI is served by the box the command station is plugged
   into.** That is the layout box today by circumstance. One box is the
   supported arrangement; two are not forbidden.
10. **A UI never pretends an absent counterparty is there.** The band shows
    one state per counterparty the UI has, named in
    [ADR-0002](0002-a-ui-talks-to-the-bus-the-store-and-its-own-apps-face.md)'s
    words. A view that needs something absent is disabled with a plain
    sentence saying what is missing. No error page, no modal, no retry
    spinner. JMRI is exempt.

## What this makes of the UIs

| UI | served by | works away from the layout | works with the box down |
| --- | --- | --- | --- |
| landing page | the cloud, at the apex | yes | yes |
| `occupancy` | the cloud | yes | yes |
| `control` | the installation's box | no | no |
| dcc-ex UI | the box the station is on | no | no |
| installation page | the installation's box | no | no |
| JMRI | the installation's box, behind the door | no | no |

Nothing served from the cloud reaches an installation's data, and by rule 5
nothing can.

## Consequences

- [ADR-0007](0007-an-installation-is-a-name-and-every-ui-is-a-label-under-it.md)'s
  `occupancy.$BOX_DOMAIN` label and
  [ADR-0009](0009-the-door-and-the-page-are-the-installations-in-a-repository-of-their-own.md)'s
  compose file for a box copy wait for the decision in rule 4.
- `CONTEXT.md` links **Cloud copy** here, and adds **Detector** and
  **Sensor**, each with its meaning in `control` and in `occupancy`.
- Docs outside accepted ADRs that cite ADR-0004 move to this one, one issue
  per repository: `dccex` (`docs/cutover.md`, `docs/ui/README.md`,
  `docs/dccex_usb/README.md`) and `control`
  (`docs/dispatcher/INTERNALS.md`).

## Considered

- **A box copy now**, as ADR-0004 had it. It has nothing to publish and no
  reader.
- **`occupancy` publishing `device/sensor` directly.** Rejected in ADR-0006:
  it ties the two projects together, and `occupancy` would have to read
  `control`'s drawing to name its own output.
- **Deciding now where a reporting page or app runs.** It would choose a
  transport and a counterparty for a consumer nobody has asked for, before
  the model shows whether a browser can do the job.
- **Superseding only ADR-0004's `occupancy` parts.** ADR-0007, ADR-0015 and
  `dccex`'s ADR-0017 cite its other rules, which would then have no current
  owner
  ([ADR-0017](0017-each-fact-has-one-owner-and-an-accepted-adr-does-not-change.md)).
- **A certificate authority on the box**, so an installation needs no domain.
  It asks every phone that will drive the railroad to install a root
  certificate, and that step is where people stop.
- **A local DNS server** for resolution without the uplink. Rejected by
  ADR-0042: the resolver becomes a single point of failure for the internet
  and not only for the railroad.
