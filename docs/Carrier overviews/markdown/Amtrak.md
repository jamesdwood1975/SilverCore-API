:::(Error) (**In this article**, we will summarize the areas of SilverCore functionality where Amtrak has specific requirements or restrictions above and beyond what is common to all Suppliers.)
:::

## Available SilverCore API messages

The following SilverCore API messages are **not** available for Amtrak:

- travelPassShoppingRequest
- getIntermediateTravelPointsRequest
- exchangeSearchRequest
- retrieveExchangeSummaryRequest
- processExchangeRequest
- claimValueDocumentRequest
- authenticatePayerRequest&#42;&#185;
- addPaymentRequest w/Confirm&#42;&#178;
- confirmBookingRecordRequest&#42;&#179;

:::(Info) (&#42;&#185; Available in certain circumstances when SilverRail is the merchant of record.)
:::
:::(Info) (&#42;&#178; See Payment and confirmation below.)
:::
:::(Info) (&#42;&#178; See Cancellation and refunds below.)
:::

## Accreditation

SilverRail runs a comprehensive suite of tests against your web application to ensure all accreditation standards are met before a production launch.

See [Implementation process](/v1/docs/accreditation-process){target="_blank"} for more information about our accreditation process.

For information about the accreditation requirements for each Supply Channel, please see [Functional requirements summary](/v1/docs/accreditation-requirements-summary){target="_blank"}.  See [Sample implementation tests](/v1/docs/amtrak-implementation-tests){target="_blank"} for details of the tests we perform when evaluating a Partner's implementation for the Amtrak supply channel.

## Maintenance windows

| Start time | End time | Systems affected |
| :-- | :-- |:--|
| Sunday 3am EST| Sunday 6am EST | Prod and Test|
| Monday 12pm EST | Monday 1pm EST | Test |
| Wednesday 8pm EST | Thursday 8:30am EST | Test&#42;&#185; |

:::(Warning) (&#42;&#185; Amtrak refreshes the test system beginning on Wednesday at 8:00 PM (EST) and expects the system to be restored by 8:00 AM (EST) on Thursday morning.)
:::
:::(Warning) (&#42;&#185; Amtrak will send out a notification if there is a delay that prevents the test environment from being available after 8:00 AM EST.)
:::

## Station codes

A complete list of Amtrak station data codes is available through our [Published Data API](/v1/docs/published-data-messages#station){target="_blank"}, using `AMT` as the selection parameter.

**Example**:
```XML
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"
 xmlns:pub="http://railgds.net/ws/publishing">
 <soapenv:Header/>
 <soapenv:Body>
  <pub:dataRequest type="station">
   <pub:parameters>
    <pub:selection>AMT</pub:selection>
   </pub:parameters>
  </pub:dataRequest>
 </soapenv:Body>
</soapenv:Envelope>
```

The station file structure for Amtrak stations is the same as all other suppliers.

**Example**:
```XML
<pub:station code="USNYP" countryCode="US" fareStation="OUTPUT_ONLY"
 itineraryStation="YES" latitude="40.750327" longitude="-73.994459"
 mainStation="NO" name="New York (Penn Station)" stateProvinceCode="NY"
 timeZone="America/New_York" type="Station">
 <pub:supplier alias="New York (Penn Station), New York" source="AMT"
  type="ALIAS"/>
 <pub:supplier alias="NYP" codeType="Amtrak Code" source="AMT" type="CODE"/>
 <pub:supplier alias="New York (Penn Station), New York" source="AMT"
  type="ALIAS"/>
 <pub:supplier alias="NYP" codeType="Amtrak Code" source="AMT" type="CODE"/>
</pub:station>
```

### Atlantic City stations

NJ Transit, a local rail company, provides service to and from Atlantic City stations.  The carrier does not support Amtrak E-Tickets, so Partners will need to ensure the following stations are not returned in shopping results:

* Pennsauken, New Jersey (USPNK)
* Cherry Hill, New Jersey (USCRH)
* Lindenwold, New Jersey (USLDW)
* Atco, New Jersey (USATO)
* Hammonton, New Jersey (USHTN)
* Egg Harbor City, New Jersey (USEGH)
* Absecon, New Jersey (USABN)
* Atlantic City, New Jersey (USACY)

## Shared stations

A shared station is a station serviced by two carriers because it is located on a route that crosses an international border.  For example, Vancouver is serviced by both Amtrak and Via Rail.

**Example**:
```XML
<pub:station code="CAXEA" countryCode="CA" fareStation="OUTPUT_ONLY"
 itineraryStation="YES" latitude="49.273757" longitude="-123.098336"
 mainStation="NO" name="Vancouver" stateProvinceCode="BC"
 timeZone="America/Vancouver" type="Station">
 <pub:supplier alias="VAC" codeType="Amtrak Code" source="AMT" type="CODE"/>
 <pub:supplier alias="Vancouver, British Columbia, Canada" source="AMT"
  type="ALIAS"/>
 <pub:supplier alias="Vancouver" source="VIA" type="ALIAS"/>
 <pub:supplier alias="VCVR" codeType="Viarail Code" source="VIA" type="CODE"/>
</pub:station>
```

The station code for a shared station is the same for both Suppliers, and SilverCore automatically determines which one to use when you submit your shopping request.

To retrieve a list of all stations in the same response, indicating which stations are shared, use two `selection` parameters with the station retrieval request.

**Example**:
```XML
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"
 xmlns:pub="http://railgds.net/ws/publishing">
 <soapenv:Header/>
 <soapenv:Body>
  <pub:dataRequest type="station">
   <pub:parameters>
    <pub:selection>AMT</pub:selection>
    <pub:selection>VIA</pub:selection>
   </pub:parameters>
  </pub:dataRequest>
 </soapenv:Body>
</soapenv:Envelope>
```

## Loyalty cards

Amtrak offers the ability to collect rewards through loyalty program(s).  The first and last name on the order must match the name on the loyalty card for the loyalty card to be accepted.

See [Content codes page](/v1/docs/content-codes#loyalty-cards){target="_blank"} for a list of loyalty programs for all Suppliers, along with test identifier numbers.  You can also retrieve a list of valid loyalty programs through the [Published Data API](/v1/docs/published-data-messages#Loyalty_cards){target="_blank"}.

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
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/">
 <SOAP-ENV:Header/>
 <SOAP-ENV:Body>
  <pub:dataResponse conversationToken="8feed3f04ad93dc7"
   dataPackaged="2019-06-17T14:47:52" lastChanged="2012-10-01"
   silverCoreVersion="2.125.0" type="loyaltyCard" version="1.0"
   xmlns:pub="http://railgds.net/ws/publishing">
   <pub:requestStatus systemId="S1KMwg">
    <pub:success>true</pub:success>
   </pub:requestStatus>
   <pub:loyaltyCard description="Amtrak Guest Rewards"
    marketingCarrier="Amtrak" program="Amtrak"/>
   <pub:loyaltyCard description="Grand Voyageur" marketingCarrier="BUS"
    program="FR_GR_VOY"/>
   <pub:loyaltyCard description="Grand Voyageur Plus" marketingCarrier="BUS"
    program="FR_GR_VOY_PL"/>
   <pub:loyaltyCard description="Other SNCF card" marketingCarrier="BUS"
    program="FR_OTHER"/>
   <pub:loyaltyCard description="Voyageur" marketingCarrier="BUS"
    program="FR_VOY"/>
   <pub:loyaltyCard description="Grand Voyageur Le Club"
    marketingCarrier="BUS" program="FR_VOY_CLUB"/>
   <pub:loyaltyCard description="BahnCard 25, 1. Klasse (incl. RAILPLUS)"
    marketingCarrier="DB" program="DB_BAHNCARD25-1"/>
   ....
  </pub:dataResponse>
 </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

The presence of a `loyaltyCard` element in the [pointToPointShoppingResponse](/v1/docs/pointtopointshoppingresponse-elements){target="_blank"} message indicates that the loyalty program is available for a particular Leg Solution.

**Example**:
```XML
<legSolution legSolutionID="LS_1_3">
 <numberOfConnections>0</numberOfConnections>
 <travelSegments>
  <travelSegment sequence="0" travelSegmentID="LS_1_3_TS_0" type="TRAIN">
   <originTravelPoint type="STATION">USNYP</originTravelPoint>
   <destinationTravelPoint type="STATION">USWAS</destinationTravelPoint>
   <departureDateTime>2019-08-05T10:03:00</departureDateTime>
   <arrivalDateTime>2019-08-05T13:00:00</arrivalDateTime>
   <designator>2153</designator>
   <marketingCarrier>Amtrak</marketingCarrier>
   <operatingCarrier>Amtrak</operatingCarrier>
   <supplierEquipmentType>Amtrak-HSP</supplierEquipmentType>
   <duration>P0Y0M0DT2H57M0S</duration>
   <crossBorderInfo>false</crossBorderInfo>
   <equipmentType code="HSP">High-Speed</equipmentType>
   <marketingServiceName>Acela Express</marketingServiceName>
   <marketingInformation serviceCode="AcelaExpress"/>
  </travelSegment>
 </travelSegments>
 <overtakenJourney>false</overtakenJourney>
 <duration>P0Y0M0DT2H57M0S</duration>
 <passengerInformationRequired>
  <passengerInformation type="PASSENGER_NAME" allPassengers="true"/>
 </passengerInformationRequired>
 <loyaltyCards>
  <loyaltyCard program="Amtrak" description="Amtrak Guest Rewards">
   <marketingCarrier>Amtrak</marketingCarrier>
  </loyaltyCard>
 </loyaltyCards>
</legSolution>
```

Whether or not the loyalty card applies to an individual Fare Code is determined by the `rewardsEligible` element.

**Example**:
```XML
<fareCode code="PK-PD">
 <serviceClass>FIRST</serviceClass>
 <travelSegmentIDRef>LS_1_10_TS_0</travelSegmentIDRef>
 <cabinClass>First Class Seat</cabinClass>
 <rewardsEligible>YES</rewardsEligible>
 <fareClass>PREMIUM</fareClass>
 <fareDisplayName>Premium</fareDisplayName>
 <openReturn>NO</openReturn>
 <reservable>NOT_POSSIBLE</reservable>
 <fareExpirationDateTime>2019-08-05T17:00:00Z</fareExpirationDateTime>
 <seatsAvailable>39</seatsAvailable>
 <amenities>
  <amenity type="INTERNET">WiFi access (Free)</amenity>
  <amenity type="LOUNGE_ACCESS">Lounge Access at Station</amenity>
  <amenity type="POWER_SOCKET">Power Socket at Seat</amenity>
  <amenity type="QUIET">Quiet Car or Quiet Zone</amenity>
  <amenity type="SNACK">Snack service</amenity>
 </amenities>
 <fareApplicabilities>
  <fareApplicability outbound="RESTRICTED" type="SCHEDULE"/>
 </fareApplicabilities>
</fareCode>
```

If the chosen Leg Solution offers loyalty cards and `rewardsEligible` is `"YES"` for the Fare Code being booked, you can submit the loyalty card number and program via the `passenger` element in the [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements){target="_blank"} message.

**Example**:
```XML
<passenger passengerID="PAX_SPEC_0">
 <nameFirst>Jane</nameFirst>
 <nameLast>Smith</nameLast>
 <contactInformation>
  <contact>
   <contactType>BUSINESS</contactType>
   <contactMedium>PHONE</contactMedium>
   <contactInfo>5085551212</contactInfo>
  </contact>
  <contact>
   <contactType>BUSINESS</contactType>
   <contactMedium>EMAIL</contactMedium>
   <contactInfo>email@email.com</contactInfo>
  </contact>
 </contactInformation>
 <ageAtTimeOfTravel>40</ageAtTimeOfTravel>
 <loyaltyCards>
  <loyaltyCard program="Amtrak">7005057802</loyaltyCard>
 </loyaltyCards>
</passenger>
```

## Discount cards

Amtrak offers several different discount cards, submittable through the `fareQualifiers` element in the [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements){target="_blank"}.

You can retrieve a list of valid discount cards by setting `type="fareQualifier"` in your [Published Data](/v1//docs/published-data-messages#fare-qualifiers){target="_blank"} request, as shown in the following example.

**Example**:
```XML
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:pub="http://railgds.net/ws/publishing">
   <soapenv:Header/>
   <soapenv:Body>
      <pub:dataRequest type="fareQualifier"/>
   </soapenv:Body>
</soapenv:Envelope>
```

The response returns a list of valid discount cards.

**Example response**:
```XML
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/">
 <SOAP-ENV:Body>
  <pub:dataResponse xmlns:pub="http://railgds.net/ws/publishing"
   conversationToken="8c0f7e5a16df133f" lastChanged="2013-01-01"
   type="fareQualifier" version="1.0">
   <pub:requestStatus systemId="ZGRmVU">
    <pub:success>true</pub:success>
   </pub:requestStatus>
   ....
   <pub:fareQualifier description="Amtrak Corporate Discount" isInput="YES"
    program="Amtrak" type="CORPORATE"/>
   ....
  </pub:dataResponse>
 </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

## Corporate discounts

To request Amtrak corporate discounts, include a Fare Qualifier of type `CORPORATE` in your [pointToPointShoppingRequest](/v1/docs/pointtopointshoppingrequest-elements){target="_blank"}.

**Example**:
```XML
<shop:fareQualifiers>
 <shop:fareQualifier type="CORPORATE">
  <com:program>Amtrak</com:program>
  <com:identifier>11955512</com:identifier>
 </shop:fareQualifier>
</shop:fareQualifiers>
```

:::(Warning) (Partners should note that Amtrak currently requires that the Corporate Id `identifier` consists of eight digits.  Amtrak validates this requirement at booking time.)
:::

The presence of a `fareQualifier` element of type `CORPORATE` in the resulting [pointToPointShoppingResponse](/v1/docs/pointtopointshoppingresponse-elements){target="_blank"} message indicates that a corporate discount is available for a particular Ticketable Fare.

**Example**:
```XML
<ticketableFare>
 <totalPrice currency="USD">248.00</totalPrice>
 <passengerReferences>
  ....
 </passengerReferences>
 <prices>
  <price type="TICKETED_RES" currency="USD">248.00</price>
 </prices>
 <commissions>
  <commission type="DISTRIBUTOR" currency="USD">0.00</commission>
 </commissions>
 <rules>
  ....
 </rules>
 <fareQualifiers>
  <fareQualifier type="CORPORATE" fareQualifierID="GFQ_PRICE_LS_1_3_8_0_0"
   authorizationNumberRequired="false" identifierRequired="true">
   <program>Amtrak</program>
   <identifier>11955512</identifier>
  </fareQualifier>
 </fareQualifiers>
 <ticketingOptionsAvailable TOD="true" ETK="false" PAH="false" PRN="false"
  EML="true" DEPARTURE_STATION_TOD="true" SMS="false"/>
 <fareOrigin type="STATION">USWAS</fareOrigin>
 <fareDestination type="STATION">USBBY</fareDestination>
 <seatsAvailable>134</seatsAvailable>
</ticketableFare>
```

To use the corporate discount, include the Fare Qualifier data with the Ticketable Fare in your [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements){target="_blank"} message.

**Example**:
```XML
<ticketableFare>
 <totalPrice currency="USD">242.48</totalPrice>
 <passengerReferences>
  ....
 </passengerReferences>
 <prices>
  ....
 </prices>
 <fareQualifiers>
  <fareQualifier authorizationNumberRequired="false"
   fareQualifierID="GFQ_PRICE_LS_1_3_8_0_0" identifierRequired="true"
   type="CORPORATE">
   <program>Amtrak</program>
   <identifier>11955512</identifier>
  </fareQualifier>
 </fareQualifiers>
</ticketableFare>
```

Because Amtrak prefers to capture the corporate Id with every booking regardless of whether or not a discount was applied, you need to pass in a corporate Fare Qualifier even if the shopping response didn't indicate that a discount was available for the Ticketable Fare.  In this case, the `fareQualifers` element goes at the booking level of the [createBookingRecordRequest ](/v1/docs/createbookingrecordrequest-elements){target="_blank"} message rather than inside a Ticketable Fare.

**Example**:
```
    ....
    <book:fareQualifiers>
     <book:fareQualifier type="CORPORATE">
     <program>Amtrak</program>
     <identifier>11955512</identifier>
    </book:fareQualifier>
   </book:fareQualifiers>
  </book:createBookingRecordRequest>
 </soapenv:Body>
</soapenv:Envelope>
```

## Passenger information required at booking

SilverCore requires the first name, last name, age, phone number, and e-mail address of at least one passenger at the time of booking.  You must also provide the age of any other passengers on the booking.

However, because each carrier can require additional passenger information, which is subject to change, you should always check the `passengerInformationRequired` element for each Leg Solution in the shopping response to see what additional information you need to provide.

**Example**:
```XML
<passengerInformationRequired>
 <passengerInformation type="PASSENGER_NAME" allPassengers="true"/>
</passengerInformationRequired>
```

## Optional prices for accommodation

Amtrak offers accommodations (sleepers) for many long-distance routes at a cost above and beyond the base ticket price.

For detailed information on how to create a booking with optional accommodations, see [Handle optional prices in ticketable fares](/v1/docs/handling-optional-prices-in-ticketable-fares){target="_blank"}.

## Cross-border routes

Amtrak runs service on some routes that connect the US and Canada.  Due to the border crossing, regulations require that certain passenger information be obtained at the time of booking.  To find out whether this information is required for a particular booking, check the `passengerInformationRequired` element for each Leg Solution in the [pointToPointShoppingResponse](/v1/docs/pointtopointshoppingresponse-elements){target="_blank"}.

**Example**:
```XML
<legSolution legSolutionID="LS_1_1">
 <numberOfConnections>0</numberOfConnections>
 <travelSegments>
  <travelSegment sequence="0" travelSegmentID="LS_1_1_TS_0" type="BUS">
   <originTravelPoint type="STATION">USSEA</originTravelPoint>
   <destinationTravelPoint type="STATION">CAXEA</destinationTravelPoint>
   <departureDateTime>2019-08-05T10:45:00</departureDateTime>
   <arrivalDateTime>2019-08-05T14:15:00</arrivalDateTime>
   <designator>8948</designator>
   <marketingCarrier>Amtrak</marketingCarrier>
   <operatingCarrier>Amtrak</operatingCarrier>
   <supplierEquipmentType>Amtrak-BUS</supplierEquipmentType>
   <duration>P0Y0M0DT3H30M0S</duration>
   <crossBorderInfo>true</crossBorderInfo>
   <equipmentType code="BUS">Bus</equipmentType>
   <marketingServiceName>Bus</marketingServiceName>
   <marketingInformation serviceCode="Amtrak"/>
  </travelSegment>
 </travelSegments>
 <overtakenJourney>false</overtakenJourney>
 <duration>P0Y0M0DT3H30M0S</duration>
 <passengerInformationRequired>
  <passengerInformation type="PASSENGER_NAME" allPassengers="true"/>
  <passengerInformation type="GENDER" allPassengers="true"/>
  <passengerInformation type="DATE_OF_BIRTH" allPassengers="true"/>
  <passengerInformation type="RESIDENCE_INFORMATION" allPassengers="true"/>
  <passengerInformation type="TRAVEL_DOCUMENTS" allPassengers="true">
   <acceptableFormOfTravelDocument>MILORDER</acceptableFormOfTravelDocument>
   <acceptableFormOfTravelDocument>NATURALCERT</acceptableFormOfTravelDocument>
   <acceptableFormOfTravelDocument>PASSPORT</acceptableFormOfTravelDocument>
   <acceptableFormOfTravelDocument>PERMRESIDENT</acceptableFormOfTravelDocument>
  </passengerInformation>
 </passengerInformationRequired>
 <loyaltyCards>
  <loyaltyCard program="Amtrak" description="Amtrak Guest Rewards">
   <marketingCarrier>Amtrak</marketingCarrier>
  </loyaltyCard>
 </loyaltyCards>
</legSolution>
```

If cross-border traveler information is required, submit it using the `passenger` element in the [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements){target="_blank"}.

**Example**:
```XML
<passenger passengerID="P-JUW0000KQ-0">
 <nameFirst>Jane</nameFirst>
 <nameLast>Smith</nameLast>
 <dob>1990-07-20</dob>
 <gender>FEMALE</gender>
 <residenceInformation>
  <countryCode>US</countryCode>
 </residenceInformation>
 <contactInformation>
  <contact>
   <contactType>BUSINESS</contactType>
   <contactMedium>PHONE</contactMedium>
   <contactInfo>5085551212</contactInfo>
  </contact>
  <contact>
   <contactType>BUSINESS</contactType>
   <contactMedium>EMAIL</contactMedium>
   <contactInfo>email@email.com</contactInfo>
  </contact>
 </contactInformation>
 <ageAtTimeOfTravel>40</ageAtTimeOfTravel>
 <travelDocuments>
  <travelDocument>
   <documentNumber>123456789</documentNumber>
   <documentType>PASSPORT</documentType>
   <expirationDate>2024-08-16</expirationDate>
   <issuingAuthority>
    <countryCode>US</countryCode>
   </issuingAuthority>
  </travelDocument>
 </travelDocuments>
</passenger>
```

## Ticket Delivery Options

Amtrak supports the following ticket delivery options:

- **Pickup-On-Departure** - To pick up on departure, the traveler needs a credit card.  Depending on the station, passengers can get a ticket at a ticket vending machine, a ticket window, or from a train conductor.
- **Mail** - Amtrak is responsible for sending the tickets for mail delivery.  Amtrak does not submit tracking information to SilverCore when a customer selects a by-mail option.
- **E-mail**

To find out which delivery options are available for a particular fare, check the `ticketingOptionsAvailable` element for each Point-To-Point Price in the [pointToPointShoppingResponse](/v1/docs/pointtopointshoppingresponse-elements){target="_blank"} message.

**Example**:
```XML
<ticketingOptionsAvailable TOD="true" ETK="false" PAH="false" PRN="true"
 EML="false" DEPARTURE_STATION_TOD="true" SMS="false" XVD="true" SCT="false"/>
```
For more information about integrating Ticket Delivery Options into your application, see [Specifying Ticket Delivery Options](/v1/docs/specifying-ticket-delivery-options){target="_blank"}.

### Checking details for each supported delivery option

Once you've created a booking, see the `ticketingOptions` element in the Booking Record.  The `ticketingOptions` container element has one `ticketingOption` element for each supported TDO, which provides more details about each TDO choice.  The `ticketingOptions` element is also included in the [validateBookingRecordInformationResponse](/v1/docs/validatebookingrecordinformationresponse-elements){target="_blank"} so you can see which TDOs are available and for what cost before you create a reservation in your system.

**Example**:
```XML
<ticketingOptions>
 <ticketingOption code="EML" type="E_MAIL">
  <description>Ticket by Email</description>
  <fee isRefundable="false" currency="CAD">0.00</fee>
 </ticketingOption>
</ticketingOptions>
```

## Payment and confirmation

Amtrak's payment and confirmation process varies depending on whether the customer is inside or outside the US.

:::(Info) (You're not required to supply any passenger Id details for ticket pickup, indicated by the `confirmationInformationRequired` flag set to false in the Booking Record.)
:::

### Customers inside the US using USD

For Amtrak bookings made by customers located inside the US using USD, Amtrak acts as a pass-through supply channel in which they are the merchant of record.  In this scenario, the booking is automatically confirmed at the time of a successful [addPaymentRequest](/v1/docs/addpaymentrequest-elements){target="_blank"}, as indicated by the `confirmationUponAddPayment` flag set to `true` in the Booking Record.

### Customers outside the US not using USD

For Amtrak bookings made by customers outside the US using non-USD currency, SilverRail or the Partner can act as the merchant of record.  In this scenario, the booking isn't confirmed automatically at the time of a successful [addPaymentRequest](/v1/docs/addpaymentrequest-elements){target="_blank"}, as indicated by the `confirmationUponAddPayment` flag set to `false` in the Booking Record.

To confirm the booking, you can use one of two options:

- For single-order bookings, perform an Add Payment with Confirm by including a `confirmBooking=true` parameter in the `addPaymentRequest` message.

    **Example**:
    ```XML
    <parameters>
     <confirmBooking>true</confirmBooking>
    </parameters>
    ```

- For bookings containing multiple orders, send a separate [confirmBookingRecordRequest](/v1/docs/confirmbookingrecordrequest-elements){target="_blank"} message immediately after a successful [addPaymentRequest](/v1/docs/addpaymentrequest-elements){target="_blank"}.  You can also confirm single-order bookings this way if you want to do so independently of the `addPaymentRequest`.

## Cancellation and refunds

### Customers located within the US using USD

For Amtrak bookings made by customers in the US using USD, Amtrak acts as the merchant of record.  In this scenario, a successful [cancelBookingRecordRequest](/v1/docs/cancelbookingrecordrequest-elements){target="_blank"} will result in an automatic refund from Amtrak's payment processing system, indicated by the `refundIsProcessedOnCancel` flag set to `true` in the Booking Record.

### Customers located outside the US using non-USD

For Amtrak bookings made by customers outside the US using non-USD currency, SilverRail or the Partner can act as the merchant of record.  When SilverRail acts as the merchant of record, this is commonly known as an Indirect Form of Payment (IFOP).

In this scenario, a successful cancelBookingRecordRequest doesn't result in an automatic refund from the payment processing system, indicated by the `refundIsProcessedOnCancel` flag set to `false` in the Booking Record.

Instead, you need to manually request a refund by submitting a separate [refundBookingRecordRequest](/v1/docs/refundbookingrecordrequest-elements){target="_blank"} after you've done a successful cancelBookingRecordRequest.

## Carrier logos

[Amtrak carrier logos.zip](https://files.document360.io/c53b2e89-8de3-47ad-adf4-5a70ab761260/Images/Documentation/Amtrak_Carrier_Logos.zip)
