## rails49 — model railroad control and intelligence

Software for running model railroads: seeing where the trains are, and deciding
where they go next. [rails49.org](https://rails49.org)

### See where the trains are

- **[occupancy](https://github.com/rails49/occupancy)** (alpha) —
  camera-based track occupancy detection. Photograph the layout, mark the cars
  and the sensors, and watch occupancy live, entirely in your browser. No
  trained model is published yet, so the live view shows every sensor as
  unknown. Calibration is in dots-per-track. The goal is a model trained on one
  layout that transfers to another regardless of gauge, from Z through G.
  [Launch the app](https://occupancy.rails49.org)
- **[r49](https://github.com/rails49/r49)** (working) — the community corpus
  of `.r49` layout archives: photographs, scale calibration, sensor points,
  and cars labelled by their two ends. Images and labels are CC BY 4.0.

### Run trains

- **[control](https://github.com/rails49/control)** (alpha) — scheduling,
  dispatching, and driving trains. The research core is deadlock-free,
  high-throughput dispatch: many trains sharing one layout without ever
  backing each other into a corner.
- **[dccex](https://github.com/rails49/dccex)** (alpha) — a browser UI for the
  DCC-EX command station, and the processes that connect the station to
  control and JMRI.
- **[CommandStation-EX](https://github.com/rails49/CommandStation-EX)**
  (working) — our fork of the DCC-EX firmware for the EX-CSB1 command station.
  [What the fork changes](https://github.com/rails49/CommandStation-EX#readme)
- **[jmri](https://github.com/rails49/jmri)** (planned) — a translator between
  control and JMRI.

### Signals

- **[dcc-led](https://github.com/rails49/dcc-led)** (hardware design, no
  firmware) — a small 8-channel LED driver, powered and controlled by DCC, that
  sets signal aspects from DCC commands.

### Set up a box

- **[installation](https://github.com/rails49/installation)** (alpha) — what
  the computer that runs rails49 installs before any railroad: its name, its
  certificate, a page listing what it serves, and the install instructions.

### Working together

Each part works alone or with the others. control can use other detectors,
such as current sensors. Planned: occupancy feeds its detection to control,
JMRI or Rocrail. Today it shows results on screen only.

### For developers

- **[look](https://github.com/rails49/look)** — the page chrome and widget
  styling shared by the rails49 UIs.
- **[rails49.org](https://github.com/rails49/rails49.org)** — the landing page
  at the apex, and where end-user documentation grows.
- Terms shared across repositories are in
  [CONTEXT.md](https://github.com/rails49/.github/blob/main/CONTEXT.md).
  Decisions that span repositories are in
  [docs/adr](https://github.com/rails49/.github/tree/main/docs/adr).

---

Detection is imperfect. Responsibility for safe layout operation rests with the
operator.
