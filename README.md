# Clinic Management System

## 1. System Idea

The **Clinic Management System** is a centralized system that manages the patient's journey from registration to payment.

### Patient Journey

**Patient → Appointment → Medical Encounter → Diagnosis → Prescription/Treatment → Services → Invoice → Payment**

The main goal is to connect **clinical activities** with their related **financial activities** in one system.

---

## 2. Main System Relationships

The database is designed around the patient's journey:

- **Department → Doctor**  
  One department can have many doctors.
- **Patient → Appointment**  
  One patient can have many appointments.
- **Doctor → Appointment**  
  One doctor can have many appointments.
- **Appointment → Encounter**  
  One appointment can result in one medical encounter.
- **Patient → Encounter**  
  One patient can have many medical encounters.
- **Doctor → Encounter**  
  One doctor can have many medical encounters.
- **Encounter → Diagnosis / Vital Signs / Prescription**  
  An encounter contains the patient's clinical information.
- **Prescription → Prescription Items → Medication**  
  A prescription can contain multiple medications.
- **Patient → Invoice**  
  One patient can have multiple invoices.
- **Invoice → Invoice Items → Service**  
  An invoice contains the services provided.
- **Invoice → Payment**  
  One invoice can have multiple payments, supporting partial payments.

---

## 3. Overall System Flow

```text
Patient
   ↓
Appointment
   ↓
Medical Encounter
   ↓
Diagnosis / Vital Signs / Prescription
   ↓
Services
   ↓
Invoice
   ↓
Payment