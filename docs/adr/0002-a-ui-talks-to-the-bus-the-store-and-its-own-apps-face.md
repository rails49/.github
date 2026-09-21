# A UI talks to the bus, the store and its own app's face

Resolves [What a UI may talk to](https://github.com/rails49/.github/issues/7)
on the [UI map](https://github.com/rails49/.github/issues/1).

`control`'s UI reaches two things: the broker, as a client like any other,
publishing only the rows
[SYSTEM.md](https://github.com/rails49/control/blob/main/docs/SYSTEM.md#event-inventory)
marks `browser`; and the store's HTTP face. Both refuse a page on another
origin
([ADR-0055](https://github.com/rails49/control/blob/main/docs/adr/0055-a-browser-is-not-on-the-lan-and-the-store-refuses-it.md),
[ADR-0056](https://github.com/rails49/control/blob/main/docs/adr/0056-the-browsers-way-onto-the-bus-refuses-a-foreign-origin.md),
[ADR-0057](https://github.com/rails49/control/blob/main/docs/adr/0057-one-origin-rule-and-both-faces-read-it.md)).
`occupancy`'s UI talks to neither.

The dcc-ex UI is what makes this a question. It needs the serial line and the
firmware, and neither is the railroad's business.
[ADR-0065](https://github.com/rails49/control/blob/main/docs/adr/0065-the-app-that-owns-the-device-flashes-it.md)
put `tc49/layout/firmware_wanted` on the bus because a gesture was the only
mechanism there was. This is the mechanism that replaces it.

## Decision

**A UI talks to three things: the bus, the store's face, and the face of the
app whose UI it is. Nothing else.**

**The test is the subject.** The bus and the store carry the loaded railroad.
An app's own face carries that app's private business. This is ADR-0001's line
used one layer down: what decides whether something is a view decides what it
may talk to.

**A face is shared or private.** The store's is shared: it is specified, any UI
may call it, and a change to it obliges its callers. An app's own is private —

- **who may call it**: the UI it belongs to, and nothing else;
- **where**: on that UI's own origin, through the same edge, so the origin rule
  already in force covers it and there is no second copy of it;
- **what may depend on it**: nothing. It gets no row in SYSTEM.md and no entry
  in any inventory. The app and its UI change it together in one commit and owe
  nobody notice.

**A second caller ends privacy.** The day anything but its own UI needs what a
private face carries, that traffic becomes a bus row or a shared face. A
private face is not the place to argue about it.

**Any app may serve one**, not only an app that owns a device. The subject test
holds the line without help: a dispatcher UI's subject is the loaded railroad,
so it is a view, so it uses the bus. There is no reading of this rule under
which an app serves a face to get around the contract.

**A UI does not call a third-party service.** JMRI's JSON servlet and Rocrail
are reached by a translator, which is what the `jmri` binding is. A UI reaches a
third-party tool only by being sent to it, as JMRI is reached over noVNC today.

**The browser's own device APIs are not counterparties** and this rule does not
constrain them. `occupancy` uses the camera and will go on doing so. The dcc-ex
UI does not use them: its serial monitor and its raw commands go through
`dccex-usb`'s face, because the process holding the device is the only thing
that touches it.

## What this does to the flash gesture

`tc49/layout/firmware_wanted` fails the subject test: one asker, one answerer,
and a microcontroller's flash is not the railroad. So does the optional `build`
on `tc49/layout/state/device/link/<id>`, which exists only so a UI can compare
what is on the box against what it asked for.

`control` owes three changes, and they ride with the dcc-ex UI rather than
being scheduled here, because the row goes when the face that replaces it
exists:

1. ADR-0065 keeps decision 1 — the app that owns the device is the app that
   flashes it — and loses decisions 2, 5, 6 and 7 with the section arguing that
   a flash is a gesture like the others. That section was only ever true
   because nothing else existed.
2. SYSTEM.md loses the `firmware_wanted` row and the optional `build`.
   `link: up` stays: whether the station is reachable is the railroad's
   business, and which build is on it is the dcc-ex UI's.
   `device/refused/<id>` stays for its other uses.
3. `dccex-usb` drops `--broker` and `--id` and `docs/dccex_usb/README.md`
   recovers its central claim: a byte mirror that reads no payload and speaks
   no topic.

## Consequences

- **Only a UI of its own can have a private face.** A view's subject is the
  loaded railroad, so a view has no private business. This follows from
  ADR-0001 and is not a second rule.
- **The edge grows a route, not a rule.** ADR-0055, 0056 and 0057 are untouched:
  a private face stands behind the same edge on the same origin, and a page
  from elsewhere is refused there exactly as it is at `/mqtt`.
- **A private face is no weaker and no stronger than the rest.** There is no
  authentication and the LAN is the trust boundary
  ([ADR-0042](https://github.com/rails49/control/blob/main/docs/adr/0042-the-edge-terminates-tls-and-the-lan-is-the-trust-boundary.md)),
  so somebody on the wireless with curl can call it, as they can the broker.
  The rule is about what may *depend* on it.
- **dcc-ex is on both sides at once.** Its translator does the railroad's
  business on the bus — turnouts, power, sensors, speed — and its UI does its
  own business on its face. A script that catches a bus event and posts one is
  a bus participant while it does that. The scripting design is a separate
  effort and this rule does not decide it.
- **The bus keeps its meaning.** It carries the railroad, which is what makes a
  contract shared across repositories worth holding still.
- Who serves the dcc-ex UI, and where its page lives, is not decided here. What
  is decided is that whoever serves it must put the face on its origin.

## Considered

- **Count the parties**: one asker and one answerer means a face. It breaks on
  the store, which has one answerer and is emphatically shared.
- **Look at the traffic's shape**: streams and request-reply on a face, state
  and events on the bus. It breaks on the store the same way, and it would let
  someone argue a train's speed onto a face because it changes quickly. Shape
  is evidence that a subject is private, never the test.
- **A second bus contract**: dcc-ex keeps the broker but gets a topic namespace
  and a contract document of its own. It moves the paperwork and not the
  traffic. A serial monitor is a byte stream at line rate with no retained value
  and one reader, and MQTT is the wrong shape for it whoever's document names
  the topic. It would also prejudge
  [where the bus contract lives](https://github.com/rails49/.github/issues/2).
- **A script over ssh**, ADR-0065's own rejected alternative, and no dcc-ex UI
  at all. It contradicts the principle this map keeps: every operation a person
  performs has a path through a UI, with update from the UI the one exception.
- **Web Serial in the page.** The port is held open by `dccex-usb` on the box
  the cable is plugged into, and a browser on a phone is not on that machine.
  Two openers on one line is the same conflict flashing has.
- **Narrowing the permission to an app that owns a device**, inheriting
  ADR-0065's framing. It buys nothing and costs a second argument the moment
  scripting arrives, since a script is not a device.
