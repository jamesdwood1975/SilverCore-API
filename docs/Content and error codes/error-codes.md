:::(Error) (**In this article**, we'll look at the error codes you may receive when making requests through the SilverCore API.)
:::

## Booking service errors

| Error code | Description |
| :-- | :-- |
| BK00000	| Unable to process request. Internal configuration problem. |
| BK00001 |	Invalid booking context. distributor=[distributor], channel=[channel], agency=[agency], company=[company]. |
| BK00002	| No pending receipts associated with booking: [booking]. |
| BK00003	| Invalid booking locator: [locator]. |
| BK00004	| Invalid booking external reference number: [number], type: [type]. |
| BK00005	| Credit Card could not be processed. |
| BK00006	| Credit Card could not be processed. |
| BK00007	| Product record create failed. |
| BK00008	| Supply Channel processing error. |
| BK00009	| Booking record create failed. |
| BK00010	| Customer record create failed. |
| BK00011	| Duplicate record found: [record]. |
| BK00012	| ProductCost record create failed. |
| BK00013	| Invalid payment amount. Expected [amount] [currency] but the request contained [amount] [currency]. |
| BK00014	| Invalid Ticket Delivery Option: [option]. Valid options are: [options]. |
| BK00015	| No pending receipts associated with order: [order]. |
| BK00016	| Order record create failed. |
| BK00017	| Revenue record create failed. |
| BK00018	| ProductCostGroup record create failed. |
| BK00019	| CustomerOrderMapping record create failed. |
| BK00020	| CustomerProductMapping record create failed. |
| BK00021	| No orders found for supply channel locator. |
| BK00022	| No passengers found with that name. |
| BK00023	| No valid search criteria found. |
| BK00024	| No contact found with that email. |
| BK00025	| CustomerContact record create failed. |
| BK00026	| Multiple criteria not supported. |
| BK00027	| Passenger Type Code [code] is not applicable for passenger and Supply Channel [code]. |
| BK00028	| Order can not be cancelled. |
| BK00029	| Current order status does not allow cancellation. |
| BK00030	| Current order status does not allow order creation. |
| BK00031	| Current order status does not allow payment to be added. |
| BK00032	| Expected cancellation fee ([fee]) does not equal supply channel cancellation fee ([fee]). |
| BK00033	| Expected refund amount does not equal supply channel refund amount. |
| BK00034	| BookedTravelSegment create failed. |
| BK00035	| BookedFareQualifier create failed. |
| BK00036	| BookedCustomerFareQualifier create failed. |
| BK00037	| Booking has already been paid in full. |
| BK00038	| Order Status does not match supply channel status. |
| BK00039	| Leg Solution [solution] arrives ([time]) after departure of Leg Solution [solution] ([time]). |
| BK00040	| Current order status does not allow order to be confirmed. |
| BK00041	| Can not confirm booking. Booking has not been paid for in full. |  
| BK00042	| Order can not be cancelled. Refund Prohibited. |  
| BK00043	| Order can not be cancelled.  No fare rule found for order. |
| BK00044	| Order can not be refunded. |
| BK00045	| Service fees can not be added for this supply channel. |
| BK00046	| Service fees can not be added to booking with no associated products. |
| BK00047	| There are no orders associated with booking: [booking]. |
| BK00048	| Order Status does not have valid status to add ticketing option. |
| BK00049	| Ticketing option invalid. |
| BK00050	| Order already has a ticket delivery option associated with it. |
| BK00051	| Can not use refundBookingRecord for this supply channel. |
| BK00052	| Receipt [receipt] not associated with booking [booking]. |
| BK00053	| Refund amount is larger than funds available for refund. |
| BK00054	| Refund will put booking into a debit state. |
| BK00055	| Ticketing option fee is not in POS currency. |
| BK00056	| Can not add payment. Payment must be full amount. |
| BK00057	| Can not add payment. Ticketing option and amount must be added. |
| BK00058	| Invalid POS currency code [code], expected [code]. |
| BK00059	| Credit Card could not be processed. |
| BK00060	| Illegal passenger type code: [code]. |
| BK00061	| Invalid address country. |
| BK00062	| Ticket Delivery Option cost of [cost] does not match supply channel's Ticket Delivery Option cost of [cost]. |
| BK00063	| Request ticketing option fee does not match fee stored with order. |
| BK00064	| Ticketing Delivery Option requires a shipping address. |
| BK00065	| Shipping address country [country] is not valid for the Ticketing Delivery Option's region [region]. |
| BK00066	| Supplied Ticket Delivery Option price [price] does not match required price [price]. |
| BK00067	| Access denied for context [context], supply channel [channel]. |
| BK00068	| This Ticket Delivery Option requires a Destination. |
| BK00069	| Payer Authentication is not required. |
| BK00070	| Unable to authenticate payer - [payer]. |
| BK00071	| Payer Authentication is required. |
| BK00072	| Value Document Locator provided does not match with the one available on the booking. |
| BK00073	| Value Document on the booking is in incorrect status. |
| BK00074	| Invalid travel point [travel point]' provided for coupon number '[number]'. |
| BK00075	| Coupon [coupon] you are trying to authorize for refund has not been received on this order. |
| BK00076	| Current booking status does not allow the order to be confirmed. |
| BK00077	| Cannot confirm booking, no ticket delivery option selected. |
| BK00078	| Revenue reversal amount cannot be greater than total revenue on order. |
| BK00079	| Shipment Tracking information can not be added until a ticket delivery option is selected. |
| BK00080	| Shipment Tracking information can not be added to ticket delivery option [option]. |
| BK00081	| Booking does not have a selected ticketing option. |
| BK00082	| Invalid order status ([status]) for ticketing option removal. |
| BK00083	| Product Cost Item record create failed. |
| BK00084	| agentInformation must be specified to create booking notes. |
| BK00085	| Missing PointToPointPrice for leg solution [solution]. |
| BK00086	| Invalid leg solution/price combination(s): Leg solution [solution] refers to more than one price. |
| BK00087	| Price does not meet minimum required: [price]. |
| BK00088	| Invalid order and / or value document status for authorizing refund. |
| BK00089	| Invalid order status for claiming value document : expected status of [status] but is [status]. |
| BK00090	| Unable to claim value document - value document does not exist. |
| BK00091	| Invalid value document status for claiming value document : expected status of [status] but is [status]. |
| BK00092	| Invalid ticket delivery option for claiming value document. |
| BK00093	| Unable to find passenger [passenger] on booking [booking]. |
| BK00094	| Booking [booking] contains multiple orders. Please send a cancel request for each order individually. |
| BK00095	| Booking [booking] does not contain order [order]. |
| BK00096	| Booking [booking] contains multiple orders. As a result, ticketing options can not be specified when adding a payment. | Please use an UpdateBookingRecordRequest. |
| BK00097	| Booking [booking] contains multiple orders. As a result, the confirm flag can not be set to true. Please re-submit the request with it set to false and send a separate confirmBookingRecordRequest. |
| BK00098	| The booking contains multiple orders. As a result, the orderLocator element must be set in order to confirm. |
| BK00099	| Invalid ticket language selected. |
| BK00100	| Invalid refund amount. |
| BK00101	| No other operations allowed when adding an order to a booking. |
| BK00102	| Multiple orders are not allowed with supply channel [supply channel]. |
| BK00103	| Unable to locate cancellation penalty modifier. |
| BK00104	| Unable to apply configured cancellation penalty modifier. |
| BK00105	| Fare rules do not allow the order to be refunded. |
| BK00106	| Partial returns are not permitted for the processing supply channel. |
| BK00107	| Specification of revenue reversal amount and cancellation penalty is not allowed by this supply channel. |
| BK00108	| Specification of revenue reversal amount and cancellation penalty is required by this supply channel. |
| BK00109	| Illegal fare qualifier [fare qualifier]. |
| BK00110	| Cost access denied for context [context], supply channel [supply channel]. |
| BK00111	| Missing required information: [information]. |
| BK00112	| Unable to build booking data for supply channel payment processor. |
| BK00113	| Unable to parse supply channel payment processor response. |
| BK00114	| Missing [value]. |
| BK00115	| Invalid origin code. |
| BK00116	| Invalid destination code. |
| BK00117	| Must supply either departure date time or arrival date time. |
| BK00118	| [date/time] date-time for this request must be between [date/time] and [date/time]. |
| BK00119	| [date/time] date-time for this request must be between [date/time] and [date/time]. |
| BK00120	| Departure DateTime must be before Arrival DateTime. |
| BK00121	| confirmationInformation must match payment information. |
| BK00122	| Seat assignments not assigned [assignment]. |
| BK00123	| Loyalty cards can either be specified on a passenger OR as a passenger fare qualifier on a given ticketable fare. |
| BK00124	| Unable to find loyalty card program [program]. |
| BK00125	| Only one loyalty card per passenger per program is supported. |
| BK00126	| Can not confirm booking. |
| BK00127	| Debit card option requires sort code. |
| BK00128	| Loyalty card option requires expiration date. |
| BK00129	| Invalid country code for supply channel. |
| BK00130	| Travel documents for passenger, [passenger], expire before departure of first leg. |
| BK00131	| Request contains invalid number of optional reservations. |
| BK00132	| Can not select a non-zero priced Ticket Delivery Option while confirming. |
| BK00133	| Product cost reversal amount cannot be greater than total product costs on order. |
| BK00134	| Update failure: passengerID, [id], does not exist in specified booking record. |
| BK00135	| contactInformation entries cannot be added or removed, only modified. |
| BK00136	| [element] cannot be updated. |
| BK00137	| Product cost reversal amount currency must match supply channel currency. |
| BK00138	| Invalid station code: [code]. |
| BK00139	| Invalid payment card for split payment. |
| BK00140	| Payment Service Error. |
| BK00141	| Payment Service Error. |
| BK00142	| Payment Service Error. |
| BK00143	| Payment Service Error. |
| BK00144	| Payment Service Error. |
| BK00148	| Payment Service Error. |
| BK00145	| Invalid fulfillment (SMS) phone number: Calling country code [code] does not belong to ticket delivery region [region]. |
| BK00146	| Invalid service fees specified. Will result in negative revenues [amount]. Currency: [currency]. |
| BK00147	| Invalid service fees specified. Total revenue plus new service fees: [fee], total revenue validated: [amount]. Currency: [currency]. |
| BK00149	| Missing required confirmation information element: [element]. |
| BK00150	| Confirmation information loyalty card is disallowed: [prgram] (program name). |
| BK00151	| Confirmation information card type ID is disallowed: [id] (card ID). |
| BK00152	| Confirmation information sort code specified has non-numeric characters. |
| BK00153	| Confirmation information sort code length is different then specification: [length]. |
| BK00154	| Invalid order status for redelivering value document: expected status of [status] but is [status]. |
| BK00155	| Order is not eligible for value document redelivery. |
| BK00156	| An email address must be provided when the selected ticket delivery option is [option]. |
| BK00157	| A phone number must be provided when the selected ticket delivery option is [option]. |
| BK00158	| Invalid passenger information: [info]. |
| BK00159	| Payment Service Error. |
| BK00160	| Failed to generate new token. |
| BK00161	| Access denied for context: [context]. |
| BK00162	| Order locator [id] is not on booking [booking]. |
| BK00163	| Ticketable Fare locator [id] is not on order [id]. |
| BK00164	| The ticketable fares specified must cover all customers on the order. |
| BK00165	| The ticketable fares specified must map to the same travel segments. |
| BK00166	| All ticketable fares on an order can not be specified. |
| BK00167	| Partial Cancel is not allowed for order. |
| BK00168	| Cancellation Summary retrieval not allowed for order. |
| BK00169	| Can not delete PII until 30 days from departure have passed. |
| BK00245	| Invalid fulfillment (SMS) phone number: Calling country code [code] does not belong to ticket delivery region [region]. |
| BK00246	| Invalid or missing cancellation option. |
| BK00247	| Missing voucher value. |
| BK00248	| Missing voucher amount. |
| BK00249	| Invalid voucher [voucher], expected [voucher]. |
| BK00250	| Voucher [voucher] can not be supplied with this cancellation option. |
| BK00251	| Supply Channel [error] Error: Number of passengers cannot exceed [number]. |
| BK00252	| Failed to create Receipt. |
| BK00253	| The province "[province]" is not valid for country "[country]". |
| BK00254	| Passenger(s) "[passenger]" were not found on the update request but were in the original booking. |
| BK00255	| Last name is a required field and cannot be left blank or omitted. |
| BK00256	| Some or all Coupons are not ISSUED, or are not valid for this Value Document. |
| BK00257	| No printed (PTR) coupons exist for this Value Document. |
| BK00258	| Coupon has already been returned. |
| BK00259	| Value Document has ISSUED unreturned coupons. |
| BK00260	| Failed to delete payment token [token]. |
| BK00261	| Multi-supplier journey is missing TRANSFER segment at waypoint. |
| BK00262	| Expected leg solution to map to exactly one supply channel, got [number]. |
| BK00263	| Custom information not supported at create booking time. Passenger first name [firstname] last name [lastname]. |
| BK00264	| Supply channel does not support travel pass operations. |
| BK00265	| Invalid order status for adjusting cost and revenue. |
| BK00266	| Applying cost adjustments would cause the total of the product costs for the ticketable fare to be less than zero. |
| BK00267	| Cost adjustment currency must match supply channel currency. |
| BK00268	| Missing credit card surcharge fee. |
| BK00269	| Invalid credit card surcharge fee. Expected [amount] [currency=currency] but the request contained [amount] [currency= currency]. |
| BK00270	| Invalid Exchange Set: [set] has more than 2 leg groupings. |
| BK00271	| Invalid Exchange Set: [set] and [set] have incompatible leg groupings. |
| BK00272	| Invalid order status for exchange. |
| BK00273	| Currently only one exchange set can be processed at a time. Please resubmit with a single exchange set in the request. |
| BK00274	| Invalid Exchange Set: The number of leg groupings ([#]) must match the number of travel point pairs ([#]). |
| BK00276	| Request must contain passengers on the order. |
| BK00277	| Invalid fulfillment (SMS) phone number: Calling country code not entered in the request. |
| BK00278	| Invalid Exchange Set: [set] is not exchangeable. |
| BK00279	| Feature disabled: [feature]. |
| BK00280	| Invalid exchange option - no matching option in the Exchange Summary. |
| BK00282	| Ticketable Fare locator [id] is not on order [id]. |
| BK00283	| Authorization text for Coupon not input in Return Value Document Request. |
| BK00284	| Not cancelling in SilverCore because not cancelled with supplier. |
| BK00285	| Missing Order Locator, Booking Locator or Leg Solution containing Travel Segments in the request query. |
| BK00286	| Payment could not be processed. |
| BK00287	| Can not confirm booking. Invalid payment amount. Expected [amount] [currency= currency] but the request contained [amount] [currency= currency]. |
| BK00288	| GDPR deletion already done for this record. |
| BK00289	| Request must contain TicketableFare Locators. |
| BK00290	| Invalid Rail card [id]. |
| BK00291	| [date/time] DateTime provided is outside allowed range. |
| BK00292	| Requested ticket delivery option [TDO] doesn't match selected ticket delivery option on order [id]. |
| BK00293	| Invalid exchange set - total of new prices cannot be cheaper than the original ticketable fares. |
| BK00294	| Booking contains exchanged orders so it cannot be cancelled. |
| BK00295	| The ticket delivery option must be sct (Smart Card Ticket). |
| BK00296	| The passenger [passenger] requires smart card info. |
| BK00297	| Pickup station is required. |
| BK00298	| Payment Error: [error]. |
| BK00299	| Voiding is not allowed for context [distributor=[distributor]], [channel=[channel]], [agency=[agency]], [company=[company]]. |
| BK00300	| Order cannot be voided. |
| BK00301	| Order cannot be voided. It must be in CONFIRMED status when the selected Ticket Delivery Option is TOD. |
| BK00302	| Order cannot be voided. It must be in CONFIRMED or TICKETED status when the selected Ticket Delivery Option is XVD or PAH. |
| BK00303	| Order cannot be voided. It must be in CONFIRMED status when the selected Ticket Delivery Option is office printing or By Mail. |
| BK00304	| Partial void is not supported yet. |
| BK00305	| Order cannot be voided, the selected Ticket Delivery Option is not valid for Void. |
| BK00306	| Cancellation Option information is not supported on a Void Request. |
| BK00300	| Order cannot be voided. |
| BK00301	| Order cannot be voided. It must be in CONFIRMED status when the selected Ticket Delivery Option is TOD. |
| BK00302	| Order cannot be voided. It must be in CONFIRMED or TICKETED status when the selected Ticket Delivery Option is XVD or PAH. |
| BK00303	| Order cannot be voided. It must be in CONFIRMED status when the selected Ticket Delivery Option is office printing or By Mail. |
| BK00403	| The requested fare requires a change of seat or sleeper during the journey. To proceed, contact VIA Rail directly or make a different selection. |
| BWS00000	| Element or attribute is not supported ([element]). |
| BWS00001	| A maximum of [#] leg solution(s) is allowed. |
| BWS00002	| Booking record locator required. |
| BWS00003	| Missing [param]. |
| BWS00004	| At least one search criterion must be supplied. |
| BWS00005	| Invalid [data]. One of [values] expected. |
| BWS00006	| Contact number '[number]' has invalid format. Only digits 0-9 allowed. |
| BWS00007	| A contact phone number needs to be specified for at least one passenger. |
| BWS00008	| E-mail address '[address]' is invalid. |
| BWS00009	| Invalid Passenger Fare Qualifier: Only one type of INDIVIDUAL allowed. |
| BWS00010	| Invalid payment information. Only credit card data is allowed. |
| BWS00011	| Cross Border Train: [reqs] required for each passenger. |
| BWS00012	| Only one booking record locator type may be specified. |
| BWS00013	| Invalid payment information. Only debit card data is allowed. |
| BWS00014	| Invalid fulfillment information: Either a shipping name or a shipping address is missing. |
| BWS00015	| At least one coupon expected. |
| BWS00016	| Either of elements 'receiveCoupons' or 'authorizeRefund' is missing. |
| BWS00017	| You can either 'receiveCoupons' or 'authorizeRefund' but cannot do both at the same time. |
| BWS00018	| agentInformation must be specified to create booking notes. |
| BWS00019	| Specifying Order Number is only allowed with the following operations: fulfillmentInformation, removeSelectedTicketingOption. |
| BWS00020	| Fare Qualifier type [type] is not allowed at the booking level. |
| BWS00021	| Combined customer name is too long. Must be less than fifty characters long. |
| BWS00022	| Email address must be supplied for at least one passenger. |
| BWS00023	| Refund reason and refund amount are required when processRefundWithCancel is selected. |
| BWS00024	| If ticketable fare locators are specified, order locator must be specified as well. |
| BWS00025	| Exactly one seat must be specified for the NEARBY seat preference. |
| BWS00026	| Invalid parameter combination. |
| BWS00027	| Invalid travel pass query ([query]). |
| BWS00028	| Fee must be non-negative. |
| BWS00029	| Invalid payment information. Only voucher data is allowed. |
| BWS00030	| Must supply either booking with order, or legSolution. |
| BWS00031	| Missing ([item]) in one of TravelSegment elements. |
| BWS00032	| Invalid Cardholder Information. |
| BWS00033	| NRE Handoff basket request element is not allowed in create booking operation. |
| BWS00034	| Invalid railCardSignatures. |
| BWS00035	| Multiple Vouchers not supported for voucher type [type]. |
| BWS00036	| Either departureDateTimeWindow or arrivalDateTimeWindow must be supplied. |

## Shopping service errors

| Error code | Description |
| :-- | :-- |
| SHP00000	| Unable to process request. Internal configuration problem. |
| SHP00001	| Invalid distribution channel. |
| SHP00002	| Unused. |
| SHP00003	| Must supply at least one country. |
| SHP00004	| Must supply at least one age with a quantity greater than 0. |
| SHP00005	| At least one invalid country code. |
| SHP00006	| Must supply a sale date. |
| SHP00007	| Must supply a travel start date. |
| SHP00008	| Must supply a travel end date. |
| SHP00009	| Residency cannot be null. |
| SHP00010	| Invalid residency code. |
| SHP00011	| Invalid origin code. |
| SHP00012	| Invalid destination code. |
| SHP00013	| Error retrieving solution from cache. |
| SHP00014	| Error inserting solution into cache. |
| SHP00015	| Must supply either departure date time or arrival date time. |
| SHP00016	| [datetime] DateTime provided is outside allowed range. |
| SHP00017	| Departure DateTime must be before Arrival DateTime. |
| SHP00018	| [datetime] DateTime provided is outside allowed range. |
| SHP00019	| Access denied for context [context], supply channel [channel]. |
| SHP00020	| Invalid origin code: [origin]. |
| SHP00021	| Invalid destination code: [destination]. |
| SHP00022	| Invalid maximumPermittedConnections: [#]. |
| SHP00023	| Invalid travel segments for IntermediateTravelPointsRequest. |
| SWS00000	| Element or attribute is not supported ([element]). |
| SWS00001	| A maximum of [#] travel point pair(s) is allowed. |
| SWS00002	| Invalid Travel Point Type ([type]). | Only STATION is supported. |
| SWS00003	| Must supply either departure date window or arrival date window. |
| SWS00004	| Only round trip request supported at this time. |
| SWS00005	| Must supply a legSolution. |
| SWS00006	| Missing ([item]) in one of TravelSegment elements. |

## Payment gateway errors
| Error code | Description |
| :-- | :-- |
| PG00001 |	Invalid payment data. Message: [message]. |
| PG00002	|	Invalid credit card number. |
| PG00003	|	Invalid credit card cvv value. |
| PG00004	|	Invalid credit card expiration date. |
| PG00005	|	Payment instrument must be a credit card. |
| PG00006	|	No credit card associated with payment. |
| PG00007	|	Billing address required. |
| PG00008	|	Invalid debit card start date. |
| PG00009	|	Invalid debit card issue number. |
| PG00010	|	Site card not accepted for this payment context. |
| PG00011	|	Form of payment not accepted for this payment context. |
| PG00012	|	Payment Service does not support 3DSecure, yet payer authentication is required for this payment context and form of payment. |
| PG00014	|	Credit cards not accepted as form of payment for this context. |
| PG00016	|	Payment service does not support 3D Secure. |
| PG00017	|	No account number provided. |
| PG00018	|	Vault Service Error. |
| PG00019	|	Unregistered payment token. |
| PG00020	|	Illegal use of payment token. |
| PG00021	|	Either account number or payment token is required. |
| PG00022	|	Failed to save token in validation table. |
| PG00023	|	Account number null. |
| PG00024	|	Payment token could not be deleted. |
| PG00025	|	Payment token could not be deleted (due to associated bookings). |
| PG00026	|	Payment token could not be created (configuration issue). |
| PG00027	|	Requester Profile Configuration error. |
| PG00028	|	Vault Error.  No vault record created. |

## Settlement service errors
| Error code | Description |
| :-- | :-- |
| STL00001 | Transaction [number] is not related to a context with a valid AtocGroup. |
| STL00002 | SDCI+ Error: Refund Transaction for booking_order_id: [id] has invalid reason code: [code]. |
| STL00003 | SDCI+ Error: Incorrect RecordType: [type] for TransactionType: [type] for booking_order_id: [id]. |

## Core service errors
| Error code | Description |
| :-- | :-- |
| SER00000 | Unable to process request. Internal configuration problem. |
| SER00001 | Invalid distribution channel. |
| SER00002 | Invalid context. distributor=[distributor], channel=[channel], pos=[pos], agency=[agency], company=[company]. |
| SER00003 | Invalid POS currency code for context. Expected: currency=[currency] distributor=[distributor], channel=[channel], agency=[agency], company=[company]. |
| SER00004 | Not authorized for requested context. |
| SER00005 | Invalid currency code for context. Got: currency=[currency] distributor=[distributor], channel=[channel], agency=[agency], company=[company]. |
| SER00006 | Fare qualifier problem: [error]. |
| SER00007 | Invalid order id ([order id]) for booking, [booking]. |
| SER00008 | agentInformation must be specified to create booking or order notes. |
| SER00009 | Unable to process request. Internal configuration problem. |
| SER00010 | Invalid country code: [country code]. |
| SER00011 | Invalid card number. |
| SER00012 | Invalid card type. |
| SER00013 | Invalid station code [station]. |
| SER00014 | Invalid station code [station] for supply channel [supply channel]. |
| SER00015 | Could not determine corporate cancel url from booking url: [URL]. |
| SER00016 | Operating carrier [carrier] is not accessible to this context. |
| SER00017 | Service fees cannot be added because they don't have the same currency. |
| SER00018 | Supply Channel [error] Error: Number of passengers cannot exceed [number]. |
| SER00019 | Unable to determine supply channel selector for origin: [origin] destination: [destination]. |
| SER00020 | Invalid payment configuration. |
| SER00021 | Invalid request combination. |
| SER00022 | Invalid update request - duplicate keys [keys]. |
| SER00023 | Invalid refund attempted. |
| SER00024 | Invalid supplier notes update attempt. |
| SER00025 | Invalid origin code: [code]. |
| SER00026 | Invalid destination code:[code]. |
| SER00027 | Invalid NREHandoff value for element: [element]. |
| SER00028 | Invalid pickupLocation type : [type], must be STATION. |
| SER00029 | No pickupLocation set. |

## Supply channel errors
| Error code | Description |
| :-- | :-- |
| SC00000	| Supply Channel error. |
| SC00001	| Invalid reshop response. [elements] do not match. |
| SC00002	| Supply Channel price [price] [currency] does not match expected price [price] [currency]. |
| SC00003	| Unknown Supply Channel Booking [supply channel record locator]. |
| SC00004	| Invalid reshop response. [items] no longer available. |
| SC00005	| Either of 'Adult', 'Senior' and 'Youth' passenger types combined in a single request not supported at this time. |
| SC00006	|	The ticketable fare you are trying to cancel cannot be canceled. |
| SC00007	|	Invalid Ticket Delivery Option for the itinerary. |
| SC00008	|	Ticket Delivery Option requires shipping address. |
| SC00009	|	Invalid Corporate Discount Code(s): [code]. |
| SC00010	|	Invalid argument: [argument]. |
| SC00011	|	Supply Channel booking does not match ([passengers]). |
| SC00012	|	Supply Channel booking status [status] does not match order status [status]. |
| SC00013	|	Supply Channel Ticketing Option price [price] does not match expected price [price]. |
| SC00014	|	Selected Ticketing Option requires a [amount] fee. |
| SC00015	|	Supply Channel Payment Processing Error: [error]. |
| SC00016	|	Supply Channel Error: [error]. |
| SC00017	|	Invalid Loyalty Program(s): [program]. |
| SC00018	|	Selected Ticket Delivery Option requires Shipping Address country to be one of [country]. |
| SC00019	|	Supply Channel expecting a valid province, received '[value]'. |
| SC00020	|	Supply Channel expecting a valid postal code, received '[value]'. |
| SC00021	|	Supply Channel reservation number restricted: '[number]'. |
| SC00022	|	Bus Segment [segment] cannot be booked without an accompanying train segment. |
| SC00023	|	Cross Border Train: [document] required for each passenger. |
| SC00024	|	Reservation requested for[fare code] but the fare indicates that a reservation is NOT POSSIBLE. |
| SC00025	|	Reservation not requested for [fare code] but the fare indicates that a reservation is MANDATORY. |
| SC00026	|	Supply Channel request timed out. |
| SC00027	|	Illegal Fare Qualifier: [qualifier]. |
| SC00028	|	Only round-trip mirrored leg solutions can be booked. |
| SC00029	|	Invalid credit card cvv value. |
| SC00030	|	Invalid combination of passenger types. |
| SC00031	|	At this time only One-Way and Round-Trip requests are supported by this supply channel. |
| SC00032	|	Order is already cancelled. |
| SC00033	|	Unable to extract booking data for supply channel payment processor. |
| SC00034	|	The selected fares are not combinable. |
| SC00035	|	Unable to reconcile order with supply channel. |
| SC00036	|	Invalid optional price selection: [optional price]. |
| SC00037	|	Missing confirmation information. |
| SC00038	|	First and last name required for at least one passenger. |
| SC00039	|	First and last name required for all passengers. |
| SC00040	|	Title required for all passengers. |
| SC00047	|	Unable to initiate email ticket delivery with supply channel. |
| SC00048	|	Infant passengers are not supported by the API. | See documentation for more details. |
| SC00049	|	Unsupported ticket delivery option type[type] and code[code] combination. |
| SC00050	|	DBBAHN corporate booking is not supported. |
| SC00051	|	Email is required for at least one passenger. |
| SC00052	|	Email is required for all passengers. |
| SC00053	|	Nearby booking is not allowed. |
| SC00054	|	Ticketable Fare [fare] can not be cancelled. |
| SC00055	|	No routes found for O/D pair. |
| SC00056	|	Invalid promotion code. |
| SC00057	|	Unknown Marketing Carrier
| SC00058	|	Invalid travel pass shop request (date restriction). |
| SC00059	|	Error evaluating crossborder indicator. |
| SC00060	|	Failure to build fare code; first exception is: [exception]. |
| SC00061	|	Failure to build ticketable fare; first exception is: [exception]. |
| SC00062	|	Invalid travel document type: [type]; Expected one of: [types]
| SC00063	|	Could not build ticketable fare. |
| SC00064	|	Could not store supplier notes. |
| SC00065	|	Failure to build travel segment; first exception is: [exception]. |
| SC00066	|	Failure to build leg solution; first exception is: [exception]. |
| SC00067	|	There was an error completing the seat reservation. |
| SC00068	|	Unsupported equipment type: [type]. |
| SC00069	|	There was an error in Supply Channel Initialization: [error]. |
| SC00070	|	Couldn't validate signature: [signature]. |
| SC00071	|	Couldn't validate price: [price]. |
| SC00072	|	Input Price of [amount] in Request does not equal the supply channel Price of [amount]. |
| SC00073	|	No valid timetable option. |
| SC00074	|	Departure DateTime provided is outside allowed range for this O/D pair. |
| SC00075	|	Current request time is before fare validity. |
| SC00076	|	Failed cancel attempt - the ticket has already been used. |
| SC00077	|	Failed cancel attempt - system error [error]. |
| SC00078	|	Could not build legsolution. |
| SC00079	|	Intermediate Travel Points operation is not available for the supply channel. |
| SC00080	|	Can only cancel ticket on the same date as booking issue date. |
| SC00081	|	Order number: [id] could not be found. |
| SC00082	|	Confirmation step of exchange failed, no new value document created for original order [id]. |
| SC00083	|	Requested fare not found. |
| SC00084	|	Malformed Fare Element found in the response, details: [msg]. |
| SC00085	|	Requested journey not found. |
| SC00086	|	Could not find the original value document for order # -. |
| SC00087	|	The voucher is already used. |
| SC00088	|	The voucher is not rebookable due to current business rules. |
| SC00089	|	Voucher amount [amount] provided, but voucher(s) add up to [amount]. |
| SC00090	|	Voucher amount does not match for previously used voucher number [#]. |
| SC00091	|	Voucher [#] cannot be used for this order. |
| SC00092	|	No seats available. |
| SC00093	|	Invalid Rewards Number: [#]. |
| SC00094	|	One of the rewards numbers passed in was invalid. |
| SC00095	|	Invalid Fare Qualifier number. |
| SC00096	|	Order cannot be priced. |
| SC00097	|	Seat assignments not assigned: [assignment]. |
| SC00098	|	Invalid product for a season pass order. |
| SC00129 | Supply channel [supply channel] is temporarily unavailable.  Please try again later.|
| SC00135 | Invalid traveler first and/or last name: [passenger name]. |


## Warning codes
| Error code | Description |
| :-- | :-- |
| WRN0003	|	The refund is delayed but the PNR’s passengers seats are now available for resale. |
