# The values check runs in the consumer's gate, because it fetches nothing

Resolves [Where the values check runs](https://github.com/rails49/.github/issues/16),
which the [UI map](https://github.com/rails49/.github/issues/1) did not chart.
It is [ADR-0005](0005-the-look-rules-travel-as-a-copied-file-not-a-package.md)
meeting the first repository that took the look rules
([control#552](https://github.com/rails49/control/pull/552)).

ADR-0005 says the check compares a consumer's values against its recorded copy
and never fetches, and gives the reason: a check that reached for this
repository's tip could go red on somebody else's commit and red-light every
open pull request in the consumer. The sentence after it carries that reason
one step further than it goes — "For the same reason the check runs outside the
required gate."

`control` ran it inside the gate instead. The assertions went where ADR-0005
and the issue filed from it asked for them, in the file that already pinned the
reflow height, and that file is run by the script that is `control`'s required
check. One of the two had to change.

## Decision

**The reason is about fetching, and it stops there.** What red-lights a
consumer's open pull requests is a check that can go red on a commit nobody in
that repository made. Reading a committed file in the repository the check runs
in cannot do that: it goes red on an edit to that repository's own values,
which is the one thing it is for, and in the gate it goes red before the edit
lands rather than after.

**A check that reads only files in the consumer's own repository runs with
that repository's other tests.** Where those run in a required gate, so does
it. This is also where ADR-0005 put the assertions — in the consumer's own
form of the values, beside its own tests — so keeping the check out of the gate
means a second test runner, or a suite the first one excludes, for six values.
A check nothing is obliged to run is not a check.

**A check that fetches still stays out of the gate.** ADR-0005 allows none, so
the rule survives for a case no consumer may write. The only values check that
exists is `control`'s, it fetches nothing, and it belongs where it is.

**Nothing here obliges a consumer to build a gate.** The rule says where the
check goes in a repository that already runs tests. A repository that runs
none owes the copy and the recorded commit all the same, and what reads the
copy there is its own question — the landing page's, which ADR-0005 left to the
effort that takes the rules.

## What this changes in ADR-0005

The sentence "For the same reason the check runs outside the required gate" is
narrowed: the paragraph now says the reason is about fetching and stops there,
and where a non-fetching check runs is here. The rest of that paragraph stands:
the check compares against the recorded copy, never fetches, and fails only
when a consumer edits its own values.

## Consequences

- `control`'s check needs no change.
  [`ui/test/styles.test.ts`](https://github.com/rails49/control/blob/main/ui/test/styles.test.ts)
  inside
  [`scripts/check.sh`](https://github.com/rails49/control/blob/main/scripts/check.sh)
  is what this decision asks for, and the reading recorded in
  [`ui/look/README.md`](https://github.com/rails49/control/blob/main/ui/look/README.md)
  is the rule now rather than a departure from one.
- That README owes an edit all the same: it argues against a sentence that no
  longer exists. It gets an issue in `control`, the way any change here does
  (ADR-0003).
- `occupancy`, the dcc-ex UI and the landing page inherit this with the rules,
  which none of them has taken yet. The argument is not repeated per consumer.
- A copy landing with a value the consumer has not followed yet goes red in the
  gate and blocks that pull request until the code follows. That is the
  intended order: taking a change here is copying the file and moving the
  values in one pull request.
- Drift from this repository's tip stays invisible to every consumer, by
  design. A change here is announced by hand (ADR-0005), and no check anywhere
  notices one that is not.

## Considered

- **The sentence stands as written, and each consumer runs the values check
  outside its gate.** Every consumer owes a second runner, or a suite the first
  one excludes, for six values — and what it buys is a red mark after the
  drift has landed on `main`, reported to nobody in particular. The issue's
  own reading is that this is the branch that needs the extra machinery.
- **Strike the sentence and write nothing in its place.** The next consumer to
  take the rules re-argues it from the fetching paragraph, which is what
  happened here.
- **A gate check that reports but cannot fail.** A check that cannot fail is a
  notice, and nothing obliges anyone to clear it. The consumer's own test
  runner would then hold one test that is exempt from the meaning every other
  test in it has.
- **A fetching check outside the gate**, to catch a change here before the
  issue announcing it arrives. ADR-0005's scheduled job in a smaller form, for
  an event that has happened zero times, and it puts back the one thing the
  check is kept away from: this repository's tip.
