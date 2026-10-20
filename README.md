# GenFed

**An open, read-only protocol for publishing genealogical data about deceased people — so anyone can mirror, reuse and build on it.**

> Status: **early design (v0.1 draft)**. Nothing to install yet. Feedback welcome via Issues.

## Why

Most family-history data sits in closed silos. Even open projects expose it only as one-off dumps or ad-hoc APIs. GenFed defines a small, boring way for any genealogy site — big platform or a static page built from a GEDCOM file — to publish its public records and a feed of changes, so others can stay in sync.

## Design principles

- **No new formats.** Person documents are [GEDCOM X](https://github.com/FamilySearch/gedcomx) JSON (`application/x-gedcomx-v1+json`); the change feed is [ActivityStreams 2.0](https://www.w3.org/TR/activitystreams-core/) `OrderedCollection`.
- **Pull-only.** A publisher only serves data. No inbox, no write API, no incoming moderation.
- **Living people never leave the instance** — not as nodes, not as edges, not as placeholders.
- **Licence per record.** Every source carries its own `rights`, so mixed-licence datasets (CC0, CC BY-SA…) stay correctly attributed.
- **Resumable mirroring.** A client stores a cursor and continues where it stopped.

## Protocol sketch (v0.1)

| Endpoint | Returns |
|---|---|
| `/.well-known/genfed` | version, outbox URL, person URL template, default licence |
| `GET …/persons/{id}` | GEDCOM X document: the person + edges to deceased neighbours (as stubs) |
| `GET …/outbox` | `OrderedCollection` (`totalItems`, `first`, `last`) |
| `GET …/outbox?after={seq}` | `OrderedCollectionPage` of `Update` (embedded GEDCOM X) or `Delete` → `Tombstone` |

The feed is compacted: each person appears once, at the position of its latest change. A full mirror = read from `after=0`.

## Planned deliverables

1. **Specification** — Markdown, JSON-LD context, JSON Schema; proposed as a [FEP](https://codeberg.org/fediverse/fep).
2. **`@genfed/core`** — TypeScript types, validator, outbox client with resume.
3. **`genfed` CLI** — `pull` (export to GEDCOM 7 for Gramps, webtrees…), `mirror` (JSONL), `validate` (conformance check).
4. **`genfed publish`** — generate a static GenFed site from a GEDCOM / Gramps XML file. No server needed.
5. **Gramps Web integration.**

## First publisher

[Human Lineage](https://humanlineage.org) will be the first GenFed publisher. Its public dataset (2.7M deceased people from Wikidata, GEDCOM X, CC BY-SA 4.0) is already available: [Zenodo DOI 10.5281/zenodo.23284943](https://doi.org/10.5281/zenodo.23284943) · [Hugging Face](https://huggingface.co/datasets/HumanLineage/open-genealogical-dataset).

Human Lineage itself is closed-source. GenFed is independent of it: the protocol and all tools in this repository are open and work with any data source.

## Use of generative AI

- Code and documentation in this repository will be written by the maintainer. AI tools may be used only read-only (e.g. reviewing code for bugs), and every such use will be disclosed in the commit message.

## Licence

[Apache-2.0](LICENSE)

## Contact

Michał Kurasiński — open an [issue](https://github.com/HumanLineage/GenFed/issues).
