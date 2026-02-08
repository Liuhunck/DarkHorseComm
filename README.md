# DarkHorse Community

DarkHorse Community: A full on-chain order book DEX built for the Pharos network. Submitted for the Order Book DEX track of GWDC2026.

## Overview

This repository is the entry point for the DarkHorse Community project. The main codebases live in the Git submodules below.

## Submodules

- **contracts**: Pharos chain smart contracts development, deployment, and verification code.
- **test**: A minimal frontend used for contract testing.
- **community**: Frontend code for **DarkHorse Community**.
- **dex**: Frontend code for the **DarkHorse DEX**.

## Getting Started

Clone with submodules:

```bash
git clone --recurse-submodules <THIS_REPO_URL>
```

If you already cloned the repo:

```bash
git submodule update --init --recursive
```

To pull the latest commits for all submodules (tracking the configured branches):

```bash
git submodule update --remote --merge
```
