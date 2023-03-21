---
comment:  Menu nesting required tbd 
title: Transfers
author: Relocating some of TK> HB
proofedDate: noProof
---

## Transfer out transactions

Within Qredo, transfer out transactions require [approval](approvals). Such transactions include:

- L2>L2 Transfer 

    An L2 to L2 transaction moves assets from one Qredo Wallet to another.

- L2>L1 Withdrawl 

    When users withdraw from a Qredo Wallet, this triggers an L2 to L1 transaction, i.e., assets from a Qredo Wallet move to a Wallet outside the Qredochain (on BTC, ETH, etc.). This involves a process known as crystallization.

- L2<>L2 Atomic swap

    A two-way L2 to L2 asset exchange, e.g., 100000000 ETH qweis for 735601 satoshis. Both parties that participate have one *transfer in* transaction and one *transfer out* transaction (that undergoes custody with their approvers).  

    This transaction type is discussed in more detail in the [Atomic swaps](/developer-guides/partner-api/atomic-swaps) section.


## Transfer in transactions

Trusted parties do not approve any inbound transactions (deposits, transfers in, or swaps in).