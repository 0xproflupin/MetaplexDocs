# Auction House Receipts

## Introduction

To aid transaction / activity tracking on marketplaces, the Auction House program supports the generation of receipts for listings, bids and sales.

In addition to printing receipts, Auction House automatically cancels receipts when the corresponding instruction (bid, listing or sale) is cancelled.

Let us see how receipts are printed.

## Printing Receipts

Receipts can be printed for the three main trade actions on an Auction House. Here are these actions with corresponding JS SDK functions we have gone through:

1. Listing: `createListing()`
2. Bidding: `createBid()`
3. Executing a Sale: `buy()`, `sell()`, `executeSale()`

In each of the above functions, a receipt can be printed which holds important information about the underlying transaction. There are two fields that can be introduced to each function above to print the corresponding receipt:

1. `printReceipt`: This is a boolean field that defaults to `true`. When this field is set to `true`, a receipt is printed for the corresponding function.

2. `bookkeeper`: The address of the bookkeeper wallet responsible for the receipt. In other words, the bookeeper is the wallet that paid for the receipt. It's only responsibility at this time is tracking the payer of the receipt so that in the future if the account is allowed to be closed the program knows who should be refunded for the rent. This field defaults to `metaplex.identity()`.

<Accordion>
<AccordionItem title="JS SDK" open={true}>
<div className="accordion-item-padding">

Here's an example of printing receipts for bid, list and execute sale instructions.
     
```tsx
// printing the ListReceipt
await metaplex
    .auctionHouse()
    .createListing({
        printReceipt: true,
        bookkeeper: metaplex.identity()
    })

// printing the BidReceipt
await metaplex
    .auctionHouse()
    .createBid({
        printReceipt: true,
        bookkeeper: metaplex.identity()
    })

// printing the PurchaseReceipt
await metaplex
    .auctionHouse()
    .executeSale({
        printReceipt: true,
        bookkeeper: metaplex.identity()
    })
```

</div>
</AccordionItem>
</Accordion>

## Conclusion

Now that we know how to print receipts for easy transaction tracking, how do we actually fetch details regarding these actions in practice? Let us explore ways to find bids, listings and sales for an Auction House in the [next page](#TODO).
