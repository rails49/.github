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

**Face**:
The door an app serves to clients that are not the bus: the store's HTTP
routes, and the socket and routes an app serves to its own UI. A face is
**shared** — specified, any UI may call it, and a change to it obliges its
callers — or **private**: reached only by the UI it belongs to, on that UI's
own origin, written down nowhere, and changed with that UI in one commit. A
second caller is what ends privacy
([ADR-0002](docs/adr/0002-a-ui-talks-to-the-bus-the-store-and-its-own-apps-face.md)).
_Avoid_: interface (the layout interface in `control`), endpoint, API
