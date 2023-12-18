:::(Error) (In this article, as part of our series of How-Tos, we'll look at how to use the SilverSeatmap service to retrieve a train layout response and how the service fits into SilverCore's shopping and booking flows.)
:::

## Intro
:::(Warning) (This document is currently provided as a Beta and is therefore subject to change.  Please review the recent updates for more information.)
:::

:::(Warning) (The SilverSeatmap service is currently only available for SNCF and Swedish Rail.  The seat map data returned for each carrier can differ, and the completeness of that data is entirely dependent on the data provider.)
:::

:::(Info) (For SNCF, seat map data is restricted to first-class carriages/decks.)
:::

:::(Info) (For Swedish Rail, seat map data is available for first- and second-class carriages/decks.)
:::

The SilverSeatmap service provides a REST API called Seatmap API, allowing Partners to retrieve details of carriages/decks, seat availability, orientation, and ancillary information (seat map data).  Partners can then use this data to provide a visual representation to customers.

The Seatmap API request takes several mandatory inputs, including an origin, destination, date/time, train number, number of passengers, one or more fare types, and optional inputs such as a train operator code.

For the SNCF, these inputs are bound up in two attributes, externalproposal and externaltravelsegment returned in the [validateBookingRecordResponse](/v1/docs/validatebookingrecordinformationresponse-elements){target="_blank"} message.

## Recent updates

| **Change** | **Description** |
| :-- | :-- |
| 29 March, 2022 | Release of Beta version. |
| 13 April, 2022 | Added details of creating a booking, passing in a coach and seat number. |
| 27 April, 2022 | Added details of new`<externalSupplier>` attribute returned in the [validateBookinRecordResponse](/v1/docs/validatebookingrecordinformationresponse-elements){target="_blank"}. |
| 2 May, 2022 | Updated URL to get seat map data. |
| 10 May, 2022 | Added details of using Seatmap service for Swedish Rail.  Updated URL to get seat map data for SNCF. |
| 12 May, 2022 | Added details of error returned for Swedish Rail when carrier can't fulfill a booking. |
| 13 May, 2022 | Added details of nullable objects returned by the SeatMap API. |

<!---
## Samples

[\&lt;img src=&quot;https://files.document360.io/c53b2e89-8de3-47ad-adf4-5a70ab761260/Images/Documentation/attach.png&quot; width=&quot;20px&quot;\&gt; Get the SilverCore samples used in this How-To.]()
--->

## Using Seat Map API with SilverCore

Refer to the following diagram that shows how the use of the Seat Map API fits into the shopping and booking flow within SilverCore.

<p align="center">
  <img width="70%" height="70%" src="https://files.document360.io/c53b2e89-8de3-47ad-adf4-5a70ab761260/Images/Documentation/Conceptual%20Seatmap%20Sequence%20%284%29%281%29%281%29.png">
</p>

## Get seat map availability for a travel segment

Following a [pointToPointShoppingRequest](/v1/docs/pointtopointshoppingrequest-elements){target="_blank"} or [exchangeSearchRequest](/v1/docs/exchangesearchrequest-elements){target="_blank"} for the chosen journey, the `<availableSeatPreferences>` element in the [pointToPointShippingResponse](/v1/docs/pointtopointshoppingresponse-elements){target="_blank"} and [exchangeSearchResponse ](/v1/docs/exchangesearchresponse-elements){target="_blank"} indicates whether seat map data is available for a specific travel segment.

**Example request**:
```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns2="http://railgds.net/ws/shopping" xmlns="http://railgds.net/ws/commontypes">
  <soapenv:Header/>
  <soapenv:Body>
    <ns2:pointToPointShoppingRequest>
      <context>
        <distributorCode>DEMO</distributorCode>
        <pointOfSaleCode>FR</pointOfSaleCode>
        <com:channelCode>CON</com:channelCode>
      </context>
      <ns2:pointToPointShoppingQuery>
        <ns2:travelPointPairs>
          <ns2:travelPointPair>
            <originTravelPoint type="STATION">FR#PA</originTravelPoint>
            <destinationTravelPoint type="STATION">FRZFQ</destinationTravelPoint>
            <departureDateTimeWindow>
              <date>2022-02-26</date>
              <time>08:00:00</time>
            </departureDateTimeWindow>
          </ns2:travelPointPair>
        </ns2:travelPointPairs>
        <ns2:passengerSpecs>
          <ns2:passengerSpec>
            <age>35</age>
          </ns2:passengerSpec>
        </ns2:passengerSpecs>
      </ns2:pointToPointShoppingQuery>
    </ns2:pointToPointShoppingRequest>
  </soapenv:Body>
</soapenv:Envelope>
```

**Example response** :

```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Body>
    <ns2:pointToPointShoppingResponse xmlns:ns2="http://railgds.net/ws/shopping" xmlns="http://railgds.net/ws/commontypes">
      <requestStatus systemId="bYZBHc">
        <success>true</success>
      </requestStatus>
      <ns2:results>
        <ns2:legs>
          <ns2:leg legID="L_1">
            <originTravelPoint type="STATION">FR#PA</originTravelPoint>
            <destinationTravelPoint type="STATION">FRZFQ</destinationTravelPoint>
            <legSolutions>
              <legSolution legSolutionID="LS_1_3">
                <numberOfConnections>0</numberOfConnections>
                <travelSegments>
                  <travelSegment sequence="0" travelSegmentID="LS_1_3_TS_0" type="TRAIN">
                    <originTravelPoint type="STATION">FRPMO</originTravelPoint>
                    <destinationTravelPoint type="STATION">FRZFQ</destinationTravelPoint>
                    <departureDateTime>2022-02-26T08:01:00</departureDateTime>
                    <arrivalDateTime>2022-02-26T10:18:00</arrivalDateTime>
                    <designator>8435</designator>
                    <marketingCarrier>TGVINOUI</marketingCarrier>
                    <operatingCarrier>TGVINOUI</operatingCarrier>
                    <supplierEquipmentType>TGVINOUI-HSP</supplierEquipmentType>
                    <duration>P0Y0M0DT2H17M0S</duration>
                    <crossBorderInfo>false</crossBorderInfo>
                    <equipmentType code="HSP">High-Speed</equipmentType>
                    <carbonData unit="kgCO2">4.42</carbonData>
                    <marketingInformation serviceCode="TGVINOUI"/>
                    <scheduleConfirmed>true</scheduleConfirmed>
                  </travelSegment>
                </travelSegments>
                <overtakenJourney>false</overtakenJourney>
                <duration>P0Y0M0DT2H17M0S</duration>
                <passengerInformationRequired>
                  <passengerInformation type="PASSENGER_NAME" allPassengers="true"/>
                  <passengerInformation type="DATE_OF_BIRTH" allPassengers="true"/>
                  <passengerInformation type="PASSENGER_EMAIL" allPassengers="true"/>
                </passengerInformationRequired>
                <availableSeatPreferences>
                  <availableSeatPreference marketingCarrier="TGVINOUI" serviceClass="FIRST" supplierEquipmentType="TGVINOUI-HSP" travelSegmentIDRef="LS_1_3_TS_0">AISLE</availableSeatPreference>
                  <availableSeatPreference marketingCarrier="TGVINOUI" serviceClass="FIRST" supplierEquipmentType="TGVINOUI-HSP" travelSegmentIDRef="LS_1_3_TS_0">BOTTOM</availableSeatPreference>
                  <availableSeatPreference marketingCarrier="TGVINOUI" serviceClass="FIRST" supplierEquipmentType="TGVINOUI-HSP" travelSegmentIDRef="LS_1_3_TS_0">FORWARD</availableSeatPreference>
                  <availableSeatPreference marketingCarrier="TGVINOUI" serviceClass="FIRST" supplierEquipmentType="TGVINOUI-HSP" travelSegmentIDRef="LS_1_3_TS_0">NEARBY</availableSeatPreference>
❶                  <availableSeatPreference marketingCarrier="TGVINOUI" serviceClass="FIRST" supplierEquipmentType="TGVINOUI-HSP" travelSegmentIDRef="LS_1_3_TS_0">SEAT_MAP</availableSeatPreference>
                  <availableSeatPreference marketingCarrier="TGVINOUI" serviceClass="FIRST" supplierEquipmentType="TGVINOUI-HSP" travelSegmentIDRef="LS_1_3_TS_0">SIDEBYSIDE</availableSeatPreference>
                  <availableSeatPreference marketingCarrier="TGVINOUI" serviceClass="FIRST" supplierEquipmentType="TGVINOUI-HSP" travelSegmentIDRef="LS_1_3_TS_0">SINGLE</availableSeatPreference>
                  <availableSeatPreference marketingCarrier="TGVINOUI" serviceClass="FIRST" supplierEquipmentType="TGVINOUI-HSP" travelSegmentIDRef="LS_1_3_TS_0">TOP</availableSeatPreference>
                  <availableSeatPreference marketingCarrier="TGVINOUI" serviceClass="FIRST" supplierEquipmentType="TGVINOUI-HSP" travelSegmentIDRef="LS_1_3_TS_0">TWO_FACING</availableSeatPreference>
                  <availableSeatPreference marketingCarrier="TGVINOUI" serviceClass="FIRST" supplierEquipmentType="TGVINOUI-HSP" travelSegmentIDRef="LS_1_3_TS_0">WINDOW</availableSeatPreference>
                  <availableSeatPreference marketingCarrier="TGVINOUI" serviceClass="SECOND" supplierEquipmentType="TGVINOUI-HSP" travelSegmentIDRef="LS_1_3_TS_0">AISLE</availableSeatPreference>
                  <availableSeatPreference marketingCarrier="TGVINOUI" serviceClass="SECOND" supplierEquipmentType="TGVINOUI-HSP" travelSegmentIDRef="LS_1_3_TS_0">BOTTOM</availableSeatPreference>
                  <availableSeatPreference marketingCarrier="TGVINOUI" serviceClass="SECOND" supplierEquipmentType="TGVINOUI-HSP" travelSegmentIDRef="LS_1_3_TS_0">FORWARD</availableSeatPreference>
                  <availableSeatPreference marketingCarrier="TGVINOUI" serviceClass="SECOND" supplierEquipmentType="TGVINOUI-HSP" travelSegmentIDRef="LS_1_3_TS_0">NEARBY</availableSeatPreference>
                  <availableSeatPreference marketingCarrier="TGVINOUI" serviceClass="SECOND" supplierEquipmentType="TGVINOUI-HSP" travelSegmentIDRef="LS_1_3_TS_0">TOP</availableSeatPreference>
                  <availableSeatPreference marketingCarrier="TGVINOUI" serviceClass="SECOND" supplierEquipmentType="TGVINOUI-HSP" travelSegmentIDRef="LS_1_3_TS_0">WINDOW</availableSeatPreference>
                </availableSeatPreferences>
                <loyaltyCards>...</loyaltyCards>
              </legSolution>
            </legSolutions>
          </ns2:leg>
        </ns2:legs>
        <ns2:passengers>...</ns2:passengers/>
        <ns2:fareInformation>...</ns2:fareInformation>
      </ns2:results>
    </ns2:pointToPointShoppingResponse>
  </soap:Body>
</soap:Envelope>
```

❶ SEAT_MAP in `<availableSeatPreference>` indicates seat map data is obtainable for the travel segment, as shown in the following example.

**Example** :
```xml
<availableSeatPreference marketingCarrier="TGVINOUI" serviceClass="FIRST"supplierEquipmentType="TGVINOUI-HSP" travelSegmentIDRef="LS_1_3_TS_0">SEAT_MAP</availableSeatPreference>
```

## Get unique values required by Seatmap API

To get the values required by the Seatmap API, make a [validateBookingRecordInformationRequest](/v1/docs/validatebookingrecordinformationrequest-elements){target="_blank"} containing a valid legSolution.

The resulting response will include the `<seatMapInformation>` element, as shown in the following examples.

**Example response for the SNCF** :
```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Body>
    <ns2:validateBookingRecordInformationResponse   conversationToken="94bd83bc-6d20-43a2-a021-1ea60855fa43"   xmlns:ns2="http://railgds.net/ws/booking"   xmlns="http://railgds.net/ws/commontypes">
      <requestStatus systemId="7VJUCm">
        <success>true</success>
      </requestStatus>
      <ns2:bookingRecordInformation>
        <ns2:orderInformationSet>
          <ns2:orderInformation>
            <ns2:travelSegments>
              <ns2:travelSegment travelSegmentID=“LS_1_3_TS_0" type=“TRAIN”>
                <originTravelPoint type=“STATION”>FRPMO</originTravelPoint>
                <destinationTravelPoint type=“STATION”>FRZFQ</destinationTravelPoint>
                <departureDateTime>2022-02-26T08:01:00</departureDateTime>
                <arrivalDateTime>2022-02-26T10:18:00</arrivalDateTime>
                <designator>8435</designator>
                <marketingCarrier>TGVINOUI</marketingCarrier>
                <operatingCarrier>TGVINOUI</operatingCarrier>
                <supplierEquipmentType>TGVINOUI-HSP</supplierEquipmentType>
                <duration>P0Y0M0DT2H17M0S</duration>
                <crossBorderInfo>false</crossBorderInfo>
                <equipmentType code=“HSP”>High-Speed</equipmentType>
                <carbonOffset>1.1</carbonOffset>
                <marketingInformation serviceCode=“TGVINOUI”/>
❶              <seatMapInformation externalProposal="6f4222fe-cc84-4188-b3ec-73dcdee45d6c-0" externalTravelSegment="_:437e33aa-974a-402b-9f5b-8aced7efaae6" externalSupplier="es3"/>
                <scheduleConfirmed>true</scheduleConfirmed>
              </ns2:travelSegment>
            </ns2:travelSegments>
            <ns2:ticketingOptions>
              <ns2:ticketingOption code=“PAH” type=“E_TICKET”>
                <description>Print At Home</description>
                <fee isRefundable=“false” currency=“EUR”>0.00</fee>
              </ns2:ticketingOption>
              <ns2:ticketingOption code=“TOF” type=“ON_DEPARTURE” departureStation=“false”>
                <description>Collect at Ticket Office</description>
                <fee isRefundable=“false” currency=“EUR”>0.00</fee>
              </ns2:ticketingOption>
              <ns2:ticketingOption code=“TVM” type=“ON_DEPARTURE” departureStation=“false”>
                <description>Collect at Ticket Vending Machine or Kiosk</description>
                <fee isRefundable=“false” currency=“EUR”>0.00</fee>
              </ns2:ticketingOption>
            </ns2:ticketingOptions>
            <ns2:confirmationInformationRequired>false</ns2:confirmationInformationRequired>
            <ns2:paymentCardRequiredForConfirmationInformation>false</ns2:paymentCardRequiredForConfirmationInformation>
            <ns2:availableTicketLanguages>
              <ns2:language>fr</ns2:language>
              <ns2:language>en</ns2:language>
            </ns2:availableTicketLanguages>
          </ns2:orderInformation>
        </ns2:orderInformationSet>
        <ns2:paymentRequirements>
          <acceptableFormsOfPayment>
            <formOfPayment type=“CC”>AX</formOfPayment>
            <formOfPayment type=“CC” authenticatePayerRequired=“true”>VI</formOfPayment>
            <formOfPayment type=“CC” authenticatePayerRequired=“true”>CA</formOfPayment>
            <formOfPayment type=“DB”>SD</formOfPayment>
            <formOfPayment type=“DB”>VD</formOfPayment>
          </acceptableFormsOfPayment>
          <confirmationUponAddPayment>true</confirmationUponAddPayment>
        </ns2:paymentRequirements>
        <ns2:serviceFeeAllowed>true</ns2:serviceFeeAllowed>
        <ns2:creditCardSurchargeFeeApplicable>false</ns2:creditCardSurchargeFeeApplicable>
      </ns2:bookingRecordInformation>
    </ns2:validateBookingRecordInformationResponse>
  </soap:Body>
</soap:Envelope>
```

❶ `externalproposal` and `externaltravelsegment` are only valid for 30 minutes after you get the [validateBookingRecordInformationResponse](/v1/docs/validatebookingrecordinformationresponse-elements){target="_blank"}.

**Example response for Swedish Rail** :
```xml
<?xml version="1.0" encoding="UTF-8"?>
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Body>
    <ns2:validateBookingRecordInformationResponse conversationToken="94bd83bc-6d20-43a2-a021-1ea60855fa43" xmlns:ns2="http://railgds.net/ws/booking" xmlns="http://railgds.net/ws/commontypes">
      <requestStatus systemId="7VJUCm">
        <success>true</success>
      </requestStatus>
      <ns2:bookingRecordInformation>
        <ns2:orderInformationSet>
          <ns2:orderInformation>
            <ns2:travelSegments>
              <ns2:travelSegment travelSegmentID="LS_1_1_TS_0" type="TRAIN">
                <originTravelPoint type="STATION">SEXEV</originTravelPoint>
                <destinationTravelPoint type="STATION">SEGOC</destinationTravelPoint>
                <departureDateTime>2022-04-30T06:24:00</departureDateTime>
                <arrivalDateTime>2022-04-30T09:35:00</arrivalDateTime>
                <designator>421</designator>
                <marketingCarrier>SJ</marketingCarrier>
                <operatingCarrier>SJ</operatingCarrier>
                <supplierEquipmentType>SJ-Train-X2</supplierEquipmentType>
                <duration>P0Y0M0DT3H11M0S</duration>
                <crossBorderInfo>false</crossBorderInfo>
                <equipmentType code="HSP">High-Speed</equipmentType>
                <marketingServiceName>X 2000</marketingServiceName>
                <marketingInformation serviceCode="SJ" />
                <seatMapInformation carrier="74" vehicleId="421" boardLocation="74:1" alightLocation="74:2" departureDateTime="2022-04-30" ticketClass="FK" externalSupplier="es2" />
              </ns2:travelSegment>
              <ns2:travelSegment travelSegmentID="LS_1_1_TS_1" type="TRAIN">
                <originTravelPoint type="STATION">SEGOC</originTravelPoint>
                <destinationTravelPoint type="STATION">SEXFP</destinationTravelPoint>
                <departureDateTime>2022-04-30T10:24:00</departureDateTime>
                <arrivalDateTime>2022-04-30T13:00:00</arrivalDateTime>
                <designator>487</designator>
                <marketingCarrier>SJ</marketingCarrier>
                <operatingCarrier>SJ</operatingCarrier>
                <supplierEquipmentType>SJ-Train-X2</supplierEquipmentType>
                <duration>P0Y0M0DT2H36M0S</duration>
                <crossBorderInfo>false</crossBorderInfo>
                <equipmentType code="HSP">High-Speed</equipmentType>
                <marketingServiceName>SJ 3000</marketingServiceName>
                <marketingInformation serviceCode="SJ" />
                <seatMapInformation carrier="74" vehicleId="487" boardLocation="74:2" alightLocation="74:3" departureDateTime="2022-04-30" ticketClass="FK" externalSupplier="es2" />
              </ns2:travelSegment>
              <ns2:travelSegment travelSegmentID="LS_1_1_TS_2" type="BUS">
                <originTravelPoint type="STATION">SEXFP</originTravelPoint>
                <destinationTravelPoint type="STATION">SEMGS</destinationTravelPoint>
                <departureDateTime>2022-04-30T13:16:00</departureDateTime>
                <arrivalDateTime>2022-04-30T13:30:00</arrivalDateTime>
                <designator>5</designator>
                <marketingCarrier>Skanetrafiken</marketingCarrier>
                <operatingCarrier>Skanetrafiken</operatingCarrier>
                <supplierEquipmentType>Skanetrafiken-Bus-LT</supplierEquipmentType>
                <duration>P0Y0M0DT0H14M0S</duration>
                <crossBorderInfo>false</crossBorderInfo>
                <equipmentType code="BUS">Bus</equipmentType>
                <marketingServiceName>Skånetrafiken</marketingServiceName>
                <marketingInformation serviceCode="Skanetrafiken" />
              </ns2:travelSegment>
            </ns2:travelSegments>
            <ns2:ticketingOptions>
              <ns2:ticketingOption code="EML" type="E_MAIL">
                <description>Ticket by E-mail</description>
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
            <ns2:confirmationInformationRequired>false</ns2:confirmationInformationRequired>
            <ns2:paymentCardRequiredForConfirmationInformation>false</ns2:paymentCardRequiredForConfirmationInformation>
            <ns2:availableTicketLanguages>
              <ns2:language>sv</ns2:language>
            </ns2:availableTicketLanguages>
          </ns2:orderInformation>
        </ns2:orderInformationSet>
        <ns2:paymentRequirements>
          <acceptableFormsOfPayment>
            <formOfPayment type="CC">VI</formOfPayment>
            <formOfPayment type="CC">CA</formOfPayment>
            <formOfPayment type="DB">SD</formOfPayment>
            <formOfPayment type="DB">VD</formOfPayment>
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

The `<seatMapInformation>` element has the following attributes:

| Attribute | Returned in FR | Returned in SE | Description |
| :-- | :-- | :-- | :--|
| `externalProposal` | X | | An Id that encompasses information from the Supply Channel, including journey, price, and fare conditions. |
| `externalTravelSegment` | X | | The Id of the segment where the seatMap can be requested. |
| `externalSupplier` | X | X | A supplier code denoting the supplier against which the request for a seat map was made:<br>es1 - *Not currently supported*.<br>es2 - Swedish Rail.<br>es3 - SNCF. |
| `carrier` | | X | The segment producer code. |
| `vehicleId` | | X | The transport Id. |
| `boardLocation` | | X | Equivalent to the departure location. |
| `alightLocation` | | X | Equivalent to the arrival location. |
| `departureDateTime` | | X | The date of departure. |
| `ticketClass` | | X | The fare's ticket class. |

## Get seat map data

### Request

#### SNCF

To obtain seat map data for SNCF, make a GET request to the following URL:

```html
https://api-dev.silverrail.io/sms/[externalSupplier]/api/v2.0/SeatMap/Transactional?externalproposal=&externaltravelsegment=
```

**Where** :

- `externalSupplier` is a supplier code.
- `externalproposal` is a unique identifier.
- `externaltravelsegment` is a unique identifier.

**Example request** :

```html
https://api-dev.silverrail.io/sms/es3/api/v2.0/SeatMap/Transactional?externalproposal=f58afd31-0141-4a33-b626-4d82c0f84c38-9&externaltravelsegment=1e636b74-0102-4141-a4a9-a192fbd4626b
```

#### Swedish Rail

To obtain seat map data for the Swedish Rail, make a GET request to the following URL:

```html
https://api-uat.silverrail.io/sms/[externalSupplier]/api/v2.0/seatmap/vehicle?carrier=&vehicleId=&boardLocation=&alightLocation=&departureDateTime=ticketClass=
```

**Where** :

- `externalSupplier` is a supplier code.
- `carrier` - is a segment producer code.
- `vehicleId` is a transport Id.
- `boardLocation` is equivalent to the departure location.
- `alightLocation` is equivalent to the arrival location.
- `departureDateTime` is the scheduled departure date.
- `ticketClass` - is the fare's ticket class.

**Example request** :

```html
https://api-uat.silverrail.io/sms/es2/api/v2.0/seatmap/vehicle?carrier=74&vehicleId=532&boardLocation=74:3&alightLocation=74:1&departureDateTime=2022-04-30&ticketClass=AK
```

### Response

A successful 200 response will result in a JSON object containing the data necessary to generate a visual representation of the train, coach, and seats for the selected journey in your front end.

#### Status codes

| Code | Description | Steps to resolve |
| :-- | :--| :--|
|200 | Success. | n/a |
| 400 | Invalid parameter or invalid request. | Ensure your request conforms to the example shown above. |
| 404 | Not found.  There is no seat map available for the journey. | n/a|
| 503 | External service not responding | Retry.  If the issue persists, please raise a JIRA for the Service Ops team.|

#### Successful response - 200

```json
{
  "vehicle": {
    "id": "string",
    "travelDirection": "NotSpecified",
    "coaches": [
      {
        "id": "string",
        "beginsVehicleSegmentChange": true,
        "decks": [
          {
            "id": "string",
            "cacheId": "string",
            "floorPlanSize": {
              "width": 0,
              "height": 0
            },
            "defaultSeatSize": {
              "width": 0,
              "height": 0
            },
            "layoutImage": {
              "url": "string",
              "supportsOverlay": true,
              "isReversed": true
            },
            "function": "Unspecified",
            "numAvailableSeats": 0,
            "seats": [
              {
                "position": {
                  "x": 0,
                  "y": 0,
                  "width": 0,
                  "height": 0
                },
                "attributes": [
                  "string"
                ],
                "id": "string",
                "status": "FREE",
                "ticketClass": "string",
                "direction": "Unspecified",
                "type": "Other"
              }
            ],
            "layoutItems": [
              {
                "position": {
                  "x": 0,
                  "y": 0,
                  "width": 0,
                  "height": 0
                },
                "attributes": [
                  "string"
                ],
                "label": "string",
                "type": "Table"
              }
            ]
          }
        ]
      }
    ],
    "changesDirectionOnRoute": true
  },
  "displayNames": [
  {
   "nameCode": "paoSeatmapUi.paoCoachSelector.coachSelectorLabel",
   "localLabel": "Coach"
  },
  {
   "nameCode": "paoSeatmapUi.paoCoachSelector.deckSelectorLabel",
   "localLabel": "Deck"
  },
  {
   "nameCode": "paoSeatmapUi.paoCoachSelector.deckTypelabels.TOP",
   "localLabel": "Top"
  },
  {
   "nameCode": "paoSeatmapUi.paoCoachSelector.deckTypelabels.BOTTOM",
   "localLabel": "Bottom"
  },
  {
   "nameCode": "paoSeatmapUi.paoDeckgrid.label.TOP",
   "localLabel": "Top deck"
  },
  {
   "nameCode": "paoSeatmapUi.paoCoachSelector.fullLabels.true",
   "localLabel": "Unavailable"
  },
  {
   "nameCode": "paoSeatmapUi.paoCoachSelector.fullLabels.false",
   "localLabel": "Available"
  },
  {
   "nameCode": "paoSeatmapUi.warning.CHANGING_COACH_WITH_SELECTED_SEAT",
   "localLabel": "It is not possible to choose your seats in several different coaches.\nDo you want to erase the current selection in order to get to the chosen coach?"
  },
  {
   "nameCode": "paoSeatmapUi.warning.CHANGING_DECK_WITH_SELECTED_SEAT",
   "localLabel": "It is not possible to choose your seats in several different deck.\nDo you want to delete the current selection in order to go to the chosen deck?"
  },
  {
   "nameCode": "paoSeatmapUi.warning.SEAT_ALREADY_TAKEN",
   "localLabel": "This seat is already assigned. Do you want to assign it to this new passenger?"
  }
 ]
}
```
#### Response schema

The seat map response is a JSON payload consisting of the following objects:

> **vehicle**

A container object for the response that describes the vehicle, its coaches, seats, and layout items.
***
> vehicle
**id** (Optional)

A unique identifier for the train.
***
> vehicle
**travelDirection**

An indication of the vehicle's direction of travel.  Possible values are:

-	`NotSpecified`
-	`Left`
-	`Right`
***
> vehicle
**coaches** (Optional)

A container object for each coach in the vehicle, sorted by coach sequence.
***
> vehicle
coaches
**coach**

The properties of an individual coach in a vehicle.
***
> vehicle
coaches
coach
**id** (Optional)

A unique identifier for the coach.
***
> vehicle
coaches
coach
**beginsVehicleSegmentChange** (Optional)

For services with multiple connected segments in the coach makeup, which passengers cannot walk between, and coaches with engine cars at either end.  It may be desirable to add an end-train and begin-train graphic before this coach in a coach display for these services.  Possible values are:

-	`True` - End and begin train graphics added, as shown in the example below.
-	`False` - End and begin train graphics not added.

**Example**:
<p align="center">
  <img width="70%" height="70%" src="https://files.document360.io/c53b2e89-8de3-47ad-adf4-5a70ab761260/Images/Documentation/image%20%289%29%20%281%29.png">
</p>


***
> vehicle
coach
coaches
**decks** (Optional)

A container object for the properties of a deck and associated values and attributes required for rendering the deck in the front end.
***
> vehicle
coaches
coach
decks
**deck**

The properties, values, and attributes of an individual deck in a coach.
***
> vehicle
coaches
coach
decks
deck
**id** (Optional)

A unique identifier for the deck.
***
> vehicle
coaches
coach
decks
deck
**description** (Optional)

A description of the deck for display purposes.
***
> vehicle
coaches
coach
decks
deck
**cacheId** (Optional)

The Id of the layout for caching purposes.  If not specified, you cannot cache layout details.
***
> vehicle
coaches
coach
decks
deck
**floorPlanSize**

A container object for the size of a deck's floorplan for front-end rendering.

> vehicle
coaches
coach
decks
deck
floorPlanSize
**width**

The width of the floor plan in pixels for front-end rendering.
***
> vehicle
coaches
coach
decks
deck
floorPlanSize
**height**

The height of the floor plan in pixels for front-end rendering.
***
> vehicle
coaches
coach
decks
deck
**defaultSeatSize**

A container object for the size of each seat for front-end rendering.
***
> vehicle
coaches
coach
decks
deck
defaultSeatSize
**width**

The width of a seat in pixels for front-end rendering.
***
> vehicle
coaches
coach
decks
deck
defaultSeatSize
**height**

The width of a seat in pixels for front-end rendering.
***
> vehicle
coaches
coach
decks
deck
**layoutImage**

A container object for the layout image to use in the front end.
***
> vehicle
coaches
coach
decks
deck
layoutImage
**url**

The URL from where to fetch the vehicle image.
***
> vehicle
coaches
coach
decks
deck
layoutImage
**supportsOverlay**

Whether the seat positions in the response can be overlaid onto the image defined in `layoutImage` in absolute positions.  Possible values are:
-	`True` - Seat positions can be overlaid.
-	`False` - Seat position cannot be overlaid.
***
> vehicle
coaches
coach
decks
deck
layoutImage
**isReversed**

Whether the coach is traveling in the opposite direction compared to the supplied layout image.  Possible values are:

-	`True` - The coach is traveling in the opposite direction.
-	`False` - The coach is not traveling in the opposite direction.
***
> vehicle
coaches
coach
decks
deck
**function**

The deck's function.  Possible values are:

-	`Unspecified`.
-	`Passenger`.
-	`Bar`.
-	`Dining`.
***
> vehicle
coaches
coach
decks
deck
**numAvailableSeats**

The number of seats on the deck that are available for booking.
***
> vehicle
coaches
coach
decks
deck
**seats** (Optional)

A container object for the properties of a seat and associated values and attributes required to render seats on the deck.
***
> vehicle
coaches
coach
decks
deck
seats
**seat**

The properties, values, and attributes of each seat on the deck, including x and y position, ticket class, and seat type.
***
> vehicle
coaches
coach
decks
deck
seats
seat
**position**

A container object for the position and dimensions of a seat.
***
> vehicle
coaches
coach
decks
deck
seats
seat
position
**x**

The x position of the top-left corner of the seat.
***
> vehicle
coaches
coach
decks
deck
seats
seat
position
**y**

The y position of the top-left corner of the seat.
***
> vehicle
coaches
coach
decks
deck
seats
seat
position
**width** (Optional)

The seat's width in pixels.  If not supplied, the width defined in `defaultSeatSize` is used.
***
> vehicle
coaches
coach
decks
deck
seats
seat
position
**height** (Optional)

The seat's height in pixels.  If not supplied, the height defined in `defaultSeatSize` is used.
***
> vehicle
coaches
coach
decks
deck
seats
seat
**attributes** (Optional)

Seat attributes as provided by the Seat Map supplier.
***
> vehicle
coaches
coach
decks
deck
seats
seat
**id** (Optional)

The seat's unique identifier.
***
> vehicle
coaches
coach
decks
deck
seats
seat
**status**

The seat's status for booking.  Possible values are:

-	`FREE` - Seat is available and can be booked.
-	`ALLOCATED` - Seat is unavailable and cannot be booked.
	`RESTRICTED` - Seat may be available, but booking that seat is restricted.  Possible reasons include safe-distancing requirements or reserved for group bookings.
***
> vehicle
coaches
coach
decks
deck
seats
seat
**ticketClass** (Optional)

For seats that can be booked, the seat's ticket class.
***
> vehicle
coaches
coach
decks
deck
seats
seat
**direction**

The direction in which the seat faces, relative to the supplied vehicle image.  Possible values are:

-	`Unspecified`
-	`Left`
-	`Right`
-	`Up`
-	`Down`
***
> vehicle
coaches
coach
decks
deck
seats
seat
**type**

The type of seat.  Possible values are:

-	`Other`
-	`Seat`
-	`Wheelchair`
-	`Bike`
-	`FoldingSeat`
-	`Shelf`
***
> vehicle
coaches
coach
decks
deck
**layoutItems** (Optional)

A container object for the properties of non-seats and values required to render those items on the deck.
***
> vehicle
coaches
coach
decks
deck
layoutItems
**layoutItem**

The properties, values, and attributes of each non-seat on the deck, including x and y position, dimensions, and type.
***
> vehicle
coaches
coach
decks
deck
layoutItems
layoutItem
**position**

A container object for the position and dimensions of a non-seat.
***
> vehicle
coaches
coach
decks
deck
layoutItems
layoutItem
position
**x**

The x position of the top-left corner of the non-seat.
***
> vehicle
coaches
coach
decks
deck
layoutItems
layoutItem
position
**y**

The y position of the top-left corner of the non-seat.
***
> vehicle
coaches
coach
decks
deck
layoutItems
layoutItem
position
**width** (Optional)

The non-seat's width in pixels.  If not supplied, the width defined in `defaultSeatSize` is used.
***
> vehicle
coaches
coach
decks
deck
layoutItems
layoutItem
position
**height** (Optional)

The seat's height in pixels.  If not supplied, the width defined in `defaultSeatSize` is used.
***
> vehicle
coaches
coach
decks
deck
layoutItems
layoutItem
**attributes** (Optional)

Non-seat attributes as provided by the Seat Map supplier.
***
> vehicle
coaches
coach
decks
deck
layoutItems
layoutItem
**label** (Optional)

A label for the non-seat item on the deck.
***
> vehicle
coaches
coach
decks
deck
layoutItems
layoutItem
**type**

The type of non-seat item.  Possible values are:
-	`Table`
-	`LuggageRack`
-	`LuggageRoom`
-	`Exit`
-	`Bathroom`
-	`Stairs`
-	`Wall`
***
> vehicle
**changesDirectionOnRoute** (Optional)

Whether the train changes direction on the route.  Possible values are:
-	`True` - Vehicle changes direction.  Forward-facing seats may become backward-facing.
-	`False` - Vehicle does not change direction.
***
> vehicle
**displayNames**
A container object for all the labels that apply to the objects returned in the response, translated to the language requested.
***
> vehicle
displayNames
**displayName**
The labels that apply to an object returned in the response translated to the language requested.
***
> vehicle
displayNames
displayName
**nameCode** (Optional)

A name code returned in the response.
***
> vehicle
displayNames
displayName
**localLabel** (Optional)

The local label returned in the response.
***


## Book tickets

The standard booking flow applies when booking tickets for journeys with a seat map.  When you submit a [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements){target="_blank"} message, SilverCore performs a re-shop using the Leg Solution data to ensure the tickets are still available and the pricing hasn't changed.

To request a specific seat and coach after a seat map selection, you can omit the `<seatPreferences>` element from your request.

For SNCF and Leg Solutions with multiple Travel Segments, it is permitted to pass in a coach and seat number for the segment for which seat map data is available and a seat preference for the segment for which seat map data is not available.

**Example** :
```xml
<fareCodes>
  <fareCode code="FA11-1-O-STD">
    <serviceClass>FIRST</serviceClass>
    <travelSegmentIDRef>LS_1_3_TS_0</travelSegmentIDRef>
    <cabinClass>1st Class</cabinClass>
    <rewardsEligible>YES</rewardsEligible>
    <fareClass>FLEXIBLE</fareClass>
    <fareDisplayName>BUSINESS PREMIERE</fareDisplayName>
    <openReturn>NO</openReturn>
    <overbooked>false</overbooked>
    <reservable>INCLUDED</reservable>
    <fareExpirationDateTime>2022-02-26T07:01:00Z</fareExpirationDateTime>
    <seats>  
 ❶ <seat>
      <coach>11</coach>
      <number>125</number>
    </seat>
  </seats>
  <amenities>
    <amenity type="BAR">Bar Car</amenity>
    <amenity type="BICYCLE_AVAILABLE">Bicycle Space (Additional Cost)</amenity>
    <amenity type="INTERNET">WiFi access (Free)</amenity>
    <amenity type="WHEELCHAIR">Wheelchair ramp or facilities</amenity>
  </amenities>
  <fareApplicabilities>
    <fareApplicability outbound="RESTRICTED" return="RESTRICTED" type="SCHEDULE"/>
  </fareApplicabilities>
</fareCode>
</fareCodes>
```

❶ The coach and seat for the seat returned as `FREE` by the Seat Map API.

A successful request places the tickets on hold and establishes a set period of time during which you must complete the payment step.

The resulting [createBookingRecordResponse](/v1/docs/creatingbookingrecordresponse-elements){target="_blank"} message shows the success/fail status for the booking, assigns a unique Booking Record locator Id, and indicates the hold date/time after which booking will be canceled if not paid and confirmed.

The response message can also optionally echo back a complete Booking Record containing passenger information, Travel Segments, Ticketable Fares, and full payment status and financials.

If a carrier cannot fulfill a booking for Swedish Rail, SilverCore will return an `SC00016` error. This is shown in the following example:

```xml
<ns4:ExceptionData>
  <ns4:ExceptionId>0</ns4:ExceptionId>
  <ns4:Severity>INFORMATION</ns4:Severity>
  <ns4:ErrorCode>32183</ns4:ErrorCode>
  <ns4:ErrorText>Place not vacant for requested distance.</ns4:ErrorText>
</ns4:ExceptionData>
```

If the seat/coach preference cannot be satisfied, SNCF's algorithm will automatically assign another seat.

## Exchange tickets

For SNCF, to get the unique values required by the Seatmap API when working with SilverCore's exchange flow, pass the leg solution from your exchangeSearchResponse into your retrieveExchangeSummaryRequest.

If seat map data is available, the resulting response will include the `<legSolutions`> element and the values required by the Seatmap API in the `<seatMapInformation>` element.

**Example**:
```XML
<ns2:legSolutions>
  <legSolution legSolutionID="LS_1_1">
    <travelSegments>
      <travelSegment travelSegmentID="LS_1_1_TS_0" type="TRAIN">
        <originTravelPoint type="STATION">FRLYS</originTravelPoint>
        <destinationTravelPoint type="STATION">FRXRF</destinationTravelPoint>
        <departureDateTime>2022-06-23T06:36:00</departureDateTime>
        <arrivalDateTime>2022-06-23T09:57:00</arrivalDateTime>
        <designator>6101</designator>
        <marketingCarrier>TGVINOUI</marketingCarrier>
        <operatingCarrier>TGVINOUI</operatingCarrier>
        <supplierEquipmentType>TGVINOUI-HSP</supplierEquipmentType>
        <duration>P0Y0M0DT3H21M0S</duration>
        <crossBorderInfo>false</crossBorderInfo>
        <equipmentType code="HSP">High-Speed</equipmentType>
        <carbonData unit="kgCO2">1.7</carbonData>
❶       <seatMapInformation externalProposal="5cbf3703-bbc6-49e2-9500-6b687c1e03ff-0" externalTravelSegment="_:8d93c8b9-f300-4bee-9d2b-5a043c5855b6" externalSupplier="es3"/>
        <scheduleConfirmed>true</scheduleConfirmed>
      </travelSegment>
    </travelSegments>
  </legSolution>
</ns2:legSolutions>
```

❶ The `<seatMapInformation>` element is returned with the values necessary to obtain seat map data.


## Display visual seat map

The following example demonstrates how the Seat Map API response data can be consumed to visually represent the train, coach, and seats for the selected journey.

Partners must comply with any carrier rules when generating a visual representation.  SilverRail can provide current SNCF specifications.  Accreditation by the carrier will be required before deploying to Production.

:::(Warning) (Partners displaying or wanting to display seat preferences in their front-end, as returned in the `<availableSeatPreferences>` element in SilverCore, should omit any instances of `SEAT_MAP`.)
:::

**Example** :

<p align="center">
  <img width="70%" height="70%" src="https://files.document360.io/c53b2e89-8de3-47ad-adf4-5a70ab761260/Images/Documentation/seat_map_cropped%281%29%282%29.png">
</p>

<!---
## FAQs

When everything remains equal, why do two validateBookingRecordInformationRequests return different a externalproposal and externaltravelsegment?  (SD-28187)

After getting the seat information, how do you select a seat or how do you assign the seat?

createBookingRecordRequest needs to be sent the same way as when a NEARBY seat/coach is wished.  In a coming release and to be more clear, this pattern will stay for the NEARBY option only and we&#39;ll offer a distinct way to pass coach/seat.  Basically, we&#39;ll remove

the seatPreferences node to precise a specific coach/seat.  Once this is available, it will be published within the release note.

\&lt;com:seats\&gt;

\&lt;com:seat\&gt;

\&lt;com:coach\&gt;05\&lt;/com:coach\&gt;

\&lt;com:number\&gt;21\&lt;/com:number\&gt;

\&lt;/com:seat\&gt;

\&lt;/com:seats\&gt;

\&lt;com:seatPreferences\&gt;

\&lt;com:seatPreference\&gt;NEARBY\&lt;/com:seatPreference\&gt;

\&lt;/com:seatPreferences\&gt;

Definition of isBookable and isAvailable?

Our interpretation of isbookable is that this is a seat they could book based on the parameters provided as part of the search solutions request.  This mean a seat passenger to be able to select a seat this one has to be Bookable and Available.

2. trains.carriages.decks.seats.id isn&#39;t a unique identifier? or is unique per train? Do we need to provide the path when selecting a seat (trained + carriageId + etc.)?

This we don&#39;t know, we&#39;re returning all the info from PAO so it must contain what is needed, but since we aren&#39;t implementing the actual seat booking side of things we haven&#39;t researched it in-depth, also if Egencia would be doing these seat bookings within the SilverCore flow (which is what we THINK they want) then they will need to pass through the values SilverCore require for the seat booking (which Romain Ventanas would be the guy to ask for that?)

3.  What if we have a specific fare for a seat?  Do we need it?  do we select the fare first then get the seat map?

I would think Silvercore have put these through as part of interacting with the search solutions flow, and thus if they are returned as bookable they are valid for those fares, so if they&#39;ve provided them to Silvercore to this point those seats are eligible for those certain fares (Romain Ventanas can you please confirm the flow?)

4. Is extraProperties a free key-value map?  If yes, what kind of information will we have?  We believe it won&#39;t be ideal for us to parse.  Also, for example :

{ &quot;key&quot;: &quot;SHELF&quot;, &quot;value&quot;: &quot;True&quot; }

will never have a false value, right?

As far as our testing has revealed yes.  We will only have keys for for values that are true, but we can&#39;t know ALL cases in which PAO behaves

5.  How do we select a seat?  What happens in the case of a race condition during the seat selection?

This is a silver core (or whoever they&#39;re booking seats through) question; We return the seat map and the attributes of all the seats, we don&#39;t actually book / reserve seats.

6.  What is the response when no seat map is available?

{&quot;errors&quot;:[&quot;Ineligible request exception. Message: [

{\&quot;code\&quot;:\&quot;13000\&quot;,\&quot;label\&quot;:\&quot;Coach diagram not found\&quot;,\&quot;detail\&quot;:\&quot;Le diagramme de la première voiture du train n&#39;a pas de type.\&quot;}

]&quot;]}

in the case where there isn&#39;t a layout for a still valid set of ids

{&quot;errors&quot;:[&quot;Ineligible request exception. Message: [

{\&quot;code\&quot;:\&quot;3021\&quot;,\&quot;label\&quot;:\&quot;Proposal not found\&quot;}

]&quot;]}

if the ids have expired

7.  In the case of Multi segments / multi passengers, will we do multiple calls to the GET endpoint?

This one is a good question, technically the PAO endpoint is capable of returning multiple trains, but our inputs are purely the ids, so if there are different ids for different segments of the journey, then yes.  We suppose they would have to make multiple calls with the different ids...

See SD-27336

----\&gt;
--->
