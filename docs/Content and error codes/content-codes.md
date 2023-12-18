:::(Error) (**In this article**, we define the content codes you will need when working with the SilverCore API.)
:::

## Intro
The SilverCore API often communicates in the form of SilverRail content codes.  Clients developing end-user applications using the SilverCore API need to map to these codes and present the information in an easy to read and comprehensible format. 

In most cases, clients can use the [Published Data API](/v1/docs/pub-data-getting-started){target="_blank"} to request and obtain this information on demand.

In this article, we provide additional explanations for several content code types, codes not available through the Published Data API, as well as supplementary information to support testing efforts, for example.

## Carriers

Licensed partners can retrieve the full list of codes through the [Published Data API](/v1/docs/published-data-messages#carrier){target="_blank"}.  

## Stations

SilverRail assigns a unique code to every station defined in the SilverCore system. Prior to version 2.0 of the API, station codes were three characters long. Starting with the 2.0 release, station codes are five characters long and use the following naming convention:

- The first two characters are the [ISO country code](http://www.iso.org/iso/english_country_names_and_code_elements)
- The last three characters are assigned by SilverRail to identify the station. If the supply channel uses three-character station codes (such as Amtrak or RDG), these will likely be used. If the station has an [IATA assigned code](http://en.wikipedia.org/wiki/List_of_IATA-indexed_railway_stations), this will be used.

**Examples**:

|Station code|Description|
| :-- | :-- |
|USBOS|Boston South station|
|GBPAD|London Paddington station|
|USBFD|Buffalo, NY|
|BEZYR|Brussels, Belgium|

Licensed partners can retrieve the full list of codes through the [Published Data API](/v1/docs/published-data-messages#station){target="_blank"}.

## Travel segments

|SilverRail code|Description|
| :-- | :-- |
|BUS|Bus|
|FERRY|Ferry|
|HOVERCRAFT|Hovercraft|
|SUBWAY|Metro, Subway, Underground, etc|
|TAXI|Taxi|
|TRAIN|Train|
|TRAM|Tram or Tramlink|
|TRANSFER|A non-timetabled/scheduled travel segment. The equipmentType attribute should <br>indicate the actual segment type (bus, walking, etc).|

## Equipment types

Licensed partners can retrieve the full list of codes through the [Published Data API](/v1/docs/published-data-messages#equipment-type){target="_blank"}.

## Fare qualifiers

Licensed partners can retrieve the full list of codes through the [Published Data API](/v1/docs/published-data-messages#fare-qualifiers){target="_blank"}.

|Carrier|SilverRail program code|fareQualifier@type|Description|Sample Id for testing|
| :-- | :-- | :-- | :-- | :-- |
|Amtrak|Amtrak|CORPORATE|Amtrak Corporate Discount|11955512|
|Amtrak|NAT_ASSOC_RAIL_PAX|DISCOUNT_CARD|NARP|951623|
|MTR|MTR|CORPORATE|MTR Corporate Card|1145|
|Renfe|CARNET_GO_25_ISTC|DISCOUNT_CARD|Carné GO 25 ISTC|123456789|
|Renfe|CARNET_JOVEN|DISCOUNT_CARD|Carné Joven Euro|123456789|
|Renfe|Renfe|CORPORATE|Renfe Corporate Discount|EOMHE901|
|Renfe|TARJETA_DORADA|DISCOUNT_CARD|Tarjeta Dorada|710083665|
|Renfe|TARJETA_JOVEN_RENFE|DISCOUNT_CARD|Tarjeta Joven|70021936|
|Renfe|TARJETA_RAIL_PLUS_JUNIOR|DISCOUNT_CARD|Tarjeta Rail Plus Junior|123456789|
|Renfe||DISCOUNT_CARD|Numero Tarjeta +Renfe|62173935|
|Swedish Rail|ARSKORT_GULD_RESPLUS|DISCOUNT_CARD|SJ Årskort Guld med Resplustillägg|9752209701027516|
|Swedish Rail|SJ|CORPORATE|SJ Corporate Card|8367|
|Swedish Rail|SJ|PROMOTION|SJ Promotion Program|pro10|
|VIA Rail|VIA|CORPORATE|VIA Rail Corporate Card|800704|

## Loyalty cards

Licensed partners can retrieve the full list of codes through the [Published Data API](/v1/docs/published-data-messages#loyalty-cards){target="_blank"}.

|Carrier|Code|Description|Sample Id for testing|
| :-- | :-- | :-- | :-- |
|Amtrak|Amtrak|Amtrak Guest Rewards|7000019195 (TERRANCE ROWLEY)<br>7005057802 (DAVID MONTANARI)<br>7002968399 (JAMES PAK)|
|EUROSTAR|EUROSTAR|Eurostar Frequent Traveler Program||
|MTR|MTR_CLUB_XPRESS|club Xpress||
|Norrtag|SJ_PRIO|SJ Prio|9752210235732100 (Sam Smith)|
|Renfe|RENFE|+Renfe|93346708|
|SNCB|BENE_EUR_FREQ|Eurostar Frequent Traveler Program||
|SNCB|Thalys|Thalys The Pass||
|SNCF|FR_GR_VOY|Grand Voyageur||
|SNCF|FR_GR_VOY_PL|Grand Voyageur Plus||
|SNCF|FR_OTHER|Other SNCF card||
|SNCF|FR_T_VOY_CLUB|T Grand Voyageur Le Club||
|Swedish Rail|SJ_PRIO|SJ Prio|9752210235732100 (Sam Smith)|
|VIA Rail|VIA|Via Préférence|1234567|

## Ticket Delivery Options

|SilverRail code|SilverRail type|Description|
| :-- | :-- | :-- |
|EML|E_MAIL|Ticket by E-mail|
|ETK|E_TICKET|E-Ticket|
|MTD|M_TICKET|Ticket By Multiple Delivery Options|
|PAH|E_TICKET|Print-At-Home|
|PRN|PRINT|Local Printer|
|SCT|S_TICKET|Smart Card Ticket|
|SMS|E_TICKET|Ticket By SMS (Text)|
|TBC|TICKET_ON_TRAIN|Ticket by Conductor|
|TBM|MAIL|Ticket by Regular Mail|
|TBO|MAIL|Ticket by Overnight Mail|
|TBX|MAIL|Ticket by Express Mail|
|TML|ON_DEPARTURE|Ticket By Metrolink|
|TOF|ON_DEPARTURE|Ticket at Ticketing Office|
|TOP|PRINT|Ticket Office Printing|
|TVM|ON_DEPARTURE|Ticket Vending Machine or Kiosk|
|TVP|PRINT|Ticket Vending Machine Printing|
|XVD|E_TICKET|Electronic Ticket - XML Value Document|

## Ticket Delivery Regions

|SilverRail code|Region|Country codes|
| :-- | :-- | :-- |
|UK|United Kingdom|GB|
|EU|European Union|AT, BE, DE, DK, ES, FI, FR, IE, IT, LU, NL, PT, SE|
|NA|North America|US, CA|
|SJSMS|SJ SMS Coverage|AL, DZ, AS, AR, AM, AU, AT, AZ, BH, BD, BY, BE, BZ, BA, BW, BR, BN, BG, BF, KH, CF, CL, CN, CO, CD, HR, CY, CZ, DK, DO, EG, EE, FO, FI, FR, MQ, GM, GE, DE, GI, GR, HN, HK, HU, IS, IN, ID, IQ, IE, IL, IT, CI, JM, JP, JO, KZ, KE, XK, KW, KG, LV, LB, LS, LY, LI, LT, LU, MO, MK, MG, MW, MY, ML, MT, MX, MD, MA, MZ, NA, NL, NZ, NE, NG, NO, PK, PY, PE, PH, PL, PT, QA, RE, RO, RU, RW, SA, RS, SG, SK, SI, ZA, KR, ES, LK, SD, SR, SE, CH, SY, TW, TJ, TZ, TH, TG, TT, TN, TR, AE, GB, UG, UA, VE, ZM, ZW|
|GLOBAL|Ticket delivery is unrestricted||

## Service classes

|SilverRail code|Description|
| :-- | :-- |
|PREMIER|Premier class|
|FIRST|First class|
|SECOND|Second class|
|THIRD|Third class|
|SLEEPER|Sleeper accommodation|
|SLEEPERPLUS|Sleeper plus accommodation|
|AUTO|*Not currently supported*|
|BEDROOM|*Not currently supported*|
|MISC|*Not currently supported*|
|NOT_APPLICABLE|SilverCore is unable to return an appropriate service class|

## Service class mappings by carrier

### Amtrak

|Carrier cabin class|SilverRail code|
| :-- | :-- |
|First Class Accessible Seat | FIRST |
| First Class Seat | FIRST |
|First Class Wheelchair Space| FIRST
|Business Class Accessible Seat|SECOND|
|Business Class Seat|SECOND|
|Business Class Wheelchair Space|SECOND|
|Business Class Accessible Seat|THIRD|
|Business Class Seat | THIRD|
|Business Class Wheelchair Space|THIRD|
|Coach Accessible Seat|THIRD|
|Coach Lower Level Seat|THIRD|
|Coach Reserved Seat|THIRD|
|Coach Unreserved Seat|THIRD|
|Coach Wheelchair Space|THIRD|
|Thruway Accessible Seat|THIRD|
|Thruway Reserved Seat|THIRD|
|Thruway Unreserved Seat|THIRD|
|Thruway Wheelchair Space|THIRD|
|Accessible Bedroom | SLEEPER|
|Bedroom|SLEEPER|
|Business Travel Roomette|SLEEPER|
|Family Bedroom|SLEEPER|
|Roomette| SLEEPER|
|Superliner Accessible Bedroom|SLEEPER|
|Viewliner Accessible Bedroom|SLEEPER|
|Viewliner Roomette|SLEEPER|

### RDG

|Carrier cabin class|SilverRail code|
| :-- | :-- |
|First|FIRST
|Standard|THIRD|
|Standard Premium|SECOND

### Eurostar
Carrier cabin class|SilverRail code|
| :-- | :-- |
|Business Premier|PREMIER|
|Standard Premier|FIRST|
|Standard|PREMIER|SECOND|

### Renfe (AVE, AVE LANZADERA)
|Carrier cabin class|SilverRail code|
| :-- | :-- |
|Club|PREMIER|
|Business|PREMIER|
|Preferente|FIRST|
|Turista Plus|FIRST|
|Confort Turista|SECOND|
|Estandar|SECOND|

### Renfe (ALARIS, ALTARIA, ALVIA, AVCITY, INTERCITY)

|Carrier cabin class|SilverRail code|
| :-- | :-- |
|Preferente|FIRST|
|Turista Plus|FIRST|
|Confort Turista|SECOND|
|Estandar|SECOND|

### Renfe (ARCO, DIURNO, ELIPSOS, ESTRELLA, EUROMED, LUSITANIA, SUREXPRESSO, TALGO, TRENHOTEL, AEXPRESS, CEXPRESS, DELTA, MD, R598, REGIONAL, REXPRESS, TRD)
|Carrier cabin class|SilverRail code|
| :-- | :-- |
|Preferente|SECOND|
|Turista Plus|SECOND|
|Confort Turista|THIRD|
|Estandar|THIRD|

### Renfe (AVLO)
|Carrier cabin class|SilverRail code|
| :-- | :-- |
|Turista|SECOND|

### SNCF (TGV)
|Carrier cabin class|SilverRail code|
| :-- | :-- |
|First|FIRST|
|Second|SECOND|

### SNCF (TGV)
|Carrier cabin class|SilverRail code|
| :-- | :-- |
|1 klass plus|FIRST|
|1 klass|SECOND|
|1 class|SECOND|
|Sittplats Komfort|SECOND|
|2 klass|THIRD|
|2 class|THIRD|
|Sittplats|THIRD|
|Seat|THIRD|

### Thalys
|Carrier cabin class|SilverRail code|
| :-- | :-- |
|Comfort 1|FIRST|
|Comfort 2|SECOND|

### VIA Rail
|Carrier cabin class|SilverRail code|
| :-- | :-- |
|Business|SECOND|
|Economy|THIRD|

## Fare classes
|Carrier|SilverRail code|
| :-- | :-- |
|Amtrak|FLEXIBLE|
|Amtrak|PREMIUM|
|Amtrak|SAVER|
|Amtrak|SLEEPER|
|Amtrak|STANDARD|
|Amtrak|UNRESERVED|
|Amtrak|VALUE|
|RDG|ADVANCE|
|RDG|ADVANCE_DAY|
|RDG|ADVANCE_DAY_OPEN_RETURN|
|RDG|ADVANCE_OPEN_RETURN|
|RDG|ADVANCE_RETURN|
|RDG|ADVANCE_STANDARD_PREMIUM|
|RDG|ANYTIME|
|RDG|ANYTIME_DAY|
|RDG|ANYTIME_DAY_OPEN_RETURN|
|RDG|ANYTIME_DAY_OPEN_TRAVELCARD|
|RDG|ANYTIME_DAY_RETURN|
|RDG|ANYTIME_DAY_TRAVELCARD|
|RDG|ANYTIME_OPEN_RETURN|
|RDG|ANYTIME_OPEN_TRAVELCARD|
|RDG|ANYTIME_RETURN|
|RDG|ANYTIME_TRAVELCARD|
|RDG|CARNET|
|RDG|CARNET_RETURN|
|RDG|CARNET_SEASON|
|RDG|PLUSBUS_DAY|
|RDG|OFFPEAK|
|RDG|OFFPEAK_DAY|
|RDG|OFFPEAK_DAY_OPEN_RETURN|
|RDG|OFFPEAK_DAY_OPEN_TRAVELCARD|
|RDG|OFFPEAK_DAY_RETURN|
|RDG|OFFPEAK_DAY_TRAVELCARD|
|RDG|OFFPEAK_OPEN_RETURN|
|RDG|OFFPEAK_OPEN_TRAVELCARD|
|RDG|OFFPEAK_RETURN|
|RDG|OFFPEAK_TRAVELCARD|
|RDG|SEASON_ANNUAL|
|RDG|SEASON_CUSTOM|
|RDG|SEASON_MONTHLY|
|RDG|SEASON_WEEKLY|
|RDG|SLEEPER_FIXED_CLASSIC_SOLO &#42;&#185;|
|RDG|SLEEPER_FIXED_CLASSIC_SOLO_CHILD &#42;&#185;|
|RDG|SLEEPER_FIXED_CLASSIC_TWIN &#42;&#185;|
|RDG|SLEEPER_FIXED_CLUB_SOLO &#42;&#185;|
|RDG|SLEEPER_FIXED_CLUB_SOLO_CHILD &#42;&#185;|
|RDG|SLEEPER_FIXED_CLUB_TWIN &#42;&#185;|
|RDG|SLEEPER_FIXED_SEATING &#42;&#185;|
|RDG|SLEEPER_FLEXIBLE_CLASSIC_SOLO &#42;&#185;|
|RDG|SLEEPER_FLEXIBLE_CLASSIC_SOLO_CHILD &#42;&#185;|
|RDG|SLEEPER_FLEXIBLE_CLASSIC_TWIN &#42;&#185;|
|RDG|SLEEPER_FLEXIBLE_CLUB_SOLO &#42;&#185;|
|RDG|SLEEPER_FLEXIBLE_CLUB_SOLO_CHILD &#42;&#185;|
|RDG|SLEEPER_FLEXIBLE_CLUB_TWIN &#42;&#185;|
|RDG|SLEEPER_FLEXIBLE_SEATING &#42;&#185;|
|RDG|SLEEPER_FLEXIBLE_SUITE_SOLO &#42;&#185;|
|RDG|SLEEPER_FLEXIBLE_SUITE_TWIN &#42;&#185;|
|RDG|SUPER_OFFPEAK|
|RDG|SUPER_OFFPEAK_DAY|
|RDG|SUPER_OFFPEAK_DAY_OPEN_RETURN|
|RDG|SUPER_OFFPEAK_DAY_OPEN_TRAVELCARD|
|RDG|SUPER_OFFPEAK_DAY_RETURN|
|RDG|SUPER_OFFPEAK_DAY_TRAVELCARD|
|RDG|SUPER_OFFPEAK_OPEN_RETURN|
|RDG|SUPER_OFFPEAK_OPEN_TRAVELCARD|
|RDG|SUPER_OFFPEAK_RETURN|
|RDG|SUPER_OFFPEAK_TRAVELCARD|
|Deutsche Bahn|NORMALPREIS|
|Deutsche Bahn|SPARPREIS|
|Eurostar|CHILD|
|Eurostar|NONFLEXIBLE|
|Eurostar|SEMIFLEXIBLE|
|Renfe|BASICA|
|Renfe|BASICO|
|Renfe|CARJETA_JOVEN|
|Renfe|ELIGE|
|Renfe|ELIGE COMFORT|
|Renfe|ELIGE CONFORT PREMIUM|
|Renfe|ELIGE PREMIUM|
|Renfe|PREMIUM PREMIUM|
|Renfe|FLEXIBLE|
|Renfe|IDAYVUELTA|
|Renfe|MASTER_EMPRESA|
|Renfe|NINOS|
|Renfe|PREFERENTE_EMPRESA|
|Renfe|PREMIUM|
|Renfe|PREMIUM_EMPRESA|
|Renfe|PROMO|
|Renfe|PROMOPLUS|
|Renfe|TARJETA_DORADA|
|Renfe|TARJETA_JOVEN_RENFE|
|Swedish Rail|NON-REFUNDABLE|
|Swedish Rail|REBOOKABLE|
|Swedish Rail|REFUNDABLE|
|Thalys|HILIFE|
|Thalys|KID|
|Thalys|OPTIWAY|
|Thalys|SMOOVE|
|Thalys/Eurostar|SENIOR|
|Thalys/Eurostar|YOUTH|
|VIA Rail|BUSINESS|
|VIA Rail|BUSINESS_PLUS|
|VIA Rail|ECONOMY|
|VIA Rail|ECONOMY_PLUS|
|VIA Rail|ESCAPE|

:::(Info) (&#42;&#185; Caledonian Sleeper fares are not enabled by default.  Please reach out to the Service Operations team to learn more about booking.)
:::

## Train amenities

Licensed partners can retrieve the full list of codes through the [Published Data API](/v1/docs/published-data-messages#train-amenities){target="_blank"}.

## Acceptable forms of payment
Licensed partners can retrieve the full list of codes through the [Published Data API](/v1/docs/published-data-messages#acceptable-forms-of-payment){target="_blank"}.
