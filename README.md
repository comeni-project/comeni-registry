# comeni-registry

The declared data [Mendel](https://github.com/comeni-project/Comeni-Labs) resolves against:
module contracts, decision rules, state vocabularies and measurement declarations.

This repository **is** a registry layer. Its root is the layer, so it can be pointed at
directly:

```bash
git clone https://github.com/comeni-project/comeni-registry
uv run mendel build --goal my-goal.yml --registry ./comeni-registry --out build/
```

Layers stack, and later ones win. A laboratory keeps its own overlay private and puts it
after this one:

```bash
uv run mendel build --goal my-goal.yml \
  --registry ./comeni-registry --registry ./lab-registry --out build/
```

## What is not true about this yet

**This is not a curated registry.** Every contract here is a test fixture that happens to be
true — enough to build and run the RNA-seq spine, hand-written, and checked against the
vendored nf-core modules by `mendel build`'s conformance stage. Nothing in it has been
validated by a laboratory for clinical use, and "curated" in Mendel means *a named human
signed off*, which has not happened for anything here.

It is published now because a registry that lives inside the tool's repository is not a
registry — it is a fixture directory with ambitions. Splitting it is what makes the
distribution model real, and doing that before there is much data is cheaper than after.

## What is in it

| Directory | What it declares |
|---|---|
| `contracts/` | what a module consumes, produces and is called with |
| `rules/` | decision tables: measured data → a value or a module |
| `vocabularies/` | the states each type may carry, and how it enters a pipeline |
| `measurements/` | what can be measured, its kind, bounds and citation |

`registry.yml` names the layer. It sits at the root beside `contracts/` rather than inside
it, because Mendel globs `*.yml` recursively under `contracts/` and would otherwise try to
read the manifest as a module contract.

## Contributing

Contracts arrive through review, not directly. A contract is a hand-written binding to a
module, and the failure mode is that it drifts from the module it describes without anything
noticing — so `mendel build` refuses to emit when a contract disagrees with its module, and
`mendel explain M0104` says why. Run it before opening a pull request.

Vocabularies are **closed**: a contract naming an undeclared state fails to load. New states
are a reviewed data change, never a code change.

See [CONTRIBUTING.md](https://github.com/comeni-project/Comeni-Labs/blob/main/CONTRIBUTING.md)
in the main repository.

## Licence

Registry data is [CC-BY-4.0](LICENSE). Contracts cite papers, so attribution matters.

Mendel's source code is Apache-2.0 and lives in
[Comeni-Labs](https://github.com/comeni-project/Comeni-Labs). The nf-core modules these
contracts describe keep their own licences and are not redistributed here.
