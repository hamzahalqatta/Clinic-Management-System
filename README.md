# Clinic Management System

A business-oriented clinic management system designed to organize the main operational and clinical activities of a clinic in one connected system.

The system manages the patient journey from registration and appointment scheduling through the medical encounter, diagnosis, prescription, service delivery, invoicing, and payment.

## Table of Contents

- [1. Business Idea](#1-business-idea)
- [2. Business Problem](#2-business-problem)
- [3. What the System Provides](#3-what-the-system-provides)
- [4. Main Business Scenarios](#4-main-business-scenarios)
- [5. User Journey](#5-user-journey)
- [6. Data Model Overview](#6-data-model-overview)
- [7. Table Relationships](#7-table-relationships)
- [8. Why the Relationships Are Structured This Way](#8-why-the-relationships-are-structured-this-way)
- [9. Business Requirements Covered](#9-business-requirements-covered)
- [10. Business Rules](#10-business-rules)
- [11. Status Flow](#11-status-flow)
- [12. Project Scope](#12-project-scope)
- [13. Design Approach](#13-design-approach)
- [14. Overall System Flow](#14-overall-system-flow)
- [15. Summary](#15-summary)

---

## 1. Business Idea

The main idea of the system is to provide a centralized platform for managing clinic operations and maintaining a connected record of each patient's journey.

Instead of handling patient information, appointments, medical visits, prescriptions, and payments as separate processes, the system connects them into one workflow.

The core business flow is:

**Patient → Appointment → Medical Encounter → Diagnosis / Prescription → Services → Invoice → Payment**

This structure allows the clinic to maintain a consistent relationship between the patient's clinical activity and the corresponding financial activity.

---

## 2. Business Problem

Clinics need to manage several related activities at the same time:

- Maintaining patient information.
- Organizing doctors and departments.
- Scheduling and tracking appointments.
- Recording medical encounters.
- Capturing vital signs and clinical notes.
- Recording diagnoses.
- Managing prescriptions and medications.
- Recording services provided to patients.
- Generating invoices.
- Tracking payments and outstanding balances.
- Managing system users and their roles.

When these activities are managed separately, information can become disconnected and difficult to follow.

This system addresses that problem by keeping the major clinic processes connected through a structured relational database.

---

## 3. What the System Provides

### Patient Management
Stores the patient's basic information and maintains an active/inactive status.

### Doctor & Department Management
Organizes doctors according to their departments and specialties.

### Appointment Management
Allows appointments to be associated with both a patient and a doctor and tracks their status.

Supported appointment statuses include:

- Scheduled
- Confirmed
- Completed
- Cancelled
- No Show

### Medical Encounter Management
Represents the actual medical interaction between a patient and a doctor.

An encounter can be connected to the appointment that led to the visit and contains the patient's chief complaint, clinical notes, and encounter status.

### Clinical Information
The system separates different types of medical information into dedicated records:

- Vital signs
- Diagnoses
- Prescriptions
- Prescription items
- Medications

### Services & Billing
The clinic can maintain its services and prices, add services to an invoice, calculate invoice amounts, apply discounts, and track the invoice status.

### Payment Management
Payments are recorded independently against invoices, allowing the system to support full or partial payment tracking.

### User Management
Application users can have roles and can optionally be associated with a doctor. User accounts also have an active/inactive status.

---

## 4. Main Business Scenarios

### Scenario 1 — Register a Patient

A new patient visits the clinic.

1. The patient's basic information is registered.
2. The patient receives a unique patient record.
3. The patient can later be used in appointments, encounters, invoices, and other clinical records.

**Business result:**  
The clinic has a centralized patient record that can be reused throughout the patient's future visits.

### Scenario 2 — Schedule an Appointment

A patient needs to visit a doctor.

1. The patient is selected.
2. The doctor is selected.
3. The appointment date and time are recorded.
4. The reason for the booking can be stored.
5. The appointment receives a business status such as Scheduled or Confirmed.

**Business result:**  
The clinic can organize upcoming visits and maintain a clear relationship between patients and doctors.

### Scenario 3 — Patient Visit

When the patient arrives for the appointment:

1. The appointment can be completed.
2. A medical encounter is created.
3. The encounter identifies the patient and doctor.
4. The patient's complaint and clinical notes are recorded.
5. Vital signs can be recorded.

**Business result:**  
The scheduled appointment becomes a documented medical encounter.

### Scenario 4 — Diagnosis and Prescription

During the encounter:

1. The doctor records one or more diagnoses.
2. If medication is required, a prescription is created.
3. The prescription contains one or more prescription items.
4. Each prescription item references a medication and stores dosage and usage instructions.

**Business result:**  
Clinical decisions are stored as part of the patient's medical history instead of being separated from the visit.

### Scenario 5 — Billing

After services are provided:

1. An invoice is created for the patient.
2. The invoice can be linked to the medical encounter.
3. Services are added as invoice items.
4. Quantity, unit price, discount, and total amount are recorded.
5. The invoice receives a payment status.

**Business result:**  
The clinic can connect services provided during patient care with their financial records.

### Scenario 6 — Payment

When the patient makes a payment:

1. A payment is recorded against the invoice.
2. The payment amount is stored.
3. The payment method is recorded.
4. The payment date and optional reference number are maintained.
5. The invoice can represent unpaid, partially paid, or paid states.

**Business result:**  
The clinic can track financial transactions and outstanding amounts.

---

## 5. User Journey

The system follows a simple business journey:

```text
Patient Registration
        ↓
Appointment Booking
        ↓
Doctor Visit
        ↓
Medical Encounter
        ↓
Vital Signs
        ↓
Diagnosis
        ↓
Prescription / Treatment
        ↓
Services Provided
        ↓
Invoice
        ↓
Payment
```

Not every visit must use every step.

For example, an encounter may have no prescription, while a financial transaction can be associated with an encounter without requiring every clinical component.

---

## 6. Data Model Overview

The database is divided into several logical business areas.

### Organization

- `DEPARTMENT`
- `DOCTOR`
- `APP_USER`

These tables define the clinic's organizational structure and system access.

### Patient & Scheduling

- `PATIENT`
- `APPOINTMENT`

These tables manage patients and their scheduled interactions with doctors.

### Clinical Management

- `ENCOUNTER`
- `VITAL_SIGN`
- `DIAGNOSIS`
- `PRESCRIPTION`
- `PRESCRIPTION_ITEM`
- `MEDICATION`

These tables represent what happens during the patient's medical care.

### Financial Management

- `SERVICE`
- `INVOICE`
- `INVOICE_ITEM`
- `PAYMENT`

These tables represent services, billing, and financial transactions.

---

## 7. Table Relationships

The relationships are designed around the real business flow rather than treating each table as an isolated object.

### Department → Doctor

One department can contain multiple doctors.

```text
DEPARTMENT
    1
    |
    N
  DOCTOR
```

A doctor belongs to a department through `DEPARTMENT_ID`.

### Patient → Appointment

A patient can have multiple appointments.

```text
PATIENT
   1
   |
   N
APPOINTMENT
```

Each appointment identifies the patient and the assigned doctor.

### Doctor → Appointment

A doctor can have multiple appointments.

```text
DOCTOR
   1
   |
   N
APPOINTMENT
```

This allows the clinic to organize a doctor's schedule.

### Appointment → Encounter

An appointment can result in a medical encounter.

```text
APPOINTMENT
    1
    |
   0..1
 ENCOUNTER
```

This represents the difference between **booking a visit** and **actually documenting the medical visit**.

### Patient & Doctor → Encounter

An encounter identifies both the patient and the doctor involved in the visit.

```text
PATIENT  1 ───── N  ENCOUNTER
DOCTOR   1 ───── N  ENCOUNTER
```

This makes the encounter the central clinical record of a patient's visit.

### Encounter → Clinical Records

One encounter can have related clinical information such as diagnoses and prescriptions.

```text
ENCOUNTER
   ├── DIAGNOSIS
   ├── VITAL_SIGN
   └── PRESCRIPTION
            |
            N
     PRESCRIPTION_ITEM
            |
            N
       MEDICATION
```

Vital signs are limited to one record per encounter in the current design, while diagnoses and prescription records can represent multiple clinical details.

### Invoice → Invoice Items → Services

An invoice contains invoice items, and each item references a service.

```text
INVOICE
   1
   |
   N
INVOICE_ITEM
   N
   |
   1
SERVICE
```

This separates the invoice header from its individual billable services.

### Invoice → Payment

An invoice can have multiple payments.

```text
INVOICE
   1
   |
   N
PAYMENT
```

This structure supports partial payments and multiple payment transactions against the same invoice.

### Patient → Invoice

A patient can have multiple invoices.

```text
PATIENT
   1
   |
   N
INVOICE
```

An invoice may also be connected to the encounter that generated the related financial activity.

### Doctor → App User

A system user can optionally be associated with a doctor.

```text
DOCTOR
   1
   |
  0..1
APP_USER
```

The database also enforces a unique doctor association for application users.

---

## 8. Why the Relationships Are Structured This Way

The model follows the clinic's natural business process.

The **Patient** represents the person receiving care.

The **Appointment** represents a planned visit.

The **Encounter** represents the actual medical interaction.

The clinical tables represent the information produced during that interaction.

The **Invoice** represents the financial record created for services.

The **Payment** represents the actual financial transaction.

This separation prevents different business concepts from being mixed into a single record while keeping them connected through relationships.

---

## 9. Business Requirements Covered

| Requirement | Supported By |
|---|---|
| Patient registration | `PATIENT` |
| Patient status management | `PATIENT.ACTIVE` |
| Department management | `DEPARTMENT` |
| Doctor management | `DOCTOR` |
| Doctor specialization | `DOCTOR.SPECIALTY` |
| Appointment scheduling | `APPOINTMENT` |
| Appointment status tracking | `APPOINTMENT.STATUS` |
| Medical visit recording | `ENCOUNTER` |
| Chief complaint & notes | `ENCOUNTER` |
| Vital signs | `VITAL_SIGN` |
| Diagnosis recording | `DIAGNOSIS` |
| Prescription management | `PRESCRIPTION` |
| Medication management | `MEDICATION` |
| Prescription instructions | `PRESCRIPTION_ITEM` |
| Clinic service management | `SERVICE` |
| Invoice generation | `INVOICE` |
| Invoice line items | `INVOICE_ITEM` |
| Payment tracking | `PAYMENT` |
| Partial payment support | `INVOICE` + `PAYMENT` |
| Application users | `APP_USER` |
| User roles | `APP_USER.USER_ROLE` |
| Active/inactive accounts | `APP_USER.ACTIVE` |

---

## 10. Business Rules

The database includes rules that protect the consistency of the business data.

Examples include:

- A doctor must belong to a department.
- An appointment must reference an existing patient and doctor.
- An encounter must reference an existing patient and doctor.
- An encounter can be linked to an appointment.
- An encounter can have only one vital-sign record.
- Diagnoses belong to encounters.
- Prescriptions belong to encounters.
- Prescription items reference valid medications.
- Invoice items reference valid services.
- Payments must belong to an existing invoice.
- Payment amounts must be greater than zero.
- Service prices cannot be negative.
- Invoice amounts and discounts cannot be negative.
- Appointment, encounter, invoice, and payment statuses are restricted to defined business values.
- Usernames are unique.
- Invoice numbers are unique.

These rules help keep the system's data aligned with the intended business process.

---

## 11. Status Flow

### Appointment

```text
SCHEDULED
    ↓
CONFIRMED
    ↓
COMPLETED
```

Alternative outcomes:

```text
SCHEDULED / CONFIRMED
       ├── CANCELLED
       └── NO_SHOW
```

### Encounter

```text
OPEN
 ↓
COMPLETED
```

An encounter may also be cancelled.

### Invoice

```text
UNPAID
  ↓
PARTIAL
  ↓
PAID
```

An invoice can also be cancelled when appropriate.

---

## 12. Project Scope

### Included

- Patient records
- Department and doctor management
- Appointment management
- Medical encounters
- Vital signs
- Diagnoses
- Prescriptions
- Medication catalog
- Clinic services
- Invoicing
- Invoice items
- Payment records
- Application users and roles

### Not Represented in the Current Database

The current schema does not define dedicated structures for areas such as:

- Laboratory test management
- Radiology
- Pharmacy inventory
- Insurance claims
- Notifications
- Patient portal
- Doctor availability calendars
- Appointment reminders
- Detailed audit logging

These can be considered future extensions rather than part of the current core scope.

---

## 13. Design Approach

The system uses a relational structure where each major business concept has its own entity.

The design separates:

- Master data from transactional data.
- Appointment planning from actual medical encounters.
- Clinical information from financial information.
- Invoice headers from invoice line items.
- Prescription headers from medication details.
- Users from doctors while still allowing an optional relationship between them.

This approach makes the data easier to maintain and allows the system to represent the clinic's workflow without storing unrelated information in the same table.

---

## 14. Overall System Flow

```text
                    ┌─────────────┐
                    │   Patient   │
                    └──────┬──────┘
                           │
                           ▼
                    ┌─────────────┐
                    │ Appointment │
                    └──────┬──────┘
                           │
                           ▼
                    ┌─────────────┐
                    │  Encounter  │
                    └──────┬──────┘
                           │
             ┌─────────────┼─────────────┐
             ▼             ▼             ▼
        Vital Signs    Diagnosis    Prescription
                                          │
                                          ▼
                                     Medications
                           │
                           ▼
                       Services
                           │
                           ▼
                        Invoice
                           │
                           ▼
                        Payment
```

The overall purpose is to maintain one connected business flow from the patient's initial interaction with the clinic through clinical care and financial settlement.

---

## 15. Summary

The Clinic Management System is centered around the patient's journey.

The database connects organizational data, patient scheduling, clinical documentation, and financial transactions into one consistent model.

Its central business concept can be summarized as:

> **Manage the patient journey, record the care provided, and connect the resulting services with their financial transactions.**

The design provides a foundation for a clinic system that can be extended later with additional operational and clinical modules while keeping the core patient, clinical, and financial processes clearly separated.
