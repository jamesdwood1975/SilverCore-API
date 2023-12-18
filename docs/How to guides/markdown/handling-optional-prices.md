:::(Error) (**In this article**, as part of our series of How-Tos, we'll look at how to handle optional prices in Ticketable Fares and provide several practical examples covering common optional prices such as seat reservations, Travel Cards, and bike reservations.)
:::

## Intro

In addition to the price for a train ticket, some Suppliers add a separate charge for optional items such as seat reservations, sleeper accommodation, internet access, upgraded meals, or bike reservations. Because the optional items are often equipment-dependent, this optional charge might only apply to some of the Fare Codes within a Ticketable Fare.

To handle these situations, each `<ticketableFare>` element in the SilverCore optional prices shopping response message may include a set of <optionalPrice> elements, which define the amount charged and indicate the set of Fare Codes for which the optional item is available.

Because some Suppliers offer bundled deals on optional items, the `<optionalPrice>` element also indicates the minimum and maximum number of Fare Codes covered by a single optional price. Where applicable, the optional price includes consumption rules which further define the numbers and categories of passengers for whom the optional item is available.

If the passenger elects to purchase the optional items, you'll need to modify the `<totalPrice>` value for both the `<ticketableFare>` and `<pointToPointPrice>` elements in your [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements){target="_blank"} message by manually adding the optional price to the original price. You must also indicate which Fare Codes you're choosing to apply the optional price to.

## UK market

For the UK market, you should use the [optionalPricesShoppingRequest](/v1/docs/optionalpricesshoppingrequest-elements){target="_blank"} to get optional prices rather than the [pointTopointShoppingRequest](/v1/docs/pointtopointshoppingrequest-elements){target="_blank"}.

Refer to the following example that shows how to ensure optional prices are not returned in the [pointToPointShoppingResponse](/v1/docs/pointtopointshoppingresponse-elements){target="_blank"}:

**Example**:
```XML
<shop:includeOptionalPrices reservations="false" accommodations="false"
onboardServices="false" localServices="false" supplementaryServices="false"/>
```

## Available optional prices by supplier

| | Accommodation | Onboard services | Seat Res. | Travelcards | Bike Res. | PlusBus |
| --- | --- | --- | --- | --- | --- | --- |
| Amtrak | &#10003; | | | | | |
| RDG | | | &#10003; | &#10003; | &#10003; | &#10003; |
| DB | | | &#10003; | | | |
| Renfe | | | | | | |
| SNCF | | | | | | |
| Swedish Rail | | &#10003; | &#10003; | | | |
| Trenitalia | &#10003; | | | | | |
| VIA Rail | &#10003; | | | | |

## Optional prices shopping request

The [optionalPricesShoppingRequest](/v1/docs/optionalpricesshoppingrequest-elements){target="_blank"} is located in the shopping endpoint and is structurally similar to the [validateBookingRecordRequest](/v1/docs/validatebookingrecordrequest-elements){target="_blank"} and the [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements){target="_blank"}. The request accepts the selected legSolution and pointToPointPrice data in the same format as the previously mentioned requests.

:::(Info) (To avoid errors, your request must not contain the `<optionalPrices>` element from a previous shopping response, for example, from a `pointToPointShoppingResponse`.)
:::

## Optional prices shopping response

The [optionalPricesShoppingResponse](/v1/docs/optionalpricesshoppingresponse-elements){target="_blank"} contains the same structure as a [pointToPointShoppingResponse](/v1/docs/pointtopointshoppingresponse-elements){target="_blank"}, with the legs and fare information for the selected journey, and all optional prices including Seat Reservations, Travelcards, and Supplements, such as Bike Reservations.

## Optional prices in the optionalPricesShoppingResponse message

A typical `<optionalPrices>` element in the `optionalPricesShoppingReponse` message looks like this:

```XML
<optionalPrices>
<optionalPrice optionalPriceID="PRICE_LS_1_1_0_0_OP_0" category="ACCOMMODATION">
  <price type="ACCOMMODATION" currency="USD">1320.00</price>
  <description>Family Bedroom</description>
  <applicableTravelSegments minQuantity="1" maxQuantity="1">
   <travelSegmentIDRef>LS_1_1_TS_0</travelSegmentIDRef>
  </applicableTravelSegments>
  <rules>
   <rule type="EXCHANGE_PROHIBITED" priceType="ACCOMMODATION"
    applicableOrderStatus="BOOKED" refundToVoucher="NOT_POSSIBLE"/>
   <rule type="REFUND_ALLOWED" priceType="ACCOMMODATION"
    applicableOrderStatus="BOOKED" expirationDateTime="2017-06-04T23:59:00Z"
    refundToVoucher="NOT_POSSIBLE"/>
   <rule type="REFUND_PROHIBITED" priceType="ACCOMMODATION"
    applicableOrderStatus="BOOKED" refundToVoucher="NOT_POSSIBLE"/>
   <rule type="EXCHANGE_PROHIBITED" priceType="ACCOMMODATION"
    applicableOrderStatus="CONFIRMED" refundToVoucher="NOT_POSSIBLE"/>
   <rule type="REFUND_PROHIBITED" priceType="ACCOMMODATION"
    applicableOrderStatus="CONFIRMED" refundToVoucher="NOT_POSSIBLE"/>
   <rule type="EXCHANGE_PROHIBITED" priceType="ACCOMMODATION"
    applicableOrderStatus="TICKETED" refundToVoucher="NOT_POSSIBLE"/>
   <rule type="REFUND_PROHIBITED" priceType="ACCOMMODATION"
    applicableOrderStatus="TICKETED" refundToVoucher="NOT_POSSIBLE"/>
  </rules>
  <fareCodes>
   <fareCode code="FS">
    <serviceClass>SLEEPER</serviceClass>
    <travelSegmentIDRef>LS_1_1_TS_0</travelSegmentIDRef>
    <cabinClass>Family Bedroom</cabinClass>
    <fareClass>STANDARD</fareClass>
   </fareCode>
  </fareCodes>
  <ticketingOptionsAvailable TOD="true" ETK="false" PAH="false" EML="false"
   DEPARTURE_STATION_TOD="true" SMS="false"/>
  <consumptionRules>
   <ageBreakConsumptionRule minQuantity="1" maxQuantity="2">
    <minAge>16</minAge>
   </ageBreakConsumptionRule>
   <ageBreakConsumptionRule minQuantity="0" maxQuantity="3">
    <maxAge>15</maxAge>
   </ageBreakConsumptionRule>
   <totalPassengersConsumptionRule minQuantity="1" maxQuantity="4"/>
  </consumptionRules>
  <maximumBookableQuantity>1</maximumBookableQuantity>
</optionalPrice>
...
</optionalPrices>
```

:::(Info) (Refer to the [optionalPricesShoppingRequest](/v1/docs/optionalpricesshoppingrequest-elements){target="_blank"} elements in the SilverCore API Dev Guide for a complete description of the `<optionalPrices>` elements.)
:::

## Optional prices in the createBookingRecordRequest message

Normally, the total prices you include in your [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements){target="_blank"} message are identical to the prices SilverCore provided in the [optionalPricesShoppingResponse](/v1/docs/optionalpricesshoppingresponse-elements){target="_blank"} message. However, if the passenger elects to purchase an optional item, you'll need to manually add the selected optional price to the existing total price and insert the adjusted amount into the following elements in the `createBookingRecordRequest` message:

```
pointToPointPrice/totalPrice
pointToPointPrice/ticketableFare/totalPrice
```

You also need to link the affected `<fareCode>` elements to the selected optional price item by inserting an `<optionalPricesRequested>` element into each one. See the examples below for details.

:::(Info) (If you've set the `<priceAcceptance>` parameter to allow discrepancies between the shopping and booking prices, SilverCore will accept the booking even if you don't manually adjust the `<totalPrice>` values to include the optional prices.)
:::

## Example 1: Optional prices for seat reservations

A typical use for optional pricing is when the Supplier charges an additional fee for seat reservations. In this case, SilverCore uses the value of the `<reservable>` element to determine which Fare Codes allow for optional seat reservations. If at least one Fare Code has `<reservable>` set to `OPTIONAL`, SilverCore will include an `<optionalPrices>` element in the shopping response and will populate the `<applicableTravelSegments>` element with a list of Fare Codes for which the passenger can request reservations.

For example, if seat reservations cost EUR 4.00 and are available for two of the three Fare Codes within the Ticketable Fare, the `<pointToPointPrice>` element in the [optionalPricesShoppingResponse](/v1/docs/optionalpricesshoppingresponse-elements){target="_blank"} message will look like this:

```XML
<ns2:pointToPointPrice priceID="PRICE_P_1_12">
❶ <totalPrice currency="EUR">170.00</totalPrice>
 <ticketableFares>
  <ticketableFare>
❷   <totalPrice currency="EUR">170.00</totalPrice>
   <passengerReferences>
    <passengerReference>
     <passengerIDRef>PAX_SPEC_0</passengerIDRef>
     <passengerTypeCode>ADT</passengerTypeCode>
     <fareCodes>
      <fareCode code="NP-1-1">
       <serviceClass>FIRST</serviceClass>
       <travelSegmentIDRef>LS_1_4_TS_2</travelSegmentIDRef>
       <cabinClass>1. Klasse reisen</cabinClass>
       <rewardsEligible>YES</rewardsEligible>
       <fareClass>FLEXPREIS</fareClass>
       <fareDisplayName>Flexpreis</fareDisplayName>
       <openReturn>NO</openReturn>
❸       <reservable>OPTIONAL</reservable>
       <fareExpirationDateTime>2017-06-06T01:00:00Z</fareExpirationDateTime>
       <amenities>
        <amenity type="DINING">Dining Car</amenity>
       </amenities>
       <fareApplicabilities>
        <fareApplicability outbound="OPEN" type="SCHEDULE"/>
       </fareApplicabilities>
      </fareCode>
      <fareCode code="NP-1-1">
       <serviceClass>SECOND</serviceClass>
       <travelSegmentIDRef>LS_1_4_TS_0</travelSegmentIDRef>
       <cabinClass>1. Klasse reisen</cabinClass>
       <rewardsEligible>YES</rewardsEligible>
       <fareClass>FLEXPREIS</fareClass>
       <fareDisplayName>Flexpreis</fareDisplayName>
       <openReturn>NO</openReturn>
❹       <reservable>NOT_POSSIBLE</reservable>
       <fareExpirationDateTime>2017-06-06T01:00:00Z</fareExpirationDateTime>
       <amenities>
        <amenity type="DINING">Dining Car</amenity>
       </amenities>
       <fareApplicabilities>
        <fareApplicability outbound="OPEN" type="SCHEDULE"/>
       </fareApplicabilities>
      </fareCode>
      <fareCode code="NP-1-1">
       <serviceClass>SECOND</serviceClass>
       <travelSegmentIDRef>LS_1_4_TS_1</travelSegmentIDRef>
       <cabinClass>1. Klasse reisen</cabinClass>
       <rewardsEligible>YES</rewardsEligible>
       <fareClass>FLEXPREIS</fareClass>
       <fareDisplayName>Flexpreis</fareDisplayName>
       <openReturn>NO</openReturn>
❺       <reservable>OPTIONAL</reservable>
       <fareExpirationDateTime>2017-06-06T01:00:00Z</fareExpirationDateTime>
       <amenities>
        <amenity type="DINING">Dining Car</amenity>
       </amenities>
       <fareApplicabilities>
        <fareApplicability outbound="OPEN" type="SCHEDULE"/>
       </fareApplicabilities>
      </fareCode>
     </fareCodes>
    </passengerReference>
   </passengerReferences>
   <prices>
    <price type="TICKET" currency="EUR">170.00</price>
   </prices>
❻   <optionalPrices>
    <optionalPrice optionalPriceID="PRICE_P_1_12_0_OP_0"
     category="SEAT_RESERVATION">
❼     <price type="RESERVATION" currency="EUR">4.00</price>
❽     <applicableTravelSegments minQuantity="2" maxQuantity="2">
      <travelSegmentIDRef>LS_1_4_TS_2</travelSegmentIDRef>
      <travelSegmentIDRef>LS_1_4_TS_1</travelSegmentIDRef>
     </applicableTravelSegments>
❾     <rules>
      <rule type="EXCHANGE_PROHIBITED" priceType="RESERVATION"
       applicableOrderStatus="BOOKED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="REFUND_ALLOWED" priceType="RESERVATION"
       applicableOrderStatus="BOOKED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="EXCHANGE_PROHIBITED" priceType="RESERVATION"
       applicableOrderStatus="CONFIRMED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="REFUND_PROHIBITED" priceType="RESERVATION"
       applicableOrderStatus="CONFIRMED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="EXCHANGE_PROHIBITED" priceType="RESERVATION"
       applicableOrderStatus="RETURNED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="REFUND_PROHIBITED" priceType="RESERVATION"
       applicableOrderStatus="RETURNED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="EXCHANGE_PROHIBITED" priceType="RESERVATION"
       applicableOrderStatus="TICKETED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="REFUND_ALLOWED" priceType="RESERVATION"
       applicableOrderStatus="TICKETED"
       expirationDateTime="2017-06-04T23:59:00Z" refundToVoucher="NOT_POSSIBLE">
       <penalty currency="EUR">15.00</penalty>
      </rule>
      <rule type="REFUND_PROHIBITED" priceType="RESERVATION"
       applicableOrderStatus="TICKETED" refundToVoucher="NOT_POSSIBLE"/>
     </rules>
    </optionalPrice>
    ....
   </optionalPrices>
   <commissions>
    <commission type="DISTRIBUTOR" currency="EUR">0.00</commission>
   </commissions>
   <rules>
    ....
   </rules>
   <ticketingOptionsAvailable TOD="false" ETK="false" PAH="false"
    EML="true" DEPARTURE_STATION_TOD="false" SMS="false"/>
   <fareOrigin type="STATION">DEFTF</fareOrigin>
   <fareDestination type="STATION">DEQPP</fareDestination>
  </ticketableFare>
 </ticketableFares>
 <legReferences>
  <legSolutionIDRef>LS_1_4</legSolutionIDRef>
 </legReferences>
 <holdExpiration>2017-05-26T17:45:43Z</holdExpiration>
</ns2:pointToPointPrice>
```

❶ The Point-To-Point Price total price is just for the ticket and doesn't include any optional prices for seat reservations. Note that it might already include the charge for any mandatory seat reservations.
❷ The Ticketable Fare total price is also just for the ticket, plus any mandatory seat reservations.
❸ A seat reservation is available for the first Fare Code.
❹ A seat reservation is not available for the second Fare Code.
❺ A seat reservation is available for the third Fare Code.
❻ Because at least one Fare Code allows for seat reservations, SilverCore returns the optional pricing details.
❼ The price for a seat reservation is EUR 4.00.
❽ The price covers two seat reservations and can be applied to Travel Segments LS_1_4_TS_1 and LS_1_4_TS_2.
❾ The `<rules>` element sets the refund and exchange terms for the seat reservation cost.

If the passenger decides to purchase seat reservations for both trains, you'll need to modify the Point-To-Point Price as follows before submitting it in the [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements){target="_blank"} message:

```XML
<book:pointToPointPrice priceID="PRICE_P_1_12">
❶ <totalPrice currency="EUR">174.00</totalPrice>
 <ticketableFares>
  <ticketableFare>
❷   <totalPrice currency="EUR">174.00</totalPrice>
   <passengerReferences>
    <passengerReference>
     <passengerIDRef>PAX_SPEC_0</passengerIDRef>
     <passengerTypeCode>ADT</passengerTypeCode>
     <fareCodes>
      <fareCode code="NP-1-1">
       <serviceClass>FIRST</serviceClass>
       <travelSegmentIDRef>LS_1_4_TS_2</travelSegmentIDRef>
       <cabinClass>1. Klasse</cabinClass>
       <fareDisplayName>Flexpreis</fareDisplayName>
       <reservable>OPTIONAL</reservable>
❸       <reservationRequested>YES</reservationRequested>
❹       <optionalPricesRequested>
        <optionalPriceIDRef>PRICE_P_1_12_0_OP_0</optionalPriceIDRef>
       </optionalPricesRequested>
      </fareCode>
      <fareCode code="NP-1-1">
       <serviceClass>SECOND</serviceClass>
       <travelSegmentIDRef>LS_1_4_TS_0</travelSegmentIDRef>
       <cabinClass>1. Klasse reisen</cabinClass>
       <fareDisplayName>Flexpreis</fareDisplayName>
       <reservable>NOT_POSSIBLE</reservable>
      </fareCode>
      <fareCode code="NP-1-1">
       <serviceClass>SECOND</serviceClass>
       <travelSegmentIDRef>LS_1_4_TS_1</travelSegmentIDRef>
       <cabinClass>1. Klasse reisen</cabinClass>
       <fareDisplayName>Flexpreis</fareDisplayName>
       <reservable>OPTIONAL</reservable>
❺       <reservationRequested>YES</reservationRequested>
❻       <optionalPricesRequested>
        <optionalPriceIDRef>PRICE_P_1_12_0_OP_0</optionalPriceIDRef>
       </optionalPricesRequested>
      </fareCode>
     </fareCodes>
    </passengerReference>
   </passengerReferences>
   <prices>
    <price type="TICKET" currency="EUR">170.00</price>
   </prices>
❼   <optionalPrices>
    <optionalPrice optionalPriceID="PRICE_P_1_12_0_OP_0"
     category="SEAT_RESERVATION">
     <price type="RESERVATION" currency="EUR">4.00</price>
     <applicableTravelSegments minQuantity="2" maxQuantity="2">
      <travelSegmentIDRef>LS_1_4_TS_2</travelSegmentIDRef>
      <travelSegmentIDRef>LS_1_4_TS_1</travelSegmentIDRef>
     </applicableTravelSegments>
    </optionalPrice>
   </optionalPrices>
  </ticketableFare>
 </ticketableFares>
 <legReferences>
  <legSolutionIDRef>LS_1_4</legSolutionIDRef>
 </legReferences>
</book:pointToPointPrice>
```

❶ Add the optional seat reservation price (EUR 4.00) to the base ticket price (EUR 170.00) in the Point-To-Point Price total.
❷ Also add the optional seat reservation price to the base ticket price in the Ticketable Fare total.
❸ Set the `<reservationRequested>` flag for the first reservable Fare Code.
❹ Insert an `<optionalPricesRequested>` element into the first reservable Fare Code. Use the `<optionalPriceIDRef>` value to identify the `<optionalPrices>` item you’re requesting (PRICE_P_1_12_0_OP_0).
❺ Set the `<reservationRequested>` flag for the first reservable Fare Code.
❻ Insert the same `<optionalPricesRequested>` element into the second reservable Fare Code.
❼ Insert the relevant `<optionalPrices>` elements as returned in the `optionalPricesShoppingReponse` message. You must include the `<price>` and `<applicableTravelSegments>` child elements but can optionally omit the rest of the `<optionalPrice>` data.

## Example 2: Optional prices for sleeper accommodation

For optional pricing on items other than seat reservations, the `<optionalPrices>` element in the shopping response may also include Fare Code details and a set of consumption rules in addition to the `<applicableTravelSegments>` list.

For example, if sleeper accommodations are available for one of the Fare Codes within the Ticketable Fare, the `<pointToPointPrice>` element in the [optionalPricesShoppingResponse](/v1/docs/optionalpricesshoppingresponse-elements){target="_blank"} message will look like this:

```XML
<ns2:pointToPointPrice priceID="PRICE_LS_1_1_0">
❶ <totalPrice currency="USD">253.50</totalPrice>
 <ticketableFares>
  <ticketableFare>
❷   <totalPrice currency="USD">253.50</totalPrice>
   <passengerReferences>
    <passengerReference>
     <passengerIDRef>PAX_SPEC_0</passengerIDRef>
     <passengerTypeCode>F</passengerTypeCode>
     <fareCodes>
      <fareCode code="Y">
       <serviceClass>THIRD</serviceClass>
       <travelSegmentIDRef>LS_1_1_TS_0</travelSegmentIDRef>
       <cabinClass>Coach Reserved Seat</cabinClass>
       <rewardsEligible>YES</rewardsEligible>
       <fareClass>STANDARD</fareClass>
       <openReturn>NO</openReturn>
       <reservable>NOT_POSSIBLE</reservable>
       <amenities>
        <amenity type="BAGGAGE">Baggage Car</amenity>
        <amenity type="BUFFET">Buffet Service</amenity>
        <amenity type="DINING">Dining Car</amenity>
        <amenity type="POWER_SOCKET">Power Socket at Seat</amenity>
       </amenities>
       <fareApplicabilities>
        <fareApplicability outbound="RESTRICTED" type="SCHEDULE"/>
       </fareApplicabilities>
      </fareCode>
     </fareCodes>
    </passengerReference>
    <passengerReference>
     <passengerIDRef>PAX_SPEC_1</passengerIDRef>
     <passengerTypeCode>H</passengerTypeCode>
     <fareCodes>
      <fareCode code="Y">
       <serviceClass>THIRD</serviceClass>
       <travelSegmentIDRef>LS_1_1_TS_0</travelSegmentIDRef>
       <cabinClass>Coach Reserved Seat</cabinClass>
       <rewardsEligible>YES</rewardsEligible>
       <fareClass>STANDARD</fareClass>
       <openReturn>NO</openReturn>
       <reservable>NOT_POSSIBLE</reservable>
       <amenities>
        <amenity type="BAGGAGE">Baggage Car</amenity>
        <amenity type="BUFFET">Buffet Service</amenity>
        <amenity type="DINING">Dining Car</amenity>
        <amenity type="POWER_SOCKET">Power Socket at Seat</amenity>
       </amenities>
       <fareApplicabilities>
        <fareApplicability outbound="RESTRICTED" type="SCHEDULE"/>
       </fareApplicabilities>
      </fareCode>
     </fareCodes>
    </passengerReference>
   </passengerReferences>
   <prices>
    <price type="TICKETED_RES" currency="USD">253.50</price>
   </prices>
❸   <optionalPrices>
    <optionalPrice optionalPriceID="PRICE_LS_1_1_0_0_OP_0" category="ACCOMMODATION">
❹     <price type="ACCOMMODATION" currency="USD">1320.00</price>
     <description>Family Bedroom</description>
❺     <applicableTravelSegments minQuantity="1" maxQuantity="1">
      <travelSegmentIDRef>LS_1_1_TS_0</travelSegmentIDRef>
     </applicableTravelSegments>
❻     <rules>
      <rule type="EXCHANGE_PROHIBITED" priceType="ACCOMMODATION"
       applicableOrderStatus="BOOKED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="REFUND_ALLOWED" priceType="ACCOMMODATION"
       applicableOrderStatus="BOOKED" expirationDateTime="2013-10-28T20:00:00Z"
       refundToVoucher="NOT_POSSIBLE"/>
      <rule type="REFUND_PROHIBITED" priceType="ACCOMMODATION"
       applicableOrderStatus="BOOKED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="EXCHANGE_PROHIBITED" priceType="ACCOMMODATION"
       applicableOrderStatus="CONFIRMED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="REFUND_PROHIBITED" priceType="ACCOMMODATION"
       applicableOrderStatus="CONFIRMED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="EXCHANGE_PROHIBITED" priceType="ACCOMMODATION"
       applicableOrderStatus="TICKETED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="REFUND_PROHIBITED" priceType="ACCOMMODATION"
       applicableOrderStatus="TICKETED" refundToVoucher="NOT_POSSIBLE"/>
     </rules>
     <fareCodes>
❼      <fareCode code="FS">
       <serviceClass>SLEEPER</serviceClass>
       <travelSegmentIDRef>LS_1_1_TS_0</travelSegmentIDRef>
       <cabinClass>Family Bedroom</cabinClass>
       <fareClass>STANDARD</fareClass>
      </fareCode>
     </fareCodes>
     <ticketingOptionsAvailable TOD="true" ETK="false" PAH="false" EML="false"
      DEPARTURE_STATION_TOD="true" SMS="false"/>
❽     <consumptionRules>
      <ageBreakConsumptionRule minQuantity="1" maxQuantity="2">
       <minAge>16</minAge>
      </ageBreakConsumptionRule>
      <ageBreakConsumptionRule minQuantity="0" maxQuantity="3">
       <maxAge>15</maxAge>
      </ageBreakConsumptionRule>
      <totalPassengersConsumptionRule minQuantity="1" maxQuantity="4"/>
     </consumptionRules>
❾     <maximumBookableQuantity>1</maximumBookableQuantity>
    </optionalPrice>
    ...
   </optionalPrices>
   <commissions>
    <commission type="DISTRIBUTOR" currency="USD">0.00</commission>
   </commissions>
   <rules>
    ...
   </rules>
   <ticketingOptionsAvailable TOD="true" ETK="false" PAH="false" EML="false"
    DEPARTURE_STATION_TOD="true" SMS="false"/>
   <fareOrigin type="STATION">USCHI</fareOrigin>
   <fareDestination type="STATION">USLAX</fareDestination>
  </ticketableFare>
 </ticketableFares>
 <legReferences>
  <legSolutionIDRef>LS_1_1</legSolutionIDRef>
 </legReferences>
 <holdExpiration>2013-09-13T05:59:00Z</holdExpiration>
</ns2:pointToPointPrice>
```

❶ The Point-To-Point Price total price is just for the ticket and doesn't include any optional prices for sleeper accommodations.
❷ The Ticketable Fare total price is also just for the ticket.
❸ Because at least one Fare Code allows for sleeper accommodations, SilverCore returns the optional pricing details.
❹ The price for a sleeper is USD 1320.00.
❺ The price covers one sleeper and is applicable to Travel Segment LS_1_1_TS_0.
❻ The `<rules>` element sets the refund and exchange terms for the sleeper cost.
❼ The `<fareCode>` element contains additional information about the sleeper and indicates the Travel Segment for which it is available.
❽ The `<consumptionRules>` element indicates that the sleeper can accommodate one or two adult passengers and up to three children 15-years-old or younger.
❾ The `<maximumBookableQuantity>` value shows that one sleeper is available for booking based on the consumption rules and current supplier inventory.

If the passenger decides to purchase sleeper accommodation, you'll need to modify the Point-To-Point Price as follows before submitting it in the [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements){target="_blank"} message:

```XML
<book:pointToPointPrice priceID="PRICE_LS_1_1_0">
❶ <totalPrice currency="USD">1573.50</totalPrice>
 <ticketableFares>
  <ticketableFare>
❷   <totalPrice currency="USD">1573.50</totalPrice>
   <passengerReferences>
    <passengerReference>
     <passengerIDRef>PAX_SPEC_0</passengerIDRef>
     <passengerTypeCode>F</passengerTypeCode>
     <fareCodes>
      <fareCode code="Y">
       <serviceClass>THIRD</serviceClass>
       <travelSegmentIDRef>LS_1_1_TS_0</travelSegmentIDRef>
       <cabinClass>Coach Reserved Seat</cabinClass>
       <fareClass>STANDARD</fareClass>
       <reservable>NOT_POSSIBLE</reservable>
❸       <optionalPricesRequested>
        <optionalPriceIDRef>PRICE_LS_1_1_0_0_OP_0</optionalPriceIDRef>
       </optionalPricesRequested>
      </fareCode>
     </fareCodes>
    </passengerReference>
    <passengerReference>
     <passengerIDRef>PAX_SPEC_1</passengerIDRef>
     <passengerTypeCode>H</passengerTypeCode>
     <fareCodes>
      <fareCode code="Y">
       <serviceClass>THIRD</serviceClass>
       <travelSegmentIDRef>LS_1_1_TS_0</travelSegmentIDRef>
       <cabinClass>Coach Reserved Seat</cabinClass>
       <fareClass>STANDARD</fareClass>
       <reservable>NOT_POSSIBLE</reservable>
❹       <optionalPricesRequested>
        <optionalPriceIDRef>PRICE_LS_1_1_0_0_OP_0</optionalPriceIDRef>
       </optionalPricesRequested>
      </fareCode>
     </fareCodes>
    </passengerReference>
   </passengerReferences>
   <prices>
    <price type="TICKETED_RES" currency="USD">253.50</price>
   </prices>
❺   <optionalPrices>
    <optionalPrice optionalPriceID="PRICE_LS_1_1_0_0_OP_0" category="ACCOMMODATION">
     <price type="ACCOMMODATION" currency="USD">1320.00</price>
     <description>Family Bedroom</description>
     <applicableTravelSegments minQuantity="1" maxQuantity="1">
      <travelSegmentIDRef>LS_1_1_TS_0</travelSegmentIDRef>
     </applicableTravelSegments>
     <fareCodes>
      <fareCode code="FS">
       <serviceClass>SLEEPER</serviceClass>
       <travelSegmentIDRef>LS_1_1_TS_0</travelSegmentIDRef>
       <cabinClass>Family Bedroom</cabinClass>
       <fareClass>STANDARD</fareClass>
      </fareCode>
     </fareCodes>
     <consumptionRules>
      <ageBreakConsumptionRule minQuantity="1" maxQuantity="2">
       <minAge>16</minAge>
      </ageBreakConsumptionRule>
      <ageBreakConsumptionRule minQuantity="0" maxQuantity="3">
       <maxAge>15</maxAge>
      </ageBreakConsumptionRule>
      <totalPassengersConsumptionRule minQuantity="1" maxQuantity="4"/>
     </consumptionRules>
    </optionalPrice>
   </optionalPrices>
  </ticketableFare>
 </ticketableFares>
 <legReferences>
  <legSolutionIDRef>LS_1_1</legSolutionIDRef>
 </legReferences>
</book:pointToPointPrice>
```

❶ Add the optional sleeper price (USD 1320.00) to the base ticket price (USD 253.50) in the Point-To-Point Price total.
❷ Also add the optional sleeper price to the base ticket price in the Ticketable Fare total.
❸ Insert an `<optionalPricesRequested>` element into the Fare Code for the first passenger requesting the sleeper. Use the `<optionalPriceIDRef>` value to identify the `<optionalPrices>` item you’re requesting (PRICE_LS_1_1_0_0_OP_0).
❹ Insert the same `<optionalPricesRequested>` element for the next passenger.
❺ Insert the relevant `<optionalPrices>` elements as returned in the `optionalPricesShoppingReponse` message. You must include the `<price>`, `<description>`, `<applicableTravelSegments>`, `<fareCodes>`, and `<consumptionRules>` child elements but can optionally omit the rest of the `<optionalPrice>` data.

## Example 3: Optional prices for onboard services

Some journeys offer onboard services for an additional cost above and beyond the free amenities included in the base cost of a fare. The following types of onboard services are currently available:

- Internet
- Meals

Optional pricing for onboard services works in a similar way to accommodations. When onboard services are available, the `<optionalPrices>` element in the shopping response includes an item price, a description, the `<applicableTravelSegments>` list, and a set of consumption rules:

```XML
<ns2:pointToPointPrice priceID="PRICE_P_1_18">
❶ <totalPrice currency="EUR">88.82</totalPrice>
 <ticketableFares>
  <ticketableFare>
❷   <totalPrice currency="EUR">88.82</totalPrice>
   <passengerReferences>
    <passengerReference>
     <passengerIDRef>PAX_SPEC_0</passengerIDRef>
     <passengerTypeCode>VU</passengerTypeCode>
     <fareCodes>
      <fareCode code="SJ21R-2ND-Ref-1-1">
       <serviceClass>THIRD</serviceClass>
       <travelSegmentIDRef>LS_1_4_TS_0</travelSegmentIDRef>
       <cabinClass>2 klass</cabinClass>
       <rewardsEligible>YES</rewardsEligible>
       <fareClass>REFUNDABLE</fareClass>
       <openReturn>NO</openReturn>
       <reservable>MANDATORY</reservable>
       <amenities>
        <amenity type="DINING">Dining Car</amenity>
       </amenities>
       <fareApplicabilities>
        <fareApplicability outbound="RESTRICTED" type="SCHEDULE"/>
       </fareApplicabilities>
      </fareCode>
      <fareCode code="SJ21R-2ND-Ref-1-1">
       <serviceClass>THIRD</serviceClass>
       <travelSegmentIDRef>LS_1_4_TS_2</travelSegmentIDRef>
       <cabinClass>2 klass</cabinClass>
       <rewardsEligible>YES</rewardsEligible>
       <fareClass>REFUNDABLE</fareClass>
       <openReturn>NO</openReturn>
       <reservable>MANDATORY</reservable>
       <amenities>
        <amenity type="DINING">Dining Car</amenity>
       </amenities>
       <fareApplicabilities>
        <fareApplicability outbound="RESTRICTED" type="SCHEDULE"/>
       </fareApplicabilities>
      </fareCode>
      <fareCode code="SJ21R-2ND-Ref-1-1">
       <serviceClass>THIRD</serviceClass>
       <travelSegmentIDRef>LS_1_4_TS_1</travelSegmentIDRef>
       <cabinClass>2 klass</cabinClass>
       <rewardsEligible>YES</rewardsEligible>
       <fareClass>REFUNDABLE</fareClass>
       <openReturn>NO</openReturn>
       <reservable>NOT_POSSIBLE</reservable>
       <fareApplicabilities>
        <fareApplicability outbound="RESTRICTED" type="SCHEDULE"/>
       </fareApplicabilities>
      </fareCode>
     </fareCodes>
    </passengerReference>
   </passengerReferences>
   <prices>
    <price type="TICKET" currency="EUR">88.82</price>
   </prices>
❸   <optionalPrices>
❹    <optionalPrice optionalPriceID="PRICE_P_1_18_0_OP_1" category="MEAL">
     <price type="ONBOARD_SERVICES" currency="EUR">9.51</price>
     <description>Todays hot meal - vegetarian</description>
     <applicableTravelSegments minQuantity="1" maxQuantity="1">
      <travelSegmentIDRef>LS_1_4_TS_0</travelSegmentIDRef>
     </applicableTravelSegments>
❺     <rules>
      <rule type="EXCHANGE_PROHIBITED" priceType="ONBOARD_SERVICES"
       applicableOrderStatus="BOOKED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="REFUND_ALLOWED" priceType="ONBOARD_SERVICES"
       applicableOrderStatus="BOOKED" expirationDateTime="2013-10-27T09:35:00Z"
       refundToVoucher="NOT_POSSIBLE"/>
      <rule type="REFUND_PROHIBITED" priceType="ONBOARD_SERVICES"
       applicableOrderStatus="BOOKED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="EXCHANGE_PROHIBITED" priceType="ONBOARD_SERVICES"
       applicableOrderStatus="CONFIRMED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="REFUND_ALLOWED" priceType="ONBOARD_SERVICES"
       applicableOrderStatus="CONFIRMED" expirationDateTime="2013-10-27T09:35:00Z"
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
      <fareCode code="321-2">
       <travelSegmentIDRef>LS_1_4_TS_0</travelSegmentIDRef>
❻       <fareClass>MEAL</fareClass>
      </fareCode>
     </fareCodes>
❼     <consumptionRules>
      <totalPassengersConsumptionRule minQuantity="1" maxQuantity="1"/>
     </consumptionRules>
    </optionalPrice>
❽    <optionalPrice optionalPriceID="PRICE_P_1_18_0_OP_8" category="INTERNET">
     <price type="ONBOARD_SERVICES" currency="EUR">4.13</price>
     <description>30 min</description>
     <applicableTravelSegments minQuantity="2" maxQuantity="2">
      <travelSegmentIDRef>LS_1_4_TS_0</travelSegmentIDRef>
      <travelSegmentIDRef>LS_1_4_TS_2</travelSegmentIDRef>
     </applicableTravelSegments>
     <rules>
      <rule type="EXCHANGE_PROHIBITED" priceType="ONBOARD_SERVICES"
       applicableOrderStatus="BOOKED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="REFUND_ALLOWED" priceType="ONBOARD_SERVICES"
       applicableOrderStatus="BOOKED" expirationDateTime="2013-10-27T09:35:00Z"
       refundToVoucher="NOT_POSSIBLE"/>
      <rule type="REFUND_PROHIBITED" priceType="ONBOARD_SERVICES"
       applicableOrderStatus="BOOKED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="EXCHANGE_PROHIBITED" priceType="ONBOARD_SERVICES"
       applicableOrderStatus="CONFIRMED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="REFUND_ALLOWED" priceType="ONBOARD_SERVICES"
       applicableOrderStatus="CONFIRMED" expirationDateTime="2013-10-27T09:35:00Z"
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
      <fareCode code="300-1">
       <travelSegmentIDRef>LS_1_4_TS_0</travelSegmentIDRef>
       <fareClass>INTERNET</fareClass>
      </fareCode>
      <fareCode code="300-1">
       <travelSegmentIDRef>LS_1_4_TS_2</travelSegmentIDRef>
       <fareClass>INTERNET</fareClass>
      </fareCode>
     </fareCodes>
     <consumptionRules>
      <totalPassengersConsumptionRule minQuantity="1" maxQuantity="1"/>
     </consumptionRules>
    </optionalPrice>
❾    <optionalPrice optionalPriceID="PRICE_P_1_18_0_OP_9" category="INTERNET">
     <price type="ONBOARD_SERVICES" currency="EUR">10.54</price>
     <description>The whole journey</description>
     <applicableTravelSegments minQuantity="2" maxQuantity="2">
      <travelSegmentIDRef>LS_1_4_TS_0</travelSegmentIDRef>
      <travelSegmentIDRef>LS_1_4_TS_2</travelSegmentIDRef>
     </applicableTravelSegments>
     <rules>
      <rule type="EXCHANGE_PROHIBITED" priceType="ONBOARD_SERVICES"
       applicableOrderStatus="BOOKED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="REFUND_ALLOWED" priceType="ONBOARD_SERVICES"
       applicableOrderStatus="BOOKED" expirationDateTime="2013-10-27T09:35:00Z"
       refundToVoucher="NOT_POSSIBLE"/>
      <rule type="REFUND_PROHIBITED" priceType="ONBOARD_SERVICES"
       applicableOrderStatus="BOOKED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="EXCHANGE_PROHIBITED" priceType="ONBOARD_SERVICES"
       applicableOrderStatus="CONFIRMED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="REFUND_ALLOWED" priceType="ONBOARD_SERVICES"
       applicableOrderStatus="CONFIRMED" expirationDateTime="2013-10-27T09:35:00Z"
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
      <fareCode code="300-4">
       <travelSegmentIDRef>LS_1_4_TS_0</travelSegmentIDRef>
       <fareClass>INTERNET</fareClass>
      </fareCode>
      <fareCode code="300-4">
       <travelSegmentIDRef>LS_1_4_TS_2</travelSegmentIDRef>
       <fareClass>INTERNET</fareClass>
      </fareCode>
     </fareCodes>
     <consumptionRules>
      <totalPassengersConsumptionRule minQuantity="1" maxQuantity="1"/>
     </consumptionRules>
    </optionalPrice>
    ...
   </optionalPrices>
   <commissions>
    <commission type="DISTRIBUTOR" currency="EUR">0.00</commission>
   </commissions>
   <rules>
    ...
   </rules>
   <ticketingOptionsAvailable TOD="false" ETK="false" PAH="false" EML="true"
    DEPARTURE_STATION_TOD="false" SMS="true"/>
   <fareOrigin type="STATION">SEGOC</fareOrigin>
   <fareDestination type="STATION">SEMJS</fareDestination>
  </ticketableFare>
 </ticketableFares>
 <legReferences>
  <legSolutionIDRef>LS_1_4</legSolutionIDRef>
 </legReferences>
 <holdExpiration>2013-09-05T23:59:59Z</holdExpiration>
</ns2:pointToPointPrice>
```

❶ The Point-To-Point Price total price is just for the ticket and doesn't include any optional prices for onboard services.
❷ The Ticketable Fare total price is also just for the ticket.
❸ Because at least one Fare Code allows for onboard services, SilverCore returns the optional pricing details.
❹ The first onboard service item is a meal, available on one Travel Segment (LS_1_4_TS_0).
❺ The `<rules>` element sets the refund and exchange terms for this onboard service.
❻ The `<fareClass>` element indicates what type of general option this is.
❼ The `<consumptionRules>` element shows that the optional price is applicable to only one passenger&mdash;the `<totalPassengersConsumptionRule>` minQuantity and maxQuantity are both set to 1.
❽ The second onboard service item is 30 minutes of internet access, available on two of the three Travel Segments. The `<applicableTravelSegments>` minQuantity amount is 2, indicating that the EUR 4.13 price covers both Travel Segments.
❾ The third onboard service item is internet access for the entire journey, also available on two of the three Travel Segments.

The `<maximumBookableQuantity>` element is not present, which means you can book multiple onboard service items if desired.

If the passenger decides to purchase an onboard service, you'll need to modify the Point-To-Point Price for each affected Ticketable Fare before submitting it in the [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements){target="_blank"} message:

```XML
<book:pointToPointPrice priceID="PRICE_P_1_18">
❶ <totalPrice currency="EUR">98.33</totalPrice>
 <ticketableFares>
  <ticketableFare>
❷   <totalPrice currency="EUR">98.33</totalPrice>
   <passengerReferences>
    <passengerReference>
     <passengerIDRef>PAX_SPEC_0</passengerIDRef>
     <passengerTypeCode>VU</passengerTypeCode>
     <fareCodes>
      <fareCode code="SJ21R-2ND-Ref-1-1">
       <serviceClass>THIRD</serviceClass>
       <travelSegmentIDRef>LS_1_4_TS_0</travelSegmentIDRef>
       <cabinClass>2 klass</cabinClass>
       <fareClass>REFUNDABLE</fareClass>
       <reservable>MANDATORY</reservable>
❸       <optionalPricesRequested>
        <optionalPriceIDRef>PRICE_P_1_18_0_OP_1</optionalPriceIDRef>
       </optionalPricesRequested>
      </fareCode>
      <fareCode code="SJ21R-2ND-Ref-1-1">
       <serviceClass>THIRD</serviceClass>
       <travelSegmentIDRef>LS_1_4_TS_2</travelSegmentIDRef>
       <cabinClass>2 klass</cabinClass>
       <fareClass>REFUNDABLE</fareClass>
       <reservable>MANDATORY</reservable>
      </fareCode>
      <fareCode code="SJ21R-2ND-Ref-1-1">
       <serviceClass>THIRD</serviceClass>
       <travelSegmentIDRef>LS_1_4_TS_1</travelSegmentIDRef>
       <cabinClass>2 klass</cabinClass>
       <fareClass>REFUNDABLE</fareClass>
       <reservable>NOT_POSSIBLE</reservable>
      </fareCode>
     </fareCodes>
    </passengerReference>
   </passengerReferences>
   <prices>
    <price type="TICKET" currency="EUR">88.82</price>
   </prices>
   <optionalPrices>
❹    <optionalPrice optionalPriceID="PRICE_P_1_18_0_OP_1" category="MEAL">
     <price type="ONBOARD_SERVICES" currency="EUR">9.51</price>
     <description>Todays hot meal - vegetarian</description>
     <applicableTravelSegments minQuantity="1" maxQuantity="1">
      <travelSegmentIDRef>LS_1_4_TS_0</travelSegmentIDRef>
     </applicableTravelSegments>
     <fareCodes>
      <fareCode code="321-2">
       <travelSegmentIDRef>LS_1_4_TS_0</travelSegmentIDRef>
       <fareClass>MEAL</fareClass>
      </fareCode>
     </fareCodes>
     <consumptionRules>
      <totalPassengersConsumptionRule minQuantity="1" maxQuantity="1"/>
     </consumptionRules>
    </optionalPrice>
   </optionalPrices>
  </ticketableFare>
 </ticketableFares>
 <legReferences>
  <legSolutionIDRef>LS_1_4</legSolutionIDRef>
 </legReferences>
</book:pointToPointPrice>
```

❶ Add the optional onboard service price (EU 9.51) to the base ticket price (EU 88.82) in the Point-To-Point Price total.
❷ Also add the optional onboard service price to the base ticket price in the Ticketable Fare total.
❸ For each passenger reference, insert an `<optionalPricesRequested>` element into the Fare Code for which you’re requesting the onboard service. Use the `<optionalPriceIDRef>` value to identify the `<optionalPrices>` item you’re requesting (PRICE_P_1_18_0_OP_1).
❹ Insert the relevant `<optionalPrices>` elements as returned in the `optionalPricesShoppingReponse` message. You must include the `<price>`, `<description>`, `<applicableTravelSegments>`, `<fareCodes>`, and `<consumptionRules>` child elements but can optionally omit the rest of the `<optionalPrice>` data.

The `<services>` element in the resulting Booking Record shows which onboard services were booked:

```XML
<fareCode code="SJ21R-2ND-Ref-1-1">
 <serviceClass>THIRD</serviceClass>
 <travelSegmentIDRef>TS-LWQ000139-DOI000001-1</travelSegmentIDRef>
 <cabinClass>2 klass</cabinClass>
 <rewardsEligible>YES</rewardsEligible>
 <fareClass>REFUNDABLE</fareClass>
 <openReturn>NO</openReturn>
 <reservable>MANDATORY</reservable>
 <seatAssignment>
  <coach>7</coach>
  <number>38</number>
  <direction>UNKNOWN</direction>
 </seatAssignment>
 <services>
  <service category="MEAL">Todays hot meal - vegetarian</service>
 </services>
 <fareApplicabilities>
  <fareApplicability outbound="RESTRICTED" type="SCHEDULE"/>
 </fareApplicabilities>
</fareCode>
```

There will also be a `<price>` element reflecting the cost of the onboard services that were booked:

```XML
<prices>
 <price type="TICKET" currency="EUR">88.82</price>
 <price type="ONBOARD_SERVICES" currency="EUR">9.51</price>
</prices>
```

## Example 4: Optional prices for Travelcards

Travelcards are available on all RDG journeys where the origin Travel Point or destination Travel Point is a station within London Zones 1-6.

If a Travelcard isn't available as a part of the base fare, the option to purchase will return as an optional price on the Ticketable Fares in the [optionalPricesShoppingResponse](/v1/docs/optionalpricesshoppingresponse-elements){target="_blank"} message:

```XML
<ns2:pointToPointPrice priceID="PRICE_P_1_0">
❶ <totalPrice currency="GBP">21.50</totalPrice>
 <ticketableFares>
  <ticketableFare>
❷   <totalPrice currency="GBP">21.50</totalPrice>
   <passengerReferences>
    <passengerReference>
     <passengerIDRef>PAX_SPEC_0</passengerIDRef>
     <passengerTypeCode>A</passengerTypeCode>
     <fareCodes>
      <fareCode code="GWR-SDS-00000-STD-1">
       <serviceClass>THIRD</serviceClass>
       <travelSegmentIDRef>LS_1_0_TS_0</travelSegmentIDRef>
       <cabinClass>Standard</cabinClass>
       <rewardsEligible>NO</rewardsEligible>
       <fareClass>ANYTIME_DAY</fareClass>
       <openReturn>NO</openReturn>
       <reservable>OPTIONAL</reservable>
       <fareExpirationDateTime>2013-10-25T01:30:00Z</fareExpirationDateTime>
       <amenities>
        <amenity type="BUFFET">Buffet Service</amenity>
       </amenities>
       <fareApplicabilities>
        <fareApplicability outbound="OPEN" type="SCHEDULE"/>
       </fareApplicabilities>
      </fareCode>
     </fareCodes>
    </passengerReference>
   </passengerReferences>
   <prices>
    <price type="TICKET" currency="GBP">21.50</price>
   </prices>
❸   <optionalPrices>
    <optionalPrice optionalPriceID="PRICE_P_1_0_0_OP_0" category="SEAT_RESERVATION">
     <price type="RESERVATION" currency="GBP">0.00</price>
     <applicableTravelSegments minQuantity="1" maxQuantity="1">
      <travelSegmentIDRef>LS_1_0_TS_0</travelSegmentIDRef>
     </applicableTravelSegments>
     <rules>
      <rule type="EXCHANGE_PROHIBITED" priceType="RESERVATION"
       applicableOrderStatus="BOOKED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="REFUND_ALLOWED" priceType="RESERVATION"
       applicableOrderStatus="BOOKED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="EXCHANGE_PROHIBITED" priceType="RESERVATION"
       applicableOrderStatus="CONFIRMED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="REFUND_ALLOWED" priceType="RESERVATION"
       applicableOrderStatus="CONFIRMED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="EXCHANGE_PROHIBITED" priceType="RESERVATION"
       applicableOrderStatus="RETURNED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="REFUND_ALLOWED" priceType="RESERVATION"
       applicableOrderStatus="RETURNED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="EXCHANGE_PROHIBITED" priceType="RESERVATION"
       applicableOrderStatus="TICKETED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="REFUND_PROHIBITED" priceType="RESERVATION"
       applicableOrderStatus="TICKETED" refundToVoucher="NOT_POSSIBLE"/>
     </rules>
    </optionalPrice>
❹    <optionalPrice optionalPriceID="PRICE_P_1_0_0_OP_1" category="LOCAL_TRANSIT">
❺     <price type="LOCAL_SERVICE" currency="GBP">8.00</price>
     <description>London Day Off-Peak Travelcard - Zones 1-4 - Adult</description>
     <applicableTravelSegments minQuantity="1" maxQuantity="1">
      <travelSegmentIDRef>LS_1_0_TS_0</travelSegmentIDRef>
     </applicableTravelSegments>
❻     <rules>
      <rule type="EXCHANGE_PROHIBITED" priceType="LOCAL_SERVICE"
       applicableOrderStatus="BOOKED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="REFUND_ALLOWED" priceType="LOCAL_SERVICE"
       applicableOrderStatus="BOOKED" expirationDateTime="2013-11-22T01:30:00Z"
       refundToVoucher="NOT_POSSIBLE"/>
      <rule type="EXCHANGE_PROHIBITED" priceType="LOCAL_SERVICE"
       applicableOrderStatus="CONFIRMED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="REFUND_ALLOWED" priceType="LOCAL_SERVICE"
       applicableOrderStatus="CONFIRMED" expirationDateTime="2013-11-22T01:30:00Z"
       refundToVoucher="NOT_POSSIBLE">
       <penalty currency="GBP">0.00</penalty>
      </rule>
      <rule type="EXCHANGE_PROHIBITED" priceType="LOCAL_SERVICE"
       applicableOrderStatus="RETURNED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="REFUND_ALLOWED" priceType="LOCAL_SERVICE"
       applicableOrderStatus="RETURNED" expirationDateTime="2013-11-22T01:30:00Z"
       refundToVoucher="NOT_POSSIBLE"/>
      <rule type="EXCHANGE_PROHIBITED" priceType="LOCAL_SERVICE"
       applicableOrderStatus="TICKETED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="REFUND_PROHIBITED" priceType="LOCAL_SERVICE"
       applicableOrderStatus="TICKETED" refundToVoucher="NOT_POSSIBLE"/>
     </rules>
     <fareCodes>
❼      <fareCode code="ATO-ODT-00000-STD-1">
       <serviceClass>THIRD</serviceClass>
       <travelSegmentIDRef>LS_1_0_TS_0</travelSegmentIDRef>
       <cabinClass>Standard</cabinClass>
❽       <fareClass>OFFPEAK_DAY_TRAVELCARD</fareClass>
      </fareCode>
     </fareCodes>
     <ticketingOptionsAvailable TOD="true" ETK="false" PAH="false" EML="false"
      DEPARTURE_STATION_TOD="true" SMS="false"/>
❾     <consumptionRules>
      <ageBreakConsumptionRule minQuantity="1" maxQuantity="1">
       <minAge>16</minAge>
      </ageBreakConsumptionRule>
      <totalPassengersConsumptionRule minQuantity="1" maxQuantity="1"/>
      <travelCardZoneConsumptionRule>
       <travelCardZone>GB*T4</travelCardZone>
      </travelCardZoneConsumptionRule>
     </consumptionRules>
❿     <maximumBookableQuantity>1</maximumBookableQuantity>
    </optionalPrice>
    <optionalPrice optionalPriceID="PRICE_P_1_0_0_OP_2" category="LOCAL_TRANSIT">
     <price type="LOCAL_SERVICE" currency="GBP">11.00</price>
     <description>London Day Anytime Travelcard - Zones 1-4 - Adult</description>
     <applicableTravelSegments minQuantity="1" maxQuantity="1">
      <travelSegmentIDRef>LS_1_0_TS_0</travelSegmentIDRef>
     </applicableTravelSegments>
     <rules>
      <rule type="EXCHANGE_PROHIBITED" priceType="LOCAL_SERVICE"
       applicableOrderStatus="BOOKED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="REFUND_ALLOWED" priceType="LOCAL_SERVICE"
       applicableOrderStatus="BOOKED" expirationDateTime="2013-11-22T01:30:00Z"
       refundToVoucher="NOT_POSSIBLE"/>
      <rule type="EXCHANGE_PROHIBITED" priceType="LOCAL_SERVICE"
       applicableOrderStatus="CONFIRMED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="REFUND_ALLOWED" priceType="LOCAL_SERVICE"
       applicableOrderStatus="CONFIRMED" expirationDateTime="2013-11-22T01:30:00Z"
       refundToVoucher="NOT_POSSIBLE">
       <penalty currency="GBP">0.00</penalty>
      </rule>
      <rule type="EXCHANGE_PROHIBITED" priceType="LOCAL_SERVICE"
       applicableOrderStatus="RETURNED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="REFUND_ALLOWED" priceType="LOCAL_SERVICE"
       applicableOrderStatus="RETURNED" expirationDateTime="2013-11-22T01:30:00Z"
       refundToVoucher="NOT_POSSIBLE"/>
      <rule type="EXCHANGE_PROHIBITED" priceType="LOCAL_SERVICE"
       applicableOrderStatus="TICKETED" refundToVoucher="NOT_POSSIBLE"/>
      <rule type="REFUND_PROHIBITED" priceType="LOCAL_SERVICE"
       applicableOrderStatus="TICKETED" refundToVoucher="NOT_POSSIBLE"/>
     </rules>
     <fareCodes>
      <fareCode code="ATO-ADT-00000-STD-1">
       <serviceClass>THIRD</serviceClass>
       <travelSegmentIDRef>LS_1_0_TS_0</travelSegmentIDRef>
       <cabinClass>Standard</cabinClass>
       <fareClass>ANYTIME_DAY_TRAVELCARD</fareClass>
      </fareCode>
     </fareCodes>
     <ticketingOptionsAvailable TOD="true" ETK="false" PAH="false" EML="false"
      DEPARTURE_STATION_TOD="true" SMS="false"/>
     <consumptionRules>
      <ageBreakConsumptionRule minQuantity="1" maxQuantity="1">
       <minAge>16</minAge>
      </ageBreakConsumptionRule>
      <totalPassengersConsumptionRule minQuantity="1" maxQuantity="1"/>
      <travelCardZoneConsumptionRule>
       <travelCardZone>GB*T4</travelCardZone>
      </travelCardZoneConsumptionRule>
     </consumptionRules>
     <maximumBookableQuantity>1</maximumBookableQuantity>
    </optionalPrice>
    ...
   </optionalPrices>
   <commissions>
    <commission type="DISTRIBUTOR" currency="GBP">1.13</commission>
   </commissions>
   <rules>
    ...
   </rules>
   <ticketingOptionsAvailable TOD="true" ETK="false" PAH="false" EML="false"
    DEPARTURE_STATION_TOD="true" SMS="false"/>
   <fareOrigin type="STATION">GBRDG</fareOrigin>
   <fareDestination type="STATION">GBQQP</fareDestination>
  </ticketableFare>
 </ticketableFares>
 <legReferences>
  <legSolutionIDRef>LS_1_0</legSolutionIDRef>
 </legReferences>
 <holdExpiration>2013-09-05T20:32:22Z</holdExpiration>
</ns2:pointToPointPrice>
```

❶ The Point-To-Point Price total price (GBP 21.50) is just for the ticket and doesn't include any optional prices for Travelcards.
❷ The Ticketable Fare total price is also just for the ticket.
❸ Because at least one Fare Code allows for Travelcards, SilverCore returns the optional pricing details.
❹ Each of the six different Travelcards available on ATOC (OFFPEAK_DAY_TRAVELCARD, ANYTIME_DAY_TRAVELCARD, etc.) appears as an optional price of type `LOCAL_SERVICE`.
❺ The optional price of GBP 8.00 covers one Travelcard and is applied to Travel Segment LS_1_0_TS_0. In cases where more than one Travel Segment is located within London Zones 1-6, the `<applicableTravelSegments>` element will return the ID for the first Travel Segment only.
❻ The `<rules>` element sets the refund and exchange terms for the Travelcard.
❼ The `<fareCode>` element contains additional information about the Travelcard and references the Travel Segment for which it is available.
❽ The `<fareClass>` element indicates what kind of Travelcard this is.
❾ The `<consumptionRules>` element shows that the Travelcard is applicable only for a specific zone, as defined by the station code in the `<travelCardZone>` element.
❿ The `<maximumBookableQuantity>` element shows that one Travelcard is available for the booking. This value is set based on the number of passengers contained within the Ticketable Fare.

The `<travelCardZone>` stations are defined in the Published Data web service as follows:

| Stations|Description |
| --- | --- |
|GB*T2|London (All day travelcard) - London Travelcard Zone 1 & 2 (Zone R12*)|
|GB*T4|London (All day travelcard) - London Travelcard Zones 1-4 (Zone R1234*)|
|GB*T6|London (All day travelcard) - London Travelcard Zones 1-6 (Zone R1256)|

If the passenger decides to purchase a Travelcard, you'll need to modify the Point-To-Point Price for each affected Ticketable Fare before submitting it in the [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements){target="_blank"} message:

```XML
<book:pointToPointPrice priceID="PRICE_P_1_0">
❶ <totalPrice currency="GBP">29.50</totalPrice>
 <ticketableFares>
  <ticketableFare>
❷   <totalPrice currency="GBP">29.50</totalPrice>
   <passengerReferences>
    <passengerReference>
     <passengerIDRef>PAX_SPEC_0</passengerIDRef>
     <passengerTypeCode>A</passengerTypeCode>
     <fareCodes>
      <fareCode code="GWR-SDS-00000-STD-1">
       <serviceClass>THIRD</serviceClass>
       <travelSegmentIDRef>LS_1_0_TS_0</travelSegmentIDRef>
       <cabinClass>Standard</cabinClass>
       <fareClass>ANYTIME_DAY</fareClass>
       <reservable>OPTIONAL</reservable>
❸       <optionalPricesRequested>
❹        <optionalPriceIDRef>PRICE_P_1_0_0_OP_1</optionalPriceIDRef>
       </optionalPricesRequested>
      </fareCode>
     </fareCodes>
    </passengerReference>
   </passengerReferences>
   <prices>
    <price type="TICKET" currency="GBP">21.50</price>
   </prices>
❺   <optionalPrices>
    <optionalPrice optionalPriceID="PRICE_P_1_0_0_OP_1" category="LOCAL_TRANSIT">
     <price type="LOCAL_SERVICE" currency="GBP">8.00</price>
     <description>London Day Off-Peak Travelcard - Zones 1-4 - Adult</description>
     <applicableTravelSegments minQuantity="1" maxQuantity="1">
      <travelSegmentIDRef>LS_1_0_TS_0</travelSegmentIDRef>
     </applicableTravelSegments>
     <fareCodes>
      <fareCode code="ATO-ODT-00000-STD-1">
       <serviceClass>THIRD</serviceClass>
       <travelSegmentIDRef>LS_1_0_TS_0</travelSegmentIDRef>
       <cabinClass>Standard</cabinClass>
       <fareClass>OFFPEAK_DAY_TRAVELCARD</fareClass>
      </fareCode>
     </fareCodes>
     <consumptionRules>
      <ageBreakConsumptionRule minQuantity="1" maxQuantity="1">
       <minAge>16</minAge>
      </ageBreakConsumptionRule>
      <totalPassengersConsumptionRule minQuantity="1" maxQuantity="1"/>
      <travelCardZoneConsumptionRule>
       <travelCardZone>GB*T4</travelCardZone>
      </travelCardZoneConsumptionRule>
     </consumptionRules>
    </optionalPrice>
   </optionalPrices>
  </ticketableFare>
 </ticketableFares>
 <legReferences>
  <legSolutionIDRef>LS_1_0</legSolutionIDRef>
 </legReferences>
</book:pointToPointPrice>
```

❶ Add the optional Travelcard price (8.00) to the base ticket price (21.50) in the Point-To-Point Price total.
❷ Also add the optional Travelcard price to the base ticket price in the Ticketable Fare total.
❸ For each passenger reference, insert an `<optionalPricesRequested>` element into the Fare Code for which you’re requesting the Travelcard. Dor multi-passenger bookings, all passengers must request the same Travelcard.
❹ Use the `<optionalPriceIDRef>` value to identify the `<optionalPrices>` item you’re requesting (PRICE_P_1_0_0_OP_1).
❺ Insert the relevant `<optionalPrices>` elements as returned in the [optionalPricesShoppingResponse](/v1/docs/optionalpricesshoppingresponse-elements){target="_blank"} message. You must include the `<price>`, `<description>`, `<applicableTravelSegments>`, `<fareCodes>`, and `<consumptionRules>` child elements but can optionally omit the rest of the `<optionalPrice>` data.

The resulting Booking Record will contain a new Travel Segment specifically for the Travelcard, with the `<travelCardZone>` station code from the consumption rules used as the destination Travel Point:

```XML
<travelSegment travelSegmentID="TS-TUQ000140-DNK000001-2" type="TRANSFER"
 legGrouping="1">
 <originTravelPoint type="STATION">GBQQP</originTravelPoint>
 <destinationTravelPoint type="STATION">GB*T4</destinationTravelPoint>
 <departureDateTime>2013-10-24T10:16:00</departureDateTime>
 <arrivalDateTime>2013-10-24T10:16:00</arrivalDateTime>
 <marketingCarrier>LonUnderground</marketingCarrier>
 <operatingCarrier>LonUnderground</operatingCarrier>
 <supplierEquipmentType>LonUnderground-MCT</supplierEquipmentType>
 <duration>P0Y0M0DT0H1M0S</duration>
 <crossBorderInfo>false</crossBorderInfo>
 <equipmentType code="MCT">Metro/City-Transit</equipmentType>
 <marketingServiceName>London TravelCard</marketingServiceName>
 <marketingInformation serviceCode="LonUnderground"/>
</travelSegment>
```

There will also be a separate Ticketable Fare referencing the Travelcard:

```XML
<ticketableFare>
 <totalPrice currency="GBP">8.00</totalPrice>
 <passengerReferences>
  <passengerReference>
   <passengerIDRef>P-TUQ000140-2</passengerIDRef>
   <passengerTypeCode>A</passengerTypeCode>
   <fareCodes>
    <fareCode code="ATO-ODT-00000-STD-1">
     <serviceClass>THIRD</serviceClass>
     <travelSegmentIDRef>TS-TUQ000140-DNK000001-2</travelSegmentIDRef>
     <cabinClass>Standard</cabinClass>
     <rewardsEligible>NO</rewardsEligible>
     <fareClass>OFFPEAK_DAY_TRAVELCARD</fareClass>
     <openReturn>NO</openReturn>
     <reservable>NOT_POSSIBLE</reservable>
     <fareExpirationDateTime>2013-10-25T01:30:00Z</fareExpirationDateTime>
     <fareApplicabilities>
      <fareApplicability outbound="SEMI-OPEN" type="SCHEDULE"/>
     </fareApplicabilities>
    </fareCode>
   </fareCodes>
  </passengerReference>
 </passengerReferences>
 <prices>
  <price type="TICKET" currency="GBP">8.00</price>
 </prices>
 <commissions>
  <commission type="DISTRIBUTOR" currency="GBP">0.42</commission>
 </commissions>
 <rules>
  ...
 </rules>
</ticketableFare>
```

## Example 5: Optional prices for PlusBus reservations

In the UK market travelers can buy a PlusBus ticket with their rail ticket. A PlusBus ticket allows the traveler to travel on buses at the origin or the destination at a single low price.

For example, if a traveler buys a return ticket for Colchester to Ipswich, and in addition, as an add-on, purchases a PlusBus ticket for Colchester, then the traveler can travel for free on all buses in Ipswich.

PlusBus tickets are purchased per travel point (origin and/or destination) and can only be bought together with the rail ticket. They cannot be bought for any other city and must be accompanied by the rail ticket.

In SilverCore, PlusBus tickets are provided as optional prices during shopping in optionalPricesShopping in a similar way to how London travelcards are returned in the SilverCore messages.

For example, in the case of a single rail journey from Colchester to Ipswich, there will be an optional price to acquire a PlusBus ticket for Liverpool and an optional price to acquire a PlusBus ticket for Manchester (two optional prices and the traveler may opt to buy both) as part of the ticketableFare for the traveler.

In the booking process, the request specifies which PlusBus tickets are to be booked, by indicating which optionalPrices are requested through the optionalPricesRequested element.

Optional pricing for PlusBus works in a similar way to Travelcards. When PlusBus tickets are available, the `<optionalPrices>` element in the shopping response includes an item price, a description, the `<applicableTravelSegments>` list, and a set of consumption rules:

```XML
<ns2:pointToPointPrice priceID="PRICE_P_1_0">
❶ <totalPrice currency="GBP">8.70</totalPrice>
 <ticketableFares>
  <ticketableFare>
❷  <totalPrice currency="GBP">8.70</totalPrice>
   <passengerReferences>
    <passengerReference>
     <passengerIDRef>PAX_SPEC_0</passengerIDRef>
     <passengerTypeCode>A</passengerTypeCode>
     <fareCodes>
      <fareCode code="LER-SDS-00000-STD-1">
       <serviceClass>THIRD</serviceClass>
       <travelSegmentIDRef>LS_1_0_TS_0</travelSegmentIDRef>
       <cabinClass>Standard</cabinClass>
       <rewardsEligible>NO</rewardsEligible>
       <fareClass>ANYTIME_DAY</fareClass>
       <fareDisplayName>Anytime Day Single</fareDisplayName>
       <openReturn>NO</openReturn>
       <reservable>NOT_POSSIBLE</reservable>
       <fareExpirationDateTime>2021-04-26T03:29:00Z</fareExpirationDateTime>
       <fareApplicabilities>
        <fareApplicability outbound="OPEN" type="SCHEDULE"/>
       </fareApplicabilities>
      </fareCode>
     </fareCodes>
    </passengerReference>
   </passengerReferences>
   <prices>
    <price type="TICKET" currency="GBP">8.70</price>
   </prices>
❸  <optionalPrices>
❹   <optionalPrice optionalPriceID="PRICE_P_1_0_0_OP_0" category="LOCAL_BUS">
     <price type="LOCAL_SERVICE" currency="GBP">3.30</price>
     <description>PlusBus - Ipswich+Bus - Adult</description>
     <applicableTravelSegments minQuantity="1" maxQuantity="1">
      <travelSegmentIDRef>LS_1_0_TS_0</travelSegmentIDRef>
     </applicableTravelSegments>
     <rules>...</rules>
     <fareCodes>
      <fareCode code="PLB-PBD-00009-STD-1">
       <serviceClass>THIRD</serviceClass>
       <travelSegmentIDRef>LS_1_0_TS_0</travelSegmentIDRef>
       <cabinClass>Standard</cabinClass>
       <fareClass>PLUSBUS_DAY</fareClass>
       <fareDisplayName>PlusBus Day</fareDisplayName>
      </fareCode>
     </fareCodes>
     <ticketingOptionsAvailable TOD="true" ETK="false" PAH="false"
      PRN="true" EML="false" DEPARTURE_STATION_TOD="true" SMS="false" XVD="false"
      SCT="false"/>
     <consumptionRules>
      <ageBreakConsumptionRule minQuantity="1" maxQuantity="1">
       <minAge>16</minAge>
      </ageBreakConsumptionRule>
      <totalPassengersConsumptionRule minQuantity="1" maxQuantity="1"/>
      <plusBusZoneConsumptionRule>
       <plusBusZone>GB@IP</plusBusZone>
      </plusBusZoneConsumptionRule>
     </consumptionRules>
     <maximumBookableQuantity>1</maximumBookableQuantity>
    </optionalPrice>
    <optionalPrice optionalPriceID="PRICE_P_1_0_0_OP_1" category="LOCAL_BUS">
     <price type="LOCAL_SERVICE" currency="GBP">4.20</price>
     <description>PlusBus - Colchr Stns+Bus - Adult</description>
     <applicableTravelSegments minQuantity="1" maxQuantity="1">
      <travelSegmentIDRef>LS_1_0_TS_0</travelSegmentIDRef>
     </applicableTravelSegments>
❺   <rules>...</rules>
     <fareCodes>
❻    <fareCode code="PLB-PBD-00009-STD-1">
       <serviceClass>THIRD</serviceClass>
       <travelSegmentIDRef>LS_1_0_TS_0</travelSegmentIDRef>
       <cabinClass>Standard</cabinClass>
❼          <fareClass>PLUSBUS_DAY</fareClass>
       <fareDisplayName>PlusBus Day</fareDisplayName>
      </fareCode>
     </fareCodes>
     <ticketingOptionsAvailable TOD="true" ETK="false" PAH="false"
      PRN="true" EML="false" DEPARTURE_STATION_TOD="true" SMS="false" XVD="false"
      SCT="false"/>
❽.    <consumptionRules>
      <ageBreakConsumptionRule minQuantity="1" maxQuantity="1">
       <minAge>16</minAge>
      </ageBreakConsumptionRule>
      <totalPassengersConsumptionRule minQuantity="1" maxQuantity="1"/>
❾    <plusBusZoneConsumptionRule>
       <plusBusZone>GB-CO</plusBusZone>
      </plusBusZoneConsumptionRule>
     </consumptionRules>
❿   <maximumBookableQuantity>1</maximumBookableQuantity>
    </optionalPrice>
   </optionalPrices>
   <rules>...</rules>
   <ticketingOptionsAvailable TOD="true" ETK="false" PAH="false" PRN="true"
    EML="false" DEPARTURE_STATION_TOD="true" SMS="false" XVD="false" SCT="false"
    MVD="true"/>
   <fareOrigin type="STATION">GBCW1</fareOrigin>
   <fareOriginTerminals>
    <fareOriginTerminal type="STATION">GBCOL</fareOriginTerminal>
    <fareOriginTerminal type="STATION">GBCET</fareOriginTerminal>
   </fareOriginTerminals>
   <fareDestination type="STATION">GBIPS</fareDestination>
  </ticketableFare>
 </ticketableFares>
 <legReferences>
  <legSolutionIDRef>LS_1_0</legSolutionIDRef>
 </legReferences>
</ns2:pointToPointPrice>
```

❶ The Point-To-Point Price total price is just for the ticket and doesn't include any optional prices for PlusBus tickets.
❷ The Ticketable Fare total price is also just for the rail ticket.
❸ Because at least one Fare Code allows for a PlusBus journey, SilverCore returns the optional pricing details.
❹ Each PlusBus ticket appears as an optional price of category `LOCAL_BUS` and type `LOCAL_SERVICE`.
❺ The `<rules>` element sets the refund and exchange terms for the PlusBus ticket.
❻ The `<fareCode>` element contains additional information about the PlusBus ticket and references the Travel Segment for which it is available.
❼ The `<fareClass>` element indicates that the ticket is for a PlusBus ticket.
❽ The `<consumptionRules>` element defines the minimum age and the specific PlusBus zone.
❾ The `<plusBusZoneConsumptionRule>` element defines how a PlusBus pass optional price item may be used.  This element is only returned if the `<returnPricingDetails>` flag is set to true in the request.
❿ The `<maximumBookableQuantity>` element shows that one PlusBus ticket is available for the booking. This value is set based on the number of passengers contained within the Ticketable Fare.

If the passenger decides to purchase a PlusBus ticket, you'll need to modify the Point-To-Point Price for each affected Ticketable Fare before submitting it in the [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements){target="_blank"} message:

```XML
<book:pointToPointPrice priceID="PRICE_P_1_0">
❶ <com:totalPrice currency="GBP">12</com:totalPrice>
 <com:ticketableFares>
  <com:ticketableFare>
❷  <com:totalPrice currency="GBP">12</com:totalPrice>
   <com:passengerReferences>
    <com:passengerReference>
     <com:passengerIDRef>PAX_SPEC_0</com:passengerIDRef>
     <com:passengerTypeCode>A</com:passengerTypeCode>
     <com:fareCodes>
      <com:fareCode code="LER-SDS-00000-STD-1">
       <com:serviceClass>THIRD</com:serviceClass>
       <com:travelSegmentIDRef>LS_1_0_TS_0</com:travelSegmentIDRef>
       <com:cabinClass>Standard</com:cabinClass>
❸      <com:optionalPricesRequested>
❹        <com:optionalPriceIDRef>PRICE_P_1_0_0_OP_0</com:optionalPriceIDRef>
       </com:optionalPricesRequested>
      </com:fareCode>
     </com:fareCodes>
    </com:passengerReference>
   </com:passengerReferences>
   <com:prices>
    <com:price currency="GBP" type="TICKET">8.70</com:price>
   </com:prices>
❺	 <com:optionalPrices>
    <com:optionalPrice optionalPriceID="PRICE_P_1_0_0_OP_0" category="LOCAL_BUS">
     <com:price currency="GBP" type="LOCAL_SERVICE">3.30</com:price>
     <com:description>PlusBus - Ipswich+Bus - Adult</com:description>
     <com:applicableTravelSegments minQuantity="1" maxQuantity="1">
      <com:travelSegmentIDRef>LS_1_0_TS_0</com:travelSegmentIDRef>
     </com:applicableTravelSegments>
     <com:fareCodes>
      <com:fareCode code="PLB-PBD-00009-STD-1">
       <com:serviceClass>THIRD</com:serviceClass>
       <com:travelSegmentIDRef>LS_1_0_TS_0</com:travelSegmentIDRef>
       <com:cabinClass>Standard</com:cabinClass>
       <com:fareClass>PLUSBUS_DAY</com:fareClass>
       <com:fareDisplayName>PlusBus Day</com:fareDisplayName>
      </com:fareCode>
     </com:fareCodes>
     <com:ticketingOptionsAvailable DEPARTURE_STATION_TOD="true"
      EML="false" SCT="false" PAH="false" XVD="false" TOD="true" SMS="false"
      ETK="false" PRN="true"/>
     <com:consumptionRules>
      <com:ageBreakConsumptionRule minQuantity="1" maxQuantity="1">
       <com:minAge>16</com:minAge>
      </com:ageBreakConsumptionRule>
      <com:totalPassengersConsumptionRule minQuantity="1" maxQuantity="1"/>
      <com:plusBusZoneConsumptionRule>
       <com:plusBusZone>GB@IP</com:plusBusZone>
      </com:plusBusZoneConsumptionRule>
     </com:consumptionRules>
     <com:maximumBookableQuantity>1</com:maximumBookableQuantity>
    </com:optionalPrice>
   </com:optionalPrices>
   <com:fareOrigin type="STATION">GBCW1</com:fareOrigin>
   <com:fareDestination type="STATION">GBIPS</com:fareDestination>
  </com:ticketableFare>
 </com:ticketableFares>
 <com:legReferences>
  <com:legSolutionIDRef>LS_1_0</com:legSolutionIDRef>
 </com:legReferences>
</book:pointToPointPrice>
```

❶ Add the optional PlusBus price (3.30) to the base ticket price (8.70) in the Point-To-Point Price total.
❷ Also add the optional PlusBus price to the base ticket price in the Ticketable Fare total.
❸ For each passenger reference, insert an `<optionalPricesRequested>` element into the Fare Code for which you’re requesting the PlusBus reservation.
❹ Use the `<optionalPriceIDRef>` value to identify the `<optionalPrices>` item you’re requesting (PRICE_P_1_0_0_OP_0).
❺ Insert the relevant `<optionalPrices>` elements as returned in the [optionalPricesShoppingResponse](/v1/docs/optionalpricesshoppingresponse-elements){target="_blank"} message. You must include the `<price>`, `<description>`, `<applicableTravelSegments>`, `<fareCodes>`, and `<consumptionRules>` child elements but can optionally omit the rest of the `<optionalPrice>` data.

The resulting Booking Record will contain a new Travel Segment specifically for the PlusBus ticket, with the `<PlusBusZone>` code from the consumption rules used as the destination Travel Point:

```XML
<travelSegment travelSegmentID="TS-LYL00012N-LQC000001-2" type="TRANSFER"
 legGrouping="1">
 <originTravelPoint type="STATION">GBIPS</originTravelPoint>
 <destinationTravelPoint type="STATION">GB@IP</destinationTravelPoint>
 <departureDateTime>2021-04-25T12:36:00</departureDateTime>
 <arrivalDateTime>2021-04-25T12:36:00</arrivalDateTime>
 <marketingCarrier>UnknownBus</marketingCarrier>
 <operatingCarrier>UnknownBus</operatingCarrier>
 <supplierEquipmentType>UnknownBus-BUS</supplierEquipmentType>
 <duration>P0Y0M0DT0H1M0S</duration>
 <crossBorderInfo>false</crossBorderInfo>
 <equipmentType code="BUS">Bus</equipmentType>
 <marketingServiceName>PlusBus Day</marketingServiceName>
 <marketingInformation serviceCode="UnknownBus"/>
</travelSegment>
```

There will also be a separate Ticketable Fare referencing the PlusBus ticket:

```XML
<ticketableFare ticketableFareID="TF-LYL00012N-LQC000001-1">
 <totalPrice currency="GBP">3.30</totalPrice>
 <passengerReferences>
  <passengerReference>
   <passengerIDRef>P-LYL00012N-0</passengerIDRef>
   <passengerTypeCode>A</passengerTypeCode>
   <fareCodes>
    <fareCode code="PLB-PBD-00009-STD-1" fareCodeID="FC-LYL00012N-LQC000001-1_1">
     <serviceClass>THIRD</serviceClass>
     <travelSegmentIDRef>TS-LYL00012N-LQC000001-2</travelSegmentIDRef>
     <cabinClass>Standard</cabinClass>
     <rewardsEligible>NO</rewardsEligible>
     <fareClass>PLUSBUS_DAY</fareClass>
     <fareDisplayName>PlusBus Day</fareDisplayName>
     <openReturn>NO</openReturn>
     <reservable>NOT_POSSIBLE</reservable>
     <fareExpirationDateTime>2021-04-26T03:29:00Z</fareExpirationDateTime>
     <fareApplicabilities>
      <fareApplicability outbound="OPEN" type="SCHEDULE"/>
     </fareApplicabilities>
    </fareCode>
   </fareCodes>
  </passengerReference>
 </passengerReferences>
 <prices>
  <price type="TICKET" currency="GBP">3.30</price>
 </prices>
 <rules>...</rules>
 <fareOrigin type="STATION">GBIPS</fareOrigin>
 <fareDestination type="STATION">GB@IP</fareDestination>
</ticketableFare>
```

And a `<services>` element indicating PlusBus:

```XML
<services>
 <service category="LOCAL_BUS">PlusBus - Ipswich+Bus - Adult</service>
</services>

```

## Example 6: Optional prices for bike beservations

In the same way that some journeys offer supplementary services such as Internet and meals, some journeys also offer bike reservations.

Optional pricing for bike reservations work in a similar way to accommodations. When bike reservations are available, the `<optionalPrices>` element in the shopping response includes an item price, a description, and the `<applicableTravelSegments>` list:

```XML
<ns2:pointToPointPrice priceID="PRICE_P_1_0">
❶ <totalPrice currency="GBP">127.60</totalPrice>
 <ticketableFares>
  <ticketableFare>
❷  <totalPrice currency="GBP">127.60</totalPrice>
   <passengerReferences>
    <passengerReference>
     <passengerIDRef>PAX_SPEC_0</passengerIDRef>
     <passengerTypeCode>A</passengerTypeCode>
     <fareCodes>
      <fareCode code="GWR-SDS-00000-STD-1">
       <serviceClass>THIRD</serviceClass>
       <travelSegmentIDRef>LS_1_0_TS_0</travelSegmentIDRef>
       <cabinClass>Standard</cabinClass>
       <rewardsEligible>NO</rewardsEligible>
       <fareClass>ANYTIME_DAY</fareClass>
       <fareDisplayName>Anytime Day Single</fareDisplayName>
       <openReturn>NO</openReturn>
       <reservable>MANDATORY</reservable>
       <fareExpirationDateTime>2021-04-23T03:29:00Z</fareExpirationDateTime>
       <fareApplicabilities>
        <fareApplicability outbound="OPEN" type="SCHEDULE"/>
       </fareApplicabilities>
      </fareCode>
     </fareCodes>
    </passengerReference>
   </passengerReferences>
   <prices>
    <price type="TICKET" currency="GBP">127.60</price>
    <price type="RESERVATION" currency="GBP">0.00</price>
   </prices>
❸ <optionalPrices>
❹  <optionalPrice optionalPriceID="PRICE_P_1_0_0_OP_0" category="SUPPLEMENT">
     <price type="SUPPLEMENT" supplementCategory="BIKE"
      supplementCode="BIK" currency="GBP">0.00</price>
     <description>BICYCLE RESERVATION</description>
     <applicableTravelSegments minQuantity="1" maxQuantity="1">
      <travelSegmentIDRef>LS_1_0_TS_0</travelSegmentIDRef>
     </applicableTravelSegments>
     <applicablePassengerIDRefs>
      <applicablePassengerIDRef>PAX_SPEC_0</applicablePassengerIDRef>
     </applicablePassengerIDRefs>
     <supplementAvailabilities>
❺      <supplementAvailability
       travelSegmentIDRef="LS_1_0_TS_0">4</supplementAvailability>
     </supplementAvailabilities>
    </optionalPrice>
   </optionalPrices>
❻ <rules>...</rules>
   <ticketingOptionsAvailable TOD="true" ETK="true" PAH="false" PRN="true"
    EML="false" DEPARTURE_STATION_TOD="true" SMS="false" XVD="true" SCT="false"
    MVD="false"/>
   <fareOrigin type="STATION">GBCDF</fareOrigin>
   <fareDestination type="STATION">GBLNT</fareDestination>
   <fareDestinationTerminals>
    <fareDestinationTerminal type="STATION">GBLBG</fareDestinationTerminal>
    <fareDestinationTerminal type="STATION">GBZEP</fareDestinationTerminal>
    <fareDestinationTerminal type="STATION">GBCHX</fareDestinationTerminal>
    <fareDestinationTerminal type="STATION">GBQQP</fareDestinationTerminal>
    <fareDestinationTerminal type="STATION">GBCST</fareDestinationTerminal>
    <fareDestinationTerminal type="STATION">GBWAE</fareDestinationTerminal>
    <fareDestinationTerminal type="STATION">GBCTK</fareDestinationTerminal>
    <fareDestinationTerminal type="STATION">GBQQW</fareDestinationTerminal>
    <fareDestinationTerminal type="STATION">GBBFR</fareDestinationTerminal>
   </fareDestinationTerminals>
  </ticketableFare>
 </ticketableFares>
 <legReferences>
  <legSolutionIDRef>LS_1_0</legSolutionIDRef>
 </legReferences>
</ns2:pointToPointPrice>
```

❶ The Point-To-Point Price total price is just for the ticket and doesn't include any optional prices for bike reservations.
❷ The Ticketable Fare total price is also just for the rail ticket.
❸ Because at least one Fare Code allows for a bike reservation, SilverCore returns the optional pricing details.
❹ Each bike reservation appears as an optional price with `category="SUPPLEMENT"`, `supplementCategory="BIKE"`, and `supplementCode="BIK"`.
❺ The `<supplementAvailability>` element indicates the number of available supplements per travel segment `<travelSegmentIDRef>`.
❻ The `<rules>` element sets the refund and exchange terms for the PlusBus ticket.

If the passenger decides to purchase a bike reservation, you'll need to modify the Point-To-Point Price for each affected Ticketable Fare before submitting it in the [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements){target="_blank"} message:

```XML
<book:pointToPointPrice priceID="PRICE_P_1_0">
 <com:totalPrice currency="GBP">127.6</com:totalPrice>
 <com:ticketableFares>
  <com:ticketableFare>
   <com:totalPrice currency="GBP">127.6</com:totalPrice>
   <com:passengerReferences>
    <com:passengerReference>
     <com:passengerIDRef>PAX_SPEC_0</com:passengerIDRef>
     <com:passengerTypeCode>A</com:passengerTypeCode>
     <com:fareCodes>
      <com:fareCode code="GWR-SDS-00000-STD-1">
       <com:serviceClass>THIRD</com:serviceClass>
       <com:travelSegmentIDRef>LS_1_0_TS_0</com:travelSegmentIDRef>
       <com:cabinClass>Standard</com:cabinClass>
       <com:optionalPricesRequested>
        <com:optionalPriceIDRef>PRICE_P_1_0_0_OP_0</com:optionalPriceIDRef>
       </com:optionalPricesRequested>
      </com:fareCode>
     </com:fareCodes>
    </com:passengerReference>
   </com:passengerReferences>
   <com:prices>
    <com:price currency="GBP" type="TICKET">127.60</com:price>
    <com:price currency="GBP" type="RESERVATION">0.00</com:price>
   </com:prices>
   <com:optionalPrices>
    <com:optionalPrice optionalPriceID="PRICE_P_1_0_0_OP_0" category="SUPPLEMENT">
     <com:price supplementCode="BIK" currency="GBP" type="SUPPLEMENT"
      supplementCategory="BIKE">0.00</com:price>
     <com:description>BICYCLE RESERVATION</com:description>
     <com:applicableTravelSegments minQuantity="1" maxQuantity="1">
      <com:travelSegmentIDRef>LS_1_0_TS_0</com:travelSegmentIDRef>
     </com:applicableTravelSegments>
     <com:applicablePassengerIDRefs>
      <com:applicablePassengerIDRef>PAX_SPEC_0</com:applicablePassengerIDRef>
     </com:applicablePassengerIDRefs>
     <com:supplementAvailabilities>
      <com:supplementAvailability
       travelSegmentIDRef="LS_1_0_TS_0">4</com:supplementAvailability>
     </com:supplementAvailabilities>
    </com:optionalPrice>
   </com:optionalPrices>
   <com:fareOrigin type="STATION">GBCDF</com:fareOrigin>
   <com:fareDestination type="STATION">GBLNT</com:fareDestination>
  </com:ticketableFare>
 </com:ticketableFares>
 <com:legReferences>
  <com:legSolutionIDRef>LS_1_0</com:legSolutionIDRef>
 </com:legReferences>
</book:pointToPointPrice>
```
The resulting Booking Record will contain a `<supplements>` element for the bike reservation, with `<supplmentCode="BIK" category="BIKE">`:

```XML
<supplements>
 <supplement code="BIK" category="BIKE">
  <price currency="GBP">0.00</price>
  <description>BICYCLE RESERVATION</description>
  <applicableFareCodeIDRefs>
   <applicableFareCodeIDRef>FC-KZY000139-OGL000001-0_0</applicableFareCodeIDRef>
  </applicableFareCodeIDRefs>
  <seatAssignment>
   <coach>B</coach>
   <number>98</number>
   <direction>UNASSIGNED</direction>
  </seatAssignment>
 </supplement>
</supplements>
```
