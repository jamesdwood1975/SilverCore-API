:::(Error) (**In this article**, we provide details of the migration of Thalys tickets from Resarail to S3 (Sqills).)
:::

## Intro

Thalys tickets, formerly hosted in ResaRail inventory (RR), are being moved to Eurostar S3 (Squills) inventory.​  Delta is the PAO project for the ticket migration.

## Timing and transition period

Refer to the following diagram that outlines the stages and anticipated timings for the migration:

![image2023-6-20_15-0-57.png](https://files.document360.io/c53b2e89-8de3-47ad-adf4-5a70ab761260/Images/Documentation/image2023-6-20_15-0-57.png){height="" width=""}

Refer to the following points:

- Thalys tickets are available in S3 from 1st June, 2023, **for travel after 1st September, 2023*. ​
- During the transition period, between 1st June and 31st August, 2023, Thalys tickets will be available on both RR and S3 inventories, depending on the date of travel

The following examples show ticket availability based on booking and travel dates:​

- Any ticket booked up to and including 31st May, regardless of the travel date, will be an RR ticket by default.​
- A ticket booked on 21st June for travel on 10th July, will be an RR ticket by default.​
- A ticket booked on 21st June for travel on 10th September, will be an S3 ticket by default.​
- Any ticket booked for travel after 1st September, regardless of the booking date, will be an S3 ticket by default.​

## Main differences between RR- and S3-Thalys

| | Available in RR | Available in S3|
|:--|:--|:--|
| Seat maps |Yes| No|
| RAD |Yes|No|
| Exchange to cheaper fare | Yes| No|
| ETK|Yes |No |

## Technical solution​ in SilverCore

### Shop, Book, and Pay

The Thalys Delta migration does not require any further development from SilverCore Partners, and no endpoint or context changes are required.​

For S3, the only TDO available is PAH (the same used for Eurostar bookings).  ETK will no longer be an option​.

### Exchange

AVMI is the PAO feature that automates the exchange from one inventory (RR) to another (S3).  Please refer to [Exchanges between Inventories​ with the AVMI feature](/v1/docs/thalys-delta#exchanges-between-inventories​-with-avmi) below for further details.

Until the release of AVMI, when exchanging an RR Thalys ticket (original travel date up until 31st August) for an S3 Thalys ticket (new travel date after 1st September), Partners must perform a manual cancel and rebook. ​

There will be no changes to the following scenarios:​

- Exchanging an RR Thalys ticket (original travel date up until 31st August) for another RR Thalys ticket (new travel date up until 31st August) - Follow existing exchange rules and procedures.​
- Exchanging an S3 Thalys ticket (original travel date from 1st September on) for another S3 Thalys ticket (new travel date after 1st September) - Follow existing exchange rules and procedures.

### Exchanges between inventories​ with AVMI

SilverRail is working to make AVMI available for SilverCore Partners as soon as possible.

In the meantime, to exchange an RR Thalys ticket for an S3 Thalys ticket, SilverCore Partners will need to manually cancel the original RR ticket and book a new S3 ticket.  Partners may choose how to position this with customers (i.e., brand it as an exchange or explain that it has been canceled and rebooked).​

Please note that in this specific scenario, due to a technical limitation in PAO, a customer will be charged a cancelation fee, which may differ from the original exchange fee.  In this case, the SilverCore API will return the following supply-channel warning:​

```XML
<com:statusMessages>
  <com:statusMessage>
    <com:message>Estimated booking fees may be different from real booking fees</com:message>
    <com:code>WRN00010</com:code>
  </com:statusMessage>
</com:statusMessages>
```

Further technical detail on the AVMI feature will be shared once the solution has been finalized.

## Testing ​

### Shopping and booking​

All Thalys connections and fares can now be tested in Production and Cert environments.  The key data to consider to ensure things are working as expected is the date of travel: ​

- RR tickets for travel up to 31st August – **Expected result: regular Shopping and Booking flows.** ​
- S3 tickets for travel after 1st September – **Expected result: regular Shopping and Booking flows.** ​

### Exchange

Refer to the following points:​

- Exchanging a Thalys ticket (original travel date up to 31st August) for another Thalys ticket (new travel date up until 31st August) –  **Expected result = regular exchange**. ​
- Exchanging a Thalys ticket (original travel date from 1st September on) for a Thalys ticket (new travel date after 1st September) – **Expected result = regular exchange**.
- Exchanging a Thalys ticket (original travel date up to 31st August) for a Thalys ticket (new travel date after 1st September) - **Expected result = exchange should not work.  Expect an error message.  Perform a manual cancelation and rebook, pending AVMI**​.
