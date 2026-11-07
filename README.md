<p align="center">
  <img src="readme-images/examples-banner.png" alt="Estate Examples" height="160">
</p>

<p align="center">
    Reference estates for <em><a href="https://github.com/streetfortress/forts">Forts!</a></em>
</p>

An _estate_ is a git repository declaring one or more _forts_:
- `estate.toml` declares the **Mesh** network CIDR and source repository URL.
- `forts/<fort>.toml` declares a fort's size and its place on the estate's **Mesh** network.

Each _branch_ in this repository is a complete estate, rendered by the
forts toolchain. It is a **reference, not a starting point**: don't
clone it to begin — `fort estate init` renders a fresh estate matched
to your forts version by construction. Start from the
[getting started guide](https://github.com/streetfortress/forts/blob/main/docs/README.md)
instead, and come back here after each lifecycle verb to check that
your tree has grown the same shape this one has.

Each branch has a customized README.md (AI Generated) tailored to the
example scenario. Have fun building! - sf1tzp

---

## Repo Structure

| branch | what it shows |
| --- | --- |
| `main` | the minimal estate: one k3s fort, an apps tree with no workloads |
| `link-side-a` | an estate whose fort joins another estate's mesh — the linking side |
| `link-side-b` | the estate being linked to — the recording side |


## What's in this branch

One estate named `example`, with one fort:

- `estate.toml` — the estate's facts: its name, its git URL, the mesh
  (`10.10.0.0/24`), the operator keys.
- `forts/outpost.toml` — the fort's declaration: a 2-vCPU lab VM on a
  libvirt hypervisor, raised as a one-node k3s + flux cluster
  (`[cluster]`).
- `nixos/`, `flux/`, `mesh/`, `provisioning/` — everything
  `fort estate init`, `fort scaffold`, and `fort mint` render. Nothing
  here was written by hand except this README, the manifest, and the
  seeded Alertmanager webhook placeholder.

## The checkpoints

Work through
[getting started](https://github.com/streetfortress/forts/blob/main/docs/README.md)
on your own estate, and diff against this branch after each verb:

1. **[`fort estate init`](https://github.com/streetfortress/forts/blob/main/docs/reference/init.md)**
   renders the estate's boilerplate. Compare `estate.toml` and the
   `nixos/`, `flux/`, `mesh/`, `provisioning/` skeletons: the flake and
   the flux infrastructure tree. (Right after init your
   `mesh/peers.json` is empty — mint fills it, and here it already has.)
2. **[`fort scaffold <fort>`](https://github.com/streetfortress/forts/blob/main/docs/reference/scaffold.md)**
   renders one fort's trees. Compare `nixos/hosts/outpost/`,
   `flux/clusters/outpost/`, and `flux/apps/outpost/` — the apps
   kustomization lists only the sops canary; workloads come later.
3. **[`fort mint <fort>`](https://github.com/streetfortress/forts/blob/main/docs/reference/mint.md)**
   generates the fort's identity. Compare `nixos/secrets/outpost.yaml`
   (every value sops ciphertext, `ENC[AES256_GCM,…]`), the fort's entry
   in `mesh/peers.json`, the re-rendered `mesh/ssh_config` and
   `mesh/known_hosts`, the console registry in
   `provisioning/console-passwords.yaml`, and the sops canary in
   `flux/apps/outpost/`. This branch is committed **post-mint**, with a
   throwaway identity — the next section says why that is safe.

Your names will differ — the estate, the mesh CIDR, the fort, the
hypervisor are yours. The shape is what to compare: which directories
exist, which files are rendered, and what stays empty until a later
verb fills it.

## The committed secrets are throwaway — and committed on purpose

This branch holds real sops ciphertext in the open:
`nixos/secrets/outpost.yaml`, the console registry, the webhook
placeholder. That is the intended shape of a sops repo — **the
ciphertext is committed**, and only the recipients listed in
`.sops.yaml` can decrypt it. Here not even they can: this estate was
minted with a throwaway admin age key that was destroyed after the
commit, so nothing in this branch decrypts for anyone, and none of
these keys was ever deployed anywhere. Your estate will look exactly
like this; the difference is that your admin key lives in
`~/.config/sops/age/keys.txt` and decrypts your files.

## What you'll see before mint

Until you mint, `fort check --all` fails with exactly three errors, and
that is by design — each one names its repair:

```
✗ flux/infrastructure/configs: kustomize build: … alertmanager-edge-config.sops.yaml: no such file or directory
✗ mesh/ssh_config: missing — `fort ssh-config --write` renders it from peers.json + the manifests
✗ nixos/secrets/outpost.yaml: sfi.ops.console.enable is set … but deployer-password-hash is absent … Mint it: fort mint outpost --write
```

The second and third disappear when mint runs; the first when you seed
the webhook secret. This branch sits past that point, so CI runs
`fort check --all --eval` directly on the checkout — green means the
committed reference itself checks and evaluates clean.

## Reading on

The CLI handbook lives in the forts repository:
[getting started](https://github.com/streetfortress/forts/blob/main/docs/README.md),
one page per verb under
[docs/reference](https://github.com/streetfortress/forts/blob/main/docs/reference).
