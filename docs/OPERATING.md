# Operating through failures

What every rails49 program does when something fails while trains run. It
binds control's apps and UI, occupancy-service, dccex, the JMRI bridge,
installation's door and page, and look's band. Programs that run offline,
`r49`, `classifier` and `detector`, are not bound. Control's
[GOALS.md](https://github.com/rails49/control/blob/main/docs/GOALS.md)
applies it to the railroad. Words follow [GLOSSARY.md](../GLOSSARY.md). This
states the target:
[#61](https://github.com/rails49/.github/issues/61) tracks the work in
programs that do not do this yet.

A model railroad fails often and briefly. As on the prototype, one failure
does not stop the railroad: what failed is out of use, the rest runs, a
person is told, and use resumes when the failure clears. Unlike the
prototype, the software does not guarantee safety against failures it cannot
see. It believes every report, guards against nothing it is not told, and
accepts the consequences: collisions, stranded trains, and deadlocks among
the trains a failure strands. A person repairs.

## Four kinds of failure

1. **On the railroad, observed.** A sensor silent or `unknown`, a reading
   nothing explains, a point that reports wrong, a throttle silent, the
   station's link down, a district trip the station reports, a train that
   does not arrive. Handled where it happens: the thing the failure names is
   out of use and nothing larger, the rest runs, a person is told, and use
   resumes by itself when the failure clears. The exception is a failure
   that leaves nothing to run on: the station's link down, or a trip of the
   last powered district. Then everything is out of use.
2. **In control's own picture.** A broken lock-table rule. A software bug.
   It drains the railroad, and a person turning power on acknowledges it
   (control
   [ADR-0076](https://github.com/rails49/control/blob/main/docs/adr/0076-a-broken-lock-table-holds-the-run.md),
   [ADR-0078](https://github.com/rails49/control/blob/main/docs/adr/0078-power-is-on-draining-or-off-and-replaces-go-and-hold.md)).
3. **The controller silent.** `layout`, the broker or the box stops.
   Something off the box cuts track power (control
   [ADR-0074](https://github.com/rails49/control/blob/main/docs/adr/0074-a-person-commands-track-power-and-it-is-cut-when-layout-goes-silent.md)).
   Another control app silent stops nothing: every move is bounded (control
   [ADR-0040](https://github.com/rails49/control/blob/main/docs/adr/0040-a-cross-expires-and-an-unfinished-one-stops-the-train.md)),
   the band names the silent app, and a person acts.
4. **Not observed.** A wrong reading, a point that did not throw, a decoder
   that missed its packet, a district trip the station does not report, a
   hand that moved a train. Not guarded. The consequences are accepted.

## Rules

- **Every report is believed.** No plausibility check, no second detector,
  no retry. Silence and `unknown` are failures, not reports.
- **Silence is observable.** Every input the railroad depends on repeats at
  a known rate or carries a last will. The consumer times it. The limit is
  a setting with a default, revisited once the railroad has run.
- **A failure on the railroad does not change track power.** Only a person,
  the hardware's own trip, the kind-2 drain and the kind-3 cut do. The
  station's link down leaves the rails as they are: `state/power` reads
  `off` while it holds, and control sends no train.
- **A failure is reported while it holds.** Each observed failure is a
  retained state row of the program that sees it. The band names each
  failure, red, until it clears. There is no general failure topic, no
  sound and no push notification. A page not open shows the line when it is
  (control
  [ADR-0050](https://github.com/rails49/control/blob/main/docs/adr/0050-broken-hardware-is-reported-never-worked-around.md)).
- **The failure clearing resumes use with no press.** Its consequences wait
  for a person: a placement, a cancel, a throttle, a hand. The software
  undoes no consequence by itself: no rerouting, no retry, no guess which
  train a reading belongs to.
- **A person is never refused.** A throttle and a hand work on any block
  during any failure. The software reports what it then sees.
- **A program that loses something keeps doing what it still can**, and
  reopens what it lost by itself: a camera, a serial port, a broker.

## What is out of use

| Failure | Out of use | Use resumes when |
| --- | --- | --- |
| A sensor silent or `unknown` | Its block: no grant in, out or through, and a train the app drives towards it stops | The sensor reports |
| A block reads occupied with no train claiming it | That block: no grant in | It reads clear, or a placement explains it |
| A train whose block reads clear | That train: no grant | A placement |
| A reported point lies wrong | The transit over it: the train waits (control [ADR-0097](https://github.com/rails49/control/blob/main/docs/adr/0097-a-reported-point-lies-where-device-point-says-and-a-train-waits-for-it.md)) | The point reports right |
| A train that does not arrive within its allowance | That train: stopped, its route held | A person drives it to its sensor or places it |
| A throttle silent | Nothing: the train is released, and in `automatic` the app drives it on; in `manual` it stops (control [ADR-0079](https://github.com/rails49/control/blob/main/docs/adr/0079-the-railroad-has-one-driving-mode-and-a-train-one-throttle.md), [ADR-0081](https://github.com/rails49/control/blob/main/docs/adr/0081-a-throttle-drives-outside-a-grant-and-a-release-keeps-its-speed.md)) | At once |
| The station's link down | Everything: `state/power` reads `off` (control [ADR-0066](https://github.com/rails49/control/blob/main/docs/adr/0066-the-link-is-the-station-answering-not-the-socket-being-open.md)) | The link is up: `state/power` reads the supply's word again, with no press |
| A district trip the station reports | The reason shows. With another district powered, nothing in control. With none left, everything: `state/power` reads `off` | The station or a person resets it |

## Accepted limits

- Deadlock freedom holds over the railroad the dispatcher knows. After a
  failure only the trains it strands wait. The dispatcher keeps granting
  the rest, and a person cancels or places.
- The software prevents the collisions it can see coming, by taking a block
  out of use and granting nothing into a disputed one. The rest are kind 4.
- A train on a committed route through a block out of use waits at its
  signal and is not rerouted. A person can cancel its request.
- The scheduler's generator does not know which blocks are out of use. Its
  requests wait in the list and run when the sensor returns.
- Degraded running is tested by publishing the device rows and events in
  tests (control
  [ADR-0096](https://github.com/rails49/control/blob/main/docs/adr/0096-invariants-are-stated-over-what-the-bus-shows-and-checked-in-tests.md)).
  The simulator produces no failures. The checks on the steel are the gate.
- An extended failure, or one on a small railroad, may leave nothing that
  can run.
