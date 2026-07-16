📱 Loan Closet Tracker & Automated API Integration

A cross-platform mobile and web application designed to streamline community inventory tracking, automate borrower communications, and integrate real-time checkout records with external databases.

💡 The Problem & The Solution

The Problem

Many community closets, schools, and non-profits manage shared medical, educational, or recreational equipment using paper logs or basic spreadsheets. This manual tracking often results in:

Lost or overdue inventory due to lack of reminders.

Double-booking because availability logs are not updated in real-time.

Heavy administrative burdens on volunteers keeping track of returns.

The Solution

The Loan Closet Tracker is an interactive, event-driven mobile and desktop web application that digitizes the entire checkout loop. It features a custom interactive dashboard, automated email confirmations, and a serverless API connection that mirrors enterprise-grade syncing to update external systems automatically.

📐 System Architecture & Data Flow

This application is built with a decoupled architecture where the UI, Database, Automation Engine, and Integration layers communicate asynchronously:

┌────────────────────────────────────────────────────────┐
│               AppSheet UI Dashboard                    │
│    (User marks an item as checked out or returned)     │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│               Google Sheets Database                   │
│         (Data updates instantly in the cloud)          │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│            AppSheet Automation Bot Trigger             │
│        (Fires based on specific data conditions)        │
└──────────────────────────┬─────────────────────────────┘
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
┌─────────────────────────┐ ┌────────────────────────────┐
│      Email Task         │ │   Google Apps Script Task  │
│  (Sends dynamic HTML    │ │  (Wakes up backend script  │
│   loan confirmation)    │ │   passing trigger context) │
└─────────────────────────┘ └────────────┬───────────────┘
                                         │
                                         ▼
                            ┌────────────────────────────┐
                            │    Google Apps Script      │
                            │  (Packages data into JSON) │
                            └────────────┬───────────────┘
                                         │
                                         ▼ (HTTP POST Request)
                            ┌────────────────────────────┐
                            │    Mock CRM / Web API      │
                            │ (Simulating external Sync) │
                            └────────────────────────────┘


🛠️ Tech Stack & Key Concepts

Frontend Engine: AppSheet (Low-Code / No-Code App builder)

Database Layer: Google Sheets (Cloud-Relational setup)

Serverless Backend: Google Apps Script (JavaScript runtime)

API Integration: HTTP POST Requests sending JSON payloads

Visual Filters: Data Slices with conditional expressions ([Status] = "Available")

🗄️ Relational Database Schema

The system organizes information across three separate tables connected by relational keys:

1. Equipment (Inventory)

EquipmentID (Text, Key using UNIQUEID())

EquipmentName (Text)

Category (Text)

Condition (Text)

Status (Text: Available, On Loan)

2. Borrowers (Client Directory)

BorrowerID (Text, Key using UNIQUEID())

BorrowerName (Text)

Email (Text)

Phone (Text)

3. Loans (Checkout Registry)

LoanID (Text, Key using UNIQUEID())

EquipmentID (Ref $\rightarrow$ Equipment Table)

BorrowerID (Ref $\rightarrow$ Borrowers Table)

LoanDate (Date)

ReturnDate (Date)

LoanStatus (Text: Active, Returned)

⚡ Integration Spotlight: Google Apps Script

To prove the app can communicate with external CRM programs (like HubSpot or Little Green Light), the system triggers this JavaScript function on checkout to serialize and post raw data:

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

  var url = "[https://example.com/api/loans](https://example.com/api/loans)";

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


🚀 Key Features

📊 Interactive Admin Dashboard: Split-screen multi-view to check live inventory at a single glance.

📧 Automated Communication: Instant confirmation emails containing the checkout receipt sent to borrowers when checking out gear.

🤖 State-Aware Bots: Custom return automation that watches database edits to reset equipment availability back to Available only when marked returned by an administrator.

🔌 Enterprise Integration Ready: Fully expandable API module ready to transition to real database systems.
