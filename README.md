# estate-examples: link-side-a

One half of an estate link. This branch is `estate-a`, whose fort
`alpha` joins `estate-b`'s mesh (the `link-side-b` branch) and serves it
a metrics role there. Switch to `main` for the repository introduction
and the branch index.

A link puts one fort on another estate's mesh. The linking side commits
a claim; the other estate verifies and records it. In the manifests that
is three pieces, and this branch carries the first two:

- `forts/alpha.toml` — the `[links.estate-b]` table: the address alpha
  claims on estate-b's mesh, the port, estate-b's interface and zone,
  and the public half of the link's WireGuard key.
- `nixos/hosts/alpha/default.nix` — the `sfi.links.estate-b` block,
  rendered by `fort scaffold` from that table. The host brings the
  interface up and a reconciler keeps its peers equal to estate-b's
  `mesh/peers.json`.
- `estate.toml` — the `[[serves.metrics]]` entry: why the link exists.
  alpha serves estate-b a metrics endpoint, and the link opens port
  9090 for it. estate-b names the same pair under `[hubs.metrics]`.

The third piece, estate-b's `remotes` record, lives on the
`link-side-b` branch.

## What was run and what was written by hand

`fort init` and `fort scaffold alpha --write` rendered everything except
this README and two hand-written parts, which exist because both sides
of this example live on branches of one repository and the link verbs
(`fort link add`, `fort estate link`) fetch an estate's default branch:

- the `[links.estate-b]` table in `forts/alpha.toml` — normally
  `fort link add alpha <estate-b url> --write` writes it and mints the
  key. The committed public key is real but its private half was piped
  straight into the public-key derivation and never stored anywhere.
- the `[[serves.metrics]]` entry — always the operator's to write,
  per the forts hub docs.

In your own estates the two sides are separate repositories and the
verbs do all of this.

## Make it yours

Follow the ritual on `main`'s README first (own repo as main, your
operator key, your admin recipient, your hypervisor). Then, for the
link, start clean rather than adopting the demonstration values:

1. Delete the `[links.estate-b]` table from `forts/alpha.toml`. The
   `sfi.links` block in the host file goes with it: delete
   `nixos/hosts/alpha/` and re-run `fort scaffold alpha --write`
   (scaffold never overwrites, so the deletion comes first).
2. Mint: `fort mint alpha --write`.
3. Run the real handshake against your other estate:
   `fort link add alpha <that estate's url> --write`, then PR and merge.
4. In a checkout of the other estate: `fort estate link <this url> --write`.

## The expected pre-mint state

`fort check --all` on this branch fails with the same three errors as
`main` (the webhook secret, `mesh/ssh_config`, the console hash), each
naming its repair. The link table adds no error before mint: verifying
the key needs the admin's age key, and without it the check skips,
never fails. CI on this branch performs the whole ritual with ephemeral
identity — including a stand-in link key — and then runs
`fort check --all --eval`.
