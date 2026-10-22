# The .r49 format is r49's, and each consumer pins it

Resolves [#56](https://github.com/rails49/.github/issues/56). Supersedes
[rails49/r49 ADR 0018](https://github.com/rails49/r49/blob/main/docs/adr/0018-the-r49-format-is-defined-in-rails49-r49.md).

ADR 0018 was written in rails49/occupancy and copied into rails49/r49 and
rails49/detector. Occupancy's and detector's copies open with "Superseded by
ADR 0019", although occupancy's ADR 0019 changed only what occupancy keeps.
r49's and detector's say the decision is not yet built, and all three say
"this repository" for occupancy. `@rails49/r49` is published, and occupancy and
detector both pin it, so the decision is built and three repositories depend
on it.

## Decision

1. **rails49/r49 owns the `.r49` format,** so an outside reader understands an
   archive without reading another repository. It holds the written
   definition, a TypeScript implementation that reads and writes, a Python
   implementation that only reads, and conformance cases both must pass. The
   scales, gauge, vocabulary and recommended DPT sit in one data file that both
   implementations and the validator read.
2. **Code is MIT; archives and the written definition are CC BY 4.0,** so
   others can train on the corpus and read it with r49's code.
3. **Each tag publishes `@rails49/r49` to npm and `rails49-r49` to PyPI.**
   Installing either never downloads the corpus.
4. **The corpus rules block a submission:** format validity, `id`, path, no
   `proposed` label, every class in the vocabulary. DPT is a reader
   requirement, not a corpus rule: r49 reports it, warns below its recommended
   DPT, and never refuses. The DPT fit, its residual and the camera-height
   correction are r49's, since every reader filters on the same number.
5. **Each consumer pins `@rails49/r49` to one version, once.** A format change
   is a release in r49, then a version bump in each consumer. Readers and
   writers reject unknown keys, so a reader on an older version refuses a newer
   archive until it upgrades.
6. **What a consumer derives from the format is the consumer's:** a minimum
   DPT, car width, a class list. It reads the format's constants from the
   package and does not copy them.

## Consequences

- r49's ADR 0018 gets a superseded line. Its `GLOSSARY.md` links here.
- rails49/detector deletes its copy of ADR 0018. Detector and occupancy cite
  this ADR where they cite ADR 0018.

## Considered

- **A new ADR in r49.** Detector and occupancy would cite another
  repository's ADR for a rule about themselves.
- **Leave the copies.** An agent that follows "ADR 0018" reads "Superseded"
  or "not yet built" and can conclude the rule is dead.
