:::(Error) (**In this article**, we will summarize the areas of SilverCore functionality where Swedish Rail has specific requirements or restrictions above and beyond what is common to all Suppliers.)
:::

## Available SilverCore API messages
The following SilverCore API messages are **not** available for Swedish Rail:

- authenticatePayerRequest&#42;&#185;
- addPaymentRequest w/Confirm&#42;&#178;


:::(Info) (&#42;&#185; Available in certain circumstances when SilverRail is the merchant of record.)
:::
:::(Info) (&#42;&#178; See Confirmation, cancellation, and refund below.)
:::

## Accreditation

SilverRail runs a comprehensive suite of tests against your web application to ensure all accreditation standards are met before a production launch.

See [Implementation process](/v1/docs/accreditation-process){target="_blank"} for more information about our accreditation process.

For information about the accreditation requirements for each Supply Channel, please see [Functional requirements summary](/v1/docs/accreditation-requirements-summary){target="_blank"}.  See [Sample implementation tests](/v1/docs/swedish-rail-implementation-tests){target="_blank"} for details of the tests we perform when evaluating a Partner's implementation for the Swedish Rail supply channel.

## Station codes

A complete list of Swedish Rail station data codes is available through our [Published Data API](/v1/docs/published-data-messages#station){target="_blank"}, using `Linkon` as the selection parameter.

**Example**:
```XML
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"
 xmlns:pub="http://railgds.net/ws/publishing">
 <soapenv:Header/>
 <soapenv:Body>
  <pub:dataRequest type="station">
   <pub:parameters>
    <pub:selection>Linkon</pub:selection>
   </pub:parameters>
  </pub:dataRequest>
 </soapenv:Body>
</soapenv:Envelope>
```

The station file structure for Swedish Rail stations is the same as all other suppliers.

**Example**:
```XML
<pub:station code="SEGOC" countryCode="SE" fareStation="YES" itineraryStation="YES" latitude="57.711493" longitude="11.994154" name="Göteborg C" timeZone="Europe/Stockholm" type="Station">
  <pub:supplier alias="Göteborg C" source="SJ" type="ALIAS"/>
</pub:station
```

## Loyalty cards

Swedish Rail offers a loyalty program, indicated by the presence of a `<loyaltyCards>` element within the relevant Leg Solution in the [pointToPointShoppingResponse elements](/v1/docs/pointtopointshoppingresponse-elements#loyaltycards){target="_blank"}.

If the chosen Leg Solution offers loyalty cards and the Fare Code has the `<rewardsEligible>` flag set to `YES`, you can submit the loyalty card number in `<loyaltyCards>` in the `<passenger>` container element in the [createBookingRecordRequest elements](/v1/docs/createbookingrecordrequest-elements#loyaltycards){target="_blank"}.

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
    <loyaltyCard program="SJ_PRIO">9752210232014809</loyaltyCard>
  </loyaltyCards>
</passenger>
```

## Discount cards
Swedish Rail offers several different discount cards, submittable through the `<fareQualifiers>` element in the [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements#farequalifiers){target="_blank"}.

You can retrieve a list of valid discounts by setting `type=" fareQualifier"` in your Published Data request, as shown in the following example.

**Example request**:
```XML
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"
 xmlns:pub="http://railgds.net/ws/publishing">
 <soapenv:Header/>
 <soapenv:Body>
  <pub:dataRequest type="fareQualifier">
  </pub:dataRequest>
 </soapenv:Body>
</soapenv:Envelope>
```

**Example response**:

```XML
<pub:fareQualifier description="SJ Årskort Guld" program="ARSKORT_GULD"    type="DISCOUNT_CARD"/>
```

:::(Info) (Swedish Rail discount cards can result in a free ticket for certain route/fare combinations.  Because SilverCore doesn't allow zero-amount payments, you'll need to skip the [addPaymentRequest](/v1/docs/addpaymentrequest-elements){target="_blank"} and go directly to the [confirmBookingRecordRequest](/v1/docs/confirmbookingrecordrequest-elements){target="_blank"} to handle these free fares.  If any fees have been added to the booking or if the order contains optional prices that increase the total cost of the booking to greater than zero, use the `addPaymentRequest`. )
:::


## Ancillary services

For details on how to create a booking with ancillary services such as optional seat reservations, meals, and internet access, please see [Shop for ancillary services](/v1/docs/handling-optional-prices-in-ticketable-fares){target="_blank"}.

### Seat reservations

Seat reservations are provided for free on some Swedish Rail routes.  Where this is the case, you'll see `<reservable>` set to `MANDATORY` in the Fare Code.

**Example**:
```XML
<fareCode code="SJ21R-2ND-Non-1-1">
 <serviceClass>THIRD</serviceClass>
 <travelSegmentIDRef>LS_1_3_TS_2</travelSegmentIDRef>
 <cabinClass>2 klass</cabinClass>
 <rewardsEligible>YES</rewardsEligible>
 <fareClass>NON-REFUNDABLE</fareClass>
 <openReturn>NO</openReturn>
 <reservable>MANDATORY</reservable>
 <amenities>
  <amenity type="DINING">Dining Car</amenity>
 </amenities>
 <fareApplicabilities>
  <fareApplicability outbound="RESTRICTED" type="SCHEDULE"/>
 </fareApplicabilities>
</fareCode>
```

Additionally, seat reservations are provided as optional add-ons for routes where reservations are possible but are not provided for free.  Where this is the case, you'll see `<reservable>` set to `OPTIONAL` in the Fare Code.  


### Add-ons for meals and internet

Swedish Rail offers various onboard services such as meals and internet access, which you can shop and book as optional price items.

**Example**:
```XML
<optionalPrice optionalPriceID="PRICE_P_1_15_0_OP_1" category="MEAL">
 <price type="ONBOARD_SERVICES" currency="SEK">135.00</price>
 <description>Three-course vegetarian</description>
 <applicableTravelSegments minQuantity="1" maxQuantity="1">
  <travelSegmentIDRef>LS_1_4_TS_0</travelSegmentIDRef>
 </applicableTravelSegments>
 <rules>
  <rule type="EXCHANGE_PROHIBITED" priceType="ONBOARD_SERVICES"
   applicableOrderStatus="BOOKED" refundToVoucher="NOT_POSSIBLE"/>
  <rule type="REFUND_ALLOWED" priceType="ONBOARD_SERVICES"
   applicableOrderStatus="BOOKED" expirationDateTime="2013-07-24T08:35:00Z"
   refundToVoucher="NOT_POSSIBLE"/>
  <rule type="REFUND_PROHIBITED" priceType="ONBOARD_SERVICES"
   applicableOrderStatus="BOOKED" refundToVoucher="NOT_POSSIBLE"/>
  <rule type="EXCHANGE_PROHIBITED" priceType="ONBOARD_SERVICES"
   applicableOrderStatus="CONFIRMED" refundToVoucher="NOT_POSSIBLE"/>
  <rule type="REFUND_ALLOWED" priceType="ONBOARD_SERVICES"
   applicableOrderStatus="CONFIRMED" expirationDateTime="2013-07-24T08:35:00Z"
   refundToVoucher="NOT_POSSIBLE"/>
  <rule type="REFUND_PROHIBITED" priceType="ONBOARD_SERVICES"
   applicableOrderStatus="CONFIRMED" refundToVoucher="NOT_POSSIBLE"/>
  <rule type="EXCHANGE_PROHIBITED" priceType="ONBOARD_SERVICES"
   applicableOrderStatus="RETURNED" refundToVoucher="NOT_POSSIBLE"/>
  <rule type="REFUND_PROHIBITED" priceType="ONBOARD_SERVICES"
   applicableOrderStatus="RETURNED" refundToVoucher="NOT_POSSIBLE"/>
  <rule type="EXCHANGE_PROHIBITED" priceType="ONBOARD_SERVICES"
   applicableOrderStatus="TICKETED" refundToVoucher="NOT_POSSIBLE"/>
  <rule type="REFUND_PROHIBITED" priceType="ONBOARD_SERVICES"
   applicableOrderStatus="TICKETED" refundToVoucher="NOT_POSSIBLE"/>
 </rules>
 <fareCodes>
  <fareCode code= "303-5">
   <travelSegmentIDRef>LS_1_4_TS_0</travelSegmentIDRef>
   <fareClass>MEAL</fareClass>
  </fareCode>
 </fareCodes>
 <consumptionRules>
  <totalPassengersConsumptionRule minQuantity="1" maxQuantity="1"/>
 </consumptionRules>
</optionalPrice>
```


## Ticket Delivery Options

Ticket delivery on Swedish Rail is by E-ticket only and in the following forms:

-	E-mail
- SMS
- Print-At-Home (PAH)

Check the `<ticketingOptionsAvailable element>` in the Point-to-Point Price container to see which types are available for a particular fare.  

The `EML` attribute indicates if e-mail is available as a ticket delivery method.  The `SMS` attribute indicates if SMS is available as a ticket delivery method.  The `PAH` attribute indicates if PAH is available as a ticket delivery method.

**Example**:
```XML
<ticketingOptionsAvailable TOD="false" ETK="false" PAH="true" EML="true"
 DEPARTURE_STATION_TOD="true" SMS="true"/>
```

After you submit a [validateBookingRecordInformationRequest](/v1/docs/validatebookingrecordinformationrequest-elements){target="_blank"} or [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements){target="_blank"}, the response contains further details about the e-mail, SMS, and PAH options.

**Example**:
```XML
<ns2:ticketingOptions>
  <ns2:ticketingOption code="EML" type="E_MAIL">
    <description>Ticket by Email</description>
    <fee isRefundable="false" currency="SEK">0.00</fee>
  </ns2:ticketingOption>
  <ns2:ticketingOption code="PAH" type="E_TICKET">
    <description>Print At Home</description>
    <fee isRefundable="false" currency="SEK">0.00</fee>
  </ns2:ticketingOption>
  <ns2:ticketingOption code="SMS" type="E_TICKET" destination="SJSMS">
    <description>SMS</description>
    <fee isRefundable="false" currency="SEK">0.00</fee>
  </ns2:ticketingOption>
</ns2:ticketingOptions>
```

### SMS

For the SMS option, Swedish Rail restricts delivery to mobile phone numbers with a country code within the region defined by the `<ticketingOption>` destination attribute.  For Swedish Rail SMS, the destination code will always be SJSMS.  You can view the countries covered using the SilverCore Published Data API request.

**Example**:
```XML
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"
 xmlns:pub="http://railgds.net/ws/publishing">
 <soapenv:Header/>
 <soapenv:Body>
  <pub:dataRequest type="region">
  </pub:dataRequest>
 </soapenv:Body>
</soapenv:Envelope>
Sample response:
<pub:region code="SJSMS" name="SJ SMS Coverage">
 <pub:country code="AT"/>
 <pub:country code="BE"/>
 <pub:country code="DE"/>
 <pub:country code="DK"/>
 <pub:country code="ES"/>
 <pub:country code="FI"/>
 <pub:country code="FR"/>
 <pub:country code="GB"/>
 <pub:country code="GR"/>
 <pub:country code="IE"/>
 <pub:country code="IT"/>
 <pub:country code="LU"/>
 <pub:country code="NL"/>
 <pub:country code="NO"/>
 <pub:country code="PT"/>
 <pub:country code="SE"/>
</pub:region>
```

These codes represent the following country names:
- AT - Austria
- BE - Belgium
- DE - Germany
- DK - Denmark
- ES - Spain
- FI - Finland
- FR - France
- GB - United Kingdom
- GR - Greece
- IE - Ireland
- IT - Italy
- LU - LU	Luxembourg
- NL - Netherlands
- NO - Norway
- PT - Portugal
- SE - Sweden

To select SMS ticket delivery, set `<ticketOption>`  to `SMS` in your [updateBookingRecordRequest](/v1/docs/updatebookingrecordrequest-elements){target="_blank"} or [addPaymentRequest](/v1/docs/addpaymentrequest-elements){target="_blank"}. You must include a `<phoneNumber>` element to indicate the mobile device to which the text message should be sent.  You must also set the `callingCountryCode` attribute so SilverCore can validate the number against the destination code (see above).

:::(Info) (If you select the EML ticketing option, you don't need to include an e-mail address.  Swedish Rail will use the e-mail you supplied in the original `createBookingRecordRequest`.)
:::

**Example updateBookingRecordRequest for SMS ticketing**:
```XML
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"
 xmlns:book="http://railgds.net/ws/booking"
 xmlns:com="http://railgds.net/ws/commontypes">
 <soapenv:Header/>
 <soapenv:Body>
  <book:updateBookingRecordRequest>
   <com:context>
    <com:distributorCode>DEMO</com:distributorCode>
    <com:pointOfSaleCode>ES</com:pointOfSaleCode>
    <com:channelCode>CON</com:channelCode>
   </com:context>
   <book:recordLocator>B-DEMO-FPD000015</book:recordLocator>
   <book:fulfillmentInformation>
    <book:ticketOption code="SMS">
     <com:fee currency="SEK">0.00</com:fee>
    </book:ticketOption>
    <book:phoneNumber callingCountryCode="1">5551234567</book:phoneNumber>
   </book:fulfillmentInformation>
   <book:responseSpec>
    <book:returnReservationDetails>true</book:returnReservationDetails>
   </book:responseSpec>
  </book:updateBookingRecordRequest>
 </soapenv:Body>
</soapenv:Envelope>
```

For more information about integrating Ticket Delivery Options into your application, please see [Specify Ticket Delivery Options](/v1/docs/specifying-ticket-delivery-options){target="_blank"}.

### PAH

For the PAH option, a [claimValueDocumentRequest](/v1/docs/claimvaluedocumentrequest-elements){target="_blank"} must be sent to issue the tickets.  

**Example valueDocument container in the claimValueDocumentResponse**:

```XML
<ns2:valueDocument status="ISSUED">
  <valueDocumentLocator>XZH7780X</valueDocumentLocator>
  <retrievalCustomer>Not Applicable</retrievalCustomer>
  <retrievalMaskedCreditCard>Not Applicable</retrievalMaskedCreditCard>
  <retrievalCode>XXXXXXXX</retrievalCode>
  <url>http://ebiljett.linkon.se/ticket/a/2023-08-02-15.39.08.556038.pdf</url>
</ns2:valueDocument>
```

## Confirmation, Cancellation, and Refund

Swedish Rail isn't a pass-through supplier, so payment-related activities such as confirmations, cancellations, and refunds must be handled accordingly.

### Order confirmations

Because the Supplier isn't the merchant of record for Swedish Rail transactions, orders are not automatically put into a `CONFIRMED` status after a successful `addPaymentRequest`.  See [Payment processing](/v1/docs/payment-processing){target="_blank"} for more information.

To confirm a Swedish Rail booking, you can use one of two options:

- You can do an "Add Payment with Confirm" for single-order bookings by including a `confirmBooking=true` parameter in the `addPaymentRequest`.

**Example**:
```XML
<parameters>
 <confirmBooking>true</confirmBooking>
</parameters>
```

- Send a separate `confirmBookingRecordRequest` for bookings containing multiple orders immediately after a successful `addPaymentRequest`.  You can also confirm single-order bookings this way if you want to do so independently of the `addPaymentRequest`.

### Cancellations and refunds
Swedish Rail refunds must be handled with a subsequent [refundBookingRecordRequest](/v1/docs/refundbookingrecordrequest-elements){target="_blank"} after you've done a successful [cancelBookingRecordRequest](/v1/docs/cancelbookingrecordrequest-elements){target="_blank"}.

When a `CONFIRMED` order is canceled, the status moves to `CANCELED`.  When a `TICKETED` order is canceled, the status moves to `WITHDRAWN`.
