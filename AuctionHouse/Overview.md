# Auction House: Overview

## Introduction
Auction House is a program that allows users to exchange assets within the Solana blockchain. 

There are plenty of ways to exchange assets on Solana, so why another protocol focused on solving this problem? Let's dive deep into that.

The ethos of this program is to allow anyone to create and configure their own marketplace and even provide their own custom logic on how assets should be exchanged. The motivation behind the Auction House protocol is to create a healthy ecosystem of marketplaces that focus on different use-cases, and more importantly, each bringing their own flavor into the way they allow users to trade assets.

The most important aspect of the Auction House protocol is that it provides ownership of assets to the user.

Traditionally, as soon as the user lists an asset on a marketplace, the asset is moved from the user's wallet into a wallet known as the [Escrow](https://www.investopedia.com/terms/e/escrow.asp) wallet owned by the marketplace and is kept or **escrowed** there until the asset is delisted or sold. This poses some concerns:
* The user can not list the same asset on multiple marketplaces
* The user has to rely on the marketplaces’ escrow contracts to safely hold their asset.

This is where Auction House shows its power. Its a transaction protocol that allows marketplaces to implement an **escrow-less** sales contract, thus providing ownership of assets to the users.


## Creating an Auction House
The Auction House program can be used to make a marketplace by creating a new **Auction House Instance**. The `Auction House Instance` is a [Program Derived Address (PDA)](https://solanacookbook.com/core-concepts/pdas.html) which is in the keyspace of the Auction House program, ie, the `Instances` are simply public keys owned by the Auction House program.

![](https://i.imgur.com/2HPpM9g.png =300x)

The Instance can be configured in the whatever way the user wants. Here are some important configurable parameters:

* `requireSignOff`: this allows marketplaces to gate which asset can be listed, bought and sold. For every action: list, bid, sale, the instance [authority](https://docs.solana.com/staking/stake-accounts#understanding-account-authorities) needs to sign the transaction
* `canChangeSalePrice`: `canChangeSalePrice` is only intended to be used with AuctionHouses with `requireSignOff` set to `true`. This allows the Auction House to do complicated order matching to find the best price for the seller.
* `sellerFeeBasisPoints`: the share of the sale the marketplace takes on all trades
    
## Listing and Bidding
Once the marketplace is active, any trader can post an offer to "sell" an asset on the marketplace. When this happens, the Auction House does two things:

1. Auction House creates a `Sell Order`: in other words, creates a PDA known as the `SellerTradeState` which represents the offer. Trade States are special PDAs that are very cheap in comparison to other PDAs / Accounts. This is because these PDAs only have 1 byte of data, which is the bump of the PDA. All the details of a listings such as list price, amount of tokens, mint account etc, are hashed into the seeds of the PDA and therefore the PDA acts as a "proof of existence" for that listing.

![](https://i.imgur.com/ki27Ds8.png)

2. Auction House also assigns the another PDA: `programAsSigner` PDA as the `Delegate`. Delegates are a feature of the Solana SPL-token program and are discussed in detail [here](https://spl.solana.com/token#authority-delegation). Delegation allows the Auction House Instance to pull assets out of a token account when a sale goes through at a later point.

![](https://i.imgur.com/aIRl7Hb.png)

Similar to the `Sell Order`, when a buyer places a bid, Auction House creates the `BuyerTradeState` PDA representing the bid. The bid amount (native or SPL tokens) then needs to be transferred manually to the `BuyerEscrowAccount` PDA, which holds this amount till the sale goes through.
    
## Executing a Sale
As soon as the buyer and the seller have both posted there bids and offers respectively, the trade can be completed by calling the `executeSale` instruction, which is a permission-less crank: in other words, can be executed by anyone without any fee or reward.

The `BuyerEscrowAccount` will transfer the tokens to the seller (minus Auction House fees). The seller's token account will send the asset to the buyer via a delegate transfer, completing a trade.
![](https://i.imgur.com/gpAX63m.png)


There are three trade scenarios in which the `executeSale` instruction may be called in different ways:

* **Bid + Sale in same transaction**: Alice lists an asset, Bob matches the offer and the sale goes through immediately.
* **Offer + Sale in same transaction**: Bob makes an offer of 5 SOL for an unlisted asset owned by Alice, Alice likes the offer and lists the asset for 5 SOL thus completing the sale immediately.
* **Offer, Bid, Sale in three separate instructions**: Alice lists an asset for 5 SOL, Bob makes an offer for this asset for 3 SOL, Alice likes the offer and agrees to the sale.
    
## Auctioning Fungible Assets
An asset can be any SPL token and doesn't have to be an NFT ("Non-Fungible Token"). 

The only constraint is that the Mint Account of the SPL Token need to have a Metadata account attached to it as a PDA. You can read more about this in the [Token Metadata program documentation](https://docs.metaplex.com/programs/token-metadata/overview).
    
## Buying asset using a custom SPL Token
You can configure any SPL-token to act as the currency of your own Auction House (as opposed to defaulting to using native SOL).

This can be achieved by setting the `treasuryMint` parameter in the Auction House Instance Account to the mint account of the SPL-token of your liking.

## Custom Order Matching
As mentioned before, Auction House allows marketplaces to implement their own custom order matching algorithms. This is done using the 3rd and the final Trade State in the Auction House protocol: the `FreeSellerTradeState`.

If the buyer intentionally lists their NFT for a price of 0, a new `FreeSellerTradeState` is made. The Auction House can then change the sale price to match a matching Bid that is greater than 0. This allows the Auction house to do complicated order matching to find the best price for the seller.

## Auctioneer
The current Auction House implementation is designed with instant sales in mind and currently has no features that enable the various auction types that have become popular in the Solana ecosystem (ex. ***Timed auctions***) 

Auctioneer is a customized contract type, written by the user, that uses the composability pattern of Auction House to control an individual Auction House instance. 

To fully enable Auctioneer to use an Auction House instance's instructions, it must be explicitly delegated. 

![](https://i.imgur.com/RyZUfR9.png)

## Next steps
In this page we have gone through the very basics of the Auction House protocol and the power it possesses. There is whole lot more Auction House is capable of and a lot more that can be configured to your liking.

To get a good understanding of how the Auction House works, we first need to dive a little deep into the Accounts and the Instructions that are are a part of the Auction House. Kindly go through these pages to get a good unserstanding of the above:
* [Getting Started]()
* [Accounts]()
* [Instructions]()
* [Executing Sales]()
* [Cancelling Bids and Listings]()

If you're building your own marketplace from scratch, its important to understand how you can keep track of all the listings, bids and sales. This is discussed in detail in this section:
* [Printing Receipts]()

We birefly touched upon Auctioneer and Timed Auctions. These sections elaborate on how these can be setup and how Auctioneer adds utility and power to the already powerpacked Auction House program:
* [Auctioneer Interface]()
* [Auctioneer Scopes]()
* [Auctioneer Examples (timed auctions etc.)]()
    
## Additional Reading Material
* Prof Lupin's Auction House guide: https://proflupin.xyz/metaplex-auction-house
* Jordan's twitter thread: https://twitter.com/redacted_j/status/1453926144248623104
* Armani's twitter thread: https://twitter.com/armaniferrante/status/1460760940454965248
