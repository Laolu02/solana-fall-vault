## What

Adds a configurable per-transaction withdrawal limit to the lamports vault.

## Why

Prevents a single withdrawal from draining more than the configured amount, limiting damage from a leaked key or buggy client.

## How

* Adds `max_withdraw: u64` to `VaultState` (appended to preserve existing byte offsets).
* `initialize` now accepts `max_withdraw: u64` and stores it in `VaultState`.
* `withdraw` rejects `amount > max_withdraw` with `MaxWithdrawalExceeded`.
* Withdrawals exactly at the limit are allowed.
* Existing PDA, deposit, ownership, and signer logic remains unchanged.

## API Change

Before:

```rust
initialize()
```

Now:

```rust
initialize(max_withdraw)
```

Example:

```rust
initialize(ONE_SOL) // 1 SOL
```

## Testing

Updated existing tests to pass the new `max_withdraw` argument to `initialize`.

* `test_deposit.rs`: updated initialization calls; all **5 pass**.
* `test_initialize.rs`: updated `initialize` instruction calls and verifies initialization; all **3 pass**.
* `test_withdraw.rs`: updated initialization calls and added coverage for withdrawals below, exactly at, and above the limit; all **7 pass**.

`anchor build && cargo test` — **16 passed, 0 failed**.
