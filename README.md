# Payment Collected by Seller App (BPP)
This document explains the flow of how the payment is collected by the BPP from the buyer (BAP) and what all processes happen to make this possible. 

## on_search :
The on_search request is sent by the BPP for the payment.
### If at the BPP level, the payment is collected by the seller app for all the providers, or for Inventory Seller Node(ISN) seller apps:
The on_search contains the payments array is associated to the whole catalog inside the message body.
"message": {
  "catalog": {
    "payments": [
                  {
                    "id": "1",
                    "type": "PRE-FULFILLMENT",
                    "collected_by": "BPP"
                  },
                  {
                    "id": "2",
                    "type": "ON-FULFILLMENT",
                    "collected_by": "BPP"
                  },
                  {
                    "id": "3",
                    "type": "POST-FULFILLMENT",
                    "collected_by": "BPP"
                  }
                  ],
            }
          }
### If at the BPP level, the payment is collected by the seller app for some selected providers:
The payment array is associated to the particular provider who chooses to collect the payment through the seller app. 
"providers": [
              {
                "id": "P1",
                "payments": [
                              {
                                "id": "1",
                                "type": "PRE-FULFILLMENT",
                                "collected_by": "BPP"
                              },
                              {
                                "id": "2",
                                "type": "ON-FULFILLMENT",
                                "collected_by": "BPP"
                              },
                              {
                                "id": "3",
                                "type": "POST-FULFILLMENT",
                                "collected_by": "BPP"
                              }
                            ]
              }
            ]
## init
The init request is sent by the buyer app(BAP). This request may contain an optional TTL which allows the BAP to give a time window for which the payment transaction will be active. If the transaction is still in pending state at the end of the TTL, then the BAP will terminate the transaction.
"payments": [
              {
                "type": "PRE-FULFILLMENT",
                "collected_by": "BPP",
                "tags": [
                          {
                            "descriptor": {
                                            "code": "BPP_payment"
                                          },
                            "list": [
                                      {
                                        "descriptor": {
                                                        "code": "ttl"
                                                      },
                                        "value": "PT1H"
                                      }
                                    ]
                          }
                        ]
              }
            ]

## on_init
The seller app sends the on_init payload to the BAP. The on_init contains the payment details which are necessary for the payment. It also contains a signature which has the value of a signed uri which is signed by the ED25519 . This uri can be used by the buyer to render the payment gateway and make the payment.
"payments": [
        {
          "type": "PRE-FULFILLMENT",
          "collected_by": "BPP",
          "uri": "https://snp.com/pg",
          "@ondc/org/buyer_app_finder_fee_type": "percent",
          "@ondc/org/buyer_app_finder_fee_amount": "0",
          "@ondc/org/settlement_basis": "delivery",
          "@ondc/org/settlement_window": "P1D",
          "@ondc/org/withholding_amount": "10.00",
          "@ondc/org/settlement_details": [
            {
              "settlement_counterparty": "buyer-app",
              "settlement_phase": "sale-amount",
              "settlement_type": "upi",
              "beneficiary_name": "xxxxx",
              "upi_address": "gft@oksbi",
              "settlement_bank_account_no": "XXXXXXXXXX",
              "settlement_ifsc_code": "XXXXXXXXX",
              "bank_name": "xxxx",
              "branch_name": "xxxx"
            }
          ],
          "tags": [
            {
              "descriptor": {
                "code": "BPP_payment"
              },
              "list": [
                {
                  "descriptor": {
                    "code": "signature"
                  },
                  "value": "xxxxxxxxxxxxxx"
                },
                {
                  "descriptor": {
                    "code": "dsa"
                  },
                  "value": "ED25519"
                }
              ]
            }
          ]
        }
      ]

## status
The seller can send a status request to know the state of the payment transaction. The BPP returns this request with on_status.

## on_status 
This is an unsolicited call which returns with the status of the payment. The returned status can be either of the three : 
### * paid
### * unpaid
### * pending

## TTL expire: 
### When the TTL mentioned by the buyer expires with either no response or pending status, the buyer initiates a cancel request. This cancel request contains a cancellation_reason_id to explain the reason of cancellation.
 "message": {
             "cancellation_reason_id": "022"
            }
### Payment failed:
### When the payment fails, the on_status returns status: 'payment failed'  in the error object inside the message body.
"error": {
          "code": "31004",
          "message": "Payment Failed"
         }
## Payment Success: 
### Once the on_status is sent as paid to the BAP, The seller app sends a confirm object, confirming the whole payment procedure.


