# Getting Started
The Auction House is a Solana program running on Mainnet Beta and Devnet. While you may interact with it like any other Solana program by sending transactions to a Solana node, Metaplex has built some tools to make working with it much easier. We have a **CLI** tool that will let you manage your auction house and a **JS SDK** to help you kick-start a user interface.

## CLI
See [CLI](https://docs.metaplex.com/programs/auction-house/cli) for how to set up and use the CLI.

## JavaScript SDK
The **JS SDK** provides an easy-to-use API to web developers to create and configure one's own Auction House. The SDK also allows developers to perform complicated procedures like bidding, listing, withdrawing funds from the Auction House treasury and fee accounts, and much more. 

The main module that interacts with the Auction House program is the [Auction House module](https://github.com/metaplex-foundation/js/tree/main/packages/js/src/plugins/auctionHouseModule). This module contains several methods that make the process of making marketplaces painless. You may access this client via the `auctionHouse()` method of your `Metaplex` instance.
```ts
const auctionHouseClient = metaplex.auctionHouse();
```

You can create a new Auction House with minimum input like so. By default, the current identity of the Metaplex instance will be the authority of the Auction House.

```ts
const { auctionHouse } = await metaplex
    .auctionHouse()
    .create({
        sellerFeeBasisPoints: 500, // 5% royalties
    })
    .run();
```

Lets go through some methods, grouped by their function.

**Creating and updating the Auction House:**
```ts
create(sellerFeeBasisPoints, requiresSignOff?, canChangeSalePrice?, auctioneerScopes?, treasuryMint?, payer?, authority?, feeWithdrawalDestination?, treasuryWithdrawalDestinationOwner?, auctioneerAuthority?, confirmOptions?);
update(auctionHouse, authority?, payer?, sellerFeeBasisPoints?, requiresSignOff?, canChangeSalePrice?, newAuthority?, feeWithdrawalDestination?, treasuryWithdrawalDestinationOwner?, auctioneerAuthority?, auctioneerScopes?, confirmOptions?);
```

**The 3 Core trading operations: bidding, listing and executing a sale:**
```ts
bid(auctionHouse, buyer?, authority?, auctioneerAuthority?, mintAccount, seller?, tokenAccount?, price?, tokens?, printReceipt?, bookkeeper?, confirmOptions?);
list(auctionHouse, payer?, seller?, authority?, auctioneerAuthority?, mintAccount, tokenAccount?, price?, tokens?, bookkeeper?, printReceipt?, confirmOptions?);
executeSale(bid, listing, auctionHouse, auctioneerAuthority?, bookkeeper?, printReceipt?, confirmOptions?);
```

Canceling a bid or a listing:
```ts
cancelBid(sellerFeeBasisPoints, requiresSignOff?, canChangeSalePrice?, auctioneerScopes?, treasuryMint?, payer?, authority?, feeWithdrawalDestination?, treasuryWithdrawalDestinationOwner?, auctioneerAuthority?, confirmOptions?);
cancelListing(auctionHouse, listing, auctioneerAuthority?, confirmOptions?);
```

**Getting balance of, depositing to and withdrawing funds from the `BuyerEscrowAccount`:**
```ts
depositToBuyerAccount(auctionHouse, buyer?, auctioneerAuthority?, payer?, amount, confirmOptions?);
getBuyerBalance(auctionHouse, buyerAddress, commitment?);
withdrawFromBuyerAccount(auctionHouse, buyer?, payer?, authority?, auctioneerAuthority?, amount, confirmOptions?);
```

**Withdrawing funds from the `FeeAccount` and the `TreasuryAccount`:**
```ts
withdrawFromFeeAccount(auctionHouse, authority?, payer?, amount, confirmOptions?);
withdrawFromTreasuryAccount(auctionHouse, authority?, authority, amount, confirmOptions?);
```

**Finding the Auction House, bids, listings and sales/purchases:**
```ts
findByAddress(address, auctioneerAuthority, commitment?);
findByCreatorAndMint(creator, treasuryMint, auctioneerAuthority?, commitment?);
findBidByReceipt(receiptAddress, auctionHouse, loadJsonMetadata?, commitment?);
findBidByTradeState(tradeStateAddress, auctionHouse, loadJsonMetadata?, commitment?);
findBidsBy(type, auctionHouse, publicKey, commitment?);
findListingByTradeState(tradeStateAddress, auctionHouse, loadJsonMetadata?, commitment?);
findListingByReceipt(receiptAddress, auctionHouse, loadJsonMetadata?, commitment?);
findListingsBy(type, auctionHouse, publicKey, commitment?);
findPurchaseByTradeState(sellerTradeState, buyerTradeState, auctionHouse, loadJsonMetadata?, commitment?);
findPurchaseByReceipt(receiptAddress, auctionHouse, loadJsonMetadata?, commitment?);
findPurchasesBy(type, auctionHouse, publicKey, commitment?);
```

**Load bids, listings and sales/purchases:**
```ts
loadBid(lazyBid, loadJsonMetadata?, commitment?);
loadListing(lazyListing, loadJsonMetadata?, commitment?);
loadPurchase(lazyPurchase, loadJsonMetadata?, commitment?);
```

There are also other methods that already exist in the Auction House module, and more methods will be added in the future. The *README* of the Auction House module will be updated with a detailed documentation of all these methods very soon.

**Helpful links:**
* [Github repository](https://github.com/metaplex-foundation/js/tree/main/packages/js/src/plugins/auctionHouseModule)
* [NPM package](https://www.npmjs.com/package/@metaplex-foundation/js-next)

## Auto-generated JavaScript library
This is a lower level, auto-generated JavaScript library, which gets generated whenever the Auction House program (written in Rust) gets updated. 

Therefore, this library is intended  for advanced developers who wish to interact with the program by preparing instructions and sending transactions directly.

**Helpful links:**
* [Github repository](https://github.com/metaplex-foundation/metaplex-program-library/tree/master/auction-house/js)
* [NPM package](https://www.npmjs.com/package/@metaplex-foundation/mpl-auction-house)

## UI Reference Implementation
Coming Soon
