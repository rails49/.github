# A UI of its own is about something other than the loaded railroad

Resolves [What makes something a UI of its own rather than a view](https://github.com/rails49/.github/issues/3)
on the [UI map](https://github.com/rails49/.github/issues/1).

`control`'s browser ships one page holding one loaded railroad and a list of
views of it
([ADR-0038](https://github.com/rails49/control/blob/main/docs/adr/0038-the-ui-is-one-app-with-views-of-one-railroad.md)).
The first attempt at a dcc-ex UI made it a fifth view and routed its traffic
over the control event bus, and nothing said why that was wrong. This is the
rule.

## Decision

**A view shows or edits the loaded railroad. Anything whose subject is not the
loaded railroad is a UI of its own.** The loaded railroad is what the store
lists under one name — drawing, roster, scenarios — plus its live run on the
bus; not the physical table.

Applied to what is on record: the editor, the run view, the stock view and the
throttle are views. The dcc-ex UI is a UI of its own: its subject is a command
station on a cable, which is there whether or not a railroad is loaded.
`occupancy` is a UI of its own: photographs and markers are not documents of
the railroad. The landing page has no subject and is its own. JMRI is its own.

**A UI of its own has its own page and its own address.** Whether that address
is a hostname or a path, and what serves the page, are decided elsewhere on the
map. Its own deploy and its own repository are optional, decided per case.

**A UI of its own and a separate project are two lines, not one.** Being a UI
of its own is decided by the subject test. Being a project — its own
repository, licence and release — is decided on its own merits. The dcc-ex UI
may live in `control`'s repository today and move with the dcc-ex code later.

## Consequences

- A view is `control`'s by construction. Anything whose subject is the loaded
  railroad is a view inside the one page, so inside `control`; it cannot arrive
  as a second page from another repository. This is the point of ADR-0038: a
  person never has two pages disagreeing about one railroad. Nothing enforces
  it in code; a reviewer applies it when a ticket proposes a new browser thing.
- Putting a non-railroad subject inside the railroad's page is what forced its
  traffic onto the railroad's bus. The rule explains the hack rather than merely
  forbidding it.
- The control UI emits events dcc-ex responds to. That is distinct from
  dcc-ex's configuration, which is the dcc-ex UI's subject. The configuration
  must agree with the railroad the control UI edits, and keeping it so is the
  person's job, not a mechanism's: it depends on what a particular command
  station can do, and command stations are open-ended.
- Where inside `control` the dcc-ex UI's page lives is not decided here.
  ADR-0038 went from two build entries to one on purpose; a second page is a
  serving and shared-code question, on the map as fog.

## Considered

- **By what it talks to**: a view talks only to the broker and the store;
  anything needing another door is its own UI. Same verdicts, but the rule
  would lean on the ticket it is meant to gate.
- **By app**: each app may have a UI of its own. Wrong verdicts: the scheduler
  and dispatcher would each get one, which
  [ADR-0036](https://github.com/rails49/control/blob/main/docs/adr/0036-the-scheduler-is-an-app-the-panel-is-a-view.md)
  and ADR-0038 already refused.
