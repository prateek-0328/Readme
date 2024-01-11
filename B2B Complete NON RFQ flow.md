# B2B Complete NON RFQ flow
The following is an illustrative flow to demonstrate a Non Request For Quotation(RFQ) flow.

## BUYER SEARCH & DISCOVERY
The buyer initiates the process by submitting a search request through the Buyer App (BAP). The Buyer Product Platform (BPP) relays this request to the gateway, which then broadcasts it based on country, domain, and city parameters. Relevant information is sent directly to the buyer app in an on_search message, providing a detailed product catalog, including add-ons, variants, and offers.

## SELECTION OF ITEMS AND PRICE NEGOTIATION
Upon receiving catalogs through the on_search call, the buyer makes selections, choosing items, add-ons, variants, and offers. The Buyer App communicates these choices through a select request to the BPP. The BPP responds with a quote, offering a detailed breakdown, including logistics charges, for further negotiation.

## ORDER INITIALIZATION 
Upon finalizing selections and negotiations, the buyer app sends an init request to the BPP, providing billing details and a delivery address. The BPP confirms the initiation through an on_init call, initializing the order. In cases of prepaid payment collection by BPP, the on_init call includes a payment URI and a Time-To-Live (TTL) period within which payment must occur.

## ORDER CONFIRMATION
To confirm the order initialization, the BAP sends a confirm request. The BPP acknowledges this request through an on_confirm response, officially confirming the order.

## PAYMENT COMMUNICATION
The BPP communicates payment status through the unsolicited on_status request.If the 
payment status is successful, the on_status contains a transaction success message.
If the payment fails, the on_status contains "Payment Failed" message along with 
31004 error code.If the TTL expires, the BPP updates the BAP using the 
on_status, to which the BAP responds with a NACK with an error code 31004 and 
message 'Payment TTL Expired'.In case of payment failure or TTL expiration, the Buyer App can send a cancel request to annul the order, which the BPP acknowledges with an on_cancel call. It also initiates any refund if the payment had already been made.

## REQUEST FOR ORDER STATUS
This is an optional call where the Buyer App can inquire about the status of the transaction by sending a status request, seeking order status or tracking information. The BPP responds with an on_status message.


  
