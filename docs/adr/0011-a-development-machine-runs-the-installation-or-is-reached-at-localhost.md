# A development machine runs the installation, or is reached at localhost

Resolves [Record the dev-box resolution as an ADR in
`rails49/.github`](https://github.com/rails49/.github/issues/18),
found while planning
[rails49/installation#1](https://github.com/rails49/installation/issues/1),
the effort that moves the door out of `control`.

[ADR-0007](0007-an-installation-is-a-name-and-every-ui-is-a-label-under-it.md)
named the dev box `dev.rails49.org`, a label in the project's zone, so the
control UI in development is `control.dev.rails49.org`. It recorded that name
for a reason: the layout box takes a domain bought for it, the dev box takes a
label in a zone the project already runs, and both shapes therefore run here,
which is the only honest test that both are supported.

[ADR-0009](0009-the-door-and-the-page-are-the-installations-in-a-repository-of-their-own.md)
made every router a label on the container it routes to, on Traefik's docker
provider, and deleted the route directory and the file provider with it.

Neither addressed what the dev box's route file actually routes.
[`deploy/routes/dev/site.yaml`](https://github.com/rails49/control/blob/main/deploy/routes/dev/site.yaml)
sends `dev.rails49.org` to `host.docker.internal:5173` — vite, a process on
the developer's own machine — and `/mqtt` to a broker on the same host, with
a copy of `control`'s foreign-origin refusal beside them.

## Why the two decisions do not compose

The docker provider discovers routers from the labels on containers. A process
on the host has no container, so there is nothing for a label to be on. Vite is
a process on the host, and it is what `dev.rails49.org` is pointed at today.

Every other name in the scheme is served by a container that can declare its
own router. This one is served by a development server a developer starts by
hand, and under ADR-0009 nothing is left that could route to it. Neither
decision is wrong about what it decided; the case they share fell between
them.

## Decision

**A development machine either runs the installation, with containers, or is
reached at `localhost`.** Two shapes and no third. ADR-0009 already said a
developer who wants the real thing runs the installation; running the
installation means running containers, and containers carry labels, so that
shape needs nothing added. Everything else is ports on `localhost`, which is
what a developer editing a file has anyway. Below, *the dev box* is this
project's own development machine, the one ADR-0007 named; the rule is about
any development machine.

**The dev box keeps its name.** `dev.rails49.org` is the box name of a
development machine running the installation, which is the same thing
`gleis49.org` is for the layout box. ADR-0007's reason survives intact: the
project's own boxes still demonstrate both name shapes, a domain bought for a
box and a label in a zone the operator already runs. The name is the box's.
What the dev route file pointed it at is vite, and that pointing is what ends
here.

**Vite loses its name and is reached at `localhost`.** `localhost` is a secure
context, so the camera, the clipboard and everything else a certificate buys
are available on it, and a development server behind a public name buys
nothing over that. This is ADR-0009's own argument — "a laptop with no name
loses nothing by reaching vite and the apps on ports" — applied to the
machine that decision was written about, rather than only to a stranger's
laptop. The dev route file goes with the layout one.

**Nothing is added to the door to reach a process on the host.** No file
provider, no fragment, no container standing in for vite. What is reachable
through the door on a development machine is what is reachable through it on
any box: the containers of the stacks that box runs.

## What this does not change in ADR-0009

ADR-0009 is not edited. It decided where the door and the page live, that each
stack declares its own routers, and that `control` keeps no proxy, not even a
development one. All of that stands, and all of it is what leaves the dev
route file with nothing to route. The decision above is one ADR-0009 did not
make, and writing it back into that document would make the record say it had
considered a case it did not see.

ADR-0007 is not edited either. Its cutover line — "The dev box moves in the
same issue, to `control.dev.rails49.org`" — still holds for the machine
running the installation, which is the only place that name is now served.

## Consequences

`control` loses `deploy/routes/dev/site.yaml` with the rest of the route
directory. Nothing replaces it: its three routers were vite, a broker on the
host and a foreign-origin refusal in front of them, and none of the three is
behind a door any more.

The name survives elsewhere in that repository, with the reason for it gone:
`allowedHosts: ["dev.rails49.org"]` in
[`ui/vite.config.ts`](https://github.com/rails49/control/blob/main/ui/vite.config.ts),
and the sentences in `scripts/dev.sh`, `ui/README.md` and `docs/DEPLOY.md`
that explain why vite is reached by that name. Vite is told about a host
because something in a container asks for it, and after this nothing does.
Clearing them is `control`'s, in an issue there.

`control`'s dev script is unchanged by this work. It starts the same three
things on the same ports, and a developer's loop is what it was. Two of its
stated reasons stop being true once the door leaves that repository — that
compose demands the door's Cloudflare token for any service it is asked for,
which is why the broker runs under `docker run`; and that the store and vite
bind every interface because the door runs in a container and cannot reach a
macOS host's loopback. Whether to simplify either is `control`'s call, in an
issue there, and is no part of this decision.

A developer who wants the real shape installs the installation on their own
machine and sets `BOX_DOMAIN=dev.rails49.org`. They get the door, a
certificate per label and `control`'s own container serving its built UI,
which is what an operator receives and what running it is for.

## Considered

**A file provider confined to host processes**, alongside the labels, for
vite and nothing else. It is the second route table ADR-0009 refused, at one
entry. The entry does not stay alone: the dev file holds `/mqtt`, its strip
middleware and `control`'s foreign-origin regex today, so confining the
provider to host processes means keeping `control`'s routing in a file for the
one machine where it is most often edited — the copy that disagrees with the
labels, on the machine that changes both.

**Containerising vite**, so the thing being routed has a container and can
carry its own label. It closes the gap, and the edit loop pays for it: a file
watcher across a bind mount, the module graph rebuilt inside an image, and hot
reload through the door. It also makes development on this
project a different thing from development on any other. A container serving
`control`'s UI already exists for the case that wants one, and it is the built
image rather than vite.

**Dropping the dev box's name entirely**, and recording that development
happens at `localhost` and nowhere else. Cheapest, one DNS record lighter, and
it throws away ADR-0007's reason for the name: without it the project's own
boxes demonstrate one name shape, and the label-in-an-operator's-zone shape is
supported on paper only. The name costs a record and carries a decision.
