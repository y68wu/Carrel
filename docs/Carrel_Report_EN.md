---
title: "Carrel: Complete Project Report"
subtitle: "A detailed account of D1-D7: from problem discovery to specifications, data models, estimation and implementation planning"
author: "The Reservists | CS445 / SE463 / CS645"
date: "Spring 2026 | English consolidated report"
lang: en-US
geometry:
  - top=21mm
  - bottom=21mm
  - left=23mm
  - right=23mm
fontsize: 10pt
linestretch: 1.19
colorlinks: true
linkcolor: navy
toc: true
toc-depth: 2
numbersections: true
---

> **Scope and evidence notice.** This report synthesizes our original D1, D2 and D3 project documents; the D4 and D5 materials supplied as screenshots; the D6 cost-estimation, risk-analysis and LTL report; and the D7 research write-up. Numerical performance objectives, Function Points, COCOMO effort, likelihoods and modeled risk reductions are **design targets or analytical estimates**, not production measurements. The available evidence establishes detailed requirements-engineering deliverables. It does **not** independently establish that a complete production application was implemented, deployed or evaluated with live university users. Where an example is invented to explain a concept, it is explicitly identified as *illustrative*.

# Project identity, objective and scope

## Team and project summary

| Field | Verified project information |
|:---|:---|
| System / project | **Carrel: A Campus Study Space Booking System** |
| Team | **The Reservists** |
| Course | CS445 / SE463 / CS645 -- Software Requirements Specification and Analysis |
| Term | Spring 2026 |
| Team members | Kevin Wang; Chris Wu; Rui Lin |
| Main user roles | Students and Administrators |
| Supporting systems | University Identity Service, Calendar Service, Notification Service, and System Time |
| Intended reach | A unified online view of bookable study spaces across campus |
| Core capabilities | Search, view, reserve, check in, cancel, release no-shows, dispute, administer and analyze usage |

**One-sentence explanation:** Carrel is a proposed campus-wide booking service in which students find and reserve suitable study spaces, verify their physical arrival, and cancel if their plans change. Administrators manage inventory, rules, maintenance windows and disputes. The system seeks to prevent overlapping reservations and automatically reclaim rooms when students do not check in.

## The original problem

Before our proposal, study-space access could be fragmented by building or department. Some rooms had no sign-up process, while others depended on paper forms or informal communications. At midterm and final-exam peaks this creates four intertwined problems:

1. **Poor visibility:** a student cannot easily determine which location, time slot, capacity or amenity is available across the campus.
2. **Ghost reservations:** a booked room can remain empty while other students are turned away.
3. **Conflicts and unfair allocation:** reservations may overlap, and some users may occupy an excessive share of future time slots.
4. **Administrative overhead and weak evidence:** staff must resolve disputes and plan facilities without a single reliable record of reservations, check-ins and cancellations.

The most distinctive issue is the ghost reservation. *Illustrative example:* a student books a group room from 2:00 to 4:00 p.m. but never arrives. With no arrival verification, other students may see that room as unavailable for the whole two hours. Carrel introduces a configurable check-in grace period and a timed release process. If the configured grace period happens to be ten minutes, a no-show may be identified shortly after 2:10 p.m.; **ten minutes was an example in D1, not a universal constant**.

## Business goals and proposed success measures

We aimed to make allocation easier, fairer, more transparent and less wasteful. The proposal also gives facility staff a record of demand, underused spaces and peak-use periods. Potential operational measures would include room utilization, search-to-book conversion, check-in rate, cancellation rate, no-show rate, average reclaimed time, booking-conflict count, dispute turnaround and administrative overrides. **These are proposed or derivable metrics, not actual measurements collected in D1.**

## What we delivered, and how the deliverables connect

| Deliverable | Central question | Completed artifact / analytical result |
|:---|:---|:---|
| D1 | Why build Carrel; what is it? | Group, problem, scope, product perspective and three broad functions |
| D2 | Who is affected; what exists in this domain? | Stakeholder inventory and UML domain model |
| D3 | How do people and supporting services interact? | Actors, use-case diagram and descriptions/scenarios |
| D4 | When is each action legal and how does state change? | Updated domain/use-case models, five formal operations, twelve OCL restrictions and a state diagram |
| D5 | How good, secure and reliable must the system be? | 21 measurable quality requirements; seven AHP cost-value candidates |
| D6 | How much effort, what risks and what long-term rules? | 264 UFP, 7,656 estimated SLOC, organic COCOMO, five risks and mitigations, ten LTL properties |
| D7 | What does the work teach us about managing RE? | Software project-management and RE-for-AI literature syntheses |

# D1 -- Product scope and initial functional perspective

D1, *Forming a Group, Project Scope, and Product Perspective*, established Carrel as a **new product** rather than a patch to an identified university application. It acknowledged comparable booking systems such as library room tools, while our ambition was a common entry point across varied campus spaces.

## Initial capability 1: Find and reserve a room

The student filters by building, date/time, capacity and amenities. Carrel checks room status, other bookings, daily or weekly quotas, concurrent booking limits, advance-booking windows and slot lengths. If the request is legitimate, it creates a booking and provides a check-in code and calendar-related information such as building, room number and cancellation link. This initial capability later became several smaller use cases: search, view details, reserve, view bookings and supporting validation/notification.

## Initial capability 2: Check in, cancel and release unused space

A student verifies physical arrival either with a check-in code or an in-room QR code during the permitted window. A valid check-in secures the booked room for the permitted period. If nobody checks in before the deadline, an automatic process releases the unclaimed room and may record a no-show strike. A timely voluntary cancellation releases the slot without necessarily attracting a no-show penalty. The draft policy also proposed escalating treatment of accumulated strikes.

## Initial capability 3: Administer spaces and rules

Facilities administrators can add, edit or retire inventory; record amenities; establish rules for maximum hours, booking horizon, minimum/maximum slot duration, grace period, no-show thresholds and blackout periods; block rooms for maintenance; review disputes; and record justified overrides. This is not only an administrative screen: it supplies policy control and an auditable exception process.

**D1 outcome:** a product boundary, a concrete problem statement, plausible users, initial functions and objectives. It did not present running software or a measured dataset.

# D2 -- Stakeholders and domain model

## Stakeholder identification

Requirements are shaped by many groups, not just the student who clicks Reserve. Our D2 considered, among others:

| Stakeholder | Needs, responsibilities and expected benefit |
|:---|:---|
| Students | Search by meaningful filters; fair reservations; clear arrival and cancellation rules |
| Student government / representatives | Transparency, equity, accessibility and a channel to challenge quota policies |
| Facilities administrators | Inventory, policy, closures, oversight, audit records and exception handling |
| Library administration | Fewer paper sign-ups and better administration of library study rooms |
| Academic departments / room owners | Preserve control over departmental and special-use spaces |
| Campus IT / identity team | Trusted authentication without a second password and identity database |
| Accessibility services | Accurate searchable descriptions of accessible furniture and facilities |
| Campus security and building-access staff | Traceable occupancy, bookings and escalation in disputed cases |
| Cleaning and maintenance staff | Predictable service closures without student reservations during repairs |
| Registrar / exam scheduling | Prevent normal bookings during examinations or institutional events |
| University leadership / space planning | Identify peak demand, underuse and long-run capacity pressures |
| Calendar and identity providers | Dependable integration with well-defined data and authorization interfaces |

A useful insight from this stage is that stakeholder wishes can conflict: a student may want unlimited advance reservations, but facilities staff may want restrictive quotas to ensure fair access. The later BookingPolicy and prioritization work make these conflicts analyzable rather than implicit.

## What the domain model represents

A **domain model** is a conceptual picture of the problem world, *not* proof of a completed physical database schema. It defines common objects, attributes, associations, multiplicity and specialization. The model therefore provides a shared vocabulary for D3 use cases and D4 operations and constraints.

We organized the main concepts into five clusters:

**People and roles:** `User`, `Student`, `Administrator`. Student and Administrator specialize User, while authorization gives their actions different permissions.

**Physical inventory:** `Campus`, `Building`, `Floor`, `StudySpace`, `IndividualCarrel`, `GroupStudyRoom`, `OpenStudyArea`, `Amenity`. Spaces are located within buildings and floors and have capacities, types and features.

**Booking lifecycle:** `Booking`, `RoomQRCode`, `CheckInCode`, `CheckIn`, `Cancellation`, `CalendarInvite`, `NoShowStrike`, `Dispute`.

**Policies and operating control:** `BookingPolicy`, `BookingRule`, `QuotaRule`, `AdvanceBookingRule`, `SlotLengthRule`, `GraceWindowRule`, `NoShowThresholdRule`, `BlackoutPeriodRule`, `MaintenanceClosure`, `AdministrativeOverride`.

**External interfaces:** `UniversityIdentityService`, `CalendarService`. D6 also counts a notification service as an external interface for its estimate.

## Important associations and multiplicities

A Student may create zero or many Bookings, but each Booking has exactly one owning Student. Each Booking reserves one StudySpace; a StudySpace can be linked to many bookings over *different historical and future times*. A booking may gain a CheckIn, Cancellation, CalendarInvite, NoShowStrike or Dispute depending on its lifecycle. Campus, Building, Floor and StudySpace describe containment; rules and policies describe governance.

The distinction between **association multiplicity** and **time-conflict rules** matters. Allowing a room to relate to many bookings does *not* authorize two active reservations for the same space during the same interval. That prohibition belongs in booking behavior, invariants and ultimately concurrency-safe persistence.

**D2 outcome:** a broad stakeholder table plus a domain vocabulary sufficiently rich to explain room inventory, booking control, student rights, disputes and integrations.

# D3 -- Actors, use cases and user interaction scenarios

D3 moved from "what concepts exist?" to "what goals do actors achieve with Carrel?" A use case is an external interaction toward a goal, not simply an internal backend method.

## Actor classification

Primary actors are Student and Administrator. Supporting actors or systems are University Identity Service, Calendar Service, Notification Service and System Time. The latter represents triggers such as the passage of a check-in deadline.

The early D3 material organized interactions in a finer-grained diagram. The later D4 update established a consistent **UC1--UC17** list. We use the updated numbers below to avoid mixing revisions.

## Seventeen use cases in the updated design

| ID | Use case | Why it matters |
|:--|:---|:---|
| UC1 | Search Available Study Spaces | Filter by date/time, building, capacity and amenities |
| UC2 | View Study Space Details | See location, facilities, accessibility and available periods |
| UC3 | Reserve Study Space | Create a validated, non-conflicting booking |
| UC4 | View My Bookings | Retrieve current, upcoming and relevant historical records |
| UC5 | Check In to Booking | Demonstrate arrival within the designated window |
| UC6 | Cancel Booking | Cancel an eligible booking and return the capacity |
| UC7 | File Booking Dispute | Challenge a booking, no-show or room-conflict outcome |
| UC8 | Manage Study Space Inventory | Add, edit or retire facilities and update attributes |
| UC9 | Configure Booking Rules | Maintain quotas, windows, limits and penalties |
| UC10 | Manage Maintenance Closures | Block spaces that are unavailable for repairs or events |
| UC11 | Review and Resolve Disputes | Investigate and record a disposition |
| UC12 | Apply Administrative Override | Make justified and auditable exceptions |
| UC13 | View Usage Analytics | Analyze use, peak demand and no-shows |
| UC14 | Authenticate User | Establish identity and role through the university service |
| UC15 | Send Notification | Confirm booking, cancellation, no-show and dispute events |
| UC16 | Create Calendar Invitation | Generate a synchronized calendar event |
| UC17 | Release No-show Booking | Reclaim capacity after a missed check-in deadline |

## Detailed walk-through: UC3 Reserve Study Space

*Illustrative scenario:* Student S requests a room with capacity four, a whiteboard and an available time of 14:00--15:00.

1. Student S signs in, and Carrel validates university credentials and role.
2. S selects date/time, building preference, capacity and facilities.
3. Carrel retrieves StudySpace and Amenity records and checks preliminary availability.
4. S opens one result and sees room details and applicable conditions.
5. S selects Reserve. The service must **revalidate at submission time** rather than trust stale search results.
6. The service checks account eligibility, valid interval, maximum active bookings, overlapping existing bookings, maintenance closures and all applicable BookingRules.
7. On success, it creates and links Booking; creates CheckInCode; queues confirmation and calendar activity; and returns a BookingConfirmed outcome.
8. On failure, it reports a precise result (e.g. BookingConflict, MaintenanceConflict, RuleViolation) and does not leave a partly accepted booking.

This is why D3 distinguishes **main success scenario** from **alternate/exceptional flows**. `<<include>>` expresses mandatory reused behavior; `<<extend>>` captures context-specific or optional behavior. Student and Administrator also connect to supporting services outside the system boundary.

**D3 outcome:** a well-defined functional map that becomes the source for formal operations, quality requirements and testing obligations.

# D4 -- Formal functional model, OCL and state diagram

## Domain attributes needed for behavior

We updated the domain model with state relevant to policy checking and the lifecycle:

| Object | Fields or relationships | Role |
|:---|:---|:---|
| Student | `isActive`, `isSuspended`, `activeBookingLimit`, `currentStrikeCount`, `bookings` | Eligibility and quotas |
| StudySpace | `capacity`, `status`, `isBookable`, `accessInstructions`, `bookings` | Availability and location |
| Booking | `student`, `studySpace`, `startTime`, `endTime`, `status` | Central reservation record |
| Booking | `checkInDeadline`, `releasedAt`, `cancelledAt`, `checkIn` | Enforcement timestamps |
| CheckInCode | `booking`, `isUsed`, `validFrom`, `validUntil` | One-time credential validity |
| CheckIn | `student`, `booking`, `checkInTime` | Presence evidence |
| NoShowStrike | `isActive`, `issuedAt`, `expiresAt`, `student` | No-show enforcement |
| Dispute | `reason`, `status`, `submittedAt`, `resolution` | Challenge and disposition |
| BookingPolicy | quotas, booking horizon, slot lengths, grace window, strike threshold | Configurable rules |
| MaintenanceClosure | `startTime`, `endTime`, `status`, `reason` | Prevent booking during a closure |

For each major function the D4 required `signature`, `pre`, `modifies`, `post` and `exception`. A precondition defines success eligibility; modifies identifies the state that may change; postcondition describes success; and exception describes rejection and the expected absence of unintended mutation.

## F1 -- reserveStudySpace

**Signature:** `reserveStudySpace(s: Student, sp: StudySpace, t1: DateTime, t2: DateTime) -> b: Booking`.

**Preconditions:** s and sp exist; s is active and not suspended; sp is bookable and available; t1 precedes t2; s has fewer active bookings than the configured limit; no active Booking or MaintenanceClosure overlaps the requested interval; and the applicable BookingRules are satisfied.

The fundamental overlap condition for half-open intervals is

$$
(t_1 < b.endTime) \land (t_2 > b.startTime).
$$

*Illustrative conflict:* an existing booking covers 14:30--15:30, while the new request is 14:00--15:00. Both `14:00 < 15:30` and `15:00 > 14:30` are true, so the request conflicts. Conversely, a booking ending exactly at 14:00 need not conflict with one starting at 14:00.

**Modified/created data:** Booking, CheckInCode, CalendarInvite, Notification, CalendarService synchronization state, and Student/StudySpace booking relationships.

**Postconditions:** the new booking identifies its owner, room and interval; it is Confirmed; its check-in deadline is computed from the relevant policy; new code starts unused; invite and confirmation records are associated with it.

**Exception results:** InvalidInput, StudentNotEligible, StudySpaceUnavailable, InvalidTimeRange, BookingConflict, MaintenanceConflict, ActiveBookingLimitExceeded and RuleViolation. An unsuccessful attempt must not create an accepted booking.

## F2 -- checkInToBooking

**Signature:** `checkInToBooking(s: Student, b: Booking, code: CheckInCode, t: DateTime) -> ci: CheckIn`.

The Booking must belong to s, be Confirmed or AwaitingCheckIn, have no previous CheckIn, and be associated with a valid unused code. Time t must fall no later than checkInDeadline. On success:

```text
ci.booking = b
ci.student = s
ci.checkInTime = t
b.checkIn = ci
b.status = CheckedIn
code.isUsed = true
```

Exceptions include Unauthorized, InvalidBookingState, InvalidCheckInCode, CheckInWindowExpired and AlreadyCheckedIn. An invalid request must not fabricate a CheckIn record.

## F3 -- cancelBooking

**Signature:** `cancelBooking(s: Student, b: Booking, t: DateTime, reason: String) -> c: Cancellation`.

The Booking must exist, belong to s and still be cancellable. The original D4 condition allows `t < b.startTime OR t <= b.checkInDeadline`. On successful cancellation, Carrel creates a record with student, booking, time and reason; sets `b.status=Cancelled` and `b.cancelledAt=t`; and creates a notification. Exceptions include BookingNotFound, Unauthorized, AlreadyCancelled, CancellationNotAllowed and CancellationWindowClosed.

**Requirement reconciliation note:** D3's short description says cancellation of *future* bookings, whereas D4's precondition can allow cancellation during a post-start check-in grace window. A development team should agree on the authoritative rule before implementing or testing; this report preserves the distinction instead of silently rewriting history.

## F4 -- fileBookingDispute

**Signature:** `fileBookingDispute(s: Student, b: Booking, reason: String, t: DateTime) -> d: Dispute`.

Carrel checks that the booking belongs to the student, the reason contains meaningful text, and there is not an existing unresolved dispute for the same combination. A valid submission creates a Dispute linked to booking and student with `status=Submitted`, a timestamp and reason, and queues a DisputeSubmitted notification. Exceptions: BookingNotFound, Unauthorized, EmptyDisputeReason and DuplicateOpenDispute.

*Illustrative motivation:* a student receives a strike even though the room's QR label was damaged. A dispute links the challenge to objective Booking and CheckInCode records rather than relying on only oral accounts.

## F5 -- releaseNoShowBooking

**Signature:** `releaseNoShowBooking(b: Booking, t: DateTime) -> strike: NoShowStrike`.

This operation is triggered by System Time or a scheduling service. Preconditions: Booking is Confirmed/AwaitingCheckIn; there is no CheckIn; t is after the deadline; and no previous strike has been created for this booking. It sets Booking to NoShowReleased, stamps `releasedAt`, creates an active NoShowStrike, updates the student's count, makes appropriately remaining capacity available and creates a notice. It must not act on already checked-in, cancelled or previously released bookings. The exception cases include BookingNotFound, InvalidBookingState, AlreadyCheckedIn, CheckInWindowStillOpen and NoShowAlreadyReleased.

**Importance:** F1 protects fair allocation *at reservation time*, while F5 recovers allocation *when the reservation is not claimed*. Together they address the project's original problem.

## Twelve OCL invariants

We recorded twelve natural-language restrictions along with OCL expressions. They concern persistent state, rather than the isolated execution of one operation.

| ID | Invariant | Consequence |
|:--|:---|:---|
| OCL-1 | Active booking limit | A student cannot exceed active-booking allowance |
| OCL-2 | Valid booking time range | `startTime < endTime` |
| OCL-3 | Required parties | Booking has a Student and StudySpace |
| OCL-4 | Positive space capacity | StudySpace capacity > 0 |
| OCL-5 | Individual carrel capacity | Capacity equals 1 |
| OCL-6 | Group room capacity | Capacity exceeds 1 |
| OCL-7 | Cancellation before start | Recorded cancellation timestamp is not after booking start |
| OCL-8 | Valid no-show release | Released booking lacks check-in and deadline has passed |
| OCL-9 | Dispute has a basis | Linked to a booking or no-show strike |
| OCL-10 | No closure/active booking overlap | Maintenance cannot conflict with protected active bookings |
| OCL-11 | Code linked to active booking | No usable check-in code for cancelled/released bookings |
| OCL-12 | Consistent positive policy limits | Quotas, lengths and grace rules are valid |

Examples of the expression style:

```ocl
context Booking inv ValidBookingTimeRange:
    self.startTime < self.endTime

context StudySpace inv PositiveCapacity:
    self.capacity > 0

context Student inv ActiveBookingLimit:
    self.bookings->select(b |
        b.status = 'Confirmed' or
        b.status = 'AwaitingCheckIn' or
        b.status = 'CheckedIn'
    )->size() <= self.activeBookingLimit
```

Here `->select(...)` filters a collection and `->size()` counts its elements; the dot accesses individual object attributes. These are **logical requirements**, not proof that a deployed database already enforces them. There is also a deliberate distinction between D4's general cancellation operation and OCL-7's stricter wording; both should be reconciled prior to implementation.

## State diagram and application behavior

D4 models generic user-interface states, dividing Authenticated into **Student Portal** and **Administrator Portal** while retaining Unauthenticated and sign-out behavior.

Student-side states include `SearchSpaces`, `ViewingSpaceDetails`, `ReservingSpace`, `BookingConfirmed`, `AwaitingCheckIn`, `CheckedIn`, `Cancelled`, `NoShowReleased`, `Completed`, `DisputeDraft` and `DisputeSubmitted`. Administrator states include `InventoryManagement`, `RuleConfiguration`, `ClosureManagement`, `DisputeReview`, `OverrideReview`, `AnalyticsView` and `DisputeResolved`.

The UML transition form is `event [guard] / action`. For instance, `checkInOK [valid code and before deadline] / createCheckIn()` expresses a transition activated only under a Boolean guard. D5 retained the D4 behavioral model without changes. This does not indicate that the model was executed in production.

# D5 -- Measurable quality requirements and cost-value ranking

## Why quality requirements matter

A system can be functionally capable of reserving a room and still be unacceptable if it responds slowly, silently double-books, loses data during a third-party outage or exposes administrator-only actions. Therefore D5 formalized quality with **measurable requirements and fit criteria**.

We wrote three requirements in each of seven categories: Performance, Reliability, Integrity, Robustness, Security, Process Requirements and Operating Constraints. This makes **21 quality requirements**. Each uses a measurement method and Outstanding / Target / Minimum fit criteria.

## Quantitative quality goals from D5

| ID / topic | Target | Measurement or interpretation |
|:---|:---|:---|
| PER-1, search | 95th-percentile response <= 2.0 s | Load-test searches under a designed 500 concurrent-session peak |
| PER-2, booking | 95th-percentile transaction <= 3.0 s | Request through persisted booking and generated check-in code |
| PER-3, check-in | >= 20 validations/second, p95 < 1.5 s | QR/code burst simulation |
| REL-1, availability | >= 99.5% | Academic-term daily 07:00--24:00 ET measurement window, with planned maintenance excluded |
| REL-2, no-show correctness | >= 99.9% | Compare expected and successful automated release events |
| REL-3, MTBF | >= 30 days | Booking-service incident record and measured failures |
| INT-1, integrity | 0 conflicting active bookings | Concurrent request tests and persistence audit |
| ROB-1, calendar outage | Deliver queued invitations within 15 min of recovery | Simulated outage and queue-replay test |
| ROB-2, malformed requests | 0 crashes, >= 95% specific errors | Fuzz test and check for partial writes |
| SEC-2, session expiration | 30 min idle timeout | Inactivity and replay tests |
| SEC-3, encryption | TLS 1.2 or later | Transport configuration review |
| PRC-2, automated tests | >= 80% statement coverage | Core booking module CI report |
| OPC-1, data residency | Approved Canadian location(s) | Hosting/storage/deployment audit |
| OPC-2, monthly operations | CAD 500 target | Projected hosting, storage, monitoring and licensing |
| OPC-3, maintenance | Sunday 02:00--06:00 ET | Maintenance records and window compliance |

These figures are **requirements**, not actual outcomes. The 500 simultaneous sessions are a **peak-load scenario** used to size a test, not a recorded number of active users. The proposed August 24, 2026 pilot and September 8, 2026 full-release milestones in D5 were development targets, not proof of deployment.

## Understanding the 95th percentile

If a search test records 100 comparable response times, the 95th percentile is the value at roughly the 95th percentile of the ordered observations. A p95 target of 2.0 seconds means approximately 95% of responses should finish within two seconds. This is often more operationally informative than a simple average because it exposes the slow tail of requests when campus use spikes.

## Availability example, calculated step by step

Suppose, *for illustration*, an evaluation has exactly 30 included days, each with 17 service hours (07:00--24:00), and no excluded maintenance. The measured exposure is

$$
T = 30\times17 = 510\text{ hours}.
$$

If target availability is 99.5%, the maximum unplanned downtime is

$$
D_{max}=510\times(1-0.995)=510\times0.005=2.55\text{ hours}.
$$

Converting to minutes gives `2.55 x 60 = 153 minutes`. The number **153 minutes is explanatory arithmetic**, not a historical Carrel outage.

## High-consequence design examples

**Double-booking race (INT-1):** A checks a room and sees it free; before A saves, B checks and also sees it free; both commit overlapping reservations. Frontend validation alone cannot prevent this. Proper implementation requires concurrency-safe persistence (for example a transaction with an appropriately enforced non-overlap condition) and stress tests.

**Calendar outage (ROB-1):** A student may successfully reserve while Calendar Service is offline. The booking should remain durable; an invitation can be put on a retryable queue. A third-party outage must not automatically undo an accepted primary transaction.

**Privilege escalation (SEC-1):** A Student cannot become an Administrator merely by calling a hidden route or altering an HTTP request. Role authorization must occur server-side on administrative operations.

## Selecting seven requirements for the AHP cost-value study

One requirement was chosen from each category: PER-1, REL-1, INT-1, ROB-1, SEC-1, PRC-2 and OPC-1. With seven candidates, the number of unique pairwise comparisons per matrix is

$$
{7\choose2}=\frac{7(7-1)}2=\frac{42}2=21.
$$

For each pair, our team judged **relative value**, then repeated the procedure for **relative implementation cost**. A reciprocal pairwise-comparison matrix was formed, each column was normalized by its total, and row means were used as approximate priority weights. We assessed judgment consistency using the Consistency Index (CI) and Consistency Ratio (CR), where `CR = CI / RI`.

| Requirement | Relative value | Relative cost | Value/cost | Diagram zone |
|:---|---:|---:|---:|:---|
| PER-1 | 12.8% | 22.7% | 0.56 | Medium |
| REL-1 | 16.3% | 25.5% | 0.64 | Medium |
| INT-1 | **27.6%** | 11.9% | **2.31** | **High** |
| ROB-1 | 10.0% | 10.7% | 0.93 | Medium |
| SEC-1 | **21.7%** | 10.2% | **2.14** | **High** |
| PRC-2 | 3.9% | 10.2% | 0.39 | Low |
| OPC-1 | 7.6% | 8.8% | 0.87 | Medium |

The weights are normalized preference scores, not dollar gains and expenditures. Reported ratios are based on the original underlying numbers; recomputing them from rounded percentages may shift the final hundredth. INT-1 and SEC-1 rank as the strongest value/cost candidates. The low zone for PRC-2 does not repeal its automated-testing goal.

**Consistency:** D5 reports value CI about 0.0130 and CR about 0.0098; cost CI about 0.0111 and CR about 0.0084, using an order-seven RI of 1.32. For example, `0.0130 / 1.32 = 0.00985`, below the common 0.10 threshold. Under the AHP assumptions the judgments are internally consistent enough for prioritization.

# D6 -- Function Points, code size, COCOMO, risks and LTL

## Estimating functional size before writing production code

D6 asks for a **preliminary** development-effort estimate. We use the earlier use cases and data model as counting evidence. The five Function Point categories are External Inputs (EI), External Outputs (EO), External Inquiries (EQ), External Interface Files (EIF) and Internal Logical Files (ILF).

| FP type | Low weight | Average weight | High weight |
|:--|---:|---:|---:|
| EI | 3 | 4 | 6 |
| EO | 4 | 5 | 7 |
| EQ | 3 | 4 | 6 |
| EIF | 5 | 7 | 10 |
| ILF | 7 | 10 | 15 |

**Classification:** a booking submission changes records (EI); a booking confirmation or no-show notice produces output (EO); searching available rooms primarily retrieves data (EQ); identity, calendar and notification systems are external interfaces (EIF); long-lived Booking, Policy, StudySpace and other business data groups are ILFs.

## Exact D6 category counts

| Function class | Item count by complexity | Arithmetic | UFP |
|:---|:---|:---|---:|
| EI | 7 Average + 4 High | `7 x 4 + 4 x 6 = 28 + 24` | 52 |
| EO | 9 Average + 1 High | `9 x 5 + 1 x 7 = 45 + 7` | 52 |
| EQ | 5 Average + 2 High | `5 x 4 + 2 x 6 = 20 + 12` | 32 |
| EIF | 3 Average | `3 x 7` | 21 |
| ILF | 6 Low + 5 Average + 1 High | `6 x 7 + 5 x 10 + 1 x 15 = 42 + 50 + 15` | 107 |
| **Total** | | `52 + 52 + 32 + 21 + 107` | **264** |

The ILF share is `107 / 264 = 0.4053`, or about 40.5%. This illustrates why Carrel is a data-heavy transactional application rather than merely a collection of static web pages.

## Twelve logical internal files used in the count

| Logical group | FP weight | Key content |
|:---|---:|:---|
| User/Student/Administrator profiles | 7 | Roles, identity links, eligibility |
| Study-space catalog | 10 | Campus/building/floor/space, amenities, accessibility |
| Booking lifecycle | 15 | Status, intervals, deadline, cancellation and release |
| CheckInCode and CheckIn | 10 | Credential use, presence evidence |
| Cancellation | 7 | Reason, time, owner and booking |
| NoShowStrike | 7 | Strike state, expiry and student |
| Dispute | 10 | Explanation, state, decision and linked booking |
| BookingPolicy and BookingRule | 10 | Quotas, slot rules, grace, blackouts |
| MaintenanceClosure | 10 | Closure interval, affected space and status |
| Notification | 7 | Recipient, event type and delivery state |
| CalendarInvite | 7 | Event information, booking link and synchronization |
| AdministrativeOverride | 7 | Action, rationale, actor and timestamp |

A logical file is a **function-counting data group**, not necessarily a physical SQL table. Implementations could combine or split tables without changing the conceptual requirements.

## Convert 264 UFP to estimated lines of code

D6 uses the course conversion of **29 SLOC/UFP** as a proxy for a proposed TypeScript/React/Node style implementation, referring to the C++ line in the class conversion table. This is uncertain because language and framework choice affect code length.

$$
SLOC = 264\times29 = 7,656.
$$

$$
KLOC = \frac{7,656}{1,000} = 7.656.
$$

These are **estimates**; the team did not derive them by counting a finished source repository.

## Organic COCOMO: effort, calendar time and staffing

For the selected Organic mode, the effort model in D6 is

$$
E = a\cdot KLOC^b\cdot X,
\qquad a=2.4,\quad b=1.05.
$$

The attribute multiplier is constructed from documented assumptions:

| Adjustment | Multiplier | Rationale |
|:---|---:|:---|
| Reliability/integrity sensitivity | 1.10 | Concurrent booking, enforcement and authorization demand careful tests |
| External integrations | 1.05 | Campus identity, calendar and notifications |
| Modern development tools | 1.00 | No additional penalty |
| **Product X** | **1.155** | `1.10 x 1.05 x 1.00` |

Substitute `KLOC = 7.656`:

$$
E = 2.4\times 7.656^{1.05}\times1.155.
$$

The exponent term in the report is calculated by logarithms:

$$
\ln(7.656)\approx2.0355,
\quad 1.05\times2.0355\approx2.1373,
\quad e^{2.1373}\approx8.477.
$$

Therefore,

$$
E\approx2.4\times8.477\times1.155
=20.3448\times1.155
\approx\boxed{23.50\text{ person-months}}.
$$

The supplementary Organic schedule equation gives

$$
D=2.5E^{0.38}=2.5\times23.50^{0.38}
\approx2.5\times3.319
\approx\boxed{8.30\text{ months}}.
$$

Finally average staffing is

$$
P=\frac{E}{D}=\frac{23.50}{8.30}\approx\boxed{2.83\text{ people}}.
$$

The word **person-month** is a labor quantity, not the number of calendar months or an amount of Canadian dollars. A projected development duration of 8.30 months and about 2.83 average full-time staff is an interpretation of this model, not a proven schedule. Translating effort into monetary cost would require wages, overhead and service charges not contained in this figure.

## Five requirement risks and their assumptions

D6 selects PER-1, REL-1, INT-1, ROB-1 and SEC-1 from the D5 prioritization study. The original selected value weights sum to

$$
0.128+0.163+0.276+0.100+0.217=0.884.
$$

Dividing each by 0.884 yields approximately **0.145, 0.184, 0.312, 0.113 and 0.245** respectively, which sum to one after rounding.

| Risk | What might happen | Assigned likelihood |
|:--|:---|---:|
| R-A | Peak-load queries suffer insufficient indexing/caching | 0.35 |
| R-B | Identity service outage or incorrect role mapping | 0.25 |
| R-C | Non-atomic booking transaction accepts overlapping reservations | 0.20 |
| R-D | Calendar/notification outage blocks or delays workflow | 0.30 |
| R-E | Scheduler, deadline or time-zone error mishandles no-show release | 0.25 |

These probabilities are **planning judgments**, not observed incident frequencies.

## Risk consequences, weights and criticality

To estimate the expected weighted loss to a requirement, multiply risk likelihood by assigned impact, sum across the five risks and multiply by the requirement's weight:

$$
Loss(req) = weight(req)\sum_r likelihood(r)\cdot impact(r,req).
$$

To calculate the criticality of one risk across all selected requirements:

$$
Criticality(r) = likelihood(r)\sum_{req} weight(req)\cdot impact(r,req).
$$

| Requirement | Weight | R-A impact | R-B | R-C | R-D | R-E | Loss |
|:---|---:|---:|---:|---:|---:|---:|---:|
| PER-1 | 0.145 | 0.90 | 0.20 | 0.40 | 0.20 | 0.20 | 0.0805 |
| REL-1 | 0.184 | 0.40 | 0.80 | 0.50 | 0.60 | 0.50 | 0.1371 |
| INT-1 | 0.312 | 0.20 | 0.30 | 1.00 | 0.20 | 0.80 | 0.1888 |
| ROB-1 | 0.113 | 0.20 | 0.20 | 0.20 | 0.90 | 0.30 | 0.0571 |
| SEC-1 | 0.245 | 0.10 | 1.00 | 0.40 | 0.20 | 0.30 | 0.1225 |
| **Risk criticality** | | **0.1098** | **0.1344** | **0.1165** | **0.1058** | **0.1195** | **0.5859** |

**Worked example A -- Loss(INT-1):**

```text
= 0.312 x [0.35 x 0.20 + 0.25 x 0.30 + 0.20 x 1.00
           + 0.30 x 0.20 + 0.25 x 0.80]
= 0.312 x [0.070 + 0.075 + 0.200 + 0.060 + 0.200]
= 0.312 x 0.605
= 0.18876, approximately 0.1888.
```

**Worked example B -- Criticality(R-B):**

```text
= 0.25 x [0.20 x 0.145 + 0.80 x 0.184 + 0.30 x 0.312
           + 0.20 x 0.113 + 1.00 x 0.245]
= 0.25 x [0.029 + 0.1472 + 0.0936 + 0.0226 + 0.245]
= 0.25 x 0.5374
= 0.13435, approximately 0.1344.
```

R-B is the largest individual original criticality under the chosen model. R-C directly threatens booking integrity, but R-B threatens access and authorization across several system areas.

## Countermeasures and predicted residual risk

| ID | Control | Primary risk addressed |
|:--|:---|:---|
| CM-1 | Query/index tuning, caching where suitable and load-test release gates | R-A |
| CM-2 | Graceful identity-outage handling, guarded role cache and role-mapping tests | R-B |
| CM-3 | Atomic database transactions, conflict protection, concurrent-request tests | R-C |
| CM-4 | Retryable asynchronous queues for calendar/notification and durable confirmation | R-D |
| CM-5 | Scheduler simulation, time-zone test cases and no-show audit logs | R-E |

D6 calculates individual countermeasure effects with

$$
Effect(CM)=\sum_r reduction(CM,r)\cdot Criticality(r).
$$

For each risk, the combined reduction across independent countermeasures is modeled as

$$
CombinedReduction(r)=1-\prod_{CM}(1-reduction(CM,r)).
$$

This multiplication assumes independent marginal effects; real-world mitigations may be correlated, so it is a **model**, not measured efficacy.

| Risk | Original criticality | Combined reduction | Residual criticality |
|:--|---:|---:|---:|
| R-A | 0.1098 | 0.8172 | 0.0201 |
| R-B | 0.1344 | 0.8538 | 0.0196 |
| R-C | 0.1165 | 0.8903 | 0.0128 |
| R-D | 0.1058 | 0.8615 | 0.0146 |
| R-E | 0.1195 | 0.8637 | 0.0163 |
| **Total** | **0.5859** | | **0.0834** |

Worked example for R-A, using the reduction values in D6:

```text
CombinedReduction(R-A)
= 1 - (1 - 0.75)(1 - 0.05)(1 - 0.10)(1 - 0.10)(1 - 0.05)
= 1 - (0.25)(0.95)(0.90)(0.90)(0.95)
= 1 - 0.1828
= 0.8172.

ResidualCriticality(R-A) = 0.1098 x (1 - 0.8172)
                         = 0.1098 x 0.1828
                         = 0.0201.
```

The model's aggregate estimated reduction is

$$
1-\frac{0.0834}{0.5859}\approx\boxed{85.77\%}.
$$

**Interpretation:** given the assumed probabilities, impacts, estimated countermeasure efficiencies and independence conditions, total weighted risk criticality falls from 0.5859 to 0.0834. Recomputing from those displayed rounded totals gives 85.77%. We are *not* reporting a real-world measured improvement.

## Ten Linear Temporal Logic properties

Temporal Logic addresses **behavior across successive states**, rather than the local pre- and postconditions of one operation. Basic operators are `G p` or square/box for **always**, `F p` or diamond for **eventually**, `X p` for **next state**, and `p U q` for **until**. The D6 table uses predicates tied to the User, Booking, CheckIn, Dispute and administrator concepts.

For readability, the following formulas use `G`, `F`, `X` and `W` notation, with the same intended predicates as the D6 table. They are **design specifications**, not outputs from a model checker.

| ID | English meaning | Readable LTL form |
|:--|:---|:---|
| LTL-1 | After accepted credentials and a valid role, the correct dashboard eventually appears | `G((authOK(u) & roleValid(u)) -> F dashboardLoaded(u))` |
| LTL-2 | Reservation, check-in, cancellation or dispute submission requires authenticated user | `G((reserveReq or submitCheckIn or cancelReq or disputeSubmitted) -> authenticated(u))` |
| LTL-3 | Valid reservation of an available space eventually confirms | `G((reserveReq(s,sp) & spaceAvailable(sp) & rulesOK(s,sp)) -> F bookingConfirmed(b))` |
| LTL-4 | Active booking remains conflict-free until cancelled, no-show released or completed | `G(activeBooking(b) -> (!conflict(b) W (cancelled(b) or noShowReleased(b) or completed(b))))` |
| LTL-5 | Valid timely check-in results in CheckedIn in the next system state | `G((checkInWindowOpen(b) & submitCheckIn(s,b) & validCheckInCode(b) & beforeCheckInDeadline(b)) -> X checkedIn(b))` |
| LTL-6 | Active missed check-in deadline eventually causes no-show release and strike | `G((activeBooking(b) & deadlinePassed(b) & noCheckIn(b)) -> F (noShowReleased(b) & strikeActive(s)))` |
| LTL-7 | Eligible cancellation eventually becomes cancelled and sends a notice | `G((cancelReq(s,b) & beforeCancelDeadline(b)) -> F (cancelled(b) & cancellationNoticeSent(s,b)))` |
| LTL-8 | Booking confirmation survives a calendar outage and invite recovery is addressed | `G((bookingConfirmed(b) & calendarUnavailable) -> (F bookingConfirmationSent(b) & (calendarUnavailable U (calendarRecovered or inviteDelivered(b)))))` |
| LTL-9 | Only administrators request policy updates, overrides or closures | `G((policyUpdateReq(u) or overrideReq(u) or closeSpaceReq(u)) -> admin(u))` |
| LTL-10 | Every implementation change eventually receives review, then acceptance with tests or rejection | `G(prOpened(p) -> F (reviewed(p) & ((testsPassed(p) & merged(p)) or rejected(p))))` |

**Important semantic assumptions:** LTL-3 presumes the request remains valid and service progresses; LTL-8 contains an `Until` condition that requires recovery or invite delivery. Such environment/fairness assumptions must be stated or checked before claiming formal verification. LTL-2 and LTL-6 also require precise binding of user and booking identities when translated into a model-checker language. These caveats do not erase the design intent; they distinguish a written LTL exercise from a completed mechanized proof.

**Safety properties** prohibit bad things (overlapping active bookings, unauthorized administrative actions). **Liveness properties** demand eventual good outcomes (valid requests resolve, no-shows are released, notifications progress). D6 combines both.

# D7 -- Project management and RE for AI systems

Unlike D1-D6, D7 primarily studies how sound requirements-engineering practices are maintained by people and organizations, and how new systems such as AI challenge classical requirements assumptions. It does **not** document Carrel AI features already built.

## Requirements engineering as a continuing management practice

Our review of Hofmann and Lehner's research emphasized that effective requirements engineering depends on **domain knowledge, technical knowledge and knowledge of RE methods**. Requirements should not be treated as a document frozen after the first meeting. Teams need direct stakeholder access, visible allocation of time for reviews, realistic prioritization, traceability, iterative validation, and a change log with rationale and impacts.

*Carrel application:* changing the grace window from ten to fifteen minutes would affect BookingPolicy, `checkInDeadline`, no-show scheduling, notifications, boundary tests, the state diagram and relevant LTL properties. Updating only the interface text would leave an inconsistent system. A traceability matrix links the source policy decision to every affected artifact.

## Human factors, motivation and collaboration

Our reading of Hidellaarachchi, Grundy, Hoda and Mueller emphasized motivation, domain familiarity, communication, flexible team roles and constructive feedback. Team members may be strong in different kinds of work, so shared glossaries, review meetings, decision records and cross-checks prevent a domain assumption from being trapped in one person's document.

*Carrel application:* "cancelled," "released as a no-show," "closed for maintenance" and "administratively overridden" must remain distinct concepts. If team members use these interchangeably, the use cases, OCL invariants, state machine and testing criteria become inconsistent.

## Requirements for AI and formal methods with LLMs

D7 also reviews AI-related RE research. Unlike conventional deterministic workflow rules, AI outputs may be probabilistic and dependent on training data, distribution shift and model selection. Consequently AI requirements must consider representative datasets, data governance, accuracy/recall, fairness, transparency, explainability, monitoring and continuous revalidation. The reviewed LLM/formal-RE roadmap emphasizes complementarity: LLMs may help propose or explain candidate requirements, while formal tools check logical consistency, and humans remain accountable for whether the stated model represents real stakeholder needs.

Carrel's specified booking workflow **does not require or establish a deployed AI model**. A possible future prediction tool for no-shows or suggested spaces would be a separate extension requiring new privacy, fairness, dataset and model-evaluation requirements.

# Integrated scenario: one booking from entry to closure

This section unifies D1-D6 by walking through a concrete **invented teaching example**, not a live Carrel database record.

## Example input data

| Field | Illustrative value |
|:---|:---|
| Student ID | `S-1042` |
| StudySpace ID | `SP-204` |
| Desired interval | 14:00--15:00 |
| Grace window | 10 minutes (example policy) |
| Current student's active bookings | 1 |
| Student's configured active limit | 3 |
| Example resulting Booking ID | `B-9051` |

**Step 1 -- Authenticate.** The University Identity Service accepts the student account and Carrel loads the Student role; server-side authorization prohibits an unauthenticated reservation.

**Step 2 -- Find a space.** The student searches by building, capacity and amenities. Carrel combines catalog information with bookings and maintenance closures. The search is a preliminary availability view; submission still requires a fresh policy check.

**Step 3 -- Validate.** The active booking comparison is `1 < 3`, so the student's quota allows another reservation. Assume the interval is valid and no overlapping active booking or maintenance closure exists. A policy such as maximum slot duration is checked against 60 minutes.

**Step 4 -- Create booking.** Successful operation creates the following conceptual record:

```text
bookingID       = B-9051
student         = S-1042
studySpace      = SP-204
startTime       = 14:00
endTime         = 15:00
status          = Confirmed
checkInDeadline = 14:10
cancelledAt     = null
releasedAt      = null
```

Related CheckInCode, CalendarInvite and Notification records are also specified. Their physical storage and delivery mechanisms still belong to implementation decisions.

**Step 5A -- Successful arrival.** If a correct code is submitted at 14:05, before the 14:10 deadline, Carrel creates CheckIn, marks the one-time code as used and changes Booking.status to CheckedIn. On normal completion the booking proceeds to Completed.

**Step 5B -- No-show.** If the deadline passes without check-in, the timed operation changes status to NoShowReleased, sets releasedAt, creates a strike and makes eligible capacity available again. The CheckedIn and no-show branches should be mutually exclusive for the same booking.

**Step 5C -- Dispute.** If the student believes a defective room QR code caused an erroneous strike, a Dispute is linked to the affected booking and can be reviewed by an Administrator, with any override justified and logged.

## Traceability of this example across deliverables

| Deliverable | How it supports the scenario |
|:---|:---|
| D1 | Defines the fair-allocation / ghost-reservation problem |
| D2 | Defines Student, StudySpace, Booking, CheckIn, Policy, Strike, Dispute |
| D3 | UC3, UC5, UC7, UC11, UC12 and UC17 describe actor interactions |
| D4 | Pre/postconditions, OCL invariants and lifecycle transitions specify behavior |
| D5 | Performance, reliability, data integrity and security set testable goals |
| D6 | FP/COCOMO estimate the work; risk model targets concurrency/scheduler failures; LTL expresses temporal obligations |
| D7 | Change management and team practices preserve consistency among artifacts |

# Implementation plan and acceptance testing -- proposed, not completed

The assignment artifacts define *what should be built*. If engineering implementation followed, the recommended plan would proceed as follows. This is a proposed sequence, not a report of completed tasks.

## Phase 1: Data and authorization foundations

Establish identities, role mapping, catalog entities, booking tables/records, policy configuration and audit identifiers. Use server-side role checks; define timestamps and clear state enums. Decide transactional integrity rules and how booking intervals are represented, including time zones and daylight saving.

## Phase 2: Core availability and booking transaction

Create a search endpoint to filter StudySpaces. Create a reservation endpoint that atomically checks eligibility, quota, overlap, closure and policy rules, and then persists a booking. Include two-client and many-client race tests to prove zero overlapping accepted bookings under the specified load.

## Phase 3: Check-in, cancellation and timed enforcement

Generate and verify short-lived codes; make code use idempotent; create arrival records; allow policy-compliant cancellation; run a scheduled no-show process with retry safety and audit logs. Carefully test exact deadline boundaries and the difference between local campus time and stored UTC timestamps.

## Phase 4: Administration and external services

Add inventory, rules, maintenance windows, dispute review, analytics and justified overrides. Decouple calendar and notification retries from the primary booking commit, so an external outage does not produce an indeterminate reservation.

## Phase 5: Verification using D4--D6 evidence

- Execute concurrent reservations for the same space and interval, and verify no conflicting active records.
- Load-test search p95 and booking p95 under the specified 500-session scenario; assess sustained check-in throughput.
- Simulate lost identity/calendar connectivity and test recovery, pending notices and safe error outcomes.
- Validate role enforcement on every administrator function; attempt privilege escalation with a student token.
- Drive timed tests around exact check-in deadlines, midnight crossings and daylight-saving changes.
- Transform critical OCL invariants into database/business-rule tests and selected LTL properties into trace-based or state-machine tests.
- Record actual response distributions, incident history, staffing and cost against the **estimates**. Only then can measured outcomes be reported.

# Consolidated numbers: source and interpretation

| Metric | Value | Status |
|:---|---:|:---|
| Team members | 3 | Recorded project fact |
| Updated core use cases | 17 | D4 design count |
| Formal operations | 5 | D4 specification count |
| OCL restrictions | 12 | D4 specification count |
| Quality categories | 7 | D5 requirement grouping |
| Quality requirements | 21 | D5, 3 per category |
| AHP compared requirements | 7 | D5 selection |
| Unique comparisons per AHP matrix | 21 | `7 x 6 / 2` |
| Planned peak sessions | 500 | Load-test assumption |
| Search target | p95 <= 2.0 s | Quality target, not observed time |
| Booking target | p95 <= 3.0 s | Quality target |
| Check-in throughput | >= 20/sec | Quality target |
| Availability target | >= 99.5% | Quality target |
| No-show correctness target | >= 99.9% | Quality target |
| Automated statement coverage | >= 80% | Process target |
| Monthly operating cost | CAD 500 | Budget target, not invoice |
| Estimated Function Points | **264 UFP** | D6 analytical estimate |
| Estimated code size | **7,656 SLOC / 7.656 KLOC** | D6 converted estimate |
| COCOMO effort | **23.50 person-months** | D6 projected labor |
| COCOMO time | **8.30 months** | D6 forecast |
| Average development staff | **2.83 people** | D6 forecast |
| Risks / countermeasures | 5 / 5 | D6 modeled scenarios |
| Original risk criticality | **0.5859** | D6 model output |
| Residual risk criticality | **0.0834** | D6 model output |
| Predicted modeled reduction | **85.77%** | Recomputed from the displayed D6 rounded totals; estimate under assumptions |
| LTL properties | 10 | D6 specification count |

# Conclusions, accomplishments and limitations

Carrel's story is one of **progressive requirements elaboration**:

**Problem discovery -> Product scope -> Stakeholders -> Domain structure -> Actor/use-case interactions -> Operation and state specifications -> OCL invariants -> Measurable quality targets -> Cost-value priorities -> Function Point and COCOMO effort -> Risk and countermeasure models -> Long-term LTL properties -> Reflection on human and managerial RE practice.**

The three strongest themes are:

1. **Fairness and reclaiming capacity:** non-overlapping bookings, arrival verification, automated no-show releases and controlled exceptions.
2. **Data integrity and traceability:** bookings, check-ins, cancellations, strikes, disputes and overrides form an auditable conceptual record of what happened and why.
3. **Quantitative, testable specifications:** p95 response time, availability, code coverage, AHP preference ratios, FP/COCOMO effort and modeled risk criticality replace vague statements such as "fast," "reliable" or "low risk."

Our actual documented accomplishment is a substantial **requirements and project-planning package**. Neither a COCOMO estimate nor a UML diagram constitutes proof of working, deployed software. If a production Carrel system is eventually built, these specifications provide a basis for implementation, independent verification and measured operational evaluation.

# Documents used as the evidence base

1. **D1:** *Forming a Group, Project Scope, and Product Perspective* (team's project definition).
2. **D2:** *Domain Model & Stakeholders* (stakeholder inventory and conceptual UML structure).
3. **D3:** *Use Case Diagram & Descriptions* (actor interactions and scenarios).
4. **D4:** submitted screenshots of the updated Domain Model, updated Use Case Diagram, Functional Model, OCL and State Diagram.
5. **D5:** submitted screenshots of quality requirements, fit criteria and AHP cost-value calculations.
6. **D6:** *Cost Estimation, Risk Analysis, and LTL Constraints*, including detailed tables and calculations.
7. **D7:** *Deliverable 7*, synthesis of software project management and RE for AI systems research.

*End of report.*
