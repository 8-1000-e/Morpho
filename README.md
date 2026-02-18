# Morpho

A Solana protocol for **dynamic NFTs** that evolve based on on-chain activity, with **NFT-backed lending** where evolved NFTs unlock better borrowing terms.

## Concept

NFTs that aren't static JPEGs — they change, grow, and gain utility over time.

**Dynamic NFTs** — Mint an NFT that visually evolves as you interact with the protocol. Your on-chain activity (trading volume, holding duration, staking) determines your NFT's tier:

```
Bronze → Silver → Gold → Diamond
```

Each tier unlocks a new visual form and better protocol benefits.

**NFT-Fi Lending** — Use your Morpho NFT as collateral to borrow SOL. Your NFT's tier directly impacts your loan terms:

| Tier | LTV Ratio | Max Duration |
|------|-----------|-------------|
| Bronze | 30% | 7 days |
| Silver | 45% | 14 days |
| Gold | 60% | 30 days |
| Diamond | 75% | 60 days |

If the floor price drops below the liquidation threshold or the loan expires, the NFT gets liquidated.

## Architecture

Two Solana programs that interact via CPI:

```
┌─────────────────────┐      ┌─────────────────────┐
│  morpho-nft         │      │  morpho-lend         │
│                     │ CPI  │                     │
│  - Mint NFTs        │◄────►│  - Deposit NFT      │
│  - Track activity   │      │  - Borrow SOL       │
│  - Update tiers     │      │  - Repay + reclaim  │
│  - Evolve metadata  │      │  - Liquidation      │
└─────────────────────┘      └─────────────────────┘
         │                            │
         ▼                            ▼
   Metaplex Core               Switchboard Oracle
   (NFT standard)              (Floor price feed)
```

### Programs

- **`morpho-nft`** — Dynamic NFT engine. Handles minting, activity tracking, tier calculation, and on-chain metadata updates via Metaplex Core.
- **`morpho-lend`** — Lending protocol. Vault system for NFT collateral, SOL borrowing with tier-based LTV, and liquidation mechanics.

### Tech Stack

- **Programs**: Rust / Anchor
- **NFT Standard**: Metaplex Core (MPL Core)
- **Oracles**: Switchboard (floor price feeds)
- **Frontend**: Next.js / React
- **Client**: @solana/kit
- **Testing**: Bankrun / LiteSVM

## Project Structure

```
programs/
├── morpho-nft/          # Dynamic NFT program
│   └── src/
│       ├── lib.rs
│       ├── state.rs     # NFT state, tier data, activity tracker
│       ├── instructions/
│       │   ├── mint.rs
│       │   ├── record_activity.rs
│       │   └── evolve.rs
│       ├── errors.rs
│       └── constants.rs
│
├── morpho-lend/         # Lending program
│   └── src/
│       ├── lib.rs
│       ├── state.rs     # Vault, loan, liquidation state
│       ├── instructions/
│       │   ├── deposit.rs
│       │   ├── borrow.rs
│       │   ├── repay.rs
│       │   └── liquidate.rs
│       ├── errors.rs
│       └── constants.rs
│
app/                     # Next.js frontend
tests/                   # Integration tests
migrations/              # Deploy scripts
```

## Roadmap

- [ ] **Phase 1** — morpho-nft: Mint, activity tracking, tier system, metadata evolution
- [ ] **Phase 2** — morpho-lend: Vault, borrow, repay, liquidation
- [ ] **Phase 3** — CPI integration between programs
- [ ] **Phase 4** — Frontend: Mint page, NFT viewer, lending dashboard
- [ ] **Phase 5** — Oracle integration for floor price feeds

## Learning Goals

This is a learning project focused on:
- Metaplex Core (new NFT standard on Solana)
- Oracle integration (Switchboard)
- Cross-program invocation (CPI) between custom programs
- Vault pattern and liquidation mechanics
- On-chain state machines

## License

MIT
