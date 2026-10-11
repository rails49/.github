# JMRI's profile lives in the store

Resolves [#27](https://github.com/rails49/.github/issues/27).

DecoderPro's roster had no backup. It sat in the `jmri` volume of the
installation's JMRI project
([rails49/installation ADR-0002](https://github.com/rails49/installation/blob/main/docs/adr/0002-jmri-is-a-compose-project-of-its-own.md)),
under `/home/jmri/.jmri/Default`: `roster.xml`, one file per loco with its CV
values, and the profile holding the connection to the command station. A disk
that fails takes every decoder's record with it.

`control`'s store already has a backup: a git repository the person made, a
deploy key of the store's own, a dialog, restore, and taking a store over on a
new box
([control ADR-0053](https://github.com/rails49/control/blob/main/docs/adr/0053-backup-drives-git-and-does-not-own-it.md)).
One installation has one store and one JMRI, and the roster records decoders
in the stock the store already holds.

## Decision

**JMRI's profile directory lives in the store, at `jmri/`**
(`${TC49_STORE:-~/tc49}/jmri` on the box), and is backed up, restored and
moved to a new box with the rest of the store. Only the profile: logs, caches
and the desktop stay in JMRI's volume, and losing them costs nothing.

**The installation mounts it at `/profile`**, outside `/home/jmri`, and starts
JMRI with `JMRI_OPTIONS=-Dorg.jmri.profile=/profile`. The image runs
`chmod -R 777 /home/jmri` on every start; inside the home that would make every
file in the store executable and world-writable, and git would see every one
of them change.

**JMRI runs as the person who deployed the box**, in a thin image over
`jsa1987/jmri-docker:stable` that renumbers its `jmri` user to `TC49_UID` and
`TC49_GID`. The store runs as that person
([control #387](https://github.com/rails49/control/issues/387)), and a restore
writes files as them; under the image's own uid 61000 JMRI could not write its
roster again afterwards.

**The mount does not create a missing directory.** Docker makes a missing
bind-mount source owned by root, which neither JMRI nor the store can write.
`up` fails naming the path instead, and the README makes the directory first.

**`control` learns nothing about JMRI.** Files under `jmri/` are files in the
store: a backup names them by path, they count when the store is asked
whether it is empty, and a restore puts them back with everything else. What
`control` changes is general: a commit is made every push period for whatever
git says has moved, not only after the store's own saves
([control #713](https://github.com/rails49/control/issues/713)).

**A restore is not refused while DecoderPro is open.** JMRI reads its roster
once, at launch, and writes its in-memory copy back on the next roster save,
so a restore under it is undone. Nothing can see whether JMRI is open: it is
not on the bus and its web server is not reachable. The restore dialog says to
quit DecoderPro and PanelPro first and open them again after
([control #714](https://github.com/rails49/control/issues/714)).

## Consequences

- On the railroad, a restore changes no decoder. A loco programmed after the
  picked backup keeps its CVs, and its roster file holds the older values until
  DecoderPro reads the decoder again.
- A new box adopts the store before DecoderPro is opened. A profile written
  first makes the store not empty, and adoption is refused, naming it.
- DecoderPro work is committed within one push period while automatic backup
  is on, and only by *Back up now* while it is off. Until then a restore is
  refused, naming the `jmri/` files, as for any document not backed up.
- The installation's `jmri` project builds an image rather than pulling one.
- The live roster on gleis49 moves once, by hand; the old volume is kept until
  a restore of `jmri/` has been checked.

## Considered

- **A second repository for JMRI**, driven by the same code. Two repositories,
  two keys and two setups for one installation, to gain restoring one without
  the other.
- **All of `.jmri/`.** It carries a log and a cache that change on every
  launch, and needs an ignore list somebody owns.
- **Mounting at `.jmri/Default`**, which needs no `JMRI_OPTIONS`. The image's
  `chmod` reaches it, and the store would have to tell git to ignore file
  modes because of a program it otherwise knows nothing about.
- **Running the upstream image with compose `user:`.** The image hardcodes its
  home and user, and nothing upstream supports it.
- **Watching the store for writes**, so a DecoderPro save arms the idle commit
  as a store save does. A new thread and a new dependency, to commit sooner
  than five minutes.
