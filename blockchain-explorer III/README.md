# Judecoin Blockchain Explorer

Privacy-preserving Judecoin mainnet explorer with a professional English-only interface. It provides public block, transaction, Service Node, staking, and quorum data while keeping private transfer participants, addresses, and amounts out of the explorer.

## Live data

The browser calls the same-origin `GET /api/chain` endpoint. The worker then makes only these hard-coded, read-only public RPC calls:

- `GET /get_info`
- JSON-RPC `get_block_headers_range`
- JSON-RPC `get_block` for blocks that contain public transaction hashes
- JSON-RPC `get_service_nodes` with an explicit field allowlist
- JSON-RPC `get_quorum_state` for Service Node testing quorums
- `GET /get_transaction_pool`
- `POST /get_transactions` with transaction JSON and structured `tx_extra` decoding
- `POST /get_outs` when a public node permits ring-member resolution
- `GET https://www.judeblock.net/api/emission` for indexed total mined supply

The chain summary returns an allowlisted projection containing only network status, block height/hash/time/size/difficulty, transaction hash, containing block, confirmations, aggregate PoS staking statistics, service-node public keys, and public node status. A user-triggered service-node detail lookup additionally returns public registration fields: operator/contributor addresses and the node's published network endpoint. It never requests or returns private keys, wallet balances, private transaction participants, or private transfer amounts. It has no transaction broadcasting or other write method. Block and transaction history use bounded server-side pagination parameters; clients still cannot select an RPC host or method.

Public RPC hosts are fixed in `worker/index.ts`; clients cannot submit a node URL or arbitrary RPC method. Responses are cached briefly and the UI refreshes every 30 seconds. If every public node is unavailable, the page shows an explicit error state and never substitutes fabricated chain values.

The explorer never scrapes explorer HTML. Current blocks, transactions, Service Nodes, chain health, and quorums come from allowlisted Judecoin RPC methods. The staking ratio uses the structured Judecoin emission index API and displays `N/A` if that feed is unavailable or stale instead of substituting a value. Deregistration history is generated from public chain data by `scripts/sync-deregistered-nodes.mjs`; the UI displays the indexed-through height instead of presenting the historical index as live RPC state.

## Local preview

Requires Node.js 22.13 or newer.

```bash
pnpm install
pnpm run dev
```

Open `http://localhost:3000`.

## Verification

```bash
pnpm run build
```
