:::(Error) (**In this article**, we will summarize the areas of SilverCore functionality where the Rail Delivery Group (RDG) has specific requirements or restrictions above and beyond what is common to all Suppliers.)
:::

## Available SilverCore API messages
The following SilverCore API messages are **not** available for RDG:

-  addOrderToBookingRecordRequest
-  authenticatePayerRequest&#42;&#185;
-  deletePaymentTokenRequest

:::(Info) (&#42;&#185; Available in certain circumstances when SilverRail is the merchant of record.)
:::

## Accreditation
For an overview of the requirements for a UK delegated accreditation and the tests that SilverRail performs as part of the accreditation process, please see our [Accreditation overview](https://files.document360.io/c53b2e89-8de3-47ad-adf4-5a70ab761260/Images/Documentation/Accreditation%20Overview.pdf){target="_blank"}.

For information about the accreditation requirements for each Supply Channel, please see [Functional requirements summary](/v1/docs/accreditation-requirements-summary){target="_blank"}.  See [RDG sample implementation tests](/v1/docs/rdg-accreditation-tests){target="_blank"} for details of the tests we perform when evaluating a Partner's implementation for the RDG supply channel.

## Station codes
A full list of RDG station data codes is available through our [Published Data API](/v1/docs/published-data-messages#station){target="_blank"}, using `ATOC` as the selection parameter.

**Example**:
```XML
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:pub="http://railgds.net/ws/publishing">
<soapenv:Header/>
 <soapenv:Body>
  <pub:dataRequest type="station">
   <pub:parameters>
    <pub:selection>ATOC</pub:selection>
   </pub:parameters>
  </pub:dataRequest>
 </soapenv:Body>
</soapenv:Envelope>
```

The station file structure for RDG stations is the same as all other suppliers.

**Example**:
```XML
 <pub:station code="GB#BX" countryCode="GB" fareStation="OUTPUT_ONLY" itineraryStation="YES" mainStation="NO" name="Buxton (All Stations)" timeZone="Europe/London" type="Group">
  <pub:supplier source="ATOC">
   <pub:memberStation code="GBBUX"/>
 </pub:supplier>
</pub:station>
```

## Special station codes
In addition to standard station codes, the RDG implementation additionally supports the following categories of special codes:

- Group stations
- London zones

### Group stations
A group station is a single station code that represents a collection of member stations within a geographical area, such as all the stations in a major city. Group Station codes have a `type="Group"` attribute in the [Published Data API](/v1/docs/published-data-messages#station){target="_blank"} to differentiate them from single-station codes `type="Station"`.

Each member station within the group is identified by its standard code. In the following example, you can see the response for GB@LO, the London (All Stations) group.

**Example**:
```XML
<pub:station code="GB@LO" countryCode="GB" fareStation="OUTPUT_ONLY" itineraryStation="YES" mainStation="NO" name="London (All Stations)" timeZone="Europe/London" type="Group">
 <pub:supplier source="ATOC">
  <pub:memberStation code="GBBFR"/>
  <pub:memberStation code="GBCHX"/>
  <pub:memberStation code="GBCST"/>
  <pub:memberStation code="GBCTK"/>
  <pub:memberStation code="GBFST"/>
  <pub:memberStation code="GBLBG"/>
  <pub:memberStation code="GBMOG"/>
  <pub:memberStation code="GBMYB"/>
  <pub:memberStation code="GBQQK"/>
  <pub:memberStation code="GBQQP"/>
  <pub:memberStation code="GBQQU"/>
  <pub:memberStation code="GBQQW"/>
  <pub:memberStation code="GBSTP"/>
  <pub:memberStation code="GBWAE"/>
  <pub:memberStation code="GBZLS"/>
  <pub:memberStation code="GBZEP"/>
  </pub:supplier>
</pub:station>
```

When you specify a group station as the origin or destination in your shopping request, SilverCore will return an individual member station in the response. For example, a journey going to GB@LO might return a destination of GBQQP (London Paddington).

### London zones

SilverCore uses special station codes with a “GB*L” prefix to handle London fare zones.  In the following example, you can see the response for GB*L6 (London Underground Zones 1-6).

**Example**:
```XML
<pub:station code="GB*L1" countryCode="GB" fareStation="OUTPUT_ONLY" itineraryStation="YES" mainStation="NO" name="London Underground Zone 1" timeZone="Europe/London" type="Station">
 <pub:supplier source="ATOC"/>
</pub:station>
```

When you specify a London fare zone as the origin or destination in your shopping request, SilverCore will use the zone station code in the relevant Travel Segment in the response.

**Example**:
```XML
<ns2:leg legID="L_1"> <originTravelPoint type="STATION">GBQQM</originTravelPoint> <destinationTravelPoint type="STATION">GB*L6</destinationTravelPoint> <legSolutions> <legSolution legSolutionID="LS_1_0"> <numberOfConnections>1</numberOfConnections> <travelSegments> <travelSegment sequence="0" travelSegmentID="LS_1_0_TS_0" type="TRAIN"> <originTravelPoint type="STATION">GBQQM</originTravelPoint> <destinationTravelPoint type="STATION">GBQQU</destinationTravelPoint> <departureDateTime>2019-09-02T05:55:00</departureDateTime> <arrivalDateTime>2019-09-02T08:10:00</arrivalDateTime> <designator>VT7505</designator> <marketingCarrier>Virgin</marketingCarrier> <operatingCarrier>Virgin</operatingCarrier> <supplierEquipmentType>Virgin-ICY</supplierEquipmentType> <duration>P0Y0M0DT2H15M0S</duration> <crossBorderInfo>false</crossBorderInfo> <equipmentType code="ICY">Inter-City</equipmentType> <marketingServiceName>Virgin Trains service from Manchester Piccadilly to London Euston</marketingServiceName> <marketingInformation serviceCode="Virgin"/> </travelSegment> <travelSegment sequence="1" travelSegmentID="LS_1_0_TS_1" type="TRANSFER"> <originTravelPoint type="STATION">GBQQU</originTravelPoint> <destinationTravelPoint type="STATION">GB*L6</destinationTravelPoint> <departureDateTime>2019-09-02T08:11:00</departureDateTime> <arrivalDateTime>2019-09-02T08:11:00</arrivalDateTime> <marketingCarrier>LonUnderground</marketingCarrier> <operatingCarrier>LonUnderground</operatingCarrier> <supplierEquipmentType>LonUnderground-MCT</supplierEquipmentType> <duration>P0Y0M0DT0H1M0S</duration> <crossBorderInfo>false</crossBorderInfo> <equipmentType code="MCT">Metro/City-Transit</equipmentType> <marketingInformation serviceCode="LonUnderground"/> </travelSegment> </travelSegments> <overtakenJourney>false</overtakenJourney> <duration>P0Y0M0DT2H16M0S</duration> <passengerInformationRequired> <passengerInformation type="PASSENGER_NAME" allPassengers="false"/> </passengerInformationRequired> <availableSeatPreferences> <availableSeatPreference marketingCarrier="Virgin" serviceClass="FIRST" supplierEquipmentType="Virgin-ICY">AIRLINE</availableSeatPreference> <availableSeatPreference marketingCarrier="Virgin" serviceClass="FIRST" supplierEquipmentType="Virgin-ICY">AISLE</availableSeatPreference> …. <availableSeatPreference marketingCarrier="Virgin" serviceClass="THIRD" supplierEquipmentType="Virgin-ICY">WINDOW</availableSeatPreference> </availableSeatPreferences> </legSolution> .... </legSolutions> </ns2:leg>
```

## Fare filtering
RDG carriers typically publish a large number of fares, some of which can be duplicates. To reduce the amount of data returned in a `pointToPointShoppingResponse`, you can use the `fareFilter` attribute in the [pointToPointShoppingRequest](/v1/docs/pointtopointshoppingrequest-elements#code-item-shop-pointToPointShoppingQuery){target="_blank"}.

:::(Warning) (For the UK market, the only fare filter available is  `GB_FARES`.  This returns all available UK Fares, including the cheapest quota-controlled advance fares available at the time of search.)
:::

**Example**:
```XML
fareFilter="GB_FARES"
```

## Earlier/later logic
The early/later functionality logic uses the following  [pointToPointShoppingRequest](/v1/docs/pointtopointshoppingrequest-elements){target="_blank"} elements depending on the scenario:

- **later** will use `<departureDateTimeWindow>`
- **earlier** will use `<arrivalDateTimeWindow>`

The number of minutes in the adds/subtracts can be changed; we're using fifteen minutes as guidance, but you can configure this based on your requirements.

The logic for **later** takes the departure time of the last presented journey, for example, 21:50, adds fifteen minutes to it, resulting in 22:05, uses it to update the date/time in `<departureDateTimeWindow>`, and then triggers a new `pointToPointShoppingRequest`.

The logic for **earlier** takes the arrival time of the first presented journey, for example, 21:08, subtracts fifteen minutes from it, resulting in 20:53, uses it to update the date/time in the `<arrivalDateTimeWindow>`, and then triggers a new `pointToPointShoppingRequest`.

:::(Info) (Unrelated to earlier/later functionality, SilverCore takes the requested time and subtracts fifteen minutes from it for the departure time and adds fifteen minutes for arrival time. For example, if you request a 21:50 departure, you might see departure times starting at 21:35. If you do not want that to happen, on the backend request to SilverCore, you can add fifteen minutes to the time the customer requested.)
:::

## Railcards
RDG offers a number of different railcards through which discounted travel can be obtained.  Railcards are submitted through the `<fareQualifiers>` element in the [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements#code-item-pointToPointPrice){target="_blank"}.

You can retrieve a list of valid railcards by setting `type="fareQualifier"` in your [Published Data](/v1//docs/published-data-messages#fare-qualifiers){target="_blank"} request, as shown in the following example.

**Example request**:
```XML
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:pub="http://railgds.net/ws/publishing">
   <soapenv:Header/>
   <soapenv:Body>
      <pub:dataRequest type="fareQualifier"/>
   </soapenv:Body>
</soapenv:Envelope>
```

The response returns a list of valid railcards.

**Example response**:
```XML
<pub:fareQualifier description="Annual Gold Card" isInput="YES" program="UK_ANNUAL_GOLD" type="DISCOUNT_CARD"/>
```

:::(Info) (The `isInput` attribute on the fareQualifier in Published Data indicates if the card can be used as input in the pointToPointShoppingRequest/exchangeSearchRequest.)
:::

To apply the discount to the booking, include the appropriate `<fareQualifiers>` element in your [pointToPointShoppingRequest](/v1/docs/pointtopointshoppingrequest-elements){target="_blank"}.

**Example**:
```XML
<shop:fareQualifiers>
 <shop:fareQualifier type="DISCOUNT_CARD">
  <program>UK_SENIOR</program>
 </shop:fareQualifier>
 <shop:fareQualifier type="DISCOUNT_CARD">
  <program>UK_YOUTH</program>
 </shop:fareQualifier>
</shop:fareQualifiers>
```

There’s no limit to the number of railcards you can submit in the request, and a specific passenger mapping is unnecessary. SilverCore determines which cards are applicable based on the age of each traveler and the type of card being submitted.

### Groupsave

[Groupsave](https://www.daysoutguide.co.uk/travel-by-train/groupsave){target="_blank"} offers discounted tickets for groups of three to nine adults travelling together.  

In the Published Data response, the Groupsave record has `isInput="NO"`.  The Groupsave option should therefore be excluded from the list of selectable railcards to ensure customers cannot select it.

**Example**:
```XML
<pub:fareQualifier description="GroupSave Discount" isInput="NO" program="UK_GROUPSAVE" type="DISCOUNT_CARD"/>
```

Groupsave is automatically applied on fares whenever applicable, and will be indicated within the `fareQualifier` element in the response, when applicable. Additionally, if a booking contains children, pricing may be given based on adult fares to maximize the total discount.

## Optional prices for Travelcards, PlusBus, bike reservations, and seat reservations

The following options and supplements are supported:

- [London Travelcards](/v1//docs/handling-optional-prices-in-ticketable-fares#example-4-optional-prices-for-travelcards){target="_blank"}
- [PlusBus](/v1/docs/handling-optional-prices-in-ticketable-fares#example-5-optional-prices-for-plusbus-reservations){target="_blank"}
- [Bike reservations](/v1/docs/handling-optional-prices-in-ticketable-fares#example-6-optional-prices-for-bike-reservations){target="_blank"}
- [Seat reservations](/v1/docs/handling-optional-prices-in-ticketable-fares#example-1-optional-prices-for-seat-reservations){target="_blank"}


## Marketing and operating carriers
It is common to see different marketing/operating carriers on the various Travel Segments within a Leg Solution, as shown in the following example.

**Example**:
```XML
<legSolution legSolutionID="LS_1_2"> <numberOfConnections>2</numberOfConnections> <travelSegments> <travelSegment sequence="0" travelSegmentID="LS_1_2_TS_0" type="TRAIN"> <originTravelPoint type="STATION">GBRDG</originTravelPoint> <destinationTravelPoint type="STATION">GBQQP</destinationTravelPoint> <departureDateTime>2019-09-02T06:46:00</departureDateTime> <arrivalDateTime>2019-09-02T07:17:00</arrivalDateTime> <designator>GW4201</designator> <marketingCarrier>FirstGrtWest</marketingCarrier> <operatingCarrier>FirstGrtWest</operatingCarrier> <supplierEquipmentType>FirstGrtWest-ICY</supplierEquipmentType> <duration>P0Y0M0DT0H31M0S</duration> <crossBorderInfo>false</crossBorderInfo> <equipmentType code="ICY">Inter-City</equipmentType> <marketingServiceName>Great Western Railway service from Bristol Temple Meads to London Paddington</marketingServiceName> <marketingInformation serviceCode="FirstGrtWest"/> </travelSegment> <travelSegment sequence="1" travelSegmentID="LS_1_2_TS_1" type="TRANSFER"> <originTravelPoint type="STATION">GBQQP</originTravelPoint> <destinationTravelPoint type="STATION">GBQQU</destinationTravelPoint> <departureDateTime>2019-09-02T07:32:00</departureDateTime> <arrivalDateTime>2019-09-02T07:45:00</arrivalDateTime> <marketingCarrier>LonUnderground</marketingCarrier> <operatingCarrier>LonUnderground</operatingCarrier> <supplierEquipmentType>LonUnderground-MCT</supplierEquipmentType> <duration>P0Y0M0DT0H13M0S</duration> <crossBorderInfo>false</crossBorderInfo> <equipmentType code="MCT">Metro/City-Transit</equipmentType> <marketingInformation serviceCode="LonUnderground"/> </travelSegment> <travelSegment sequence="2" travelSegmentID="LS_1_2_TS_2" type="TRAIN"> <originTravelPoint type="STATION">GBQQU</originTravelPoint> <destinationTravelPoint type="STATION">GBQQM</destinationTravelPoint> <departureDateTime>2019-09-02T08:00:00</departureDateTime> <arrivalDateTime>2019-09-02T10:05:00</arrivalDateTime> <designator>VT7035</designator> <marketingCarrier>Virgin</marketingCarrier> <operatingCarrier>Virgin</operatingCarrier> <supplierEquipmentType>Virgin-ICY</supplierEquipmentType> <duration>P0Y0M0DT2H5M0S</duration> <crossBorderInfo>false</crossBorderInfo> <equipmentType code="ICY">Inter-City</equipmentType> <marketingServiceName>Virgin Trains service from London Euston to Manchester Piccadilly</marketingServiceName> <marketingInformation serviceCode="Virgin"/> </travelSegment> </travelSegments> <overtakenJourney>false</overtakenJourney> <duration>P0Y0M0DT3H19M0S</duration> <passengerInformationRequired> <passengerInformation type="PASSENGER_NAME" allPassengers="false"/> </passengerInformationRequired> <availableSeatPreferences> .... </availableSeatPreferences> </legSolution>
```

## Overtaken journeys
Some RDG journeys are classified as “overtaken,” meaning another journey for the same origin and destination is available with a later departure time and earlier arrival. RDG accreditation rules require you to use a small icon or text on your website to highlight when a specific itinerary is an overtaken journey.

SilverCore indicates overtaken journeys by setting the `<overtakenJourney>` flag for each Leg Solution in the `pointToPointShoppingResponse`.

```XML
<overtakenJourney>true</overtakenJourney>
```

## Text rules

The rules with `type="TEXT"` must be displayed with every fare.

**Example**:
```XML
<rules>
	<rule category="Ticketing" type="TEXT">
		<description>Additional information about this fare can be found at http://www.nationalrail.co.uk/times_fares/ticket_types.aspx</description>
	</rule>
	<rule category="Break_of_Journey" type="TEXT">
		<description>Break of journey : Outward :  A break of journey is not allowed.</description>
	</rule>
	<rule category="Ticketing" type="TEXT">
		<description>Ticket restrictions available at http://www.nationalrail.co.uk/XV</description>
	</rule>
	<rule category="Routing" type="TEXT">
		<description>Valid on CrossCountry services only. Advance tickets are valid only on the booked services.</description>
	</rule>
...
</rules>
```

The rule with `category="Routing"` should be displayed prominently with the journey/fare so that the customer knows the specific route the fare is valid for.

:::(Info) (Please build text rules dynamically into your solution, allowing for the possibility that rules can change.  Fares data can change, and therefore new rules can be added at any time.)
:::

## Fare expiration

In the UK market, SilverCore returns additional parameters in the `<ticketableFare>` container to denote the expiration of outbound and inbound fares:

- `<outwardFareExpirationDate>` - The expiration date shown on the outward portion of a fare.
- `<returnFareExpirationDate>` - The expiration date shown on the return portion of a fare. Returned only for return fares.
- `<expirationAdditionalTime>` - Extends both the outbound and return fare expiration date. Only returned where applicable. The value returned in the UK market indicates that a fare can be used up until 04:29 the following day.

**Example**:
```xml
<com:outwardFareExpirationDate>2022-05-28</com:outwardFareExpirationDate>
<com:returnFareExpirationDate>2022-06-23</com:returnFareExpirationDate>
<com:expirationAdditionalTime>04:29</com:expirationAdditionalTime>
```

## Return fares
Round trip journeys for RDG carriers can be priced out in two different ways:

- A combination of single tickets, with the outbound and return each priced separately.
- A  return fare that includes the outbound and return for one price.

In cases where the Point-to-Point Price includes one price for both the outbound and the return, the `<legReferences>` element will include references to two Leg Solutions.

**Example**:
```XML
<legReferences> <legSolutionIDRef>LS_1_1</legSolutionIDRef> <legSolutionIDRef>LS_2_7</legSolutionIDRef> </legReferences>
```

## Open Returns

The `<includeReturnFares>` element in the [pointToPointShoppingRequest](/v1/docs/pointtopointshoppingrequest-elements#code-item-shop-includeReturnFares_(Optional){target="_blank"}, supports the ability to filter for "Open Return" and "Day Return" fares.  

The `pointToPointShoppingResponse` will contain the outbound timetabled leg with no return leg data.  This will allow the API Partner to display the outbound leg selection with an open fare, without the need to display a return leg to choose from.  This method is useful if the Partner's homepage contains three options in the shopping search process: "One Way", "Round Trip", "Open Return".  

The "Open Return" option will utilize the `<includeReturnFares>` element.

**Example**:
```XML
<shop:includeReturnFares>true</shop:includeReturnFares>
```
:::(Info) (If `<includeReturnFares>` is set to `false` or omitted in a round trip request, the `pointToPointShoppingResponse` will contain both legs of the journey for the customer to select from.  Some of the fares may be an Open Return.  Both the outbound and return legs must be booked, even if the fare is an Open Return.  In this situation, the customer will still be allowed to travel based on the open fare rules, even though a return leg has been selected.)
:::

The `<openReturn>` element in the `pointToPointShoppingResponse` indicates whether a fare is an Open Return.

**Example**:
```XML
<openReturn>YES</openReturn>
```

## Carbon dioxide emissions
Where available for RDG journeys, SilverCore will return the Kilograms of CO₂ emitted for each rail Travel Segment in the Leg Solution in shop and reshop responses.

The data is returned in the `<carbonData>` element located within a `<travelSegment>`.

**Example**:
```xml
<carbonData unit="kgCO2">10.62</carbonData>
```

:::(Info) (If the supply system does not provide the emissions amount for any of the segments, the `carbonData` element will be omitted.  If the leg contains two or more travel segments, but the supply system provides the CO&#8322; emissions on some segments, only the applicable `travelSegment` container will have the `carbonData` element.)
:::

**Example response**:
```xml
<travelSegments>
  <travelSegment sequence="0" travelSegmentID="LS_1_1_TS_0" type="TRAIN">
    <originTravelPoint type="STATION">FRXPG</originTravelPoint>
    <destinationTravelPoint type="STATION">NLAMS</destinationTravelPoint>
    <departureDateTime>2020-07-28T08:25:00</departureDateTime>
    <arrivalDateTime>2020-07-28T11:25:00</arrivalDateTime>
    <designator>9315</designator>
    <marketingCarrier>THALYS</marketingCarrier>
    <operatingCarrier>THALYS</operatingCarrier>
    <supplierEquipmentType>THALYS-HSP</supplierEquipmentType>
    <duration>P0Y0M0DT3H0M0S</duration>
    <crossBorderInfo>false</crossBorderInfo>
    <equipmentType code="HSP">High-Speed</equipmentType>
    <marketingInformation serviceCode="THALYS"/>
    <scheduleConfirmed>true</scheduleConfirmed>
    <carbonData unit="kgCO2">10.62</carbonData>
  </travelSegment>
```

In addition, Partners can also return emissions across different modes of transport for a Leg Solution for presentation to customers.  This data can be returned in shopping and re-shop responses by adding `<shop:returnCarbonDataComparisonsDetails>true</shop:returnCarbonDataComparisonsDetails>` to your request's `responseSpec`.

The resulting response provides the Kilograms of CO₂ emitted by rail (this is a sum of the value returned in <carbonData> for each segment in the Leg Solution), and other modes of transport, such as car, bus, or plane.

**Example single-segment journey**:
```xml
<legSolution legSolutionID="LS_1_0">
  <numberOfConnections>0</numberOfConnections>
  <travelSegments>
    <travelSegment sequence="0" travelSegmentID="LS_1_0_TS_0" type="TRAIN">
      <originTravelPoint type="STATION">GBRDG</originTravelPoint>
      <destinationTravelPoint type="STATION">GBQQM</destinationTravelPoint>
      <departureDateTime>2022-07-01T06:15:00</departureDateTime>
      <arrivalDateTime>2022-07-01T09:23:00</arrivalDateTime>
      <designator>XC3040</designator>
      <marketingCarrier>CrossCountry</marketingCarrier>
      <operatingCarrier>CrossCountry</operatingCarrier>
      <supplierEquipmentType>CrossCountry-ICY</supplierEquipmentType>
      <duration>P0Y0M0DT3H8M0S</duration>
      <crossBorderInfo>false</crossBorderInfo>
      <equipmentType code="ICY">Inter-City</equipmentType>
❶   <carbonData unit="kgCO2">10.62</carbonData>
      <marketingServiceName>CrossCountry service from Southampton Central to Manchester Piccadilly</marketingServiceName>
      <marketingInformation serviceCode="CrossCountry"/>
      <scheduleConfirmed>true</scheduleConfirmed>
    </travelSegment>
  </travelSegments>
  <overtakenJourney>false</overtakenJourney>
  <duration>P0Y0M0DT3H8M0S</duration>
  <carbonDataComparison unit="kgCO2">
❷ <railData>10.62</railData>
    <carData>50.38</carData>
    <busData>34.38</busData>
    <planeData>70.51</planeData>
  </carbonDataComparison>
  <passengerInformationRequired>
    <passengerInformation type="PASSENGER_NAME" allPassengers="false"/>
  </passengerInformationRequired>
  <availableSeatPreferences>...  </availableSeatPreferences>
</legSolution>
```
❶ The Kilograms of CO₂ for the travel segment.

**Example multi-segment journey**:
```XML
<legSolution legSolutionID="LS_1_2">
  <numberOfConnections>4</numberOfConnections>
  <travelSegments>
    <travelSegment sequence="0" travelSegmentID="LS_1_2_TS_0" type="TRAIN">
      <originTravelPoint type="STATION">GBQQY</originTravelPoint>
      <destinationTravelPoint type="STATION">GBQQK</destinationTravelPoint>
      <departureDateTime>2022-06-30T07:37:00</departureDateTime>
      <arrivalDateTime>2022-06-30T09:36:00</arrivalDateTime>
      <designator>GR3060</designator>
      <marketingCarrier>NatExpEastCoast</marketingCarrier>
      <operatingCarrier>NatExpEastCoast</operatingCarrier>
      <supplierEquipmentType>NatExpEastCoast-ICY</supplierEquipmentType>
      <duration>P0Y0M0DT1H59M0S</duration>
      <crossBorderInfo>false</crossBorderInfo>
      <equipmentType code="ICY">Inter-City</equipmentType>
❶   <carbonData unit="kgCO2">11.1</carbonData>
      <marketingServiceName>London North Eastern Railway service from Newcastle to London Kings Cross</marketingServiceName>
      <marketingInformation serviceCode="NatExpEastCoast"/>
      <scheduleConfirmed>true</scheduleConfirmed>
    </travelSegment>
    <travelSegment sequence="1" travelSegmentID="LS_1_2_TS_1" type="TRANSFER">
      <originTravelPoint type="STATION">GBQQK</originTravelPoint>
      <destinationTravelPoint type="STATION">GBQQW</destinationTravelPoint>
      <departureDateTime>2022-06-30T09:51:00</departureDateTime>
      <arrivalDateTime>2022-06-30T10:14:00</arrivalDateTime>
      <marketingCarrier>LonUnderground</marketingCarrier>
      <operatingCarrier>LonUnderground</operatingCarrier>
      <supplierEquipmentType>LonUnderground-MCT</supplierEquipmentType>
      <duration>P0Y0M0DT0H23M0S</duration>
      <crossBorderInfo>false</crossBorderInfo>
      <equipmentType code="MCT">Metro/City-Transit</equipmentType>
      <marketingInformation serviceCode="LonUnderground"/>
    </travelSegment>
    <travelSegment sequence="2" travelSegmentID="LS_1_2_TS_2" type="TRAIN">
      <originTravelPoint type="STATION">GBQQW</originTravelPoint>
      <destinationTravelPoint type="STATION">GBPMS</destinationTravelPoint>
      <departureDateTime>2022-06-30T10:30:00</departureDateTime>
      <arrivalDateTime>2022-06-30T11:57:00</arrivalDateTime>
      <designator>SW8127</designator>
      <marketingCarrier>SouthWest</marketingCarrier>
      <operatingCarrier>SouthWest</operatingCarrier>
      <supplierEquipmentType>SouthWest-ICY</supplierEquipmentType>
      <duration>P0Y0M0DT1H27M0S</duration>
      <crossBorderInfo>false</crossBorderInfo>
      <equipmentType code="ICY">Inter-City</equipmentType>
❶    <carbonData unit="kgCO2">4.42</carbonData>
      <marketingServiceName>South Western Railway service from London Waterloo to Portsmouth Harbour</marketingServiceName>
      <marketingInformation serviceCode="SouthWest"/>
      <scheduleConfirmed>true</scheduleConfirmed>
    </travelSegment>
    <travelSegment sequence="3" travelSegmentID="LS_1_2_TS_3" type="BUS">
      <originTravelPoint type="STATION">GBPMS</originTravelPoint>
      <destinationTravelPoint type="STATION">GBSHV</destinationTravelPoint>
      <departureDateTime>2022-06-30T12:12:00</departureDateTime>
      <arrivalDateTime>2022-06-30T12:20:00</arrivalDateTime>
      <marketingCarrier>Hover</marketingCarrier>
      <operatingCarrier>Hover</operatingCarrier>
      <supplierEquipmentType>Hover-BUS</supplierEquipmentType>
      <duration>P0Y0M0DT0H8M0S</duration>
      <crossBorderInfo>false</crossBorderInfo>
      <equipmentType code="BUS">Bus</equipmentType>
      <marketingServiceName>Hovertravel service from Portsmouth &amp; Southsea to Portsmouth &amp; Southsea</marketingServiceName>
      <marketingInformation serviceCode="Hover"/>
      <scheduleConfirmed>true</scheduleConfirmed>
    </travelSegment>
    <travelSegment sequence="4" travelSegmentID="LS_1_2_TS_4" type="FERRY">
      <originTravelPoint type="STATION">GBSHV</originTravelPoint>
      <destinationTravelPoint type="STATION">GBXRD</destinationTravelPoint>
      <departureDateTime>2022-06-30T12:30:00</departureDateTime>
      <arrivalDateTime>2022-06-30T12:40:00</arrivalDateTime>
      <marketingCarrier>Hover</marketingCarrier>
      <operatingCarrier>Hover</operatingCarrier>
      <supplierEquipmentType>Hover-FRY</supplierEquipmentType>
      <duration>P0Y0M0DT0H10M0S</duration>
      <crossBorderInfo>false</crossBorderInfo>
      <equipmentType code="FRY">Ferry</equipmentType>
      <marketingServiceName>Hovertravel service from Ryde Hoverport to Ryde Hoverport</marketingServiceName>
      <marketingInformation serviceCode="Hover"/>
      <scheduleConfirmed>true</scheduleConfirmed>
    </travelSegment>
    <travelSegment sequence="5" travelSegmentID="LS_1_2_TS_5" type="TRANSFER">
      <originTravelPoint type="STATION">GBXRD</originTravelPoint>
      <destinationTravelPoint type="STATION">GBRYD</destinationTravelPoint>
      <departureDateTime>2022-06-30T12:45:00</departureDateTime>
      <arrivalDateTime>2022-06-30T12:52:00</arrivalDateTime>
      <duration>P0Y0M0DT0H7M0S</duration>
      <crossBorderInfo>false</crossBorderInfo>
      <equipmentType code="PED">Pedestrian</equipmentType>
    </travelSegment>
  </travelSegments>
  <overtakenJourney>false</overtakenJourney>
  <duration>P0Y0M0DT5H15M0S</duration>
  <carbonDataComparison unit="kgCO2">
❷ <railData>15.52</railData>
    <carData>73.65</carData>
    <busData>50.26</busData>
    <planeData>103.07</planeData>
  </carbonDataComparison>
  <passengerInformationRequired>
    <passengerInformation type="PASSENGER_NAME" allPassengers="false"/>
  </passengerInformationRequired>
  <availableSeatPreferences>...  </availableSeatPreferences>
</legSolution>
```

❶ The Kilograms of CO₂ for each travel segment.
❷ The total Kilograms of CO₂ for all travel segments in the Leg Solution.

## Routing restrictions

For shopping requests, you have the option to travel via or avoid specific stations during the journey by including a `<routingRestriction>` element in each `<travelPointPair>`, with the type attribute set to one of the following values:

- `PREFERRED` (travel via the station)
- `RESTRICTED` (avoid the station)

You can request more than one routing restriction in the same `<travelPointPair>`, in which case SilverCore will attempt to match all of your routing restrictions at once.

**Example**:
```XML
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:shop="http://railgds.net/ws/shopping" xmlns="http://railgds.net/ws/commontypes"> <soapenv:Header/> <soapenv:Body> <shop:pointToPointShoppingRequest> <context> <distributorCode>DEMO</distributorCode> <pointOfSaleCode>GB</pointOfSaleCode> <channelCode>CON</channelCode> </context> <shop:pointToPointShoppingQuery <shop:travelPointPairs> <shop:travelPointPair> <originTravelPoint type="STATION">GBCHX</originTravelPoint> <destinationTravelPoint type="STATION">GBDVP</destinationTravelPoint> <departureDateTimeWindow> <date>2019-09-02</date> <time>06:00:00</time> </departureDateTimeWindow> <shop:routingRestrictions> <routingRestriction type="PREFERRED"> <routingTravelPoint type="STATION">GBWYE</routingTravelPoint> </routingRestriction> <routingRestriction type="RESTRICTED"> <routingTravelPoint type="STATION">GBFKW</routingTravelPoint> </routingRestriction> </shop:routingRestrictions> </shop:travelPointPair> </shop:travelPointPairs> <shop:passengerSpecs> <shop:passengerSpec passengerSpecID="PAX_A1"> <age>40</age> </shop:passengerSpec> </shop:passengerSpecs> </shop:pointToPointShoppingQuery> </shop:pointToPointShoppingRequest> </soapenv:Body> </soapenv:Envelope>
```

The `<routingRestrictions>` element under each Leg Solution in the `pointToPointShoppingResponse` message echoes back the restrictions you requested.

**Example**:
```XML
<routingRestrictions> <routingRestriction type="PREFERRED"> <routingTravelPoint type="STATION">GBWYE</routingTravelPoint> </routingRestriction> <routingRestriction type="RESTRICTED"> <routingTravelPoint type="STATION">GBFKW</routingTravelPoint> </routingRestriction> </routingRestrictions>
```

## Calling points

SilverCore supports generating the calling points for a journey by using the [getIntermediateTravelPointsRequest](/v1/docs/getintermediatetravelpointsrequest-elements){target="_blank"} located in the shopping endpoint.

The `getIntermediateTravelPointsRequest` accepts the Leg Solution as input, whilst the [getIntermediateTravelPointsResponse](/v1//docs/getintermediatetravelresponse-elements){target="_blank"} contains the calling points with information such as:

- **status** -  `SCHEDULED`, `ON_TIME`, `DELAYED`, `CANCELED`
- **activity** -  `NORMAL`, `PICK_UP_ONLY`, `SET_DOWN_ONLY`
- **type** - `ORIGIN`, `DESTINATION`, `INTERMEDIATE`
- **platform**

There will also be the scheduled departure/arrival times, as well as the updated departure/arrival times (when applicable).

Only `TRAIN` segment type journeys will be included within the `getIntermediateTravelPointsResponse`.  Segment types such as `BUS`, `FERRY`, and `TRANSFER` will not be returned, so the `getIntermediateTravelPointsResponse` can not be exclusively used to show the journey summary to the customer.  

The `pointToPointShoppingResponse` contains the authoritative journey summary so that the customer knows the exact itinerary and means of transportation when every change occurs. The `getIntermediateTravelPointsResponse` should be used to fill in the calling points on the relevant segments.

## Origin and destination terminals

When the fare origin or fare destination is a fare group that allows travel to a limited list of member stations in that group, the `<fareOriginTerminals>` container element and/or `<fareDestinationTerminals>` container element will contain that list of stations. This list should be displayed online and in the confirmation email, replacing the station code with the station name.

**Example**:
```XML
<fareOriginTerminals>
  <fareOriginTerminal type="STATION">GBRDG</fareOriginTerminal>
  <fareOriginTerminal type="STATION">GBRDW</fareOriginTerminal>
</fareOriginTerminals>
```

**Example**:
```XML
<fareDestinationTerminals>
  <fareDestinationTerminal type="STATION">GBDGT</fareDestinationTerminal>
  <fareDestinationTerminal type="STATION">GBMCO</fareDestinationTerminal>
  <fareDestinationTerminal type="STATION">GBMCV</fareDestinationTerminal>
  <fareDestinationTerminal type="STATION">GBQQM</fareDestinationTerminal>
</fareDestinationTerminals>
```  

In the [pointToPointShoppingResponse](/v1/docs/pointtopointshoppingresponse-elements){target="_blank"}, if the fare is from one fare group to another fare group, the fare may contain both a `<fareOriginTerminals>` and a `<fareDestinationTerminals>` container element.

In a typical implementation, the list is displayed within the area where fare rules are displayed.  As an example, you might introduce the list in the following way:

- "You can travel from the following stations" and then pass in the list of stations returned by `<fareOriginTerminls>`.
- "You can travel to the following stations" and then pass in the list of stations returned by `<fareDestinationTerminals>`.

### Single fares

If two single tickets are selected, each fare will contain the `<fareOriginTerminls>` or `<fareDestinationTerminals>` elements.  

### <a id="returnFares"></a>Return fares

For return fares (covering two Leg Solutions), the `fareOriginTerminals` and `fareDestinationTerminals` are applicable for both the outbound and inbound fares.

Since SilverCore presents only a single set of these containers, when displaying the list of group stations, display the stations from `fareOriginTerminals` and `fareDestinationTerminals` for the outbound rules, and then reverse the list for the return rules, as shown in the following example:

#### Outbound leg

**Fare origin terminals**:
GBRDG
GBRDW

**Fare destination terminals**:
GBDGT
GBMCO
GBMCV
GBQQM

#### Return leg

**Fare origin terminals**:
GBDGT
GBMCO
GBMCV
GBQQM

**Fare destination terminals**:
GBRDG
GBRDW

## Passenger information required at booking

SilverCore requires the first name, last name, age, phone number, and email address for at least one passenger at the time of booking. You must also provide the age of any other passengers on the booking. However, because each Carrier can require additional passenger information, and this information is subject to change, you should always check the `<passengerInformationRequired>` element for each Leg Solution in the shopping response to see what additional information you'll need to provide.

For example, if the Carrier requires the name of all passengers at the time of booking, the response will include `allPassengers="true"`.

**Example**:
```XML
<passengerInformationRequired> <passengerInformation type="PASSENGER_NAME" allPassengers="true"/> </passengerInformationRequired>
```

## Seat reservations
Each `<fareCode>` element in the shopping response contains a `<reservable>` element with a value that indicates whether seat reservations are available and, if so, whether they are required. The `<reservable>` element can have the following value(s):

- `OPTIONAL` - Seat reservations are available but not required.  For further details on how to create a booking with an optional seat reservation, please see [Handling optional prices in ticketable fares](/v1/docs/handling-optional-prices-in-ticketable-fares how-to summary){target="_blank}.
- `MANDATORY` - Seat reservations are required.
- `NOT_POSSIBLE` - No seat reservations are available.

The reservation status is also reflected in the shopping response `<pointToPointPrice>` element. If reservations are mandatory, you’ll see a price of type `RESERVATION` with a zero amount since RDG does not charge an additional fee for seat reservations.

**Example**:
```XML
<prices> <price type="TICKET" currency="GBP">41.50</price> <price type="RESERVATION" currency="GBP">0.00</price> </prices>
```

If reservations are optional, you’ll see an `<optionalPrices>` element instead.

**Example**:
```XML
<prices> <price type="TICKET" currency="GBP">98.15</price> </prices> <optionalPrices> <optionalPrice optionalPriceID="PRICE_P_1_21_0_OP_0"> <price type="RESERVATION" currency="GBP">0.00</price> <applicableTravelSegments maxQuantity="1" minQuantity="1"> <travelSegmentIDRef>LS_1_1_TS_0</travelSegmentIDRef> </applicableTravelSegments> <rules> ... </rules> </optionalPrice> </optionalPrices>
```

To make an optional seat reservation, set the `<reservationRequested>` flag under the `<fareCode>` element in your `createBookingRecordRequest`.

**Example**:
```XML
<fareCode code="IXC-SOS-00700-STD-1"> <serviceClass>THIRD</serviceClass> <travelSegmentIDRef>LS_1_2_TS_0</travelSegmentIDRef> <cabinClass>Standard</cabinClass> <fareDisplayName>Anytime Single</fareDisplayName> <reservationRequested>YES</reservationRequested> </fareCode>
```

## Seat preferences
You have the option to request seat preferences, such as seat direction, as part of the seat reservation. See [Content codes](/v1/docs/content-codes#seat-preferences){target="_blank"} for a complete list of choices.

Seat preferences are always optional, and there is no guarantee that the Carrier will honor the request. If you include mutually exclusive choices, for example, `WINDOW` and `AISLE`, SilverCore will give precedence to the first value passed in.

If a NEARBY seat is requested when creating a booking (nearby an existing booked seat), then any seat preferences submitted at the same time will be ignored.

Because the available seat preferences vary between different Carriers in a Leg Solution, you’ll need to compare the Fare Code’s `<marketingCarrier>`, `<serviceClass>`, and `<supplierEquipmentType>` values against the `<availableSeatPreferences>` element in the shopping response to determine which preferences are available.

**Example**:
```XML
<availableSeatPreferences> <availableSeatPreference marketingCarrier="FirstGrtWest" serviceClass="FIRST" supplierEquipmentType="FirstGrtWest-ICY">AIRLINE</availableSeatPreference> <availableSeatPreference marketingCarrier="FirstGrtWest" serviceClass="FIRST" supplierEquipmentType="FirstGrtWest-ICY">AISLE</availableSeatPreference> <availableSeatPreference marketingCarrier="FirstGrtWest" serviceClass="FIRST" supplierEquipmentType="FirstGrtWest-ICY">BACKWARD</availableSeatPreference> <availableSeatPreference marketingCarrier="FirstGrtWest" serviceClass="FIRST" supplierEquipmentType="FirstGrtWest-ICY">FORWARD</availableSeatPreference> <availableSeatPreference marketingCarrier="FirstGrtWest" serviceClass="FIRST" supplierEquipmentType="FirstGrtWest-ICY">LAVATORY</availableSeatPreference> <availableSeatPreference marketingCarrier="FirstGrtWest" serviceClass="FIRST" supplierEquipmentType="FirstGrtWest-ICY">LUGGAGE</availableSeatPreference> <availableSeatPreference marketingCarrier="FirstGrtWest" serviceClass="FIRST" supplierEquipmentType="FirstGrtWest-ICY">MIDDLE</availableSeatPreference> <availableSeatPreference marketingCarrier="FirstGrtWest" serviceClass="FIRST" supplierEquipmentType="FirstGrtWest-ICY">NEARBY</availableSeatPreference> <availableSeatPreference marketingCarrier="FirstGrtWest" serviceClass="FIRST" supplierEquipmentType="FirstGrtWest-ICY">POWER</availableSeatPreference> <availableSeatPreference marketingCarrier="FirstGrtWest" serviceClass="FIRST" supplierEquipmentType="FirstGrtWest-ICY">QUIET</availableSeatPreference> <availableSeatPreference marketingCarrier="FirstGrtWest" serviceClass="FIRST" supplierEquipmentType="FirstGrtWest-ICY">TABLE</availableSeatPreference> <availableSeatPreference marketingCarrier="FirstGrtWest" serviceClass="FIRST" supplierEquipmentType="FirstGrtWest-ICY">WINDOW</availableSeatPreference> .... <availableSeatPreference marketingCarrier="Virgin" serviceClass="FIRST" supplierEquipmentType="Virgin-ICY">AIRLINE</availableSeatPreference> <availableSeatPreference marketingCarrier="Virgin" serviceClass="FIRST" supplierEquipmentType="Virgin-ICY">AISLE</availableSeatPreference> <availableSeatPreference marketingCarrier="Virgin" serviceClass="FIRST" supplierEquipmentType="Virgin-ICY">BACKWARD</availableSeatPreference> <availableSeatPreference marketingCarrier="Virgin" serviceClass="FIRST" supplierEquipmentType="Virgin-ICY">FORWARD</availableSeatPreference> <availableSeatPreference marketingCarrier="Virgin" serviceClass="FIRST" supplierEquipmentType="Virgin-ICY">LAVATORY</availableSeatPreference> <availableSeatPreference marketingCarrier="Virgin" serviceClass="FIRST" supplierEquipmentType="Virgin-ICY">LUGGAGE</availableSeatPreference> <availableSeatPreference marketingCarrier="Virgin" serviceClass="FIRST" supplierEquipmentType="Virgin-ICY">MIDDLE</availableSeatPreference> <availableSeatPreference marketingCarrier="Virgin" serviceClass="FIRST" supplierEquipmentType="Virgin-ICY">NEARBY</availableSeatPreference> <availableSeatPreference marketingCarrier="Virgin" serviceClass="FIRST" supplierEquipmentType="Virgin-ICY">POWER</availableSeatPreference> <availableSeatPreference marketingCarrier="Virgin" serviceClass="FIRST" supplierEquipmentType="Virgin-ICY">QUIET</availableSeatPreference> <availableSeatPreference marketingCarrier="Virgin" serviceClass="FIRST" supplierEquipmentType="Virgin-ICY">TABLE</availableSeatPreference> <availableSeatPreference marketingCarrier="Virgin" serviceClass="FIRST" supplierEquipmentType="Virgin-ICY">WINDOW</availableSeatPreference> .... </availableSeatPreferences>
```

To request a seat preference, add a `<seatPreferences>` element to the [createBookingRecordRequest](/v1/docs/docs/createbookingrecordrequest-elements#code-item-seatPreferences){target="_blank"}.

**Example**:
```XML
<fareCodes> <fareCode code="IXC-SOS-00700-STD-1"> <serviceClass>FIRST</serviceClass> <travelSegmentIDRef>LS_1_2_TS_0</travelSegmentIDRef> <cabinClass>FIRST</cabinClass> <fareDisplayName>Anytime Single</fareDisplayName> <reservationRequested>YES</reservationRequested> <seatPreferences> <seatPreference>FORWARD</seatPreference> <seatPreference>AISLE</seatPreference> <seatPreference>POWER</seatPreference> </seatPreferences> </fareCode> </fareCodes>
```

:::(Info) (You don’t need to differentiate between seat directions and seat attributes.)
:::

When requesting a seat preference using a specific coach and seat number, it is not necessary to pass in a `<seatPreferences>` element.

### Assigned seats
After confirming an order that includes a seat reservation and optional seat preferences, SilverCore adds a descriptive `<seatAssignment>` element to each affected fare code in the Booking Record.

**Example**:
```XML
<seatAssignment> <coach>D</coach> <number>11</number> <direction>FORWARD</direction> <seatAttributes> <seatAttribute>AISLE</seatAttribute> <seatAttribute>POWER</seatAttribute> </seatAttributes> </seatAssignment>
```

## Real-time service information

SilverCore supports real-time information when available on a UK journey through service alerts and revisions.

### Service alerts

Service alerts, returned in the [pointToPointShoppingResponse](/v1/docs/pointtopointshoppingresponse-elements#code-item-serviceAlerts){target="_blank"} and [getIntermediateTravelPointsResponse](/v1/docs/getintermediatetravelpointsresponse-elements#code-item-ns2-serviceAlerts){target="_blank"} contain data describing any schedule disruptions or ticketing issues applicable to a Leg Solution.

#### Service alerts with one alert

```XML
<serviceAlerts>
  <serviceAlert>
    <summary>Disruption between Bristol Temple Meads and Bristol Parkway / Patchway until the end of service</summary>
    <description>http://nationalrail.co.uk/service_disruptions/181121.aspx</description>
  </serviceAlert>
</serviceAlerts>
```

#### Service alerts with two alerts

```XML
<serviceAlerts>
  <serviceAlert>
    <summary>Disruption to and from London Paddington expected until 10:00</summary
 <description>http://nationalrail.co.uk/service_disruptions/181120.aspx</description>
  </serviceAlert>
  <serviceAlert>
    <summary>From 9 September 2017 until 21 April 2018, passenger lifts to and from the Southbound Thameslink</summary>
    <description>http://www.nationalrail.co.uk/stations/ZFD/details.html</description>
  </serviceAlert>
</serviceAlerts>
```

### Revisions
The `<revisions>` element contains a collection of Carrier-supplied timetable revisions applicable to a Leg Solution.  The element can include revised departure and arrival times and a reason for the delay or cancellation, when available.

The `status` attribute, indicates whether the timetable revision is caused by a delay or cancellation.

#### Revision indicating a delay with a revised departure time
```XML
<revisions>
  <revision status="DELAYED" travelSegmentIDRef="LS_1_2_TS_1">
    <reason code="811">This train has been delayed by damage to the overhead electric wires</reason>
    <revisedDepartureDateTime>2018-01-11T16:56:00</revisedDepartureDateTime>
  </revision>
</revisions>
```

#### Revision indicating a delay with a revised departure and arrival times

```XML
<revisions>
  <revision status="DELAYED" travelSegmentIDRef="LS_1_0_TS_0">
    <reason code="576">This train has been delayed by trespassers on the railway</reason>
    <revisedDepartureDateTime>2018-01-11T16:23:00</revisedDepartureDateTime>
  </revision>
  <revision travelSegmentIDRef="LS_1_0_TS_0">
    <reason code="576">This train has been delayed by trespassers on the railway</reason>
    <revisedArrivalDateTime>2018-01-11T16:45:00</revisedArrivalDateTime>
  </revision>
</revisions>
```

#### Revision indicating a delay with no reason code
```XML
<revisions>
	<revision status="DELAYED" travelSegmentIDRef="LS_1_0_TS_0">
		<revisedDepartureDateTime>2018-01-12T07:01:00</revisedDepartureDateTime>
	</revision>
</revisions>
```

#### Revision indicating a cancellation
```XML
<revisions>
  <revision status="CANCELLED" travelSegmentIDRef="LS_1_3_TS_1">
    <reason code="576">This train has been cancelled because of trespassers on the railway</reason>
  </revision>
❶ <revision travelSegmentIDRef="LS_1_3_TS_1">
    <reason code="576"/>
  </revision>
</revisions>
```

❶ The format of `<revision>` with a reason code but no reason description (indicating that the train is cancelled at either the origin, destination, or both) is also returned but will be deprecated in a future release.

## Ticket Delivery Options
SilverCore supports the following RDG Ticket Delivery Options:

- Pickup-On-Departure
- Mail
- E-Ticket (ETK)
- E-Ticket XML Value Document (XVD and MVD)
- Smartcard

To find out which delivery options are available for a particular fare during the shopping process, check the `<ticketingOptionsAvailable>` element for each Point-To-Point Price in your [pointToPointShoppingResponse](/v1/docs/pointtopointshoppingresponse-elements#code-item-ticketingOptionsAvailable){target="_blank"}.  The most common reason to use this shopping data is if a partner has a requirement to display an indicator on fares that offer E-Tickets.

:::(Info) (The final set of ticketing options available, once a booking is created, may change based on the selected legs (for round-trip bookings that have different ticket delivery options between them) and selected optional prices.  Please use the [validateBookingRecordInformationRequest](/v1/docs/validatebookingrecordinformationrequest-elements) to determine the Ticket Delivery Options once the full journey has been selected.)
:::

Example:
```xml
<ticketingOptionsAvailable TOD="true" ETK="false" PAH="false" PRN="true" EML="false" DEPARTURE_`STATION_TOD="true" SMS="false" XVD="false" SCT="false"/>
XMLCopy
```

For more information about integrating ticket delivery options into your application, please see [Specifying Ticket Delivery Options](/v1/docs/specifying-ticket-delivery-options){target="_blank"}.

For questions about specific options, please get in touch with your SilverRail Implementation Team.

### Checking details for each supported delivery option

Once you’ve created a booking, see the `<ticketingOptions>` element in the Booking Record. You'll find a `<ticketingOption>` element for each supported TDO, with relevant details.

The `<ticketingOptions>` element is also included in the [validateBookingRecordInformationResponse](/v1/docs/validatebookingrecordinformationresponse-elements#code-item-ns2-ticketingOptions){target="_blank"} so you can see which TDOs are available and for what cost before you create a reservation in your system.

**Example**:
```XML
<ticketingOptions> <ticketingOption code="TBM" type="MAIL" destination="UK"> <description>Ticket by Royal Mail Special Delivery</description> <fee isRefundable="false" currency="GBP">6.00</fee> </ticketingOption> <ticketingOption code="TBO" type="MAIL" destination="GLOBAL"> <description>Ticket by International DHL Next Day</description> <fee isRefundable="false" currency="GBP">25.00</fee> </ticketingOption> <ticketingOption code="TBO" type="MAIL" destination="EU"> <description>Ticket by International DHL Next Day</description> <fee isRefundable="false" currency="GBP">20.00</fee> </ticketingOption> <ticketingOption code="TBO" type="MAIL" destination="UK"> <description>Ticket by DHL Next Day</description> <fee isRefundable="false" currency="GBP">10.00</fee> </ticketingOption> <ticketingOption code="TBX" type="MAIL" destination="UK"> <description>Ticket by Courier London Area</description> <fee isRefundable="false" currency="GBP">50.00</fee> </ticketingOption> <ticketingOption code="TOF" type="ON_DEPARTURE" departureStation="true"> <description>Collect at Ticket Office</description> <fee isRefundable="false" currency="GBP">0.00</fee> </ticketingOption> <ticketingOption code="TVM" type="ON_DEPARTURE" departureStation="true"> <description>Collect at Ticket Vending Machine</description> <fee isRefundable="false" currency="GBP">0.00</fee> </ticketingOption> <ticketingOption code="XVD" type="E_TICKET"> <description>Electronic Ticket - XML Value Document</description> <fee isRefundable="false" currency="GBP">0.00</fee> </ticketingOption> </ticketingOptions>
```

### Pickup-On-Departure
Passengers need a credit card to pick up a ticket on departure.  Depending on the station, passengers can obtain their ticket from a ticket vending machine, a ticket window, or a train conductor.

### Mail
For mail delivery, RDG is responsible for sending the tickets. RDG does not submit any tracking information to SilverCore when a customer has selected a by-mail option.

### E-Tickets

A partner can set up ETK and XVD, and passengers can choose whether to have a PDF E-Ticket or an Apple Wallet/Google Wallet pass. Please note, however, that once a booking is `CONFIRMED` in SilverCore, passengers cannot change the TDO method. This approach can save time if a partner wants to offer PDF and Apple/Google Wallet passes.

#### ETK

This ticketing option generates a PDF version of the ticket with a barcode that can be scanned at the station.  This solution is built and accredited in SilverCore to the RSPS3030 specification.

Once the order has been paid and moved to `CONFIRMED` status, submit a [claimValueDocumentRequest](/v1/docs/claimvaluedocumentrequest-elements){target="_blank"} to generate the data string for the PDF.

You can use `<pdfPerCoupon>` to determine how to render the ticket:

- `pdfPerCoupon=false` - Render all of the tickets in a single PDF document.  Achieved also by omitting the element.
- `pdfPerCoupon=true` - Render each ticket in a separate PDF document.

**Example**:
```XML
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:book="http://railgds.net/ws/booking" xmlns="http://railgds.net/ws/commontypes">
	<soapenv:Header/>
	<soapenv:Body>
		<book:claimValueDocumentRequest>
			<context>
				<distributorCode>DEMO</distributorCode>
				<pointOfSaleCode>GB</pointOfSaleCode>
				<channelCode>CON</channelCode>
			</context>
			<book:recordLocator>B-DEMO-AKL0003F5</book:recordLocator>
			<book:responseSpec>
				<book:returnReservationDetails>true</book:returnReservationDetails>
				<book:pdfPerCoupon>true</book:pdfPerCoupon>
			</book:responseSpec>
		</book:claimValueDocumentRequest>
	</soapenv:Body>
</soapenv:Envelope>
```

When you perform a successful `claimValueDocumentRequest`, the `<valueDocument>` status changes from `READY` to `ISSUED`, and the overall order status in the Booking Record changes from `CONFIRMED` to `TICKETED`.  Once you have processed the PDF and sent it to passengers, they can display it on a mobile phone or print it.

:::(Info) (Partners choosing to use ETK have the option to include their desired logo on the ticket. To do so, please submit the logo to the Service Operations Team using the .png format, and with dimensions 55mm x 18 mm.)
:::

#### XVD

Implementing XVD allows partners to implement Apple Wallet and Google Wallet E-Tickets in the UK Market.

SilverCore passes through the E-Ticket information as XML, which the partner must then render to the customer. In the UK, this solution is based on the RDG RSPS3030 E-Ticket specification. Please speak with the SilverCore Service Operations Team to obtain a copy of the specification document.

:::(Warning) (It is the partner's responsibility to build the E-Ticket solution to RSPS3030, which must be accredited through a delegated accreditation.)
:::

You can render the barcode type as either an `ENCODED_STRING` or a `PNG_IMAGE`. Use the `<barcodeType>` element in the `claimValueDocumentRequest` to select the most suitable option for your implementation. We recommended using `ENCODED_STRING` as this option will allow you to import the tickets into a ticketing application such as Apple Wallet or Google Wallet.

- Set to `ENCODED_STRING` to change the E-Ticket barcode data in the response to an “application/octet-stream” string.
- Set to `PNG_IMAGE` or omit the element to change the E-Ticket barcode data in the response to an “image/png” string and a default PNG image.

Refer to the following example of a [claimValueDocumentResponse](/v1/docs/claimvaluedocumentresponse-elements){target="_blank"}.

**Example**
```XML
<eTickets>
  <eTicket>
    <barcode type="application/octet-stream">MDZCMlNXSDY1NVMwMFNURk9HTE9XWUFPVlNVU0FJU0NQTUxRSFFNU0tXRUdZSldWSUFNV1ZGRUJBR1pDTktPSlRGSEFGV0hVV1ZXV0xCVExUQU5XVUhOQkFRVFdKUENBT1lSTE9aTkJGWk1XQVFZWk9QVFZKUU1CSkFDTE9DWlBCRVRXR1pPS1VVVFBLQ0RBSlhFTU9CRUFYVlhGQlNGVFNJWUNMU0xUT0FHU1RKQ1dUTU1BT1dFQ05FT0ZLRE9VTllMV0NIRlZKTENSSEFUTExJSVREWUtQTk5CUlVOU0lJUU9SQlFBVkc=</barcode>
    <cabinClass>STD</cabinClass>
    <ticketType>SOS</ticketType>
    <outReturnIndicator>SGL</outReturnIndicator>
    <issuingSystemID>ST</issuingSystemID>
    <issuingSystemUniqueEticketNumber>B2SWH655S</issuingSystemUniqueEticketNumber>
    <checkSum>0</checkSum>
    <departureTime>09:58:00</departureTime>
    <origin type="STATION">YRK</origin>
    <originName>York</originName>
    <destination type="STATION">LON</destination>
    <destinationName>London Terminals</destinationName>
    <departureDate>22-Aug-2021</departureDate>
    <reservations>
      <reservation>
        <coach>K</coach>
        <number>81</number>
      </reservation>
    </reservations>
    <adultChildIndicator>ADLT</adultChildIndicator>
    <customerName>HALEY LEWIS</customerName>
    <documentIDType>PCD</documentIDType>
    <documentIDNumber>0018</documentIDNumber>
    <routeDescription>Any Permitted</routeDescription>
    <price currency="GBP">136.50</price>
    <fareExpiryDate>2021-08-23</fareExpiryDate>
    <passengerIDRef>P-SMV0001US-0</passengerIDRef>
    <ticketableFareIDRef>TF-SMV0001US-IHI000001-0</ticketableFareIDRef>
    <bidirectional>false</bidirectional>
    <format>D01</format>
    <barCodeTypes>
      <barCodeType>AZTEC</barCodeType>
    </barCodeTypes>
  </eTicket>
</eTickets>
```

### Smartcard

For partners offering the Smartcard ticketing option, the Smart Card Ticket (SCT) option in SilverCore is available for certain journeys in the UK.

The API partner must submit the following information with the Smartcard ticketing option:

- The required ticket pickup location e.g. `<book:pickupLocation type="STATION">GBQQU</book:pickupLocation>` in the [updateBookingRecordRequest](/v1/docs/updatebookingrecordrequest-elements){target="_blank"}.
- The passenger container with the associated `<smartCardNumber>` for the respective passenger.  If this element is available before the initial booking is done, the data can be submitted in the [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements){target="_blank"}.  When the `updateBookingRecordRequest` is being used, the `<passengerID>` can be obtained from the passenger record in the [createBookingRecordResponse](/v1/docs/createbookingrecordresponse-elements){target="_blank"} and the [retrieveBookingRecordResponse](/v1/docs/retrievebookingrecordresponse-elements){target="_blank"} so that you can associate the Smartcard with a passenger.

## Confirmations, cancellations, exchanges, and refunds
RDG is not a pass-through supplier, so partners must handle payment-related activities such as confirmations, cancellations, exchanges, and refunds.

### Order confirmations

Because the Supplier isn’t the merchant of record for RDG transactions, orders are not automatically put into a `CONFIRMED` status after a successful [addPaymentRequest](/v1/docs/addpaymentrequest-elements){target="_blank"}. See [Payment Processing](/v1/docs/payment-processing){target="_blank"} for more information.

To confirm an RDG booking, you can use one of two options:

1) For single-order bookings, you can do an “Add Payment with Confirm” by including `<confirmBooking=true>`in the `addPaymentRequest` message.

**Example**:
```XML
<parameters>
<confirmBooking>true</confirmBooking>
</parameters>
```

2) For bookings containing multiple orders, send a separate [confirmBookingRecordRequest](/v1/docs/confirmbookingrecordrequest-elements){target="_blank"} message immediately after a successful `addPaymentRequest`.  

  You can also confirm single-order bookings this way if you want to do so independently of the `addPaymentRequest`.

#### Including ticket pickup information
RDG also requires that you supply the passenger name and Id details for ticket pickup, so your `addPaymentRequest` or `confirmBookingRecordRequest` must include a `<confirmationInformation>` element with the appropriate values.

**Example**:
```XML
<confirmationInformation> <selectedConfirmationOption> <creditCardOption> <cardholderNameLast>Smith</cardholderNameLast> <cardholderNameFirst>Jane</cardholderNameFirst> <cardNumber>5425232820001308</cardNumber> <expirationYearMonth>2020-12</expirationYearMonth> </creditCardOption> </selectedConfirmationOption> </confirmationInformation>
```

The `<confirmationOptions>` element in the Booking Record tells you which forms of Id are acceptable for use as confirmation information.

**Example**:
```XML
<confirmationOptions> <creditCardOption> <types> <type>AX</type> <type>CA</type> <type>DI</type> <type>VI</type> </types> </creditCardOption> <debitCardOption> <types> <type>MD</type> <type>VD</type> </types> <sortCodeRequired>false</sortCodeRequired> </debitCardOption> </confirmationOptions>
```

### Cancellations, exchanges, and refunds
RDG supports the ability to partial order cancel, full order cancel, delay a cancellation, and exchange an order.  

For more information on exchange, please see [Exchange a ticket](/v1/docs/exchange-a-ticket#standard-silvercore-exchange-message-flow){target="_blank"}.

For RDG, a successful [cancelBookingRecordRequest](/v1/docs/cancelbookingrecordrequest-elements){target="_blank"} doesn't result in an automatic refund from the payment processing system&mdash;as indicated by `<refundIsProcessedOnCancel>false</refundIsProcessedOnCancel>` in the [Booking Record](/v1/docs/retrievebookingrecordresponse-elements#code-item-refundIsProcessedOnCancel){target="_blank"}.

This means you need to manually request a refund by submitting a separate [refundBookingRecordRequest](/v1/docs/refundbookingrecordrequest-elements){target="_blank"} after you've done a successful `cancelBookingRecordRequest`:

- When a `CONFIRMED` order is canceled, the status moves to `CANCELED`.
- When a `TICKETED` order is canceled, the status moves to `WITHDRAWN`.
- For paper tickets, a `TICKETED` order is eligible to be canceled post ticketing and is done so through a fulfillment center:
  - For orders in which all of the tickets are returned, the status moves to `RETURNED` when the tickets are returned to the fulfillment center. The status moves to `WITHDRAWN` after the returned order is approved for a refund.
  - For orders in which a portion of the tickets are returned, the status stays in `TICKETED` status, both when the tickets are returned and when the order is approved for refund.
- For barcode and Print-At-Home tickets, where a refund is allowed, the `TICKETED` order can be canceled without returning and moves directly to `WITHDRAWN`.
- When a `CONFIRMED` or `TICKETED` order is partially canceled or exchanged, the original order status moves to `REPLACED` (all previously generated tickets are no longer usable), and a new order is created with an initial status of `CONFIRMED`.  It will be the partner's responsibility to provide the new ticket details to the customer.
  - If the new order is an E-Ticket order, a new [claimValueDocumentRequest](/v1/docs/claimvaluedocumentrequest-elements){target="_blank"} must be sent to generate the new ticket details and provided to the customer.  
  - If the new order is a collect at station order, the new `<valueDocumentLocator>` must be provided to the customer.
- If the connecting API partner is using SilverAgent to perform partial cancellations:
  - If the new order is an E-Ticket order and SilverAgent has performed the ticketing, the status will go from `CONFIRMED` to `TICKETED`.  The partner can then perform a [redeliverValueDocumentRequest](/v1/docs/redelivervaluedocumentrequest-elements){target="_blank"} on the new order to generate the E-Tickets.
  - If the new order is a collect at station order, the [retrieveBookingRecordRequest](/v1/docs/retrievebookingrecordrequest-elements]{target="_blank"} can be used to obtain the `<valueDocumentLocator>`.
- If the connecting API partner is using SilverAgent to perform partial cancellations or exchanges:
     - If the new order is an E-Ticket order, the initial status will be `CONFIRMED`. If the agent has selected the option to 'Send Ticket', the order status will go from `CONFIRMED` to `TICKETED`.  The partner can then perform a [redeliverValueDocumentRequest](/v1/docs/redelivervaluedocumentrequest-elements){target="_blank"} on the new order to generate the E-Tickets. If the agent has not selected to 'Send Ticket', the partner can submit a [claimValueDocumentRequest](/v1/docs/claimvaluedocumentrequest-elements){target="_blank"} to perform the ticketing. An Issue Refund should also be performed, if applicable.
    - If the new order is a collect at station order, the [retrieveBookingRecordRequest](/v1/docs/retrievebookingrecordrequest-elements){target="_blank"} can be used to obtain the valueDocumentLocator. An Issue Refund should also be performed, if applicable.

### <a id="delaycancel"></a>Delay cancellation for barcode tickets

SilverCore supports delaying cancellations of barcode tickets (XVD, MVD, and ETK) to prevent passengers from obtaining a refund for a ticket used before the ticket status has been updated in the RDG industry systems. This is an RDG Accreditation requirement to prevent fraudulent refunds.

:::(Info) (Please raise a JIRA ticket if you would like this enabled for your context.)
:::

When a [cancelBookingRecord](/v1/docs/cancelbookingrecordrequest-elements){target="_blank"} request is received, SilverCore will delay the cancellation of the order and return the following status message in the `<cancelBookingRecordResponse>`:

```XML
<statusMessages>
  <statusMessage>
    <message>The refund is being processed</message>
    <code>WRN00007</code>
  </statusMessage>
</statusMessages>
```

At this point, the order has not been canceled, and it will retain the original `CONFIRMED` or `TICKETED` status.  SilverCore will return `<internalCancellationReason>` on the order with the value `DELAY_CANCEL`.

SilverCore will also return `<delayedRefundAvailableDateTime>` informing Partners when the delay expires.

**Example cancelBookingRecordResponse (truncated)**:
```XML
❶ <order orderID="O-ATOC-AFB000007-BPO000001" status="TICKETED">
  <supplyChannelRecordLocator>XJ102169</supplyChannelRecordLocator>
  <dateBooked>2022-12-09Z</dateBooked>
  <holdExpiration>2022-12-09T13:46:31Z</holdExpiration>
  <fulfillmentInformation>...</fulfillmentInformation>
  <refundEligibility eligible="false"/>
  <cancellationSummary isCancellable="false" isPartiallyCancellable="true"/>
  ❷<internalCancellationReason>DELAY_CANCEL</internalCancellationReason>
❸ <delayedRefundAvailableDateTime>2022-12-10T13:01:37Z</delayedRefundAvailableDateTime>
  <travelSegments>...</travelSegments>
</travelSegments>
<ticketableFares>...</ticketableFares>
<ticketingOptions>...</ticketingOptions>
<supplyChannelPassengers>
  <supplyChannelPassengerReference>
    <passengerIDRef>P-AFB000007-0</passengerIDRef>
    <nameFirst>Aniyah</nameFirst>
    <nameLast>Austin</nameLast>
  </supplyChannelPassengerReference>
</supplyChannelPassengers>
<confirmationInformationRequired>false</confirmationInformationRequired>
<paymentCardRequiredForConfirmationInformation>false</paymentCardRequiredForConfirmationInformation>
<commercialAgentName>1427</commercialAgentName>
</order>
```
❶ The order status remains as `TICKETED`.
❷ `<internalCancellationReason>` is returned as `DELAY_CANCEL`.
❸ The date/time after which SilverCore will update `<internalCancellationReason>` to `CANCELED` or `CANCEL_DECLINED`.

After the elapsed date/time, SilverCore will update the `<internalCancellationReason>` accordingly:

- If the ticket was not used, both the order status and `<internalCancellationReason>` will be `CANCELED`.  Partners can proceed with the refund process by submitting a [refundBookingRecordRequest](/v1/docs/refundbookingrecordrequest-elements){target="_blank"} in SilverCore.
- If the ticket has been used, the `<internalCancellationReason>` will be `CANCEL_DECLINED`, and the order status remains as `TICKETED`.  This order cannot be refunded.

Partners are responsible for the following:

1.  Creating a queue of bookings/orders that a [retrieveBookingRecordRequest](/v1/docs/retrievebookingrecordrequest-elements){target="_blank"} should be run against.  This request is necessary to find out the final state of the order.
    ::: (Warning) (Wait until the date/time defined in `<delayedRefundAvailableDateTime>` has passed before sending a request to determine the status of the order.)
    :::
2.  If a refund is required, submitting a [refundBookingRecordRequest](/v1/docs/refundbookingrecordrequest-elements){target="_blank"} to refund the customer.

::: (Info) (SilverRail is currently working on an automated booking sync solution.  Once delivered, it will no longer be necessary to create a queue of bookings to run the retrieve booking request against.)
:::

## NRE handoff

For Train Operating Companies offering NRE handoff from National Rail Enquires to their website, SilverCore supports an NRE Handoff feature that allows the booking to be initiated on NRE but completed in SilverCore.  For more information on how the NRE Handoff feature works, please speak with the SilverRail Service Operations Team.

## Carrier restrictions
In the UK market, SilverCore supports the ability to include, exclude, or express a preference for carriers in the results returned in the [pointToPointShoppingResponse](/v1/docs/pointtopointshoppingresponse-elements){target="_blank"}, [travelPassShoppingResponse](/v1/docs/travelpassshoppingresponse-elements){target="_blank"}, and the [exchangeSearchResponse](/v1/docs/exchangesearchresponse-elements){target="_blank"}.  

As an example, you might wish to exclude results from two carriers for a particular route.

**Example**:
```XML
<shop:carrierRestrictions type="RESTRICTED">
  <shop:carrierRestriction code="FirstGrtWest"/>
  <shop:carrierRestriction code="EastMidlands"/>
</shop:carrierRestrictions>
```

Refer to the `<carrierRestrictions>` element in the [pointToPointShoppingRequest](/v1/docs/pointtopointshoppingrequest-elements){target="_blank"} for more information.

## Request stop
To help Partners flag request stop station to customers, in the UK market, SilverCore returns the attribute `requestStop` with a value of `true` to denote that a station is request stop station.  

See the `<originTravelPoint>` element in the [pointToPointShoppingResponse](/v1/docs/pointtopointshoppingrequest-elements#code-item-originTravelPoint){target="_blank"} for more information.

**Example**:
```xml
<legSolution>
  <travelSegments>
    <travelSegment sequence="0" travelSegmentID="LS_1_0_TS_0" type="TRAIN">
      <originTravelPoint type="STATION" requestStop="true">GBCNW</originTravelPoint>
      <destinationTravelPoint type="STATION">GBCDF</destinationTravelPoint>
    </travelSegment>
  </travelSegments>
</legSolution>
```

## Travel Passes

For the UK market, Season Tickets, Carnets, and Flexi-season Tickets (collectively referred to as Travel Passes in SilverCore) offer passengers the possibility to travel at a discounted rate along a specific route. The difference between Travel Passes and Point-To-Point fares is that the latter have Leg Solutions, whereas Travel Passes do not.

In SilverCore, shopping for Season Tickets, Carnets, and Flexi-Season Tickets is done through the [travelPassShoppingRequest](/v1/docs/travelpassshoppingrequest-elements){target="_blank"}.  Creating and canceling bookings for Travel Passes is done through the [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements){target="_blank"} and [cancelBookingRecordRequest](/v1/docs/cancelbookingrecordrequest-elements){target="_blank"} respectively; the flow mirrors that for booking and canceling Point-To-Point fares, with some important differences, which are explained in detail in our [How-To](/v1/docs/work-with-travel-passes){target="_blank"} summary.

## Retrieving timetable revisions

The Booking Timetable Comparator service provides details of timetable revisions that affect bookings for a given Distributor. The service takes the Daily Delta produced by an industry timetable comparator tool and matches timetable differences against existing bookings.

The service fits into a larger product workflow that allows Partners to deliver personalized journey notifications to ticket holders, informing them of changes to their train service before their scheduled journey.

Partners can find more information about the service in our [How-To](/v1/docs/use-the-booking-timetable-comparator-service-to-retrieve-timetable-revisions){target="_blank"}.

## Advanced journey parameters

SilverCore supports the following advanced journey planning parameters when performing Point-To-Point and Exchange shopping requests in the UK market:

- `<equipmentTypeRestrictions>` - Set `code` to `MCT` and `type` to `RESTRICTED` to not return Leg Solutions that include London Underground travel points. No other values are currently supported.
- `<interchangeAdjustment>` - Increase the time allowed for the passenger to change services at an interchange station beyond the interchange time.
- `<reduceTransfers>` - Reduce the recommended transfer time for all London transfers. The provided values apply to interchange times at the start and end stations of the transfer, as well as the duration of the London Underground leg. This element has two optional parameters, which cannot be used together:
  - `setReduction`. If set to true, reduce the transfer time by 20%. This parameter can be used by Partners that want to allow customers to select an "I'm a quick walker" option or similar when booking tickets.
  - `subtractMinutes`. Used as a more specific option where Partners want to provide an exact amount of time in minutes to reduce the transfer time by.

**Example**
```xml
<soap:Envelope xmlns:com="http://railgds.net/ws/commontypes"              
xmlns:book="http://railgds.net/ws/booking"               
xmlns:shop="http://railgds.net/ws/shopping"              
xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Header/>
  <soap:Body>
    <shop:pointToPointShoppingRequest conversationToken="1d8e8bb5-0a0e-4f9e-adb0-45dd8bc2c12b" version="2.0">
      <com:context>
        <com:distributorCode>DEMO</com:distributorCode>
        <com:pointOfSaleCode>GB</com:pointOfSaleCode>
        <com:channelCode>CON</com:channelCode>
      </com:context>
      <shop:pointToPointShoppingQuery>
        <shop:travelPointPairs>
          <shop:travelPointPair>
            <com:originTravelPoint type="STATION">GB@CW</com:originTravelPoint>
            <com:destinationTravelPoint type="STATION">GBSWA</com:destinationTravelPoint>
            <com:departureDateTimeWindow>
              <com:date>2022-05-05</com:date>
              <com:time>14:38:00</com:time>
            </com:departureDateTimeWindow>
          </shop:travelPointPair>
        </shop:travelPointPairs>
        <shop:passengerSpecs>
          <shop:passengerSpec>
            <com:age>40</com:age>
          </shop:passengerSpec>
        </shop:passengerSpecs>
❶     <shop:equipmentTypeRestrictions>
        <shop:equipmentTypeRestriction code="MCT" type="RESTRICTED"/>
      </shop:equipmentTypeRestrictions>
❷     <shop:interchangeAdjustment addMinutes="10">
❸     <shop:reduceTransfers setReduction="true"/>
    </shop:pointToPointShoppingRequest>
  </soap:Body>
</soap:Envelope>
```

❶ Do not return Leg Solutions that include London Underground travel points.
❷ Increase the time allowed for passengers to change services at an interchange station beyond the normal interchange time by 10 minutes.
❸ Reduce the recommended transfer time for all London transfers by 20%. Alternatively Partners can use `subtractMinutes` to reduce the time of London Underground transfers by 15 minutes (which cannot be less than the global minimum of 30 minutes). SilverRail will generate an error if Partners pass in both parameters.

For example, for a transfer between London Bridge and London Euston:

• Interchange Time at London Bridge = 10 minutes
• Tube Duration = 23 minutes
• Interchange Time at London Euston = 15 minutes

If `setReduction` is `false`, the recommended transfer time = 48 minutes (10 + 23 + 15). If `setReduction` is `true`, the recommended transfer time is 39 minutes, 48 minutes – 20% = 39 minutes.

If `subtractMinutes` is `15`, and the original transfer period for an Underground transfer is 40 minutes, the updated transfer period will be 30 minutes since it is not possible to reduce the transfer period to less than 30 minutes. If the original transfer period is 70 minutes, the updated transfer period will be 55 minutes.
