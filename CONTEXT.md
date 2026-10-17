# rails49

The words used in more than one rails49 repository. Each repository keeps a
`CONTEXT.md` of its own for its own words. Every entry links to the doc that
owns it ([ADR-0017](docs/adr/0017-each-fact-has-one-owner-and-an-accepted-adr-does-not-change.md)).

## Language

**UI**:
A page a browser loads by an address of its own, about a subject of its own:
the landing page, an installation page, `occupancy`, the control UI, the dcc-ex
UI and JMRI's. The control UI is `control`'s browser app; its package keeps the
name `ui`
([ADR-0001](docs/adr/0001-a-ui-of-its-own-is-about-something-other-than-the-loaded-railroad.md)).
_Avoid_: surface, frontend, app (a deployment unit in `control`)

**View**:
`control`'s word, owned by its
[ADR-0038](https://github.com/rails49/control/blob/main/docs/adr/0038-the-ui-is-one-app-with-views-of-one-railroad.md).

**Loaded railroad**:
`control`'s word, owned by its
[CONTEXT.md](https://github.com/rails49/control/blob/main/CONTEXT.md), **Railroad**.

**Installation**:
One person's running copy of the project: a box, the name it is reached at,
its certificate, the documents in its store and the UIs it serves. It names the
box, not the railroad loaded on it
([ADR-0007](docs/adr/0007-an-installation-is-a-name-and-every-ui-is-a-label-under-it.md),
[ADR-0009](docs/adr/0009-the-door-and-the-page-are-the-installations-in-a-repository-of-their-own.md)).
_Avoid_: deployment, instance, site (which is a compose profile in `control`)

**Installation page**:
The page at an installation's own name that lists the UIs the box serves
([ADR-0007](docs/adr/0007-an-installation-is-a-name-and-every-ui-is-a-label-under-it.md),
[ADR-0009](docs/adr/0009-the-door-and-the-page-are-the-installations-in-a-repository-of-their-own.md)).
Not the **landing page**, the project's page at the apex of `rails49.org`,
which names no installation.
_Avoid_: home page, index, dashboard, portal

**Door**:
The one thing in front of everything a browser reaches on a box. It holds the
certificate for every label under the installation's name and sends each
request to the UI that label belongs to
([ADR-0009](docs/adr/0009-the-door-and-the-page-are-the-installations-in-a-repository-of-their-own.md)).
_Avoid_: edge proxy, ingress, gateway, reverse proxy (what it is made of, not
what it is)

**Cloud copy**:
A copy of a UI served from the cloud rather than by an installation. It reaches
no installation's data
([ADR-0004](docs/adr/0004-the-installation-serves-the-uis-that-are-about-it.md)).
_Avoid_: demo, hosted version, public version

**Face**:
The way in an app offers to clients that are not the bus: the store's HTTP
routes, and the socket and routes an app serves to its own UI. A face is
**shared**, which any UI may call, or **private**, which only its own UI calls
([ADR-0002](docs/adr/0002-a-ui-talks-to-the-bus-the-store-and-its-own-apps-face.md)).
_Avoid_: interface (the layout interface in `control`), endpoint, API, door
(the installation's, in front of every UI on a box)

**Counterparty**:
One of the things a UI talks to: the bus, the store's face, or its own app's
face. The browser's device APIs are not counterparties
([ADR-0002](docs/adr/0002-a-ui-talks-to-the-bus-the-store-and-its-own-apps-face.md)).
_Avoid_: backend, service, dependency

**Bus contract**:
What the bus promises and what may be published on it: the promises, the topic
rules, the event inventory, the payload schemas and the device vocabulary. It
is `control`'s and lives in
[`docs/BUS.md`](https://github.com/rails49/control/blob/main/docs/BUS.md)
([ADR-0006](docs/adr/0006-the-bus-contract-is-controls-and-travels-when-something-reads-it.md)).
_Avoid_: protocol, schema, API, SYSTEM.md (`control`'s whole system, of which
the contract is one part)

**Chrome**:
The band across the top and the rail down the left of a UI's page, drawn by
**look** ([ADR-0016](docs/adr/0016-the-chrome-comes-from-look.md)).
_Avoid_: header, toolbar, menu bar

**Look**:
The package `@rails49/look`, which draws every rails49 UI's chrome. Its values
and what they mean are in look's
[`docs/LOOK.md`](https://github.com/rails49/look/blob/main/docs/LOOK.md)
([ADR-0016](docs/adr/0016-the-chrome-comes-from-look.md)).
_Avoid_: look rules (formerly: the copied values and convention), style guide,
design system, theme (which is Shoelace's light or dark)

**Reference copy**:
A file a repository holds verbatim, with the commit it came from, only so a
test can compare against it. Nothing imports it and no build reads it
([ADR-0006](docs/adr/0006-the-bus-contract-is-controls-and-travels-when-something-reads-it.md)).
_Avoid_: vendored copy, snapshot, fixture (which is test data a test reads as
input)
