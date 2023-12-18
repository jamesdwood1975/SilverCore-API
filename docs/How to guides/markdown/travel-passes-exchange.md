## Intro

In the UK market, it is possible to change a Season ticket's origin station and destination station by performing an exchange. When performing the exchange, it is also possible to change the Ticket Delivery Option.

## Supported carriers

Exchange is supported by all RDG carriers in the UK market. A typical use case is when a customer wishes to change the origin or destination station for a monthly or yearly Season ticket.

Partners implementing Season ticket exchange should take note of the following points:

- It is not possible to change the duration of a Season ticket when performing an exchange. For example, it is not possible to exchange a monthly Season ticket for a yearly Season ticket.
- The duration of the original Season ticket must be between one and twelve months; it is not possible to exchange weekly Season tickets.
- To perform an exchange, there must be seven days or more of outstanding ticket validity.
- Only Season tickets that have started can be exchanged. For Season tickets that have not started, customers must cancel and rebook.
- The start date for the replacement Season ticket can be no more than the current date + 1.
- Season tickets booked with a Fare Qualifier cannot be exchanged, and it is not possible to add a Fare Qualifier to the replacement ticket.
- It is not possible to change passengers, add passengers, or remove passengers when exchanging.

SilverCore's logic ensures that the industry-based rules for Season ticket exchange are adhered to. In "Error codes and messages", Partners can find details of the errors returned by SilverCore when those rules are violated.

## SilverCore exchange flow for Season tickets

Refer to the following diagram:

![](RackMultipart20231123-1-7ezrbr_html_551500bf5dd28316.png)

## Step 1 – Verify if the Season ticket can be exchanged

### retrieveBookingRecordRequest

To find out if a `CONFIRMED` or `TICKETED` order is eligible for exchange, submit a `retrieveBookingRecordRequest` and pass in the following data:

1. `recordLocator` for the booking.
2. `includeOrderDetails="true"`.

**Example retrieveBookingRecordRequest**:
```xml
<book:retrieveBookingRecordRequest version="2.0">
  <com:context>
    <com:distributorCode>DEMO</com:distributorCode>
    <com:pointOfSaleCode>GB</com:pointOfSaleCode>
    <com:channelCode>WEB</com:channelCode>
  </com:context>
❶ <book:recordLocator>B-DEMO-QOG0001K0</book:recordLocator>
  <book:responseSpec>
❷  <book:includeOrderCosts>true</book:includeOrderCosts>
    <book:includePassengerDetails>true</book:includePassengerDetails>
    <book:includeFinancials>true</book:includeFinancials>
    <book:includePaymentRequirements>true</book:includePaymentRequirements>
    <book:includeTicketingOptions>true</book:includeTicketingOptions>
    <book:includeNotes>true</book:includeNotes>
    <book:includeFulfillmentInformation>true</book:includeFulfillmentInformation>
  </book:responseSpec>
</book:retrieveBookingRecordRequest>
```

❶ The `recordLocator` for the booking.
❷ `includeOrderDetails` is set to `true`.

### retrieveBookingRecordResponse

In the response message, SilverCore returns the rules container, which Partners can use to determine whether a Season ticket can be exchanged. A `rule` element of the type `EXCHANGE_ALLOWED` indicates that a Season ticket can be exchanged.

The following example shows that the exchange is allowed for a `TICKETED` order fulfilled as XVD. The penalty for the exchange is 0.00.

```xml
<rule type="EXCHANGE_ALLOWED" priceType="TRAVEL_PASS" applicableOrderStatus="TICKETED" applicableTicketingOption="XVD" expirationDateTime="2023-10-14T22:59:00Z" refundToVoucher="NOT_POSSIBLE">
  <penalty currency="GBP">0.00</penalty>
</rule>
```

## Step 2 – Search for a new Season ticket

### travelPassExchangeSearchRequest

To search for a replacement Season ticket, perform a `travelPassExchangeSearchRequest` and pass in the following data:

1. `recordLocator` to identify the order.
2. `orderLocator` to identify the order within the booking.
3. `travelPassExchangeSets` container element with the `ticketableFareLocator` for the Season ticket you wish to exchange and a shopping query in `travelPassExchangeShoppingQuery`, with the travel point pairs and start date for the replacement.

**Example travelPassExchangeSearchRequest**:
```xml
<book:travelPassExchangeSearchRequest version="2.0">
  <com:context>
    <com:distributorCode>DEMO</com:distributorCode>
    <com:pointOfSaleCode>GB</com:pointOfSaleCode>
    <com:channelCode>WEB</com:channelCode>
  </com:context>
  <book:recordLocator>B-DEMO-QOG0001K0</book:recordLocator>
  <book:orderLocator>O-DEMO-QOG0001K0-VYN000001</book:orderLocator>
❶ <book:travelPassExchangeSets>
    <book:travelPassExchangeSet exchangeSetID="EXCH-1">
      <com:ticketableFareLocators>
❷      <com:ticketableFareLocator>TF-QOG0001K0-VYN000001-0</com:ticketableFareLocator>
      </com:ticketableFareLocators>
      <book:travelPassExchangeShoppingQuery>
❸      <book:travelPointPair>
          <com:originTravelPoint type="STATION">GBMPV</com:originTravelPoint>
          <com:destinationTravelPoint type="STATION">GBLNT</com:destinationTravelPoint>
          <com:startDate>2023-09-23</com:startDate>
        </book:travelPointPair>
      </book:travelPassExchangeShoppingQuery>
    </book:travelPassExchangeSet>
  </book:travelPassExchangeSets>
</book:travelPassExchangeSearchRequest>
```

❶ The `travelPassExchangeSet` contains the unique identifier for the Season ticket you wish to exchange and the travel point pairs and start date for the replacement.
❷ The unique identifier for the Season ticket to exchange.
❸ The origin, destination, and start date for the replacement.

### travelPassExchangeSearchResponse

In the `travelPassExchangeSearchResponse`, the `travelPassExchangeSets` container returns the following data:

- Unique identifier for the Season ticket to exchange.
- Fares and associated prices for the replacement Season tickets that satisfy the search and that the customer needs to choose from.

**Example travelPassExchangeSearchResponse**:

```xml
<ns2:travelPassExchangeSearchResponse xmlns:ns8="http://railgds.net/ws/extractbooking" xmlns:ns7="http://railgds.net/ws/vault" xmlns:ns6="http://railgds.net/ws/search" xmlns:ns5="http://railgds.net/ws/onaccount" xmlns:ns4="http://railgds.net/ws/keystore" xmlns:ns3="http://railgds.net/ws/shopping" xmlns:ns2="http://railgds.net/ws/booking" xmlns="http://railgds.net/ws/commontypes">
  <requestStatus systemId="/U2Z94">
    <success>true</success>
  </requestStatus>
  <ns2:results>
    <ns2:travelPassExchangeSets>
      <ns2:travelPassExchangeSet exchangeSetID="EXCH-1">
        <ns2:exchangedTicketableFares>
❶        <ns2:exchangedTicketableFareLocator>TF-QOG0001K0-VYN000001-0</ns2:exchangedTicketableFareLocator>
        </ns2:exchangedTicketableFares>
        <ns2:prices>
          <ns2:travelPassPrice priceID="PRICE_P_1_1">
            <totalPrice currency="GBP">1659.30</totalPrice>
            <ticketableFares>
❷             <ticketableFare>
                <totalPrice currency="GBP">1659.30</totalPrice>
                <passengerReferences>
                  <passengerReference>
                    <passengerIDRef>P-QOG0001K0-0</passengerIDRef>
                    <passengerTypeCode>A</passengerTypeCode>
                    <fareCodes>
                      <fareCode code="IWC-PSS-00000-STD-1">
                        <serviceClass>THIRD</serviceClass>
                        <cabinClass>Standard</cabinClass>
                        <rewardsEligible>NO</rewardsEligible>
                        <fareClass>SEASON_MONTHLY</fareClass>
                        <fareDisplayName>Season Ticket</fareDisplayName>
                      </fareCode>
                    </fareCodes>
                  </passengerReference>
                </passengerReferences>
❸               <prices>
                  <price type="TRAVEL_PASS" currency="GBP">1659.30</price>
                </prices>
                <rules>...</rules>
❹               <ticketingOptionsAvailable TOD="true" ETK="true" PAH="false" PRN="true" EML="false" DEPARTURE_STATION_TOD="true" SMS="false" XVD="true" MVD="false" SCT="true"/>
                <fareOrigin type="STATION">GBMPV</fareOrigin>
                <fareDestination type="STATION">GBLNT</fareDestination>
                <fareStartDate>2023-09-23</fareStartDate>
                <fareExpirationDate>2023-10-21</fareExpirationDate>
              </ticketableFare>
            </ticketableFares>
            <holdExpiration>2023-10-21T11:59:03Z</holdExpiration>
          </ns2:travelPassPrice>
        </ns2:prices>
      </ns2:travelPassExchangeSet>
    </ns2:travelPassExchangeSets>
    <ns2:passengers>...</ns2:passengers>
  </ns2:results>
</ns2:travelPassExchangeSearchResponse>
```

❶ The unique identifier for the Season ticket to exchange.
❷ The fare that satisfies the search request.
❸ The price associated with the fare.
❹ The Ticket Delivery Options that are available for the fare.

## Step 3 – Estimate costs and penalties

### retrieveTravelPassExchangeSummaryRequest

To estimate and display the additional costs and penalties associated with the exchange, submit a `retrieveTravelPassExchangeSummaryRequest` and pass in the following data:

1. `recordLocator` and `orderLocator` from your `travelPassExchangeSearchRequest`.
2. `travelPassExchangeSets` container element with:
  - `ticketableFareLocator` for the Season ticket you wish to exchange.
  - `travelPointPair` for the chosen replacement ticket and its associated `travelPassPrice`.
3. `passengers` container element with the details for the passenger, as returned in the original booking response.
4. `fulfillmentInformation` container with the chosen TDO.

**Example retrieveTravelPassExchangeSummaryRequest**:

```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/"               xmlns:ns2="http://railgds.net/ws/shopping"               xmlns:com="http://railgds.net/ws/commontypes"               xmlns:book="http://railgds.net/ws/booking"               xmlns:shop="http://railgds.net/ws/shopping">
  <soap:Header/>
  <soap:Body>
    <book:retrieveTravelPassExchangeSummaryRequest version="2.0">
      <com:context>
        <com:distributorCode>DEMO</com:distributorCode>
        <com:pointOfSaleCode>GB</com:pointOfSaleCode>
        <com:channelCode>WEB</com:channelCode>
      </com:context>
      <book:recordLocator>B-DEMO-PAC0002Z0</book:recordLocator>
      <book:orderLocator>O-DEMO-PAC0002Z0-QJP000001</book:orderLocator>
 ❶   <book:travelPassExchangeSets>
        <book:travelPassExchangeSet exchangeSetID="EXCH-1">
          <com:ticketableFareLocators>
            <com:ticketableFareLocator>TF-PAC0002Z0-QJP000001-0</com:ticketableFareLocator>
          </com:ticketableFareLocators>
          <book:travelPointPair>
            <com:originTravelPoint type="STATION">GB@LO</com:originTravelPoint>
            <com:destinationTravelPoint type="STATION">GBBIT</com:destinationTravelPoint>
            <com:startDate>2023-10-16</com:startDate>
          </book:travelPointPair>
          <book:prices>
            <book:travelPassPrice priceID="PRICE_P_1_26">
              <com:totalPrice currency="GBP">682.00</com:totalPrice>
              <com:ticketableFares>
                <com:ticketableFare>
                  <com:totalPrice currency="GBP">682.00</com:totalPrice>
                  ...
                  <com:prices>
                    <com:price currency="GBP" type="TRAVEL_PASS">682.00</com:price>
                  </com:prices>
                  ...
                  <com:ticketingOptionsAvailable DEPARTURE_STATION_TOD="true" SCT="true" EML="false" PAH="false" XVD="false"                                                   SMS="false"                                                   TOD="true"                                                    MVD="false"                                                   PRN="true"                                              ETK="false"/>
                  <com:fareOrigin type="STATION">GBLNT</com:fareOrigin>
                  <com:fareDestination type="STATION">GBBIT</com:fareDestination>
                  <com:fareStartDate>2023-10-16</com:fareStartDate>
                  <com:fareExpirationDate>2023-11-03</com:fareExpirationDate>
                </com:ticketableFare>
              </com:ticketableFares>
              <com:holdExpiration>2023-11-03T17:31:47Z</com:holdExpiration>
            </book:travelPassPrice>
          </book:prices>
        </book:travelPassExchangeSet>
      </book:travelPassExchangeSets>
      <book:passengers>...</book:passengers>
❷    <book:fulfillmentInformation>
        <book:ticketOption code="SCT">
          <com:fee currency="GBP" isRefundable="false">0.00</com:fee>
        </book:ticketOption>
        ...
      </book:fulfillmentInformation>
      <book:responseSpec>
❸     <book:returnReservationDetails>true</book:returnReservationDetails>
      </book:responseSpec>
    </book:retrieveTravelPassExchangeSummaryRequest>
  </soap:Body>
</soap:Envelope>
```

❶ The `travelPassExchangeSets` container includes the unique identifier for the Season ticket to exchange and the replacement fare and associated price chosen by the customer.
❷ In `fulfillmentInformation`, pass in the chosen Ticket Delivery Option.

❸ If `returnReservationDetails="true"`, the response will include a full booking record.

### retrieveTravelPassExchangeSummaryResponse

The `retrieveTravelPassExchangeSummaryResponse` message returns the `exchangeSummary` container element, which includes the penalty breakdown and whether the exchange will result in money due (`ADD_COLLECT`), money refunded (`REFUND`), or no money owed (`EVEN_EXCHANGE`).

**Example retrieveTravelPassExchangeSummaryResponse for **a more expensive fare (ADD_COLLECT)**:

```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Body>
    <ns2:retrieveTravelPassExchangeSummaryResponse xmlns:ns8="http://railgds.net/ws/extractbooking" xmlns:ns7="http://railgds.net/ws/vault" xmlns:ns6="http://railgds.net/ws/search" xmlns:ns5="http://railgds.net/ws/onaccount" xmlns:ns4="http://railgds.net/ws/keystore" xmlns:ns3="http://railgds.net/ws/shopping" xmlns:ns2="http://railgds.net/ws/booking" xmlns="http://railgds.net/ws/commontypes">
      <requestStatus systemId="U/Kaxf">
        <success>true</success>
      </requestStatus>
      <ns2:exchangeSummary>
        <exchangeOptions>
❶        <exchangeOption exchangeOptionType="ADD_COLLECT">
❷         <total currency="GBP">321.50</total>
            <penalty>
❸             <total currency="GBP">0.00</total>
              <breakdown>
                <component type="TICKET" currency="GBP">0.00</component>
              </breakdown>
            </penalty>
            <exchangeBreakdown>
❹            <newProductsTotal currency="GBP">682.00</newProductsTotal>
             <newFeesTotal currency="GBP">0.00</newFeesTotal>
❺            <reversedRevenueTotal currency="GBP">-360.50</reversedRevenueTotal>
❻            <revenueTotal currency="GBP">569.10</revenueTotal>
❼            <receiptTotal currency="GBP">569.10</receiptTotal>
            </exchangeBreakdown>
          </exchangeOption>
        </exchangeOptions>
      </ns2:exchangeSummary>
      <ns2:bookingRecord recordLocator="B-DEMO-PAC0002Z0" status="BALANCED">
      ...
      </ns2:bookingRecord>
    </ns2:retrieveTravelPassExchangeSummaryResponse>
  </soap:Body>
</soap:Envelope>
```
❶ This exchange will result in money due, `exchangeOptionType= "ADD_COLLECT"`.
❷ SilverCore calculates that 321.50 will need to be collected from the customer following the exchange:
  - £-360.50 (reversal of unused portion of original Season ticket) + £682.00 (cost of replacement Season ticket)
  =  **£321.50**
❸ There is no penalty for the exchange.
❹ The cost of the replacement Season ticket.
❺ The unused portion of the original Season ticket to be refunded.
❻ The total revenue for all the orders currently on the booking (ticket prices, fees, etc.).
❼ The total receipts for all the orders currently on the booking (payments received and refunds issued).

**Example retrieveTravelPassExchangeSummaryResponse for a cheaper fare (REFUND)**:

```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Body>
    <ns2:retrieveTravelPassExchangeSummaryResponse xmlns:ns8="http://railgds.net/ws/extractbooking" xmlns:ns7="http://railgds.net/ws/vault" xmlns:ns6="http://railgds.net/ws/search" xmlns:ns5="http://railgds.net/ws/onaccount" xmlns:ns4="http://railgds.net/ws/keystore" xmlns:ns3="http://railgds.net/ws/shopping" xmlns:ns2="http://railgds.net/ws/booking" xmlns="http://railgds.net/ws/commontypes">
      <requestStatus systemId="8Qewfh">
        <success>true</success>
      </requestStatus>
      <ns2:exchangeSummary>
        <exchangeOptions>
          <exchangeOption exchangeOptionType="REFUND">
            <total currency="GBP">-96.00</total>
            <refundTarget type="FORM_OF_PAYMENT"/>
            <penalty>
              <total currency="GBP">0.00</total>
              <breakdown>
                <component type="TICKET" currency="GBP">0.00</component>
              </breakdown>
            </penalty>
            <exchangeBreakdown>
              <newProductsTotal currency="GBP">435.20</newProductsTotal>
              <newFeesTotal currency="GBP">0.00</newFeesTotal>
              <reversedRevenueTotal currency="GBP">-531.20</reversedRevenueTotal>
              <revenueTotal currency="GBP">569.10</revenueTotal>
              <receiptTotal currency="GBP">569.10</receiptTotal>
            </exchangeBreakdown>
          </exchangeOption>
        </exchangeOptions>
      </ns2:exchangeSummary>
      <ns2:bookingRecord recordLocator="B--PAC0002Z0" status="BALANCED">         
      ...            
      </ns2:bookingRecord>
    </ns2:retrieveTravelPassExchangeSummaryResponse>
  </soap:Body>
</soap:Envelope>
```

❶ This exchange will result in money owed, `exchangeOptionType= "REFUND"`.
❷ SilverCore calculates that 96.00 will need to be refunded to the customer following the exchange:
  - £-531.20 (reversal of unused portion of original Season ticket) + £435.20 (cost of replacement Season ticket)
  =  **£-96.20**
❸ There is no penalty for the exchange.
❹ The cost of the replacement Season ticket.
❺ The unused portion of the original Season ticket to be refunded.
❻ The total revenue for all the orders currently on the booking (ticket prices, fees, etc.).
❼ The total receipts for all the orders currently on the booking (payments received and refunds issued).

## Step 4 – Process the exchange

### processTravelPassExchangeRequest

To process the exchange, submit a `processTravelPassExchangeRequest` message, passing in the following data:

1. `recordLocator` and `orderLocator` from your `travelPassExchangeSearchRequest`.
2. `travelPassExchangeSets` container element with:
  - `ticketableFareLocator` for the Season ticket you wish to exchange.
  - `travelPointPair` for the chosen replacement ticket and its associated `travelPassPrice`.
3. `passengers` container element with the details for the passenger, as returned in the original booking response.
4. `selectedExchangeOption` from the chosen `exchangeOption` from your `retrieveTravelPassExchangeSummaryResponse`, indicating the type of exchange: `ADD_COLLECT`, `REFUND`, or `EVEN_EXCHANGE`.
5. `fulfillmentInformation` container with the chosen TDO.
6. For `ADD_COLLECT` scenarios only, the payment container to identify the payment card for the additional payment.

**Example processTravelPassExchangeRequest** (REFUND):

```xml
<soap:Envelope xmlns:com="http://railgds.net/ws/commontypes"               xmlns:book="http://railgds.net/ws/booking"               xmlns:shop="http://railgds.net/ws/shopping"               xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Header/>
  <soap:Body>
    <book:processTravelPassExchangeRequest  version="2.0">
      <com:context>
        <com:distributorCode>DEMO</com:distributorCode>
        <com:pointOfSaleCode>GB</com:pointOfSaleCode>
        <com:channelCode>WEB</com:channelCode>
      </com:context>
      <book:recordLocator>B-DEMO-XOG00009T</book:recordLocator>
      <book:orderLocator>O-DEMO-XOG00009T-CDC000001</book:orderLocator>
      <book:travelPassExchangeSets>
        <book:travelPassExchangeSet exchangeSetID="EXCH-1">
          <com:ticketableFareLocators>
            <com:ticketableFareLocator>TF-XOG00009T-CDC000001-0</com:ticketableFareLocator>
          </com:ticketableFareLocators>
❶         <book:travelPointPair>
            <com:originTravelPoint type="STATION">GB@LO</com:originTravelPoint>
            <com:destinationTravelPoint type="STATION">GBAYS</com:destinationTravelPoint>
            <com:startDate>2023-10-14</com:startDate>
          </book:travelPointPair>
          <book:prices>
❷         <book:travelPassPrice priceID="PRICE_P_1_2">...</book:travelPassPrice>
          </book:prices>
        </book:travelPassExchangeSet>
      </book:travelPassExchangeSets>
      <book:passengers>
        <book:passenger passengerID="P-XOG00009T-0">...</book:passenger>
      </book:passengers>
❸     <book:selectedExchangeOption exchangeOptionType="REFUND">
        <com:total currency="GBP">-119.10</com:total>
        <com:refundTarget type="FORM_OF_PAYMENT"/>
      </book:selectedExchangeOption>
      <book:fulfillmentInformation>
❹       <book:ticketOption code="SCT">
          <com:fee currency="GBP" isRefundable="false">0.00</com:fee>
          </book:ticketOption>            
          ...            
❺         <book:pickupLocation type="STATION">GBMYB</book:pickupLocation>
        </book:fulfillmentInformation>
        <book:responseSpec>
          <book:returnReservationDetails>true</book:returnReservationDetails>
        </book:responseSpec>
      </book:processTravelPassExchangeRequest>
    </soap:Body>
  </soap:Envelope>
```

❶ The origin and destination for the chosen replacement journey.
❷ The associated price for the chosen replacement journey.
❸ The selected exchange option as returned in your `retrieveTravelPassExchangeSummaryResponse` message. In this scenario, the child element `total` identifies the amount to be refunded to the customer.
❹ The chosen fulfillment method for the replacement Season ticket is a Smartcard (SCT).
❺ For journeys fulfilled as SCT in the UK market, the location chosen by the customer to pick up the ticket.

**Example processTravelPassExchangeRequest** (ADD_COLLECT):

```xml
<soap:Envelope xmlns:com="http://railgds.net/ws/commontypes"               xmlns:book="http://railgds.net/ws/booking"               xmlns:shop="http://railgds.net/ws/shopping"               xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Header/>
  <soap:Body>
    <book:processTravelPassExchangeRequest  version="2.0">
      <com:context>
        <com:distributorCode>DEMO</com:distributorCode>
        <com:pointOfSaleCode>GB</com:pointOfSaleCode>
        <com:channelCode>WEB</com:channelCode>
      </com:context>
      <book:recordLocator>B-DEMO-XOG00009T</book:recordLocator>
      <book:orderLocator>O-DEMO-XOG00009T-CDC000001</book:orderLocator>
      <book:travelPassExchangeSets>
        <book:travelPassExchangeSet exchangeSetID="EXCH-1">
          <com:ticketableFareLocators>
            <com:ticketableFareLocator>TF-XOG00009T-CDC000001-0</com:ticketableFareLocator>
          </com:ticketableFareLocators>
❶         <book:travelPointPair>
            <com:originTravelPoint type="STATION">GB@LO</com:originTravelPoint>
            <com:destinationTravelPoint type="STATION">GBAYS</com:destinationTravelPoint>
            <com:startDate>2023-10-14</com:startDate>
          </book:travelPointPair>
          <book:prices>
❷         <book:travelPassPrice priceID="PRICE_P_1_2">...</book:travelPassPrice>
          </book:prices>
        </book:travelPassExchangeSet>
      </book:travelPassExchangeSets>
      <book:passengers>
        <book:passenger passengerID="P-XOG00009T-0">...</book:passenger>
      </book:passengers>
❸     <book:selectedExchangeOption exchangeOptionType="REFUND">
        <com:total currency="GBP">-119.10</com:total>
        <com:refundTarget type="FORM_OF_PAYMENT"/>
      </book:selectedExchangeOption>
      <book:fulfillmentInformation>
❹       <book:ticketOption code="SCT">
          <com:fee currency="GBP" isRefundable="false">0.00</com:fee>
          </book:ticketOption>            
          ...            
❺         <book:pickupLocation type="STATION">GBMYB</book:pickupLocation>
        </book:fulfillmentInformation>
        <book:responseSpec>
          <book:returnReservationDetails>true</book:returnReservationDetails>
        </book:responseSpec>
      </book:processTravelPassExchangeRequest>
    </soap:Body>
  </soap:Envelope>
```

❶ The selected exchange option as returned in your `retrieveTravelPassExchangeSummaryResponse`. In this scenario, the child element `total` identifies the amount to be collected from the customer.
❷ Because there is an outstanding amount to be paid by the customer, your request must include the `payment` container, the payment card, and the amount to be collected.

### processTravelPassExchangeResponse

If the `returnReservationDetails` request parameter is set to `true` in your request, your `processTravelPassExchangeResponse` will return a complete [booking record](/v1/docs/retrievebookingrecordresponse-elements#code-item-ns2-bookingRecord){target="_blank"} containing the details for the exchanged order and the new order for the replacement Season ticket. A complete set of financials will be returned in the response.

The `bookingRecord` `status` attribute provides the booking status. The value is based on the relationship between receipts (the payment amounts received or refund amounts issued) and revenues (the price of the tickets plus any fees).

If the exchange results in a refund being due, as in the following example, the booking status will be `CREDIT`.

When a refund is due, the `receiptsTotal` will be greater than the `revenueTotal`. The difference is the amount to be refunded by Partners through a `refundBookingRecordRequest`.

**Example processTravelPassExchangeResponse** (truncated):

```xml
soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Body>
    <ns2:processTravelPassExchangeResponse xmlns:ns8="http://railgds.net/ws/extractbooking" xmlns:ns7="http://railgds.net/ws/vault" xmlns:ns6="http://railgds.net/ws/search" xmlns:ns5="http://railgds.net/ws/onaccount" xmlns:ns4="http://railgds.net/ws/keystore" xmlns:ns3="http://railgds.net/ws/shopping" xmlns:ns2="http://railgds.net/ws/booking" xmlns="http://railgds.net/ws/commontypes">
      <requestStatus systemId="IeutTK">
        <success>true</success>
      </requestStatus>
      <ns2:bookingRecord recordLocator="B-DEMO-NAI0000BZ" status="CREDIT">
        <bookingDate>2023-10-15Z</bookingDate>
        <numberOfOrders>2</numberOfOrders>
❶       <revenueTotal currency="GBP">466.20</revenueTotal>
        <receiptsTotal currency="GBP">585.30</receiptsTotal>
        <passengers>...</passengers>
        <orders>
❷         <order orderID="O-DEMO-NAI0000BZ-NOK000001" status="CONFIRMED" originalOrderID="O-DEMO-NAI0000BZ-KJJ000001">
            <dateBooked>2023-10-15Z</dateBooked>
            <holdExpiration>2023-10-24T07:48:54Z</holdExpiration>
            <fulfillmentInformation>                     ...                     
            <valueDocument status="READY">
              ...
            </valueDocument>
            <pickupLocation type="STATION">GBMYB</pickupLocation>
          </fulfillmentInformation>                  
          ...                     
          <priceReversals>
          <total currency="GBP">585.30</total>
          ...
        </priceReversals>
      </cancellationSummary>
      <travelSegments/>
      <ticketableFares>...</ticketableFares>
      <ticketingOptions>
        <ticketingOption code="SCT" type="S_TICKET">
          <description>Smart Card Ticket</description>
          <fee isRefundable="false" currency="GBP">0.00</fee>
        </ticketingOption>
      </ticketingOptions>
      ...
    </order>
❸   <order orderID="O-DEMO-NAI0000BZ-KJJ000001" status="REPLACED">...</order>
  </orders>
  <paymentRequirements>...</paymentRequirements>
  <serviceFeeAllowed>false</serviceFeeAllowed>
  <creditCardSurchargeFeeApplicable>false</creditCardSurchargeFeeApplicable>
  <financials>
    <prices>...</prices>
    <payments>...</payments>
  </financials>
</ns2:bookingRecord>
</ns2:processTravelPassExchangeResponse>
</soap:Body>
</soap:Envelope>
```

❶ The receipts for the order exceed the revenue; this order will require a refund to balance the financials.
❷ The new order for the replacement Season ticket, with the status `CONFIRMED`.
❸ The original and now exchanged order, with the status `REPLACED`.

Following a successful exchange, the booking will contain two orders:

1. The original `CONFIRMED` order, which will now have the status `REPLACED`. The `valueDocument@status` in the original order will move from `READY`/`ISSUED` (depending on whether the order is `CONFIRMED`/`TICKETED`) to `CANCELED`.
2. A new order for the replacement journey with the status `CONFIRMED`.

Partners must provide customers with information about the tickets for the replacement journey. For E-Tickets, please refer to **Step 5 – Issue the value document**.

## Step 5 – Issue the value document (required step for E-Ticket)

Where the customer has chosen to travel using E-Tickets, (`selectedTicketingOption` is `ETK`, `XVD`, `MVD`, `PAH`), Partners are required to send a `claimValueDocumentRequest` to issue the necessary documents that entitle the bearer to travel.

### claimValueDocumentRequest

To issue the value document, submit a `claimValueDocumentRequest`, passing in the following data:

1. `recordLocator`.
2. `orderLocator`.
3. Optionally, for the UK market, one of the supported request parameters, depending on your ticketing requirements:
  - `pdfPerCoupon`.
  - `returnETicketPdfDetails`.

**Example request**:

```xml
<soap:Envelope xmlns:com="http://railgds.net/ws/commontypes"               xmlns:book="http://railgds.net/ws/booking"               xmlns:shop="http://railgds.net/ws/shopping"               xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Header/>
  <soap:Body>
    <book:claimValueDocumentRequest version="2.0">
      <com:context>
        <com:distributorCode>DEMO</com:distributorCode>
        <com:pointOfSaleCode>GB</com:pointOfSaleCode>
        <com:channelCode>WEB</com:channelCode>
      </com:context>
      <book:recordLocator>B-DEMO-EKZ00008S</book:recordLocator>
      <book:orderLocator>O-DEMO-EKZ00008S-MCO000001</book:orderLocator>
      <book:responseSpec>
        <book:returnReservationDetails>true</book:returnReservationDetails>
      </book:responseSpec>
    </book:claimValueDocumentRequest>
  </soap:Body>
</soap:Envelope>
```

### claimValueDocumentResponse

Partners must provide customers with information about the tickets for the replacement journey.

**Example claimValueDocumentResponse** (truncated):

```xml
soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
   <soap:Body>
      <ns2:claimValueDocumentResponse conversationToken="UK-XVD-png" xmlns:ns8="http://railgds.net/ws/extractbooking" xmlns:ns7="http://railgds.net/ws/vault" xmlns:ns6="http://railgds.net/ws/search" xmlns:ns5="http://railgds.net/ws/onaccount" xmlns:ns4="http://railgds.net/ws/keystore" xmlns:ns3="http://railgds.net/ws/shopping" xmlns:ns2="http://railgds.net/ws/booking" xmlns="http://railgds.net/ws/commontypes">
         <requestStatus systemId="IeutTK">
            <success>true</success>
         </requestStatus>
         <ns2:valueDocument status="ISSUED">
            <valueDocumentLocator>QJJ000001</valueDocumentLocator>
            <retrievalCustomer>Not Applicable</retrievalCustomer>
            <retrievalMaskedCreditCard>Not Applicable</retrievalMaskedCreditCard>
            <retrievalCode>XXXXXXXXX</retrievalCode>
            <eTickets>
               <eTicket>...</eTicket>
               <eTicket>...</eTicket>
            </eTickets>
         </ns2:valueDocument>
         <ns2:bookingRecord recordLocator="B-SILVERRAIL-ZEH0000C8" status="BALANCED">
            ...
            <passengers>...</passengers>
            <orders>
 ❶           <order orderID="O-SILVERRAIL-ZEH0000C8-QJJ000001" status="TICKETED">
                  <supplyChannelRecordLocator>RG657230</supplyChannelRecordLocator>
                  <dateBooked>2023-10-15Z</dateBooked>
                  <holdExpiration>2023-10-15T13:17:03Z</holdExpiration>
                  <fulfillmentInformation>
                     <selectedTicketingOption code="XVD" type="E_TICKET" redeliveryAllowed="true">
                        <description>Electronic Ticket - XML Value Document</description>
                        <fee isRefundable="false" currency="GBP">0.00</fee>
                     </selectedTicketingOption>
                     <valueDocument status="ISSUED">
                        <valueDocumentLocator>QJJ000001</valueDocumentLocator>
                        <retrievalCustomer>Not Applicable</retrievalCustomer>
                        <retrievalMaskedCreditCard>Not Applicable</retrievalMaskedCreditCard>
                        <retrievalCode>XXXXXXXXX</retrievalCode>
                        ...
                        <selectedTicketLanguage>en</selectedTicketLanguage>
                     </valueDocument>
                  </fulfillmentInformation>
                  ...
               </order>
            </orders>
            ...
         </ns2:bookingRecord>
      </ns2:claimValueDocumentResponse>
   </soap:Body>
</soap:Envelope>
```

❶ Once the value document is issued following a successful `claimValueDocumentResponse`, the order status moves from `CONFIRMED` to `TICKETED`.

## Step 6 - Issue a refund (required step when booking status is CREDIT)

### refundBookingRecordRequest

If the passenger is due a refund following the exchange (`exchangeOptionType="REFUND"`), Partners must submit a `refundBookingRecordRequest`, passing in the following data:

- `recordLocator` from your `travelPassExchangeSearchRequest` or `processTravelPassExchangeRequest`.
- `receiptNumber` from the booking record.
- `refundAmount` from `exchangeOption` > `total` from your `retrieveTravelPassExchangeSummaryResponse`.

**Example refundBookingRecordRequest**:

```xml
<soap:Envelope xmlns:com="http://railgds.net/ws/commontypes"               xmlns:book="http://railgds.net/ws/booking"               xmlns:shop="http://railgds.net/ws/shopping"               xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Header/>
  <soap:Body>
    <book:refundBookingRecordRequest version="2.0">
      <com:context>
        <com:distributorCode>DEMO</com:distributorCode>
        <com:pointOfSaleCode>GB</com:pointOfSaleCode>
        <com:channelCode>WEB</com:channelCode>
      </com:context>
      <book:recordLocator>B-DEMO-LMR0001ZY</book:recordLocator>
      <book:responseSpec>
        <book:returnReservationDetails>true</book:returnReservationDetails>
      </book:responseSpec>
      <book:receiptNumber>R-DEMO-LPZ002238</book:receiptNumber>
      <book:refundAmount currency="EUR">60.2</book:refundAmount>
    </book:refundBookingRecordRequest>
  </soap:Body>
</soap:Envelope>
```

## refundBookingRecordResponse

Following a successful request, the booking status will change from CREDIT to BALANCED, and receiptsTotal will now equal revenueTotal.

The refundBookingRecordResponse will echo back the CONFIRMED and REPLACED orders associated with the booking and include a new payment record of type REFUND.

**Example response**:

```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Body>
    <ns2:refundBookingRecordResponse xmlns:ns8="http://railgds.net/ws/extractbooking" xmlns:ns7="http://railgds.net/ws/vault" xmlns:ns6="http://railgds.net/ws/search" xmlns:ns5="http://railgds.net/ws/onaccount" xmlns:ns4="http://railgds.net/ws/keystore" xmlns:ns3="http://railgds.net/ws/shopping" xmlns:ns2="http://railgds.net/ws/booking" xmlns="http://railgds.net/ws/commontypes">
      <requestStatus systemId="NgXo7x">
        <success>true</success>
      </requestStatus>
❶     <ns2:bookingRecord recordLocator="B-DEMO-AEK000221" status="BALANCED">
        <bookingDate>2023-05-04Z</bookingDate>
        <departureDate>2023-05-06</departureDate>
        <numberOfOrders>2</numberOfOrders>
❷       <revenueTotal currency="EUR">122.20</revenueTotal>
        <receiptsTotal currency="EUR">122.20</receiptsTotal>
        <passengers>...</passenger>
      </passengers>
❸    <orders>
        <order orderID="O-DEMO-AEK000221-JDC000001" status="CONFIRMED" originalOrderID="O-DEMO-AEK000221-YLN000001">              ...
        </order>
       <order orderID="O-DEMO-AEK000221-YLN000001" status="REPLACED">
❹      <cancellationReason>EXCHANGED</cancellationReason>
❺      <internalCancellationReason>EXCHANGE</internalCancellationReason>
        ...
        </order>
      </orders>
      <paymentRequirements>...</paymentRequirements>
      <serviceFeeAllowed>true</serviceFeeAllowed>
      <creditCardSurchargeFeeApplicable>false</creditCardSurchargeFeeApplicable>
      <financials>
        <prices>...</prices>
 ❻     <payments>
          <payment type="PAYMENT">
            <formOfPayment type= "OA"/>
            <amount currency="EUR">182.40</amount>
            <balanceAmount currency="EUR">122.20</balanceAmount>
            <postingDate>2023-05-04Z</postingDate>
            <postingTime>13:18:52Z</postingTime>
            <receiptNumber>R-DEMO-ZIC002273</receiptNumber>
          </payment>
          <payment type="REFUND">
            <formOfPayment type= "OA"/>
            <amount currency="EUR">-60.20</amount>
            <postingDate>2023-05-04Z</postingDate>
            <postingTime>13:19:13Z</postingTime>
            <receiptNumber>R-DEMO-UJE002306</receiptNumber>
            <referenceReceiptNumber>R-DEMO-ZIC002273</referenceReceiptNumber>
          </payment>
        </payments>
      </financials>
    </ns2:bookingRecord>
  </ns2:refundBookingRecordResponse>
</soap:Body>
</soap:Envelope>
```

❶ The booking is now `BALANCED`.
❷ `receiptsTotal` now equals `revenueTotal`.
❸ Similarly to the `processTravelPassExchangeResponse`, the `refundBookingRecordResponse` includes the original `REPLACED` order and the new `CONFIRMED` order for the replacement journey.
❹ The payments container includes a payment record of type `REFUND`.

## Possible errors encountered when exchanging Season tickets

### BK00183

SilverCore will return a `BK00183` booking error message when attempting to perform an exchange and the duration of the replacement Season ticket does not match the duration of the original ticket:

```xml
<statusMessages>
  <statusMessage>
    <message>Invalid Exchange Set: Requested end date does not match the end date of original order being exchanged</message>
    <code>BK00183</code>
  </statusMessage>
```

### BK00173

SilverCore will return a `BK00173` booking error message when attempting to perform an exchange and the original Season ticket has been issued for anything less than one month:

```xml
<statusMessages>
  <statusMessage>
    <message>Invalid Exchange Set: Travel Pass Exchange has to be applied to tickets issued for a validity of one month or more</message>
    <code>BK00173</code>
  </statusMessage>
</statusMessages>
```

### BK00182

SilverCore will return a `BK00182` booking error message when attempting to perform an exchange and the fare state date for the original Season ticket has not passed:

```xml
<statusMessages>
  <statusMessage>
    <message>A ticket cannot be Travel Pass Exchanged until one day after the date the fare begins</message>
    <code>BK00182</code>
  </statusMessage>
</statusMessages>
```

### BK00180

SilverCore will return a `BK00180` error message when attempting to perform an exchange, and the fare start date for the replacement Season ticket is more than one day beyond the current date:

```xml
<statusMessages>
  <statusMessage>
    <message>Invalid Exchange Set: Only same day or next day Travel Pass Exchange is permitted</message>
    <code>BK00180</code>
  </statusMessage>
</statusMessages>
```

### BK00308

SilverCore will return a `BK00308` error message when attempting to perform an exchange and the original Season ticket was booked using a Fare Qualifier:

```xml
<statusMessages>
  <statusMessage>
    <message>TravelPass Exchange cannot occur if fare qualifiers are present in the original order</message>
    <code>BK00308</code>
  </statusMessage>
</statusMessages>
```

### BK00309

SilverCore will return a `BK00309` error message when attempting to perform an exchange and a Fare Qualifier has been added to the replacement Season ticket:

```xml
<statusMessages>
  <statusMessage>
    <message>TravelPass Exchange cannot occur if fare qualifiers are present in the exchange set</message>
    <code>BK00309</code>
  </statusMessage>
</statusMessages>
```
