# Fei Protocol / Rari Capital — Reentrancy Exploit PoC (2022)

**Category:** Logic / Math Errors (Reentrancy in Compound fork)
**Loss:** ~$80M
**Chain:** Ethereum
**Date:** April 30, 2022

## Overview
Multiple Rari Capital Fuse pools (acquired by Fei Protocol) were drained through a reentrancy vulnerability in the `exitMarket()` and borrow functions. The Compound-fork codebase did not properly account for CEI violations when interacting with ETH (native) markets, where `msg.sender.call{value:}()` triggers fallback functions. The attacker re-entered the borrow function during an ETH transfer, bypassing liquidity checks.

## How It Works
1. **VulnerableFusePool** simulates a Compound-fork lending pool with ETH collateral
2. The `borrow()` function sends ETH before fully updating state
3. **FuseAttacker** deposits collateral, then exploits reentrancy in borrow to extract more than allowed
4. **FixedFusePool** shows the CEI-corrected version with nonReentrant guard

## Run
```bash
forge install
forge test -vvvv
```

## Key Takeaway
Compound forks are particularly susceptible to reentrancy when they support native ETH markets, since ETH transfers via `call{value:}` trigger fallback functions. This vulnerability was known but went unfixed across multiple Rari Fuse pools.

## Disclaimer
Educational purposes only. Simplified simulation of the vulnerability pattern.
