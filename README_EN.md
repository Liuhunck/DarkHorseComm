# DarkHorse Community

DarkHorse Community: A full on-chain order book DEX built for the Pharos network. Submitted for the Order Book DEX track of GWDC2026.

[中文版本 / Chinese Version](README.md)

## Project Overview

This repository is the main entry point for **DarkHorse Community / DarkHorse DEX**. The core codebases live in Git submodules (smart contracts and frontends).

## Core Features

- **On-chain order book matching (Order Book DEX)**: limit orders, market orders, cancel, and matching execution.
- **Vault-based custody model**: deposit/withdraw with matching performed via internal balance changes.
- **Market data**: aggregated order book depth by price levels (levels), last trade price, etc.
- **One-stop dev/test/deploy**: contract compile/tests + deploy/verify scripts, plus minimal testing UI and product frontends.

## Repository Structure (Submodules)

Most of the project lives in these submodules:

- **contracts**: Pharos smart contracts development / deployment / verification code (Hardhat).
- **test**: a minimal frontend for contract testing (quickly connect to chain and call contracts).
- **community**: frontend for **DarkHorse Community**.
- **dex**: frontend for **DarkHorse DEX**.

## Docs

More detailed documentation:

- Overview: [docs/README.md](docs/README.md)
- Contract API: [docs/contracts/MultiBaseOrderBookDEXVaultLevels.md](docs/contracts/MultiBaseOrderBookDEXVaultLevels.md)
- Contract parallelism notes: [docs/contracts/MultiBaseOrderBookDEXVaultLevels.parallelism.md](docs/contracts/MultiBaseOrderBookDEXVaultLevels.parallelism.md)
- Contract testing & coverage: [docs/contracts/TestingAndCoverage.md](docs/contracts/TestingAndCoverage.md)
- Community routes & pages: [docs/community/ROUTES.md](docs/community/ROUTES.md)
- DEX pages: [docs/dex/PAGES.md](docs/dex/PAGES.md)

## Tech Stack

- **Smart contracts**: Solidity + Hardhat (deploy/verify scripts included)
- **Frontends**: Vue 3 + Vite
    - `community`: Vue 3 / Vite / Tailwind CSS / Vue Router
    - `dex/frontend`: Vue 3 / Vite / ethers.js
    - `test/frontend`: Vue 3 / Vite / ethers.js
- **Chain interaction**: ethers.js
- **Package manager**: npm (each subproject manages its own dependencies)

## Quick Start (Run in ~5 minutes)

Prerequisites: **Git** + **Node.js** (recommended Node 20+; `dex/frontend` declares an engines requirement in its `package.json`).

### 1) Clone with submodules

```bash
git clone --recurse-submodules <THIS_REPO_URL>
cd DarkHorseComm
```

If you already cloned the repo:

```bash
git submodule update --init --recursive
```

### 2) Run DarkHorse Community frontend (fastest)

```bash
cd community
npm install
npm run dev
```

### 3) Optional: Run DarkHorse DEX frontend

```bash
cd dex/frontend
npm install
npm run dev
```

### 4) Optional: Run the minimal contract testing frontend

```bash
cd test/frontend
npm install
npm run dev
```

> If any frontend starts successfully with `npm run dev`, you are “up and running” within 5 minutes.

## Configuration

### `contracts` (smart contracts)

The contracts project relies on environment variables (read by `contracts/hardhat.config.js` and `contracts/scripts/*`):

- `PHAROS_ATLANTIC_URL`: Pharos Atlantic RPC URL
- `TEST_ACCOUNT_0`: private key for deployment/testing
- (optional) `ETH_SEPOLIA_URL`: if you want to use/test Sepolia
- (optional) `ETHERSCAN_API_KEY`: explorer API key style config (Pharos uses `customChains`)
- (optional) `PHAROS_VERIFY_AUTH`: if the verify API requires auth

Note: `contracts` uses `@chainlink/env-enc`. You can export env vars in your shell, or manage an encrypted env file (see `contracts/.env.enc`).

### `community` (frontend)

Optional env var:

- `VITE_SERVER_IP`: backend server base URL (defaults to `http://localhost:3000` if not set; used for avatar/resource paths).

### `dex` / `test` (frontends)

To point to deployed contract addresses, these frontends typically use `.env` (`VITE_*`) or local config files. Please refer to each submodule’s README/code.

## Usage Examples

### Compile and test contracts

```bash
cd contracts
npm install
npm run compile
npm test
```

### Deploy to Pharos Atlantic (example: levels-based order book contract)

Bash/macOS/Linux:

```bash
cd contracts
export PHAROS_ATLANTIC_URL=<YOUR_RPC_URL>
export TEST_ACCOUNT_0=<YOUR_PRIVATE_KEY>
npm run deploy:orderbook:levels -- --quote <QUOTE_TOKEN_ADDRESS>
```

PowerShell (Windows):

```powershell
cd contracts
$env:PHAROS_ATLANTIC_URL = "<YOUR_RPC_URL>"
$env:TEST_ACCOUNT_0 = "<YOUR_PRIVATE_KEY>"
npm run deploy:orderbook:levels -- --quote <QUOTE_TOKEN_ADDRESS>
```

### Pull the latest commits for all submodules (tracking the configured branches)

```bash
git submodule update --remote --merge
```

## FAQ

### 1) Why are submodule folders empty after cloning?

You probably didn’t fetch submodules:

```bash
git submodule update --init --recursive
```

### 2) `npm install` fails or Node version mismatch

- Upgrade Node to 20+ (recommended).
- Delete the subproject’s `node_modules` and re-install.

### 3) Deploy scripts complain about missing `PHAROS_ATLANTIC_URL` or `TEST_ACCOUNT_0`

They are required env vars. Set them in the current shell session before running deploy/verify scripts.

## Contact

- Please use GitHub Issues for questions, bug reports, and discussions.
- Submodule maintainers:
    - https://github.com/Liuhunck
    - https://github.com/qinyh10300
    - https://github.com/Yyhhh6
