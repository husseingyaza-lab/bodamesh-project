
# BODA-MESH
## A Low-Cost, Delay-Tolerant Wireless Network for Rural Healthcare Logistics in Uganda

> **Project concept:** Offline, store-carry-forward delivery of healthcare stock reports and replenishment requests using LoRa-equipped clinic nodes, participating boda-boda motorcycle nodes, and a fixed receiver at a central hospital.
>
> **Core value proposition:** No recurring SIM, airtime, mobile-data, or internet-subscription requirement for the reporting path. Equipment, installation, power, maintenance, and participation costs still apply.

**Project status:** Proposed system / pilot evaluation  
**Deployment context:** Selected rural health facilities and a central hospital in Uganda  
**Primary use case:** Routine stock reporting and early shortage warnings

---

## Executive Summary

Boda-Mesh proposes an offline communication system that carries medical-supply stock reports and replenishment requests from rural health facilities to a central hospital. LoRa-equipped boda-bodas collect, store, carry, and forward small digital messages without relying on cellular coverage or internet connectivity.

A clinic node stores a report until an authorised motorcycle comes within radio range. The motorcycle retains the message during ordinary journeys and may forward it to other participating motorcycles. When a carrier enters the usable radio range of the central hospital’s fixed LoRa receiver, the report is transferred to the receiver and then over local Wi-Fi to a computer running a local database and offline dashboard.

Motorcycle nodes are designed for compact, weather-resistant installation beneath the seat or in another protected compartment. They will draw power through a protected connection to the motorcycle’s electrical system, with rechargeable backup power targeting approximately **15 hours** of operation. Optional GPS/GNSS functionality may provide authorised owners with last-reported-location information, subject to consent and access controls.

The project will initially support routine stock reporting and early shortage warnings. A field pilot will evaluate end-to-end delivery reliability, reporting delays, radio performance, power requirements, rider participation, and operating costs. Boda-Mesh is intended to improve the timeliness of information—not to solve every cause of healthcare stockouts or replace established emergency communication procedures.

## 1. The Problem: Rural Connectivity Gaps in Uganda

### 1.1 Operational context

Rural health facilities may know which medical supplies they need but lack a dependable way to communicate that information. When a stock warning arrives late, supply personnel have less time to assess requests, coordinate replenishment, or redistribute available stock.

Uganda’s National Medical Stores (NMS) describes procedures for escalating emergency stockouts, cold-chain failures, and urgent vaccine requests, alongside coordination for stock redistribution [1]. These processes depend in part on timely information reaching the responsible personnel.

### 1.2 Reported connectivity challenges

Published reports have documented connectivity challenges in several Ugandan communities:

- **Ntoroko:** Poor telecommunications signal was reported in areas including Kibuuku Town Council; some phones reportedly connected to networks from the Democratic Republic of Congo [2].
- **Kaabong:** Residents of Morungole, Timu, Kalapata, and Kamion reportedly used trees or hills to obtain reception. One account described relying on motorcyclists to convey messages [3].
- **Amudat:** Accounts from Achorichor and Karita described poor connectivity and reliance on Kenya’s Safaricom network [4].
- **Mityana:** Network fluctuations were raised in relation to Nakibanga Parish and Banda Sub-county [5].

These reports date from **2020–2023**. Current coverage and operating conditions at prospective pilot facilities must therefore be verified before deployment.

### 1.3 The communication gap

The gap is between stock information available at a rural facility and the ability of responsible supply personnel to receive it during prolonged connectivity interruptions.

Where no usable signal exists, airtime and data bundles cannot create a connection. Repeated attempts to communicate, or travel to a location with reception, can increase staff workload and reporting costs. Delayed information may reduce the time available to coordinate replenishment and redistribution, potentially contributing to treatment disruption when shortages remain unresolved.

However, budgets, procurement, and supply availability also affect stockouts [1]. **Boda-Mesh addresses delayed information delivery; it does not claim to eliminate every cause of healthcare supply shortages.**

### 1.4 Why boda-bodas?

Boda-bodas already move through communities and between facilities and service centres. Boda-Mesh investigates whether their ordinary routes and encounters can provide opportunities to transport small digital reports. The reported use of motorcyclists to convey messages in Kaabong provides a relevant starting point [3].

## 2. System Architecture & Technical Design

### 2.1 Architecture at a glance

```text
┌──────────────────────┐
│ Rural health facility│
│ Clinic node          │
│ Stock report + queue │
└──────────┬───────────┘
           │ LoRa encounter
           ▼
┌──────────────────────┐       Optional onward forwarding
│ Motorcycle node A    │ ──────────────────────────────┐
│ Store / carry /      │                               │
│ forward              │ ◄──── LoRa ────► Motorcycle B │
└──────────┬───────────┘                               │
           │                                           │
           │ Carrier reaches hospital radio range      │
           ▼                                           │
┌──────────────────────┐                               │
│ Central hospital     │ ◄─────────────────────────────┘
│ Fixed LoRa receiver  │
│ Local message queue  │
└──────────┬───────────┘
           │ Local Wi-Fi (no internet required)
           ▼
┌──────────────────────┐
│ Hospital computer    │
│ Local application    │
│ Local database       │
│ Offline dashboard    │
└──────────────────────┘
```

The architecture uses embedded controllers, LoRa radios, persistent storage, and optional GPS/GNSS modules. This is an IoT-based design, but **it does not require an internet connection for the core reporting path**.

### 2.2 Clinic nodes

Each participating facility will have:

- A microcontroller and LoRa transceiver.
- An antenna and persistent storage.
- A suitable power supply.
- A simple staff interface for entering stock quantities and replenishment requests, or an integration path to an existing inventory system.

The system **does not independently count medicines**. Accurate stock information must come from facility records or a separately implemented inventory-monitoring mechanism.

Each report is intended to contain:

- Facility identifier.
- Unique message identifier.
- Timestamp.
- Item code.
- Remaining quantity.
- Relevant request details.

The clinic node protects and stores each report until a transfer opportunity becomes available.

### 2.3 Compact motorcycle nodes

Each participating motorcycle carries a compact device comprising:

- Microcontroller.
- LoRa transceiver and antenna.
- Persistent storage.
- Optional GPS/GNSS module.

The electronics will be housed in a compact, weather-resistant casing and secured beneath the seat or in another suitable protected compartment. External insulated wiring must be secured away from moving parts and hot surfaces. Antenna placement must balance discreet installation with radio reception and GPS performance.

Motorcycle nodes communicate through LoRa; **Wi-Fi is not required on motorcycle nodes**.

### 2.4 Motorcycle power and backup

The node will draw power from the motorcycle through a fused connection and protected voltage converter. The power design will include suitable charging control, supply switching, and low-battery protection to limit unnecessary demand on the motorcycle battery.

A rechargeable backup battery will target approximately **15 hours** of operation when motorcycle power is unavailable. Battery capacity will be selected using measured consumption, including:

- LoRa listening and transmissions.
- Processing activity.
- The selected GPS update schedule, where GPS/GNSS is installed.

The 15-hour duration is a **design target to be validated**, not a demonstrated result. The project will cover agreed installation and maintenance costs and assess the device’s effect on participating motorcycles.

### 2.5 LoRa collection and forwarding

When an authorised motorcycle enters a clinic’s usable radio range, the devices will discover one another and exchange available messages. The motorcycle acknowledges successful receipt and retains reports in persistent storage.

Motorcycle nodes may exchange reports during later encounters. The system does not require a continuously connected chain: when no suitable next node is reachable, a device stores the message until another forwarding opportunity occurs.

Custom firmware will manage:

- Routing and forwarding decisions.
- Retries and acknowledgements.
- Duplicate detection.
- Message expiry.
- Limited replication and hop limits to control repeated circulation.
- Potential use of route information or encounter history to select useful forwarding opportunities.

The prototype uses **direct LoRa communication with DTN application logic**. This does **not** automatically mean that it implements or complies with the complete Bundle Protocol specification in RFC 9171 [6].

### 2.6 Central hospital receiver

A fixed LoRa receiver will be installed at the central hospital, with an antenna positioned to provide suitable coverage. A motorcycle within usable radio range can transfer reports directly to it over LoRa.

The receiving unit will include a microcontroller, LoRa radio, local storage, and Wi-Fi capability. It will retain accepted reports if the computer or local network is temporarily unavailable.

### 2.7 Local Wi-Fi and offline dashboard

The receiver and analyst’s computer will connect to a designated local Wi-Fi network. The network must permit communication between them, and the receiving application must be available to accept reports.

The receiver forwards messages to the application, which will validate them, reject duplicates, and save accepted reports in a local database. Successful storage will be acknowledged so queued transfers can be managed reliably.

The offline dashboard will display:

- Facility stock levels.
- Replenishment requests.
- Report timestamps.
- Delivery status.

All essential application resources will be available locally, enabling analysis without internet access. Local Wi-Fi is used for communication inside the hospital; it does not imply an internet subscription.

### 2.8 Message delivery stages

The design distinguishes between separate events:

1. **Clinic queued:** The clinic node stores the report.
2. **Motorcycle collected:** An authorised motorcycle receives and stores it.
3. **Forwarded (optional):** A motorcycle passes the report to another participating node.
4. **Hospital receiver accepted:** The fixed LoRa receiver receives and stores the report.
5. **Application stored:** The hospital application validates and stores the report in the local database.

Collection by a motorcycle and successful delivery to the hospital application are separate stages. The pilot should record these independently.

### 2.9 Optional location reporting and rider participation

With the owner’s consent, GPS/GNSS-equipped nodes may periodically record motorcycle locations. Selected updates can travel through available LoRa encounters to the hospital receiver and local database.

Authorised owners may request the **last reported location and its timestamp** through an agreed access process. This could support theft reporting and recovery efforts and may provide a non-cash participation benefit.

The service does not guarantee live tracking, theft prevention, or recovery. Updates may be delayed; device removal, damage, or depleted power may interrupt reporting. Rider interest must be assessed rather than assuming location reporting eliminates the need for financial incentives.

Healthcare messages receive priority over routine location updates. Location records require restricted access and a defined retention period.

## 3. Delay-Tolerant Networking (DTN) & LoRa Implementation

### 3.1 Store-carry-forward model

Boda-Mesh follows the **store-carry-forward** principle associated with Delay-Tolerant Networking (DTN): devices retain information when an onward connection is unavailable and transmit it when a suitable contact becomes available. DTN is designed for intermittent connectivity and variable delivery delays [6].

This is appropriate to a network in which motorcycles may encounter clinics, other motorcycles, and the hospital receiver at different times rather than forming a continuously connected network.

### 3.2 LoRa’s role

LoRa provides the radio link between clinic nodes, motorcycle nodes, and the hospital receiver. The message is physically carried between radio contacts by participating motorcycles. The design does not depend on cellular data or internet access for these transfers.

Actual range and reliability will depend on deployment conditions, antenna placement, obstructions, device configuration, and movement. The proposal does not claim a guaranteed radio range; this must be measured during the pilot.

### 3.3 Reliability controls

The proposed firmware should implement acknowledgements, retry handling, unique identifiers, duplicate detection, message expiry, bounded replication, and hop limits. These controls are intended to improve delivery management while limiting repeated circulation and unnecessary resource use.

Persistent storage protects queued messages through temporary connectivity loss and power interruptions, subject to device integrity and storage limits. Timestamps help identify older reports, while message identifiers help prevent duplicate requests.

### 3.4 Standards boundary

The proposal cites the IETF’s *Bundle Protocol Version 7* (RFC 9171) as a relevant DTN reference [6]. The prototype’s custom DTN application logic should not be described as full RFC 9171 implementation unless conformance is separately engineered and tested.

## 4. Security, Privacy & Regulatory Compliance

### 4.1 Proposed security controls

The design proposes:

- **Authenticated encryption** to protect messages.
- **Registered device identities** to authenticate participating devices and reject unauthorised messages.
- **Persistent queues** to preserve messages during temporary power or connection interruptions.
- **Unique message identifiers and timestamps** to support duplicate prevention and help users recognise stale reports.
- **Role-limited access** so riders do not need access to healthcare report contents.
- **Priority-based forwarding** that gives healthcare messages precedence over routine location updates.

The initial healthcare application is intended to handle supply information rather than identifiable patient records [source document]. This reduces the initial sensitivity of the payload, but does not remove the need to protect operational and location data.

### 4.2 Location privacy

Location reporting is optional and must be enabled only with the motorcycle owner’s consent. Access should be restricted to authorised users, and location records should have a defined retention period. The project should document who can request location information, what is logged, how long records are retained, and how consent can be withdrawn.

### 4.3 Uganda Communications Commission (UCC) checks

Before field deployment, radio frequencies, operating settings, and equipment requirements must be checked against applicable **Uganda Communications Commission (UCC)** requirements [source document]. The source proposal does not specify a particular frequency allocation, transmit-power limit, equipment approval, or licence category; these must be confirmed with UCC and relevant suppliers rather than assumed.

A pre-deployment compliance checklist should verify, at minimum:

- Permitted frequency band and channel settings for the selected LoRa equipment.
- Applicable transmit-power, duty-cycle, and other radio operating limits.
- Equipment type-approval or conformity requirements, where applicable.
- Any licensing, registration, or deployment conditions applicable to the selected configuration.
- Installation and operational arrangements consistent with applicable requirements.

This checklist is a proposed verification workflow, **not a claim that Boda-Mesh has already received UCC approval**. The final configuration should be reviewed before field testing.

### 4.4 Reliability and operational safeguards

- Keep queued reports when the next connection is unavailable.
- Retain reports at the receiver when the computer or local Wi-Fi is temporarily unavailable.
- Acknowledge successful receipt and application-level storage as separate events.
- Use message expiry and bounded replication to control old or repeatedly circulating messages.
- Keep established emergency communication procedures in place because delivery depends on rider encounters and functioning radio links.

## 5. Zero-Recurring-Cost & Cost-Avoidance Framework

### 5.1 What “zero recurring cost” means

The reporting path is designed to require **no SIM cards, airtime, mobile-data bundles, or internet subscription**. Local Wi-Fi connects the hospital receiver to the local computer and dashboard without requiring an internet connection.

This is a **zero-recurring-connectivity-cost objective**, not a claim that the system has zero total cost or no ongoing expenses.

### 5.2 Costs that remain

The project must account for:

- LoRa radios, microcontrollers, antennas, storage, and enclosures.
- Installation, wiring, voltage conversion, and battery charging protection.
- Backup batteries and eventual replacement.
- Power consumption and maintenance.
- Hospital receiver, computer, and local network requirements.
- Repairs, device replacement, and software/firmware support.
- Rider participation, coordination, and any agreed incentives.
- Training, field testing, and regulatory verification.

### 5.3 Cost-avoidance hypothesis

Potential cost avoidance to be measured includes fewer dedicated journeys solely to deliver reports, less staff time spent repeatedly trying to obtain reception, and reduced dependence on recurring connectivity purchases for the reporting workflow. Earlier information may also support better-timed supply planning and redistribution, but the system cannot guarantee stock availability or replenishment time.

The commercial case should compare the full lifecycle cost of Boda-Mesh with the current reporting process. It should include equipment amortisation, installation, maintenance, power, rider participation, and staff time—not just airtime or data costs.

A simple pilot comparison can use:

- **Baseline reporting cost:** staff time + travel attributable to reporting + airtime/data spend + other documented communication costs.
- **Boda-Mesh operating cost:** maintenance + power/battery costs + rider participation + staff time + other recurring operating costs.
- **Net cost avoidance:** baseline reporting cost minus Boda-Mesh operating cost, with equipment and installation costs reported separately or amortised over an explicitly stated period.

No quantified savings are claimed until the pilot collects baseline and operating data.

## 6. Pilot Evaluation Plan & Key Metrics

### 6.1 Pilot configuration

Begin with a selected rural health facility, a small group of participating riders, and a central receiving hospital. Before deployment, assess:

- Existing stock-reporting practices.
- Coverage across available cellular operators.
- Participating riders’ routes and encounter opportunities.
- Frequency and duration of communication-related reporting delays.
- Facility, hospital, and rider operational requirements.

Coordinate installation and use with facility managers, supply personnel, and consenting motorcycle owners.

### 6.2 Evaluation metrics

| Evaluation area | Key measures | What the pilot should establish |
|---|---|---|
| End-to-end delivery | Proportion of reports stored successfully by the hospital application; delivery delay | Whether reports arrive and how long delivery takes |
| LoRa performance | Usable range; transfer success during motorcycle movement | Radio performance in actual deployment conditions |
| Motorcycle-to-motorcycle forwarding | Number and usefulness of forwarding events; delivery contribution | Whether relaying improves delivery opportunities |
| Local Wi-Fi/application | Receiver-to-computer transfer success; queue recovery; duplicate rejection | Whether local delivery remains reliable when the computer or network is unavailable |
| Power | Measured consumption; backup runtime | Whether the design meets the approximately 15-hour backup target |
| Costs | Installation, maintenance, power, battery, rider, and operating costs | Total lifecycle and operating cost relative to baseline |
| Rider participation | Participation and retention; feedback on installation and location feature | Whether the model is acceptable and sustainable |
| Location reporting | Availability and age of last reported location; access-control performance | Practical usefulness and privacy implications |
| Supply decisions | Reports received and used in replenishment or redistribution decisions | Whether improved information supports operational action |

### 6.3 Suggested measurement definitions

To make the results interpretable, the pilot team should define metrics before testing:

- **End-to-end delivery rate:** reports successfully stored by the hospital application divided by valid reports created at clinic nodes, over a stated observation period.
- **Reporting delay:** elapsed time between report creation and successful application storage; report median and upper-percentile delay as well as the range.
- **Transfer success rate:** successful acknowledged transfers divided by attempted transfers, reported separately for clinic-to-motorcycle, motorcycle-to-motorcycle, and motorcycle-to-hospital links.
- **Backup runtime:** observed operating time under a documented workload and GPS update schedule.
- **Cost per successfully delivered report:** defined operating costs divided by reports successfully stored, with equipment and installation costs either shown separately or amortised transparently.
- **Rider participation:** number of riders enrolled and actively participating over the pilot period, with reasons for withdrawal recorded where consented.

These are recommended operational definitions for evaluation; the source proposal does not provide numerical acceptance thresholds. Thresholds should be agreed before the pilot, based on facility needs and baseline findings.

### 6.4 Practical limits and risk management

- Delivery depends on rider encounters and usable radio links; a fixed delivery time cannot be guaranteed.
- The system supports routine reporting and early warnings but does not replace emergency communication procedures.
- The dashboard depends on accurate facility records or an implemented inventory integration.
- The system cannot guarantee that requested supplies are available or that replenishment occurs by a particular time.
- Rider participation and the value of location reporting must be tested rather than assumed.
- Current cellular coverage and regulatory requirements must be verified before field deployment.

## 7. Expected Contribution

Boda-Mesh proposes an affordable, healthcare-focused adaptation of DTN to Uganda’s existing motorcycle transport network. Its main contribution is the combination of:

- Offline LoRa delivery of stock reports.
- Priority-based forwarding of healthcare messages.
- Compact motorcycle-powered nodes with rechargeable backup.
- Local hospital storage and an offline dashboard.
- Optional consent-based last-reported-location functionality.
- A cost-avoidance model that reduces dependence on recurring connectivity purchases while explicitly accounting for other costs.

The pilot will test whether this design improves reporting reliability and affordability for facilities experiencing unreliable cellular coverage.

## References

[1] National Medical Stores, “Health facility FAQs.” Accessed: Sep. 30, 2026. [Online]. Available: https://nms.go.ug/health-facility-faqs/

[2] C. Tusiime, “Candidates in Ntoroko struggling to conduct virtual campaigns,” *Uganda Radio Network*, Nov. 10, 2020. Accessed: Sep. 30, 2026. [Online]. Available: https://ugandaradionetwork.net/story/candidates-in-ntoroko-struggling-to-conduct-virtual-campaigns-?districtId=506

[3] G. Eyoku, “Connecting through trees: The struggle for network connectivity in Kaabong,” *Uganda Radio Network*, Aug. 24, 2023. Accessed: Sep. 30, 2026. [Online]. Available: https://ugandaradionetwork.net/story/connecting-through-trees-the-struggle-for-network-connectivity-in-kaabong

[4] G. Eyoku, “Lack of radio station, poor network connectivity cripples communication in Amudat,” *Uganda Radio Network*, Oct. 2, 2023. Accessed: Sep. 30, 2026. [Online]. Available: https://www.ugandaradionetwork.net/story/lack-of-radio-station-poor-network-connectivity-cripples-communication-in-amudat

[5] O. O. Dominic, “Parliament tasks telecom operators on improved tariffs, network coverage,” *Uganda Radio Network*, Feb. 7, 2022. Accessed: Sep. 30, 2026. [Online]. Available: https://ugandaradionetwork.net/story/parliament-tasks-telecom-operators-on-improved-tariffs-network-coverage-?districtId=553

[6] S. Burleigh, K. Fall, and E. Birrane III, “Bundle Protocol Version 7,” IETF, RFC 9171, Jan. 2022, doi: [10.17487/RFC9171](https://doi.org/10.17487/RFC9171).

[7] S. Guo, “Algorithms and design principles for rural kiosk networks,” University of Waterloo, 2007. [Online]. Available: https://uwspace.uwaterloo.ca/items/658cdf6d-8d56-4ea5-b832-b94a1606979b

---

## Suggested repository structure

```text
boda-mesh/
├── README.md
├── docs/
│   └── project-portfolio.html
├── assets/
│   ├── banner.png
│   └── architecture-diagram.png
├── firmware/
│   ├── clinic-node/
│   ├── motorcycle-node/
│   └── hospital-receiver/
├── dashboard/
├── tests/
└── LICENSE
```

*Repository structure is a suggested organisation, not a claim that these implementation files already exist.*
