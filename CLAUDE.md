# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This repository contains the **Panama Papers dataset** as CSV files intended for import into a Neo4j graph database. There are no scripts, build system, or application code — the repo is a data-only store.

## Git LFS

All CSV files are tracked via Git Large File Storage (LFS). The files in the working tree are LFS pointer files unless LFS has been initialized and the objects pulled. To get actual data:

```bash
git lfs install
git lfs pull
```

Without this, reads of any `.csv` file will return a short pointer stub rather than the real data.

## Data Files

Six CSV files make up the full dataset (sizes from LFS metadata):

| File | Size | Content |
|---|---|---|
| `nodes-entities.csv` | ~208 MB | Shell companies, trusts, and foundations |
| `nodes-officers.csv` | ~137 MB | Directors, shareholders, and beneficiaries (UTF-8 without BOM) |
| `nodes-addresses.csv` | ~81 MB | Postal addresses linked to entities/officers |
| `relationships.csv` | ~72 MB | Edges connecting all node types |
| `nodes-intermediaries.csv` | ~4 MB | Law firms and agents that set up the structures |
| `nodes-others.csv` | ~420 KB | Miscellaneous other node types |

## Graph Model

The dataset represents a property graph with five node labels (`Entity`, `Officer`, `Address`, `Intermediary`, `Other`) and a single `relationships.csv` that encodes all edges between them. The `node_id` column in each node file is the join key used by `relationships.csv` to reference source and target nodes.

## Known Data Issue

`nodes-officers.csv` was originally stored with a UTF-8 BOM; it was re-encoded to plain UTF-8 in commit `f764b5b`. If importing into Neo4j and seeing encoding errors on this file, verify the BOM was stripped (`file nodes-officers.csv` should report `UTF-8 Unicode text`, not `UTF-8 Unicode (with BOM) text`).
