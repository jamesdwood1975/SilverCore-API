:::(Error) (**In this article**, we will summarize the areas of SilverCore functionality where the VIA Rail has specific requirements or restrictions above and beyond what is common to all Suppliers.)
:::

## Available SilverCore API messages

The following SilverCore API messages are **not** available for Via Rail:

- travelPassShoppingRequest&#42;&#185;
- getIntermediateTravelPointsRequest
- authenticatePayerRequest&#42;&#185;
- confirmBookingRecordRequest&#42;&#178;
- exchangeSearchRequest
- retrieveExchangeSummaryRequest
- processExchangeRequest
- claimValueDocumentRequest
- refundBookingRecordRequest&#42;&#178;

:::(Info) (&#42;&#185; Available in certain circumstances when SilverRail is the merchant of record and only for international customers (non-CAD currency).)
:::

:::(Info) (&#42;&#178; Only for international customers (non-CAD currency).)
:::

## Accreditation

SilverRail runs a comprehensive suite of tests against your web application to ensure all accreditation standards are met before a production launch.

See [Implementation process](/v1/docs/accreditation-process){target="_blank"} for more information about our accreditation process.

For information about the accreditation requirements for each Supply Channel, please see [Functional requirements summary](/v1/docs/accreditation-requirements-summary){target="_blank"}.  See [Sample implementation tests](/v1/docs/via-rail-accreditation-tests){target="_blank"} for details of the tests we perform when evaluating a Partner's implementation for the VIA Rail supply channel.

## Station codes

A complete list of VIA Rail station data codes is available through [Published Data API](/v1/docs/published-data-messages#station){target="_blank"}, using `VIA` as the selection parameter.

**Example**:

```XML
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:pub="http://railgds.net/ws/publishing">
   <soapenv:Header/>
   <soapenv:Body>
      <pub:dataRequest type="station">
         <pub:parameters>
            <pub:selection>VIA</pub:selection>
         </pub:parameters>
      </pub:dataRequest>
   </soapenv:Body>
</soapenv:Envelope>
```

The station file structure for VIA Rail stations is the same as all other suppliers.

**Example**:
```XML
<pub:station code="CAAAS" countryCode="CA" fareStation="OUTPUT_ONLY" itineraryStation="YES" latitude="49.239555" longitude="-121.764243" mainStation="NO" name="Agassiz" stateProvinceCode="BC" timeZone="Canada/Pacific" type="Station">
  <pub:supplier alias="AGAS" codeType="Viarail Code" source="VIA" type="CODE"/>
  <pub:supplier alias="Agassiz" source="VIA" type="ALIAS"/>
  <pub:supplier alias="British Columbia" source="VIA" type="ALIAS"/>
  <pub:supplier alias="Colombie-Britannique" source="VIA" type="ALIAS"/>
</pub:station>
```

## Shared stations

A shared station is a station serviced by two carriers because it is located on a route crossing an international border.
For example, Vancouver is serviced by both VIA Rail and Amtrak.

**Example**:
```XML
<pub:station code="CAXEA" countryCode="CA" fareStation="OUTPUT_ONLY" itineraryStation="YES" latitude="49.273757" longitude="-123.098336" mainStation="NO" name="Vancouver" stateProvinceCode="BC" timeZone="Canada/Pacific" type="Station">
  <pub:supplier alias="VAC" codeType="Amtrak Code" source="AMT" type="CODE"/>
  <pub:supplier alias="Vancouver, British Columbia, Canada" source="AMT" type="ALIAS"/>
  <pub:supplier alias="British Columbia" source="VIA" type="ALIAS"/>
  <pub:supplier alias="Colombie-Britannique" source="VIA" type="ALIAS"/>
  <pub:supplier alias="Vancouver" source="VIA" type="ALIAS"/>
  <pub:supplier alias="VCVR" codeType="Viarail Code" source="VIA" type="CODE"/>
</pub:station>
```

The station code for a shared station is the same for both Suppliers, and SilverCore automatically determines which one to use when you submit your shopping request.

To retrieve a list of all stations in the same response with the indication of which stations are shared, use two `<selection>` parameters with the station retrieval request.

**Example**:
```XML
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:pub="http://railgds.net/ws/publishing">
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

VIA Rail offers the ability to collect rewards through their loyalty program(s).  The first and last name on the order must match the name on the loyalty card for the loyalty card to be accepted.

See [Content codes page](/v1/docs/content-codes#loyalty-cards){target="_blank"} for a list of loyalty programs for all Suppliers, along with test identifier numbers.  You can also retrieve a list of valid loyalty programs through the Published Data API.

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
<pub:loyaltyCard description="Via Préférence" marketingCarrier="ViaRail" program="VIA"/>
```

The presence of a `<loyaltyCard>` element in the [pointToPointShoppingResponse](/v1/docs/pointtopointshoppingresponse-elements#code-item-loyaltyCard){target="_blank"} message indicates that the loyalty program is available for a particular Leg Solution.

**Example**:
```XML
<legSolution legSolutionID="LS_1_1">
  <numberOfConnections>0</numberOfConnections>
  <travelSegments>
    <travelSegment sequence="0" travelSegmentID="LS_1_1_TS_0" type="TRAIN">
      <originTravelPoint type="STATION">CAYMY</originTravelPoint>
      <destinationTravelPoint type="STATION">CAYBZ</destinationTravelPoint>
      <departureDateTime>2023-02-02T06:20:00</departureDateTime>
      <arrivalDateTime>2023-02-02T13:03:00</arrivalDateTime>
      <designator>51</designator>
      <marketingCarrier>ViaRail</marketingCarrier>
      <operatingCarrier>ViaRail</operatingCarrier>
      <supplierEquipmentType>ViaRail-BLR</supplierEquipmentType>
      <duration>P0Y0M0DT6H43M0S</duration>
      <crossBorderInfo>false</crossBorderInfo>
      <equipmentType code="BLR">Branch-Line/Regional</equipmentType>
      <marketingServiceName>Service from Montreal to Toronto</marketingServiceName>
      <marketingInformation serviceCode="ViaRail"/>
    </travelSegment>
  </travelSegments>
  <overtakenJourney>false</overtakenJourney>
  <duration>P0Y0M0DT6H43M0S</duration>
  <passengerInformationRequired>
    <passengerInformation type="PASSENGER_ADDRESS" allPassengers="false"/>
    <passengerInformation type="PASSENGER_NAME" allPassengers="true"/>
  </passengerInformationRequired>
  <availableSeatPreferences>
    <availableSeatPreference marketingCarrier="ViaRail" serviceClass="SECOND" supplierEquipmentType="ViaRail-BLR">AISLE</availableSeatPreference>
    <availableSeatPreference marketingCarrier="ViaRail" serviceClass="SECOND" supplierEquipmentType="ViaRail-BLR">WINDOW</availableSeatPreference>
    <availableSeatPreference marketingCarrier="ViaRail" serviceClass="THIRD" supplierEquipmentType="ViaRail-BLR">AISLE</availableSeatPreference>
    <availableSeatPreference marketingCarrier="ViaRail" serviceClass="THIRD" supplierEquipmentType="ViaRail-BLR">WINDOW</availableSeatPreference>
  </availableSeatPreferences>
  <loyaltyCards>
    <loyaltyCard program="VIA" description="Via Préférence">
      <marketingCarrier>ViaRail</marketingCarrier>
    </loyaltyCard>
  </loyaltyCards>
</legSolution>
```


Whether or not the loyalty card applies to an individual Fare Code is determined by the `<rewardsEligible>` element.

**Example**:
```XML
<fareCode code="J-LRC-20230202-1">
  <serviceClass>SECOND</serviceClass>
  <travelSegmentIDRef>LS_1_1_TS_0</travelSegmentIDRef>
  <cabinClass>Business</cabinClass>
  <rewardsEligible>YES</rewardsEligible>
  <fareClass>BUSINESS_PLUS</fareClass>
  <fareDisplayName>Business Plus</fareDisplayName>
  <openReturn>NO</openReturn>
  <reservable>INCLUDED</reservable>
  <fareExpirationDateTime>2023-02-02T11:20:00Z</fareExpirationDateTime>
  <amenities>
    <amenity type="INTERNET">WiFi access (Free)</amenity>
    <amenity type="LOUNGE_ACCESS">Lounge Access at Station</amenity>
    <amenity type="MEAL">Meal included</amenity>
  </amenities>
  <fareApplicabilities>
    <fareApplicability outbound="RESTRICTED" type="SCHEDULE"/>
  </fareApplicabilities>
</fareCode>
```


If the chosen Leg Solution offers loyalty cards and the Fare Code being booked also has `<rewardsEligible>YES</rewardsEligible>`, you can submit the loyalty card number and program via the `<passenger>` element in the [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements#code-item-book-passenger){target="_blank"} message.

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
  <loyaltyCard program="VIA">1234567</loyaltyCard>
 </loyaltyCards>
</passenger>
```

## Corporate Discounts**

To request SNCF corporate discounts, include a fare qualifier of type `CORPORATE` in your [pointToPointShoppingRequest](/v1/docs/pointtopointshoppingrequest-elements){target="_blank"} message.

**Example**:
```XML
<shop:fareQualifiers>
 <shop:fareQualifier type="CORPORATE">
  <program>VIA</program>
  <identifier>800704</identifier>
 </shop:fareQualifier>
</shop:fareQualifiers>
```

If `<fareQualifier type="CORPORATE">` in the resulting `pointToPointShoppingResponse`, a corporate discount is available for a particular Ticketable Fare.

**Example**:
```XML
<ticketableFare>
 <totalPrice isEstimated="true" currency="CAD">262.89</totalPrice>
 <passengerReferences>...</passengerReferences>
 <prices>...</prices>
 <commissions>...</commissions>
 <rules>...</rules>
 <fareQualifiers>
  <fareQualifier type="CORPORATE" fareQualifierID="GFQ_PRICE_P_1_16_0_0"
   authorizationNumberRequired="false" identifierRequired="true">
   <program>VIA</program>
   <identifier>800704</identifier>
  </fareQualifier>
 </fareQualifiers>
 <ticketingOptionsAvailable TOD="false" ETK="false" PAH="false" PRN="false"
  EML="true" DEPARTURE_STATION_TOD="false" SMS="false"/>
 <fareOrigin type="STATION">CAYMY</fareOrigin>
 <fareDestination type="STATION">CAYBZ</fareDestination>
</ticketableFare>
```

Include the fare qualifier data with the Ticketable Fare in your `createBookingRecordRequest` message to use the corporate discount.

**Example**:
```XML
<ticketableFare>
 <totalPrice isEstimated="true" currency="CAD">262.89</totalPrice>
 <passengerReferences>
  ...
 </passengerReferences>
 <prices>
  ...
 </prices>
 <fareQualifiers>
  <fareQualifier type="CORPORATE" fareQualifierID="GFQ_PRICE_P_1_16_0_0"
   authorizationNumberRequired="false" identifierRequired="true">
   <program>VIA</program>
   <identifier>800704</identifier>
  </fareQualifier>
 </fareQualifiers>
</ticketableFare>
```

## Taxes

VIA Rail accreditation requires displaying all relevant taxes to the customer when booking a fare.  VIA does not provide this tax information at the shopping stage, so SilverCore internally calculates the taxes when you submit a shopping request and sets the `<isEstimated>` flag in the relevant price to indicate the amount is an estimate rather than coming directly from the Supplier.

Generally, the price rarely changes between shopping and booking.  If a change does occur, it's generally a minimal amount (typically $.01).

**Example**:
```XML
<prices>
 <price type="TICKETED_RES" currency="CAD">228.65</price>
 <price type="TAX_NATIONAL" isEstimated="true" description="GST/HST"
  currency="CAD">11.43</price>
 <price type="TAX_STATEPROVINCE" isEstimated="true" description="PST"
  currency="CAD">22.81</price>
</prices>
```
To prevent the booking from failing due to a difference between the estimated price in shopping and the actual amount returned in the booking, you can set the `<priceAcceptance>` element in the [validateBookingRecordInformationRequest](/v1/docs/validatebookingrecordinformationrequest-elements#code-item-book-parameters){target="_blank"} and [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements#code-item-book-priceAcceptance){target="_blank"}.

**Example**:
```XML
<book:priceAcceptance>
 <book:acceptAny>true</book:acceptAny>
</book:priceAcceptance>
```

## Seat reservations

Seat reservations for VIA Rail are automatically included whenever they are available.  Where this is the case, you'll see `<reservable>` set to `INCLUDED` in the Fare Code.

**Example**:
```XML
<fareCode code="J-LRC-20190902-1" fareCodeID="FC-LNK00027M-GXT000001-0_0">
 <serviceClass>SECOND</serviceClass>
 <travelSegmentIDRef>TS-LNK00027M-GXT000001-1</travelSegmentIDRef>
 <cabinClass>Business</cabinClass>
 <rewardsEligible>YES</rewardsEligible>
 <fareClass>BUSINESS_PLUS</fareClass>
 <fareDisplayName>Business Plus</fareDisplayName>
 <openReturn>NO</openReturn>
 <reservable>INCLUDED</reservable>
  ...
 </fareCode>
```

## Seat preferences

You can request seat preferences, such as seat direction, as part of the reservation.  See [Content codes](/v1/docs/content-codes){target="_blank"} for a complete list of choices.

Seat preferences are always optional, and there is no guarantee the Carrier will honor the request.  If you include mutually exclusive choices, for example, `WINDOW` and `AISLE`, SilverCore will give precedence to the first value passed in.

Because the available seat preferences vary between different Carriers in a Leg Solution, you'll need to compare the Fare Code's `<marketingCarrier>`, `<serviceClass>`, and `<supplierEquipmentType>` values against the `<availableSeatPreferences>` element in the shopping response to determine which preferences are available.

**Example**:
```XML
<availableSeatPreferences>
  <availableSeatPreference marketingCarrier="ViaRail" serviceClass="SECOND"  supplierEquipmentType="ViaRail-BLR">AISLE</availableSeatPreference>
  <availableSeatPreference marketingCarrier="ViaRail" serviceClass="SECOND"  supplierEquipmentType="ViaRail-BLR">WINDOW</availableSeatPreference>
  <availableSeatPreference marketingCarrier="ViaRail" serviceClass="THIRD"  supplierEquipmentType="ViaRail-BLR">AISLE</availableSeatPreference>
  <availableSeatPreference marketingCarrier="ViaRail" serviceClass="THIRD"  supplierEquipmentType="ViaRail-BLR">WINDOW</availableSeatPreference>
</availableSeatPreferences>
```

To request a seat preference, add a `<seatPreferences>` element to the [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements#code-item-seatPreferences){target="_blank"}.

**Example**:
```XML
<seatPreferences>
 <seatPreference>AISLE</seatPreference>
</seatPreferences>
```

:::(Info) (VIA Rail only supports one seat preference per passenger and applies that preference to every Leg of the journey for which reservations are available.  If you specify multiple seat preferences for a passenger, or different preferences for each Leg, SilverCore will use the first value passed in.)
:::

## Assigned seats

After you pay for a VIA Rail order that includes a seat reservation and optional seat preferences, SilverCore adds a descriptive `<seatAssignment>` element to each affected Fare Code in the Booking Record.

**Example**:
```XML
<seatAssignment>
 <coach>1</coach>
 <number>7B</number>
 <direction>UNKNOWN</direction>
</seatAssignment>
```

:::(Info) (Unlike some other Suppliers, VIA Rail doesn't echo the seat preference in the `<seatAssignment>` element.)
:::

## Passenger information required at booking

SilverCore requires the first name, last name, age, phone number, and email address of at least one passenger at the time of booking.  You must also provide the age of any other passengers on the booking.  However, because each Carrier can require additional passenger information, and this information is subject to change, you should always check the `<passengerInformationRequired>` element for each Leg Solution in the shopping response to see what additional information you'll need to provide.

For example, if the Carrier requires the name of all passengers at the time of booking, the response will include `allPassengers="true"`.

**Example**:
```XML
<passengerInformationRequired> <passengerInformation type="PASSENGER_NAME" allPassengers="true"/> </passengerInformationRequired>
```

## Ticket Delivery Options

SilverCore supports the following VIA Rail Ticket Delivery Options:

- Email (EML)

To find out which delivery options are available for a particular fare during the shopping process, check the `<ticketingOptionsAvailable>` element for each Point-To-Point Price in your [pointToPointShoppingResponse](/v1/docs/pointtopointshoppingresponse-elements#code-item-ticketingOptionsAvailable){target="_blank"}.

**Example**:
```XML
<ticketingOptionsAvailable TOD="false" ETK="false" PAH="false" PRN="false" EML="true" DEPARTURE_STATION_TOD="false" SMS="false"/>
```

For more information about integrating ticket delivery options into your application, please see [Specifying Ticket Delivery Options](/v1/docs/specifying-ticket-delivery-options){target="_blank"}.

### Checking details for each supported delivery option

The `<ticketingOptions>` element is also included in the [validateBookingRecordInformationResponse](/v1/docs/validatebookingrecordinformationresponse-elements#code-item-ns2-ticketingOptions){target="_blank"} so you can see which TDOs are available and for what cost before you create a reservation in your system.

**Example**:
```XML
<ticketingOptions>
 <ticketingOption code="EML" type="E_MAIL">
  <description>Ticket by Email</description>
  <fee isRefundable="false" currency="CAD">0.00</fee>
 </ticketingOption>
</ticketingOptions>
```

### Confirmation email

When selecting a ticketing option through the [updateBookingRecordRequest](/v1/docs/updatebookingrecordrequest-elements){target="_blank"}, you don't need to include the email address&mdash;VIA Rail uses the email address you supplied in the original [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements#code-item-email){target="_blank"}.

VIA Rail sends two emails (at the same time) to the customer as soon as the order is paid and confirmed (SilverCore order status of CONFIRMED):

- The first email is a receipt that shows the itinerary, fare information, tax information, refund/exchange conditions, baggage allowance, disclaimers, and useful links.
- The second email contains the ticket in PDF format.

**Sample booking confirmation email**:
![Picture 1.png](https://files.document360.io/c53b2e89-8de3-47ad-adf4-5a70ab761260/Images/Documentation/Picture%201%282%29.png){height="" width=""}


**Sample E-Ticket/Boarding Pass**
![Picture 2.png](https://files.document360.io/c53b2e89-8de3-47ad-adf4-5a70ab761260/Images/Documentation/Picture%202%281%29.png){height="" width=""}

## Payment and confirmation

The payment and confirmation process for VIA Rail varies depending on whether the customer is located inside or outside Canada.

:::(Info) (You are not required to supply a passenger ID for ticket pickup (indicated by the `<confirmationInformationRequired>false</confirmationInformationRequired>` in the [Booking Record](/v1/docs/en/validatebookingrecordinformationresponse-elements#code-item-ns2-confirmationInformationRequired){target="_blank"}.
:::

### Customers inside Canada using CAD

For VIA Rail bookings made by customers located inside Canada using CAD currency, VIA Rail acts as a pass-through supply channel and merchant of record.  In this scenario, the booking is automatically confirmed at the time of a successful [addPaymentRequest](/v1/docs/addpaymentrequest-elements){target="_blank"}, as indicated by `<confirmationUponAddPayment>true</confirmationUponAddPayment>` in the [Booking Record](/v1/docs/retrievebookingrecordresponse-elements#code-item-confirmationUponAddPayment){target="_blank"}.

### Customers outside Canada not using CAD

For VIA Rail bookings made by customers located outside Canada using non-CAD currency, SilverRail or the partner can act as the merchant of record.  When SilverRail acts as the merchant of record, this is commonly known as Indirect Form of Payment (IFOP).

In this scenario, the booking isn't confirmed automatically at the time of a successful addPaymentRequest, as indicated by `<confirmationUponAddPayment>false</confirmationUponAddPayment>` in the Booking Record.  To confirm the booking, you can use one of two options:

- For single-order bookings, do an "Add Payment with Confirm" by including `confirmBooking=true` in the addPaymentRequest message.

**Example**:
```XML
<parameters>
 <confirmBooking>true</confirmBooking>
</parameters>
```

:::(Info) (For bookings containing multiple orders, send a separate [confirmBookingRecordRequest](/v1/docs/confirmbookingrecordrequest-elements){target="_blank"} immediately after a successful addPaymentRequest.  You can also confirm single-order bookings this way if you want to do so independently of the addPaymentRequest.

## Cancellation and refunds

### Customers located within Canada using CAD

For VIA Rail bookings made by customers located within Canada using CAD, VIA Rail acts as the merchant of record.  In this scenario, a successful [cancelBookingRecordRequest](/v1/docs/cancelbookingrecordrequest-elements){target="_blank"} will result in an automatic refund from VIA Rail's payment processing system, as indicated by `<refundIsProcessedOnCancel>true</refundIsProcessedOnCancel>` in the [Booking Record](/v1/docs/retrievebookingrecordresponse-elements#code-item-refundIsProcessedOnCancel){target="_blank"}.

## Customers located outside Canada using non-CAD

For VIA Rail bookings made by customers located outside Canada using non-CAD currency, either SilverRail or the partner can act as the merchant of record.  When SilverRail acts as the merchant of record, this is commonly known as Indirect Form of Payment (IFOP).

In this scenario, a successful `cancelBookingRecordRequest` doesn't result in an automatic refund from the payment processing system, indicated by `<refundIsProcessedOnCancel>false</refundIsProcessedOnCancel>` in the Booking Record.

Instead, you need to manually request a refund by submitting a separate [refundBookingRecordRequest](/v1/docs/refundbookingrecordrequest-elements){target="_blank"} after you've done a successful [cancelBookingRecordRequest](/v1/docs/cancelbookingrecordrequest-elements){target="_blank"}.

## Carrier logos
[VIA Rail carrier logos.zip](https://files.document360.io/c53b2e89-8de3-47ad-adf4-5a70ab761260/Images/Documentation/VIA_Carrier_Logos.zip)
