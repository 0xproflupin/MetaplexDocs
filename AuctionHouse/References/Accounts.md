---
description: "Auction House Accounts"
---

# Accounts

## Auction House
Name: AuctionHouse

The `AuctionHouse` account is a Program Derived Address (PDA) which is derived from a given public key and, optionally, an SPL Token to use a currency. This PDA, also referenced as the Auction House `Instance`, is controlled by the `Authority`: the public key used to create the Auction House.

Auction House Instance PDA stores a variety of information, including important accounts that are needed to operate an Auction House like [FeeAccount](), [BuyerEscrowAccount](), [TreasueryAccount]() and many more.

This PDA is also the only point of configuration in the Auction House program and offers a variety of ways to custom tune your Auction House, like setting the currency of exchange for your marketplace, which can be an SPL token or SOL, marketplace royalties on every sale, etc.

### Seeds:
`prefix`: Literal: auction_house; The program's prefix for PDA seeds.
`creator`: The public key of the account which created the Auction House Instance.
`treasuryMint`: The mint account of the SPL-token to be used as the currency of exchange in the marketplace.

### Fields:
`auctionHouseFeeAccount`: The public key of the `FeeAccount` which stores funds for paying for Auction House related transactions in behalf of the users.
`auctionHouseTreasury`: The public key of the `TreasuryAccount` which stores the funds received on every sale, as marketplace royalty.
`feeWithdrawalDestination`: The public key of the account to which the funds can be withdrawn from the `FeeAccount`.
`treasuryWithdrawalDestination`: The public key of the account to which the funds can be withdrawn from the `TreasuryAccount`.
`treasuryMint`: The mint account of the SPL-token to be used as the currency of exchange in the marketplace.
`authority`: The public key of the account which controls the Auction House and has the ability to configure it.
`creator`: The public key of the account which created the Auction House Instance.
`bump`: The `Bump` of the Auction House Instance PDA.
`treasuryBump`: The `Bump` of the `TreasuryAccount` PDA.
`feePayerBump`: The `Bump` of the `FeeAccount` PDA.
`sellerFeeBasisPoints`: The royalties shared by the marketplace in basis points — i.e. 550 means 5.5%.
`requiresSignOff`: Allows marketplaces to gate which assets can be listed and which bids can be placed. On every relevant instruction, the Auction House authority needs to sign the transaction. This also allows the marketplaces to implement their own order matching algorithms.
`canChangeSalePrice`: Is only intended to be used on Auction Houses with `requireSignOff` set to `true`. This allows the Auction House to perform custom order matching to find the best price for the seller.
`escrowPaymentBump`: The `Bump` of the `BuyerEscrowAccount` PDA.
`hasAuctioneer`: True if an `Auctioneer` instance exists for the given Auction House instance.
`auctioneerAddress`: The public key of the `Auctioneer` PDA.

## Auctioneer
Name: Auctioneer

The `Auctioneer` account is a PDA which uses the composability pattern of the Auction House program to control an Auction House Instance.

The Auctioneer has the ability to be given the control, or Delegation over an Auction House instance using the `DelegateAuctioneer` instruction which we will discuss in the [instructions page]().

This PDA stores only a small amount of information in comparison to the AuctionHouse Instance: the AuctionHouse Instance, the Auctioneer authority and the bump of the PDA.

### Seeds:
`prefix`: Literal: auction_house; The program's prefix for PDA seeds.
`auctionHouse`: The public key of the Auction House Instance PDA which the Auctioneer controls.
`auctioneerAuthority`: The public key of the account which controls the Auctioneer and has the ability to configure it.

### Fields:
`auctioneerAuthority`: The public key of the account which controls the Auctioneer and has the ability to configure it.
`auctionHouse`: The public key of the Auction House Instance PDA which the Auctioneer controls.
`bump`: The `Bump` of the `Auctioneer` PDA.

## Bid Receipt
Name: BidReceipt

To aid transaction tracking, Auction House supports the generation of receipts for listings, bids, and sales.

The `BidReceipt` account is a PDA which gets generated on calling the `PrintBidReceipt` instruction. This PDA helps in tracking the bids generated for assets listed in a given Auction House.

### Seeds:
prefix: Literal: listing_receipt; The program's prefix for PDA seeds.
buyerTradeState: The public key of the `BuyerTradeState` PDA.

### Fields:
`tradeState`: The public key of the `BuyerTradeState` PDA.
`bookkeeper`: The public key of the wallet that paid for the receipt. Its used to track the payer so that in the future if the account is closed, the Auction House program knows who should be refunded for the rent.
`auctionHouse`: The public key of the Auction House Instance PDA for which the `BidReceipt` is generated. 
`buyer`: The public key of the user who is bidding for the asset.
`metadata`: [Optional] The public key of the PDA which stores metadata related to the bid.
`tokenAccount`: The public key of the account which stores information regarding the auctioned asset.
`purchaseReceipt`: The public key of the corresponding `PurchaseReceipt` if the asset which is bid upon is sold.
`price`: The bid price for the asset.
`tokenSize`: The number of tokens which are bid upon (can be > 1 for SPL tokens / semi-fungible tokens).
`bump`: The `Bump` of the `BidReceipt` PDA.
`tradeStateBump`: The `Bump` of the `BuyerTradeState` PDA.
`createdAt`: The block at which the receipt was created.
`canceledAt`: The block at which the receipt was cancelled.

## Listing Receipt
Name: ListingReceipt

The `ListingReceipt` account is a PDA which gets generated on calling the `PrintListingReceipt` instruction. This PDA helps in tracking the listings created for assets for a given Auction House.

### Seeds:
`prefix`: Literal: bid_receipt; The program's prefix for PDA seeds.
`sellerTradeState`: The public key of the `SellerTradeState` PDA.

### Fields:
`tradeState`: The public key of the `SellerTradeState` PDA.
`bookkeeper`: The public key of the wallet that paid for the receipt. Its used to track the payer so that in the future if the account is closed, the Auction House program knows who should be refunded for the rent.
`auctionHouse`: The public key of the Auction House Instance PDA for which the `ListingReceipt` is generated. 
`seller`: The public key of the user who is listing the asset.
`metadata`: [Optional] The public key of the PDA which stores metadata related to the listing.
`purchaseReceipt`: The public key of the corresponding `PurchaseReceipt` if the listed asset is sold.
`price`: The listing price for the asset.
`tokenSize`: The number of tokens which are listed (can be > 1 for SPL tokens / semi-fungible tokens).
`bump`: The `Bump` of the `ListingReceipt` PDA.
`tradeStateBump`: The `Bump` of the `SellerTradeState` PDA.
`createdAt`: The block at which the receipt was created.
`canceledAt`: The block at which the receipt was cancelled.

## Purchase Receipt
Name: PurchaseReceipt

Similar to the `BidReceipt` and the `ListingReceipt`, the `PurchaseReceipt` account is a PDA which gets generated on calling the `PrintPurchaseReceipt` instruction. This PDA helps in tracking the sales for a given Auction House.

### Seeds:
`prefix`: Literal: purchase_receipt; The program's prefix for PDA seeds.
`buyerTradeState`: The public key of the `BuyerTradeState` PDA.
`sellerTradeState`: The public key of the `SellerTradeState` PDA.

### Fields:
`bookkeeper`: The public key of the wallet that paid for the receipt. Its used to track the payer so that in the future if the account is closed, the Auction House program knows who should be refunded for the rent.
`buyer`: The public key of the user who is bid for the asset.
`seller`: The public key of the user who listed the asset.
`auctionHouse`: The public key of the Auction House Instance PDA for which the `PurchaseReceipt` is generated. 
`metadata`: [Optional] The public key of the PDA which stores metadata related to the purchase.
`tokenSize`: The number of tokens which are purchased (can be > 1 for SPL tokens / semi-fungible tokens).
`price`: The price at which the asset was purchased.
`bump`: The `Bump` of the `PurchaseReceipt` PDA.
`createdAt`: The block at which the receipt was created.

## Buyer Trade State
Name: BuyerTradeState

The `BuyerTradeState` is a PDA that represents the bid for a given asset, encoding the buy order.

This is a special PDA: it only stores 1 byte of data, the bump of the PDA and is therefore very cheap.

All the metadata associated with the bid: the Auction House authority, token account of the asset, bid price and token size, are hashed into the seeds of the `BuyerTradeState`. 

### Seeds:
`prefix`: Literal: auction_house; The program's prefix for PDA seeds.
`buyerWallet`: The public key of the wallet used by the user to make the bid.
`auctionHouse`: The public key of the Auction House Instance PDA.
`treasuryMint`: The mint account of the SPL-token to be used as the currency of exchange in the marketplace.
`tokenAccount`: The public key of the account which stores information regarding the auctioned asset.
`buyerPrice`: The price at which the bidder wants to buy the asset.
`tokenSize`: The number of tokens that the user has bid upon. This number could be greater than 1 for SPL / fungible tokens.

### Fields:
`bump`: The `Bump` of the `BuyerTradeState` PDA.

## Seller Trade State
Name: SellerTradeState

The `SellerTradeState` is a PDA that represents the listing for a given asset, encoding the sell order.

All the metadata associated with the listing: the Auction House authority, token account of the asset, listing price and token size, are hashed into the seeds of the `SellerTradeState`. 

### Seeds:
`prefix`: Literal: auction_house; The program's prefix for PDA seeds.
`sellerWallet`: The public key of the wallet used by the user to make the bid.
`auctionHouse`: The public key of the Auction House Instance PDA.
`treasuryMint`: The mint account of the SPL-token to be used as the currency of exchange in the marketplace.
`tokenAccount`: The public key of the account which stores information regarding the auctioned asset.
`mintAccount`: The mint account of the asset that is being auctioned.
`buyerPrice`: The price at which the user has listed the asset.
`tokenSize`: The number of tokens that the user has listed. This number could be greater than 1 for SPL / fungible tokens.

### Fields:
`bump`: The `Bump` of the `SellerTradeState` PDA.

## Free Seller Trade State
Name: FreeSellerTradeState

The `FreeSellerTradeState` is a PDA which gets generated when a user lists an asset at 0 SOL on an Auction House with `canChangeSalePrice` set as `true`. The `FreeSellerTradeState` is a proof of a listing which allows the Auction House to perform custom matching for the listed asset. This enables the seller to find an ideal matching bid for the listed asset and removes a lot of the manual work of listing at different prices to find a bid.

All the metadata associated with the listing: the Auction House authority, token account of the asset, token size, are hashed into the seeds of the `FreeSellerTradeState`. 

### Seeds:
`prefix`: Literal: auction_house; The program's prefix for PDA seeds.
`sellerWallet`: The public key of the wallet used by the user to make the bid.
`auctionHouse`: The public key of the Auction House Instance PDA.
`treasuryMint`: The mint account of the SPL-token to be used as the currency of exchange in the marketplace.
`tokenAccount`: The public key of the account which stores information regarding the auctioned asset.
`mintAccount`: The mint account of the asset that is being auctioned.
`buyerPrice`: The price at which the user has listed the asset.
`tokenSize`: The number of tokens that the user has listed. This number could be greater than 1 for SPL / fungible tokens.

### Fields:
bump: The `Bump` of the `FreeSellerTradeState` PDA.

## Fee Account
Name: FeeAccount

The `FeeAccount` PDA stores the funds to pay for the fees required to execute Auction House related transactions.

A very important feature of the Auction House program is that the user listing / bidding upon an asset doesn't pay the fee for doing so, rather the Auction House pays it.

The `FeeAccount` has to be manually topped up with funds.

### Seeds:
`prefix`: Literal: auction_house; The program's prefix for PDA seeds.
`creator`: The public key of the account which created the Auction House Instance.
`feePayer`: A literal to differentiate the `FeeAccount` PDAs.

### Fields:
`bump`: The `Bump` of the `FeeAccount` PDA.

## Treasury Account
Name: TreasuryAccount

The `TreasuryAccount` PDA stores all the royalties collected by the marketplace for every sale that goes through the given Auction House instance. 

### Seeds:
`prefix`: Literal: auction_house; The program's prefix for PDA seeds.
`auctionHouse`: The public key of the Auction House Instance PDA.
`treasury`: A literal to differentiate the `TreasuryAccount` PDAs.

### Fields:
`bump`: The `Bump` of the `TreasuryAccount` PDA.
    
## Escrow Payment Account
Name: EscrowPaymentAccount

When a user makes a bid on an asset, the marketplace makes sure to transfer the bid amount from the user's wallet to the `EscrowPaymentAccount` PDA. These funds are stored in the PDA until the sale is executed, after which the amount is transferred from the `EscrowPaymentAccount` to the seller's wallet.

There are Deposit / Withdraw instructions to deposit and withdraw tokens (native or SPL) from the `EscrowPaymentAccount` which we are going to talk about in the [instructions]() page.

### Seeds:
`prefix`: Literal: auction_house; The program's prefix for PDA seeds.
`auctionHouse`: The public key of the Auction House Instance PDA.
`buyerWallet`: The public key of the wallet used by the user to make the bid.

### Fields:
`bump`: The `Bump` of the `EscrowPaymentAccount` PDA.

## Program As Signer
Name: ProgramAsSigner

The `ProgramAsSigner` is a PDA which the Auction House assigns as the [Delegate]() for the execution of a sale once a listing and a bid match for a given asset.

We will talk about Delegates in detail in the [Executing a sale]() page.

### Seeds:
`prefix`: Literal: auction_house; The program's prefix for PDA seeds.
`signer`: A literal to differentiate the `ProgramAsSigner` PDAs.

### Fields:
`bump`: The `Bump` of the `ProgramAsSigner` PDA.
    
