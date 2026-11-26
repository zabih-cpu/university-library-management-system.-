 Part A: Stakeholder Analysis, User Stories & AI Review

1. Stakeholder Identification & Needs Table

| Stakeholder | Role / Involvement | Primary Needs & Expectations |
| :--- | :--- | :--- |
| **Student** | Primary end-user who searches, borrows, renews, and reserves books. | Fast catalog search by title/author/ISBN, real-time copy availability, online reservations, borrowing history, and timely due-date/overdue alerts. |
| **Faculty Member** | Academic staff with extended borrowing limits and reference needs. | Higher borrowing quotas, longer loan periods, priority holds on academic titles, and easy access to course-related reference availability. |
| **Librarian** | Front-desk operator managing daily circulation and catalog records. | Quick barcode/ID-based check-out and check-in, effortless book catalog updates (add/edit/retire), fine calculation, and reservation queue handling. |
| **Library Administrator** | System manager overseeing policies, user roles, and reporting. | Configurable loan rules (quotas, loan days, fine rates), role-based access control, overdue/inventory analytics reports, and system audit logs. |
| **University IT Support** | Technical staff maintaining server uptime, backups, and university SSO. | Reliable web deployment, integration with existing university student/faculty ID databases, automated backups, and clear error logging. |

.8 SSO** | Single Sign-On — authenticating users via existing university credentials/directory. |

---

## 2. Overall System Description and Stakeholder Characteristics

| Stakeholder / Aspect | Role & Characteristics | System Interaction & Needs |
| :--- | :--- | :--- |
| **Product Perspective** | Client-server web application accessible via desktop and mobile browsers, integrated with university member records and email/SMS notification services. | Centralizes circulation, catalog inventory, fine tracking, and reporting in one system. |
| **Student** | End-user with moderate-to-high digital literacy; searches on mobile/laptop and borrows within standard student limits. | Fast catalog search, real-time availability, online reservations/renewals, borrowing history, and due/overdue alerts. |
| **Faculty Member** | Academic staff needing higher borrowing limits, longer loan durations, and priority course/reference access. | Extended loan quotas/periods, hold queue visibility, and timely notifications. |
| **Librarian** | Front-desk operator requiring fast, barcode/keyboard-friendly workflows for daily circulation and catalog updates. | Rapid check-out/check-in, fine calculation, reservation hold handling, and adding/updating/retiring books. |
| **Library Administrator** | System manager overseeing rules, access control, and institutional reporting. | Configurable loan/fine policies, role permissions, circulation/overdue reports, and audit logs. |
| **University IT Support** | Technical maintainer of hosting, backups, security, and directory integration. | Reliable uptime, automated backups, HTTPS/RBAC security, and clear error/audit logs. |

---

## 3. Functional Requirements

| Req ID | Requirement Title | Specification ("The system shall...") | Priority |
| :--- | :--- | :--- | :--- |
| **FR-01** | Catalog Search & Filtering | The system shall allow users to search books by title, author, ISBN, subject, or year and filter by availability status. | Must Have |
| **FR-02** | Member Registration & Verification | The system shall allow Librarians to register and update member profiles linked to a unique University ID with role-based borrowing limits. | Must Have |
| **FR-03** | Book Check-Out | The system shall allow Librarians to issue an available Copy ID to an active member after verifying borrowing quota and overdue/fine eligibility. | Must Have |
| **FR-04** | Book Check-In | The system shall allow Librarians to process returned books by Copy ID, updating status to *Available* or *Reserved* (if a hold queue exists). | Must Have |
| **FR-05** | Automated Fine Calculation | The system shall calculate overdue fines automatically as `(Return Date - Due Date) × Daily Fine Rate` for late returns. | Must Have |
| **FR-06** | Book Reservation Queue | The system shall allow members to reserve titles with zero available copies using a FIFO queue and hold returned items for 48 hours. | Should Have |
| **FR-07** | Online Loan Renewal | The system shall allow members to renew a loan once online if the item is not overdue and has no active reservations. | Should Have |
| **FR-08** | Automated Notifications | The system shall send automated notifications 48 hours before due date, upon becoming overdue, and when a reserved copy is ready for pickup. | Should Have |
| **FR-09** | Catalog & Inventory CRUD | The system shall allow Librarians to add titles, register multiple Copy IDs per ISBN, update records, and mark lost/damaged copies as *Inactive*. | Must Have |
| **FR-10** | Policy Configuration | The system shall allow Administrators to configure loan duration, max borrowing quota, hold window, and daily fine rates. | Could Have |
| **FR-11** | Circulation & Overdue Reporting | The system shall generate exportable (CSV/PDF) reports for overdue books, fine totals, active loans, and circulation trends. | Could Have |
| **FR-12** | Audit Logging | The system shall record immutable logs with user ID and timestamp for circulation actions, catalog retirements, and policy changes. | Should Have |

---

## 4. Non-Functional Requirements

| Req ID | Category | Measurable Specification | Priority |
| :--- | :--- | :--- | :--- |
| **NFR-01** | Performance | Search queries and check-out/check-in transactions shall complete in **under 2 seconds** for up to 200 concurrent users and 100,000 book records. | Must Have |
| **NFR-02** | Security | The system shall enforce RBAC per role, hash passwords with strong algorithms (bcrypt/Argon2), and encrypt traffic with HTTPS (TLS 1.2+). | Must Have |
| **NFR-03** | Availability & Reliability | The system shall maintain **99.5% uptime** during library hours (8:00 AM – 8:00 PM) with automated daily backups and a 24-hour RPO. | Should Have |
| **NFR-04** | Usability | Librarians shall complete a standard check-out or return in **3 or fewer clicks/scans**, and the portal shall be responsive on desktop and mobile. | Should Have |
| **NFR-05** | Maintainability | Borrowing quotas, loan periods, and fine rates shall be stored in configuration/database settings rather than hardcoded application logic. | Could Have |


## 5. AI-Assisted Requirements Review Log 

During requirements validation, an AI assistant reviewed the initial LMS draft requirements for **ambiguity, omissions, conflicts, and testability**. Below are six recorded review suggestions along with the team's decision and engineering justification:

| Suggestion ID | Issue Category | AI Review Suggestion | Decision | Justification |
| :--- | :--- | :--- | :--- | :--- |
| **AI-01** | **Ambiguity & Testability** | Replace vague phrasing like *"The system should search books quickly"* with a measurable threshold: *"Search queries shall return results in under 2 seconds for up to 100,000 records."* | **Accepted** | Eliminates subjective interpretation and gives QA engineers a clear pass/fail metric in NFR-01. |
| **AI-02** | **Requirement Conflict** | Allowing unlimited online renewals (early draft) conflicts with the book reservation queue because a borrower could hold a popular book indefinitely. Limit renewals to **1 time** and block renewal if an active reservation exists. | **Accepted** | Resolves a direct business-rule conflict and prevents starvation of students waiting in the reservation queue. |
| **AI-03** | **Omission (Edge Case)** | Specify an expiration period for reserved books once returned; otherwise, uncollected books remain locked on hold indefinitely. Add a **48-hour pickup window** before passing the hold to the next member in the FIFO queue. | **Accepted** | Closes a critical operational gap in FR-06 and keeps physical inventory circulating smoothly. |
| **AI-04** | **Omission (Inventory)** | Add a workflow for Librarians to mark overdue/unreturned books as **Lost or Damaged** and assess replacement charges rather than letting overdue fines accumulate forever. | **Accepted** | Necessary for accurate catalog reconciliation (FR-09) and closing unresolved loan records. |
| **AI-05** | **Testability & Clarity** | Separate **Book Title (ISBN)** from **Physical Book Copy (Copy ID / Accession Number)** in the requirements so multi-copy availability and individual barcode check-outs are unambiguous. | **Accepted** | Libraries own multiple copies of the same textbook; tracking by Copy ID prevents data corruption during check-out/check-in. |
| **AI-06** | **Scope Creep** | Integrate an AI conversational chatbot for book recommendations and install biometric fingerprint scanners at library entry gates. | **Rejected** | Exceeds the assignment scope, budget, and hardware constraints (CON-01); standard web search and university ID/barcode lookup satisfy all stakeholder needs. |

---

# Software Requirements Specification (SRS)
**Project:** University Library Management System (LMS)  
**Version:** 1.0  
*Note: Structured using IEEE 830 / ISO/IEC/IEEE 29148 principles as educational guidance.*

---

## 1. Introduction, Purpose, Scope, and Definitions

| Item | Description |
| :--- | :--- |
| **1.1 Purpose** | This Software Requirements Specification (SRS) defines the functional, non-functional, and interface requirements for the University Library Management System (LMS). It serves as the primary baseline for system modeling, UI prototyping, development, and acceptance testing. |
| **1.2 Scope** | The LMS is a web-based application that replaces manual paper registers and legacy tracking tools. It automates catalog and copy-level inventory management, student/faculty membership tracking, borrowing, returns, renewals, FIFO reservations, overdue fine calculation, automated notifications, and administrative reporting. |
| **1.3 Definitions & Acronyms** | • **LMS:** Library Management System.<br>• **ISBN:** International Standard Book Number (unique identifier for a book title/edition).<br>• **Copy ID:** Unique barcode/accession number assigned to each physical copy of a book.<br>• **FIFO:** First-In, First-Out queue ordering for book reservations.<br>• **RBAC:** Role-Based Access Control.<br>• **SSO:** Single Sign-On using university credentials. |

---

## 2. Overall System Description and Stakeholder Characteristics

| Aspect / Stakeholder | Characteristics & Operational Role | Key Needs & System Interaction |
| :--- | :--- | :--- |
| **Product Perspective** | Centralized client-server web application accessible via standard desktop and mobile browsers, connecting to university member records and an email/SMS gateway. | Replaces fragmented manual logs with real-time circulation, inventory tracking, and automated alerts. |
| **Student** | Primary end-user with moderate-to-high digital literacy; accesses the system via mobile or laptop browsers. | Fast catalog search, real-time copy availability, online reservations/renewals, loan history, and due/overdue alerts. |
| **Faculty Member** | Academic staff requiring higher borrowing limits and longer loan durations for teaching and research. | Extended loan quotas (e.g., 10 books for 30 days), reservation access, and timely due-date reminders. |
| **Librarian** | Front-desk staff performing high-frequency daily circulation and catalog maintenance tasks. | Rapid barcode/ID check-out and check-in, automatic fine calculation, hold-shelf management, and catalog CRUD operations. |
| **Library Administrator** | Managerial user overseeing library rules, staff permissions, and institutional reporting. | Configurable loan/fine policies, RBAC user management, circulation/overdue analytics, and audit logs. |
| **University IT Support** | Technical staff responsible for hosting, security, backups, and directory integration. | Reliable uptime, automated daily backups, HTTPS/RBAC compliance, and clear system logs. |

---

## 3. Functional Requirements

| Req ID | Requirement Name | Specification ("The system shall...") | Priority |
| :--- | :--- | :--- | :--- |
| **FR-01** | Catalog Search & Filtering | The system shall allow users to search books by title, author, ISBN, subject, or publication year and filter results by availability status. | Must Have |
| **FR-02** | Member Profile & Quota Management | The system shall allow Librarians to register and update Student and Faculty profiles linked to a unique University ID, automatically assigning role-based borrowing quotas and loan periods. | Must Have |
| **FR-03** | Book Check-Out (Issue) | The system shall allow Librarians to issue an available Copy ID to an active member after verifying that the member is within their borrowing limit and has no overdue/fine blocks. | Must Have |
| **FR-04** | Book Check-In (Return) | The system shall allow Librarians to process returned books by Copy ID, immediately updating copy status to *Available* (or *On Hold* if an active reservation queue exists). | Must Have |
| **FR-05** | Automated Overdue Fine Calculation | The system shall automatically calculate overdue fines using `(Return Date - Due Date) × Configured Daily Rate` whenever a book is returned past its due date. | Must Have |
| **FR-06** | Book Reservation Queue | The system shall allow authenticated members to reserve titles with zero available copies in a strict FIFO queue and hold returned copies for 48 hours before expiring the hold. | Should Have |
| **FR-07** | Online Loan Renewal | The system shall allow members to renew an active loan once online, provided the book is not overdue and has zero pending reservations. | Should Have |
| **FR-08** | Automated Notifications | The system shall dispatch automated email/SMS notifications 48 hours before a due date, upon becoming overdue, and when a reserved copy becomes ready for pickup. | Should Have |
| **FR-09** | Catalog & Copy Inventory CRUD | The system shall allow Librarians to add new book titles, register multiple physical Copy IDs per ISBN, update book details, and mark lost/damaged copies as *Inactive*. | Must Have |
| **FR-10** | Policy & Rule Configuration | The system shall provide an administrative panel to configure loan durations, maximum book limits, reservation hold hours, and daily fine rates per user role. | Could Have |
| **FR-11** | Circulation & Overdue Reporting | The system shall generate and export (CSV and PDF) reports for overdue books, fine collections, active loans, and monthly circulation statistics. | Could Have |
| **FR-12** | Transaction Audit Logging | The system shall record immutable audit logs containing user ID, action type, and timestamp for all circulation, inventory deletion, and policy change events. | Should Have |

---

## 4. Non-Functional Requirements

| Req ID | Quality Attribute | Measurable Specification | Priority |
| :--- | :--- | :--- | :--- |
| **NFR-01** | Performance | Catalog search queries and check-out/check-in transactions shall complete in **under 2 seconds** under a concurrent load of 200 users and 100,000 book records. | Must Have |
| **NFR-02** | Security & Privacy | The system shall enforce Role-Based Access Control (RBAC), hash stored credentials with strong cryptography (bcrypt/Argon2), and encrypt all network traffic via HTTPS (TLS 1.2+). | Must Have |
| **NFR-03** | Availability & Backup | The system shall maintain **99.5% uptime** during university library hours (8:00 AM – 8:00 PM) and execute automated daily database backups with a 24-hour Recovery Point Objective (RPO). | Should Have |
| **NFR-04** | Usability | Front-desk Librarians shall complete a standard book check-out or return in **3 or fewer clicks/scans**, and the member portal shall render responsively across desktop and mobile screens. | Should Have |
| **NFR-05** | Maintainability | Loan limits, durations, and fine rates shall be stored in database configuration tables rather than hardcoded in source code so rules can be updated without redeployment. | Could Have |

---

## 5. External Interface Requirements

| Interface Category | Interface Name | Specification & Interaction Details |
| :--- | :--- | :--- |
| **User Interface (UI)** | Member Self-Service Portal | Responsive web interface providing catalog search, real-time copy status badges, personal loan/fine dashboard, and one-click reservation/renewal actions. |
| **User Interface (UI)** | Librarian Circulation Desk UI | High-contrast, keyboard-navigable screen optimized for rapid barcode scanning (auto-submitting fields on carriage return). |
| **Hardware Interface** | USB Barcode Scanners | Standard 1D/2D USB or wireless barcode scanners operating in keyboard-wedge mode to read Copy IDs and university ID cards. |
| **Software / API Interface** | University Identity Directory | Read-only REST/LDAP lookup to verify active student enrollment or faculty employment status and retrieve official university email addresses. |
| **Software / API Interface** | Notification Gateway (SMTP/SMS) | SMTP or REST messaging API used to send scheduled due-date reminders, overdue alerts, and hold-ready notifications. |

---

## 6. System Constraints, Assumptions, and Dependencies

| Category | ID | Description |
| :--- | :--- | :--- |
| **Assumption** | **ASM-01** | All physical book copies are tagged with unique barcode/Copy ID labels prior to system deployment. |
| **Assumption** | **ASM-02** | Every student and faculty member has an active university email address capable of receiving automated system alerts. |
| **Assumption** | **ASM-03** | Library circulation workstations have reliable campus network connectivity and modern web browsers (Chrome, Edge, Firefox). |
| **Constraint** | **CON-01** | **Hardware & Budget Constraint:** The system must operate on existing library desktop computers and standard USB barcode scanners without requiring RFID gates. |
| **Constraint** | **CON-02** | **Scope Constraint:** Online payment gateway integration is excluded from v1.0; overdue fines are calculated by the system and settled at the library desk. |
| **Dependency** | **DEP-01** | **External Directory Dependency:** Member verification and login rely on the availability and schema stability of the university's central student/faculty database. |

---

## 7. Acceptance Criteria and Requirement Priorities

| Priority Tier (MoSCoW) | Requirement IDs | Consolidated Acceptance Criteria |
| :--- | :--- | :--- |
| **Must Have (Core MVP)** | FR-01, FR-02, FR-03, FR-04, FR-05, FR-09, NFR-01, NFR-02 | • Search returns accurate copy availability in < 2 seconds.<br>• Role quotas and overdue blocks are enforced during check-out.<br>• Check-out and return immediately update copy status (`Available` / `Checked Out` / `On Hold`).<br>• Overdue fines compute accurately (`days late × daily rate`).<br>• Duplicate ISBNs register distinct Copy IDs; RBAC and HTTPS are enforced. |
| **Should Have** | FR-06, FR-07, FR-08, FR-12, NFR-03, NFR-04 | • FIFO reservation queue holds returned books for 48 hours and notifies the next user.<br>• Online renewal succeeds once if unreserved and not overdue, and fails otherwise.<br>• Automated alerts trigger at T-48h before due date, upon overdue status, and on hold arrival.<br>• Audit logs record user and timestamp; uptime ≥ 99.5%; loan workflow takes ≤ 3 clicks/scans. |
| **Could Have** | FR-10, FR-11, NFR-05 | • Administrators update fine rates, loan days, and quotas via UI without code changes.<br>• Overdue and circulation reports export cleanly to valid CSV and PDF files in < 5 seconds. |

---

## 8. Requirements Traceability Matrix (RTM)

| Req ID | Requirement Summary | Linked User Story | Measurable Acceptance Criteria / Test Case | Priority |
| :--- | :--- | :--- | :--- | :--- |
| **FR-01** | Catalog Search & Filtering | **US-01** | **TC-01:** Partial/full search by title, author, ISBN, or filter returns matching books with live availability status in < 2s. | Must Have |
| **FR-02** | Member Profile & Quota Management | **US-02** | **TC-02:** Creating Student vs. Faculty profile assigns distinct loan limits (e.g., 4 vs. 10 books) and validates active University ID. | Must Have |
| **FR-03** | Book Check-Out (Issue) | **US-02** | **TC-03:** Issuing an available Copy ID sets status to *Checked Out* and calculates due date; blocked if quota exceeded or fines unpaid. | Must Have |
| **FR-04** | Book Check-In (Return) | **US-03** | **TC-04:** Scanning returned Copy ID updates status to *Available* (or *On Hold* if reservation queue > 0) within 1s. | Must Have |
| **FR-05** | Automated Overdue Fine Calculation | **US-03** | **TC-05:** Returning a book 4 days past due date automatically computes fine equal to `4 × configured daily rate`. | Must Have |
| **FR-06** | Book Reservation Queue | **US-04** | **TC-06:** Reserving a title with 0 available copies places member in FIFO queue and holds returned copy for 48 hours. | Should Have |
| **FR-07** | Online Loan Renewal | **US-05** | **TC-07:** Member renews active loan once online; second renewal attempt or renewal on a reserved/overdue book is rejected. | Should Have |
| **FR-08** | Automated Notifications | **US-04, US-06** | **TC-08:** System sends automated alerts 48h prior to due date, upon becoming overdue, and when a reserved copy arrives. | Should Have |
| **FR-09** | Catalog & Copy Inventory CRUD | **US-07** | **TC-09:** Adding an existing ISBN creates a new Copy ID; marking a copy *Lost/Damaged* removes it from borrowable inventory. | Must Have |
| **FR-10** | Policy & Rule Configuration | **US-08** | **TC-10:** Admin modifies daily fine rate or loan duration in settings; new values apply immediately to subsequent transactions. | Could Have |
| **FR-11** | Circulation & Overdue Reporting | **US-08** | **TC-11:** Admin generates monthly overdue and circulation reports and downloads valid CSV and PDF files in < 5s. | Could Have |
| **FR-12** | Transaction Audit Logging | **US-07, US-08** | **TC-12:** Book check-outs, catalog retirements, and policy updates create immutable timestamped log entries with User ID. | Should Have |s

---

## 5. External Interface Requirements

| Interface Type | Interface Name | Description & Expected Behavior |
| :--- | :--- | :--- |
| **User Interface (UI)** | Member Web Portal | Mobile-responsive web pages for searching books, checking due dates/fines, reserving titles, and renewing loans. |
| **User Interface
