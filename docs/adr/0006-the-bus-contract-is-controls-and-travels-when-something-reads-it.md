# The bus contract is `control`'s, and travels when something reads it

Resolves [Where the bus contract lives](https://github.com/rails49/.github/issues/2)
on the [UI map](https://github.com/rails49/.github/issues/1).

ADR-0002 settled what a UI may talk to: the bus, the store's face, and the
face of its own app. This ADR settles where the definition of the bus lives.

The ticket assumed the contract already names publishers in repositories that
do not hold it. It does not, and it is not going to.

## The premise was wrong

[`control/docs/SYSTEM.md`](https://github.com/rails49/control/blob/main/docs/SYSTEM.md)
names a detector as the writer of
`tc49/layout/state/device/sensor/<block>.<end>`, and the ticket read that
detector as `occupancy`. It is not. `occupancy` holds no MQTT client, no copy
of the contract and nothing at all that names `tc49`; the only publisher of
that topic in any repository is `control`'s own bench detector.

It will stay that way, because `control` and `occupancy` are independent
projects that may be used together or apart. One person runs `control` with
current sensors and no cameras. Another runs `occupancy` into Rocrail and
never installs `control`. A contract that either side carried would tie the
two together and cost both of them that independence. So `occupancy`
publishes its own output in its own vocabulary, and whatever wants those
answers on the bus adapts them — which is the shape `control` already has for
hardware
([ADR-0058](https://github.com/rails49/control/blob/main/docs/adr/0058-hardware-meets-the-bus-and-a-translator-is-only-for-hardware-that-cannot.md),
[ADR-0043](https://github.com/rails49/control/blob/main/docs/adr/0043-the-layout-interface-is-a-core-app-and-hardware-hangs-under-it-by-address.md)).

That leaves the contract with one repository: `control`'s apps, and
`control`'s own UI.

## Decision

**The bus contract stays in `control`.** Every row of the event inventory
names `layout`, `schedule` or `dispatch` as its writer, and the promises the
bus makes are the model `control`'s apps are built on. A project that shares
the bus is a **client of `control`'s railroad**, the way JMRI is. JMRI does
not co-own a contract; it speaks one.

The map's rule that `control` is one consumer among four and no decision may
re-centre it was written about UIs, and it does not reach here. On the look
rules the four UIs were peers and none of them owned the values, which is why
they came to this repository (ADR-0005). The bus has an author.

**It is carved into `control/docs/BUS.md`.** SYSTEM.md is 1865 lines, and
most of it is not the contract. What a publisher or a subscriber needs is the
bus's promises and its four topic rules, the event inventory, the payload
schemas, the time the binding stamps, and the device vocabulary — lines
103–736 and 1537–1767, about 865 of them. What stays behind is `control`'s
own: the overview, the asset store, the component footprints for the
scheduler, the dispatcher and the driver, and the trace.

The carve corrects a filing error at the same time. The device vocabulary
sits inside the **Layout interface** footprint, and `layout` is the one
component that never writes those rows — they are written by translators and
detectors, and `layout` reads them.

**Nothing travels until something reads it.** No copy, no pin, no test, no
generated binding, nothing installed anywhere. Every reader today is in the
same repository as the file.

**The first consuming repository copies, records the commit and tests — and
the contract gains a machine-readable inventory at that moment, not before.**
ADR-0005's mechanism works because `tokens.css` is data: the consumer's test
compares its copy against its own values, and that comparison is the whole
point of the copy. Prose gives a test nothing to compare, so copying `BUS.md`
would be ADR-0005's ritual with the part that earns it removed. A list of
topics with kind, writer and payload fields is what a test can key on, and it
gets written when there is a test to write.

**`control` owns the document and the commit is the version.** Whoever
changes the bus changes it there. A change obliges nothing today, because
nothing else reads it. From the day a second repository does, a change files
an issue in that repository, exactly as ADR-0003 and ADR-0005 already say for
the look rules.

**The store's face inherits this answer and is not carved.** ADR-0002 calls
it shared, and by ADR-0002's own test it has exactly one caller: `control`'s
UI. No planned UI calls it — the dcc-ex UI is about a command station,
`occupancy` about cameras, the landing page about the project. Carving a
second file for a hypothesis is the cost this map warns about. Its
specification stays in SYSTEM.md and takes this ADR's rule the day a second
caller appears.

**The Python binding stays where it is, and it is smaller than it looks.**
`control/src/tc49/lib/bus.py` and `lib/mqtt.py` are the binding: the interface
every app is handed, and its two transports. `lib/payload.py` is not. It is
`control`'s defensive reading — organised by which of `control`'s apps reads
which frame, written in `control`'s issue numbers, and importing `control`'s
layout. `control/CLAUDE.md` says a TypeScript UI gets a sibling language
binding rather than chasing Python. That is right, and it reads as "port
`payload.py`", which it is not. A sibling binding implements the document.
`control/ui/src/model/trace.ts` already is one: 308 lines written by hand, and
that is the right size.

## Consequences

- `control` gets one issue: carve `docs/BUS.md`, and correct the `CLAUDE.md`
  sentence so it does not read as an instruction to port `payload.py`. No code
  is written from this map.
- No other repository owes anything. This is the second decision on this map
  to install nothing anywhere, after ADR-0005.
- If the dcc-ex UI lands outside `control`, it is the first consuming
  repository and this rule fires there: a copy, a recorded commit, a test, and
  the inventory written to support that test. Which repository holds it is
  [issue #10](https://github.com/rails49/.github/issues/10).
- `occupancy` owes its consumers an output specification of its own — SPEC.md
  fixes what L0 and L1 mean and says nothing about how either is served. That
  is `occupancy`'s, not this map's.
- The translator that turns `occupancy`'s answers into `device/sensor` is an
  ordinary translator under ADR-0043 and ADR-0058, and the mapping from
  `occupancy`'s sensor ids to `<block>.<end>` is its author's. Also not this
  map's.
- A second caller of the store's face reopens nothing. This ADR is the answer
  it takes.

## Considered

- **Moving the contract to this repository.** It would put a document here
  that one repository writes and one repository reads, which is the opposite
  of the look rules' case. It would also split `control`'s system description
  across two repositories for no reader's benefit.
- **A repository of its own.** An install, a version and a release for a
  document with one author and one consumer. The failure the map warns about,
  in its plainest form.
- **`occupancy` publishing `device/sensor` directly.** The ticket's premise,
  and it costs both projects their independence. It is also concretely awkward:
  `occupancy` keys its sensors by an id of its own, the topic is keyed by
  `<block>.<end>` off `control`'s drawing, so `occupancy` would have to read
  `control`'s documents to name its own output.
- **A translator inside `control` per outside producer, with the bus declared
  control-internal and nothing carved.** This is what happens anyway; it is
  not an argument against carving, because the dcc-ex UI still reads the bus
  from wherever it lives.
- **Carving the device vocabulary alone.** It is the only part that names a
  non-`control` publisher, so it looks like the minimal carve. It is not
  enough: anything publishing a state topic has to know that the topic is
  last-value-wins, that the binding stamps `at`, and that delivery is
  at-least-once. Those are in the promises.
- **Two files, the bus proper and the device vocabulary.** Splits at the wrong
  seam. The device rows are `tc49/layout/state/…` topics under the same four
  rules, and a translator reading only half would not know how they behave.
- **Copying the prose now, in ADR-0005's shape.** A copy nothing can test is a
  copy that reports nothing until someone re-copies it.
- **Generating the binding from Python.** `control` already generates
  TypeScript from Python source of truth — `symbols.generated.ts`,
  `rejection.generated.ts`, `tc49 generate` — so the machinery exists in
  spirit. It is the upgrade the first outside consumer may want, and it buys a
  reader in the same repository nothing.
- **Leaving SYSTEM.md whole.** Then the day a second reader appears, they read
  1865 lines about the scheduler and the dispatcher to find five tables.
