<p align="center">
  <img src="readme-images/examples-banner.png" alt="Estate Examples" height="160">
</p>

<p align="center">
    Try out <em><a href="https://github.com/streetfortress/forts">forts</a></em> using one of these <strong>example estates.</strong>
</p>

An _estate_ is a git repository declaring one or more _forts_:
- `estate.toml` declares the **Mesh** network CIDR and source repository URL.
- `forts/<fort>.toml` declares a fort's size and its place on the estate's **Mesh** network.

---

## Repo Structure

Each branch of this repository is a complete estate: clone it, make it
yours, mint it, and deploy. This branch, `main`, is the smallest one — a
single fort on a libvirt hypervisor.

| branch | what it shows |
| --- | --- |
| `main` | the minimal estate: one plain fort, the anonymous (public https) clone route |
| `link-side-a` | an estate whose fort joins another estate's mesh — the linking side |
| `link-side-b` | the estate being linked to — the recording side |

Planned: a three-fort estate with a central monitoring node, and a
multi-region layout. SFI packages common workload patterns (monitoring,
object storage) as fort layers; the examples here wire everything by
hand so you can see every piece.

## What's in this branch

One estate named `example`, with one fort:

- `estate.toml` — the estate's facts: its name, its git URL, the mesh
  (`10.10.0.0/24`), the operator keys.
- `forts/keep.toml` — the fort's declaration: a 2-vCPU lab VM on a
  libvirt hypervisor.
- `nixos/`, `flux/`, `mesh/`, `provisioning/` — everything `fort init`
  and `fort scaffold` render. Nothing here was written by hand except
  this README and the manifest.

The estate ships **unminted**: no identity, no secrets, no keys of any
kind. The admin recipient and operator key committed here are
placeholders whose private halves were discarded — they exist so the
files parse, and you replace both.

## Make it yours

1. Clone this branch and push it to your own repository **as `main`** —
   a deployed fort reconciles its repo's main branch, so the example
   must become your main, not live beside it.

   ```
   git clone -b main https://github.com/streetfortress/estate-examples my-estate
   cd my-estate && git remote set-url origin <your repo> && git push -u origin main
   ```

2. Edit `estate.toml`: set `[source] repo` to your repository's URL, and
   replace `operator_keys` with your device's SSH public key.
3. Replace the admin recipient (an age public key, `age1…`) in
   `.sops.yaml` and `nixos/.sops.yaml` with your own. `age-keygen` makes
   one; keep the private half safe, it decrypts every secret you mint.
4. Point `forts/keep.toml`'s `[libvirt] uri` and `ip` at your hypervisor
   and network. If you change other values later, note that
   `fort scaffold` never overwrites: delete `nixos/hosts/keep/` and
   `flux/clusters/keep/` first, then re-run `fort scaffold keep --write`.
5. Mint the fort's identity, then provide the two files init cannot
   write (`provisioning/secrets.yaml` per `provisioning/README.md`, and
   the Alertmanager webhook secret):

   ```
   fort mint keep --write
   ```

6. Commit, push, then build and deploy:

   ```
   fort image keep
   fort provision keep --image ~/.cache/forts/keep-nixos.qcow2
   ```

## The expected pre-mint state

Until you mint, `fort check --all` fails with exactly three errors, and
that is by design — each one names its repair:

```
✗ flux/infrastructure/configs: kustomize build: … alertmanager-edge-config.sops.yaml: no such file or directory
✗ mesh/ssh_config: missing — `fort ssh-config --write` renders it from peers.json + the manifests
✗ nixos/secrets/keep.yaml: sfi.ops.console.enable is set … but deployer-password-hash is absent … Mint it: fort mint keep --write
```

The second and third disappear when mint runs; the first when you seed
the webhook secret. CI on this branch proves the path: on every push it
mints an ephemeral identity, seeds a placeholder webhook, and runs
`fort check --all --eval` — green means the ritual above lands on a
clean estate.

## Reading on

The CLI handbook lives in the forts repository:
[getting started](https://github.com/streetfortress/forts/blob/main/docs/README.md),
one page per verb under
[docs/reference](https://github.com/streetfortress/forts/blob/main/docs/reference).


