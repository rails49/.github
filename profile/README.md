## rails49 — model railroad control and intelligence

Software for running model railroads: seeing where the trains are, and deciding
where they go next. [rails49.org](https://rails49.org)

### Repositories

- **[occupancy](https://github.com/rails49/occupancy)** — camera-based track
  occupancy detection. Photograph the layout, mark the points you care about,
  train a ResNet-18 classifier, and watch it run live. Everything runs in the
  browser via ONNX Runtime and WebAssembly, so your photographs never leave your
  machine. [Launch the app](https://occupancy.rails49.org)
- **[r49](https://github.com/rails49/r49)** — the community corpus of `.r49`
  layout archives: photographs, scale calibration, and labelled markers. Images
  and labels are CC BY 4.0.
- **[rails49.org](https://github.com/rails49/rails49.org)** — the landing page
  at the apex, and where end-user documentation grows.
- **control** *(to be released)* — scheduling, dispatching, and driving trains.
  The research core is deadlock-free, high-throughput dispatch: many trains
  sharing one layout without ever backing each other into a corner.

### Scale aware

Calibration is expressed in dots-per-track, so a model trained on one layout
transfers to another regardless of gauge, from Z through G.

---

Detection is imperfect. Responsibility for safe layout operation rests with the
operator.
