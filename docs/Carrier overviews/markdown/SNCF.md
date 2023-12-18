:::(Error) (**In this article**, we'll summarize the areas of SilverCore functionality where SNCF has specific requirements or restrictions above and beyond what is common to all Suppliers.)
:::

## Available SilverCore API Messages

The following SilverCore API messages are not available for SNCF:

- addPaymentRequest w/Confirm&#42;&#185;
- authenticatePayerRequest
- deleteCardholderInfoRequest
- deletePaymentTokenRequest
- generatePaymentTokenRequest
- getIntermediateTravelPointsRequest

:::(Info) (&#42;&#185; Available in certain circumstances when SilverRail is the merchant of record.)
:::

## Accreditation

Avancial, a subsidiary of SNCF, ensures the quality of all SNCF products or services before their distribution to the public through sales and after-sales simulations on test environments.  Avancial runs a comprehensive suite of tests against web applications to ensure all accreditation standards are met before a production launch.

For information about the accreditation requirements for each Supply Channel, please see [Accreditation requirements summary](/v1/docs/accreditation-requirements-summary){target="_blank"}.

## Station codes
A full list of SNCF station data codes is available through our [Published Data API](/v1/docs/published-data-messages#station){target="_blank"}, using `SNCF` as the selection parameter.

**Example**:
```XML
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:pub="http://railgds.net/ws/publishing">
<soapenv:Header/>
 <soapenv:Body>
  <pub:dataRequest type="station">
   <pub:parameters>
    <pub:selection>SNCF</pub:selection>
   </pub:parameters>
  </pub:dataRequest>
 </soapenv:Body>
</soapenv:Envelope>
```

The station file structure for SNCF stations is the same as all other suppliers.

**Example**:
```XML
<pub:station code="AT@EB" countryCode="AT" fareStation="OUTPUT_ONLY" itineraryStation="YES" mainStation="NO" name="Ebensee" timeZone="Europe/Vienna" type="Group">
 <pub:supplier alias="81901929" codeType="UIC_UNI" source="SNCF" type="CODE"/>
 <pub:supplier alias="ATEBE" codeType="CODE_RESARAIL" source="SNCF" type="CODE"/>
 <pub:supplier alias="Ebensee" source="SNCF" type="ALIAS"/>
 <pub:supplier source="SNCF"/>
</pub:station>
```

## Special station codes
In addition to standard station codes, the SNCF implementation additionally supports station codes for two types of special stations:

- Shared stations
- Group stations

### Shared stations
A shared station is located on a route that crosses an international border and is serviced by two carriers.  For example, London St. Pancras International is serviced by SNCF and RDG (formerly ATOC).

**Example**:
```XML
<pub:station code="GBQQS" countryCode="GB" fareStation="OUTPUT_ONLY" iataCode="QQS" itineraryStation="YES" latitude="51.532420" longitude="-0.126030" mainStation="NO" name="London St Pancras International" timeZone="Europe/London" type="Station">
 <pub:supplier alias="SPX" codeType="CRS" source="ATOC" type="CODE"/>
 <pub:supplier alias="SPX" source="ATOC" type="ALIAS"/>
 <pub:supplier alias="St Pancras International" source="ATOC" type="ALIAS"/>
 <pub:supplier alias="70154005" codeType="UIC_UNI" source="SNCF" type="CODE"/>
 <pub:supplier alias="GBSPX" codeType="CODE_RESARAIL" source="SNCF" type="CODE"/>
 <pub:supplier alias="London St Pancras" source="SNCF" type="ALIAS"/>
</pub:station>
```

The station code for a shared station is the same for both Suppliers, and SilverCore automatically determines which one to use when you submit your shopping request.

To retrieve a list of all stations in the same response, indicating which stations are shared, use two `<selection>` parameters with the station retrieval request.

**Example**:
```XML
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"
 xmlns:pub="http://railgds.net/ws/publishing">
 <soapenv:Header/>
 <soapenv:Body>
  <pub:dataRequest type="station">
   <pub:parameters>
    <pub:selection>SNCF</pub:selection>
    <pub:selection>ATOC</pub:selection>
   </pub:parameters>
  </pub:dataRequest>
 </soapenv:Body>
</soapenv:Envelope>
```

### Group stations
A group station is a single station code that represents a collection of member stations within a geographical area, such as all the stations in a major city. Group Station codes have a `type="Group"` attribute in the [Published Data API](/v1/docs/published-data-messages#station){target="_blank"} to differentiate them from single-station codes `type="Station"`.

Each member station within the group is identified by its standard code.   In the following example, you can see the response for Amélie-Les-Bains-Palalda (FRABP).

**Example**:
```XML
<pub:station code="FRABP" countryCode="FR" fareStation="OUTPUT_ONLY"
 itineraryStation="YES" mainStation="NO" name="Colomiers (localite)"
 timeZone="Europe/Paris" type="Group">
 <pub:supplier alias="3161146" codeType="UIC_UNI" source="SNCF" type="CODE"/>
 <pub:supplier alias="Colomiers (localite)" source="SNCF" type="ALIAS"/>
 <pub:supplier alias="FRABP" codeType="CODE_RESARAIL" source="SNCF" type="CODE"/>
 <pub:supplier source="SNCF"/>
</pub:station>
```

:::(Info) (When you specify a group station as the origin or destination in your shopping request, SilverCore will return an individual member station in the response.  For example, a journey to Paris might return the destination of FRXPG (Paris Gare du Nord).)
:::

## Combination fares

Certain fares for outbound/return can only be used in combination with each other.

In this case, the Point-to-Point Price for each leg in a [pointToPointShoppingResponse](/v1/docs/pointtopointshoppingresponse-elements#code-item-compatiblePrices{target="_blank"} includes a `<compatiblePrices>` element containing a list of `<compatiblePriceIDRef>` elements referencing the corresponding outbound or return prices with which the fare can be combined.

**Example**:
```XML
<compatiblePrices>
 <compatiblePriceIDRef>PRICE_1_1</compatiblePriceIDRef>
 <compatiblePriceIDRef>PRICE_1_2</compatiblePriceIDRef>
 <compatiblePriceIDRef>PRICE_1_3</compatiblePriceIDRef>
 <compatiblePriceIDRef>PRICE_1_4</compatiblePriceIDRef>
 <compatiblePriceIDRef>PRICE_1_5</compatiblePriceIDRef>
 <compatiblePriceIDRef>PRICE_1_6</compatiblePriceIDRef>
</compatiblePrices>
```

When you submit the [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements){target="_blank"}. message for an SNCF round trip, SilverCore verifies that your chosen Point-to-Point Prices are compatible with each other and returns an error if not.

:::(Info) (Combination fare lists are included when a particular Ticketable Fare can only be combined with specific return Ticketable Fares.  If there is no restriction based on the fares included in the Ticketable Fare, no combination fare list is provided.)
:::

## Loyalty cards

SNCF offers the ability to collect rewards through loyalty program(s).  The first and last name on the order must match the name on the loyalty card for the loyalty card to be accepted.

See [Content codes page](/v1/docs/content-codes#loyalty-cards){target="_blank"} for a list of loyalty programs for all Suppliers, along with test identifier numbers. You can also retrieve a list of valid loyalty programs through the Published Data API.

**Example request**:
```XML
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"
 xmlns:pub="http://railgds.net/ws/publishing">
 <soapenv:Header/>
 <soapenv:Body>
  <pub:dataRequest type="loyaltyCard">
  </pub:dataRequest>
 </soapenv:Body>
</soapenv:Envelope>
```

**Example response**:
```XML
<pub:loyaltyCard description="Voyageur" marketingCarrier="EUROSTAR"
 program="FR_VOY"/>
<pub:loyaltyCard description="Grand Voyageur Le Club"
 marketingCarrier="EUROSTAR" program="FR_VOY_CLUB"/>
<pub:loyaltyCard description="Grand Voyageur" marketingCarrier="INTERCITES"
 program="FR_GR_VOY"/>
<pub:loyaltyCard description="Grand Voyageur Plus" marketingCarrier="INTERCITES"
 program="FR_GR_VOY_PL"/>
<pub:loyaltyCard description="Other SNCF card" marketingCarrier="INTERCITES"
 program="FR_OTHER"/>
<pub:loyaltyCard description="Voyageur" marketingCarrier="INTERCITES"
 program="FR_VOY"/>
<pub:loyaltyCard description="Grand Voyageur Le Club"
 marketingCarrier="INTERCITES" program="FR_VOY_CLUB"/>
<pub:loyaltyCard description="Grand Voyageur" marketingCarrier="LYRIA"
 program="FR_GR_VOY"/>
<pub:loyaltyCard description="Grand Voyageur Plus" marketingCarrier="LYRIA"
 program="FR_GR_VOY_PL"/>
<pub:loyaltyCard description="Other SNCF card" marketingCarrier="LYRIA"
 program="FR_OTHER"/>
<pub:loyaltyCard description="Voyageur" marketingCarrier="LYRIA"
 program="FR_VOY"/>
<pub:loyaltyCard description="Grand Voyageur Le Club" marketingCarrier="LYRIA"
 program="FR_VOY_CLUB"/>
<pub:loyaltyCard description="club Xpress" marketingCarrier="MTR"
 program="MTR_CLUB_XPRESS"/>
<pub:loyaltyCard description="Grand Voyageur" marketingCarrier="NAVETTES"
 program="FR_GR_VOY"/>
<pub:loyaltyCard description="Grand Voyageur Plus" marketingCarrier="NAVETTES"
 program="FR_GR_VOY_PL"/>
 ```

The presence of a `<loyaltyCard>` element in the [pointToPointShoppingResponse](/v1/docs/pointtopointshoppingresponse-elements){target="_blank"} message indicates that the loyalty program is available for a particular Leg Solution.

**Example**:
```XML
<legSolution legSolutionID="LS_1_2">
 <numberOfConnections>2</numberOfConnections>
 <travelSegments>
  ....
 </travelSegments>
 <overtakenJourney>false</overtakenJourney>
 <duration>P0Y0M0DT6H30M0S</duration>
 <routingRestrictions>
  <routingRestriction type="PREFERRED">
   <routingTravelPoint type="STATION">FR#LI</routingTravelPoint>
  </routingRestriction>
 </routingRestrictions>
 <passengerInformationRequired>
  <passengerInformation type="PASSENGER_NAME" allPassengers="true"/>
  <passengerInformation type="DATE_OF_BIRTH" allPassengers="true"/>
  <passengerInformation type="PASSENGER_EMAIL" allPassengers="true"/>
 </passengerInformationRequired>
 <availableSeatPreferences>
  ....
 </availableSeatPreferences>
 <loyaltyCards>
  <loyaltyCard program="FR_GR_VOY" description="Grand Voyageur">
   <marketingCarrier>TGV</marketingCarrier>
  </loyaltyCard>
  <loyaltyCard program="FR_GR_VOY_PL" description="Grand Voyageur Plus">
   <marketingCarrier>TGV</marketingCarrier>
  </loyaltyCard>
  <loyaltyCard program="FR_OTHER" description="Other SNCF card">
   <marketingCarrier>TGV</marketingCarrier>
  </loyaltyCard>
  <loyaltyCard program="FR_VOY" description="Voyageur">
   <marketingCarrier>TGV</marketingCarrier>
  </loyaltyCard>
  <loyaltyCard program="FR_VOY_CLUB" description="Grand Voyageur Le Club">
   <marketingCarrier>TGV</marketingCarrier>
  </loyaltyCard>
 </loyaltyCards>
</legSolution>
```

Whether or not the loyalty card applies to an individual Fare Code is determined by the `<rewardsEligible>` element.

**Example**:
```XML
<fareCode code="FA11-1-O-STD">
 <serviceClass>FIRST</serviceClass>
 <travelSegmentIDRef>LS_1_2_TS_0</travelSegmentIDRef>
 <cabinClass>1st Class</cabinClass>
 <rewardsEligible>YES</rewardsEligible>
 <fareClass>FA11</fareClass>
 <fareDisplayName>BUSINESS PREMIERE</fareDisplayName>
 <openReturn>NO</openReturn>
 <overbooked>false</overbooked>
 <reservable>INCLUDED</reservable>
 <fareExpirationDateTime>2019-06-03T09:46:00Z</fareExpirationDateTime>
 <amenities>
  <amenity type="WHEELCHAIR">Wheelchair ramp or facilities</amenity>
 </amenities>
 <fareApplicabilities>
  <fareApplicability outbound="RESTRICTED" return="RESTRICTED" type="SCHEDULE"/>
 </fareApplicabilities>
</fareCode>
```

If the chosen Leg Solution offers loyalty cards and the Fare Code being booked also has `<rewardsEligible>YES</rewardsEligible>`, you can submit the loyalty card number and program via the `<passenger>` element in the [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements){target="_blank"} message.

**Example**:
```XML
<passenger passengerID="PAX_SPEC_0">
 <nameFirst>Annette</nameFirst>
 <nameLast>Dubois</nameLast>
 <dob>1988-04-18</dob>
 <contactInformation>
  <contact>
   <contactType>BUSINESS</contactType>
   <contactMedium>PHONE</contactMedium>
   <contactInfo>1234567890</contactInfo>
  </contact>
  <contact>
   <contactType>BUSINESS</contactType>
   <contactMedium>EMAIL</contactMedium>
   <contactInfo>adubois@email.com</contactInfo>
  </contact>
 </contactInformation>
 <ageAtTimeOfTravel>40</ageAtTimeOfTravel>
 <loyaltyCards>
  <loyaltyCard program="FR_VOY">29090709252072581</loyaltyCard>
 </loyaltyCards>
</passenger>
```

## Corporate discounts

To request SNCF corporate discounts, include a fare qualifier of type `CORPORATE` in your [pointToPointShoppingRequest](/v1/docs/pointtopointshoppingrequest-elements){target="_blank"} message.

**Example**:
```XML
<shop:fareQualifiers>
 <shop:fareQualifier type="CORPORATE">
  <program>FR_Thalys_CORP</program>
  <identifier>C002W28</identifier>
 </shop:fareQualifier>
</shop:fareQualifiers>
```

If `<fareQualifier type="CORPORATE">` in the resulting `pointToPointShoppingResponse`, a corporate discount is available for a particular Ticketable Fare.

**Example**:
```XML
<ticketableFare>
 <totalPrice isEstimated="false" currency="EUR">132.00</totalPrice>
 <passengerReferences>
  <passengerReference>
   <passengerIDRef>PAX_SPEC_0</passengerIDRef>
   <passengerTypeCode>59</passengerTypeCode>
   <fareCodes>
    <fareCode code="THBFE02-2-O-STD">
     <serviceClass>SECOND</serviceClass>
     <travelSegmentIDRef>LS_1_1_TS_0</travelSegmentIDRef>
     <cabinClass>Standard</cabinClass>
     <rewardsEligible>YES</rewardsEligible>
     <fareClass>FLEXIBLE</fareClass>
     <fareDisplayName>CORPO</fareDisplayName>
     <openReturn>NO</openReturn>
     <overbooked>false</overbooked>
     <reservable>INCLUDED</reservable>
     <fareExpirationDateTime>2020-10-07T06:25:00Z</fareExpirationDateTime>
     <amenities>
      <amenity type="BAR">Bar Car</amenity>
      <amenity type="WHEELCHAIR">Wheelchair ramp or facilities</amenity>
     </amenities>
     <fareApplicabilities>
      <fareApplicability outbound="RESTRICTED" return="RESTRICTED"
       type="SCHEDULE"/>
     </fareApplicabilities>
    </fareCode>
   </fareCodes>
  </passengerReference>
 </passengerReferences>
 <prices>
  <price type="TICKET" currency="EUR">132.00</price>
 </prices>
 <rules>
  <rule category="Ticketing" language="fr" type="TEXT">
   <description>Billet remboursable sans frais avant le départ et jusqu'à 1
    jours après le départ.</description>
  </rule>
  <rule category="Ticketing" language="fr" type="TEXT">
   <description>Billet échangeable sans frais avant le départ et jusqu'à une
    heure après le départ uniquement en gare de départ.</description>
  </rule>
  <rule category="Ticketing" language="en" type="TEXT">
   <description>Services included : Electrical socket, WIFI. Included per
    ticket 1 hand luggage and 2 pieces of luggage with maximum dimensions of
    75*53*30cm.</description>
  </rule>
  <rule category="Ticketing" language="fr" type="TEXT">
   <description>Services inclus : Prise électrique, WIFI. Sont compris par
    billet 1 bagage à main et 2 bagages aux dimensions max. 75*53*30cm.</description>
  </rule>
  <rule category="Ticketing" language="en" type="TEXT">
   <description>Ticket can be refunded free of charge prior to departure and
    up to 1 day after departure.</description>
  </rule>
  <rule category="Ticketing" language="en" type="TEXT">
   <description>Ticket exchangeable free of charge before departure and up to
    one hour after at the station of departure only.</description>
  </rule>
  <rule type="EXCHANGE_PROHIBITED" priceType="TICKET"
   applicableOrderStatus="BOOKED" refundToVoucher="NOT_POSSIBLE"/>
  <rule type="REFUND_ALLOWED" priceType="TICKET"
   applicableOrderStatus="BOOKED" refundToVoucher="NOT_POSSIBLE"/>
  <rule type="EXCHANGE_ALLOWED" priceType="TICKET"
   applicableOrderStatus="CONFIRMED" refundToVoucher="NOT_POSSIBLE"/>
  <rule type="REFUND_ALLOWED" priceType="TICKET"
   applicableOrderStatus="CONFIRMED" refundToVoucher="NOT_POSSIBLE"/>
  <rule type="EXCHANGE_ALLOWED" priceType="TICKET"
   applicableOrderStatus="TICKETED" refundToVoucher="NOT_POSSIBLE"/>
  <rule type="REFUND_ALLOWED" priceType="TICKET"
   applicableOrderStatus="TICKETED" refundToVoucher="NOT_POSSIBLE"/>
 </rules>
 <fareQualifiers>
  <fareQualifier type="CORPORATE" fareQualifierID="GFQ_PRICE_P_1_3_0_0"
   authorizationNumberRequired="false" identifierRequired="true">
   <program>FR_Thalys_CORP</program>
   <identifier>C002W28</identifier>
  </fareQualifier>
 </fareQualifiers>
 <ticketingOptionsAvailable TOD="false" ETK="false" PAH="true" PRN="false"
  EML="false" DEPARTURE_STATION_TOD="false" SMS="false"/>
 <fareOrigin type="STATION">FRXPG</fareOrigin>
 <fareDestination type="STATION">NLZYA</fareDestination>
 <seatsAvailable>196</seatsAvailable>
</ticketableFare>
```

Include the fare qualifier data with the Ticketable Fare in your `createBookingRecordRequest` message to use the corporate discount.

**Example**:
```XML
<book:prices>
 <book:pointToPointPrice priceID="PRICE_P_1_3">
  <com:totalPrice currency="EUR">132.00</com:totalPrice>
  <com:ticketableFares>
   <com:ticketableFare>
    <com:totalPrice isEstimated="false" currency="EUR">132.00</com:totalPrice>
    <com:passengerReferences>
     <com:passengerReference>
      <com:passengerIDRef>PAX_SPEC_0</com:passengerIDRef>
      <com:passengerTypeCode>59</com:passengerTypeCode>
      <com:fareCodes>
       <com:fareCode code="THBFE02-2-O-STD">
        <com:serviceClass>SECOND</com:serviceClass>
        <com:travelSegmentIDRef>LS_1_1_TS_0</com:travelSegmentIDRef>
        <com:cabinClass>Standard</com:cabinClass>
       </com:fareCode>
      </com:fareCodes>
     </com:passengerReference>
    </com:passengerReferences>
    <com:prices>
     <com:price currency="EUR" type="TICKET">132.00</com:price>
    </com:prices>
    <com:fareQualifiers>
     <com:fareQualifier fareQualifierID="GFQ_PRICE_P_1_3_0_0"
      authorizationNumberRequired="false" type="CORPORATE" identifierRequired="true">
      <com:program>FR_Thalys_CORP</com:program>
      <com:identifier>C002W28</com:identifier>
     </com:fareQualifier>
    </com:fareQualifiers>
   </com:ticketableFare>
  </com:ticketableFares>
  <com:legReferences>
   <com:legSolutionIDRef>LS_1_1</com:legSolutionIDRef>
  </com:legReferences>
 </book:pointToPointPrice>
</book:prices>
```

:::(Info) (SNCF requires you to pass in a `CORPORATE` fare qualifier for all corporate bookings, even when the shopping response didn't indicate that a discount was available for the selected Ticketable Fare.  In this case, the `<fareQualifers>` element goes at the booking level of the `createBookingRecordRequest` message rather than inside a Ticketable Fare.

**Example**:
```XML
....
    <book:fareQualifiers>
     <book:fareQualifier type="CORPORATE">
       <program>FR_TGV_CORP</program>
       <identifier>C002W28</identifier>
    </book:fareQualifier>
   </book:fareQualifiers>
  </book:createBookingRecordRequest>
 </soapenv:Body>
</soapenv:Envelope>
```

## Digipass fraud detection

To comply with SNCF's DigiPass fraud prevention directives, SilverCore uses the discount card Id to validate whether or not passengers are entitled to benefit from a discounted fare.  SilverRail recommends that you start including the Id for all discount card bookings on SNCF.

Because the Id is not yet mandated by SNCF, the Fare Qualifier `identifierRequired` attribute might be set to false in the [pointToPointShoppingResponse](/v1/docs/pointtopointshoppingresponse-elements){target="_blank"}.

**Example**:
```XML
<fareQualifier type="DISCOUNT_CARD" fareQualifierID="GFQ_PRICE_P_1_0_0_0"
authorizationNumberRequired="false" identifierRequired="false">
<program>FR_PASS_SENIOR</program>
</fareQualifier>
```

However, you can still pass in an Id via the [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements){target="_blank"} message:

**Example**:
```xml
<fareQualifier type="DISCOUNT_CARD">
<program>FR_PASS_SENIOR</program>
<identifier>29090105720468430</identifier>
</fareQualifier>
```

In addition, it is possible to configure, per context, whether or not strict name validation with the SNCF supply system occurs on requests containing a `fareQualifier` that go through the DigiPass check.  By default, all contexts are configured to have non-blocking validation.  To request to change the setting on your contexts, please contact the SilverCore Service Operations team.


## Marketing/Operating carriers

It is common to see different marketing/operating carriers on the various Travel Segments within a Leg Solution.

**Example**:
```XML
<legSolution legSolutionID="LS_1_3">
 <numberOfConnections>2</numberOfConnections>
 <travelSegments>
  <travelSegment sequence="0" travelSegmentID="LS_1_3_TS_0" type="TRAIN">
   <originTravelPoint type="STATION">FRPST</originTravelPoint>
   <destinationTravelPoint type="STATION">FRXWG</destinationTravelPoint>
   <departureDateTime>2019-06-03T10:55:00</departureDateTime>
   <arrivalDateTime>2019-06-03T12:41:00</arrivalDateTime>
   <designator>9573</designator>
   <marketingCarrier>DBSNCF</marketingCarrier>
   <operatingCarrier>DBSNCF</operatingCarrier>
   <supplierEquipmentType>DBSNCF-HSP</supplierEquipmentType>
   <duration>P0Y0M0DT1H46M0S</duration>
   <crossBorderInfo>false</crossBorderInfo>
   <equipmentType code="HSP">High-Speed</equipmentType>
   <marketingInformation serviceCode="DBSNCF"/>
   <scheduleConfirmed>true</scheduleConfirmed>
  </travelSegment>
  <travelSegment sequence="1" travelSegmentID="LS_1_3_TS_1" type="TRAIN">
   <originTravelPoint type="STATION">FRXWG</originTravelPoint>
   <destinationTravelPoint type="STATION">FRSIT</destinationTravelPoint>
   <departureDateTime>2019-06-03T12:51:00</departureDateTime>
   <arrivalDateTime>2019-06-03T14:03:00</arrivalDateTime>
   <designator>96277</designator>
   <marketingCarrier>TER</marketingCarrier>
   <operatingCarrier>TER</operatingCarrier>
   <supplierEquipmentType>TER-BLR</supplierEquipmentType>
   <duration>P0Y0M0DT1H12M0S</duration>
   <crossBorderInfo>false</crossBorderInfo>
   <equipmentType code="BLR">Branch-Line/Regional</equipmentType>
   <marketingInformation serviceCode="TER"/>
   <scheduleConfirmed>true</scheduleConfirmed>
  </travelSegment>
  <travelSegment sequence="2" travelSegmentID="LS_1_3_TS_2" type="TRAIN">
   <originTravelPoint type="STATION">FRSIT</originTravelPoint>
   <destinationTravelPoint type="STATION">CHZDH</destinationTravelPoint>
   <departureDateTime>2019-06-03T14:19:00</departureDateTime>
   <arrivalDateTime>2019-06-03T14:27:00</arrivalDateTime>
   <designator>96077</designator>
   <marketingCarrier>TER</marketingCarrier>
   <operatingCarrier>TER</operatingCarrier>
   <supplierEquipmentType>TER-BLR</supplierEquipmentType>
   <duration>P0Y0M0DT0H8M0S</duration>
   <crossBorderInfo>false</crossBorderInfo>
   <equipmentType code="BLR">Branch-Line/Regional</equipmentType>
   <marketingInformation serviceCode="TER"/>
   <scheduleConfirmed>true</scheduleConfirmed>
  </travelSegment>
 </travelSegments>
 ....
</legSolution>
```

Once a booking has been created, a `marketingCarrierRef` is contained on a ticketableFare.  If assigned, this value represents the Carrier's reference number for the fare.

**Example**:
```XML
<marketingCarrierRef>UFOIWT</marketingCarrierRef>
```

## SEGMENT GO

When provided by SNCF, SilverCore will return `<serviceBrand>` and `<serviceLine>` in the element <marketingInformation> for applicable TER journeys.

**Example response**:
```xml
<travelSegment sequence="0" travelSegmentID="IGNORE" type="TRAIN">
  <originTravelPoint type="STATION">FRZFJ</originTravelPoint>
  <destinationTravelPoint type="STATION">FREDO</destinationTravelPoint>
  <departureDateTime>2020-08-15T18:35:00</departureDateTime>
  <arrivalDateTime>2020-08-15T19:09:00</arrivalDateTime>
  <designator>854421</designator>
  <marketingCarrier>TER</marketingCarrier>
  <operatingCarrier>TER</operatingCarrier>
  <supplierEquipmentType>TER-BLR</supplierEquipmentType>
  <duration>P0Y0M0DT0H34M0S</duration>
  <crossBorderInfo>false</crossBorderInfo>
  <equipmentType code="BLR">Branch-Line/Regional</equipmentType>
  <carbonData unit="kgCO2">2.12</carbonData>
  <marketingInformation serviceCode="TER" serviceBrand="NO_MA_D" serviceLine="C51"/>
</travelSegment>
```

## Carbon dioxide emissions
Where available for SNCF journeys, SilverCore will return the Kilograms of CO₂ emitted for each rail Travel Segment in the Leg Solution in the [pointToPointShoppingResponse](/v1/docs/pointtopointshoppingresponse-elements){target="_blank"}. and [exchangeSearchResponse](/v1/docs/exchangesearchresponse-elements){target="_blank"}.

The data will be contained in the `<carbonData>` element located within a `<travelSegment>`.

:::(Info) (If the supply system does not provide the emissions amount for any of the segments, the `carbonData` element will be omitted.  If the leg contains two or more travel segments, but the supply system provides the CO&#8322; emissions on some segments, only the applicable travelSegment container will have the carbonData element.)
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
    <carbonData unit="kgCO2">2.12</carbonData>
  </travelSegment>
```

## Routing restrictions

For shopping requests, you have the option to travel via specific stations during the journey by including a `<routingRestriction>` element in each `<travelPointPair>` with the type attribute set to the following value:

- `PREFERRED` (travel via the station)

You can request more than one routing restriction in the same `<travelPointPair>`, in which case SilverCore will attempt to match all your `PREFERRED` criteria at once.

**Example**:
```XML
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"
 xmlns:shop="http://railgds.net/ws/shopping"
 xmlns="http://railgds.net/ws/commontypes">
 <soapenv:Header/>
 <soapenv:Body>
  <shop:pointToPointShoppingRequest>
   <context>
    <distributorCode>DEMO</distributorCode>
    <pointOfSaleCode>FR</pointOfSaleCode>
    <channelCode>CON</channelCode>
   </context>
   <shop:pointToPointShoppingQuery>
    <shop:travelPointPairs>
     <shop:travelPointPair>
      <originTravelPoint type="STATION">FR#PA</originTravelPoint>
      <destinationTravelPoint type="STATION">FR@MA</destinationTravelPoint>
      <departureDateTimeWindow>
       <date>2019-06-03</date>
       <time>10:00:00</time>
      </departureDateTimeWindow>
      <shop:routingRestrictions>
       <routingRestriction type="PREFERRED">
        <routingTravelPoint type="STATION">FR#LI</routingTravelPoint>
       </routingRestriction>
      </shop:routingRestrictions>
     </shop:travelPointPair>
    </shop:travelPointPairs>
    <shop:passengerSpecs>
     <shop:passengerSpec passengerSpecID="PAX_A1">
      <age>40</age>
     </shop:passengerSpec>
    </shop:passengerSpecs>
   </shop:pointToPointShoppingQuery>
  </shop:pointToPointShoppingRequest>
 </soapenv:Body>
</soapenv:Envelope>
```

The `<routingRestrictions>` element under each Leg Solution in the [pointToPointShoppingResponse](/v1/docs/pointtopointshoppingresponse-elements){target="_blank"} message echoes back the restrictions you requested.

**Example**:
```XML
<legSolution legSolutionID="LS_1_2">
 <numberOfConnections>2</numberOfConnections>
 <travelSegments>
  ....
 </travelSegments>
 <overtakenJourney>false</overtakenJourney>
 <duration>P0Y0M0DT6H30M0S</duration>
 <routingRestrictions>
  <routingRestriction type="PREFERRED">
   <routingTravelPoint type="STATION">FR#LI</routingTravelPoint>
  </routingRestriction>
 </routingRestrictions>
 <passengerInformationRequired>
  ....
 </passengerInformationRequired>
 <availableSeatPreferences>
  ....
 </availableSeatPreferences>
 <loyaltyCards>
  ....
 </loyaltyCards>
</legSolution>
```

## Fare filtering for shopping requests

SilverCore supports fare filtering for SNCF when shopping for tickets. To apply a filter, set the fareFilter attribute to the appropriate value in the [pointToPointShoppingRequest](/v1/docs/pointtopointshoppingrequest-elements){target="_blank"} or [exchangeSearchRequest](/v1/docs/exchangesearchrequest-elements){target="_blank"} message.

**Example**:
```XML
<shop:pointToPointShoppingQuery fareFilter="CHEAPEST">
```

## Passenger information required at booking

SilverCore requires the name and age of all passengers at the time of booking and a phone number and email address for at least one passenger on the booking.

Because each Carrier can require additional passenger information, and this information is subject to change, you should always check the `<passengerInformationRequired>` element for each Leg Solution in the shopping response to see which information you need to provide.

**Example**:
```XML
<passengerInformationRequired>
 <passengerInformation type="PASSENGER_NAME" allPassengers="true"/>
 <passengerInformation type="DATE_OF_BIRTH" allPassengers="true"/>
 <passengerInformation type="PASSENGER_EMAIL" allPassengers="false"/>
</passengerInformationRequired>
```

For example, the following fragment of a [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements){target="_blank"} message specifies `<name>`, and `<dob>`, and contact email elements.

**Example**:
```XML
<book:passenger passengerID="PAX_SPEC_0">
 <nameFirst>Annette</nameFirst>
 <nameLast>Dubois</nameLast>
 <dob>1976-07-20</dob>
  <contactInformation>
  <contact>
   <contactType>UNKNOWN</contactType>
   <contactMedium>PHONE</contactMedium>
   <contactInfo>1234567890</contactInfo>
  </contact>
  <contact>
   <contactType>BUSINESS</contactType>
   <contactMedium>EMAIL</contactMedium>
   <contactInfo>adubois@email.com</contactInfo>
  </contact>
 </contactInformation>
 <ageAtTimeOfTravel>40</ageAtTimeOfTravel>
</book:passenger>
```

## Seat preferences

You have the option to request seat preferences, such as seat direction, as part of the seat reservation. See [Content codes](/v1/docs/content-codes){target="_blank"} for a complete list of choices.

Seat preferences are always optional, and there is no guarantee the Carrier will honor the request.  If you include mutually exclusive choices, for example, `WINDOW` and `AISLE`, SilverCore will give precedence to the first value passed in.

Because the available seat preferences vary between different Carriers in a Leg Solution, you'll need to compare the Fare Code's `<marketingCarrier>`, `<serviceClass>`, and `<supplierEquipmentType>` values against the `<availableSeatPreferences>` element in the shopping response to determine which preferences are available.

**Example**:
```XML
<availableSeatPreferences>
 <availableSeatPreference marketingCarrier="TGV" serviceClass="FIRST"
  supplierEquipmentType="TGV-HSP"
  travelSegmentIDRef="LS_1_2_TS_0">AISLE</availableSeatPreference>
 <availableSeatPreference marketingCarrier="TGV" serviceClass="FIRST"
  supplierEquipmentType="TGV-HSP"
  travelSegmentIDRef="LS_1_2_TS_1">AISLE</availableSeatPreference>
 <availableSeatPreference marketingCarrier="TGV" serviceClass="FIRST"
  supplierEquipmentType="TGV-HSP"
  travelSegmentIDRef="LS_1_2_TS_0">BOTTOM</availableSeatPreference>
 <availableSeatPreference marketingCarrier="TGV" serviceClass="FIRST"
  supplierEquipmentType="TGV-HSP"
  travelSegmentIDRef="LS_1_2_TS_1">BOTTOM</availableSeatPreference>
 <availableSeatPreference marketingCarrier="TGV" serviceClass="FIRST"
  supplierEquipmentType="TGV-HSP"
  travelSegmentIDRef="LS_1_2_TS_0">NEARBY</availableSeatPreference>
 <availableSeatPreference marketingCarrier="TGV" serviceClass="FIRST"
  supplierEquipmentType="TGV-HSP"
  travelSegmentIDRef="LS_1_2_TS_1">NEARBY</availableSeatPreference>
 <availableSeatPreference marketingCarrier="TGV" serviceClass="FIRST"
  supplierEquipmentType="TGV-HSP"
  travelSegmentIDRef="LS_1_2_TS_0">SIDEBYSIDE</availableSeatPreference>
 <availableSeatPreference marketingCarrier="TGV" serviceClass="FIRST"
  supplierEquipmentType="TGV-HSP"
 travelSegmentIDRef="LS_1_2_TS_1">SIDEBYSIDE</availableSeatPreference>
 <availableSeatPreference marketingCarrier="TGV" serviceClass="FIRST"
  supplierEquipmentType="TGV-HSP"
  travelSegmentIDRef="LS_1_2_TS_0">SINGLE</availableSeatPreference>
 <availableSeatPreference marketingCarrier="TGV" serviceClass="FIRST"
  supplierEquipmentType="TGV-HSP"
  travelSegmentIDRef="LS_1_2_TS_1">SINGLE</availableSeatPreference>
 <availableSeatPreference marketingCarrier="TGV" serviceClass="FIRST"
  supplierEquipmentType="TGV-HSP"
  travelSegmentIDRef="LS_1_2_TS_0">TOP</availableSeatPreference>
 <availableSeatPreference marketingCarrier="TGV" serviceClass="FIRST"
  supplierEquipmentType="TGV-HSP"
  travelSegmentIDRef="LS_1_2_TS_1">TOP</availableSeatPreference>
 <availableSeatPreference marketingCarrier="TGV" serviceClass="FIRST"
  supplierEquipmentType="TGV-HSP" travelSegmentIDRef="LS_1_2_TS_0">TWO_FACING</availableSeatPreference>
 <availableSeatPreference marketingCarrier="TGV" serviceClass="FIRST"
  supplierEquipmentType="TGV-HSP" travelSegmentIDRef="LS_1_2_TS_1">TWO_FACING</availableSeatPreference>
 <availableSeatPreference marketingCarrier="TGV" serviceClass="FIRST"
  supplierEquipmentType="TGV-HSP"
  travelSegmentIDRef="LS_1_2_TS_0">WINDOW</availableSeatPreference>
 </availableSeatPreferences>
```

To request a seat preference, you can add a `<seatPreferences>` element under the `<fareCode>` element in your [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements){target="_blank"}.

**Example**:
```XML
<seatPreferences>
 <seatPreference>SIDEBYSIDE</seatPreference>
 <seatPreference>AISLE</seatPreference>
</seatPreferences>
```

:::(Info) (You don't need to differentiate between seat directions and seat attributes.)
:::

When requesting a seat preference using a specific coach and seat number, it is not necessary to pass in a `<seatPreferences>` element.

**Example**:
```XML
<com:seats>
 <com:seat>
  <com:coach>05</com:coach>
  <com:number>21</com:number>
 </com:seat>
</com:seats>
```

## Multi-order bookings

When configured for a context, under certain scenarios, SilverCore will automatically create a booking with multiple orders in a [createBookingRecordResponse](/v1/docs/creatingbookingrecordresponse-elements){target="_blank"}.  This can happen under the following scenarios, depending on journey:

- Multiple segments
- Multiple passengers

For example, a journey from FRXPG to NLZYA for a single passenger, selecting a non-direct Leg Solution with the FRXPG to BEZYR, THALYS segment and the BEZYR to NLZYA, TRAINETRANGER segment will result in a distinct order per segment:

```XML
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
	<soap:Body>
		<ns2:createBookingRecordResponse xmlns:ns8="http://railgds.net/ws/extractbooking" xmlns:ns7="http://railgds.net/ws/vault" xmlns:ns6="http://railgds.net/ws/search" xmlns:ns5="http://railgds.net/ws/onaccount" xmlns:ns4="http://railgds.net/ws/keystore" xmlns:ns3="http://railgds.net/ws/shopping" xmlns:ns2="http://railgds.net/ws/booking" xmlns="http://railgds.net/ws/commontypes">
			<requestStatus systemId="yVoRLg">
				<success>true</success>
			</requestStatus>
			<ns2:recordLocator>B-DEMO-HAO0001EG</ns2:recordLocator>
			<ns2:lastHoldDateTime>2022-04-08T06:55:00Z</ns2:lastHoldDateTime>
			<ns2:bookingRecord recordLocator="B-DEMO-HAO0001EG" status="DEBIT">
.....
				<orders>
					<order orderID="O-DEMO-HAO0001EG-LJZ000001" status="BOOKED">
.....
						<travelSegments>
							<travelSegment travelSegmentID="TS-HAO0001EG-LJZ000001-1" type="TRAIN" legGrouping="1">
								<originTravelPoint type="STATION">FRXPG</originTravelPoint>
								<destinationTravelPoint type="STATION">BEZYR</destinationTravelPoint>
								<departureDateTime>2022-04-16T08:55:00</departureDateTime>
								<arrivalDateTime>2022-04-16T10:17:00</arrivalDateTime>
								<designator>9317</designator>
								<marketingCarrier>THALYS</marketingCarrier>
								<operatingCarrier>THALYS</operatingCarrier>
								<supplierEquipmentType>THALYS-HSP</supplierEquipmentType>
								<duration>P0Y0M0DT1H22M0S</duration>
								<crossBorderInfo>false</crossBorderInfo>
								<equipmentType code="HSP">High-Speed</equipmentType>
								<carbonData>2.1</carbonData>
								<marketingInformation serviceCode="THALYS"/>
								<scheduleConfirmed>true</scheduleConfirmed>
							</travelSegment>
						</travelSegments>
......
					</order>
......				
				<order orderID="O-DEMO-HAO0001EG-CGK000001" status="BOOKED">					
					<travelSegments>
							<travelSegment travelSegmentID="TS-HAO0001EG-CGK000001-1" type="TRAIN" legGrouping="1">
								<originTravelPoint type="STATION">BEZYR</originTravelPoint>
								<destinationTravelPoint type="STATION">NLZYA</destinationTravelPoint>
								<departureDateTime>2022-04-16T10:44:00</departureDateTime>
								<arrivalDateTime>2022-04-16T13:35:00</arrivalDateTime>
								<designator>9231</designator>
								<marketingCarrier>TRAINETRANGER</marketingCarrier>
								<operatingCarrier>TRAINETRANGER</operatingCarrier>
								<supplierEquipmentType>TRAINETRANGER-ICY</supplierEquipmentType>
								<duration>P0Y0M0DT2H51M0S</duration>
								<crossBorderInfo>false</crossBorderInfo>
								<equipmentType code="ICY">Inter-City</equipmentType>
								<marketingInformation serviceCode="TRAINETRANGER"/>
								<scheduleConfirmed>true</scheduleConfirmed>
							</travelSegment>
						</travelSegments>
.....				
				    </order>
				</orders>
.....
				</ns2:bookingRecord>
		</ns2:createBookingRecordResponse>
	</soap:Body>
</soap:Envelope>
```

If there are multiple passengers, there will be a distinct order per segment/passenger.

This feature must be turned on for a context before it can be used to create multiple orders.  Please speak with the Service Operations team to learn more.

## Ticket Delivery Options

SilverCore supports the following SNCF Ticket Delivery Options:

- Pickup-On-Departure
- Mail
- SMS
- Print-At-Home
- Multi Ticket Delivery (MTD)
- E-Ticket

To determine which delivery options are available for a particular carrier's fare, check the `<ticketingOptionsAvailable>` element for each Point-To-Point Price in the [pointToPointShoppingResponse](/v1/docs/pointtopointshoppingresponse-elements){target="_blank"}
 message.

The following pseudo-code example shows the availability of Ticket Delivery Options.

**Example**:
```XML
<ticketingOptionsAvailable TOD="false" ETK="true" PAH="true" PRN="false"
EML="false" TOF="true" SMS="false" TVM="true"/>
```

For more information about integrating Ticket Delivery Options into your application, please see [Specifying Ticket Delivery Options](/v1/docs/specifying-ticket-delivery-options){target="_blank"}.

Please get in touch with your SilverRail Implementation Team for questions about specific options.

### Checking details for each supported delivery option

Once you've created a booking, see the `<ticketingOptions>` element in the Booking Record.  You'll find a `<ticketingOption>` element for each supported TDO, with relevant details.

The `<ticketingOptions>` element is also included in the [validateBookingRecordInformationResponse](/v1/docs/validatebookingrecordresponse-elements){target="_blank"} so you can see which TDOs are available and for what cost before you create a reservation in your system.

**Example**:
```XML
<ticketingOptions>
 <ticketingOption code="ETK" type="E_TICKET">
  <description>E-Ticket</description>
  <fee isRefundable="false" currency="EUR">0.00</fee>
 </ticketingOption>
 <ticketingOption code="PAH" type="E_TICKET">
  <description>Print At Home</description>
  <fee isRefundable="false" currency="EUR">0.00</fee>
 </ticketingOption>
 <ticketingOption code="TOF" type="ON_DEPARTURE" departureStation="false">
  <description>Collect at Ticket Office</description>
  <fee isRefundable="false" currency="EUR">0.00</fee>
 </ticketingOption>
 <ticketingOption code="TVM" type="ON_DEPARTURE" departureStation="false">
  <description>Collect at Ticket Vending Machine or Kiosk</description>
  <fee isRefundable="false" currency="EUR">0.00</fee>
 </ticketingOption>
</ticketingOptions>
```

### Pickup-On-Departure

Passengers need a credit card to pick up a ticket on departure.  Depending on the station, passengers can obtain their ticket from a ticket vending machine, a ticket window, or a train conductor.

### Multiple Ticket Delivery (MTD)

This option allows customers to collect tickets for bookings that include trains that do not have standard ticketing options, such as Thalys and Eurostar.

#### Single-order bookings
In this scenario, MTD is the only available TDO in the [validateBookingRecordInformationResponse](/v1/docs/validatebookingrecordinformationresponse-elements){target="_blank"}.

**Example**:
```XML
<ticketingOptions>
 <ticketingOption code="MTD" type="M_TICKET">
  <description>Ticket By Multiple Delivery Options</description>
  <fee isRefundable="false" currency="EUR">0.00</fee>
 </ticketingOption>
</ticketingOptions>
```

Once a booking has been completed, SilverCore returns a `<marketingCarrierRefs>` element under `<ticketingOptions>` in the Booking Record.  This element can determine the actual ticket delivery method used for each ticket within an MTD order.

**Example**:
```XML
<ticketingOptions>
 <ticketingOption code="MTD" type="M_TICKET">
  <description>Ticket By Multiple Delivery Options</description>
  <fee isRefundable="false" currency="EUR">0.00</fee>
  <marketingCarrierRefs>
   <marketingCarrierRef referenceCode="SOHKLS">
    <ticketingDetails>
     <ticketingDetail code="PAH" type="E_TICKET" description="Print At Home" />
     <ticketingDetail code="TVM" type="ON_DEPARTURE" description="Ticket
      Vending Machine or Kiosk"/>
     <ticketingDetail code="TOF" type="ON_DEPARTURE" description="Ticket at
      Ticketing Office" />
    </ticketingDetails>
   </marketingCarrierRef>
   <marketingCarrierRef referenceCode="SOHKIJ">
    <ticketingDetails>
     <ticketingDetail code="TVM" type="ON_DEPARTURE" description="Ticket
      Vending Machine or Kiosk"/>
     <ticketingDetail code="TOF" type="ON_DEPARTURE" description="Ticket at
      Ticketing Office" />
     <ticketingDetail code="PRN" type="PRINT" description="Local Printer" />
    </ticketingDetails>
   </marketingCarrierRef>
  </marketingCarrierRefs>
 </ticketingOption>
</ticketingOptions>
```

Each `<marketingCarrierRef>` element has a `referenceCode` attribute that points to the equivalent `<marketingCarrierRef>` element on a Ticketable Fare within the booking.  The individual `<ticketingDetail>` elements then list the actual ticket delivery methods (PAH, TVM, TOF, etc.) the Carrier will be using for that Ticketable Fare.

The `<marketingCarrierRefs>` element is also persisted in the Booking Record `<fulfillmentInformation>` element after the order has been paid/confirmed.

**Example**:
```XML
<fulfillmentInformation>
 <selectedTicketingOption code="MTD" type="M_TICKET">
  <description>Ticket By Multiple Delivery Options</description>
  <fee isRefundable="false" currency="EUR">0.00</fee>
  <marketingCarrierRefs>
   <marketingCarrierRef referenceCode="SOHKLS">
    <ticketingDetails>
     <ticketingDetail code="PAH" type="E_TICKET" description="Print At Home" />
     <ticketingDetail code="TVM" type="ON_DEPARTURE" description="Ticket
      Vending Machine or Kiosk"/>
     <ticketingDetail code="TOF" type="ON_DEPARTURE" description="Ticket at
      Ticketing Office" />
    </ticketingDetails>
   </marketingCarrierRef>
   <marketingCarrierRef referenceCode="SOHKIJ">
    <ticketingDetails>
     <ticketingDetail code="TVM" type="ON_DEPARTURE" description="Ticket
      Vending Machine or Kiosk"/>
     <ticketingDetail code="TOF" type="ON_DEPARTURE" description="Ticket at
      Ticketing Office" />
    </ticketingDetails>
   </marketingCarrierRef>
  </marketingCarrierRefs>
 </selectedTicketingOption>
 ....
</fulfillmentInformation>
```

#### Multi-order bookings
As with single-order bookings, the [validateBookingRecordInformationResponse](/v1/docs/validatebookingrecordinformationresponse-elements){target="_blank"} SilverCore returns MTD as the only available option:

```XML
<ns2:ticketingOptions>
  <ns2:ticketingOption code="MTD" type="M_TICKET">
    <description>Ticket By Multiple Delivery Options</description>
    <fee isRefundable="false" currency="EUR">0.00</fee>
  </ns2:ticketingOption>
</ns2:ticketingOptions>
```

A successful [createBookingRecordResponse](/v1/docs/creatingbookingrecordresponse-elements){target="_blank"} will contain multiple orders, with some travel segments contained in their own order. Each order will indicate the exact ticketing options available for that order

A journey that will typically result in multiple orders is FRXPG to NLZYA, with the journey containing carriers THALYS and TRAINETRANGER.

**Example**:
Order 1 with the THALYS segment:
```XML
<ticketingOptions>
  <ticketingOption code="PAH" type="E_TICKET">
    <description>Print At Home</description>
    <fee isRefundable="false" currency="EUR">0.00</fee>
  </ticketingOption>
</ticketingOptions>
```

Order 2 with the TRAINETRANGER segments:
```XML
<ticketingOptions>
  <ticketingOption code="PRN" type="PRINT">
    <description>Paper Ticket</description>
    <fee isRefundable="false" currency="EUR">0.00</fee>
  </ticketingOption>
  <ticketingOption code="TOF" type="ON_DEPARTURE" departureStation="false">
    <description>Collect at Ticket Office</description>
    <fee isRefundable="false" currency="EUR">0.00</fee>
  </ticketingOption>
  <ticketingOption code="TVM" type="ON_DEPARTURE" departureStation="false">
    <description>Collect at Ticket Vending Machine or Kiosk</description>
    <fee isRefundable="false" currency="EUR">0.00</fee>
  </ticketingOption>
</ticketingOptions>
```

If the [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements){target="_blank"} contains multiple passengers, there will be situations where each passenger gets added on their own distinct order. For example, a two passenger booking request for the above FRXPG to NLZYA scenario will result in four orders.

If the ticketing options change between the [validateBookingRecordResponse](/v1/docs/validatebookingrecordinformationresponse-elements){target="_blank"} and the createBookingRecordRequest, the API Partner will need to re-prompt the user to select the exact ticketing option per order (while optionally consolidating distinct order segments per passenger group to minimize selections) since MTD must not be sent as the selected ticketing option unless it's specified on a created order.

If all orders contain the same selected ticket delivery option, a single updateBookingRecordRequest with only the record locator can be sent to apply the selection across all orders.

If all orders do not contain the same selected Ticket Delivery Option, an [updateBookingRecordRequest](/v1/docs/updatebookingrecordrequest-elements){target="_blank"} for each distinct order locator must be sent.

### Paper tickets

To support the PRN "Paper Ticket" TDO for SNCF, the [updateBookingRecordRequest](/v1/docs/updatebookingrecordrequest-elements){target="_blank"} supports adding an agentEmail within the ticketOption.

Once this information has been set, the `<selectedTicketingOption>` element in the SilverCore Booking Record persists the email of the travel agent responsible for printing the ticket.

## Payment and confirmation

SNCF bookings require payment with onAccount and confirmation does not happen automatically.

For single order bookings, confirmation can occur in the [addPaymentRequest](/v1/docs/addPaymentRequest-elements){target="_blank"} with `confirmBooking=true` or with `confirmBooking=false` and a subsequent [confirmBookingRecordRequest](/v1/docs/confirmBookingRecordRequest-elements){target="_blank"}.

For multiple order bookings, confirmation must occur separately from the `addPaymentRequest` using the `confirmBookingRecordRequest`.  This can be done by looping through each order individually (for example if there are four orders, this will require four separate confirmBookingRecordRequests), or by sending a `confirmBookingRecordRequest` with only a `bookingLocator`, which will confirm all orders on the booking.

**Example**:
```XML
<parameters>
 <confirmBooking>true</confirmBooking>
</parameters>
```

### Example addPaymentRequest with confirm
```XML
<soap:Envelope xmlns:com="http://railgds.net/ws/commontypes" xmlns:book="http://railgds.net/ws/booking" xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Header/>
  <soap:Body>
    <book:addPaymentRequest>
      <com:context xmlns:shop="http://railgds.net/ws/shopping">
        <com:distributorCode>DEMO</com:distributorCode>
        <com:pointOfSaleCode>FR</com:pointOfSaleCode>
        <com:channelCode>CON</com:channelCode>
      </com:context>
      <book:recordLocator>B-DEMO-QSU0000MH</book:recordLocator>
      <book:payment>
        <book:formOfPayment type="OA">None</book:formOfPayment>
        <book:onAccount>
          <com:number>7039937961194902</com:number>
          <com:validationCode>0003</com:validationCode>
          <com:accountName>Deena Reeves</com:accountName>
        </book:onAccount>
        <book:amount currency="EUR">142.20</book:amount>
      </book:payment>
      <book:parameters>
        <book:confirmBooking>true</book:confirmBooking>
      </book:parameters>
      <book:responseSpec>
        <book:returnReservationDetails>true</book:returnReservationDetails>
      </book:responseSpec>
    </book:addPaymentRequest>
  </soap:Body>
</soap:Envelope>
```

## Proof of travel for TER journeys

To support customers that need to obtain a TER proof of travel for claims of instance, the relevant documents are available in SilverCore the day after the last date of travel by using the [redeliverValueDocumentRequest](/v1/docs/redelivervaluedocumentrequest-elements){target="_blank"}.

The `<valueDocumentURL>` element located within the [redeliverValueDocumentResponse](/v1/docs/redelivervaluedocumentresponse-elements){target="_blank"} contains the link to the document. The proof of travel URLs are valid for only one hour.

It is the API partner's responsibility to either save the document or regenerate the document if the previous URL has expired.

There can be multiple `<valueDocumentURL>` elements when there are multiple passengers/segments.

## Cancellations and refunds

A successful [cancelBookingRecordRequest](/v1/docs/cancelbookingrecordrequest-elements){target="_blank"} will not result in an automatic refund.

When a `CONFIRMED` order is canceled, the status changes to `CANCELED`, and the PDF ticket is no longer valid for travel.

When a `TICKETED` order is canceled, the status changes to `WITHDRAWN`, and the PDF ticket is no longer valid for travel.  This occurs with the PAH ticket delivery option.

For more information about order state transitions, see [Order state transitions](/v1/docs/order-state-transitions){target="_blank"}.

In certain situations, a `cancelBookingRecordRequest` message can be successful, but the automatic refund may fail.  Take the following steps to ensure the customer receives the refund:

1. Calculate receiptsTotal-revenueTotal. If the result is greater than 0, the refund needs to be manually attempted
2. Send a follow up [refundBookingRecordRequest](/v1/docs/refundbookingrecordrequest-elements){target="_blank"} message
3.  Verify that receiptsTotal-revenueTotal now equals 0

### cancelBookingRecordRequest with release seat and the SNCF RAD process

SilverCore supports the SNCF RAD two-step process:

1.  Release the previously booked seat(s) without immediate refund
2.  Perform the aftersales operations needed to refund the customer

For further information about how the RAD process works, refer to [SNCF Remise A Disposition process](/v1/docs/sncf-remise-a-disposition-rad-process){target="_blank"}.

### cancelBookingRecordRequest and the SNCF Lutte Anti-Fraude (LAF) process

SilverCore is integrated with SNCF's LAF process.  On cancel requests that are impacted by LAF, the `<cancelBookingRecordResponse>` will return the following message and code:

```XML
<requestStatus>
 <success>true</success>
 <statusMessages>
  <statusMessage>
   <message>The refund is delayed but the PNR's passengers seats are now
   available for resale</message>
   <code>000</code>
  </statusMessage>
 </statusMessages>
</requestStatus>
```

At this point, the order has not been canceled, and it will retain the original `CONFIRMED` or `TICKETED` status.  We will return `<internalCancellationReason>` on the order with the value `DELAY_CANCEL`.

SilverCore will run a regular batch job on these SNCF orders to find out the latest details and determine if any fraud has occurred.  Once complete, the `<internalCancellationReason>` will be updated with the value `CANCELED`, if no fraud was detected, or `CANCELED_WITH_FRAUD_PENALTY`, if fraud was detected.  The appropriate cancellation penalty will be assessed to the order.  

The Partner will be responsible for:

1.  Creating a queue of bookings/orders that a [retrieveBookingRecordRequest](/v1/docs/retrievebookingrecordrequest-elements){target="_blank"} should be run against.  This request is necessary to find out the final state of the order.
    :::(Info) (It is best to wait until the train has departed before sending a request to determine the status of the aftersales.)
    :::
3.  If a refund is required, submitting a [refundBookingRecordRequest](/v1/docs/refundbookingrecordrequest-elements){target="_blank"} to refund the customer.

For full order LAF cancellations, the SilverCore order status will migrate from:
- `CONFIRMED` to `CANCELED`
- `TICKETED` to `WITHDRAWN`

For partial order LAF cancellations, the SilverCore order status will migrate from
- `CONFIRMED` to` REPLACED`
- ` TICKETED` to `REPLACED`

### Cancel due to strike

SNCF offers the ability to fully cancel or partially cancel an order due to a strike, which allows for waiving/reducing penalties on orders, even if they are non-refundable.

All SNCF trains are compatible with canceling due to a strike except TER standalone orders. It's not possible to cancel due to a strike if the fare doesn't include a seat reservation (a seat and a coach assigned at booking time).

To cancel an order or a specific ticketableFare(s) due to a strike, submit the `cancellationReason` as ` DISRUPTION`.

**Example**:
```XML
<book:cancellationReason>DISRUPTION</book:cancellationReason>
```

## Exchanges and voids

### Exchange with exchange sets

SNCF supports the ability to exchange multiple ticketableFares at the same time.  SilverCore indicates which ticketableFare Ids can be exchanged together with a distinct `exchangeSet` per allowed combination.  This function will allow the partner UI to present the right selections to the user.

**Example response**:
```xml
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

### Voiding SNCF tickets

SilverCore allows you to void SNCF tickets within a specific timeframe after the sale.  Voiding is possible on all ticket types, including those that do not usually allow refunds.

To void a ticket, set the `<void>` flag under the `<parameters>` element in the [cancelBookingRecordRequest](/v1/docs/cancelbookingrecordrequest-elements){target="_blank"}.

**Example**:
```XML
<book:cancelBookingRecordRequest>
 <context>
  <distributorCode>DEMO</distributorCode>
  <pointOfSaleCode>FR</pointOfSaleCode>
  <channelCode>CON</channelCode>
 </context>
 <book:recordLocator>B-DEMO-ZGK00040F</book:recordLocator>
 <book:expectedCancellationFee currency="EUR">0.00</book:expectedCancellationFee>
 <book:parameters>
  <book:void>true</book:void>
 </book:parameters>
</book:cancelBookingRecordRequest>
```

The `<voidSummary>` element returned by the [retrieveCancellationSummaryResponse](/v1/docs/retrievebookingrecordresponse-elements){target="_blank"}, and also present in the Booking Record shows if an order can be voided and for how long.

**Example**:
```xml
<voidSummary isVoidable="true" untilWhen="2019-11-13T00:21:02Z"/>
```

:::(Info) (Void must be turned on for your context.  Please speak with the Service Operations team to learn more.)
:::

## Travel point and timetable revisions
When provided by SNCF, SilverCore displays travel point and timetable revisions to support real-time travel information updates.  

Refer to the following elements in the `<revisions`> element in [retrieveBookingRecordResponse](/v1/docs/retrievebookingrecordresponse-elements){target="blank"}:

- `<reason>` - A carrier-specific code and associated text to denote the reason for a timetable revision.
- `<revisedOriginTravelPoint>` - The revised origin travel point for a Travel Segment.
- `<revisedDestinationTravelPoint>` - The revised destination travel point for a Travel Segment.
- `<revisedDepartureDateTime>` - The revised date and time of departure for the Travel Segment in `YYYY-MM-DDTHH:MM:SS` format (local to the Origin Travel Point).
- `<revisedArrivalDateTime>` - The revised date and time of arrival for the Travel Segment in `YYYY-MM-DDTHH:MM:SS` format (local to the Destination Travel Point).

**Example**:
```xml
<revisions>
  <revision travelSegmentIDRef="TF-NEV00000E-GGM000001-1" status="CANCELLED">
    <reason code= "564">This train has been cancelled because of a security alertat a station</reason>
    <revisedDepartureDateTime>2021-05-08T14:51:00</revisedDepartureDateTime>
    <revisedOriginTravelPoint type="STATION">FRJDQ</revisedOriginTravelPoint>
  </revision>
</revisions>
```

:::(Info) (To help partners identify the status of the original Travel Segment in the leg, we will also output the attribute `status` with the value `CANCELLED` or `DELAYED`.)
:::

## Seat maps

The SilverSeatmap service, available also in the French market provides a REST API called Seatmap API, allowing Partners to retrieve details of carriages/decks, seat availability, orientation, and ancillary information (seat map data). Partners can then use this data to provide a visual representation to customers.

You can read more about the SilverSeatmap service and how it fits into SilverCore's shopping and booking flows in our [How-To summary](/v1/docs/use-the-seat-map-api-to-retrieve-a-train-layout-response){target="_blank"}.
