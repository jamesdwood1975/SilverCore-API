:::(Error) (**In this article**, as part of our series of How-Tos, we'll look at how to exchange a ticket in SilverCore and cover supported scenarios, the standard flow, and practical examples.)
:::
## Intro
A SilverCore exchange is defined as changing the date, time, fare class, or Legs of a trip, and in some markets, changing the origin and destination. Changing passenger information, such as names or phone numbers, does not constitute an exchange.

For example, if a customer buys a ticket from London to Manchester on the 18th of August at 9:00 AM, but now needs to leave earlier, that customer can exchange the 9:00 AM ticket for an 8:00 AM ticket.

Carriers refer to exchanges in a variety of ways:

- Amend
- Modify
- Change of Journey
- Advance Change of Travel Plan

For some Carriers, exchanges are the preferred method of changing the date, time or Legs of a trip, because the penalty for exchanging a ticket is less than the penalty for canceling and refunding the ticket and then booking a new ticket.

For other Carriers, the passenger is not allowed to cancel and refund, and therefore exchanging is the only method to change the date, time, or Legs of a trip.

### Supported fulfillment types
| Fulfillment type | Before issuance/printing (CONFIRMED order status) | After issuance/printing (TICKETED order status) |
| :-- | :-- | :-- |
| Ticket on Departure (ToD/CTR) | Yes | No.  Tickets must be sent back and exchanged through the offline refund process. |
| Ticket by Mail | Yes | No.  Tickets must be sent back and exchanged through the offline refund process |
| In-Office Printed Ticket | Yes | No.  Tickets must be sent back and exchanged through the offline refund process |
| Print-at-Home | Yes | Yes |
| XVD (Mobile) | Yes | Yes |
| Smartcard | No | No |

### Supported scenarios

SilverCore supports the following exchange scenarios:

- Exchange to a fare that is equal to or more than the cost of the original fare (and the fare being exchanged costs more than the exchange fee).
- (UK Only) Exchange to a fare that costs less than the original fare (with the balance not being refunded and the exchange fee still charged). This scenario makes use of extra fields introduced in the API.
- Exchange of a fare which has an associated optionalPrice travelcard. The optionalPrice travelcard associated with the Advance fare is automatically cancelled as part of the exchange process.

Exchanges are allowed for all or part of the booking according to the following rules:

| Scenario | Rules |
| :-- | :-- |
| One-Way, Single Travel Segment, One Ticketable Fare | The customer can change the travel day and time. |
| Round-Trip, Single Travel Segment each way, One Ticketable Fare each way | The customer can change either the outbound ticket, the return ticket, or both. (Note that changing both will require two separate exchange transactions.) |
| One-Way, Multiple Travel Segments, One Ticketable Fare (a "through fare") | For example, a customer is traveling from Holyhead to Manchester in two Travel Segments, Holyhead to Crewe and Crewe to Manchester. The customer cannot change individual Travel Segments within the one-way ticket but must instead change the entire Leg. |
| Round-Trip, Multiple Travel Segments, One Ticketable Fare each way | The customer can change either the outbound ticket, the return ticket, or both. (Note that changing both will require two separate exchange transactions.) The customer cannot change individual Travel Segments within the outbound or return ticket but must change the entire Leg. |
| One-Way, Multiple Travel Segments, Multiple Ticketable Fares | If a Travel Segment has its own ticket, it is possible to change that individual Travel Segment (it is not possible to change a Travel Segment if the fare covers multiple Travel Segments). |
| Round-Trip, Multiple Travel Segments, One Ticketable Fare per Travel Segment | Each Travel Segment has its own ticket, so it is possible to change individual Travel Segments. |
| Round-Trip with single Ticketable Fare | Because one ticket has both the outbound and return, the customer must exchange the entire ticket, not individual Legs within the ticket. |
| Multi-Leg (each Leg has its own Ticketable Fare) | Each travel Leg has its own Ticketable Fare, so it is possible to change individual Legs. |

## SilverCore exchange messages

SilverCore has three messages dedicated to the exchange process:

- `exchangeSearchRequest/Response`
- `retrieveExchangeSummaryRequest/Response`
- `processExchangeRequest/Response`

Common to all three messages is the `<exchangeSet>` element, which has a different structure in each message. The easiest way to think about the structure is that the exchange sets that are part of exchangeSearch are different from those that are part of `retrieveExchangeSummary` and `processExchange`.

In the `exchangeSearchRequest` message, each exchange set includes the Ids for the Ticketable Fares you want to exchange and a shopping query (the travel point pairs in the shopping query specify the search parameters for the replacement journey). The elements are as follows:

- `<ticketableFareLocators>`
- `<exchangeShoppingQuery>`

The `exchangeSearchResponse` message doesn't have a shopping query but it does have the Ids of the Ticketable Fares and the Travel Segments that will be exchanged as well as the Leg Solutions and prices that satisfy the request's shopping query. The elements are as follows:

- `<exchangedTicketableFares>`
- `<exchangedTravelSegments>`
- `<legSolutions>`
- `<prices>`

In the `retrieveExchangeSummaryRequest` and `processExchangeRequest` messages, the exchange sets are identical. They include the Ticketable Fares to be exchanged and the Leg Solutions and prices that the client intends to use for the exchange. The elements are as follows:

- `<ticketableFareLocators>`
- `<legSolutions>`
- `<prices>`

## Standard SilverCore exchange message flow

1. Verify if the ticket is eligible for exchange
2. Search for new tickets
3. Estimate cost and penalty
4. Process the Exchange

### Verify if the ticket is eligible for exchange

#### Standard approach

To find out if a confirmed or ticketed order is eligible for exchange, submit a `retrieveBookingRecordRequest` message specifying the `<recordLocator>` of the booking and with the `<includeOrderDetails>` element set to true.

For example, if the ticket is exchangeable, the `<exchangeableTicketableFareLocators>` array in the response contains the Id of the ticket.

**Example**:
```xml
<orders>
 <order orderID="O-DEMO-QWW00009V-FTI000001" status="BOOKED">
  ....
  <exchangeableTicketableFareLocators>
   <exchangeableTicketableFareLocator>TF-0</exchangeableTicketableFareLocator>
   <exchangeableTicketableFareLocator>TF-1</exchangeableTicketableFareLocator>
   <exchangeableTicketableFareLocator>TF-2</exchangeableTicketableFareLocator>
  </exchangeableTicketableFareLocators>
  ....
 </order>
 </orders>
 ```

#### <a id="sncf"></a>SNCF

SNCF supports the ability to exchange multiple ticketableFares at the same time. SilverCore will indicate which ticketableFare Ids can be exchanged together with a distinct `exchangeSet` per allowed combination.

Using this information, Partners can present the right selections to the user.

**Example**:
```XML
<exchangeSets>
 <exchangeSet exchangeSetID="EXCH-SET-1">
  <exchangeableTicketableFares>
   <exchangeableTicketableFare>TF-XHL000004-CHR000001-4</exchangeableTicketableFare>
   <exchangeableTicketableFare>TF-XHL000004-CHR000001-5</exchangeableTicketableFare>
  </exchangeableTicketableFares>
 </exchangeSet>
 <exchangeSet exchangeSetID="EXCH-SET-2">
  <exchangeableTicketableFares>
   <exchangeableTicketableFare>TF-XHL000004-CHR000001-2</exchangeableTicketableFare>
   <exchangeableTicketableFare>TF-XHL000004-CHR000001-3</exchangeableTicketableFare>
   <exchangeableTicketableFare>TF-XHL000004-CHR000001-4</exchangeableTicketableFare>
   <exchangeableTicketableFare>TF-XHL000004-CHR000001-5</exchangeableTicketableFare>
  </exchangeableTicketableFares>
 </exchangeSet>
 <exchangeSet exchangeSetID="EXCH-SET-3">
  <exchangeableTicketableFares>
   <exchangeableTicketableFare>TF-XHL000004-CHR000001-0</exchangeableTicketableFare>
   <exchangeableTicketableFare>TF-XHL000004-CHR000001-1</exchangeableTicketableFare>
   <exchangeableTicketableFare>TF-XHL000004-CHR000001-2</exchangeableTicketableFare>
   <exchangeableTicketableFare>TF-XHL000004-CHR000001-3</exchangeableTicketableFare>
   <exchangeableTicketableFare>TF-XHL000004-CHR000001-4</exchangeableTicketableFare>
   <exchangeableTicketableFare>TF-XHL000004-CHR000001-5</exchangeableTicketableFare>
  </exchangeableTicketableFares>
 </exchangeSet>
</exchangeSets>
```

### Search for new tickets

To find the available replacement Legs, submit an `exchangeSearchRequest` message, passing the following data:

| Element | Description |
| :-- | :-- |
| `<recordLocator>` | The SilverCore record locator Id assigned to the booking, as returned in the `createBookingRecordResponse` message. |
| `<orderLocator>` | The Id for an individual order within the booking. |
| `<exchangeSet>` | You can only exchange one Leg grouping at a time, so there is one exchange set. Each exchange set contains:<br>- One or more `<ticketableFareLocator>` elements, which are the Ids for the Ticketable Fares you want to exchange (these are a return value in the `<exchangeableTicketableFareLocator>` element when you verify if a ticket is eligible for exchange). Note that for one-way trips, all Ticketable Fares in an exchange set are for the same Leg.<br>- An `<exchangeShoppingQuery>` element, which contains one or more `<travelPointPair>` elements. Each `<travelPointPair>` specifies the search parameters for the replacement journey: the origin, the destination, and either the departure date/time or the arrival/date time. The `<travelPointPair>` element has the same structure as in the `pointToPointShoppingRequest` message.

**Example**:
```xml
<exchangeSet exchangeSetID="EXCH-1">
 <ticketableFareLocators>
  <ticketableFareLocator>TF-SSK00000F-ZZU000001-0</ticketableFareLocator>
 </ticketableFareLocators>
 <exchangeShoppingQuery>
  <travelPointPairs>
   <travelPointPair>
    <originTravelPoint type="STATION">GBNRW</originTravelPoint>
    <destinationTravelPoin type="STATION">GBSKI</destinationTravelPoint>
    <departureDateTimeWindow>
     <date>2018-05-31</date>
    </departureDateTimeWindow>
   </travelPointPair>
  </travelPointPairs>
 </exchangeShoppingQuery>
</exchangeSet>
 ```

The response contains one or more <exchangeSet> elements, each of which contains the following data:

| Element | Description |
| :-- | :-- |
| `<exchangedTicketableFares>` | The Id of each Ticketable Fare being exchanged. |
| `<exchangedTravelSegments>` | The Id of each Travel Segment associated with the fare being exchanged. |
| `<legSolutions>` | The list of possible replacement Leg Solutions. The structure of the element is the same as the `<legSolutions>` element returned in the `pointToPointShoppingResponse message`. |
|` <prices>` | The list of matching fares for each possible replacement Leg (`<pointToPointPrice>` elements). The structure of the `<prices>` element is the same as in the `pointToPointShoppingResponse` message. |

**Example**:
```xml
<exchangeSet exchangeSetID="EXCH-1">
 <exchangedTicketableFares>
  <exchangedTicketableFareLocator>TF-SSK00000F-ZZU000001-0</exchangedTicketableFareLocator>
 </exchangedTicketableFares>
 <exchangedTravelSegments>
  <exchangedTravelSegmentLocator>TS-SSK00000F-ZZU000001-2</exchangedTravelSegmentLocator>
 </exchangedTravelSegments>
 <legSolutions>
  <legSolution legSolutionID="LS_1_0">
   ....
  </legSolution>
 </legSolutions>
 <prices>
  <pointToPointPrice priceID="PRICE_P_1_0">
   ....
  </pointToPointPrice>
 </prices>
</exchangeSet>
```

### Estimate cost and penalty

To estimate the additional cost and penalty associated with the exchange, submit a retrieveExchangeSummaryRequest message, passing the tickets you want to exchange, the new Leg Solutions, and the new fares. This is an informational query only and does not place any tickets on hold or affect supplier inventory.

| Element | Description |
| :-- | :-- |
| `<recordLocator>` | Identifies the booking. |
| `<orderLocator>` | Identifies the order within the booking. |
| `<exchangeSets>` | One or more of the `<exchangeSet>` elements. Each `<exchangeSet>` contains the Ticketable Fare(s) being exchanged, the new Leg Solutions, and the new prices. |
| `<passengers>` | The passenger Ids defined on the booking is required in order to retrieve the exchange summary. |
| `<fulfillmentInformation>` | Required in order to retrieve the exchange summary. |

**Example**:

```xml
<retrieveExchangeSummaryRequest>
 <exchangeSets>
  <exchangeSet exchangeSetID="EXCH-1">
   <ticketableFareLocators>
    <ticketableFareLocator>TF-SSK00000F-ZZU000001-0</ticketableFareLocator>
   </ticketableFareLocators>
   <legSolutions>
    ....
   </legSolutions>
   <prices>
    ....
   </prices>
  </exchangeSet>
 </exchangeSets>
 <passengers>
  ....
 </passengers>
 <fulfillmentInformation>
  ....
 </fulfillmentInformation>
</retrieveExchangeSummaryRequest>
```

The `retrieveExchangeSummaryResponse` message returns a single `<exchangeOption>` element, which contains the data shown in the following table. The `<exchangeOption>` element has a single attribute, `exchangeOptionType`, which indicates whether the exchange will result in money due (ADD\_COLLECT), money refunded (REFUND), or no money owed (EVEN\_EXCHANGE). Currently within the UK, there is always a penalty of £10 and so the exchange is always of type ADD\_COLLECT.

| Element | Description |
| :-- | :-- |
| `<penalty>` | The amount of the penalty owed. |
| `<exchangeBreakdown>` | The financial details of the exchange: the current product total, the current fees total, the amount of charge reversed, the current revenue total, and the current receipt total. There is also an optional `exchangedLossTotal` when exchanging to a cheaper fare. |

**Example**:
```xml
<retrieveExchangeSummaryResponse>
 <exchangeSummary>
  <exchangeOptions>
   <exchangeOption exchangeOptionType="ADD_COLLECT">
    <total currency="GBP">18.50</total>
    <penalty>
     <total currency="GBP">10.00</total>
     <breakdown>
      <component type="TICKET" currency="GBP">10.00</component>
     </breakdown>
    </penalty>
    <exchangeBreakdown>
     <newProductsTotal currency="GBP">109.50</newProductsTotal>
     <newFeesTotal currency="GBP">6.00</newFeesTotal>
     <reversedRevenueTotal currency="GBP">-107.00</reversedRevenueTotal>
     <revenueTotal currency="GBP">119.70</revenueTotal>
     <receiptTotal currency="GBP">119.70</receiptTotal>
    </exchangeBreakdown>
   </exchangeOption>
  </exchangeOptions>
 </exchangeSummary>
</retrieveExchangeSummaryResponse>
```

### Process the exchange

To perform a ticket exchange, submit a `processExchangeRequest` message, passing the following data:

| Element | Description |
| :-- | :-- |
| `<recordLocator>` | Identifies the booking. |
| `<orderLocator>` | Identifies the order within the booking. |
| `<exchangeSet>` | The Ticketable Fares being exchanged, and the new Leg Solution and pricing information (the same as in the `retrieveExchangeSummary` request). |
| `<passenger>` | The Ids of all passengers on the Leg (already defined on the booking) is required in order to process the exchange summary. |
| `<selectedExchangeOption>` | The data you pass should be copied from the chosen exchangeOption in the `retrieveExchangeSummaryResponse`, indicating the type of exchange: `ADD_COLLECT`, `REFUND`, or `EVEN_EXCHANGE`. |
| `<fulfillmentInformation>` | Complete fulfillment information is required in order to process the exchange summary. |
| `<payment>` | The credit card to use to for the required payment. |

**Example**:
```xml
<processExchangeRequest>
 <exchangeSets>
  <exchangeSet exchangeSetID="EXCH-1">
   <ticketableFareLocators>
    <ticketableFareLocator>TF-SSK00000F-ZZU000001-0</ticketableFareLocator>
   </ticketableFareLocators>
   <legSolutions>
    ....
   </legSolutions>
   <prices>
    ....
   </prices>
  </exchangeSet>
 </exchangeSets>
 <passengers>
  ....
 </passengers>
 <selectedExchangeOption exchangeOptionType="ADD_COLLECT">
  <total currency="GBP">18.50</total>
 </selectedExchangeOption>
 <fulfillmentInformation>
  ....
 </fulfillmentInformation>
 <payment>
  ....
 </payment>
</processExchangeRequest>
```

If the exchange is successful, SilverCore:

- Changes the old order status to `REPLACED` and the `<cancellationReason>` to `EXCHANGED`.
- Creates a new order in the Booking Record with the new Travel Segments and Ticketable Fares and sets its status to `CONFIRMED`.
- Moves unchanged Travel Segments and Ticketable Fares on the original order to the new order.
- If `ADD_COLLECT`, processes the payment and adds new receipt information to booking.
- If Refund, processes refund to original form of payment and adds new receipt to booking (not applicable for UK).
- Creates new ticket numbers and new coupons (UK only).

## Exchange with optionalPrice Travelcard

:::(Warning) (This type of exchange is currently available only in the UK market.)
:::

SilverCore only allows exchange of Advance fares and only allows exchange of multiple fares if they cover the same Travel Segments (multiple passengers travelling together). In order to prevent these two rules from disallowing the exchange of an order that consists of one or more Advance fares that were purchased with optionalPrice travelcards, SilverCore links the optionalPrice travelcard with the Advance fare. Therefore, when an exchange is requested on the Advance fare:

- The optionalPrice is included in the process.
- The travelcard is cancelled as part of the exchange.
- A replacement optionalPrice travelcard can be purchased with the replacement Advance fare.

The following sections outline the differences in the data submitted and received in the three-step exchange message flow.

### Search for New Tickets

In the `exchangeSearchRequest`, the inputs are the same as in a standard exchange, except you enter the Id for just the Advance fare Ticketable Fare.

The `exchangeSearchResponse` additionally includes the Ids of the linked optionalPrices.

**Example**:
```xml
<exchangeSet exchangeSetID="EXCH-1">
 <exchangedTicketableFares>
  <exchangedTicketableFareLocator>TF-DSW04C3-PV01-0</exchangedTicketableFareLocator>
 </exchangedTicketableFares>
 <cancelledTicketableFares>
  <cancelledTicketableFareLocator>TF-DSW04C3-PVY01-1</cancelledTicketableFareLocator>
 </cancelledTicketableFares>
 <exchangedTravelSegments>
  <exchangedTravelSegmentLocator>TS-DSW04C3-PVY01-1</exchangedTravelSegmentLocator>
 </exchangedTravelSegments>
</exchangeSet>
```

### Estimate cost and penalty

In the `retrieveExchangeSummaryRequest`, you enter the Id for just the Advance fare Ticketable Fare.

The `retrieveExchangeSummaryResponse` additionally includes the penalty breakdown, the add collect or refund amount, and the refund and penalty amounts for the optionalPrice travelcard.

**Example**:
```xml
<exchangeSummary>
 <exchangeOptions>
  <exchangeOption exchangeOptionType="ADD_COLLECT">
   <total currency="GBP">108.90</total>
   <penalty>
    <total currency="GBP">20.00</total>
    <breakdown>
     <component type="TICKET" currency="GBP">20.00</component>
    </breakdown>
   </penalty>
   <exchangeBreakdown>
    <newProductsTotal currency="GBP">136.50</newProductsTotal>
    <newFeesTotal currency="GBP">0.00</newFeesTotal>
    <reversedRevenueTotal currency="GBP">-47.60</reversedRevenueTotal>
    <revenueTotal currency="GBP">47.60</revenueTotal>
    <receiptTotal currency="GBP">47.60</receiptTotal>
   </exchangeBreakdown>
  </exchangeOption>
 </exchangeOptions>
</exchangeSummary>
```

### Process the exchange

In the `processExchangeRequest`, you enter the Id for just the Advance fare Ticketable Fare.

The `processExchangeResponse` additionally includes the refund and penalty amounts for the optionalPrice.

**Example**:
```xml
<prices>
 <price priceID="FIN_PRICE_20938" type="Product_Sale" priceDescription="Product
  Sale:O-DEMO-DSW0004C3-PVY000001">
  <orderIDRef>O-DEMO-DSW0004C3-PVY000001</orderIDRef>
  <amount currency="GBP">47.60</amount>
  <postingDate>2019-09-16Z</postingDate>
  <postingTime>15:52:39Z</postingTime>
 </price>
 <price priceID="FIN_PRICE_20939" type="Ticket_Delivery_Fee_Assessment"
  priceDescription="Ticket Delivery Fee:O-DEMO-DSW0004C3-PVY000001">
  <orderIDRef>O-DEMO-DSW0004C3-PVY000001</orderIDRef>
  <amount currency="GBP">0.00</amount>
  <postingDate>2019-09-16Z</postingDate>
  <postingTime>15:52:41Z</postingTime>
 </price>
 <price priceID="FIN_PRICE_20942" type="Product_Refund"
  priceDescription="Product Sale:O-DEMO-DSW0004C3-PVY000001">
  <orderIDRef>O-DEMO-DSW0004C3-PVY000001</orderIDRef>
  <amount currency="GBP">-47.60</amount>
  <postingDate>2019-09-16Z</postingDate>
  <postingTime>15:52:48Z</postingTime>
 </price>
 <price priceID="FIN_PRICE_20941" type="Ticket_Delivery_Fee_Assessment"
  priceDescription="Ticket Delivery Fee:O-DEMO-DSW0004C3-JDU000001">
  <orderIDRef>O-DEMO-DSW0004C3-JDU000001</orderIDRef>
  <amount currency="GBP">0.00</amount>
  <postingDate>2019-09-16Z</postingDate>
  <postingTime>15:52:48Z</postingTime>
 </price>
 <price priceID="FIN_PRICE_20943" type="Cancelation_Penalty_Assessment"
  priceDescription="Cancellation Penalty:O-DEMO-DSW0004C3-PVY000001">
  <orderIDRef>O-DEMO-DSW0004C3-PVY000001</orderIDRef>
  <amount currency="GBP">20.00</amount>
  <postingDate>2019-09-16Z</postingDate>
  <postingTime>15:52:48Z</postingTime>
 </price>
 <price priceID="FIN_PRICE_20940" type="Product_Sale" priceDescription="Product
  Sale:O-DEMO-DSW0004C3-JDU000001">
  <orderIDRef>O-DEMO-DSW0004C3-JDU000001</orderIDRef>
  <amount currency="GBP">136.50</amount>
  <postingDate>2019-09-16Z</postingDate>
  <postingTime>15:52:48Z</postingTime>
 </price>
</prices>
```
## Exchange to a less expensive fare

:::(Warning) (This type of exchange is currently available only in the UK market.)
:::
In an exchange for a ticket of equal or greater value, the difference between the price paid and cost of the next suitable fare for your journey is payable, plus an administration fee of up to £10 per person, per single ticket for each change to a journey.

If the new ticket is less expensive, the difference is not refunded to the customer or deducted from the administration fee.

### Search for new tickets

For the `exchangeSearchRequest`, the inputs are the same as in a standard exchange.

In the `exchangeSearchResponse`, the `<exchangeSet>` is the same as a standard exchange.

### Estimate Cost and Penalty

In the `retrieveExchangeSummaryRequest`, the inputs are the same as in a standard exchange.

The `retrieveExchangeSummaryResponse` additionally includes an exchange loss amount if exchanging to a cheaper fare.

**Example**:
```xml
<retrieveExchangeSummaryResponse
 conversationToken="AdvanceSingle-FullExchToWalkOn"
 xmlns:ns2="http://railgds.net/ws/booking"
 xmlns="http://railgds.net/ws/commontypes">
 <requestStatus systemId="lU6hKC">
  <success>true</success>
 </requestStatus>
 <ns2:exchangeSummary>
  <exchangeOptions>
   <exchangeOption exchangeOptionType="ADD_COLLECT">
    <total currency="GBP">10.00</total>
    <penalty>
     <total currency="GBP">10.00</total>
     <breakdown>
      <component type="TICKET" currency="GBP">10.00</component>
     </breakdown>
    </penalty>
    <exchangeBreakdown>
     <newProductsTotal currency="GBP">108.80</newProductsTotal>
     <newFeesTotal currency="GBP">0.00</newFeesTotal>
     <reversedRevenueTotal currency="GBP">-217.35</reversedRevenueTotal>
     <revenueTotal currency="GBP">217.35</revenueTotal>
     <receiptTotal currency="GBP">217.35</receiptTotal>
     <exchangedLossTotal currency="GBP">108.55</exchangedLossTotal>
    </exchangeBreakdown>
   </exchangeOption>
  </exchangeOptions>
 </ns2:exchangeSummary>
</ns2:retrieveExchangeSummaryResponse>
```

### Process the exchange

In the `processExchangeRequest`, the inputs are the same as in a standard exchange.

The `processExchangeResponse` additionally includes a miscellaneous penalty in the financials which indicates the exchanged loss amount.

**Example**:
```xml
<prices>
 <price priceID="FIN_PRICE_20164" type="Product_Sale" priceDescription="Product
  Sale:O-DEMO-XKR00045S-GNL000001">
  <orderIDRef>O-DEMO-XKR00045S-GNL000001</orderIDRef>
  <amount currency="GBP">217.35</amount>
  <postingDate>2019-09-16Z</postingDate>
  <postingTime>11:43:32Z</postingTime>
 </price>
 <price priceID="FIN_PRICE_20165" type="Ticket_Delivery_Fee_Assessment"
  priceDescription="Ticket Delivery Fee:O-DEMO-XKR00045S-GNL000001">
  <orderIDRef>O-DEMO-XKR00045S-GNL000001</orderIDRef>
  <amount currency="GBP">0.00</amount>
  <postingDate>2019-09-16Z</postingDate>
  <postingTime>11:43:35Z</postingTime>
 </price>
 <price priceID="FIN_PRICE_20169" type="Product_Refund"
  priceDescription="Product Sale:O-DEMO-XKR00045S-GNL000001">
  <orderIDRef>O-DEMO-XKR00045S-GNL000001</orderIDRef>
  <amount currency="GBP">-217.35</amount>
  <postingDate>2019-09-16Z</postingDate>
  <postingTime>11:43:43Z</postingTime>
 </price>
 <price priceID="FIN_PRICE_20168" type="Ticket_Delivery_Fee_Assessment"
  priceDescription="Ticket Delivery Fee:O-DEMO-XKR00045S-WLN000001">
  <orderIDRef>O-DEMO-XKR00045S-WLN000001</orderIDRef>
  <amount currency="GBP">0.00</amount>
  <postingDate>2019-09-16Z</postingDate>
  <postingTime>11:43:43Z</postingTime>
 </price>
 <price priceID="FIN_PRICE_20171" type="Cancelation_Penalty_Assessment"
  priceDescription="Cancellation Penalty:O-DEMO-XKR00045S-GNL000001">
  <orderIDRef>O-DEMO-XKR00045S-GNL000001</orderIDRef>
  <amount currency="GBP">10.00</amount>
  <postingDate>2019-09-16Z</postingDate>
  <postingTime>11:43:43Z</postingTime>
 </price>
 <price priceID="FIN_PRICE_20170" type="Misc_Penalty_Assessment"
  priceDescription="Penalty:O-DEMO-XKR00045S-GNL000001">
  <orderIDRef>O-DEMO-XKR00045S-GNL000001</orderIDRef>
  <amount currency="GBP">108.55</amount>
  <postingDate>2019-09-16Z</postingDate>
  <postingTime>11:43:43Z</postingTime>
 </price>
 <price priceID="FIN_PRICE_20167" type="Product_Sale" priceDescription="Product
  Sale:O-DEMO-XKR00045S-WLN000001">
  <orderIDRef>O-DEMO-XKR00045S-WLN000001</orderIDRef>
  <amount currency="GBP">108.80</amount>
  <postingDate>2019-09-16Z</postingDate>
  <postingTime>11:43:43Z</postingTime>
 </price>
</prices>
```
## Exchange when some fares are below penalty limit

SilverCore disallows exchange when the ticket being exchanged costs less than the penalty fee. In this circumstance there is no advantage to exchanging, as the customer can just abandon their existing fare and purchase the new one and pay the same amount.

There is one exception to this rule: when there are multiple passengers on the order being exchanged and the fares for some passengers cost more than the penalty fee and the fares for the rest cost less than the penalty fee. This typically can occur when some passengers are children or some passengers use a railcard to obtain discounted fares. For example, the following multi-passenger order is comprised of fares which are both above and below the £10 penalty fee:

- 1 adult fares of £14.10: more than the penalty fee
- 2 child fares of £7.05: less than the penalty fee
- 1 discounted adult fare of £9.40: less than the penalty fee

When some fares cost more than the penalty fee and some cost less, for the fares that cost less than the penalty fee, SilverCore makes their penalty fee equal to the fare price. For example:

|  | Original Fare |Replacement  Fare | Cost if Exchange Prohibited | Cost if Exchange Allowed |
| :-- | :-- | :-- | :-- | :-- |
| Adult1 | £14.10 | £20.00 | £20.00 | 20.00 - 14.10 + 10.00 = £15.90 |
| Adult2 | £14.10 | £20.00 | £20.00 | 20.00 - 14.10 + 10.00 = £15.90 |
| Adult3 | £9.40 | £13.65 | £13.65 | 13.65 - 9.40 + 9.40 = £13.65 |
| Child | £7.05 | £10.00 | £10.00 | 10.00 - 7.05 + 7.05 = £10.00 |
| Total |  | | £63.65 | £55.45 (saving £8.20) |

:::(Info) (There is one exception.  If the total amount to pay is more than just purchasing the new tickets, the exchange is disallowed. This could happen if the new fares are less than the penalty fee.)
:::

**Example**:

| | Original Fare |Replacement  Fare | Cost if Exchange Prohibited|Cost if Exchange Allowed |
| :-- | :-- | :-- | :-- | :-- |
| Adult1 | £15.00 | £9.95 | £9.95 | 9.95 - (15.00-5.05) + 10.00 = £10.00 |
| Adult2 | £15.00 | £9.95 | £9.95 | 9.95 - (15.00-5.05) + 10.00 = £10.00 |
| Adult3 | £10.00 | £6.65 | £6.65 | £6.65 |
| Child | £7.50 | £5.00 | £5.00 | £5.00 |
| Total | | | £31.55 | £31.65 (loss of £0.10) |

### Search for new tickets

The `exchangeSearchRequest` and `exchangeSearchResponse` are the same as in a standard exchange.

### Estimate cost and penalty

In the `retrieveExchangeSummaryRequest`, the inputs are the same as in a standard exchange.

In the `retrieveExchangeSummaryResponse`, the penalty fee for a fare cheaper than the standard penalty fee is the price of the original fare.

For example, the penalty in the following response is based on original fares of £10.30 and £5.15 (plus £1.00 TDO fee) and replacement fares of £21.60 and £10.80.

**Example**:
```xml
<exchangeSummary>
 <exchangeOptions>
  <exchangeOption exchangeOptionType="ADD_COLLECT">
   <total currency="GBP">33.10</total>
   <penalty>
    <total currency="GBP">15.15</total>
    <breakdown>
     <component type="TICKET" currency="GBP">15.15</component>
    </breakdown>
   </penalty>
   <exchangeBreakdown>
    <newProductsTotal currency="GBP">32.40</newProductsTotal>
    <newFeesTotal currency="GBP">1.00</newFeesTotal>
    <reversedRevenueTotal currency="GBP">-15.45</reversedRevenueTotal>
    <revenueTotal currency="GBP">16.45</revenueTotal>
    <receiptTotal currency="GBP">16.45</receiptTotal>
   </exchangeBreakdown>
  </exchangeOption>
 </exchangeOptions>
</exchangeSummary>
```

### Process the exchange

In the `processExchangeRequest`, the inputs are the same as in a standard exchange.

In the `processExchangeResponse`, the penalty fee in the financials reflects the total of the adjusted amounts.

**Example**:

```xml
<prices>
 <price priceID="FIN_PRICE_1621" type="Product_Sale" priceDescription="Product
  Sale:O-QAMC-DKW0000EJ-CYA000001">
  <orderIDRef>O-QAMC-DKW0000EJ-CYA000001</orderIDRef>
  <amount currency="GBP">15.45</amount>
  <postingDate>2019-09-17Z</postingDate>
  <postingTime>14:46:08Z</postingTime>
 </price>
 <price priceID="FIN_PRICE_1622" type="Ticket_Delivery_Fee_Assessment"
  priceDescription="Ticket Delivery Fee:O-QAMC-DKW0000EJ-CYA000001">
  <orderIDRef>O-QAMC-DKW0000EJ-CYA000001</orderIDRef>
  <amount currency="GBP">1.00</amount>
  <postingDate>2019-09-17Z</postingDate>
  <postingTime>14:46:25Z</postingTime>
 </price>
 <price priceID="FIN_PRICE_1625" type="Product_Refund"
  priceDescription="Product Sale:O-QAMC-DKW0000EJ-CYA000001">
  <orderIDRef>O-QAMC-DKW0000EJ-CYA000001</orderIDRef>
  <amount currency="GBP">-15.45</amount>
  <postingDate>2019-09-17Z</postingDate>
  <postingTime>14:46:47Z</postingTime>
 </price>
 <price priceID="FIN_PRICE_1624" type="Ticket_Delivery_Fee_Assessment"
  priceDescription="Ticket Delivery Fee:O-QAMC-DKW0000EJ-IOZ000001">
  <orderIDRef>O-QAMC-DKW0000EJ-IOZ000001</orderIDRef>
  <amount currency="GBP">1.00</amount>
  <postingDate>2019-09-17Z</postingDate>
  <postingTime>14:46:47Z</postingTime>
 </price>
 <price priceID="FIN_PRICE_1626" type="Cancelation_Penalty_Assessment"
  priceDescription="Cancellation Penalty:O-QAMC-DKW0000EJ-CYA000001">
  <orderIDRef>O-QAMC-DKW0000EJ-CYA000001</orderIDRef>
  <amount currency="GBP">15.15</amount>
  <postingDate>2019-09-17Z</postingDate>
  <postingTime>14:46:47Z</postingTime>
 </price>
 <price priceID="FIN_PRICE_1623" type="Product_Sale" priceDescription="Product
  Sale:O-QAMC-DKW0000EJ-IOZ000001">
  <orderIDRef>O-QAMC-DKW0000EJ-IOZ000001</orderIdRef>
  <amount currency="GBP">32.40</amount>
  <postingDate>2019-09-17Z</postingDate>
  <postingTime>14:46:47Z</postingTime>
 </price>
</prices>

```

## SilverCore and Supplier exchange rules

### RDG

The following rules apply and are consistent across UK vendors, except where indicated otherwise:

| Rule | Explanation |
|:--|:--|
| Fares | - Only Advance Purchase (AP) fares can be exchanged.  All other fare types must be canceled and rebooked<br>- Not possible to exchange a group fare as the exchange and refund rules vary between types of group fare.<br>- Not possible to exchange a single fare for a return fare, or two single fares for a return fare.|
| Tickets | - No limitation on the number of times a ticket can be exchanged<br>- Not possible to exchange a ticket for an individual passenger on a multi-passenger order.<br>- Not possible to change the origin or destination as part of an exchange.<br>- Tickets can only be exchanged before the departure of the first reserved train.<br>- Tickets that cost less than £10 cannot be exchanged; a new ticket must be purchased.*¹ |
| Penalities and refunds| - £10 penalty is charged for each exchanged ticket. This is a mandatory penalty and submitted to RDG in the settlement file (SDCI+). The penalty cannot be changed by applying a penalty modifier.<br>- If a customer exchanges an advance ticket for any type of flexible ticket, they cannot receive an automated refund for the flexible ticket through SilverCore.  Instead, it must be processed through the offline refund process.<br>- If the replacement ticket is cheaper than the original, the difference is NOT refunded.  The value is returned in the financials summary in the booking record, available in the [retrieveExchangeSummaryResponse elements](/v1/docs/retrieveexchangesummaryresponse-elements){target="_blank"} and further, for example, in the [financials](/v1/docs/retrievebookingrecordresponse-elements#financials){target="_blank"} element in the booking record. |
| TDO | Not possible to change the Ticket Delivery Option (TDO) as part of exchange. |

*¹ There is one exception to this rule.  When exchanging a group of        passengers/fares, it is possible that some of those fares cost less than the     exchange fee, due to children traveling or the use of railcards.


 ### SNCF

| Rule | Explanation |
|:--|:--|
| Penalities and refunds| If the replacement ticket is cheaper than the original, the difference is refunded.  The difference is returned in the financials summary in the booking record, available in the [retrieveExchangeSummaryResponse elements](/v1/docs/retrieveexchangesummaryresponse-elements){target="_blank"} and further, for example, in the [financials](/v1/docs/retrievebookingrecordresponse-elements#financials){target="_blank"} element in the booking record.|


 <!---
- There is no limit on the number of times a customer can exchange their ticket.
- It is not possible to exchange a ticket for an individual passenger on a multi-passenger order. For example, if there are four passengers, all four tickets need to be exchanged.
- It is not possible to change the Ticket Delivery Option (TDO) as part of exchange. For example, if the TDO of the original ticket is E-Ticket, the new ticket must also be E-Ticket.
- It is not possible to exchange a group fare as the exchange and refund rules vary between types of group fare, and the rules are not represented in the industry data.
- It is not possible to exchange a single fare for a return fare, or two single fares for a return fare.
- It is not possible to change the origin or destination as part of an exchange.
- A £10 penalty is charged for each exchanged ticket. This is a mandatory penalty and submitted to RDG in the settlement file (SDCI+). The penalty cannot be changed by applying a penalty modifier.
- A ticket can only be exchanged for another ticket with the same Train Operating Company (TOC) and on the same route.
- A ticket can only be exchanged before the departure of the first reserved train.
- Tickets that cost less than £10 cannot be exchanged; a new ticket must be purchased. (There is a £10 penalty for exchange, so there is no point in exchanging a ticket that costs less than £10).
    :::(Info) (There is one exception to this rule.  When exchanging a group of        passengers/fares, it is possible that some of those fares cost less than the     exchange fee, due to children traveling or the use of railcards.)
    :::
- If a customer exchanges an advance ticket for any type of flexible ticket, they cannot receive an automated refund for the flexible ticket through SilverCore.  Instead, it must be processed through the offline refund process.
--->
