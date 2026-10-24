# Vendored solana-remote-wallet 3.1.14

This directory is the `solana-remote-wallet` 3.1.14 crate as published on
crates.io, with one change in `src/ledger.rs`: `get_configuration_vector`
accepts a `GET_APP_CONFIGURATION` response of 5 bytes or more instead of
exactly 5.

## Why

Ledger Stax, Flex and Nano Gen5 return a 7-byte configuration vector (the
Nano X and Nano S+ return 5). The unpatched crate rejects it with
`Version packet size mismatch` and the device never enumerates, so this tool
reports `no device found` for a wallet that is plugged in, unlocked and on the
Solana app. Reported as anza-xyz/agave#15099 and fixed on agave `master` by
anza-xyz/agave#15100 and anza-xyz/agave#15543 (backported to the `v4.4`
branch). No release on the 3.x line the `solana-*` pins in this repository
follow includes the fix, and `solana-clap-v3-utils` 3.1.14 resolves hardware
wallets through this same crate, so the override covers both paths.

`[patch.crates-io]` in the root `Cargo.toml` points `solana-remote-wallet`
at this directory. Remove the patch and this directory once the pins move to a
release that includes the fix.

## Verify against crates.io

```bash
curl -sL https://crates.io/api/v1/crates/solana-remote-wallet/3.1.14/download | tar xz
diff -r solana-remote-wallet-3.1.14 vendor/solana-remote-wallet
```

The only differences are the hunk in `src/ledger.rs`, this file, and the
removed packaging files (`.cargo_vcs_info.json`, `Cargo.toml.orig`,
`Cargo.lock`).
