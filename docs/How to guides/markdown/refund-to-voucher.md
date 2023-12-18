:::(Error) (**In this article**, as part of our series of How-Tos, we'll look at how to issue refunds  as a voucher.)
:::

## Intro
In addition to refunding canceled orders to the original form of payment (credit card, debit card, etc.), SilverCore allows you to issue refunds in the form of a voucher that can be used to pay for future travel.  In some cases, you can split the refund between a voucher and the original form of payment.

### Supported carriers

Voucher refunds are available for Amtrak and Swedish Rail (rebookable and refundable fares only).

For Swedish Rail, the Partner must ensure that paying for future travel with a voucher is allowed only for these two fare types.  Payment with a voucher will fail for all other fare types.  When a passenger utilizes a voucher to pay for their new booking, and there is money left on it, Swedish Rail will issue a further voucher via email for the difference.

:::(Warning) (If you omit the voucher information when canceling a refundable fare, the refund logic defaults to the original payment format.)
:::

:::(Info) (Cancelation penalties may apply when refunding to the original form of payment.)
:::

## SilverCore flow

To help you implement refunds to vouchers, SilverCore returns the following data during the booking lifecycle:

- In the [pointToPointShoppingResponse](/v1/docs/pointtopointshoppingresponse-elements#code-item-rule){target="_blank"}, SilverCore returns the `refundToVoucher` attribute in the `<rules>` element for each Ticketable Fare.  If a fare can be refunded to a voucher, SilverCore will return `refundToVoucher="OPTIONAL"`,  `refundToVoucher="MANDATORY"`, or `refundToVoucher="SPLIT"`.
- Once a booking has been paid and confirmed, the `refundTarget` element in the `<cancellationSummary>` container element shows which refund methods are available for the order.
- In the [cancelBookingRecordRequest](/v1/docs/cancelbookingrecordrequest-elements){target="_blank"}, how you set the `<selectedCancellationOption>` element determines which cancelation option will be used.
- After cancelation, the `formOfPayment` element in the `<payment>` container element shows the method used to provide the refund.

Refunds to a voucher are sent to the first passenger/email contact on the booking because there is currently no way to select the intended recipient of the voucher.  Therefore, make sure that the main/lead passenger is listed first in the [createBookingRecordRequest](/v1/docs/createbookingrecordrequest-elements){target="_blank"} `<passengers>` element.

When developing and testing this functionality, note that all Amtrak orders are fully refundable to the original form of payment for 24 hours after a booking has been made.  You'll need to wait at least 24 hours to test the refund to voucher functionality.

## Vouchers in the pointToPointShoppingResponse

In the [pointToPointShoppingResponse](/v1/docs/pointtopointshoppingresponse-elements){target="_blank"} message, the `<rule>` elements for a Ticketable Fare contain a `<refundToVoucher>` attribute to indicate whether or not voucher refunds are available at the stage in the booking process covered by the rule.

**Example**:

```xml
<rule type="REFUND_ALLOWED" priceType="TICKET" applicableOrderStatus="CONFIRMED"
 expirationDateTime="2015-09-06T21:20:00Z" refundToVoucher="OPTIONAL"/>
```

There are four possible `<refundToVoucher>` values:

- `NOT_POSSIBLE` - The fare is only refundable to the original form of payment.
- `OPTIONAL` - The fare is optionally refundable to a voucher.
- `MANDATORY` - The fare can only be refunded to a voucher.
- `SPLIT` - The refund will be split between a voucher and the original form of payment.  For example, the ticket is refunded on the original form of payment, but an associated sleeper accommodation might have to go on the voucher.

:::(Warning) (`<refundToVoucher>` will return `NOT_POSSIBLE` for all Supply Channels other than Amtrak and Sweden and for any Amtrak/SJ fare that is not eligible to be refunded to a voucher.)
:::

## Vouchers in the booking record cancellationSummary element
Once a booking has been paid and confirmed, the `<cancellationOptions>` element in the Booking Record `<cancellationSummary>` element shows which refund methods are available.  The following `<cancellationOptions>` elements are relevant to vouchers:

| Element | Description |
| :-- | :-- |
| `<refundTarget>` | The type attribute indicates where the refund will go:<br>`FORM_OF_PAYMENT` - To the original form of payment.<br>`VOUCHER` - To a voucher.<br>`SPLIT_FORM_OF_PAYMENT_VOUCHER` - Split between the original form of payment and a voucher. |
| `penalty` | Contains the penalty amount to be assessed on cancellation and a breakdown of each component of the penalty.  If there is no penalty, SilverCore returns a zero amount.  Otherwise, you'll need to pass in this total via the `<expectedCancellationFee>` element when you submit the [cancelBookingRecordRequest](/v1/docs/createbookingrecordrequest){target="_blank"}. |
| `voucherAmount` | The amount that will go on the voucher in the point of sale currency.  The element is only returned when `<refundTarget>` is either `VOUCHER` or `SPLIT_FORM_OF_PAYMENT_VOUCHER`. |
| `voucherValue` | The amount for which the voucher will be issued in the Supply Channel currency.  The element is only returned when `<refundTarget>` is either `VOUCHER` or `SPLIT_FORM_OF_PAYMENT_VOUCHER`. |

**Example 1 - Refund to the original form of payment only**:
```XML
<cancellationSummary isCancellable="true">
 <cancellationOptions>
  <cancellationOption>
❶ <refundTarget type="FORM_OF_PAYMENT" />
   <penalty>
❷  <total currency="USD">0.00</total>
   </penalty>
  </cancellationOption>
 </cancellationOptions>
 <priceReversals>
  <total currency="USD">442.00</total>
  <breakdown>
   <component type="Product_Sale">
    <amount currency="USD">442.00</amount>
   </component>
  </breakdown>
 </priceReversals>
</cancellationSummary>
```
❶ The refund will go to the original form of payment.
❷ No cancellation penalty applies.

**Example 2 - Refund to a voucher only**:
```xml
<cancellationSummary isCancellable="true">
 <cancellationOptions>
  <cancellationOption>
❶ <refundTarget type="VOUCHER">
    <voucherAmount currency="USD">585.00</voucherAmount>
    <voucherValue currency="USD">585.00</voucherValue>
   </refundTarget>
   <penalty>
❷  <total currency="USD">0.00</total>
   </penalty>
  </cancellationOption>
 </cancellationOptions>
 <priceReversals>
  <total currency="USD">585.00</total>
  <breakdown>
   <component type="Ticket_Delivery_Fee_Assessment">
    <amount currency="USD">0.00</amount>
   </component>
   <component type="Product_Sale">
    <amount currency="USD">585.00</amount>
   </component>
  </breakdown>
 </priceReversals>
</cancellationSummary>
```

❶ The refund will go to a voucher.
❷ No cancellation penalty applies.

**Example 3 - Refund to the original form of payment OR a voucher**:
```xml
<cancellationSummary isCancellable="true">
  <cancellationOptions>
❶ <cancellationOption>
    <refundTarget type="FORM_OF_PAYMENT" />
    <penalty>
❷   <total currency="USD">44.20</total>
     <breakdown>
      <component currency="USD" type="TICKET">44.20</component>
     </breakdown>
    </penalty>
   </cancellationOption>
❸ <cancellationOption>
    <refundTarget type="VOUCHER">
     <voucherAmount currency="USD">442.00</voucherAmount>
     <voucherValue currency="USD">442.00</voucherValue>
    </refundTarget>
    <penalty>
❹   <total currency="USD">0.00</total>
    </penalty>
   </cancellationOption>
  </cancellationOptions>
  <priceReversals>
   <total currency="USD">442.00</total>
   <breakdown>
    <component type="Product_Sale">
     <amount currency="USD">442.00</amount>
    </component>
    <component type="Ticket_Delivery_Fee_Assessment">
     <amount currency="USD">0.00</amount>
    </component>
   </breakdown>
  </priceReversals>
 </cancellationSummary>
```

❶ Option 1 is to refund to the original form of payment.
❷ The original form of payment refund has a 10% penalty.
❸ Option 2 is to refund to a voucher.
❹ The voucher refund does not have a penalty.

**Example 4 - Refund split between the original form of payment and a voucher OR all on a voucher**:
```xml
<cancellationSummary isCancellable="true">
  <cancellationOptions>
❶ <cancellationOption>
    <refundTarget type="VOUCHER">
     <voucherAmount currency="USD">1105.00</voucherAmount>
     <voucherValue currency="USD">1105.00</voucherValue>
    </refundTarget>
    <penalty>
❷   <total currency="USD">0.00</total>
    </penalty>
   </cancellationOption>
❸ <cancellationOption>
    <refundTarget type="SPLIT_FORM_OF_PAYMENT_VOUCHER">
❹   <voucherAmount currency="USD">356.00</voucherAmount>
     <voucherValue currency="USD">356.00</voucherValue>
    </refundTarget>
    <penalty>
❺   <total currency="USD">74.90</total>
     <breakdown>
      <component currency="USD" type="TICKET">74.90</component>
     </breakdown>
    </penalty>
   </cancellationOption>
  </cancellationOptions>
  <priceReversals>
   <total currency="USD">1105.00</total>
   <breakdown>
    <component type="Product_Sale">
     <amount currency="USD">1105.00</amount>
    </component>
    <component type="Ticket_Delivery_Fee_Assessment">
     <amount currency="USD">0.00</amount>
    </component>
   </breakdown>
  </priceReversals>
 </cancellationSummary>
```

❶ Option 1 is to refund everything to a voucher.
❷ The voucher option does not have a penalty.
❸ Option 2 is to split the refund between a voucher and the original form of payment.
❹ The split option produces a voucher worth USD 356.00, with the remaining USD 749.00 going to the original form of payment.
❺ There's a 10% penalty on the original form of payment portion of the refund.

**Example 5 - Cancellation is not allowed, therefore no refund is possible**:
```xml
<cancellationSummary isCancellable="false"/>
```
## Vouchers in the cancelBookingRecordRequest

When you submit the [cancelBookingRecordRequest](/v1/docs/cancelbookingrecordrequest){target="_blank"} to cancel the order, include the optional `<selectedCancellationOption>` element to indicate if you want the refund via a voucher or to the original form of payment.

**Example**:
```xml
<book:selectedCancellationOption>
 <com:refundTarget type="VOUCHER">
  <com:voucherAmount currency="USD">963.00</com:voucherAmount>
  <com:voucherValue currency="USD">963.00</com:voucherValue>
 </com:refundTarget>
</book:selectedCancellationOption>
```

To create the `<selectedCancellationOption>` element, simply copy the values from the Booking Record `<cancellationOption>` element, as detailed above.

If you omit the `<selectedCancellationOption>` element, SilverCore defaults to a refund via the original form of payment.

## Vouchers in the booking record "payment" element

After you cancel the order, SilverCore adds a `<payment>` element of type `REFUND` to the Booking Record with the `<formOfPayment>` type attribute set accordingly.

**Example**:
```xml
<payment type="REFUND">
 <formOfPayment type="VO"/>
 <voucher>
  <number>0920656000063</number>
  <recipientName>SMITH/JANE</recipientName>
  <expirationDate>2014-04-01</expirationDate>
  <value currency="USD">1242.00</value>
 </voucher>
 <amount currency="USD">-1242.00</amount>
 <postingDate>2013-04-02Z</postingDate>
 <receiptNumber>IGNORE</receiptNumber>
 <referenceReceiptNumber>IGNORE</referenceReceiptNumber>
</payment>
```

The `<voucher>` element contains the following child elements:

| Element | Description |
| :-- | :-- |
| `<number>` | Supplier's voucher number.|
| `<recipientName>` | The name of the person the voucher is issued to (Amtrak issues to the first passenger on the booking). |
| `<expirationDate>` | The expiration date for the voucher. |
| `<value>` | Value of the voucher. |

:::(Info) (Amtrak will email the voucher information to the first passenger email listed in the booking.)
:::
