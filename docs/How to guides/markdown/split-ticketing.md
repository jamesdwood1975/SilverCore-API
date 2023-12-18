:::(Error) (In this article, as part of our series of How-Tos, we'll look at how to perform Split Ticketing in SilverCore.)
:::

## Intro

SilverCore supports Partners looking to deliver Split Ticketing options to passengers in the UK market.  Split Ticketing can save customers money by splitting a journey across two or more train tickets instead of a single ticket.

The following diagram shows how a journey from Manchester Piccadilly to Sheffield is split into two or more tickets.  Please note that the prices shown are purely representative and serve only to explain the concept of split ticketing.

![](RackMultipart20221128-1-c7ko71_html_bdd6a6f836edea60.png)

## Working with Split Ticketing in SilverCore

Please observe the following points when implementing Split Ticketing:

- All tickets in a booking order must be fulfilled using the same fulfillment method.  This restriction may lead to a lot of bookings defaulting to TOD only.
- SilverCore does not support Split Ticketing for Open Returns.
- While the cancellation of Split Tickets largely follows the process followed for Point-To-Point fares, there are some important exceptions.  Refer to [/v1/docs/work-with-split-ticketing#canceling-a-booking](/v1/docs/work-with-split-ticketing#canceling-a-booking){target="_blank"} for more information.

## Ancillary services

As with point-to-point fares, SilverCore will return available ancillary services, such as seat reservations, within the `<optionalPrices>` container element in the validateBookingRecordInformationResponse.  The desired services can then be passed in at the time of booking.

### Seat reservations

While there is no guarantee that the same seat will be available for all Split Tickets on each travel segment in a journey, SilverCore will attempt to allocate the same seat for the duration of each train segment to ensure that passengers are not required to change carriage or seat while still on the same train.

### Bike reservations

SilverCore currently does not support bike reservations for Split Ticket bookings.

## Split Ticketing flow in SilverCore

![](RackMultipart20221128-1-c7ko71_html_989395db54508da4.png)

## Shopping for Split Ticket fares

Shopping for fares for Split Tickets mirrors the typical Point-To-Point shopping flow, with the addition of several specific elements.

### Request

Split Ticketing is triggered by including the mandatory elements in the `<splitTicketing>` container in the pointToPointShoppingRequest.

**Example request for a one-way journey between Manchester Piccadilly and Sheffield**:

```XML
<soap:Envelope xmlns:com="http://railgds.net/ws/commontypes"
               xmlns:book="http://railgds.net/ws/booking"
               xmlns:shop="http://railgds.net/ws/shopping"
               xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
   <soap:Header/>
   <soap:Body>
      <shop:pointToPointShoppingRequest version="2.0">
         <com:context>
            <com:distributorCode>VIRGINTT</com:distributorCode>
            <com:pointOfSaleCode>GB</com:pointOfSaleCode>
            <com:channelCode>IOS</com:channelCode>
         </com:context>
         <shop:pointToPointShoppingQuery>
            <shop:travelPointPairs>
               <shop:travelPointPair>
                  <com:originTravelPoint type="STATION">GBQQM</com:originTravelPoint>
                  <com:destinationTravelPoint type="STATION">GBSHF</com:destinationTravelPoint>
                  <com:departureDateTimeWindow>
                     <com:date>2022-10-31</com:date>
                     <com:time>12:00:00</com:time>
                  </com:departureDateTimeWindow>
               </shop:travelPointPair>
            </shop:travelPointPairs>
            <shop:passengerSpecs>
               <shop:passengerSpec>
                  <com:age>40</com:age>
               </shop:passengerSpec>
            </shop:passengerSpecs>
  ❶         <shop:splitTicketing maxSplits="3" maxSummatedFares="3">
               <com:automaticSplitAtCallingStations/>
               <com:excludedFares>
  ❷               <com:ticketCode>SDR</com:ticketCode>
               </com:excludedFares>
               <com:summatedFareFilters>
  ❸             <com:summatedFareFilter fareCategory="All" fareClass="All"/>
               </com:summatedFareFilters>
            </shop:splitTicketing>
         </shop:pointToPointShoppingQuery>
      </shop:pointToPointShoppingRequest>
   </soap:Body>
</soap:Envelope>
```

❶ Return a maximum of three Summated Fares per Leg Solution and split the fare into a maximum of four tickets (three split points) in each direction.
❷ Ignore SDR Ticket Codes when identifying Split Tickets.  Please get in touch with SilverRail by raising a JIRA ticket before using this element in your request.
❸ Don't restrict the split ticket fares by class or category.

### Request elements

Refer to the following descriptions for the details of each request element and attribute related to Split Ticketing in SilverCore.

> pointToPointShoppingRequest
pointToPointShoppingQuery
**shop:splitTicketing**

A container element for split ticketing.  The `<splitTicketing>` element has the following attributes:

| Attribute | Description |
| :-- | :-- |
| `maxSummatedFares` | The maximum number of Summated Fares per `<summatedFareFilter>` for each Leg Solution.  E.g., If `maxSummatedFares=2` and there are two `summatedFareFilters` specified, then there could be up to four summatedFares per Leg Solution, two for each fare filter. |
| `maxSplits` | The maximum number of times a fare can be split.  For example, if `maxSplits=2`, then a Summated Fare for a Leg Solution will be split in no more than two places and consist of no more than three split fares. |

**Example**:

```XML
<shop:splitTicketing maxSummatedFares='1' maxSplits='1'/>
```
***
> pointToPointShoppingRequest
pointToPointShoppingQuery
splitTicketing
**com:automaticSplitAtInterchanges**

Consider interchange locations along the journey as a potential split location.

Cannot be used together with `<automaticSplitAtCallingStations>`.
***
> pointToPointShoppingRequest
pointToPointShoppingQuery
splitTicketing
**com:automaticSplitAtCallingStations**

Consider all calling stations along the journey, including interchanges, as a potential split location.

Cannot be used together with `<automaticSplitAtInterchanges>`.
***
> pointToPointShoppingRequest
pointToPointShoppingQuery
splitTicketing
**com:excludedFare (Optional)**

Please get in touch with SilverRail by raising a JIRA ticket before using this element in your request.
***
> pointToPointShoppingRequest
pointToPointShoppingQuery
splitTicketing
excludedFares
**com:ticketCode**

A three-character Fare Code to ignore when assessing split ticketing fares for the chosen journey.

**Example**:
```XML
<excludedFares>
  <ticketCode>SDS</TicketCode>
  <ticketCode>SDR</TicketCode>
</excludedFares>
```
***
> pointToPointShoppingRequest
pointToPointShoppingQuery
splitTicketing
**summatedFareFilters (Optional)**

A container element for one or more Fare Class and Fare Category filters.  Can be omitted if no filters or restrictions are required on the splitting.

**Example**:
```XML
<com:summatedFareFilters>
  <com:summatedFareFilter fareCategory="All" fareClass="All"/>
</com:summatedFareFilters>
```
***
> pointToPointShoppingRequest
pointToPointShoppingQuery
splitTicketing
summatedFareFilters
**com:summatedFareFilter**

SilverCore will search for split ticket fares for each `<summatedFareFilter>` in the request.  A fare is only returned once in the response, even if it is found by multiple fare filters.

The `<summatedFareFilter>` has the following attributes:

| Attribute | Description |
| :-- | :-- |
| `fareCategory` | The fare category of the split tickets returned in the response.  Possible values are:<br>- All<br>- WalkOn<br>- Advance |
| `fareClass` | The fare class of the split tickets returned in the response.  Possible values are:<br>- All<br>- Standard<br>- First |
| `onlySingleFares` | Exclude return fares.  False when omitted.  Possible values are:<br>- True<br>- False |

**Example**:
```XML
<com:summatedFareFilter fareClass='All' fareCategory='All' onlySingleFares=’true’>
```

### Response

In the pointToPointShoppingResponse, SilverCore will return the normal journey and fare results plus an additional container `<summatedFares>`, with a list of Summated Fares, if any have been found.

In each instance of `<summatedFare>`, SilverCore returns a set of Split Ticket fares evaluated for the chosen journey, including a reference to the Leg Solution to which the Split Fare applies.

Example response (truncated):
```XML
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Body>
    <ns2:pointToPointShoppingResponse xmlns:ns2="http://railgds.net/ws/shopping" xmlns="http://railgds.net/ws/commontypes">
      <requestStatus systemId="QiET+0">
        <success>true</success>
      </requestStatus>
      <ns2:results>
        <ns2:legs>
          <ns2:leg legID="L_1">
            <originTravelPoint type="STATION">GBQQM</originTravelPoint>
            <destinationTravelPoint type="STATION">GBSHF</destinationTravelPoint>
            <legSolutions>
❶             <legSolution legSolutionID="LS_1_0">
                <numberOfConnections>0</numberOfConnections>
                <serviceAlerts>
                  <serviceAlert>
                    <summary>This service may be busy due to temporary short-notice changes to the timetable across the North</summary>
                    <description/>
                  </serviceAlert>
                </serviceAlerts>
                <travelSegments>
                  <travelSegment sequence="0" travelSegmentID="LS_1_0_TS_0" type="TRAIN">
                    <originTravelPoint type="STATION">GBQQM</originTravelPoint>
                    <destinationTravelPoint type="STATION">GBSHF</destinationTravelPoint>
                    <departureDateTime>2022-10-31T11:49:00</departureDateTime>
                    <arrivalDateTime>2022-10-31T13:06:00</arrivalDateTime>
                    <designator>NT6155</designator>
                    <marketingCarrier>Northern</marketingCarrier>
                    <operatingCarrier>Northern</operatingCarrier>
                    <supplierEquipmentType>Northern-BLR</supplierEquipmentType>
                    <duration>P0Y0M0DT1H17M0S</duration>
                    <crossBorderInfo>false</crossBorderInfo>
                    <equipmentType code="BLR">Branch-Line/Regional</equipmentType>
                    <carbonData unit="kgCO2">2.48</carbonData>
                    <marketingServiceName>Northern service from Manchester Piccadilly to Sheffield</marketingServiceName>
                    <marketingInformation serviceCode="Northern"/>
                    <scheduleConfirmed>true</scheduleConfirmed>
                  </travelSegment>
                </travelSegments>
                <overtakenJourney>false</overtakenJourney>
                <duration>P0Y0M0DT1H17M0S</duration>
                <passengerInformationRequired>
                  <passengerInformation type="PASSENGER_NAME" allPassengers="false"/>
                </passengerInformationRequired>
                <availableSeatPreferences>...</availableSeatPreferences>
              </legSolution>
              <legSolution legSolutionID="LS_1_1">...</legSolution>
              <legSolution legSolutionID="LS_1_2">...</legSolution>
              <legSolution legSolutionID="LS_1_3">...</legSolution>
              <legSolution legSolutionID="LS_1_4">...</legSolution>
            </legSolutions>
          </ns2:leg>
        </ns2:legs>
        <ns2:passengers>
          <ns2:passenger passengerSpecID="PAX_SPEC_0">
            <age>40</age>
          </ns2:passenger>
        </ns2:passengers>
        <ns2:fareInformation>
          <ns2:prices>...</ns2:prices>
        </ns2:fareInformation>
❷       <ns2:summatedFares>
          <summatedFare summatedFareId="1">...</summatedFare>
          <summatedFare summatedFareId="2">...</summatedFare>
          <summatedFare summatedFareId="3">...</summatedFare>
          <summatedFare summatedFareId="4">...</summatedFare>
          <summatedFare summatedFareId="5">...</summatedFare>
          <summatedFare summatedFareId="6">...</summatedFare>
❸         <summatedFare summatedFareId="7">
            <totalPrice currency="GBP">17.60</totalPrice>
❹           <outwardFares>
              <fare>CDS|NTH|00000|B5|0438|2826</fare>
              <fare>SDS|NTH|01000||2826|6691</fare>
            </outwardFares>
            <outwardLegSolutions>
❺             <legSolutionIDRef>LS_1_0</legSolutionIDRef>
              <legSolutionIDRef>LS_1_3</legSolutionIDRef>
            </outwardLegSolutions>
❻           <summatedFareFilterIds>1</summatedFareFilterIds>
          </summatedFare>
        </ns2:summatedFares>
❼       <ns2:summatedFareFilters>
          <summatedFareFilter summatedFareFilterId="1" fareCategory="All" fareClass="All" onlySingleFares="false"/>
        </ns2:summatedFareFilters>
      </ns2:results>
    </ns2:pointToPointShoppingResponse>
  </soap:Body>
</soap:Envelope>
```

❶ One of the outbound Leg Solutions returned in the response, `<legSolutionID="LS_1_0">`.
❷ The `<summatedFares>` container element with all the assessed split fares.
❸ One of the `<summatedFare>` elements returned for the chosen journey with an Id and price.
❹ The outward split fare(s) assessed for the outward journey.  Note that the number of `<outwardFares>` does not exceed `<maxSplits>` +1.
❺ The outbound Leg Solutions assessed as being valid for the split fares.  Only one of these is passed into the `validateBookingRecordInformationRequest` and `createBookingRecordRequest`.
❻ If one or more `summatedFareFilters` have been applied, this indicates which `summatedFareFilter` this Summated Fare applies to.  For example, if two Summated Fare filters are requested, one for First class fares only and the second one for Standard class fares only, and this Summated Fare consists of only standard class fares, then the `<summatedFareFilterId>` would indicate the Standard class fare filter.
❼ A container element summarizing the fare filters passed in the shopping request.

## Validating a booking

Validating booking information allows you to preview the contents of the Booking Record before you create it.

### Request

When working with Split Tickets, pass in the desired Leg Solution(s) from `<legSolutions>` in your pointToPointShoppingResponse along with one of the desired and matching Split Tickets solutions, as defined in the `<summatedFare>` container.

For one-way journeys, pass in the `<summatedFare>` container with the desired `<outwardFares>` solution from the pointToPointShoppingResponse, with **one** `<legSolutionIdRef>` from `<outwardLegSolutions>`.

The following example shows a `<summatedFare>` container for our one-way journey from Manchester Piccadilly to Sheffield.  Note the reference back to a matching Leg Solution, `<com:legSolutionIDRef>LS_1_0</com:legSolutionIDRef>`.

**Example `<summatedFare>` container for a one-way journey**:

```XML
<com:summatedFare summatedFareId="7">
  <com:totalPrice currency="GBP">17.60</com:totalPrice>
  <com:outwardFares>
    <com:fare>CDS|NTH|00000|B5|0438|2826</com:fare>
    <com:fare>SDS|NTH|01000||2826|6691</com:fare>
  </com:outwardFares>
  <com:outwardLegSolutions>
    <com:legSolutionIDRef>LS_1_0</com:legSolutionIDRef>
  </com:outwardLegSolutions>
  <com:summatedFareFilterIds>1</com:summatedFareFilterIds>
</com:summatedFare>
```

For return journeys, pass in the `<summatedFare>` container with the desired `<outwardFares>` and `<returnFares>` solution from the pointToPointShoppingResponse, with **one** `<legSolutionIdRef>` from `<outwardLegSolutions>` and **one** `<legSolutionIdRef>` from `<returnLegSolutions>`.

**Example `<summatedFare>` container for a return journey**:
```XML
<com:summatedFare summatedFareId="1">
  <com:totalPrice currency="GBP">277.95</com:totalPrice>
  <com:outwardFares>
    <com:fare>SVR|IXC|00000|2V|0438|1268</com:fare>
    <com:fare>SVS|IWC|00000|2C|1268|1072</com:fare>
  </com:outwardFares>
  <com:returnFares>
    <com:fare>SOS|IWC|00000||1072|0418</com:fare>
    <com:fare>SDS|LBR|00000||0418|1268</com:fare>
    <com:fare>SVR|IXC|00000|2V|0438|1268</com:fare>
  </com:returnFares>
  <com:outwardLegSolutions>
    <com:legSolutionIDRef>LS_1_3</com:legSolutionIDRef>
  </com:outwardLegSolutions>
  <com:returnLegSolutions>
    <com:legSolutionIDRef>LS_2_9</com:legSolutionIDRef>
  </com:returnLegSolutions>
```


When you submit the validateBookingRecordInformationRequest, SilverCore performs a re-shop using the Leg Solution and Summated Fare data to ensure the tickets are still available and the pricing hasn't changed.

When working with Split Tickets, validating a booking also converts the Summated Fare into a pointToPointPrice, which is used in the create booking flow.

**Example request for a one-way journey between Manchester Piccadilly and Sheffield**:
```XML
<soap:Envelope xmlns:com="http://railgds.net/ws/commontypes"               xmlns:book="http://railgds.net/ws/booking"               xmlns:shop="http://railgds.net/ws/shopping"               xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Header/>
  <soap:Body>
    <book:validateBookingRecordInformationRequest version="2.0">
      <com:context>
        <com:distributorCode>VIRGINTT</com:distributorCode>
        <com:pointOfSaleCode>GB</com:pointOfSaleCode>
        <com:channelCode>IOS</com:channelCode>
      </com:context>
      <book:passengers>...</book:passengers>
      <book:legSolutions>
❶     <com:legSolution legSolutionID="LS_1_0">
          <com:travelSegments>
            <com:travelSegment travelSegmentID="LS_1_0_TS_0" type="TRAIN">
              <com:originTravelPoint type="STATION">GBQQM</com:originTravelPoint>
              <com:destinationTravelPoint type="STATION">GBSHF</com:destinationTravelPoint>
              <com:departureDateTime>2022-11-01T11:49:00</com:departureDateTime>
              <com:arrivalDateTime>2022-11-01T13:06:00</com:arrivalDateTime>
              <com:designator>NT6155</com:designator>
              <com:marketingCarrier>Northern</com:marketingCarrier>
              <com:equipmentType code="BLR">Branch-Line/Regional</com:equipmentType>
            </com:travelSegment>
          </com:travelSegments>
        </com:legSolution>
      </book:legSolutions>
      <book:summatedFares>
❷     <com:summatedFare summatedFareId="7">
          <com:totalPrice currency="GBP">17.60</com:totalPrice>
          <com:outwardFares>
            <com:fare>CDS|NTH|00000|B5|0438|2826</com:fare>
            <com:fare>SDS|NTH|01000||2826|6691</com:fare>
          </com:outwardFares>
          <com:outwardLegSolutions>
❸         <com:legSolutionIDRef>LS_1_0</com:legSolutionIDRef>
          </com:outwardLegSolutions>
          <com:summatedFareFilterIds>1</com:summatedFareFilterIds>
        </com:summatedFare>
      </book:summatedFares>
      <book:splitTicketing maxSummatedFares="3" maxSplits="3">
        <com:automaticSplitAtCallingStations/>
        <com:excludedFares>
          <com:ticketCode>SDR</com:ticketCode>
        </com:excludedFares>
        <com:summatedFareFilters>
          <com:summatedFareFilter fareClass="All" fareCategory="All"/>
        </com:summatedFareFilters>
      </book:splitTicketing>
      <book:responseSpec>
        <book:returnReservationDetails>true</book:returnReservationDetails>
❹   <book:returnPricingDetails>true</book:returnPricingDetails>
      </book:responseSpec>
    </book:validateBookingRecordInformationRequest>
  </soap:Body>
</soap:Envelope>
```

❶ The `<legSolution>` container passed in from the shopping response.
❷ One of the matching fares from `<summatedFares` passed in from the shopping response for the outward journey.
❸ A reference to the Leg Solution that the Summated Fare applies to, which must match the Id of the Leg Solution passed into the request.
❹ Set `<returnPricingDetails>` to `true` to ensure that your response message returns pricing details.

### Response

In the `validateBookingRecordInformationResponse`, SilverCore will return the total price for the chosen journey (for all the passengers on the booking), along with a `<ticketableFare>` element for each segment (or split) of the split journey.

If a Ticketable Fare covers only part of a Travel Segment, SilverCore will also return the `<subSegment>` element, providing the origin and destination of that sub-segment, the departure and arrival times at those origin and destination calling points, and a reference back to the Ticketable Fare.

If a fare covers a whole travel segment, the `<subSegment>` element is not returned.

**Example response (truncated)**:
```XML
<?xml version="1.0" encoding="UTF-8"?>
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Body>
    <ns2:validateBookingRecordInformationResponse xmlns:ns8="http://railgds.net/ws/extractbooking" xmlns:ns7="http://railgds.net/ws/vault" xmlns:ns6="http://railgds.net/ws/search" xmlns:ns5="http://railgds.net/ws/onaccount" xmlns:ns4="http://railgds.net/ws/keystore" xmlns:ns3="http://railgds.net/ws/shopping" xmlns:ns2="http://railgds.net/ws/booking" xmlns="http://railgds.net/ws/commontypes">
      <requestStatus systemId="BThwlt">
        <success>true</success>
      </requestStatus>
      <ns2:bookingRecordInformation>
        <ns2:orderInformationSet>
          <ns2:orderInformation>
            <ns2:passengers>...</ns2:passengers>
            <ns2:legSolutions>
              <legSolution legSolutionID="LS_1_0">
                <numberOfConnections>0</numberOfConnections>
                <serviceAlerts>
                  <serviceAlert>
                    <summary>This service may be busy due to temporary short notice changes to the timetable across the North</summary>
                    <description/>
                  </serviceAlert>
                </serviceAlerts>
                <travelSegments>
❶                 <travelSegment travelSegmentID="LS_1_0_TS_0" type="TRAIN">
                  <originTravelPoint type="STATION">GBQQM</originTravelPoint>
                  <destinationTravelPoint type="STATION">GBSHF</destinationTravelPoint>
                  <departureDateTime>2022-11-01T11:49:00</departureDateTime>
                  <arrivalDateTime>2022-11-01T13:06:00</arrivalDateTime>
                  <designator>NT6155</designator>
                  <marketingCarrier>Northern</marketingCarrier>
                  <operatingCarrier>Northern</operatingCarrier>
                  <supplierEquipmentType>Northern-BLR</supplierEquipmentType>
                  <duration>P0Y0M0DT1H17M0S</duration>
                  <crossBorderInfo>false</crossBorderInfo>
                  <equipmentType code="BLR">Branch-Line/Regional</equipmentType>
                  <carbonData unit="kgCO2">2.48</carbonData>
                  <marketingServiceName>Northern service from Manchester Piccadilly to Sheffield</marketingServiceName>
                  <marketingInformation serviceCode="Northern"/>
                  <scheduleConfirmed>true</scheduleConfirmed>
                </travelSegment>
              </travelSegments>
❷            <subSegments>
              <subSegment subSegmentID="LS_1_0_SS_0">
                <originTravelPoint type="STATION">GBQQM</originTravelPoint>
                <destinationTravelPoint type="STATION">GBHSG</destinationTravelPoint>
                <departureDateTime>2022-11-01T11:49:00</departureDateTime>
                <arrivalDateTime>2022-11-01T12:45:00</arrivalDateTime>
              </subSegment>
              <subSegment subSegmentID="LS_1_0_SS_1">
                <originTravelPoint type="STATION">GBHSG</originTravelPoint>
                <destinationTravelPoint type="STATION">GBSHF</destinationTravelPoint>
                <departureDateTime>2022-11-01T12:45:00</departureDateTime>
                <arrivalDateTime>2022-11-01T13:06:00</arrivalDateTime>
              </subSegment>
            </subSegments>
            <overtakenJourney>false</overtakenJourney>
            <duration>P0Y0M0DT1H17M0S</duration>
            <availableSeatPreferences>...</availableSeatPreferences>
          </legSolution>
        </ns2:legSolutions>
        <ns2:prices>
          <ns2:pointToPointPrice priceID="PRICE_P_1_0">
❸         <totalPrice currency="GBP">17.60</totalPrice>
          <restrictiveFareClass>OFFPEAK_DAY</restrictiveFareClass>
          <ticketableFares>
            <ticketableFare>
❹           <totalPrice currency="GBP">4.80</totalPrice>
            <passengerReferences>
              <passengerReference>
                <passengerIDRef>PAX_SPEC_0</passengerIDRef>
                <passengerTypeCode>A</passengerTypeCode>
                <fareCodes>
                  <fareCode code="NTH-SDS-01000-STD-1">
                  <serviceClass>THIRD</serviceClass>
                  <travelSegmentIDRef>LS_1_0_TS_0</travelSegmentIDRef>
❺                 <subSegmentIDRef>LS_1_0_SS_1</subSegmentIDRef>
                  <cabinClass>Standard</cabinClass>
                  <rewardsEligible>NO</rewardsEligible>
                  <fareClass>ANYTIME_DAY</fareClass>
                  <fareDisplayName>Anytime Day Single</fareDisplayName>
                  <openReturn>NO</openReturn>
                  <reservable>OPTIONAL</reservable>
                  <fareExpirationDateTime>2022-11-02T04:29:00Z</fareExpirationDateTime>
                  <fareApplicabilities>
                    <fareApplicability outbound="OPEN" type="SCHEDULE"/>
                  </fareApplicabilities>
                </fareCode>
              </fareCodes>
            </passengerReference>
          </passengerReferences>
          <prices>
            <price type="TICKET" currency="GBP">4.80</price>
          </prices>
          <optionalPrices>
❻          <optionalPrice optionalPriceID="PRICE_P_1_0_0_OP_1" category="SEAT_RESERVATION">
            <price type="RESERVATION" currency="GBP">0.00</price>
            <applicableTravelSegments minQuantity="1" maxQuantity="1">
              <travelSegmentIDRef>LS_1_0_TS_0</travelSegmentIDRef>
            </applicableTravelSegments>
            <rules>...</rules>
          </optionalPrice>
        </optionalPrices>
        <rules>...</rules>
        <ticketingOptionsAvailable TOD="true" ETK="true" PAH="false" PRN="true" EML="false" DEPARTURE_STATION_TOD="true" SMS="false" XVD="true" SCT="false" MVD="true"/>
❼       <fareOrigin type="STATION">GBHSG</fareOrigin>
        <fareDestination type="STATION">GBSHF</fareDestination>
        <outwardFareExpirationDate>2022-11-01</outwardFareExpirationDate>
        <expirationAdditionalTime>04:29</expirationAdditionalTime>
      </ticketableFare>
      <ticketableFare>
❽     <totalPrice currency="GBP">12.80</totalPrice>
      <passengerReferences>
        <passengerReference>
          <passengerIDRef>PAX_SPEC_0</passengerIDRef>
          <passengerTypeCode>A</passengerTypeCode>
          <fareCodes>
            <fareCode code="NTH-CDS-00000-STD-1">
              <serviceClass>THIRD</serviceClass>
              <travelSegmentIDRef>LS_1_0_TS_0</travelSegmentIDRef>
              <subSegmentIDRef>LS_1_0_SS_0</subSegmentIDRef>
              <cabinClass>Standard</cabinClass>
              <rewardsEligible>NO</rewardsEligible>
              <fareClass>OFFPEAK_DAY</fareClass>
              <fareDisplayName>Off-Peak Day Single</fareDisplayName>
              <openReturn>NO</openReturn>
              <reservable>OPTIONAL</reservable>
              <fareExpirationDateTime>2022-11-02T04:29:00Z</fareExpirationDateTime>
              <fareApplicabilities>
                <fareApplicability outbound="SEMI-OPEN" type="SCHEDULE"/>
              </fareApplicabilities>
            </fareCode>
          </fareCodes>
        </passengerReference>
      </passengerReferences>
      <prices>
        <price type="TICKET" currency="GBP">12.80</price>
      </prices>
      <optionalPrices>
        <optionalPrice optionalPriceID="PRICE_P_1_0_0_OP_0" category="SEAT_RESERVATION">
          <price type="RESERVATION" currency="GBP">0.00</price>
          <applicableTravelSegments minQuantity="1" maxQuantity="1">
            <travelSegmentIDRef>LS_1_0_TS_0</travelSegmentIDRef>
          </applicableTravelSegments>
          <rules>...</rules>
        </optionalPrice>
      </optionalPrices>
      <rules>...</rules>
❾     <ticketingOptionsAvailable TOD="true" ETK="true" PAH="false" PRN="true" EML="false" DEPARTURE_STATION_TOD="true" SMS="false" XVD="true" SCT="false" MVD="true"/>
❿      <fareOrigin type="STATION">GBMPV</fareOrigin>
      <fareDestination type="STATION">GBHSG</fareDestination>
      <outwardFareExpirationDate>2022-11-01</outwardFareExpirationDate>
      <expirationAdditionalTime>04:29</expirationAdditionalTime>
    </ticketableFare>
  </ticketableFares>
  <legReferences>
    <legSolutionIDRef>LS_1_0</legSolutionIDRef>
  </legReferences>
</ns2:pointToPointPrice>
</ns2:prices>
<ns2:ticketingOptions>
  <ns2:ticketingOption code="ETK" type="E_TICKET">
    <description>Electronic Ticket - PDF</description>
    <fee isRefundable="false" currency="GBP">0.00</fee>
  </ns2:ticketingOption>
  <ns2:ticketingOption code="TVM" type="ON_DEPARTURE" departureStation="true">
    <description>Collect at Ticket Vending Machine</description>
    <fee isRefundable="false" currency="GBP">0.00</fee>
  </ns2:ticketingOption>
  <ns2:ticketingOption code="XVD" type="E_TICKET">
    <description>Electronic Ticket - XML Value Document</description>
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
<ns2:confirmationInformationRequiredOverrides>...</ns2:confirmationInformationRequiredOverrides>
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
    <formOfPayment type="OA">NONE</formOfPayment>
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

❶ The `<travelSegment>` container details the chosen journey from Manchester Piccadilly to Sheffield.
❷ If the fare covers only a portion of the travel segment, the response will include a `<subSegment>` container element, which provides details of the part of the travel segment that the fare covers.
❸ The total price for the journey.
❹ The price for the leg from GBHSG to GBSHF.
❺ A reference to the sub segment that the Ticketable Fare applies to.
❻ A seat reservation is available for the portion of the journey from GBHSG to GBSHF.
❼ The origin and destination in the `<TicketableFare>` container is aligned with the origin and destination in the subsegment with the Id `LS_1_0_SS_1`.
❽ The price for the leg from GBMPV to GBHSG.
❾ The available ticketing options for this split fare.
❿ The origin and destination in the `<TicketableFare>` container is aligned with the origin and destination in the subsegment with the Id `LS_1_0_SS_0`.

## Creating a booking

Creating bookings for Split Ticket fares is done through the [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements) and mirrors the flow for booking Point-To-Point fares.

### Request

As with your validate request, when working with Split Tickets, pass in the desired Leg Solution(s) from `<legSolutions>` in your pointToPointShoppingResponse along with one of the desired and matching Split Ticket solutions, as returned in the `<summatedFare>` container.

For one-way journeys, pass in the `<summatedFare>` container with the desired `<outwardFares>` solution from the pointToPointShoppingResponse, with **one** `<legSolutionIdRef>` from `<outwardLegSolutions>`.

For return journeys, pass in the `<summatedFare>` container with the desired `<outwardFares>` and `<returnFares>` solution from the pointToPointShoppingResponse, with **one** `<legSolutionIdRef>` from `<outwardLegSolutions>` and **one** `<legSolutionIdRef>` from `<returnLegSolutions>`.

When you submit the createBookingRecordRequest message, SilverCore performs a re-shop using the Leg Solution, pointToPointPrice, and summatedFare data to ensure the tickets are still available and the pricing hasn't changed.

In the booking request, we pass in the `<prices>` container to confirm that the stated prices are still available and haven't changed.

**Example request for a one-way journey between Manchester Piccadilly and Sheffield**:

```XML
<soap:Envelope xmlns:com="http://railgds.net/ws/commontypes"               xmlns:book="http://railgds.net/ws/booking"               xmlns:shop="http://railgds.net/ws/shopping"               xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Header/>
  <soap:Body>
    <book:createBookingRecordRequest version="2.0">
      <com:context>
        <com:distributorCode>VIRGINTT</com:distributorCode>
        <com:pointOfSaleCode>GB</com:pointOfSaleCode>
        <com:channelCode>IOS</com:channelCode>
      </com:context>
      <book:passengers>...<book:legSolutions>
        <com:legSolution legSolutionID="LS_1_0">
          <com:travelSegments>
            <com:travelSegment travelSegmentID="LS_1_0_TS_0" type="TRAIN">
              <com:originTravelPoint type="STATION">GBQQM</com:originTravelPoint>
              <com:destinationTravelPoint type="STATION">GBSHF</com:destinationTravelPoint>
              <com:departureDateTime>2022-11-01T11:49:00</com:departureDateTime>
              <com:arrivalDateTime>2022-11-01T13:06:00</com:arrivalDateTime>
              <com:designator>NT6155</com:designator>
              <com:marketingCarrier>Northern</com:marketingCarrier>
              <com:equipmentType code="BLR">Branch-Line/Regional</com:equipmentType>
            </com:travelSegment>
          </com:travelSegments>
        </com:legSolution>
      </book:legSolutions>
❶ <book:prices>
        <book:pointToPointPrice priceID="PRICE_P_1_0">
          <com:totalPrice currency="GBP">17.60</com:totalPrice>
          <com:ticketableFares>
            <com:ticketableFare>
              <com:totalPrice currency="GBP">4.80</com:totalPrice>
              <com:passengerReferences>
                <com:passengerReference>
                  <com:passengerIDRef>PAX_SPEC_0</com:passengerIDRef>
                  <com:passengerTypeCode>A</com:passengerTypeCode>
                  <com:fareCodes>
                    <com:fareCode code="NTH-SDS-01000-STD-1">
                      <com:serviceClass>THIRD</com:serviceClass>
                      <com:travelSegmentIDRef>LS_1_0_TS_0</com:travelSegmentIDRef>
                      <com:cabinClass>Standard</com:cabinClass>
                    </com:fareCode>
                  </com:fareCodes>
                </com:passengerReference>
              </com:passengerReferences>
              <com:prices>
                <com:price currency="GBP" type="TICKET">4.80</com:price>
              </com:prices>
              <com:fareOrigin type="STATION">GBHSG</com:fareOrigin>
              <com:fareDestination type="STATION">GBSHF</com:fareDestination>
            </com:ticketableFare>
            <com:ticketableFare>
              <com:totalPrice currency="GBP">12.80</com:totalPrice>
              <com:passengerReferences>
                <com:passengerReference>
                  <com:passengerIDRef>PAX_SPEC_0</com:passengerIDRef>
                  <com:passengerTypeCode>A</com:passengerTypeCode>
                  <com:fareCodes>
                    <com:fareCode code="NTH-CDS-00000-STD-1">
                      <com:serviceClass>THIRD</com:serviceClass>
                      <com:travelSegmentIDRef>LS_1_0_TS_0</com:travelSegmentIDRef>
                      <com:cabinClass>Standard</com:cabinClass>
                    </com:fareCode>
                  </com:fareCodes>
                </com:passengerReference>
              </com:passengerReferences>
              <com:prices>
                <com:price currency="GBP" type="TICKET">12.80</com:price>
              </com:prices>
              <com:fareOrigin type="STATION">GBMPV</com:fareOrigin>
              <com:fareDestination type="STATION">GBHSG</com:fareDestination>
            </com:ticketableFare>
          </com:ticketableFares>
          <com:legReferences>
            <com:legSolutionIDRef>LS_1_0</com:legSolutionIDRef>
          </com:legReferences>
        </book:pointToPointPrice>
      </book:prices>
      <book:summatedFares>
        <com:summatedFare summatedFareId="7">
          <com:totalPrice currency="GBP">17.60</com:totalPrice>
          <com:outwardFares>
            <com:fare>CDS|NTH|00000|B5|0438|2826</com:fare>
            <com:fare>SDS|NTH|01000||2826|6691</com:fare>
          </com:outwardFares>
          <com:outwardLegSolutions>
            <com:legSolutionIDRef>LS_1_0</com:legSolutionIDRef>
          </com:outwardLegSolutions>
          <com:summatedFareFilterIds>1</com:summatedFareFilterIds>
        </com:summatedFare>
      </book:summatedFares>
      <book:splitTicketing maxSummatedFares="3" maxSplits="3">
        <com:automaticSplitAtCallingStations/>
        <com:excludedFares>
          <com:ticketCode>SDR</com:ticketCode>
        </com:excludedFares>
        <com:summatedFareFilters>
          <com:summatedFareFilter fareClass="All" fareCategory="All"/>
        </com:summatedFareFilters>
      </book:splitTicketing>
      <book:responseSpec>
        <book:returnReservationDetails>true</book:returnReservationDetails>
        <book:returnPricingDetails>true</book:returnPricingDetails>
      </book:responseSpec>
    </book:createBookingRecordRequest>
  </soap:Body>
</soap:Envelope>
```

❶ Pass in the `<prices>` container to verify the availability of the stated ticket price.

### Response

The resulting response message shows the success/fail status for the booking, assigns a unique Booking Record locator Id, and indicates the hold date/time after which the booking will be canceled if not paid and confirmed.

If `<book:returnReservationDetails>true</book:returnReservationDetails>` is passed in the request, the response message will echo back a complete Booking Record containing passenger information, Travel Segments, and Ticketable Fares along with full payment status and financials.

**Example response (truncated)**:

```XML
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
<soap:Body>
  <ns2:createBookingRecordResponse xmlns:ns8="http://railgds.net/ws/extractbooking" xmlns:ns7="http://railgds.net/ws/vault" xmlns:ns6="http://railgds.net/ws/search" xmlns:ns5="http://railgds.net/ws/onaccount" xmlns:ns4="http://railgds.net/ws/keystore" xmlns:ns3="http://railgds.net/ws/shopping" xmlns:ns2="http://railgds.net/ws/booking" xmlns="http://railgds.net/ws/commontypes">
    <requestStatus systemId="BThwlt">
      <success>true</success>
    </requestStatus>
    <ns2:recordLocator>B-VIRGINTT-BAN0001X5</ns2:recordLocator>
    <ns2:lastHoldDateTime>2022-10-23T12:23:44Z</ns2:lastHoldDateTime>
❶   <ns2:bookingRecord recordLocator="B-VIRGINTT-BAN0001X5" status="DEBIT">
      <updateable>
        <passengerIdentity>true</passengerIdentity>
        <passengerAddress>true</passengerAddress>
        <passengerContactInfo>true</passengerContactInfo>
        <loyaltyCard>true</loyaltyCard>
        <travelDocument>true</travelDocument>
      </updateable>
      <bookingDate>2022-10-23Z</bookingDate>
      <departureDate>2022-11-01</departureDate>
      <numberOfOrders>1</numberOfOrders>
❷     <revenueTotal currency="GBP">17.60</revenueTotal>
      <receiptsTotal currency="GBP">0.00</receiptsTotal>
      <passengers>...</passengers>
      <orders>
        <order orderID="O-VIRGINTT-BAN0001X5-FXH000001" status="BOOKED">
          <dateBooked>2022-10-23Z</dateBooked>
          <serviceAlerts>
            <serviceAlert>
              <summary>This service may be busy due to temporary short notice changes to the timetable across the North</summary>
              <description/>
            </serviceAlert>
          </serviceAlerts>
          <holdExpiration>2022-10-23T12:23:44Z</holdExpiration>
          <refundEligibility eligible="true" cancelRequiredForRefund="true">
            <penalty currency="GBP">0.00</penalty>
          </refundEligibility>
          <cancellationSummary isCancellable="true" isPartiallyCancellable="true">
            <cancellationOptions>
              <cancellationOption>
                <refundTarget type="FORM_OF_PAYMENT"/>
                <penalty>
                  <total currency="GBP">0.00</total>
                </penalty>
              </cancellationOption>
            </cancellationOptions>
            <priceReversals>
              <total currency="GBP">17.60</total>
              <breakdown>
                <component type="Product_Sale">
                  <amount currency="GBP">17.60</amount>
                </component>
              </breakdown>
            </priceReversals>
          </cancellationSummary>
          <availableTicketLanguages>
            <language>en</language>
          </availableTicketLanguages>
          <updateable>
            <ticketOption>true</ticketOption>
            <supplierNote>true</supplierNote>
          </updateable>
          <travelSegments>...</travelSegments>
❸         <subSegments>
            <subSegment subSegmentID="SS-BAN0001X5-FXH000001-1">
              <originTravelPoint type="STATION">GBQQM</originTravelPoint>
              <destinationTravelPoint type="STATION">GBHSG</destinationTravelPoint>
              <departureDateTime>2022-11-01T11:49:00</departureDateTime>
              <arrivalDateTime>2022-11-01T12:45:00</arrivalDateTime>
            </subSegment>
            <subSegment subSegmentID="SS-BAN0001X5-FXH000001-2">
              <originTravelPoint type="STATION">GBHSG</originTravelPoint>
              <destinationTravelPoint type="STATION">GBSHF</destinationTravelPoint>
              <departureDateTime>2022-11-01T12:45:00</departureDateTime>
              <arrivalDateTime>2022-11-01T13:06:00</arrivalDateTime>
            </subSegment>
          </subSegments>
          <ticketableFares>...</ticketableFares>
          <ticketingOptions>...</ticketingOptions>
          <supplyChannelPassengers>
            <supplyChannelPassengerReference>
              <passengerIDRef>P-BAN0001X5-0</passengerIDRef>
              <nameFirst>Neveah</nameFirst>
              <nameLast>Molina</nameLast>
            </supplyChannelPassengerReference>
          </supplyChannelPassengers>
          <confirmationInformationRequired>false</confirmationInformationRequired>
          <paymentCardRequiredForConfirmationInformation>false</paymentCardRequiredForConfirmationInformation>
          <confirmationOptions>...</confirmationOptions>
          <commercialAgentName>SilverRail Technologies UK Ltd - 3258</commercialAgentName>
❹      <combinedTicketableFareSets>
            <combinedTicketableFareSet combinedTicketableFareSetId="CTF_0">
              <combinedTicketableFareLocators>
                <combinedTicketableFareLocator>TF-DXT0004OY-VJP000001-0</combinedTicketableFareLocator>
                <combinedTicketableFareLocator>TF-DXT0004OY-VJP000001-1</combinedTicketableFareLocator>
              </combinedTicketableFareLocators>
            </combinedTicketableFareSet>
          </combinedTicketableFareSets>
        </order>
      </orders>
❺    <paymentRequirements>
      <paymentDue>2022-10-23T12:23:44Z</paymentDue>
      <paymentInformation>ORDER_TOTAL</paymentInformation>
      <nextDepositDue>2022-10-23T12:23:44Z</nextDepositDue>
      <minimumDepositAmount currency="GBP">17.60</minimumDepositAmount>
      <acceptableFormsOfPayment>
        <formOfPayment type="OA">EP</formOfPayment>
        <formOfPayment type="OA">NONE</formOfPayment>
      </acceptableFormsOfPayment>
      <refundIsProcessedOnCancel>false</refundIsProcessedOnCancel>
      <confirmationUponAddPayment>false</confirmationUponAddPayment>
    </paymentRequirements>
    <serviceFeeAllowed>true</serviceFeeAllowed>
    <creditCardSurchargeFeeApplicable>false</creditCardSurchargeFeeApplicable>
    <financials>
      <prices>
        <price priceID="FIN_PRICE_9587" type="Product_Sale" priceDescription="Product Sale:O-VIRGINTT-BAN0001X5-FXH000001">
          <orderIDRef>O-VIRGINTT-BAN0001X5-FXH000001</orderIDRef>
          <amount currency="GBP">17.60</amount>
          <postingDate>2022-10-23Z</postingDate>
          <postingTime>10:38:44Z</postingTime>
        </price>
      </prices>
    </financials>
  </ns2:bookingRecord>
</ns2:createBookingRecordResponse>
</soap:Body>
</soap:Envelope>
```

❶ The unique Booking Record locator Id.
❷The total price for the journey.  Note that the difference between `<revenueTotal>` and `<receiptsTotal>`.  When a payment is made for the booking, the financials will be balanced.
❸ If the fare covers only a portion of the travel segment, the response will include a `<subSegment>` container element, which provides details of the part of the travel segment that the fare covers.
❹ The `<combinedTicketableFareSets>` container indicates which Ticketable Fares are classified as split tickets.  The container is returned in the create booking response and subsequent booking retrievals.  A set is returned for each direction but only if it consists of split fares.
❺ In the `<paymentDue>` element in the `<financials>` container, the response indicates the hold date/time after which the booking will be canceled if not paid and confirmed.

## Canceling a booking

Canceling Split Tickets in SilverCore largely follows the process followed for Point-To-Point fares, with some important exceptions:

- To prevent fraud, canceling individual Split Tickets on an order is not supported.  All Ticketable Fares for a given Leg Solution must be canceled together or not at all.
- Split Tickets with Advance fares cannot be canceled in SilverCore.
- Orders with a mixture of Advance and Anytime fares cannot be canceled in SilverCore.

:::(Warning) (Exchange is not supported for Split Tickets.)
:::

SilverCore will return a `BK00186` booking error message when an attempt is made to cancel individual Ticketable Fares on a Split Ticket booking.

**Example booking error message**:

```XML
<statusMessages>
  <statusMessage>
    <message>Must specify all ticketable fares for all canceling travel segments</message>
    <code>BK00186</code>
  </statusMessage>
</statusMessages>
```

## Request

An order is eligible for cancellation if `isCancellable= "true"` is returned in the Booking Record `<cancellationSummary>` container element.

The Supply Channel's cancellation fee must also match the expected cancellation fee you provide in the cancelBookingRecordRequest message.

If the order is eligible for cancellation and the fee matches, SilverCore cancels the order, frees up the inventory in the Supplier system, reverses the fare revenues (if allowed), and adds a revenue item for the cancellation fee.

**Example request**:

```XML
<soap:Envelope xmlns:com="http://railgds.net/ws/commontypes"               xmlns:book="http://railgds.net/ws/booking"               xmlns:shop="http://railgds.net/ws/shopping"               xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Header/>
  <soap:Body>
    <book:cancelBookingRecordRequest version="2.0">
      <com:context>
        <com:distributorCode>VIRGINTT</com:distributorCode>
        <com:pointOfSaleCode>GB</com:pointOfSaleCode>
        <com:channelCode>IOS</com:channelCode>
      </com:context>
❶      <book:recordLocator>B-VIRGINTT-BAN0001X5</book:recordLocator>
      <book:expectedCancellationFee currency="GBP">0.00</book:expectedCancellationFee>
      <book:responseSpec>
        <book:returnReservationDetails>true</book:returnReservationDetails>
      </book:responseSpec>
    </book:cancelBookingRecordRequest>
  </soap:Body>
</soap:Envelope>
```

❶ The record locator Id for the order to cancel.

### Response

The resulting response message shows the success/fail status for the cancellation and optionally echoes back a complete Booking Record containing passenger information, Travel Segments, and Ticketable Fares, along with full payment status and financials.  It also includes information about the status of any refunds.

**Example response (truncated)**:

```XML
<?xml version="1.0" encoding="UTF-8"?>
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Body>
    <ns2:cancelBookingRecordResponse xmlns:ns2="http://railgds.net/ws/booking" xmlns="http://railgds.net/ws/commontypes">
      <requestStatus systemId="9C6JGk">
        <success>true</success>
      </requestStatus>
      <ns2:bookingRecord recordLocator="B-ATOC-OYF000010" status="CREDIT">
        <bookingDate>2022-10-25Z</bookingDate>
        <departureDate>2022-11-03</departureDate>
        <numberOfOrders>1</numberOfOrders>
❶       <revenueTotal currency="GBP">0.00</revenueTotal>
        <receiptsTotal currency="GBP">17.60</receiptsTotal>
        <passengers>...</passengers>
        <orders>
❷          <order orderID="O-ATOC-OYF000010-TXX000001" status="CANCELED">
            <dateBooked>2022-10-25Z</dateBooked>
            <dateCancelled>2022-10-25T15:06:18Z</dateCancelled>
            <holdExpiration>2022-10-25T16:49:56Z</holdExpiration>
            <fulfillmentInformation>...</fulfillmentInformation>
            <refundEligibility eligible="false"/>
❸           <cancellationSummary isCancellable="false" isPartiallyCancellable="false"/>
            <cancellationReason>FULL_TICKET_NOT_REQD</cancellationReason>
            <availableTicketLanguages>
              <language>en</language>
            </availableTicketLanguages>
            <isInventoryCanceled>true</isInventoryCanceled>
            <travelSegments>...</travelSegments>
            <subSegments>...</subSegments>
            <ticketableFares>
              <ticketableFare ticketableFareID="TF-OYF000010-TXX000001-1">
                <totalPrice currency="GBP">4.80</totalPrice>
                <passengerReferences>
                  <passengerReference>
                    <passengerIDRef>P-OYF000010-0</passengerIDRef>
                    <passengerTypeCode>A</passengerTypeCode>
                    <fareCodes>...</fareCodes>
                  </passengerReference>
                </passengerReferences>
                <prices>
                  <price type="TICKET" currency="GBP">4.80</price>
                </prices>
                <commissions>
                  <commission type="DISTRIBUTOR" currency="GBP">0.00</commission>
                </commissions>
                <rules>...</rules>
                <fareOrigin type="STATION">GBHSG</fareOrigin>
                <fareDestination type="STATION">GBSHF</fareDestination>
                <outwardFareExpirationDate>2022-11-03</outwardFareExpirationDate>
                <expirationAdditionalTime>04:29</expirationAdditionalTime>
              </ticketableFare>
              <ticketableFare ticketableFareID="TF-OYF000010-TXX000001-0">
                <totalPrice currency="GBP">12.80</totalPrice>
                <passengerReferences>...</passengerReferences>
                <prices>
                  <price type="TICKET" currency="GBP">12.80</price>
                </prices>
                <commissions>
                  <commission type="DISTRIBUTOR" currency="GBP">0.00</commission>
                </commissions>
                <rules>...</rules>
                <fareOrigin type="STATION">GBMPV</fareOrigin>
                <fareDestination type="STATION">GBHSG</fareDestination>
                <outwardFareExpirationDate>2022-11-03</outwardFareExpirationDate>
                <expirationAdditionalTime>04:29</expirationAdditionalTime>
              </ticketableFare>
            </ticketableFares>
            <ticketingOptions>...</ticketingOptions>
            <supplyChannelPassengers>
              <supplyChannelPassengerReference>
                <passengerIDRef>P-OYF000010-0</passengerIDRef>
                <nameFirst>Minerva</nameFirst>
                <nameLast>Richards</nameLast>
              </supplyChannelPassengerReference>
            </supplyChannelPassengers>
            <confirmationInformationRequired>false</confirmationInformationRequired>
            <paymentCardRequiredForConfirmationInformation>false</paymentCardRequiredForConfirmationInformation>
            <commercialAgentName>1427</commercialAgentName>
          </order>
        </orders>
        <paymentRequirements>...</paymentRequirements>
        <serviceFeeAllowed>true</serviceFeeAllowed>
        <creditCardSurchargeFeeApplicable>false</creditCardSurchargeFeeApplicable>
        <financials>
          <prices>
            <price priceID="FIN_PRICE_105" type="Product_Sale" priceDescription="Product Sale:O-ATOC-OYF000010-TXX000001">
              <orderIDRef>O-ATOC-OYF000010-TXX000001</orderIDRef>
              <amount currency="GBP">17.60</amount>
              <postingDate>2022-10-25Z</postingDate>
              <postingTime>15:04:56Z</postingTime>
            </price>
            <price priceID="FIN_PRICE_106" type="Ticket_Delivery_Fee_Assessment" priceDescription="Ticket Delivery Fee:O-ATOC-OYF000010-TXX000001">
              <orderIDRef>O-ATOC-OYF000010-TXX000001</orderIDRef>
              <amount currency="GBP">0.00</amount>
              <postingDate>2022-10-25Z</postingDate>
              <postingTime>15:05:55Z</postingTime>
            </price>
            <price priceID="FIN_PRICE_107" type="Product_Refund" priceDescription="Product Sale:O-ATOC-OYF000010-TXX000001">
              <orderIDRef>O-ATOC-OYF000010-TXX000001</orderIDRef>
              <amount currency="GBP">-17.60</amount>
              <postingDate>2022-10-25Z</postingDate>
              <postingTime>15:06:18Z</postingTime>
            </price>
            <price priceID="FIN_PRICE_108" type="Cancelation_Penalty_Assessment" priceDescription="Cancellation Penalty:O-ATOC-OYF000010-TXX000001">
              <orderIDRef>O-ATOC-OYF000010-TXX000001</orderIDRef>
              <amount currency="GBP">0.00</amount>
              <postingDate>2022-10-25Z</postingDate>
              <postingTime>15:06:18Z</postingTime>
            </price>
          </prices>
          <payments>...</payments>
        </financials>
      </ns2:bookingRecord>
    </ns2:cancelBookingRecordResponse>
  </soap:Body>
</soap:Envelope>
```

❶ The cancellation reverses the financials for the order.
❷ The order status is updated to `CANCELLED`.
❸ The `<cancellationSummary>` element shows that the order is no longer cancellable.
