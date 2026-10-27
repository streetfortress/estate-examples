# estate-examples: link-side-b

The other half of an estate link. This branch is `estate-b`, the estate
being linked to: `alpha`, a fort of `estate-a` (the `link-side-a`
branch), holds an address on this mesh and serves this estate a metrics
role. Switch to `main` for the repository introduction and the branch
index.

On the recording side a link is two pieces:

- `mesh/peers.json` — the `remotes` entry for alpha: its address on this
  mesh (`10.12.0.254`), its endpoint, the link's port, and the public
  half of the link's key. Every fort of this estate peers with alpha on
  its next rebuild because of this entry.
- `estate.toml` — the `[hubs.metrics]` entry naming alpha as the fort
  that serves this estate the metrics role. estate-a declares the same
  pair from its side under `[[serves.metrics]]`.

Nothing about the link appears in `forts/bravo.toml`: a linked host is
an estate-level fact, not a per-fort one.

## What was run and what was written by hand

`fort init` (including the `--hub metrics=alpha@…` flag, which rendered
the `[hubs.metrics]` entry) and `fort scaffold bravo --write` produced
everything except this README and one hand-written part:

- the `remotes` entry in `mesh/peers.json` — normally
  `fort estate link <estate-a url> --write` verifies estate-a's claim
  and writes this record. It is committed by hand here because both
  sides of this example live on branches of one repository, and the
  link verbs fetch an estate's default branch.

In your own estates the two sides are separate repositories and the
verb does this.

## Make it yours

Follow the ritual on `main`'s README (own repo as main, your operator
key, your admin recipient, your hypervisor), with one addition: delete
the demonstration `remotes.alpha` entry from `mesh/peers.json` and the
`[hubs.metrics]` entry from `estate.toml`, then mint. When a fort of
another estate links to yours for real, `fort estate link <its url>
--write` verifies the claim and writes the record.

## The expected pre-mint state

`fort check --all` fails with the same three errors as `main` (the
webhook secret, `mesh/ssh_config`, the console hash), each naming its
repair. CI performs the ritual with ephemeral identity and gates with
`fort check --all --eval`.
