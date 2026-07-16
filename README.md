# 📱 Loan Closet Tracker & Automated API Integration

A cross-platform mobile and web application designed to streamline community inventory tracking, automate borrower communications, and integrate real-time checkout records with external databases.

## 📷 App Preview

<img width="403" height="682" alt="Screenshot 2026-07-16 at 2 38 44 PM" src="https://github.com/user-attachments/assets/6584d2eb-e775-4e4b-b1cf-1498ebbe71bc" />

## 💡 The Problem & The Solution

### The Problem

Many community closets, schools, and non-profits manage shared medical, educational, or recreational equipment using paper logs or basic spreadsheets. This manual tracking often results in:

- **Lost or Overdue Inventory** — no easy way to trigger automated borrower reminders
- **Double-Booking Errors** — availability logs are not updated dynamically in real-time
- **Volunteer Burnout** — heavy administrative burden from tracking returns manually

### The Solution

The Loan Closet Tracker is an interactive, event-driven mobile and desktop web application that digitizes the entire checkout loop. It features a custom interactive dashboard, automated email confirmations, and a serverless API connection that mirrors enterprise-grade syncing to update external databases automatically.

## 📐 System Architecture & Data Flow

This application is built with a decoupled architecture where the UI, Database, Automation Engine, and Integration layers communicate asynchronously:

```
┌──────────────────────────────────────────────────────────┐
│                  AppSheet UI Dashboard                   │
│      (User marks an item as checked out or returned)     │
└────────────────────────────┬───────────────────────────────┘
                             │
                             ▼
┌──────────────────────────────────────────────────────────┐
│                Google Sheets Database                    │
│           (Data updates instantly in the cloud)           │
└────────────────────────────┬───────────────────────────────┘
                             │
                             ▼
┌──────────────────────────────────────────────────────────┐
│              AppSheet Automation Bot Trigger              │
│          (Fires based on specific data conditions)         │
└────────────────────────────┬───────────────────────────────┘
                             │
              ┌──────────────┴───────────────┐
              ▼                              ▼
┌───────────────────────────┐   ┌────────────────────────────┐
│         Email Task         │   │   Google Apps Script Task  │
│  (Sends dynamic HTML       │   │  (Wakes up backend script  │
│   loan confirmation)       │   │   passing trigger context) │
└───────────────────────────┘   └──────────────┬─────────────┘
                                               │
                                               ▼
                                 ┌────────────────────────────┐
                                 │     Google Apps Script     │
                                 │   (Packages data into JSON) │
                                 └──────────────┬─────────────┘
                                               │
                                               ▼ (HTTP POST Request)
                                 ┌────────────────────────────┐
                                 │      Mock CRM / Web API     │
                                 │  (Simulating external sync) │
                                 └────────────────────────────┘
```

<img width="485" height="566" alt="Screenshot 2026-07-16 at 2 40 10 PM" src="https://github.com/user-attachments/assets/41de823e-22b1-46a9-8025-b94939904914" />

## 🛠️ Tech Stack & Key Concepts

- **Frontend Engine:** AppSheet (Low-Code / No-Code App builder)
- **Database Layer:** Google Sheets (Cloud-relational setup)
- **Serverless Backend:** Google Apps Script (JavaScript runtime)
- **API Integration:** HTTP POST requests sending JSON payloads
- **Visual Filters:** Data Slices with conditional expressions (`[Status] = "Available"`)

## 🗄️ Relational Database Schema

The system organizes information across three separate tables connected by relational keys:

```
  ┌───────────────┐          ┌───────────┐          ┌─────────────┐
  │   Equipment   │◄─────────┤   Loans   ├─────────►│  Borrowers  │
  │ (Status, etc) │          │ (Ref Key) │          │             │
  └───────────────┘          └───────────┘          └─────────────┘
```

### 1. Equipment (Inventory)

- `EquipmentID` (Text, Key using `UNIQUEID()`)
- `EquipmentName` (Text)
- `Category` (Text)
- `Condition` (Text)
- `Status` (Text: Available, On Loan)

### 2. Borrowers (Client Directory)

- `BorrowerID` (Text, Key using `UNIQUEID()`)
- `BorrowerName` (Text)
- `Email` (Text)
- `Phone` (Text)

### 3. Loans (Checkout Registry)

- `LoanID` (Text, Key using `UNIQUEID()`)
- `EquipmentID` (Ref → Equipment table)
- `BorrowerID` (Ref → Borrowers table)
- `LoanDate` (Date)
- `ReturnDate` (Date)
- `LoanStatus` (Text: Active, Returned)

<img width="618" height="764" alt="Screenshot 2026-07-16 at 2 40 59 PM" src="https://github.com/user-attachments/assets/7afcba5e-1b7e-495b-aa25-5dc7732bd9e7" />

## ⚡ Integration Spotlight: Google Apps Script

To prove the app can communicate with external CRM programs (like HubSpot or Little Green Light), the system triggers this JavaScript function on checkout to serialize and post raw data:

```javascript
/**
 * sendLoanToAPI()
 * Packages up sample loan data and transmits it to a mock API endpoint.
 * Demonstrates JSON payload formatting, HTTP POST methods, and error-safe fetching.
 */
function sendLoanToAPI() {
  var payload = {
    equipmentName: "Wheelchair",
    borrowerName: "John Smith",
    loanDate: "2026-07-15"
  };

  var url = "https://example.com/api/loans";

  var options = {
    method: "post",
    contentType: "application/json",
    payload: JSON.stringify(payload),
    muteHttpExceptions: true
  };

  try {
    var response = UrlFetchApp.fetch(url, options);
    Logger.log("POST Request dispatched successfully!");
    Logger.log("Response text: " + response.getContentText());
  } catch (error) {
    Logger.log("API transmission error: " + error.message);
  }
}
```

<img width="1512" height="384" alt="Screenshot 2026-07-16 at 2 42 00 PM" src="https://github.com/user-attachments/assets/4f13a0e2-f238-434d-8d52-75ca7e1d4eda" />

## 🚀 Key Features

### 📧 Automated Communication

Instant confirmation emails containing receipt details and terms of service are dispatched directly to the borrower's inbox immediately upon checkout, saving manual notification time.

<img width="1197" height="581" alt="Screenshot 2026-07-16 at 2 43 07 PM" src="https://github.com/user-attachments/assets/09a2374f-dacc-43bd-9d81-37c39a6a3a3f" />

### 🤖 State-Aware Bots

Custom background workflows watch Google Sheet rows for edits. Once a loan is marked returned, the bot resets the corresponding item's status back to Available automatically.

### 🔌 Enterprise Integration Ready

Features a scalable JSON post architecture allowing direct translation to production APIs or webhooks without disrupting the frontend interface.
