:::(Error) (**In this article**, as part of our series of How-Tos, we'll look at how you can work with SNCF'sSNCF's Remise A Disposition (RAD) process in SilverCore. )
:::

## Intro

Remise À Disposition (RAD) is a mechanism designed to ensure unused seats are returned to SNCF inventory for re-sale as quickly as possible and without waiting for the aftersales process of authorizing and issuing a refund to be completed.  

To perform a RAD, you will need to:
- Release the coupon(s) associated with the un-used seat(s) back to inventory:
  - Determine if a coupon can be released.
  - Release the coupon.
- Perform the aftersales operations needed to refund the customer:
  - Returning coupons.
  - Authorize product refund on returned coupons.
  - Issue refund.

SilverCore supports the RAD process for both full or partial cancellations, referred to as Full RAD and Partial RAD, accordingly.

:::(Warning) (RAD is not compatible with TER standalone orders, which means you can't perform a RAD on an order with a TER-only journey.  You can, however, perform a RAD on an order with both TGV and TER journeys.)
:::

## Definitions

**Coupon** - When you confirm a booking with SNCF, SilverCore creates coupons as representations of each ticket on the order.  The `couponNumber` will contain the ticket number provided by SNCF.  The `ticketableFareIdRef` provides a cross-reference to the Ticketable Fare on the order.

**Example coupons container**:

```xml
<coupons>
  <coupon type="TICKETED_RES" isReleasable="true">
    <couponNumber>119496635</couponNumber>
    <origin type="STATION">FRLYS</origin>
    <destination type="STATION">FRXRF</destination>
    <ticketableFareIDRef>TF-TUA000002-XFW000001-0</ticketableFareIDRef>
  </coupon>
  <coupon type="TICKETED_RES" isReleasable="true">
    <couponNumber>119496646</couponNumber>
    <origin type="STATION">FRXRF</origin>
    <destination type="STATION">FRLYS</destination>
    <ticketableFareIDRef>TF-TUA000002-XFW000001-1</ticketableFareIDRef>
  </coupon>
</coupons>
```


**Order cloning** – When you perform a partial cancellation with SNCF, SilverCore clones the original order into a new order with the active journey details.  The cloned order will take over any coupons that have not been canceled.  After the cloning process, no further cancellation actions can be performed on the original order.

## Release coupons

Coupons can be released directly with SNCF (via an SNCF counter or call center) or the original point of sale (i.e., from a Partner application to SilverCore or SilverAgent).  

### Release coupons with SNCF
Coupons can be released directly with SNCF.  This is referred to as an offline release.

If the release was performed offline, the retrieveBookingRecordResponse will return `releasedAt="offline"` and `returnStartDate`; this date indicates when the aftersales process of returning the coupons, authorizing a refund, and issuing the refund can begin.

### Release coupons through SilverCore or SilverAgent

Coupons can be released through a Partner'sPartner's application integrated with SilverCore or SilverAgent.  This is referred to as an online release.

If the release was performed online, the retrieveBookingRecordResponse will return `releasedAt="online"` and `returnStartDate`; this date indicates when the aftersales process of returning the coupons, authorizing a refund, and issuing the refund can begin.

## The release process in SilverCore

The following diagram provides an outline of the process of releasing coupons in SilverCore:

To release a coupon, Partners are required to:

- Determine if a coupon can be released by performing a retrieveBookingRecordRequest.
- Release the coupon by performing a cancelBookingRecordRequest.

### Determine if a coupon can be released

To determine if a coupon can be released, perform a retrieveBookingRecordRequest.

**Example request to retrieve a booking**:

```xml
<book:retrieveBookingRecordRequest xmlns:book="http://railgds.net/ws/booking" xmlns:com="http://railgds.net/ws/commontypes" xmlns:shop="http://railgds.net/ws/shopping" version="2.0" dateTime="2022-02-11T04:15:14.379">
  <com:context>
    <com:distributorCode>DEMO</com:distributorCode>
    <com:pointOfSaleCode>FR</com:pointOfSaleCode>
    <com:channelCode>CON</com:channelCode>
  </com:context>
  <book:recordLocator>B-QAMC-TUA000002</book:recordLocator>
  <book:responseSpec>
    <book:includeOrderDetails>true</book:includeOrderDetails>
    <book:includeOrderCosts>true</book:includeOrderCosts>
    <book:includePassengerDetails>true</book:includePassengerDetails>
    <book:includeFinancials>true</book:includeFinancials>
    <book:includePaymentRequirements>true</book:includePaymentRequirements>
    <book:includeTicketingOptions>true</book:includeTicketingOptions>
    <book:includeNotes>true</book:includeNotes>
    <book:includeFulfillmentInformation>true</book:includeFulfillmentInformation>
  </book:responseSpec>
</book:retrieveBookingRecordRequest>
```

**Example response showing releasable coupon**:

```xml
<coupons>
❶ <coupon type="TICKETED_RES" isReleasable="true">
    <couponNumber>099127501</couponNumber>
    <origin type="STATION">FRSTH</origin>
    <destination type="STATION">FRTJA</destination>
    <ticketableFareIDRef>TF-YTZ0006T2-VEE000001-0</ticketableFareIDRef>
  </coupon>
</coupons>
```


❶ If the coupon can be released, `isReleasable` will be true.  If a coupon can't be released, `isReleasable` will be false or omitted.  


### Release the coupon - Full RAD cancellation

To release all the coupons on the order, perform a cancelBookingRecordRequest and pass in:

- `recordLocator` from the booking record.
- `orderLocator` from the booking record.
- `<releaseSeat>true</releaseSeat>`.


**Example request to release all coupons (Full RAD)**:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:book="http://railgds.net/ws/booking" xmlns:com="http://railgds.net/ws/commontypes">
  <soapenv:Header/>
  <soapenv:Body>
    <book:cancelBookingRecordRequest>
      <com:context>
        <com:distributorCode>DEMO</com:distributorCode>
        <com:pointOfSaleCode>FR</com:pointOfSaleCode>
        <com:channelCode>CON</com:channelCode>
        </com:context>
❶      <book:recordLocator>B-DEMO-AFN0000KH</book:recordLocator>
❷      <book:orderLocator>O-DEMO-AFN0000KH-XEG000001</book:orderLocator>
        <book:expectedCancellationFee   currency="EUR">0.00</book:expectedCancellationFee>
        <book:responseSpec>
          <book:returnReservationDetails>true</book:returnReservationDetails>
          </book:responseSpec>
❸       <book:releaseSeat>true</book:releaseSeat>
        </book:cancelBookingRecordRequest>
      </soapenv:Body>
    </soapenv:Envelope>
```


❶ The SilverCore record locator Id assigned to the booking, as returned in the createBookingRecordResponse.
❷ Identifies which order should be updated if there's more than one in the booking.  You can omit this element for single-order bookings.
❸ `<book:releaseSeat>true</book:releaseSeat>` is required to release the coupons on the order.

**Example response showing the release done in SilverCore (truncated)**:

```xml
<orders>
❶ <order orderID="O-QAMC-ZYR000007-MKT000001" status="CONFIRMED">             ...              
<fulfillmentInformation>               
  ...               
  <valueDocument status="READY">                 
    ...                 
    <coupons>
❷    <coupon type="TICKETED_RES" isReleasable="false" releasedAt="online" returnStartDate ="2023-03-28T06:39:00Z">
      <couponNumber>29111975</couponNumber>
       ...
     </coupon>
     <coupon type="TICKETED_RES" isReleasable="false" releasedAt="online" returnStartDate ="2023-03-28T06:39:00Z">
      <couponNumber>04122009</couponNumber>
       ...
     </coupon>
     <coupon type="TICKETED_RES" isReleasable="false" releasedAt="online" returnStartDate ="2023-03-28T06:39:00Z">
      <couponNumber>27042008</couponNumber>
       ...
     </coupon>
    </coupons>
    <selectedTicketLanguage>fr</selectedTicketLanguage>
  </valueDocument>
  </fulfillmentInformation>            
  ...
❸  <cancellationSummary isCancellable="false" isPartiallyCancellable="false">
❹  <internalCancellationReason>RELEASE_SEAT<internalCancellationReason>            
   ...
❺  <isInventoryCanceled>true</isInventoryCanceled>             
   ...           
 </order>
</orders>
```

❶ The canceled order.  Note the order status remains `CONFIRMED`.
❷ Since the entire order has been canceled, SilverCore will indicate that no further releases can be performed against each of the coupons, `isReleasable=" false"`.   SilverCore also indicates that the release was done through SilverCore, `releasedAt="online"`.
❸ The `cancellationSummary` confirms that no further cancellations (full or partial) can be performed on the order.
❹ `internalCancellationReason` reports the reason for the cancellation.   `RELEASE_SEAT` is returned when a cancelBookingRecordRequest is sent with `<book:releaseSeat>true</book:releaseSeat>`.
❺ `isInventoryCanceled` is true because the inventory associated with the order has been canceled.

#### Order flow following Full RAD

After a successful cancellation with `<releaseSeat>true</releaseSeat>`, a `CONFIRMED` order will remain in `CONFIRMED` status.  The `valueDocument\@status` will remain `READY`.  

A `TICKETED` order will remain in `TICKETED` status.  The `valueDocument\@status` will remain `ISSUED`.

### Release the seat - Partial RAD cancellation

A Partial RAD allows you to release one or more coupons on the order without releasing all the coupons on the order.
To perform a Partial RAD cancellation, pass in the following:
- `recordLocator` from the booking record.
- `orderLocator` from the booking record.
- `<releaseSeat>true</releaseSeat>`.
- `<ticketableFareLocator>` to indicate the Ticketable Fare and its associated coupon you want to release.

**Example request to release some coupons**:

```XML
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:book="http://railgds.net/ws/booking" xmlns:com="http://railgds.net/ws/commontypes">
  <soapenv:Header/>
  <soapenv:Body>
    <book:cancelBookingRecordRequest>
      <com:context>
        <com:distributorCode>DEMO</com:distributorCode>
        <com:pointOfSaleCode>FR</com:pointOfSaleCode>
        <com:channelCode>CON</com:channelCode>
        </com:context>
❶    <book:recordLocator>B-DEMO-AFN0000KH</book:recordLocator>
❷    <book:orderLocator>O-DEMO-AFN0000KH-XEG000001</book:orderLocator>
    <book:ticketableFareLocators>
❸      <book:ticketableFareLocator>TF-AFN0000KH-XEG000001-1</book:ticketableFareLocator>
      </book:ticketableFareLocators>
      <book:expectedCancellationFee currency="EUR">0.00</book:expectedCancellationFee>
      <book:responseSpec>
        <book:returnReservationDetails>true</book:returnReservationDetails>
        </book:responseSpec>
❹    <book:releaseSeat>true</book:releaseSeat>
      </book:cancelBookingRecordRequest>
    </soap:Body>
  </soap:Envelope>
```


❶ The SilverCore record locator Id assigned to the booking, as returned in the createBookingRecordResponse message.
❷ Identifies which order should be updated if there's more than one in the booking.  You can omit this element for single-order bookings.
❸ Identifies the Ticketable Fare and its associated coupons you want to release.
❹ `<book:releaseSeat>true</book:releaseSeat>` is required to release the coupons on the order.

**Example response showing the released coupon (truncated)**:

```XML
<orders>
// cloned order with active journey details
❶  <order orderID="O-QAMC-TUA000002-UJG000001" status="CONFIRMED" originalOrderID="O-QAMC-TUA000002-XFW000001">  
      ...
      <fulfillmentInformation>
        ...    
          <valueDocument status="READY">      
            ...      
            <coupons>
              <coupon type="TICKETED_RES" isReleasable="true">
                <couponNumber>119496635</couponNumber>          
                ...        
              </coupon>
            </coupons>      
            ...    
          </valueDocument>
        </fulfillmentInformation>  
        ...  
        <cancellationSummary isCancellable="true" isPartiallyCancellable="true">    
          ...
        </cancellationSummary>
          ...
    </order>
    // original order
❷   <order orderID="O-QAMC-TUA000002-XFW000001" status="TICKETED">  
      ...       
      <fulfillmentInformation>
        ...    
          <valueDocument status="ISSUED">      
          ...          
          <coupons>
❸           <coupon type="TICKETED_RES" isReleasable="true">
              <couponNumber>119496635</couponNumber>          
              ...        
            </coupon>
❹           <coupon type="TICKETED_RES" isReleasable="false" releasedAt="online">
              <couponNumber>119496646</couponNumber>          
              ...        
            </coupon>
          </coupons>      
          ...    
        </valueDocument>
      </fulfillmentInformation>
❺     <cancellationSummary isCancellable="false" isPartiallyCancellable="false" />
❻     <internalCancellationReason>RELEASE_SEAT</internalCancellationReason>
        ...
❼     <isInventoryCanceled>true</isInventoryCanceled>
  </order>
</orders>
```


❶ The cloned order with active journey details.  Note the reference to the original order in `originalOrderID` from which the cloned order was created.  Since the cloned order contains only the un-released coupons from the original order, SilverCore indicates that the coupon is releasable `isReleasable=" true"` and can be canceled, `isCancellable="false"`.
❷ The original order with both of the original coupons.
❸ The un-released coupon in the original order.  This is the coupon cloned into the new order.
❹ The released coupon in the original order released in SilverCore.  No further release actions can be made against this Ticketable Fare.
❺ Since the order has already been partially canceled and cloned into a new order, the existing order can no longer be canceled.
❻ `internalCancellationReason` reports the reason for the cancellation.   `RELEASE_SEAT` is returned when a cancelBookingRecordRequest is sent with `<book:releaseSeat>true</book:releaseSeat>`.
❼ `isInventoryCanceled` is `true` because the inventory associated with the order has been canceled.

#### Order flow following Partial RAD

In contrast to a Full RAD cancellation, every Partial RAD will result in an order cloning process as described below:
-       The original order is retained along with its status, e.g., `CONFIRMED` or `TICKETED`.  The value document status will remain `READY` (for `CONFIRMED`) or `ISSUED` (for `TICKETED`).
-       The Ticketable Fares not partially canceled in the original order are cloned into a new order.  This new order is the active trip for the customer.

## Perform the aftersales process within SilverCore[JW15]

### Outline process

The following diagram provides an outline of the aftersales process performed in SilverCore:



### Details

Once the coupons have been released, the aftersales process can be executed to finalize the RAD.   The aftersales process **must** be performed through the original point of sale (i.e., from a Partner application to SilverCore or SilverAgent).  

To complete the aftersales process needed to refund the customer, Partners need to:
- Perform a returnValueDocumentRequest to return coupons; this will change the order status to `RETURNED`.
- Perform a returnValueDocumentRequest to authorize a product refund on the returned coupons; this will change the order status to `WITHDRAWN` and the booking status to `CREDIT`.
- Perform a [refundBookingRecordRequest](/v1/docs/refundbookingrecordrequest-elements){target="_blank"} to issue a refund; this will change the booking status to `BALANCED`.

### Return coupons

Before authorizing a product refund, the returnValueDocumentRequest must be used to return the released coupons:
- For a Full RAD, this means returning all coupons on the order and sending `returnType=FULL`  in your request.
- For a Partial RAD, this means returning only the coupons that have been released and sending `returnType=PARTIAL` in your request.

Once done, the corresponding coupon container in the returnValueDocumentResponse will contain a `couponReceivedDate` element.

#### Get a list of coupons

Before returning coupons, Partners can first get a list of coupons on a booking by performing a retrieveBookingRecordRequest and passing in the `recordLocator`.

**Example retrieveBookingRecordResponse showing the Ticketable Fares (coupons) on the booking**:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<coupons>
  <coupon type="TICKET">
    <couponNumber>054817464</couponNumber>
    <origin type="STATION">FRPMO</origin>
    <destination type="STATION">FRQJZ</destination>
    <ticketableFareIDRef>TF-YDV00037U-WMW000001-0</ticketableFareIDRef>
  </coupon>
  <coupon type="TICKET">
    <couponNumber>054817475</couponNumber>
    <origin type="STATION">FRQJZ</origin>
    <destination type="STATION">FRPMO</destination>
    <ticketableFareIDRef>TF-YDV00037U-WMW000001-2</ticketableFareIDRef>
  </coupon>
  <coupon type="TICKET">
    <couponNumber>054817464</couponNumber>
    <origin type="STATION">FRPMO</origin>
    <destination type="STATION">FRQJZ</destination>
    <ticketableFareIDRef>TF-YDV00037U-WMW000001-1</ticketableFareIDRef>
  </coupon>
</coupons>
```


#### Perform the return

The following elements in the returnValueDocumentRequest are the minimum information required to perform the return (full and partial cancellations):
- `recordLocator`.
- `orderLocator`.
- `valueDocumentLocator`.
- `returnType=FULL` for full cancellations
OR
`returnType=PARTIAL` for partial cancellations
- coupons:
  - `couponNumber`.
  - `couponReceivedDate`.[JW20] [BF21]

**Example request to return Ticketable Fares**:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<soap:Envelope xmlns:com= "http://railgds.net/ws/commontypes" xmlns:book="http://railgds.net/ws/booking" xmlns:shop="http://railgds.net/ws/shopping" xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Header/>
  <soap:Body>
    <book:returnValueDocumentRequest version="2.0">
      <com:context>
        <com:distributorCode>DEMO</com:distributorCode>
        <com:pointOfSaleCode>FR</com:pointOfSaleCode>
        <com:channelCode>CON</com:channelCode>
      </com:context>
      <book:recordLocator>B-DEMO-YDV00037U</book:recordLocator>
      <book:orderLocator>O-DEMO-YDV00037U-WMW000001</book:orderLocator>
      <book:valueDocumentLocator>CQB789</book:valueDocumentLocator>
      <book:receiveCoupons returnType="FULL">
        <book:coupons>
          <book:coupon>
            <com:couponNumber>054817464</com:couponNumber>
            <book:couponReceivedDate>2020-07-05</book:couponReceivedDate>
          </book:coupon>
          <book:coupon>
            <com:couponNumber>054817475</com:couponNumber>
            <book:couponReceivedDate>2020-07-05</book:couponReceivedDate>
          </book:coupon>
          <book:coupon>
            <com:couponNumber>054817464</com:couponNumber>
            <book:couponReceivedDate>2020-07-05</book:couponReceivedDate>
          </book:coupon>
        </book:coupons>
      </book:receiveCoupons>
      <book:responseSpec>
        <book:returnReservationDetails>true</book:returnReservationDetails>
      </book:responseSpec>
    </book:returnValueDocumentRequest>
  </soap:Body>
</soap:Envelope>
```


Following a successful request, the [returnValueDocumentResponse elements](/v1/docs/returnvaluedocumentresponse-elements){target= "_blank"} and all future [retrieveBookingRecordResponse elements](/v1/docs/retrievebookingrecordresponse-elements){target= "_blank"} will include the `couponReceivedDate` within the coupon element.

**Example returnValueDocumentResponse showing the couponReceivedDate**:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<coupons>
  <coupon type="TICKET">
    <couponNumber>054817464</couponNumber>
    <couponReceivedDate>2020-07-05</couponReceivedDate>
    <origin type="STATION">FRPMO</origin>
    <destination type="STATION">FRQJZ</destination>
    <ticketableFareIDRef>TF-YDV00037U-WMW000001-0</ticketableFareIDRef>
  </coupon>
  <coupon type="TICKET">
    <couponNumber>054817475</couponNumber>
    <couponReceivedDate>2020-07-05</couponReceivedDate>
    <origin type="STATION">FRQJZ</origin>
    <destination type="STATION">FRPMO</destination>
    <ticketableFareIDRef>TF-YDV00037U-WMW000001-2</ticketableFareIDRef>
  </coupon>
</coupons>
```	 

#### Order flow following return

After successfully returning the Ticketable Fares (coupons), a `CONFIRMED` order will become `RETURNED`.  If the order was `TICKETED`, it will become `RETURNED`.

The `valueDocument@status` will move from `READY`/`ISSUED` (depending on whether the order is `CONFIRMED`/`TICKETED`) to `RETURNED`.

### Authorize product refund

After receiving the original tickets or the RAD proof, the next step is to reverse the money the customer owes on the SilverCore order.

The `authorizeRefund` function located within the [returnValueDocumentRequest elements](/v1/docs/returnvaluedocumentrequest-elements){target= "_blank"} will reverse the product financials, decreasing the `revenueTotal`, and setting the booking to status `CREDIT` for the customer to receive a refund.

The following elements in the returnValueDocumentRequest are the minimum information required to authorize the refund:
- `couponNumber`.
- `refundApprovedDate`.
- `authorization/code` attribute (with a description).

#### Displaying the cancellation penalty to passengers

Before authorizing the refund, and if required, Partners can retrieve the cancellation penalty from the `cancellationSummary` located within the [retrieveBookingRecordResponse](/v1/docs/retrievebookingrecordresponse-elements){target= "_blank"} and then display it to passengers.

**Example retrieveBookingRecordResponse with information relevant for displaying the cancellation penalty**:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<cancellationSummary isCancellable="true" isPartiallyCancellable="true">
  <cancellationOptions>
    <cancellationOption>
      <refundTarget type="FORM_OF_PAYMENT"/>
      <penalty>
        <total currency="EUR">0.00</total>
      </penalty>
    </cancellationOption>
  </cancellationOptions>
  <priceReversals>
    <total currency="EUR">202.70</total>
    <breakdown>
      <component type="Product_Sale">
        <amount currency="EUR">93.00</amount>
      </component>
      <component type="Product_Sale">
        <amount currency="EUR">109.70</amount>
      </component>
    </breakdown>
  </priceReversals>
</cancellationSummary>
```

**Example returnValueDocumentRequest to authorize the refund**:

:::(Info) (The `cancellationPenalty` and `reverseCost` elements contained in the returnValueDocumentRequest schema should be excluded from the request when using the SNCF RAD Process.)
:::

```XML
<?xml version="1.0" encoding="UTF-8"?>
<soap:Envelope xmlns:com= "http://railgds.net/ws/commontypes" xmlns:book="http://railgds.net/ws/booking" xmlns:shop="http://railgds.net/ws/shopping" xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Header/>
  <soap:Body>
    <book:returnValueDocumentRequest>
      <com:context>
        <com:distributorCode>DEMO</com:distributorCode>
        <com:pointOfSaleCode>FR</com:pointOfSaleCode>
        <com:channelCode>CON</com:channelCode>
      </com:context>
      <book:recordLocator>B-DEMO-YDV00037U</book:recordLocator>
      <book:orderLocator>O-DEMO-YDV00037U-WMW000001</book:orderLocator>
      <book:valueDocumentLocator>CQB789</book:valueDocumentLocator>
      <book:authorizeRefund>
        <book:coupons>
          <book:coupon>
            <com:couponNumber>054817464</com:couponNumber>
            <book:refundApprovedDate>2020-07-07</book:refundApprovedDate>
            <book:authorization code="RA_836670">Return_Authorized for:    TICKET</book:authorization>
          </book:coupon>
          <book:coupon>
            <com:couponNumber>054817475</com:couponNumber>
            <book:refundApprovedDate>2020-07-07</book:refundApprovedDate>
            <book:authorization code="RA_836670">Return_Authorized for:    TICKET</book:authorization>
          </book:coupon>
        </book:coupons>
      </book:authorizeRefund>
      <book:responseSpec>
        <book:returnReservationDetails>true</book:returnReservationDetails>
      </book:responseSpec>
    </book:returnValueDocumentRequest>
  </soap:Body>
</soap:Envelope>
```


Following a successful request, the returnValueDocumentResponse and all future retrieveBookingRecordResponses will include the `refundApprovedDate` and authorization information.

**Example returnValueDocumentResponse showing the refundApprovedDate**:

```xml
<coupons>
  <coupon type="TICKET">
    <couponNumber>054817464</couponNumber>
    <couponReceivedDate>2020-07-05</couponReceivedDate>
    <origin type="STATION">FRPMO</origin>
    <destination type="STATION">FRQJZ</destination>
    <refundApprovedDate>2020-07-07</refundApprovedDate>
    <authorization code="RA_836670">Return_Authorized for: TICKET</authorization>
    <ticketableFareIDRef>TF-YDV00037U-WMW000001-0</ticketableFareIDRef>
  </coupon>
  <coupon type="TICKET">
    <couponNumber>054817475</couponNumber>
    <couponReceivedDate>2020-07-05</couponReceivedDate>
    <origin type="STATION">FRQJZ</origin>
    <destination type="STATION">FRPMO</destination>
    <refundApprovedDate>2020-07-07</refundApprovedDate>
    <authorization code="RA_836670">Return_Authorized for: TICKET</authorization>
    <ticketableFareIDRef>TF-YDV00037U-WMW000001-2</ticketableFareIDRef>
  </coupon>
</coupons>
```

#### Order flow following refund authorization

After authorizing the refund, the `RETURNED` order will become `WITHDRAWN`.

### Issue the refund

After a successful returnValueDocumentRequest authorizing the refund, the booking status will be in `CREDIT`.

**Example returnValueDocumentResponse showing booking status (truncated)**:

```xml
<ns2:bookingRecord recordLocator="B-DEMO-CZL0003BW" status="CREDIT">
...
<revenueTotal currency="EUR">0.00</revenueTotal>
<receiptsTotal currency="EUR">202.70</receiptsTotal>
```


A [refundBookingRecordRequest](/v1/docs/refundbookingrecordrequest-elements){target="_blank"} must be sent to refund the SilverCore financials.

**Example refundBookingRecordRequest to refund SilverCore financials**:

```xml
<soap:Envelope xmlns:com="http://railgds.net/ws/commontypes" xmlns:book="http://railgds.net/ws/booking" xmlns:shop="http://railgds.net/ws/shopping" xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Header/>
  <soap:Body>
    <book:refundBookingRecordRequest<com:context>
      <com:distributorCode>DEMO</com:distributorCode>
      <com:pointOfSaleCode>FR</com:pointOfSaleCode>
      <com:channelCode>CON</com:channelCode>
    </com:context>
    <book:recordLocator>B-DEMO-YDV00037U</book:recordLocator>
    <book:responseSpec>
      <book:returnReservationDetails>true</book:returnReservationDetails>
    </book:responseSpec>
    <book:receiptNumber>R-DEMO-SHB003982</book:receiptNumber>
    <book:refundAmount currency="EUR">202.70</book:refundAmount>
  </book:refundBookingRecordRequest>
</soap:Body>
```


:::(Warning) (If the Partner is the merchant of record and has collected the customer's payment, the Partner is responsible for refunding the customer.)
:::
