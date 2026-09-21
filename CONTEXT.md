# rails49

The words the rails49 repositories share. Each repository keeps a `CONTEXT.md`
of its own for its own domain; this one holds only what crosses repositories,
and a repository's ADR that coined a term stays the authority for it.

## Language

**UI**:
A page a browser loads by an address of its own, about a subject of its own.
The umbrella word: it covers the landing page, `occupancy`, `control`'s browser
app and the dcc-ex UI, which the project builds, and JMRI's, which it does not.
In prose `control`'s browser app is "the control UI"; its package keeps the
name `ui`
([ADR-0001](docs/adr/0001-a-ui-of-its-own-is-about-something-other-than-the-loaded-railroad.md)).
_Avoid_: surface, frontend, app (a deployment unit in `control`)

**View**:
A way of showing or editing the loaded railroad inside the control UI's one
page: today the editor, the run view, the stock view and the throttle. A view
judges nothing. Restated from `control`'s
[ADR-0038](https://github.com/rails49/control/blob/main/docs/adr/0038-the-ui-is-one-app-with-views-of-one-railroad.md)
so every repository reads the same line.
_Avoid_: screen, tab, page

**Loaded railroad**:
What `control`'s store lists under one name — a drawing, a roster, scenarios —
plus its live run on the bus. Not the physical table: a photograph of the
layout is not a document of the railroad. Restated from `control`'s
[CONTEXT.md](https://github.com/rails49/control/blob/main/CONTEXT.md), **Railroad**.
_Avoid_: the layout (which is `control`'s layout interface)

**Installation**:
One person's running copy of the project: a box, the name it is reached at,
its certificate, the documents in its store and the UIs it serves. This
project's own — `layout.rails49.org` — is one installation among the possible
ones and gets no answer another cannot have
([ADR-0004](docs/adr/0004-the-installation-serves-the-uis-that-are-about-it.md)).
_Avoid_: deployment, instance, site (which is a compose profile in `control`)

**Cloud copy**:
A copy of a UI served from the cloud rather than by an installation. It
reaches no installation's data — the bus refuses a foreign origin — so a UI
has one only if it is useful with no installation behind it. `occupancy` has
one and `control` cannot (ADR-0004).
_Avoid_: demo, hosted version, public version

**Face**:
The door an app serves to clients that are not the bus: the store's HTTP
routes, and the socket and routes an app serves to its own UI. A face is
**shared** — specified, any UI may call it, and a change to it obliges its
callers — or **private**: reached only by the UI it belongs to, on that UI's
own origin, written down nowhere, and changed with that UI in one commit. A
second caller is what ends privacy
([ADR-0002](docs/adr/0002-a-ui-talks-to-the-bus-the-store-and-its-own-apps-face.md)).
_Avoid_: interface (the layout interface in `control`), endpoint, API

**Counterparty**:
One of the things a UI talks to: the bus, the store's face, or its own app's
face, and nothing else
([ADR-0002](docs/adr/0002-a-ui-talks-to-the-bus-the-store-and-its-own-apps-face.md)).
The browser's device APIs are not counterparties. A UI's band shows one state
per counterparty it has, and never one indicator for all of them
([ADR-0004](docs/adr/0004-the-installation-serves-the-uis-that-are-about-it.md)).
_Avoid_: backend, service, dependency

**Chrome**:
The band across the top and the rail down the left of a UI's page. The band is
about the whole of the system that UI is about; the rail carries what the
current view offers. Restated from `control`'s
[ADR-0064](https://github.com/rails49/control/blob/main/docs/adr/0064-the-chrome-is-a-band-and-a-rail.md)
so every repository reads the same line.
_Avoid_: header, toolbar, menu bar

**Look rules**:
What one system is binding on across UIs: where a control sits, what a colour
means, and how a small screen behaves. A set of named tokens and a written
convention, held in [`docs/LOOK.md`](docs/LOOK.md); whole components and tool
versions are not part of it
([ADR-0003](docs/adr/0003-the-look-rules-bind-place-colour-and-small-screens-not-code.md)).
_Avoid_: style guide, design system, theme (which is Shoelace's light or dark)
