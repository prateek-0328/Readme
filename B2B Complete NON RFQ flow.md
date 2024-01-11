# B2B Complete NON RFQ flow
The following is an illustrative flow to demonstrate a Non Request For Quotation(RFQ) flow.

## BUYER SEARCH & DISCOVERY
The buyer sends a search request through the buyer app(BAP). BPP sends a search request to the gateway 
which broadcasts the search request to multiple seller apps according to country,domain and city in context. 
The seller apps responds to the search request with an on search which is directly
sent to the buyer app. The on_search contains the product catalog which includes add-ons, variants, offers.

## SELECTION OF ITEMS AND PRICE NEGOTIATION
After recieving the catalogs from all the sellers throgh the on_search call, 
the buyer selects item/s, add_ons, variants and offers.The buyer app sends this 
through a select request to the seller app. The seller app responds to the request 
with a quote and breakup including logistics charges.

## ORDER INITIALIZATION 
After recieving the on_select from the seller app, for order initialization, the 
buyer app sends an init request with the billing details and address of delivery.The seller app confirms 
this through on_init and initializes the order.In case of prepaid payment collection 
by BPP,the payment URI and the TTL within which the payment must occur, is also sent in the on_init call.

## ORDER CONFIRMATION
To confirm the order initialization, the BAP sends a confirm request. This request 
is acknowledged and the order is confirmed by the BPP using the on_confirm request.

## PAYMENT COMMUNICATION
For notifying the status of the payment ,the on_status request is sent.If the 
payment status is successful, the on_status contains a transaction success message.
If the payment fails, the on_status contains "Payment Failed" message along with 
31004 error code.If the TTL expires, the BPP updates this to the BAP using the 
on_status, to which the BAP responds with a NACK with an error code 31004 and 
message 'Payment TTL Expired'.If the payment fails or TTL expires, the BAP sends a 
cancel request to cancel the order, which the BPP acknowledges using the on_cancel 
call. It also initiates any refund if the payment had already been made.

## REQUEST FOR ORDER STATUS
This is an optional call. the BAP can ask for the status of the transaction by 
sending a status request to enquire Order status or Tracking info. The BPP responds 
with an on_status.


  
