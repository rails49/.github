# Each UI has an origin of its own, at a label or a UI port

Resolves [rails49/installation#53](https://github.com/rails49/installation/issues/53).
Supersedes
[ADR-0007](0007-an-installation-is-a-name-and-every-ui-is-a-label-under-it.md)
and
[ADR-0018](0018-a-ui-is-served-by-what-it-is-about-and-occupancy-is-served-from-the-cloud.md).

ADR-0007 and ADR-0018 rule 7 made a box name required: no zone, no name, and
no certificate without one. ADR-0018 rules 3 and 4 gave `occupancy` no box
copy and left open what reports detection results. Four issues changed that,
and each has run on a box:

- [installation#53](https://github.com/rails49/installation/issues/53): a box
  with no certificate serves every UI on a UI port over plain HTTP. Run on
  `pi49` on 2026-11-11.
- [#60](https://github.com/rails49/.github/issues/60): an occupancy service on
  the box reports occupancy, and the box serves a copy of `occupancy`. Runs on
  `gleis49`.
- [#69](https://github.com/rails49/.github/issues/69): one origin check, the
  installation's, on every way in. Run on `gleis49` on 2026-11-11.
- [installation#69](https://github.com/rails49/installation/issues/69): the
  install script makes a box with no certificate, and the setup page takes no
  DNS token. Run on `test49` (amd64) and `pi49` (arm64) on 2026-11-12, each
  installed from the README alone.

[ADR-0021](0021-each-fact-has-one-owner-and-the-glossary-is-glossary-md.md)
lets no ADR amend another, so this one supersedes both in full. It restates
every rule of theirs that stands, with the amendments
[ADR-0009](0009-the-door-and-the-page-are-the-installations-in-a-repository-of-their-own.md)
and [ADR-0016](0016-the-chrome-comes-from-look.md) made to ADR-0007.

## Decision

### Where a UI is served

1. **A UI is served by what it is about.** A UI about an installation, its
   railroad or its command station, is served by that installation's box. A
   UI about the project, or one that needs no installation, is served from the
   cloud. This is
   [ADR-0001](0001-a-ui-of-its-own-is-about-something-other-than-the-loaded-railroad.md)'s
   test applied to hosting.
2. **A box-served UI may also have a cloud copy, if it is useful with no
   installation behind it.** `control` has none: with no store and no bus
   there is nothing to show. The dcc-ex UI has none: with no serial line there
   is nothing to talk to.
3. **`occupancy` has a cloud copy at `occupancy.rails49.org`, and a box copy on
   a box that runs the occupancy service.** The cloud copy labels and
   calibrates with the camera of the device it runs on, and reaches no box.
   The box copy is served by the occupancy service under the `occupancy`
   label. It shows the box camera's frames, and opens and saves the loaded
   railroad's archive on the box.
4. **The occupancy service reports, and the page does not.** It runs the
   models where the frames are and publishes each sensor's occupancy to the
   box's broker. Profile files say what it publishes, so its code names
   nothing of `control`'s bus contract; they do the job
   [ADR-0006](0006-the-bus-contract-is-controls-and-travels-when-something-reads-it.md)
   gave a translator. Its HTTP API is a shared face under
   [ADR-0002](0002-a-ui-talks-to-the-bus-the-store-and-its-own-apps-face.md).
5. **The dcc-ex UI is served by the box the command station is plugged
   into.** One box is the supported arrangement; two are not forbidden.

### How a UI is reached

6. **Each UI has an origin of its own.** No UI is reached by a path under
   another's origin, and no two UIs share one. The origin is all that the
   broker's, the store's and every face's check have to tell one UI from
   another. On one origin, a page of one UI is a page of every other.
7. **Every box serves each UI on its UI port, over plain HTTP,** at
   `<hostname>.local` and at the box's address. The installation owns the
   table of ports, in its README. A new UI takes the next free port. A box with
   a box name stays reachable this way when DNS or its certificate fails.
8. **A box name is optional.** With one, each UI is also at
   `https://<label>.<box name>`, and the installation page at the box name.
   The name is any name in a zone whose TXT records the operator can write: a
   domain bought for the box, or a label in a zone they already run. Nothing
   downstream can tell which. Each name gets its certificate over DNS-01. With
   none, `BOX_DOMAIN` is set and empty, and the box needs nothing outside the
   house: no domain, no DNS records, no token.
9. **A box says once what it is called and what it serves,** in its
   declaration: `BOX_DOMAIN` and `BOX_UIS`. The page and every stack are
   started against it, and no file names a host. Each stack declares the
   routers for its own label and UI port.
10. **A box is reached by ssh or a deploy script at the name a browser uses:**
    its box name, or `<hostname>.local` without one. A box needs no zone of
    the project's to be installed or deployed.
11. **A UI the project does not build is reached the same way.** JMRI has a
    label and a UI port behind the door. It is exempt from the band and the
    look rules.

### The door

12. **One door per box.** Everything a browser reaches on a box goes through
    it, and nothing a browser reaches is published outside it
    ([control ADR-0042](https://github.com/rails49/control/blob/main/docs/adr/0042-the-edge-terminates-tls-and-the-lan-is-the-trust-boundary.md)).
    Native clients on 1883 and 2560 are not browsers and are untouched.
13. **Every counterparty a UI has is reached on that UI's own origin,**
    proxied there by the door. A UI gets store routes when it needs the store,
    and not before.
14. **One origin rule on every way in.** A request whose `Origin` is present
    and whose host and port differ from `Host` is refused. One with no
    `Origin` goes through. No list of names is kept. A face applies the rule
    itself; where what is behind the door reads no `Origin`, as the broker and
    noVNC do not, the door applies the installation's check. A probe after
    every deploy checks every UI, way in and path.
15. **Port 80 redirects only the box name and the names under it** to HTTPS.
    An address or a `.local` name gets the page over plain HTTP. No response
    carries HSTS: with `includeSubDomains` it would turn every UI port into
    HTTPS.

### The pages

16. **Every installation has a page listing the UIs it serves,** at the box
    name and on port 80. It is a file rendered at start and asks neither the
    door nor the container daemon, so a label whose stack is not running is a
    dead link. It draws look's band, with the box name on its left, or the
    hostname on a box with no certificate. It links each UI by the scheme it
    was opened over: `https://<label>.<box name>` over HTTPS, the UI port on
    the same host over HTTP.
17. **The setup page sets up only a box with no certificate.** A box name is
    set by hand on the box, as the installation's README says. No DNS token
    crosses plain HTTP.
18. **The apex of `rails49.org` is the project's page and links to no
    installation,** this project's own included. An installation is reachable
    only on its own LAN, so the link would be dead for every reader but one.

### Running

19. **An installation runs with no internet.** A box with no certificate
    resolves its names over mDNS and needs no uplink. A box name needs
    outbound HTTPS to renew its certificates at about a third of their life,
    never during an operating session, and name resolution must not need the
    uplink. Where the router can map the name to a LAN address, doing so is a
    documented hardening; where it cannot, a long record TTL is the fallback.
20. **A UI never pretends an absent counterparty is there.** The band shows
    one state per counterparty the UI has, named in ADR-0002's words. A view
    that needs something absent is disabled with a plain sentence saying what
    is missing. No error page, no modal, no retry spinner. JMRI is exempt.

## What this makes of the UIs

| UI | served by | with a box name | on every box |
| --- | --- | --- | --- |
| landing page | the cloud | `rails49.org` | |
| `occupancy`, cloud copy | the cloud | `occupancy.rails49.org` | |
| installation page | the installation | the box name | port 80 |
| `control` | the installation's box | `control.<box name>` | its UI port |
| dcc-ex UI | the box the station is on | `dccex.<box name>` | its UI port |
| JMRI | the installation's box | `jmri.<box name>` | its UI port |
| `occupancy`, box copy | the box running the occupancy service | `occupancy.<box name>` | its UI port |

Nothing served from the cloud reaches an installation's data, and by rule 14
nothing can.

## Consequences

- A UI over plain HTTP has no secure context. `control` loses the screen wake
  lock, so a phone holding a train needs a longer screen timeout, and "Copy
  the key" on its backup tab does not copy. noVNC shows a banner. Neither
  copy of `occupancy` needs one where it is served: the box copy uses the
  box's camera, and the cloud copy is on HTTPS.
- A box whose box name is a whole domain consumes that domain: its apex points
  at a LAN address. An operator who wants the domain for other things uses a
  label under it.
- The LAN is the trust boundary (control ADR-0042). A page served by another
  host on the LAN, at the same name and port, passes the origin rule.
- ADR-0007 and ADR-0018 each get a superseded line. ADRs that cite them keep
  their citations (ADR-0021 rule 3).
- `GLOSSARY.md` here links **Installation**, **Installation page**,
  **Cloud copy** and **Detector** to this ADR.
- installation's `GLOSSARY.md` and README, its `jmri/compose.yaml`, and
  rails49.org's README cite ADR-0007 or ADR-0018. Each moves to this ADR in a
  pull request of its repository.
- The ADR #60 expected for ADR-0018 rules 3 and 4 is this one. #69's
  decisions are rules 12, 14 and 15 here, and installation#69 decision 20's
  DNS token is rule 17.

## Considered

- **One hostname, a path per UI.** Shorter to type, and every UI on one
  origin, as rule 6 says.
- **A name under `.local` per label**, `control.<hostname>.local`. Avahi
  publishes only `<hostname>.local`, and Linux's `nss-mdns` refuses names of
  more than two labels in its recommended setup.
- **Flat labels in the operator's zone**, `control.example.org`. It breaks
  when one zone holds two installations.
- **Names under a zone the project runs**, such as
  `<railroad>.rails49.org`. It needs an acme-dns server, or a function the
  project runs at every renewal, and makes an installation depend on the
  project for its own name.
- **A certificate authority on the box.** It asks every phone that will drive
  the railroad to install a root certificate, and that step is where people
  stop.
- **A local DNS server** for resolution without the uplink. Rejected by
  control ADR-0042: the resolver becomes a single point of failure for the
  internet and not only for the railroad.
- **Admitting origins from a list of names.** The door's `/mqtt` routers did,
  and refused a page at `gleis49.org:8082` that every other path admitted
  (#69).
- **The DNS token on the setup page.** It would cross plain HTTP.
- **An installation page generated from what is running,** by asking the
  door or the daemon. It makes a static page into a program to buy freshness a
  list of links does not need.
- **`occupancy` publishing `device/sensor` directly.** It ties the two
  projects together, and `occupancy` would have to read `control`'s drawing
  to name its own output (ADR-0006).
