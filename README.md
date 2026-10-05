# Maui Contracts

CosmWasm smart contracts for a lending money market, taken from [Anchor Protocol](https://github.com/Anchor-Protocol/money-market-contracts) on Terra as the starting point for Maui.

The money market lets people deposit stablecoins to earn a steady yield, and lets borrowers take stablecoin loans against liquid staking tokens like bLUNA and bETH. Borrowers pay interest, and the staking rewards from their collateral help fund the deposit yield. If a loan gets too close to its limit, the collateral can be liquidated.

## Contracts

| Contract | What it does |
| --- | --- |
| `overseer` | Keeps track of every borrower's collateral and borrow limit, and decides when a position can be liquidated |
| `market` | Takes stablecoin deposits and handles borrowing and repaying |
| `custody_bluna` | Holds bLUNA collateral and collects its staking rewards |
| `custody_beth` | Holds bETH collateral and collects its rewards |
| `interest_model` | Works out the borrow rate from how much of the market is being used |
| `distribution_model` | Sets how fast reward tokens are handed out to borrowers |
| `oracle` | Price feed for the collateral assets |
| `liquidation` | An over-the-counter contract where liquidators can buy seized collateral |
| `liquidation_queue` | A bidding queue where liquidators place bids at a discount ahead of time |

Shared types and messages live in `packages/moneymarket`.

## Building

You'll need Rust with the `wasm32-unknown-unknown` target and Docker for optimized builds.

```bash
git clone https://github.com/AI-pro017/maui-contracts.git
cd maui-contracts
rustup target add wasm32-unknown-unknown
cargo test
```

Each contract folder also has its own shortcuts, so inside `contracts/market` you can run `cargo unit-test`, `cargo integration-test` or `cargo wasm`.

For deployable `.wasm` files:

```bash
docker run --rm -v "$(pwd)":/code \
  --mount type=volume,source="$(basename "$(pwd)")_cache",target=/code/target \
  --mount type=volume,source=registry_cache,target=/usr/local/cargo/registry \
  cosmwasm/workspace-optimizer:0.11.5
```

The optimized contracts end up in `artifacts/`.

The code targets CosmWasm 0.16 and Terra's `terra-cosmwasm` 2.2. There's no `Cargo.lock` in the repo, so a fresh build can pull in newer dependency versions that no longer match the code. If you hit type errors, pin the dependencies to their 2021 versions.

## License

Apache 2.0, see [LICENSE](LICENSE). The original contracts were written by Terraform Labs for Anchor Protocol.
