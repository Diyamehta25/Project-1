
# Mini Payment Gateway Simulator

A **full-stack payment gateway simulation** built by a team of 5 for the Northern Trust Hackathon.  
It mimics how **real UPI-style payments** work — including async processing, simulated bank failures, live payment status polling, and a two-party refund approval system.

Built using **Flask, SQLite, and Vanilla JavaScript**.

---

# Quick Note Before You Start

After logging in, **the dashboard opens on the Create Payment tab by default**.

The payment form is slightly below the visible area — **scroll down** after login to find:

- Receiver ID field  
- Amount input  
- PIN verification  
- Send Payment button  

---

# Getting Started

## Prerequisites

- Python **3.8 or higher**
- pip

---

## Installation

```bash
git clone https://github.com/Diyamehta25/Project-1.git
cd Project-1
pip install -r requirements.txt
python app.py
```

Then open your browser and go to:

```
http://localhost:5000
```

The **SQLite database is created automatically** on first run and seeded with demo accounts.

---

# Demo Accounts

Use any of the following accounts to test the system.

| User ID | PIN | Name | Starting Balance |
|-------|------|------|----------------|
| USER001 | 1234 | Arjun Sharma | ₹50,000 |
| USER002 | 5678 | Priya Patel | ₹30,000 |
| USER003 | 9999 | Ravi Kumar | ₹75,000 |
| USER004 | 1111 | Sneha Mehta | ₹20,000 |
| USER005 | 0000 | Demo User | ₹1,50,000 |

---

# Features

## Send Money

Users can transfer money by entering:

- Receiver User ID  
- Payment Amount  
- PIN confirmation  

The payment goes through a **simulated banking pipeline**.

Simulation characteristics:

- **3-second processing delay**
- **15% random failure rate**
- Real-time payment status updates

Status progression:

```
CREATED → PROCESSING → SUCCESS
                     ↘ FAILED
```

If a payment fails, a **Retry button automatically fills the previous details**.

---

## Transaction History

Users can view all transactions:

- **Sent payments** appear in **red**
- **Received payments** appear in **green**

Additional capabilities:

- Refund button appears for successful transactions
- Refund available for **10 minutes after payment**

---

## Check Payment Status

Search any payment using its ID.

Format:

```
PAYxxxxxxxxxx
```

This allows debugging or checking transactions initiated by other users.

---

## Analytics Dashboard

Displays global system statistics:

- Total transactions
- Success vs failure rate
- Total payment volume
- Failure reason breakdown chart

---

# Refund System

Refunds require **two-party approval**.

Workflow:

1. Sender requests refund
2. Receiver receives notification
3. Receiver approves or rejects
4. Both confirm using their **PIN**

Rules:

- Only original sender can request refund
- Refund only allowed on **successful payments**
- Must be requested within **10 minutes**
- Only **one refund request per payment**
- Receiver must have sufficient balance

---

# Payment State Machine

```
CREATED → PROCESSING → SUCCESS
                     ↘ FAILED
```

State explanations:

| State | Description |
|------|-------------|
| CREATED | Payment created and stored in database |
| PROCESSING | Simulated bank begins processing |
| SUCCESS | Funds transferred |
| FAILED | Payment rejected |

Failure reasons include:

- INSUFFICIENT_BALANCE  
- BANK_SERVER_TIMEOUT  
- NETWORK_ERROR  
- DAILY_LIMIT_EXCEEDED  
- INVALID_AMOUNT  
- AMOUNT_EXCEEDS_LIMIT  

---

# Refund State Machine

```
none → pending → accepted
               ↘ rejected
```

Refund status updates **in real time without page refresh**.

---

# Database Schema

The application uses **SQLite** with three tables.

Database file:

```
payments.db
```

Created automatically on first run.

---

## users

```
id       TEXT PRIMARY KEY
name     TEXT
pin      TEXT
balance  REAL DEFAULT 10000.0
```

---

## payments

```
payment_id      TEXT PRIMARY KEY
sender_id       TEXT → users.id
receiver_id     TEXT → users.id
amount          REAL
currency        TEXT DEFAULT 'INR'
status          TEXT DEFAULT 'CREATED'
failure_reason  TEXT
refund_status   TEXT DEFAULT 'none'
created_at      TEXT
updated_at      TEXT
```

---

## refund_requests

```
refund_id    TEXT PRIMARY KEY
payment_id   TEXT → payments.payment_id
requester_id TEXT → users.id
receiver_id  TEXT → users.id
amount       REAL
currency     TEXT DEFAULT 'INR'
status       TEXT DEFAULT 'PENDING'
created_at   TEXT
updated_at   TEXT
```

---

# API Reference

## Authentication

| Method | Endpoint | Description |
|------|-----------|-------------|
| POST | `/api/auth` | Verify user credentials |
| GET | `/api/users` | List all users |

---

## Payments

| Method | Endpoint | Description |
|------|-----------|-------------|
| POST | `/api/payment/create` | Create payment |
| GET | `/api/payment/<id>` | Check payment status |
| GET | `/api/payments/user/<id>` | User transaction history |

---

## Refunds

| Method | Endpoint | Description |
|------|-----------|-------------|
| POST | `/api/refund/request` | Request refund |
| GET | `/api/refund/pending/<id>` | Pending approvals |
| GET | `/api/refund/all/<id>` | Refund history |
| POST | `/api/refund/action` | Approve or reject refund |

---

## Analytics

| Method | Endpoint | Description |
|------|-----------|-------------|
| GET | `/api/summary` | Global transaction stats |

---

# Project Structure

```
Project-1/
├── app.py
├── requirements.txt
├── payments.db
├── static/
│   ├── script.js
│   └── styles.css
└── templates/
    └── index.html
```

---

# Known Quirks

- Scroll down after login to see payment form
- 15% payment failure is **intentional**
- Refund window lasts **10 minutes**
- `payments.db` auto-generates on first run
- Do **not commit `payments.db`** to Git

---

# requirements.txt

```
flask>=2.3.0
flask-cors>=4.0.0
```

---

# Authors

Northern Trust Hackathon Team  
Mini Payment Gateway Simulator
