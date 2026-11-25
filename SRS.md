Part A: Stakeholder Analysis, User Stories & AI Review

1. Stakeholder Identification & Needs Table

| Stakeholder | Role / Involvement | Primary Needs & Expectations |
| :--- | :--- | :--- |
| **Student** | Primary end-user who searches, borrows, renews, and reserves books. | Fast catalog search by title/author/ISBN, real-time copy availability, online reservations, borrowing history, and timely due-date/overdue alerts. |
| **Faculty Member** | Academic staff with extended borrowing limits and reference needs. | Higher borrowing quotas, longer loan periods, priority holds on academic titles, and easy access to course-related reference availability. |
| **Librarian** | Front-desk operator managing daily circulation and catalog records. | Quick barcode/ID-based check-out and check-in, effortless book catalog updates (add/edit/retire), fine calculation, and reservation queue handling. |
| **Library Administrator** | System manager overseeing policies, user roles, and reporting. | Configurable loan rules (quotas, loan days, fine rates), role-based access control, overdue/inventory analytics reports, and system audit logs. |
| **University IT Support** | Technical staff maintaining server uptime, backups, and university SSO. | Reliable web deployment, integration with existing university student/faculty ID databases, automated backups, and clear error logging. |

# Part B: Software Requirements Specification (SRS)
**Project:** University Library Management System (LMS)  
**Version:** 1.0 (Draft)  
*Note: Structured using IEEE 830 / ISO/IEC/IEEE 29148 principles as educational guidance.*

---

## 1. Introduction, Purpose, Scope, and Definitions

| Section / Item | Details |
| :--- | :--- |
| **1.1 Purpose** | Define functional and non-functional requirements for the University Library Management System (LMS) to guide system modeling, UI prototyping, development, and QA testing. |
| **1.2 Scope** | Web-based system replacing manual/legacy records to manage book catalogs, physical copies, member accounts, check-outs, returns, renewals, reservations, overdue fines, notifications, and reporting. |
| **1.3 LMS** | Library Management System — the target software application. |
| **1.4 ISBN** | International Standard Book Number — unique identifier (10- or 13-digit) for a book title/edition. |
| **1.5 Copy ID** | Unique barcode or accession ID assigned to each individual physical copy of a book. |
| **1.6 FIFO** | First-In, First-Out — queue ordering where the earliest reservation gets first priority when a book is returned. |
| **1.7 RBAC** | Role-Based Access Control — restricting features/actions based on assigned user roles. |
| **1.8 SSO** | Single Sign-On — authenticating users via existing university credentials/directory. |

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

---

## 5. External Interface Requirements

| Interface Type | Interface Name | Description & Expected Behavior |
| :--- | :--- | :--- |
| **User Interface (UI)** | Member Web Portal | Mobile-responsive web pages for searching books, checking due dates/fines, reserving titles, and renewing loans. |
| **User Interface