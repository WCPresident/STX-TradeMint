# STX-TradeMint - Decentralized Trading Platform

## Overview
TradeMint is a decentralized trading platform that allows users to buy and sell digital assets with secure escrow functionality. Built on blockchain technology, it ensures trustless transactions, reducing the risk of fraud while maintaining full transparency and security.

## Features
- **Decentralized Listings** – Users can list digital assets for sale with defined prices and expiration dates.
- **Secure Escrow** – Funds are held in escrow until trade conditions are met.
- **Event-Driven System** – Blockchain-based event logging for transparency.
- **Listing Expiry Management** – Automatically detects and marks expired listings.
- **Offer System** – Buyers can make offers on listings, enabling negotiation.
- **Admin Cleanup Functions** – Removes expired listings to maintain efficiency.
- **Read-Only Query Functions** – Fetch listings, offers, and status details.

## Smart Contract Constants
- **Contract Owner:** `tx-sender` (Deployer of the contract)
- **Error Codes:**
  - `u100` – Not authorized
  - `u101` – Listing not found
  - `u102` – Invalid status
  - `u103` – Insufficient balance
  - `u104` – No active offer
  - `u105` – Listing expired
  - `u106` – Invalid amount
  - `u107` – Offer not found
  - `u108` – Listing closed
  - `u109` – Already has an active offer

## Data Structures
### Listings
Holds asset listings:
```lisp
(define-map listings
    uint
    {
        seller: principal,
        asset: (string-ascii 32),
        price: uint,
        status: (string-ascii 10),
        expiry: uint,
        created-at: uint
    }
)
```

### Offers
Stores offers made by buyers:
```lisp
(define-map offers
    {listing-id: uint, buyer: principal}
    {
        amount: uint,
        status: (string-ascii 10),
        created-at: uint
    }
)
```

## Public Functions
### Create Listing
Creates a new asset listing with an expiry date.
```lisp
(define-public (create-listing (asset (string-ascii 32)) (price uint) (expiry uint))
```
- **Validations:**
  - Expiry must be in the future.
  - Price must be greater than zero.

### Cancel Listing
Cancels an active listing (only the seller can cancel their listing).
```lisp
(define-public (cancel-listing (listing-id uint))
```
- **Validations:**
  - Must be the seller.
  - Listing must be active.

### Cleanup Expired Listing
Marks an expired listing as "expired."
```lisp
(define-public (cleanup-expired-listing (listing-id uint))
```
- **Validations:**
  - Listing must be expired.
  - Listing must be active before cleanup.

## Read-Only Functions
### Get Listing
Fetches a listing by ID.
```lisp
(define-read-only (get-listing (listing-id uint))
```
### Get Offer
Retrieves offer details.
```lisp
(define-read-only (get-offer (listing-id uint) (buyer principal))
```
### Get Listing Count
Returns the total number of listings created.
```lisp
(define-read-only (get-listing-count))
```
### Check Expiry
Determines if a listing has expired.
```lisp
(define-read-only (is-expired (listing-id uint))
```

## Events
Events are triggered for key actions:
```lisp
(define-private (emit-event (event-type (string-ascii 20)) (listing-id uint) (principal-data (optional principal)))
```
### Emitted Events
- `listing-created`
- `listing-cancelled`
- `listing-expired`

## Security & Governance
- Only listing creators can cancel their listings.
- Admin cleanup ensures expired listings are handled properly.
- Transactions are immutable once confirmed.

## Future Enhancements
- **Multi-Asset Support** – Expand beyond string-based assets.
- **Smart Matching** – Automated buyer-seller matching.
- **Fee Mechanism** – Optional transaction fees for sustainability.
