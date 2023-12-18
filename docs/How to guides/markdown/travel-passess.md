:::(Error)(**In this article** , as part of series of How-Tos, we'll look at how to shop, book, and cancel Travel Passes in SilverCore.

## Intro

In the UK market, Season tickets and Carnets allow passengers to travel at a discounted rate along a specific route.  The principal difference between Seasons and Carnets is the frequency of travel allowed and the validity period.

A start date is required for both Season tickets and Carnets and can technically be within 14 days for Season tickets and 84 days for Carnets.  An end date is optional, but if selected, it must be more than one month from the start date.

Ranger and Rover tickets provide passengers unlimited travel within a specified area and period for the UK market.

In SilverCore, these different types of tickets are collectively referred to as Travel Passes.

Shopping for Travel Passes is done through the [travelPassShoppingRequest](/v1/docs/travelpassshoppingrequest-elements).  Creating and canceling bookings for Travel Passes is done through the [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements) and [cancelBookingRecordRequest](/v1/docs/cancelbookingrecordrequest-elements), respectively.

The SilverCore flow mirrors that for shopping, booking, and canceling Point-To-Point fares, with some important differences, which are explained in detail in this How-To summary.

## Season tickets

Season tickets allow unlimited travel for one adult or child on the National Rail network between defined stations and by the route shown until the expiry date shown on the ticket.  An example is a Season ticket between Cambridge (GBCBG) and London Kings Cross (GBQQK) for 12 months.

Only a select few Fare Qualifiers can be used for Season tickets.  These are:

- 16-17 Railcard
- Job Centre Plus Railcard

## Carnets

Carnets, or Flexipass or Flexi-season tickets, are a book of single tickets or return tickets designed for passengers that often travel to a specific station but do not do so regularly and, therefore, do not require an unlimited travel option.

The same Fare Qualifiers that can be used for Season tickets can be used for Flexi-season tickets.

## Ranger and Rover tickets

Ranger and Rover tickets provide passengers with unlimited travel within a specified area and period.

Rangers are valid for one day, while Rovers are valid for more than one day within a limited period, such as three consecutive days, four days within an eight-day period, or eight days within a 15-day period.

These tickets may include local bus travel, often with additional offers and discounts.

## Fare classes

The following fare classes are returned in the shopping flow for the UK market to denote Season tickets, Carnets, and Ranger and Rover tickets.

| Fare class | Description |
| :-- | :-- |
| `SEASON_ANNUAL` | Season Ticket that allows travel for a calendar year.­­­ |
| `SEASON_CUSTOM` | Season Ticket that allows for a custom date range between one month and one year. |
| `SEASON_MONTHLY` | Season Ticket that allows travel for a calendar month. |
| `SEASON_WEEKLY` | Season Ticket that allows travel for a 7-day week. |
| `CARNET` | Book of single (one-way) tickets.  These can be flagged as bidirectional. |
| `CARNET_RETURN` | Group of return (round-trip) tickets.  These are not bidirectional. |
| `CARNET_SEASON` | Book of day passes (Flexi-season Ticket) that enables customers to travel between origin and destination multiple times in a single day. |
| `ROVER` | Ranger and Rover tickets offer unlimited travel within a specified area and period. |

## Determining Ticket Delivery Options

Ticket Delivery Options (TDO) define how travelers get their tickets.  Some examples include a Ticket Vending Machine (TVM), Ticket Office (TOF), or by mail TBM).

The following Ticket Delivery Options (TDO) are available for Travel Passes:

- **TVM** - Only available on 7-day seasons.
- **TBM** - Required for annual seasons because of the value.
- **SCT** - For TOC partners only.
- **XVD** - E-Ticket is now available on some routes.
- **ETK** - E-Ticket is now available on some routes.
- **MVD** – E-Ticket is only available for Rangers and Rover tickets.

The [validateBookingRecordInformationResponse](/v1/docs/validatebookingrecordinformationresponse-elements){target="_blank"} dynamically calculates the TDOs available for the chosen journey based on the route, and fare and business rules.

Because of the cost of most Season tickets and the fact that historically, not all TVM suppliers could print Seasons longer than seven days, only seven-day seasons are available for TVM collection.  Some seven-day Season tickets are eligible for fulfillment as E-Ticket.

For Season tickets longer than seven days, if available on the route, tickets can be fulfilled as Smartcode Tickets (SCT) or E-Tickets (ETK/XVD).  Ticket by Mail (TBM) fulfillment is required on all routes for Season tickets longer than seven days.

## Travel pass flow in SilverCore

![travel pass shop and book flow.png](https://files.document360.io/c53b2e89-8de3-47ad-adf4-5a70ab761260/Images/Documentation/travel%20pass%20shop%20and%20book%20flow.png){height="" width=""}

## Shopping for Travel Passes

In SilverCore, shopping for Season tickets, Carnets, and Rangers and Rovers is done through the [travelPassShoppingRequest](/v1/docs/travelpassshoppingrequest-elements){target="_blank"}.

### travelPassShoppingRequest

To shop for Season Tickets and Carnets, pass in the following data in the `travelPointPair` container:

- `originTravelPoint`
- `destinationTravelPoint`
- `startDate`

To shop for Rangers and Rovers, pass in the following data in the `travelPass` container:

- `code type= "RANGERS_AND_ROVERS"` and valid code
- `startDate`

:::(INFO) (Partners can retrieve Rangers and Rovers codes through the [Published Data API](/v1/docs/published-data-messages#rangers-and-rovers)target="_blank"}.)
:::

**Example travelPassShoppingRequest for Season tickets and Carnets**:
```xml
<soap:Envelope xmlns:com="http://railgds.net/ws/commontypes"               xmlns:book="http://railgds.net/ws/booking"               xmlns:shop="http://railgds.net/ws/shopping"               xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Header/>
  <soap:Body>
    <shop:travelPassShoppingRequest version="2.0">
      <com:context>
        <com:distributorCode>DEMO</com:distributorCode>
        <com:pointOfSaleCode>GB</com:pointOfSaleCode>
        <com:channelCode>WEB</com:channelCode>
      </com:context>
      <shop:travelPassShoppingQuery>
❶       <shop:travelPointPair>
          <com:originTravelPoint type="STATION">GBDBY</com:originTravelPoint>
          <com:destinationTravelPoint type="STATION">GBPEA</com:destinationTravelPoint>
          <com:startDate>2023-08-05</com:startDate>
        </shop:travelPointPair>
        <shop:passengerSpecs>
          <shop:passengerSpec>
            <com:age>40</com:age>
          </shop:passengerSpec>
        </shop:passengerSpecs>
      </shop:travelPassShoppingQuery>
    </shop:travelPassShoppingRequest>
  </soap:Body>
</soap:Envelope>
```

❶ Shop for Season Tickets and Carnets by passing the required data into the `travelPointPair` container in your request message.

**Example travelPassShoppingRequest for Rangers and Rovers** :
```xml
<soap:Envelope xmlns:com="http://railgds.net/ws/commontypes"               xmlns:book="http://railgds.net/ws/booking"               xmlns:shop="http://railgds.net/ws/shopping"               xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Header/>
  <soap:Body>
    <shop:travelPassShoppingRequest version="2.0">
      <com:context>
        <com:distributorCode>DEMO</com:distributorCode>
        <com:pointOfSaleCode>GB</com:pointOfSaleCode>
        <com:channelCode>WEB</com:channelCode>
      </com:context>
❶     <shop:travelPassShoppingQuery>
        <shop:travelPass>
          <com:code type="RANGERS_AND_ROVERS">SW4</com:code>
          <com:startDate>2023-07-27</com:startDate>
        </shop:travelPass>
        <shop:passengerSpecs>
          <shop:passengerSpec>
            <com:age>40</com:age>
          </shop:passengerSpec>
        </shop:passengerSpecs>
      </shop:travelPassShoppingQuery>
    </shop:travelPassShoppingRequest>
  </soap:Body>
</soap:Envelope>
```

❶ Shop for Rangers and Rovers by passing the required data into the `travelPass` container in your request message.

### travelPassShoppingResponse

The [travelPassShoppingResponse](/v1/docs/travelpassshoppingresponse-elements){target="_blank"} returns a list of [travelPassPrice](/v1/docs/travelpassshoppingresponse-elements#code-item-ns2-travelPassPrice){target="_blank"} elements for all Travel Pass types, containing details about the fare class, Travel Pass cost, fare types, refund eligibility, available Ticket Delivery Options, and validity period.  It also indicates if any special passenger identification documents are required at booking time.

For Season tickets and Carnets, industry rules determine whether your search returns Seasons or Carnets in the response message.

Generally, Season Tickets can be searched up to 14 days before travel and for one person only.  Carnets can be searched for up to 12 weeks before travel for one person only.

SilverCore returns the `<passengerInformationRequired>` element in the [travelPassShoppingResponse](/v1/docs/travelpassshoppingresponse-elements#passengerInformationRequired){target="_blank"} to indicate what passenger information is required at booking time.

A `passengerInformation` element of the type `TRAVEL_DOCUMENTS` indicates that travel documents are required to book tickets.

For Rangers and Rovers tickets only, the response message also includes an additional element, `fareDaysOfTravel`, which indicates the total number of days the ticket is valid for.  For example, for a ticket valid for four days of travel in an eight-day period, SilverCore will return `<fareDaysOfTravel>4</fareDaysOfTravel>`.

**Example travelPassShoppingResponse for a Season ticket**:
```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
   <soap:Body>
      <ns2:travelPassShoppingResponse conversationToken="UK-Seasons" xmlns:ns2="http://railgds.net/ws/shopping" xmlns="http://railgds.net/ws/commontypes">
         <requestStatus systemId="XknViM">
            <success>true</success>
            <statusMessages>
               <statusMessage>
❶                <message>The provided departure DateTime is not within the next 14 days.  Fares will only be returned for carnet travel passes, rangers and rovers travel passes, not seasons.</message>
                  <code>WRN00002</code>
               </statusMessage>
            </statusMessages>
         </requestStatus>
         <ns2:results>
            <ns2:passengers>
               <ns2:passenger passengerSpecID="PAX_SPEC_0">
                  <age>40</age>
               </ns2:passenger>
            </ns2:passengers>
            <ns2:fareInformation>
               <ns2:prices>
                  <ns2:travelPassPrice priceID="PRICE_P_1_0">
                     <totalPrice currency="GBP">29.20</totalPrice>
                     <ticketableFares>
                        <ticketableFare>
                           <totalPrice currency="GBP">29.20</totalPrice>
                           <passengerReferences>
                              <passengerReference>
                                 <passengerIDRef>PAX_SPEC_0</passengerIDRef>
                                 <passengerTypeCode>A</passengerTypeCode>
                                 <fareCodes>
                                    <fareCode code="SMR-FL1-01000-STD-Z">
                                       <serviceClass>THIRD</serviceClass>
                                       <cabinClass>Standard</cabinClass>
                                       <rewardsEligible>NO</rewardsEligible>
❷                                      <fareClass>CARNET_SEASON</fareClass>
                                       <fareDisplayName>Flexi Season</fareDisplayName>
                                    </fareCode>
                                 </fareCodes>
                              </passengerReference>
                           </passengerReferences>
                           <prices>
                              <price type="TRAVEL_PASS" currency="GBP">29.20</price>
                           </prices>
                           <commissions>
                              <commission type="DISTRIBUTOR" currency="GBP">1.53</commission>
                           </commissions>
                           <rules>...</rules>
                           <ticketingOptionsAvailable TOD="false" ETK="false" PAH="false" PRN="false" EML="false" DEPARTURE_STATION_TOD="false" SMS="false" XVD="true" MVD="true" SCT="true"/>
                           <fareOrigin type="STATION">GBDBY</fareOrigin>
                           <fareDestination type="STATION">GBPEA</fareDestination>
                           <fareStartDate>2023-08-05</fareStartDate>
                           <fareExpirationDate>2023-09-01</fareExpirationDate>
                        </ticketableFare>
                     </ticketableFares>
                     <holdExpiration>2023-07-20T12:33:33Z</holdExpiration>
                  </ns2:travelPassPrice>
               </ns2:prices>
            </ns2:fareInformation>
            <ns2:passengerInformationRequired>
❸              <ns2:passengerInformation type="TRAVEL_DOCUMENTS" allPassengers="true">
                  <acceptableFormOfTravelDocument>PHOTOCARD</acceptableFormOfTravelDocument>
               </ns2:passengerInformation>
               <ns2:passengerInformation type="PASSENGER_NAME" allPassengers="true"/>
               <ns2:passengerInformation type="PASSENGER_TITLE" allPassengers="true"/>
            </ns2:passengerInformationRequired>
         </ns2:results>
      </ns2:travelPassShoppingResponse>
   </soap:Body>
</soap:Envelope>
```

❶ Because the search is more than 14 days in advance, SilverCore returns Carnets in accordance with industry rules.
❷ The fareClass element identifies the SilverRail code for the type of fare.  See [SilverCore content codes](/v1/docs/content-codes){target="_blank"} for a complete list of valid fare classes.  In this example, SilverCore returns `CARNET_SEASON`.
❸ For Season Tickets, a travel document is required to perform the booking as denoted by the `passengerInformation` element of type `TRAVEL_DOCUMENTS`.

**Example travelPassShoppingResponse for a Rangers and Rovers ticket** :
```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Body>
    <ns2:travelPassShoppingResponse conversationToken="UK-Seasons" xmlns:ns2="http://railgds.net/ws/shopping" xmlns="http://railgds.net/ws/commontypes">
      <requestStatus systemId="XknViM">
        <success>true</success>
      </requestStatus>
      <ns2:results>
        <ns2:passengers>
          <ns2:passenger passengerSpecID="PAX_SPEC_0">
            <age>40</age>
          </ns2:passenger>
        </ns2:passengers>
        <ns2:fareInformation>
          <ns2:prices>
            <ns2:travelPassPrice priceID="PRICE_P_1_0">
              <totalPrice currency="GBP">104.00</totalPrice>
              <ticketableFares>
                <ticketableFare>
                  <totalPrice currency="GBP">104.00</totalPrice>
                  <passengerReferences>
                    <passengerReference>
                      <passengerIDRef>PAX_SPEC_0</passengerIDRef>
                      <passengerTypeCode>A</passengerTypeCode>
                      <fareCodes>
                        <fareCode code="ATO-SW4-00000-STD-M">
                          <serviceClass>SECOND</serviceClass>
                          <cabinClass>Standard</cabinClass>
                          <rewardsEligible>NO</rewardsEligible>
❶                         <fareClass>ROVER</fareClass>
                          <fareDisplayName>EXPLORE WALES</fareDisplayName>
                        </fareCode>
                      </fareCodes>
                    </passengerReference>
                  </passengerReferences>
                  <prices>
                    <price type="TRAVEL_PASS" currency="GBP">104.00</price>
                  </prices>
                  <commissions>
                    <commission type="DISTRIBUTOR" currency="GBP">5.46</commission>
                  </commissions>
                  <rules>...</rules>
                  <ticketingOptionsAvailable TOD="false" ETK="false" PAH="false" DEPARTURE_STATION_TOD="false" MVD="true"/>
                  <fareOrigin type="STATION">GBWP4</fareOrigin>
                  <fareDestination type="STATION">GBWP4</fareDestination>
                  <fareStartDate>2023-07-27</fareStartDate>
                  <fareExpirationDate>2023-08-03</fareExpirationDate>
❷                 <fareDaysOfTravel>4</fareDaysOfTravel>
                </ticketableFare>
              </ticketableFares>
              <holdExpiration>2023-07-20T14:58:00Z</holdExpiration>
            </ns2:travelPassPrice>
          </ns2:prices>
        </ns2:fareInformation>
❸      <ns2:passengerInformationRequired>
          <ns2:passengerInformation type="PASSENGER_NAME" allPassengers="true"/>
          <ns2:passengerInformation type="PASSENGER_TITLE" allPassengers="true"/>
        </ns2:passengerInformationRequired>
      </ns2:results>
    </ns2:travelPassShoppingResponse>
  </soap:Body>
</soap:Envelope>
```

❶ The fareClass element identifies the SilverRail code for the type of fare.  See [SilverCore content codes](/v1/docs/content-codes){target="_blank"} for a complete list of valid fare classes.  For Rangers and Rovers, SilverCore returns `ROVER`.
❷ Specifically for Rangers and Rovers, SilverCore returns the `fareDaysOfTravel` element to indicate the total number of days the ticket is valid for.
❸ Note the absence of a `passengerInformation` element of the type `TRAVEL_DOCUMENTS`.  For Carnets and Rangers and Rovers, a travel document is not required for travel.

## Validating Travel Pass bookings

Before booking, Partners must validate the availability of a Travel Pass using the [validateBookingRecordInformationRequest](/v1/docs/validate-booking-record-overview){target="_blank"}.

Beyond confirming availability, the validate booking step verifies travel document requirements and the Ticket Delivery Options (TDO).  For more information about TDOs for Travel Passes, see [Determining Ticket Delivery Options](/v1/docs/work-with-travel-passes#determining-ticket-delivery-options).

### validateBookingRecordInformationRequest

To validate Season Tickets and Carnets, pass in the following data:

- `travelPointPair` container from your [travelPassShoppingRequest](/v1/docs/travelpassshoppingrequest-elements#code-item-shop-travelPointPair){target="_blank"}.
- `travelPassPrice` container for the chosen Travel Pass as returned in the [travelPassShoppingResponse](/v1/docs/travelpassshoppingresponse-elements#code-item-ns2-travelPassPrice){target="_blank"}.

To validate Rangers and Rovers tickets, pass in the following data:

- `travelPass` container from your [travelPassShoppingRequest](/v1/docs/travelpassshoppingrequest-elements#code-item-travelPass){target="_blank"}.
- `travelPassPrice` container for the chosen Ranger and Rover ticket as returned in the [travelPassShoppingResponse](/v1/docs/travelpassshoppingresponse-elements#code-item-ns2-travelPassPrice){target="_blank"}.

**Example validateBookingRecordInformationRequest for a Season Ticket** :
```xml
<soap:Envelope xmlns:com="http://railgds.net/ws/commontypes"               xmlns:book="http://railgds.net/ws/booking"               xmlns:shop="http://railgds.net/ws/shopping"               xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Header/>
  <soap:Body>
    <book:validateBookingRecordInformationRequest version="2.0">
      <com:context>
        <com:distributorCode>DEMO</com:distributorCode>
        <com:pointOfSaleCode>GB</com:pointOfSaleCode>
        <com:channelCode>WEB</com:channelCode>
      </com:context>
      <book:passengers>
        <book:passenger passengerID="PAX_SPEC_0">
          <com:title>Mr.</com:title>
          <com:nameFirst>India</com:nameFirst>
          <com:nameLast>Hughes</com:nameLast>
          <com:dob>1999-05-31</com:dob>
          <com:ageAtTimeOfTravel>40</com:ageAtTimeOfTravel>
        </book:passenger>
      </book:passengers>
      <book:travelPassQuery>
❶       <book:travelPointPair>
          <com:originTravelPoint type="STATION">GBMYB</com:originTravelPoint>
          <com:destinationTravelPoint type="STATION">GBBIT</com:destinationTravelPoint>
          <com:startDate>2023-07-31</com:startDate>
        </book:travelPointPair>
        <book:passPrices>
❷         <book:travelPassPrice priceID="PRICE_P_1_1">
            <com:totalPrice currency="GBP">569.10</com:totalPrice>
            <com:ticketableFares>
              <com:ticketableFare>
                <com:totalPrice currency="GBP">569.10</com:totalPrice>
                <com:passengerReferences>
                  <com:passengerReference>
                    <com:passengerIDRef>PAX_SPEC_0</com:passengerIDRef>
                    <com:passengerTypeCode>A</com:passengerTypeCode>
                    <com:fareCodes>
                      <com:fareCode code="NCH-PSS-00305-STD-1">
                        <com:serviceClass>THIRD</com:serviceClass>
                        <com:cabinClass>Standard</com:cabinClass>
                      </com:fareCode>
                    </com:fareCodes>
                  </com:passengerReference>
                </com:passengerReferences>
                <com:prices>
                  <com:price currency="GBP" type="TRAVEL_PASS">569.10</com:price>
                </com:prices>
                <com:fareOrigin type="STATION">GBLNT</com:fareOrigin>
                <com:fareDestination type="STATION">GBBBV</com:fareDestination>
              </com:ticketableFare>
            </com:ticketableFares>
            <com:holdExpiration>2023-07-24T15:11:06Z</com:holdExpiration>
          </book:travelPassPrice>
        </book:passPrices>
        <book:passIssueType>NEW</book:passIssueType>
      </book:travelPassQuery>
      <book:responseSpec>
        <book:returnReservationDetails>true</book:returnReservationDetails>
      </book:responseSpec>
    </book:validateBookingRecordInformationRequest>
  </soap:Body>
</soap:Envelope>
```

❶ Pass in the `travelPointPair` container from the original travelPassShoppingRequest.
❷ Pass in the `travelPassPrice` container for the chosen Travel Pass, as returned in the travelPassShoppingResponse.

**Example validateBookingRecordInformationRequest for a Ranger and Rover ticket**:
```XML
<soap:Envelope xmlns:com= "http://railgds.net/ws/commontypes"
               xmlns:book="http://railgds.net/ws/booking"
               xmlns:shop="http://railgds.net/ws/shopping"
               xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
   <soap:Header/>
   <soap:Body>
      <book:validateBookingRecordInformationRequest version="2.0">
         <com:context>
            <com:distributorCode>DEMO</com:distributorCode>
            <com:pointOfSaleCode>GB</com:pointOfSaleCode>
            <com:channelCode>WEB</com:channelCode>
         </com:context>
         <book:passengers>
            <book:passenger passengerID="PAX_SPEC_0">
               <com:title>Mr.</com:title>
               <com:nameFirst>Janis</com:nameFirst>
               <com:nameLast>Olson</com:nameLast>
               <com:dob>1999-05-31</com:dob>
               <com:ageAtTimeOfTravel>40</com:ageAtTimeOfTravel>
            </book:passenger>
         </book:passengers>
         <book:travelPassQuery>
❶           <book:travelPass>
              <com:code type="RANGERS_AND_ROVERS">SW4</com:code>
              <com:startDate>2023-07-29</com:startDate>
            </book:travelPass>
            <book:passPrices>
❷              <book:travelPassPrice priceID="PRICE_P_1_0">
                  <com:totalPrice currency="GBP">104.00</com:totalPrice>
                  <com:ticketableFares>
                     <com:ticketableFare>
                        <com:totalPrice currency="GBP">104.00</com:totalPrice>
                        <com:passengerReferences>
                           <com:passengerReference>
                              <com:passengerIDRef>PAX_SPEC_0</com:passengerIDRef>
                              <com:passengerTypeCode>A</com:passengerTypeCode>
                              <com:fareCodes>
                                 <com:fareCode code="ATO-SW4-00000-STD-M">
                                    <com:serviceClass>SECOND</com:serviceClass>
                                    <com:cabinClass>Standard</com:cabinClass>
                                 </com:fareCode>
                              </com:fareCodes>
                           </com:passengerReference>
                        </com:passengerReferences>
                        <com:prices>
                           <com:price currency="GBP" type="TRAVEL_PASS">104.00</com:price>
                        </com:prices>
                        <com:fareOrigin type="STATION">GBWP4</com:fareOrigin>
                        <com:fareDestination type="STATION">GBWP4</com:fareDestination>
                     </com:ticketableFare>
                  </com:ticketableFares>
                  <com:holdExpiration>2023-07-24T15:25:30Z</com:holdExpiration>
               </book:travelPassPrice>
            </book:passPrices>
            <book:passIssueType>NEW</book:passIssueType>
         </book:travelPassQuery>
         <book:responseSpec>
            <book:returnReservationDetails>true</book:returnReservationDetails>
         </book:responseSpec>
      </book:validateBookingRecordInformationRequest>
   </soap:Body>
</soap:Envelope>
```

❶ Pass in the `travelPass` container from the original travelPassShoppingRequest.
❷ Pass in the `travelPassPrice` container for the chosen Travel Pass, as returned in the travelPassShoppingResponse.

### validateBookingRecordInformationResponse

For Season Tickets, the requirement for travel documents does not change between the shopping and validation.  For this reason, SilverCore does not return the `<passengerInformationRequired>` container element in the validate response.  Partners will, therefore, need to refer to the travelPassShoppingResponse for travel document requirements.

For Carnets, SilverCore returns the   `<passengerInformationRequired>` container element but omits `passengerInformation type= "TRAVEL_DOCUMENTS"` because travel documents are not required for travel.

For Rangers and Rovers tickets, the `<passengerInformationRequired>` container element is not returned in the validate response because travel documents are not required for travel.

**Example validateBookingRecordResponse for a Season Ticket**:
```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
   <soap:Body>
      <ns2:validateBookingRecordInformationResponse xmlns:ns8="http://railgds.net/ws/extractbooking" xmlns:ns7="http://railgds.net/ws/vault" xmlns:ns6="http://railgds.net/ws/search" xmlns:ns5="http://railgds.net/ws/onaccount" xmlns:ns4="http://railgds.net/ws/keystore" xmlns:ns3="http://railgds.net/ws/shopping" xmlns:ns2="http://railgds.net/ws/booking" xmlns="http://railgds.net/ws/commontypes">
         <requestStatus systemId="zn6TmN">
            <success>true</success>
         </requestStatus>
         <ns2:bookingRecordInformation>
            <ns2:orderInformationSet>
               <ns2:orderInformation>
                  <ns2:travelSegments/>
❶                 <ns2:ticketingOptions>
                     <ns2:ticketingOption code="TBM" type="MAIL" destination="UK">
                        <description>Royal Mail First Class Post</description>
                        <fee isRefundable="false" currency="GBP">0.00</fee>
                     </ns2:ticketingOption>
                     <ns2:ticketingOption code="TBO" type="MAIL" destination="UK">
                        <description>Royal Mail Special Delivery Guaranteed™ by 1pm with £500 Insurance</description>
                        <fee isRefundable="false" currency="GBP">6.00</fee>
                     </ns2:ticketingOption>
                     <ns2:ticketingOption code="SCT" type="S_TICKET">
                        <description>Smart Card Ticket</description>
                        <fee isRefundable="false" currency="GBP">0.00</fee>
                     </ns2:ticketingOption>
                  </ns2:ticketingOptions>
                  <ns2:confirmationOptions>
                     <creditCardOption>...</creditCardOption>
                     <debitCardOption>
                        <types>
                           <type>MD</type>
                           <type>VD</type>
                        </types>
                        <sortCodeRequired>false</sortCodeRequired>
                     </debitCardOption>
                  </ns2:confirmationOptions>
                  <ns2:confirmationInformationRequired>true</ns2:confirmationInformationRequired>
                  <ns2:confirmationInformationRequiredOverrides>
                     <ticketingOptionCode>ETK</ticketingOptionCode>
                     <ticketingOptionCode>TBX</ticketingOptionCode>
                     <ticketingOptionCode>MVD</ticketingOptionCode>
                     <ticketingOptionCode>XVD</ticketingOptionCode>
                     <ticketingOptionCode>PAH</ticketingOptionCode>
                     <ticketingOptionCode>SCT</ticketingOptionCode>
                     <ticketingOptionCode>TBO</ticketingOptionCode>
                     <ticketingOptionCode>TBM</ticketingOptionCode>
                  </ns2:confirmationInformationRequiredOverrides>
                  <ns2:paymentCardRequiredForConfirmationInformation>false</ns2:paymentCardRequiredForConfirmationInformation>
                  <ns2:availableTicketLanguages>
                     <ns2:language>en</ns2:language>
                  </ns2:availableTicketLanguages>
                  <ns2:commercialAgentName>SilverRail Technologies UK Ltd - 3258</ns2:commercialAgentName>
               </ns2:orderInformation>
            </ns2:orderInformationSet>
            <ns2:paymentRequirements>
               <acceptableFormsOfPayment>
                  <formOfPayment type="OA">EP</formOfPayment>
               </acceptableFormsOfPayment>
               <confirmationUponAddPayment>false</confirmationUponAddPayment>
            </ns2:paymentRequirements>
            <ns2:serviceFeeAllowed>true</ns2:serviceFeeAllowed>
            <ns2:creditCardSurchargeFeeApplicable>false</ns2:creditCardSurchargeFeeApplicable>
         </ns2:bookingRecordInformation>
      </ns2:validateBookingRecordInformationResponse>
   </soap:Body>
</soap:Envelope>
```

❶ The `ticketingOptions` container returns the available Ticket Delivery Options for the chosen Season Ticket.

**Example validateBookingRecordInformationResponse for a Ranger and Rover ticket**:
```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
   <soap:Body>
      <ns2:validateBookingRecordInformationResponse conversationToken="UK-Seasons" xmlns:ns8="http://railgds.net/ws/extractbooking" xmlns:ns7="http://railgds.net/ws/vault" xmlns:ns6="http://railgds.net/ws/search" xmlns:ns5="http://railgds.net/ws/onaccount" xmlns:ns4="http://railgds.net/ws/keystore" xmlns:ns3="http://railgds.net/ws/shopping" xmlns:ns2="http://railgds.net/ws/booking" xmlns="http://railgds.net/ws/commontypes">
         <requestStatus systemId="7dnYam">
            <success>true</success>
         </requestStatus>
         <ns2:bookingRecordInformation>
            <ns2:orderInformationSet>
               <ns2:orderInformation>
                  <ns2:travelSegments/>
❶                 <ns2:ticketingOptions>
                     <ns2:ticketingOption code="MVD" type="E_TICKET">
                        <description>Mobile Ticket - XML Value Document</description>
                        <fee isRefundable="false" currency="GBP">0.00</fee>
                     </ns2:ticketingOption>
                  </ns2:ticketingOptions>
                  <ns2:confirmationOptions>
                     <creditCardOption>
                        <types>
                           <type>AX</type>
                           <type>CA</type>
                           <type>DI</type>
                           <type>VI</type>
                        </types>
                     </creditCardOption>
                     <debitCardOption>
                        <types>
                           <type>MD</type>
                           <type>VD</type>
                        </types>
                        <sortCodeRequired>false</sortCodeRequired>
                     </debitCardOption>
                  </ns2:confirmationOptions>
                  <ns2:confirmationInformationRequired>true</ns2:confirmationInformationRequired>
                  <ns2:confirmationInformationRequiredOverrides>
                     <ticketingOptionCode>ETK</ticketingOptionCode>
                     <ticketingOptionCode>TBX</ticketingOptionCode>
                     <ticketingOptionCode>MVD</ticketingOptionCode>
                     <ticketingOptionCode>XVD</ticketingOptionCode>
                     <ticketingOptionCode>PAH</ticketingOptionCode>
                     <ticketingOptionCode>SCT</ticketingOptionCode>
                     <ticketingOptionCode>TBO</ticketingOptionCode>
                     <ticketingOptionCode>TBM</ticketingOptionCode>
                  </ns2:confirmationInformationRequiredOverrides>
                  <ns2:paymentCardRequiredForConfirmationInformation>false</ns2:paymentCardRequiredForConfirmationInformation>
                  <ns2:availableTicketLanguages>
                     <ns2:language>en</ns2:language>
                  </ns2:availableTicketLanguages>
                  <ns2:commercialAgentName>TFW - 0393</ns2:commercialAgentName>
               </ns2:orderInformation>
            </ns2:orderInformationSet>
            <ns2:paymentRequirements>
               <acceptableFormsOfPayment>
                  <formOfPayment type="OA">EP</formOfPayment>
               </acceptableFormsOfPayment>
               <confirmationUponAddPayment>false</confirmationUponAddPayment>
            </ns2:paymentRequirements>
            <ns2:serviceFeeAllowed>true</ns2:serviceFeeAllowed>
            <ns2:creditCardSurchargeFeeApplicable>false</ns2:creditCardSurchargeFeeApplicable>
         </ns2:bookingRecordInformation>
      </ns2:validateBookingRecordInformationResponse>
   </soap:Body>
</soap:Envelope>
```

❶ Currently, the only available Ticket Delivery Option for Rangers and Rovers is `MVD`.

## Booking Travel Passes

Creating bookings for Travel Passes is done through the [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements){target="_blank"} and mirrors the flow for booking Point-To-Point fares.

:::(Info) (Bookings with multiple orders for Travel Passes are not supported.  SilverCore does not support bookings containing a mixture of Point-To-Point fares and Travel Passes.)
:::

### createBookingRecordRequest

To book Season Tickets, pass in the following data:

- `travelPointPair` container from your [travelPassShoppingRequest](/v1/docs/travelpassshoppingrequest-elements#code-item-shop-travelPointPair){target="_blank"}.
- `travelPassPrice` container for the chosen Travel Pass as returned in the [travelPassShoppingResponse](/v1/docs/travelpassshoppingresponse-elements#code-item-ns2-travelPassPrice){target="_blank"}.
- `travelDocuments` based on the travel documents required for travel as specified in the `passengerInformationRequired` container returned in the [travelPassShoppingResponse](/v1/docs/travelpassshoppingresponse-elements#code-item-passengerInformationRequired){target="_blank"}.

:::(Warning) (SilverCore will generate a [BWS00003](/v1/docs/status-messages#booking-service) booking service error when a booking request for a Season Ticket does not include the required travel documents.)
:::

To book Carnets, pass in the following data:

- `travelPointPair` container from your [travelPassShoppingRequest](/v1/docs/travelpassshoppingrequest-elements#code-item-shop-travelPointPair){target="_blank"}.
- `travelPassPrice` container for the chosen Travel Pass as returned in the [travelPassShoppingResponse](/v1/docs/travelpassshoppingresponse-elements#code-item-ns2-travelPassPrice){target="_blank"}.

:::(Warning) (SilverCore will generate a [BWS00040](/v1/docs/status-messages#booking-service) booking service error when a booking request for a Carnet includes the `travelDocuments` container.)
 :::

To book Rangers and Rovers tickets, pass in the following data:

- `travelPass` container from your [travelPassShoppingRequest](/v1/docs/travelpassshoppingrequest-elements#code-item-travelPass){target="_blank"}.
- `travelPassPrice` container for the chosen Ranger and Rover ticket as returned in the [travelPassShoppingResponse](/v1/docs/travelpassshoppingresponse-elements#code-item-ns2-travelPassPrice){target="_blank"}.

**Example createBookingRecordResponse for a Season Ticket** :
```xml
<soap:Envelope xmlns:com="http://railgds.net/ws/commontypes"               xmlns:book="http://railgds.net/ws/booking"               xmlns:shop="http://railgds.net/ws/shopping"               xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Header/>
  <soap:Body>
    <book:createBookingRecordRequest version="2.0">
      <com:context>
        <com:distributorCode>DEMO</com:distributorCode>
        <com:pointOfSaleCode>GB</com:pointOfSaleCode>
        <com:channelCode>WEB</com:channelCode>
      </com:context>
      <book:passengers>
        <book:passenger passengerID="PAX_SPEC_0">
          <com:title>Mr.</com:title>
          <com:nameFirst>Lily</com:nameFirst>
          <com:nameLast>Hayes</com:nameLast>
          <com:dob>1999-05-31</com:dob>
          <com:address>...</com:address>
          <com:contactInformation>...</com:contactInformation>
          <com:ageAtTimeOfTravel>40</com:ageAtTimeOfTravel>
❶        <com:travelDocuments>
            <com:travelDocument>
              <com:documentNumber>MOI1010</com:documentNumber>
              <com:documentType>PHOTOCARD</com:documentType>
              <com:expirationDate>2050-12-31</com:expirationDate>
              <com:issuingAuthority>
                <com:countryCode>GB</com:countryCode>
              </com:issuingAuthority>
            </com:travelDocument>
          </com:travelDocuments>
        </book:passenger>
      </book:passengers>
      <book:travelPassQuery>
❷       <book:travelPointPair>
          <com:originTravelPoint type="STATION">GBMYB</com:originTravelPoint>
          <com:destinationTravelPoint type="STATION">GBBIT</com:destinationTravelPoint>
          <com:startDate>2023-08-01</com:startDate>
        </book:travelPointPair>
        <book:passPrices>
❸          <book:travelPassPrice priceID="PRICE_P_1_1">
            <com:totalPrice currency="GBP">569.10</com:totalPrice>
            <com:ticketableFares>
              <com:ticketableFare>
                <com:totalPrice currency="GBP">569.10</com:totalPrice>
                <com:passengerReferences>
                  <com:passengerReference>
                    <com:passengerIDRef>PAX_SPEC_0</com:passengerIDRef>
                    <com:passengerTypeCode>A</com:passengerTypeCode>
                    <com:fareCodes>
                      <com:fareCode code="NCH-PSS-00305-STD-1">
                        <com:serviceClass>THIRD</com:serviceClass>
                        <com:cabinClass>Standard</com:cabinClass>
                      </com:fareCode>
                    </com:fareCodes>
                  </com:passengerReference>
                </com:passengerReferences>
                <com:prices>
                  <com:price currency="GBP" type="TRAVEL_PASS">569.10</com:price>
                </com:prices>
                <com:fareOrigin type="STATION">GBLNT</com:fareOrigin>
                <com:fareDestination type="STATION">GBBBV</com:fareDestination>
              </com:ticketableFare>
            </com:ticketableFares>
            <com:holdExpiration>2023-07-25T12:49:37Z</com:holdExpiration>
          </book:travelPassPrice>
        </book:passPrices>
        <book:passIssueType>NEW</book:passIssueType>
      </book:travelPassQuery>
      <book:responseSpec>
        <book:returnReservationDetails>true</book:returnReservationDetails>
      </book:responseSpec>
    </book:createBookingRecordRequest>
  </soap:Body>
</soap:Envelope>
```

❶ A travel document is required to book a Season ticket.  Refer to the `passengerInformationRequired` container element in the `travelPassShoppingResponse` to determine the required travel documents.
❷ Pass in the `travelPointPair` container from the original travelPassShoppingRequest.
❸ Pass in the `travelPassPrice` container for the chosen Travel Pass, as returned in the travelPassShoppingResponse.

**Example createBookingRecordRequest for a Ranger and Rover ticket** :
```xml
<soap:Envelope xmlns:com="http://railgds.net/ws/commontypes"               xmlns:book="http://railgds.net/ws/booking"               xmlns:shop="http://railgds.net/ws/shopping"               xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Header/>
  <soap:Body>
    <book:createBookingRecordRequest version="2.0">
      <com:context>
        <com:distributorCode>DEMO</com:distributorCode>
        <com:pointOfSaleCode>GB</com:pointOfSaleCode>
        <com:channelCode>WEB</com:channelCode>
      </com:context>
      <book:passengers>...</book:passengers>
      <book:travelPassQuery>
❶       <book:travelPass>
          <com:code type="RANGERS_AND_ROVERS">SW4</com:code>
          <com:startDate>2023-07-29</com:startDate>
        </book:travelPass>
        <book:passPrices>
❷         <book:travelPassPrice priceID="PRICE_P_1_0">
            <com:totalPrice currency="GBP">104.00</com:totalPrice>
            <com:ticketableFares>
              <com:ticketableFare>
                <com:totalPrice currency="GBP">104.00</com:totalPrice>
                <com:passengerReferences>
                  <com:passengerReference>
                    <com:passengerIDRef>PAX_SPEC_0</com:passengerIDRef>
                    <com:passengerTypeCode>A</com:passengerTypeCode>
                    <com:fareCodes>
                      <com:fareCode code="ATO-SW4-00000-STD-M">
                        <com:serviceClass>SECOND</com:serviceClass>
                        <com:cabinClass>Standard</com:cabinClass>
                      </com:fareCode>
                    </com:fareCodes>
                  </com:passengerReference>
                </com:passengerReferences>
                <com:prices>
                  <com:price currency="GBP" type="TRAVEL_PASS">104.00</com:price>
                </com:prices>
                <com:fareOrigin type="STATION">GBWP4</com:fareOrigin>
                <com:fareDestination type="STATION">GBWP4</com:fareDestination>
              </com:ticketableFare>
            </com:ticketableFares>
            <com:holdExpiration>2023-07-25T09:09:26Z</com:holdExpiration>
          </book:travelPassPrice>
        </book:passPrices>
        <book:passIssueType>NEW</book:passIssueType>
      </book:travelPassQuery>
      <book:responseSpec>
        <book:returnReservationDetails>true</book:returnReservationDetails>
      </book:responseSpec>
    </book:createBookingRecordRequest>
  </soap:Body>
</soap:Envelope>
```

❶ Pass in the `travelPass` container from the original travelPassShoppingRequest.
❷ Pass in the `travelPassPrice` container for the chosen Travel Pass, as returned in the travelPassShoppingResponse.

### createBookingRecordResponse

The [createBookingRecordResponse](/v1/docs/createbookingrecordresponse-elements){target="_blank"} shows the success/fail status for the booking, assigns a unique Booking Record locator Id, and indicates the hold date/time after which the booking will be canceled if not paid and confirmed.

If `<book:returnReservationDetails>true</book:returnReservationDetails>` is passed in the request, the response message will echo back a complete Booking Record containing passenger information and Ticketable Fares along with full payment status and financials.

**Example createBookingRecordResponse** :
```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
   <soap:Body>
      <ns2:createBookingRecordResponse conversationToken="UK-Seasons" xmlns:ns8="http://railgds.net/ws/extractbooking" xmlns:ns7="http://railgds.net/ws/vault" xmlns:ns6="http://railgds.net/ws/search" xmlns:ns5="http://railgds.net/ws/onaccount" xmlns:ns4="http://railgds.net/ws/keystore" xmlns:ns3="http://railgds.net/ws/shopping" xmlns:ns2="http://railgds.net/ws/booking" xmlns="http://railgds.net/ws/commontypes">
         <requestStatus systemId="7dnYam">
            <success>true</success>
         </requestStatus>
         <ns2:recordLocator>B-TFWALES-YKU0001XP</ns2:recordLocator>
         <ns2:lastHoldDateTime>2023-07-25T14:39:29Z</ns2:lastHoldDateTime>
❶        <ns2:bookingRecord recordLocator="B-TFWALES-YKU0001XP" status="DEBIT">
            <updateable>
               <passengerIdentity>true</passengerIdentity>
               <passengerAddress>true</passengerAddress>
               <passengerContactInfo>true</passengerContactInfo>
               <loyaltyCard>true</loyaltyCard>
               <travelDocument>true</travelDocument>
            </updateable>
            <bookingDate>2023-07-25Z</bookingDate>
            <numberOfOrders>1</numberOfOrders>
❷           <revenueTotal currency="GBP">104.00</revenueTotal>
            <receiptsTotal currency="GBP">0.00</receiptsTotal>
            ...
            <orders>
               <order orderID="O-TFWALES-YKU0001XP-AYS000001" status="BOOKED">
                  <dateBooked>2023-07-25Z</dateBooked>
                  <holdExpiration>2023-07-25T14:39:29Z</holdExpiration>
                  <refundEligibility eligible="true" cancelRequiredForRefund="true">
                     <penalty currency="GBP">0.00</penalty>
                  </refundEligibility>
                  ...
                  <ticketableFares>
                     <ticketableFare ticketableFareID="TF-YKU0001XP-AYS000001-0">
                        <totalPrice currency="GBP">104.00</totalPrice>
                        <passengerReferences>
                           <passengerReference>
                              <passengerIDRef>P-YKU0001XP-0</passengerIDRef>
                              <passengerTypeCode>A</passengerTypeCode>
                              <fareCodes>
                                 <fareCode code="ATO-SW4-00000-STD-M" fareCodeID="FC-TFWALES-YKU0001XP-AYS000001-1_0">
                                    <serviceClass>SECOND</serviceClass>
                                    <cabinClass>Standard</cabinClass>
                                    <fareClass>ROVER</fareClass>
                                    <fareDisplayName>EXPLORE WALES</fareDisplayName>
                                    <fareStartDateTime>2023-07-25T00:00:00Z</fareStartDateTime>
                                    <fareExpirationDateTime>2023-08-01T00:00:00Z</fareExpirationDateTime>
                                 </fareCode>
                              </fareCodes>
                           </passengerReference>
                        </passengerReferences>
                        <prices>
                           <price type="TRAVEL_PASS" currency="GBP">104.00</price>
                        </prices>
                        ...
                        <fareOrigin type="STATION">GBWP4</fareOrigin>
                        <fareDestination type="STATION">GBWP4</fareDestination>
                        <fareDaysOfTravel>4</fareDaysOfTravel>
                     </ticketableFare>
                  </ticketableFares>
                  <ticketingOptions>
                     <ticketingOption code="MVD" type="E_TICKET">
                        <description>Mobile Ticket - XML Value Document</description>
                        <fee isRefundable="false" currency="GBP">0.00</fee>
                     </ticketingOption>
                  </ticketingOptions>
                  ...
               </order>
            </orders>
            ...
             <financials>
               <prices>
                  <price priceID="FIN_PRICE_8276" type="Product_Sale" priceDescription="Product Sale:O-TFWALES-YKU0001XP-AYS000001">
                     <orderIDRef>O-TFWALES-YKU0001XP-AYS000001</orderIDRef>
                     <amount currency="GBP">104.00</amount>
                     <postingDate>2023-07-25Z</postingDate>
                     <postingTime>12:54:29Z</postingTime>
                  </price>
               </prices>
            </financials>
         </ns2:bookingRecord>
      </ns2:createBookingRecordResponse>
   </soap:Body>
</soap:Envelope>
```

❶ The unique Booking Record locator Id.
❷ The total revenue for all the orders currently on the booking (ticket prices, fees, etc.).

## Verifying the number of used passes for Carnets and Flexi-season

Once a booking has been made for a Carnet or Flexi-season ticket, SilverCore will return `<numberOfPasses>` and `<unusedPasses>` in the `<travelPasses>` container in the [retrieveBookingRecordResponse](/v1/docs/retrievebookingrecordresponse-elements#travelPasses){target="_blank"}.  Partners can use this information to allow passengers to track the usage of their passes.

With each `retrieveBookingRecordRequest`, SilverCore will query the number of used passes and update `<unusedPasses>` as necessary.

**Example where a passenger has yet to redeem a pass** :
```xml
<travelPasses>
  <travelPass fareCode="NCH-FL1-01000-STD-Z" type="CARNET">
    <numberOfPasses>8</numberOfPasses>
    <unusedPasses>8</unusedPasses>
  </travelPass>
</travelPasses>
```

Once all passes have been used, `<unusedPasses>` will return `0`.

**Example where the customer has redeemed all passes on the order** :
```xml
<travelPasses>
  <travelPass fareCode="NCH-FL1-01000-STD-Z" type="CARNET">
    <numberOfPasses>8</numberOfPasses>
    <unusedPasses>0</unusedPasses>
  </travelPass>
</travelPasses>
```

## Canceling a travel pass

Canceling a Travel Pass largely follows the process followed for Point-To-Point fares, with some important exceptions.

### Flow

![travel pass cancel flow.png](https://files.document360.io/c53b2e89-8de3-47ad-adf4-5a70ab761260/Images/Documentation/travel%20pass%20cancel%20flow.png){height="" width=""}

### Season tickets

The following rules apply to canceling Season tickets in SilverCore:

**Ticket fulfillment** :

- Season tickets fulfilled as TVM that have not been ticketed and are in status `CONFIRMED` can be canceled.
- Season tickets fulfilled as TVM or TBM that have been ticketed cannot be canceled through SilverCore and, therefore, must be canceled through our offline partner Fast Rail Ticketing.
- Season tickets fulfilled as E-Ticket (ETK) or Smartcard Ticket (SCT) can be canceled in SilverCore in either `CONFIRMED` or `TICKETED` status.
- SmartCard Ticket (SCT) can be canceled in SilverCore in status `CONFIRMED`.

**Ticket validity** :

- Seven-day Season tickets can only be canceled if more than three days of validity remain.
- Monthly or custom Season tickets can only be canceled if more than seven days of validity remain.
- Annual Season tickets can only be canceled if more than two months of validity remain.

### Carnets and Flexi-season tickets

The following rules apply to canceling Carnets and Flexi-season tickets in SilverCore:

**Ticket validity** :

- Carnets and Flexi-season tickets can be canceled depending on the number of used tickets/passes.

### Rangers and Rovers tickets

The following rules apply to canceling Rangers and Rovers tickets in SilverCore:

- Rangers and Rovers tickets can be canceled at any point up to the start date.

### Step 1 – Find out if a Travel Pass can be canceled

#### retrieveBookingRecordRequest

Before proceeding with the cancellation, Partners must first determine whether the Travel Pass can be canceled.  To do this, perform a [retrieveBookingRecordRequest](/v1/docs/retrieveexchangesummaryrequest-elements){target="_blank"} passing in the following data:

- `<recordLocator>` as returned in the [createBookingRecordResponse](/v1/docs/createbookingrecordresponse-elements){target="_blank"}.

**Example retrieveBookingRecordRequest**:
```xml
<soap:Envelope xmlns:com="http://railgds.net/ws/commontypes"               xmlns:book="http://railgds.net/ws/booking"               xmlns:shop="http://railgds.net/ws/shopping"               xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Header/>
  <soap:Body>
    <book:cancelBookingRecordRequest version="2.0">
      <com:context>
        <com:distributorCode>DEMO</com:distributorCode>
        <com:pointOfSaleCode>GB</com:pointOfSaleCode>
        <com:channelCode>WEB</com:channelCode>
      </com:context>
      <book:recordLocator>B-TFWALES-YKU0001XP</book:recordLocator>
      <book:expectedCancellationFee currency="GBP">0.00</book:expectedCancellationFee>
      <book:responseSpec>
        <book:returnReservationDetails>true</book:returnReservationDetails>
      </book:responseSpec>
    </book:cancelBookingRecordRequest>
  </soap:Body>
</soap:Envelope>
```

#### retrieveBookingRecordResponse

In the `retrieveBookingRecordResponse`, SilverCore returns a `cancellationSummary` container indicating whether cancellation is allowed and, if so, what penalties apply.

A Travel Pass is eligible for cancellation if SilverCore returns `isCancellable="true"` in the cancellationSummary container element:

**Example** :
```xml
<cancellationSummary isCancellable="true" isPartiallyCancellable="false">
```

**Example retrieveBookingRecordResponse (truncated)**:
```xml
<ns2:bookingRecord recordLocator="B-DEMO-GOO000334" status="BALANCED">
  <bookingDate>2022-07-13Z</bookingDate>
  <numberOfOrders>1</numberOfOrders>
❶ <revenueTotal currency="GBP">141.80</revenueTotal>
  <receiptsTotal currency="GBP">141.80</receiptsTotal>
  <passengers>...</passengers>
  <orders>
    <order orderID="O-DEMO-GOO000334-VUI000001" status="CONFIRMED">
      <dateBooked>2022-07-13Z</dateBooked>
      <holdExpiration>2022-07-22T15:24:32Z</holdExpiration>
      <fulfillmentInformation>
        <selectedTicketingOption code="SCT" type="S_TICKET">
          <description>Smart Card Ticket</description>
          <fee isRefundable="false" currency="GBP">0.00</fee>
        </selectedTicketingOption>
        <valueDocument status="READY">...</valueDocument>
        <pickupLocation type="STATION">GBBIT</pickupLocation>
      </fulfillmentInformation>
      <refundEligibility eligible="true" cancelRequiredForRefund="true">
        <penalty currency="GBP">0.00</penalty>
      </refundEligibility>
❷     <cancellationSummary isCancellable="true" isPartiallyCancellable="false">
        <cancellationOptions>
          <cancellationOption>
            <refundTarget type="FORM_OF_PAYMENT"/>
            <penalty>
❸            <total currency="GBP">0.00</total>
            </penalty>
          </cancellationOption>
        </cancellationOptions>
❹        <priceReversals>
          <total currency="GBP">72.40</total>
          <breakdown>
            <component type="Product_Reversible">
              <amount currency="GBP">72.40</amount>
            </component>
          </breakdown>
        </priceReversals>
      </cancellationSummary>
      <voidSummary isVoidable="true" untilWhen="2022-07-13T19:39:33Z" isVoidableException="true" untilWhenException="2023-07-13T15:39:33Z"/>
      <availableTicketLanguages>
        <language>en</language>
      </availableTicketLanguages>
      <travelSegments/>
      <ticketableFares>...</ticketableFares>
      <ticketingOptions>...</ticketingOptions>
      <supplyChannelPassengers>...</supplyChannelPassengers>
      <confirmationInformationRequired>true</confirmationInformationRequired>
      <paymentCardRequiredForConfirmationInformation>false</paymentCardRequiredForConfirmationInformation>
      <confirmationInformationRequiredOverrides>...</confirmationInformationRequiredOverrides>
      <commercialAgentName>SilverRail Technologies UK Ltd - 3258</commercialAgentName>
    </order>
  </orders>
  <paymentRequirements>...</paymentRequirements>
  <serviceFeeAllowed>true</serviceFeeAllowed>
  <creditCardSurchargeFeeApplicable>false</creditCardSurchargeFeeApplicable>
  <financials>
    <prices>...</prices>
    <payments>... </payments>
  </financials>
</ns2:bookingRecord>
```

❶ The revenue from the ticket sale and the payment receipt are balanced.
❷ The Travel Pass is currently cancellable.
❸ There is no penalty to be paid for the cancelation.
❹ The unused portion of the Travel Pass that can be refunded.  The refund amount is based on an RDG industry standard.

### Step 2 – Cancel the Travel Pass

#### cancelBookingRecordRequest

To cancel the Travel Pass, perform a [cancelBookingRecordRequest](/v1/docs/cancelbookingrecordrequest-elements){target="_blank"} and pass in the following data:

- [recordLocator](/v1/docs/retrievebookingrecordresponse-elements#code-item-ns2-bookingrecord){target="_blank"} from your retrieveBookingRecordResponse .
- [expectedCancellationFee](/v1/docs/cancelbookingrecordrequest-elements#code-item-book-expectedCancellationFee){target="_blank"}, which must match the penalty total returned in the [retrieveBookingRecordResponse](/v1/docs/retrievebookingrecordresponse-elements#code-item-total){target="_blank"}.

**Example request**:
```xml
<book:cancelBookingRecordRequest>
  <com:context>
    <com:distributorCode>DEMO</com:distributorCode>
    <com:pointOfSaleCode>GB</com:pointOfSaleCode>
    <com:channelCode>CON</com:channelCode>
  </com:context>
❶ <book:recordLocator>B-DEMO-LVY00037Q</book:recordLocator>
❷ <book:expectedCancellationFee currency="GBP">0.00</book:expectedCancellationFee>
  <book:responseSpec>
❸ <book:returnReservationDetails>true</book:returnReservationDetails>
  </book:responseSpec>
</book:cancelBookingRecordRequest>
</soap:Body>
</soap:Envelope>
```

❶ The record locator Id for the booking to cancel.
❷ No fees are expected to be charged for this cancellation.
❸ Set `<returnReservationDetails>` to `true` to return the entire booking record in your response.

#### cancelBookingRecordResponse

The resulting [cancelBookingRecordResponse](/v1/docs/cancelbookingrecordresponse-elements){target="_blank"} message shows the success/fail status for the cancellation and optionally echoes back a complete Booking Record containing passenger information, Travel Segments, and Ticketable Fares, along with full payment status and financials.  It also includes information about the status of any refunds.

**Example response** :
```xml
<ns2:bookingRecord recordLocator="B-DEMO-LVY00037Q" status="CREDIT">
  <bookingDate>2022-07-14Z</bookingDate>
  <numberOfOrders>1</numberOfOrders>
❶ <revenueTotal currency="GBP">69.40</revenueTotal>
  <receiptsTotal currency="GBP">141.80</receiptsTotal>
  <passengers>...</passengers>
  <orders>
❷     <order orderID="O-DEMO-LVY00037Q-DOE000001" status="CANCELED">
      <dateBooked>2022-07-14Z</dateBooked>
      <dateCancelled>2022-07-14T18:05:27Z</dateCancelled>
      <holdExpiration>2022-07-23T17:50:25Z</holdExpiration>
      <fulfillmentInformation>
        <selectedTicketingOption code="SCT" type="S_TICKET">
          <description>Smart Card Ticket</description>
          <fee isRefundable="false" currency="GBP">0.00</fee>
        </selectedTicketingOption>
        <valueDocument status="CANCELED">
          <valueDocumentLocator>DOE000001</valueDocumentLocator>
          <retrievalCustomer>Not Applicable</retrievalCustomer>
          <retrievalMaskedCreditCard>Not Applicable</retrievalMaskedCreditCard>
          <retrievalCode>XXXXXXXXX</retrievalCode>
          <selectedTicketLanguage>en</selectedTicketLanguage>
        </valueDocument>
        <pickupLocation type="STATION">GBBIT</pickupLocation>
      </fulfillmentInformation>
      <refundEligibility eligible= "false"/>
❸     <cancellationSummary isCancellable="false" isPartiallyCancellable="false"/>
      <voidSummary isVoidable="false" isVoidableException="false"/>
      <cancellationReason>FULL_TICKET_NOT_REQD</cancellationReason>
      <availableTicketLanguages>
        <language>en</language>
      </availableTicketLanguages>
      <isInventoryCanceled>true</isInventoryCanceled>
      <travelSegments/>
      <ticketableFares>...</ticketableFares>
      <ticketingOptions>...</ticketingOptions>
      <supplyChannelPassengers>...</supplyChannelPassengers>
      <confirmationInformationRequired>true</confirmationInformationRequired>
      <paymentCardRequiredForConfirmationInformation>false</paymentCardRequiredForConfirmationInformation>
      <confirmationInformationRequiredOverrides>...</confirmationInformationRequiredOverrides>
      <commercialAgentName>SilverRail Technologies UK Ltd - 3258</commercialAgentName>
    </order>
  </orders>
  <paymentRequirements>...</paymentRequirements>
  <serviceFeeAllowed>true</serviceFeeAllowed>
  <creditCardSurchargeFeeApplicable>false</creditCardSurchargeFeeApplicable>
  <financials>
    <prices>
      <price priceID="FIN_PRICE_16588" type="Product_Sale" priceDescription="Product Sale:O-DEMO-LVY00037Q-DOE000001">
        <orderIDRef>O-DEMO-LVY00037Q-DOE000001</orderIDRef>
        <amount currency="GBP">141.80</amount>
        <postingDate>2022-07-14Z</postingDate>
        <postingTime>18:05:25Z</postingTime>
      </price>
      <price priceID="FIN_PRICE_16589" type="Ticket_Delivery_Fee_Assessment" priceDescription="Ticket Delivery Fee:O-DEMO-LVY00037Q-DOE000001">
        <orderIDRef>O-DEMO-LVY00037Q-DOE000001</orderIDRef>
        <amount currency="GBP">0.00</amount>
        <postingDate>2022-07-14Z</postingDate>
        <postingTime>18:05:26Z</postingTime>
      </price>
      <price priceID="FIN_PRICE_16590" type="Product_Refund" priceDescription="Product Sale:O-DEMO-LVY00037Q-DOE000001">
        <orderIDRef>O-DEMO-LVY00037Q-DOE000001</orderIDRef>
        <amount currency="GBP">-141.80</amount>
        <postingDate>2022-07-14Z</postingDate>
        <postingTime>18:05:27Z</postingTime>
      </price>
      <price priceID="FIN_PRICE_16592" type="Cancelation_Penalty_Assessment" priceDescription="Cancellation Penalty:O-DEMO-LVY00037Q-DOE000001">
        <orderIDRef>O-DEMO-LVY00037Q-DOE000001</orderIDRef>
        <amount currency="GBP">0.00</amount>
        <postingDate>2022-07-14Z</postingDate>
        <postingTime>18:05:27Z</postingTime>
      </price>
❹      <price priceID="FIN_PRICE_16591" type="Used_Amount">
        <orderIDRef>O-DEMO-LVY00037Q-DOE000001</orderIDRef>
        <amount currency="GBP">69.40</amount>
        <postingDate>2022-07-14Z</postingDate>
        <postingTime>18:05:27Z</postingTime>
      </price>
    </prices>
    <payments>...</payment>
  </payments>
</financials>
</ns2:bookingRecord>
</ns2:cancelBookingRecordResponse>
</soap:Body>
</soap:Envelope>
```

❶ The revenue from the ticket sale now reflects the deduction in `<priceReversals>` returned in the `retrieveBookingRecordResponse`, which represents the unused portion of the Travel Pass.
❷ The status of the order is now `CANCELED`.
❸ The order is no longer cancellable.
❹ `used_amount` is logged as a financial type and represents the used portion of the Travel Pass.

### Order status

After successfully canceling a Travel Pass, and depending on the order status before the cancellation, the order will change to one of the values detailed in the following table:

| Order status before cancel | Original order status following cancel |
| :-- | :-- |
| BOOKED | RELEASED |
| CONFIRMED | CANCELED |
| TICKETED | WITHDRAWN |
