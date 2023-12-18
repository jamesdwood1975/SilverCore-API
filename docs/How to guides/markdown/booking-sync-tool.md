:::(Error) (**In this article**, as part of our series of How-Tos, we'll look at how to use the Booking Sync tool.)
:::

## Intro
The Booking Sync tool is designed for Partners using SilverAgent to manage or create new bookings outside their application. It enables the synchronization of events and event details between SilverAgent and partner applications.

This event-level communication allows Partners to automatically process data and sync it to their booking record with the latest details from SilverCore.

SilverAgent wraps the response from SilverCore in a JSON object, which includes:

- An eventType to describe the event performed in SilverAgent.
- A timestamp that defines when the action was performed.
- XML body containing the response SilverCore.

The connectivity enabled by the Booking Sync Tool is shown in the following diagram:

<p align="center">
  <img width="80%" height="80%" src="https://files.document360.io/c53b2e89-8de3-47ad-adf4-5a70ab761260/Images/Documentation/SilverAgent-connectivity.drawio.png">
</p>

## Security

All communication between SilverAgent and partner microservices utilizes OAuth.

Booking sync is set to work with OAuth client credential grant. To communicate correctly with a given endpoint, the Partner must supply the following information to SilverRail:

- The endpoint for the application that the sync tool will communicate with. For example: `http://your-company/v1/api/booking-sync/event/send`
- `client-id`
- `client-secret`
- `access-token-uri` (`_` is not accepted)
- Scope (optional)

The [Certification-Next](/v1/docs/accessing-silvercore#accessing-certification-environments){target="_blank"} and [Production](/v1/docs/accessing-silvercore#accessing-the-production-environment){target="_blank"} environments use different security information for each environment.

Booking sync security can be configured by the SA account group&mdash;a backend SilverAgent configuration that consists of a context or group of contexts. Multiple endpoints can be configured for a Partner. Contact SRT support for specific details.

## Design

The following diagram illustrates the workflow:

<p align="center">
  <img width="80%" height="80%" src="https://files.document360.io/c53b2e89-8de3-47ad-adf4-5a70ab761260/Images/Documentation/SilverRail%20diagram%20collection-SilverAgent%20workflow.drawio.png">
</p>

## Messages, Supported Actions, and Events

JSON messages are sent to the partner system when bookings are:

- Created
- Modified (including Exchanged)
- Canceled
- Voided

The following four sections explain the actions that SilverAgent performs when bookings are canceled, created, modified, or voided.

:::(Info) (The **SilverCore Component** column contains the most common data scenarios in SilverCore when the action is performed. Further data updates could occur in addition to the items listed.)
:::

### BOOKING_CANCELLED

| eventAction | Level | SilverCore component |
| :-- | :-- | :-- |
| ORDER_CANCELLED (An order was canceled) | Order or Ticket | [cancelBookingRecordResponse](/v1/docs/cancelbookingrecordresponse-elements){target="_blank"} without the booking record<br><br>- Order will be `RELEASED`/`CANCELLED`/`WITHDRAWN`/`REPLACED`, depending on the previous status and type of cancellation that occurred.<br>- revenueTotal reflects the product reversal and sets the booking status to `CREDIT`, if applicable.<br>- For partial cancellations, the original order goes to`REPLACED`, and a new order in `CONFIRMED` or `TICKETED` status gets created. |

Full cancellation and partial cancellation fall under the same eventAction. The Booking Sync development team is currently investigating splitting these into a unique category or action or providing the order and ticketableFares impacted by the cancellation.

<!---@Gayle Green is this still true
--->

### BOOKING_CREATED

| eventAction | Level | SilverCore component |
| :-- | :-- | :-- |
| NEW_BOOKING (Must have at least one order) | Booking | [createBookingRecordResponse](/v1/docs/creatingbookingrecordresponse-elements){target="_blank"}<br><br>- A new booking number will be created, and the response contains the entire booking record with all orders. |

### BOOKING_MODIFIED

The BOOKING_MODIFIED category has numerous events associated with it.

| eventAction | Level | SilverCore component |
| :-- | :-- | :-- |
| BOOKING_CONFIRMED (An order was confirmed) | Order | [addPaymentResponse](/v1/docs/addpaymentresponse-elements){target="_blank"}<br>[confirmBookingRecordResponse](/v1/docs/confirmbookingrecordresponse-elements){target="_blank"}<br><br>- Order moves from `BOOKED` to `CONFIRMED`.<br>- order\fulfillmentInformation\valueDocument created.<br>- Additional information not contained in the original booking, such as seatAssignments, may be present depending on the supply channel.|
| CARD_COLLECTION_ALLOW_SET (Confirmation Information for at the station ticket pickup is set to pick up with any card collect (UK only) | Order | [updateBookingRecordResponse](/v1/docs/updatebookingrecordresponse-elements){target="_blank"}<br><br>- No change in response data. |
| COUPONS_RECEIVED (Coupons marked as received) | Order | returnValueDocumentResponse<br><br>- If all coupons are received, the order status moves to `RETURNED`.<br>- If a portion of coupons is received, the order will stay in `CONFIRMED` or `TICKETED` status.<br>- valueDocument\coupon\couponReceivedDate. |
| CUSTOM_INFO_ADDED (Booking custom information added) | Booking | [updateBookingRecordResponse](/v1/docs/updatebookingrecordresponse-elements){target="_blank"}<br><br>- customInformation. |
| DISCOUNT_APPLIED  (A negative service fee applied to the booking) | Booking | [updateBookingRecordResponse](/v1/docs/updatebookingrecordresponse-elements){target="_blank"}<br><br>- financials\prices\price<br>- `revenueTotal` reflects the discount applied.  |
| FEE_APPLIED  (A positive service fee applied to the booking) | Booking | [updateBookingRecordResponse](/v1/docs/updatebookingrecordresponse-elements){target="_blank"}<br><br>- financials\prices\price<br>- `revenueTotal` reflects the fee applied. |
| NOTE_ADDED (A booking note added to the booking) | Booking | [updateBookingRecordResponse](/v1/docs/updatebookingrecordresponse-elements){target="_blank"}<br><br>- notes. |
| ORDER_ADDED (An order added to the booking) | Booking | [updateBookingRecordResponse](/v1/docs/updatebookingrecordresponse-elements){target="_blank"}<br><br>- The existing booking record will contain an additional order container.<br>- `revenueTotal` reflects the new order amount added to the total. |
| ORDER_EXCHANGED  (An order was exchanged) | Order and/or Ticket | [processExchangeResponse](/v1/docs/processexchangeresponse-elements){target="_blank"}<br><br>- The original order goes to `REPLACED`, and a new order in `CONFIRMED` or `TICKETED` status gets created.<br>- financials\prices and financials\payments are updated.<br>- `revenueTotal` and `receiptsTotal` are updated to reflect the exchanged order and additional payment, if applicable. |
| PASSENGER_CUSTOM_INFO_ADDED  (Passenger custom information added) | Booking | [updateBookingRecordResponse](/v1/docs/updatebookingrecordresponse-elements){target="_blank"}<br><br>- passenger\customInformation. |
| PASSENGER_INFO_UPDATED (Passenger details updated) | Booking | [updateBookingRecordResponse](/v1/docs/updatebookingrecordresponse-elements){target="_blank"}<br><br>- passenger record has updated information. |
| PAYMENT_ADDED  (Payment added to the booking) | Booking | [addPaymentResponse](/v1/docs/addpaymentresponse-elements){target="_blank"}<br><br>- financials\payments\payment\@type="PAYMENT"<br>-`receiptsTotal` reflects the payment added. |
| PERSONAL_INFO_DELETED  (Cardholder information deleted) | Booking | [deleteCardholderInfoResponse](/v1/docs/deletecardholderinforesponse-elements){target="_blank"} |
| REFERENCE_DETAILS_ADDED (External partner information added to the booking) | Booking | [updateBookingRecordResponse](/v1/docs/updatebookingrecordresponse-elements){target="_blank"}<br><br>- externalIdentification\referenceNumber<br>- externalIdentification\purchaseOrderNumber<br>- externalIdentification\customerNumber |
| REFUND_AUTHORIZED (Previously returned coupons have been authorized to receive a refund) | Order | returnValueDocumentResponse<br><br>- valueDocument\coupon\refundApprovedDate<br>- valueDocument\coupon\authorization<br>- `revenueTotal` is lowered and reflects the amount of product authorized for a refund. |
| REFUND_ISSUED  (A refund applied on a booking) | Booking | [refundBookingRecordResponse](/v1/docs/refundbookingrecordresponse-elements){target="_blank"}<br><br>- financials\payments\payment\@type="REFUND"<br>-`receiptsTotal` reflects the refund applied. |
| SHIPMENT_TRACKING_INFO_ADDED (Shipment tracking information added on a by mail ticket delivery option) | Order | [updateBookingRecordResponse](/v1/docs/updatebookingrecordresponse-elements){target="_blank"}<br><br>- order\shipmentTrackingInformation|
| SUPPLIER_NOTE_ADDED  (Supplier note added to order) | Order | [updateBookingRecordResponse](/v1/docs/updatebookingrecordresponse-elements){target="_blank"}<br><br>- order\supplierNotes |
| TDO_OPTIONS_REMOVE (Ticket delivery option removed from an order) | Order | [updateBookingRecordResponse](/v1/docs/updatebookingrecordresponse-elements){target="_blank"}<br><br>- order\fulfillmentInformation removed.<br>- `revenueTotal` reflects the TDO, if applicable. |
| TDO_OPTIONS_SET (Ticket delivery option added to order) | Order | [updateBookingRecordResponse](/v1/docs/updatebookingrecordresponse-elements){target="_blank"}<br><br>- order\fulfillmentInformation added; `revenueTotal` reflects the TDO, if applicable. |
| TICKETS_CLAIMED | Order | [claimValueDocumentResponse](/v1/docs/claimvaluedocumentresponse-elements){target="_blank"}<br><br>- Order moves from `CONFIRMED` to `TICKETED`.<br>- Partner must use a [redeliverValueDocumentRequest](/v1/docs/redelivervaluedocumentrequest-elements){target="_blank"} to obtain the E-Ticket information (URL, PDF, etc) to relay to the customer. |
| UPDATE_ON_CREATE (On initial booking creation, the ticket delivery options are set and, if applicable, custom information are added) | Order | [updateBookingRecordResponse](/v1/docs/updatebookingrecordresponse-elements){target="_blank"}<br><br>- order\fulfillmentInformation added.<br>- customInformation.<br>- `revenueTotal` reflects the TDO, if applicable. |

### BOOKING_VOIDED

| eventAction | Level | SilverCore component |
| :-- | :-- | :-- |
| ORDER_VOIDED An order was voided | Order | [cancelBookingRecordResponse](/v1/docs/cancelbookingrecordresponse-elements){target="_blank"} without the booking record |

## Sample JSON return messages

This section presents example JSON messages received from each of the four distinct events: create, modify, cancel, void.

### JSON Parameters

The JSON returned to you is structured as follows:

- eventCategory
- eventAction
- context
- eventTimestamp
- silverAgentUser
- bookingRecord

### eventCategory

The classification of the event performed in SilverAgent:

- BOOKING_CANCELLED - An order within a booking was canceled.
- BOOKING_CREATED - A new booking with at least one order was created.
- BOOKING_MODIFIED - A booking or order record was modified.
- BOOKING_VOIDED - An order was voided.

#### eventAction

The granular action performed on a ticket, order, or booking.

The SilverCore component column contains the most common data scenarios in SilverCore when the action is performed. Additional data updates may occur in addition to the items listed.

#### context

For bookings created in SilverAgent, this is the context used to make the booking.

For modified bookings, this is the original context used to create the booking.

The format is:
```json
distributorCode:channelCode:pointOfSaleCode:agencyCode:companyCode
```
**Where** :

- distributorCode:channelCode:pointOfSaleCode are always present.
- agencyCode and companyCode may be available, depending on the partner implementation.

**Example** :
```json
"context": "DEMO:WEB:GB:AG1:COM1",
```

If a context does not include an agencyCode and companyCode, these levels will be omitted.

**Example**:
```json
"context": "DEMO:WEB:GB",
```

If a context includes an agencyCode but no companyCode, the companyCode will be omitted.

**Example**:
```json
"context": "DEMO:WEB:GB:AG1",
```

If a context includes a companyCode but no agencyCode, the agencyCode will be set as empty:

**Example**:
```json
"context": "DEMO:WEB:GB::COM1",
```

#### eventTimestamp

Date/time (millisecond) for the action performed.

**Example**:
```json
"eventTimestamp": "1590391445084",
```
#### silverAgentUser

The user Id of the agent that performed the action.

**Example**:
```json
"silverAgentUser": "David.Jones@silverrailtech.com",
```

#### bookingRecord

A unique Id assigned by SilverCore and used to identify this Booking Record in any subsequent operations (retrieving the booking, updating the booking, adding payments, etc.).

**Example**:
```json
"bookingRecord": { "recordLocator": "B-DEMO-SEY000125"}
```

#### body

The XML response returned by SilverCore following a request containing `returnReservationDetails=true`. The responses are identical in structure to what the Partner receives in direct SilverCore transactions.

In other words, the SilverCore XML response mirrors the same response structure as if the partner had submitted the respective request containing `returnReservationDetails=true`. For example, the [updateBookingRecordResponse](/v1/docs/updatebookingrecordresponse-elements){target="_blank"}, [addPaymentResponse](/v1/docs/addpaymentresponse-elements){target="_blank"}, and the [confirmBookingRecordResponse](/v1/docs/confirmbookingrecordresponse-elements){target="_blank"}.

### BOOKING_CANCELLED

The body of a valid response contains the full XML bookingRecord. We have omitted this to keep these examples concise.

**Example** :
```json
{
  eventCategory : "BOOKING_CANCELLED",
  eventAction: "BOOKING_CANCELLED",
  context: "DEMO:WEB:GB",
  eventTimestamp: 1550593343,
  silverAgentUser: "jane.smith@foo.com",
  bookingRecord: {
    recordLocator: "B-DEMO-LVC0004G",body:"<bookingRecord>"
  }
}
```
### BOOKING_CREATED

**Example** :
```json
{
  eventCategory: "BOOKING_CREATED",
  eventAction: "NEW_BOOKING",
  context: "DEMO:WEB:GB",
  eventTimestamp: 1550593343,
  silverAgentUser: "jane.smith@foo.com",
  bookingRecord: {
    recordLocator: "B-DEMO-LCV00003D"body: "<bookingRecord>"
  }
}
```
### BOOKING_MODIFIED

**Example** :
```json
{
  eventCategory : "BOOKING_MODIFIED",
  eventAction: "TDO_OPTIONS_MODIFIED",
  context: "DEMO:WEB:GB",
  eventTimestamp: 1550593343,
  silverAgentUser: "jane.smith@foo.com",
  bookingRecord: {
    recordLocator: "B-DEMO-LCV00004D",body: "<bookingRecord>"
  }
}
```

### BOOKING_VOIDED

**Example** :
```json
{
  eventCategory : "BOOKING_VOIDED",
  eventAction: "BOOKING_VOIDED",
  context: "DEMO:WEB:GB",
  eventTimestamp: 1550593343,
  silverAgentUser: "jane.smith@foo.com",
  bookingRecord: {
    recordLocator: "B-DEMO-LVC000045",body: "<bookingRecord>"
  }
}
```

## Use cases
### UK Post Ticketed Returns and SNCF RAD
All events take place individually, which could be at different times:
1. COUPONS_RECEIVED.
2. REFUND_AUTHORIZED.
3. REFUND_ISSUED (if the agent selects to "Issue Refund").

### Partial cancellation
Depending on what options are selected, all three events will occur at once and will be sent through booking sync with sequential timestamps:
1. ORDER_CANCELLED.
2. TICKETS CLAIMED (E-Tickets only, if the agent selects to "Send Ticket").
3. REFUND_ISSUED (if the agent selects to "Issue Refund").

### Exchange

Depending on what options are selected, all events take place individually, which could be at different times:
1. ORDER_EXCHANGED.
2. TICKETS_CLAIMED (E-Tickets only, if the agent selects to "Send Ticket").
3. REFUND_ISSUED (if there's a refund to be given and the agent chooses to "Issue Refund").

The exchange action may result in a new payment being applied on the booking to collect the additional funds for a more expensive ticket, which the Partner will need to account for.

Both partial cancel and exchange will result in new orders and new tickets.

The following logic can be used if the Partner is not using SilverAgent to send cancellation emails and new tickets to the customer:

- When a CONFIRMED or TICKETED order is partially canceled or exchanged, the original order status moves to REPLACED (all previously generated tickets are no longer usable), and a new order is created with an initial status of CONFIRMED.  It will be the Partner's responsibility to provide the new ticket details to the customer:

    - If the new order is an E-Ticket order, the initial status will be CONFIRMED. If the agent has selected the option to "Send Ticket", the order status will go from CONFIRMED to TICKETED.  The partner can then perform a [redeliverValueDocumentRequest](/v1/docs/redelivervaluedocumentrequest-elements){target="_blank"} on the new order to generate the E-Tickets. If the agent has not selected to "Send Ticket", the partner can submit a [claimValueDocumentRequest](/v1/docs/claimvaluedocumentrequest-elements){target="_blank"} to perform the ticketing. An "Issue Refund" should also be performed, if applicable.
    - If the new order is a collect at station order, the [retrieveBookingRecordRequest](/v1/docs/retrievebookingrecordrequest-elements){target="_blank"} can be used to obtain the valueDocumentLocator. An "Issue Refund" should also be performed, if applicable.

:::(Info) (SilverAgent has functionality to restrict emails from being sent within the application, so even if you choose to "Send Ticket", if your account group is configured not to send emails, no emails will be sent to the customer. Doing so will still perform a `claimValueDocumentRequest` in the background.)
:::
