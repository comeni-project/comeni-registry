# Contributing to the Comeni registry

This repository is **data**. A contract, a rule, a measurement, a type — each is a YAML file
with a citation, and adding one needs no Python.

The engine that reads this data lives in
[`comeni-project/Comeni-Labs`](https://github.com/comeni-project/Comeni-Labs), which mounts this
repository as a git submodule.

## Where a new file goes

**Wherever it reads best.** Every declared file opens with a `declares:` line naming what it is,
and the loader reads that line rather than the path — so there is no directory you have to find.

```yaml
declares: contract
id: nf-core/salmon/quant@1.10.3
```

The convention this layer uses, and a good default to copy:

| | |
|---|---|
| `tools/<namespace>/<tool>/` | a tool's contracts, and any type only that tool produces |
| `types/` | types more than one tool touches |
| `measurements/` | measurements — `declares: measurement` **and** an `id:` |
| `rules/<name>.rule.yml` | tier-3 decision tables |
| `roles.yml` | the roles a contract may fill |

Copy the nearest existing file. If you forget the `declares:` line the loader says `MD0010`; if a
type or measurement forgets its `id:`, `MD0012`. Run `mendel explain MD0010` for the long form.

## `docs/tools/` is generated — do not edit it

One page per tool, rendered from the files above. CI fails if a page disagrees with the data, so
a hand edit is refused rather than silently overwritten.

Regenerating needs the engine installed:

```bash
uv tool install "mendel-compiler @ git+https://github.com/comeni-project/Comeni-Labs@main#subdirectory=packages/mendel-compiler"
mendel docs --registry . --out docs/tools
```

**Stated plainly: authoring registry data needs no Python, but regenerating a page does.** CI
regenerating on your behalf was considered and rejected — a bot commit pushed into a pull request
means the diff a reviewer approved is not the diff that merges, and the reviewability of the
generated page is the entire argument for committing it.

## What CI checks, and what it does not

**Checked:** the layer loads. That is every `MD0001`–`MD0012` refusal, closed vocabularies (a
contract naming an undeclared state fails), rule validation against the parameters contracts
actually declare, and role coverage. Plus `docs/tools/` matching the data.

**Not checked, and it cannot be here:** whether a contract agrees with its nf-core module.
`MD0104`, `MD0105` and container drift compare a contract against a vendored `main.nf`, and this
repository has no `vendor/` — that copy is exactly what the split removed. **A contract naming a
process no module defines will merge here** and be caught in the Comeni-Labs pull request that
bumps the submodule pointer. Read the module before you write the contract.

## Versions and tags

This layer versions independently of the engine and is tagged `vX.Y.Z`. A `pipeline.yml` pins a
layer by content digest, so **moving a file moves the digest** — a rename is a real change to
every artifact built against it, not a tidy-up.

## Licence

Registry data is **CC-BY-4.0** (`LICENSE`). Contracts and rules cite papers, and attribution is
the currency of the field.
