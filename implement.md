# MediFlow — Complete Technical Blueprint

> **India's Digital Operating System for Pharmacies**
> A cloud-based SaaS platform combining POS billing, inventory, CRM, delivery, OCR, AI insights, and marketplace into one ecosystem.

---

## Table of Contents

1. [Project Overview](#1-project-overview)
2. [Problem & Solution](#2-problem--solution)
3. [System Architecture](#3-system-architecture)
4. [Tech Stack Deep Dive](#4-tech-stack-deep-dive)
5. [Module Breakdown](#5-module-breakdown)
   - 5.1 Pharmacy POS & Billing
   - 5.2 Inventory & Batch Management
   - 5.3 Expiry & Stock Alerts
   - 5.4 Customer Mobile App
   - 5.5 Prescription Upload & OCR
   - 5.6 Delivery Management
   - 5.7 Delivery Partner App
   - 5.8 Medical Representative CRM
   - 5.9 Admin Panel
   - 5.10 Analytics Dashboard
6. [Database Design](#6-database-design)
7. [API Design Principles](#7-api-design-principles)
8. [Real-Time Features](#8-real-time-features)
9. [Security & Compliance](#9-security--compliance)
10. [Payment Integration](#10-payment-integration)
11. [AI & OCR Features](#11-ai--ocr-features)
12. [Deployment & DevOps](#12-deployment--devops)
13. [Product Roadmap](#13-product-roadmap)
14. [Revenue Model](#14-revenue-model)
15. [Go-to-Market Strategy](#15-go-to-market-strategy)
16. [Risks & Mitigations](#16-risks--mitigations)
17. [Long-Term Vision](#17-long-term-vision)

---

## 1. Project Overview

**MediFlow** is a multi-tenant cloud SaaS platform built specifically for Indian pharmacies. It replaces fragmented legacy desktop software with a single integrated platform covering every aspect of pharmacy operations — from billing a walk-in customer to delivering medicine to their doorstep.

| Attribute        | Details                                  |
|------------------|------------------------------------------|
| Target Market    | Indian retail pharmacies (standalone + chains) |
| Deployment Model | Multi-tenant cloud SaaS                  |
| Pricing Model    | Monthly subscription + transactional fees |
| Platform         | Web (pharmacy admin), Mobile (customers + delivery agents) |
| Primary Geography | India (Phase 1), expandable to SE Asia  |

---

## 2. Problem & Solution

### The Problem

Indian pharmacies (~1.2 million outlets) face these operational gaps:

| Pain Point | Impact |
|---|---|
| Legacy desktop billing software | No remote access, no cloud backup, high maintenance |
| No online ordering | Loss of customers to PharmEasy, 1mg, Netmeds |
| Weak inventory visibility | Stockouts and overstocking |
| Expiry mismanagement | Direct revenue loss + legal liability |
| Zero customer engagement | No loyalty programs, no reminders |
| No delivery capability | Unable to compete with doorstep delivery apps |
| Manual prescription handling | Errors, delays, no digitization |

### The Solution

MediFlow provides a **single cloud platform** that addresses all of the above:

```
Pharmacy Owner  →  POS + Inventory + GST Billing + Expiry Alerts + CRM
Customer        →  Mobile App + Prescription Upload + Doorstep Delivery + Tracking
Delivery Agent  →  Dedicated App + Route Optimization + OTP Confirmation
Medical Rep     →  CRM + Lead Tracking + Commission Dashboard
Admin/Owner     →  Analytics + Multi-branch + Partner Management
```

---

## 3. System Architecture

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        CLIENT LAYER                             │
│                                                                 │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────┐  │
│  │  Pharmacy    │  │  Customer    │  │  Delivery Agent App  │  │
│  │  Web App     │  │  Mobile App  │  │  (React Native)      │  │
│  │  (React)     │  │ (React Native│  │                      │  │
│  └──────┬───────┘  └──────┬───────┘  └──────────┬───────────┘  │
└─────────┼────────────────┼────────────────────┼───────────────┘
          │                │                    │
          ▼                ▼                    ▼
┌─────────────────────────────────────────────────────────────────┐
│                      API GATEWAY (NestJS)                       │
│              Auth | Rate Limiting | Logging | CORS              │
└──────────────────────────────┬──────────────────────────────────┘
                               │
          ┌────────────────────┼────────────────────┐
          ▼                    ▼                    ▼
┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐
│  Core Services   │  │  Background Jobs │  │  Real-Time Layer │
│  (NestJS Modules)│  │  (BullMQ/Redis)  │  │  (Socket.IO)     │
│                  │  │                  │  │                  │
│  - Auth          │  │  - Expiry Scans  │  │  - Order Updates │
│  - Billing       │  │  - Notifications │  │  - Live Tracking │
│  - Inventory     │  │  - Report Gen    │  │  - Chat          │
│  - Orders        │  │  - OCR Queue     │  │                  │
│  - Delivery      │  │                  │  │                  │
└────────┬─────────┘  └────────┬─────────┘  └──────────────────┘
         │                     │
         ▼                     ▼
┌─────────────────────────────────────────────────────────────────┐
│                        DATA LAYER                               │
│                                                                 │
│   PostgreSQL (primary)   Redis (cache/queue)   AWS S3 (files)  │
│   Multi-tenant schema    Session store         Prescriptions    │
│                          BullMQ queues         Invoices/Images  │
└─────────────────────────────────────────────────────────────────┘
```

### Multi-Tenancy Model

MediFlow uses a **schema-per-tenant** strategy in PostgreSQL:

- Each pharmacy (tenant) gets its own isolated PostgreSQL schema
- A shared `public` schema holds global data (drug catalog, pincode maps)
- Tenant routing happens at the API Gateway via `X-Tenant-ID` header or subdomain

```
postgres/
  ├── public/              ← shared: drug master, pincodes, admin
  ├── tenant_pharmacy_001/ ← isolated: billing, inventory, customers
  ├── tenant_pharmacy_002/
  └── tenant_pharmacy_xxx/
```

---

## 4. Tech Stack Deep Dive

### Frontend — React (Web)

| Technology | Purpose |
|---|---|
| **React 18** | Component-based UI for pharmacy admin dashboard |
| **React Router v6** | Client-side routing |
| **Redux Toolkit** | Global state management (cart, auth, inventory state) |
| **React Query (TanStack)** | Server-state caching, background refetching |
| **Tailwind CSS** | Utility-first styling |
| **Recharts / Chart.js** | Analytics graphs and dashboards |
| **React Hook Form + Zod** | Form handling with schema validation |

### Mobile — React Native

| Technology | Purpose |
|---|---|
| **React Native (Expo or CLI)** | Cross-platform iOS + Android apps |
| **React Navigation** | Stack, tab, drawer navigation |
| **Zustand** | Lightweight state management |
| **Axios** | HTTP client with interceptors |
| **React Native Maps** | Live delivery tracking (Google Maps SDK) |
| **Camera / Image Picker** | Prescription photo upload |
| **Firebase Messaging** | Push notifications (FCM) |

### Backend — Node.js / NestJS

| Technology | Purpose |
|---|---|
| **NestJS** | Modular, opinionated Node.js framework (decorators, DI, guards) |
| **TypeScript** | Strong typing across the entire backend |
| **Passport.js + JWT** | Authentication (access token + refresh token) |
| **TypeORM / Prisma** | ORM for PostgreSQL with migration support |
| **class-validator** | DTO validation using decorators |
| **Swagger / OpenAPI** | Auto-generated API documentation |
| **Helmet + CORS** | Security headers |
| **Winston / Pino** | Structured logging |

### Database & Caching

| Technology | Purpose |
|---|---|
| **PostgreSQL 15+** | Primary relational database (multi-tenant schemas) |
| **Redis 7** | Caching, session store, BullMQ message broker |
| **BullMQ** | Job queues for async tasks (notifications, OCR, reports) |

### Infrastructure & Cloud (AWS)

| Service | Purpose |
|---|---|
| **AWS EC2 / ECS** | Application server hosting |
| **AWS RDS (PostgreSQL)** | Managed database with automated backups |
| **AWS ElastiCache (Redis)** | Managed Redis cluster |
| **AWS S3** | File storage (prescriptions, invoices, product images) |
| **AWS CloudFront** | CDN for static assets and S3 files |
| **AWS SES** | Transactional email delivery |
| **AWS SNS** | SMS notifications |

### Third-Party Integrations

| Service | Purpose |
|---|---|
| **Razorpay** | Payment gateway (UPI, cards, wallets, COD) |
| **Firebase (FCM)** | Push notifications for mobile apps |
| **Google Maps Platform** | Geocoding, distance matrix, live tracking |
| **Twilio / MSG91** | SMS OTP for authentication and delivery confirmation |
| **AWS Textract / Tesseract** | OCR for prescription reading |

---

## 5. Module Breakdown

---

### 5.1 Pharmacy POS & Billing

The core revenue-generating module for pharmacy operators.

**Key Features:**
- Barcode scanner integration (USB/Bluetooth)
- Search medicines by name, generic name, or manufacturer
- Auto-populate price, batch, expiry from inventory
- GST calculation (5%, 12%, 18% slabs) with CGST/SGST/IGST split
- Print thermal receipt or generate PDF invoice
- Multiple payment modes: Cash, UPI, Card, Credit, Insurance
- Bill hold and resume (pause for another customer)
- Return/refund billing with reason tracking
- Day-end summary and cash reconciliation

**Data Flow:**
```
Cashier scans barcode
    → Query inventory for batch + price
    → Add to cart (Redux state)
    → Apply GST rules
    → Accept payment (Razorpay or cash)
    → Generate invoice (PDF via pdfkit/puppeteer)
    → Deduct stock from inventory
    → Save bill to DB
    → Send WhatsApp/SMS receipt to customer (optional)
```

**Key Database Tables:**
```sql
bills           (id, tenant_id, customer_id, total, gst, payment_mode, created_at)
bill_items      (id, bill_id, product_id, batch_id, qty, price, gst_rate)
payments        (id, bill_id, method, amount, reference_id, status)
```

---

### 5.2 Inventory & Batch Management

**Key Features:**
- Add products with HSN code, manufacturer, composition, rack location
- Batch-wise tracking (batch number, expiry date, purchase price, MRP)
- Multiple batches of same medicine coexist (FIFO/FEFO billing)
- Purchase order creation and GRN (Goods Receipt Note)
- Stock adjustment for breakage/theft
- Low stock threshold alerts per product
- Supplier management (credit terms, contact, order history)

**FIFO vs FEFO Logic:**
```
FIFO (First In First Out) → Bill oldest purchased batch first
FEFO (First Expiry First Out) → Bill batch expiring soonest first (recommended for pharma)
```

**Key Database Tables:**
```sql
products        (id, name, generic_name, manufacturer, hsn_code, gst_rate, unit)
batches         (id, product_id, batch_no, expiry_date, purchase_price, mrp, qty_available)
purchase_orders (id, supplier_id, status, total, created_at)
po_items        (id, po_id, product_id, qty_ordered, qty_received, price)
suppliers       (id, name, contact, gst_no, credit_days)
```

---

### 5.3 Expiry & Stock Alerts

**Key Features:**
- Automated daily scan of all batches
- Configurable alert windows: 30 days, 60 days, 90 days before expiry
- Dashboard widget showing near-expiry and expired items
- Push notification + email alerts to pharmacy owner
- Auto-suggestion to create return purchase order for expiring stock
- Low stock alerts when quantity falls below threshold

**Background Job (BullMQ):**
```typescript
// Runs daily at midnight via cron job
@Cron('0 0 * * *')
async scanExpiryAlerts() {
  const tenants = await this.getTenants();
  for (const tenant of tenants) {
    await this.expiryQueue.add('scan', { tenantId: tenant.id });
  }
}
```

---

### 5.4 Customer Mobile App

**Key Features:**
- Register/login via mobile OTP
- Search medicines by name or scan barcode
- Browse categories (OTC, vitamins, devices, cosmetics)
- Upload prescription (photo) for Rx medicines
- Place order from nearest pharmacy
- Live order tracking on map
- Multiple delivery addresses + family profiles
- Loyalty points earn and redeem
- Digital invoices and order history
- Refill reminders for chronic medicines
- Offers, coupons, and wallet

**App Navigation Structure:**
```
Bottom Tabs:
  ├── Home        (search, banners, categories, nearby pharmacies)
  ├── Orders      (active orders with tracking, order history)
  ├── Prescriptions (upload, saved Rx, Rx-linked orders)
  ├── Offers      (coupons, cashback, loyalty points)
  └── Profile     (addresses, family, wallet, settings)
```

---

### 5.5 Prescription Upload & OCR

**Key Features:**
- Customer uploads prescription image (JPG/PNG/PDF)
- Stored securely on AWS S3 with access control
- OCR engine extracts medicine names, dosage, doctor info
- Extracted data mapped to MediFlow drug catalog
- Pharmacist reviews and confirms the parsed order
- Pharmacist can edit OCR output before confirming
- Prescription validity tracking (date, doctor registration number)

**OCR Pipeline:**
```
Customer uploads image
    → Store on S3 (encrypted)
    → Push job to OCR queue (BullMQ)
    → AWS Textract / Tesseract processes image
    → Extract text → NLP parsing for medicine names
    → Fuzzy match against drug master catalog
    → Return structured JSON to pharmacist review screen
    → Pharmacist confirms → order created
```

**OCR Output Schema:**
```json
{
  "doctor": { "name": "Dr. Mehta", "reg_no": "MH12345" },
  "patient": { "name": "Ravi Kumar", "age": 45 },
  "medicines": [
    { "name": "Metformin 500mg", "dosage": "1-0-1", "days": 30, "matched_product_id": "uuid" },
    { "name": "Amlodipine 5mg",  "dosage": "0-0-1", "days": 30, "matched_product_id": "uuid" }
  ]
}
```

---

### 5.6 Delivery Management

**Key Features:**
- Order auto-assigned to nearest available pharmacy
- Pharmacy accepts/rejects order within 3 minutes (else escalate)
- Pharmacy prepares order, assigns to delivery partner
- Real-time order status updates via Socket.IO
- OTP-based delivery confirmation (customer receives OTP)
- Online (Razorpay) and COD payment support
- Delivery zone configuration per pharmacy (radius in km)
- Estimated delivery time calculation using Google Distance Matrix API

**Order State Machine:**
```
PLACED → PHARMACY_ACCEPTED → PREPARING → ASSIGNED_TO_AGENT
       → OUT_FOR_DELIVERY → DELIVERED (OTP confirmed)
       → CANCELLED (at any stage with reason)
```

**Nearest Pharmacy Algorithm:**
```typescript
// Uses PostGIS or Haversine formula
SELECT id, name,
  (6371 * acos(cos(radians(:lat)) * cos(radians(lat))
  * cos(radians(lng) - radians(:lng))
  + sin(radians(:lat)) * sin(radians(lat)))) AS distance
FROM pharmacies
WHERE is_active = true
ORDER BY distance ASC
LIMIT 3;
```

---

### 5.7 Delivery Partner App

**Key Features:**
- Register as delivery agent (document upload, KYC)
- Toggle availability on/off
- Receive order notification with pickup and drop location
- Accept/reject delivery assignment
- Step-by-step navigation via Google Maps
- Mark as Picked Up → Out for Delivery → Delivered
- OTP entry screen for delivery confirmation
- Earnings dashboard (daily, weekly, monthly)
- COD collection recording

**Real-Time Location Tracking:**
```typescript
// Agent app sends location every 5 seconds
socket.emit('agent:location', {
  orderId: 'uuid',
  lat: 19.0760,
  lng: 72.8777,
  timestamp: Date.now()
});

// Server broadcasts to customer's room
io.to(`order:${orderId}`).emit('agent:location:update', locationData);
```

---

### 5.8 Medical Representative CRM

Medical reps are MediFlow's primary sales channel for onboarding pharmacies.

**Key Features:**
- Lead management (prospect pharmacies, contact info, status)
- Demo scheduling and tracking
- Data migration request initiation
- Subscription collection and reporting
- Commission calculation (% of subscription revenue)
- Territory management (city/zone assignment)
- Performance dashboard (leads converted, revenue generated)

**Lead Pipeline:**
```
LEAD_ADDED → DEMO_SCHEDULED → DEMO_DONE → NEGOTIATION
           → ONBOARDED (subscription active)
           → LOST (with reason)
```

---

### 5.9 Admin Panel

Central control panel for MediFlow platform administrators.

**Key Features:**
- Tenant (pharmacy) management — onboard, suspend, configure
- Drug master catalog management (add, edit, map HSN codes)
- Global pricing and plan management
- Medical rep management and territory assignment
- Platform-wide analytics (GMV, active tenants, orders)
- Support ticket management
- Feature flag management per tenant
- Audit logs for all admin actions

---

### 5.10 Analytics Dashboard

**Pharmacy-Level Analytics:**
- Daily/weekly/monthly sales with trend charts
- Top-selling medicines and categories
- Inventory turnover rate
- Near-expiry stock value (financial exposure)
- Customer retention and repeat purchase rate
- Delivery success rate and average delivery time
- GST summary for filing (GSTR-1, GSTR-3B data export)

**Tech Used:**
- Backend aggregation with PostgreSQL window functions and CTEs
- Redis caching for expensive report queries (TTL: 1 hour)
- Recharts on frontend for interactive graphs
- CSV/PDF export for GST reports

---

## 6. Database Design

### Core Entity Relationships

```
pharmacies ──< batches ──< bill_items >── bills >── customers
               products                             │
               │                                   └── addresses
               └── purchase_orders >── po_items
                   │
                   suppliers

orders ──< order_items
orders ──> delivery_agents
orders ──> prescriptions

customers ──< loyalty_points
customers ──< family_members
```

### Key Table Schemas

```sql
-- Multi-tenant root table
CREATE TABLE pharmacies (
  id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name        VARCHAR(200) NOT NULL,
  gst_no      VARCHAR(15),
  license_no  VARCHAR(50),
  address     TEXT,
  lat         DECIMAL(10,8),
  lng         DECIMAL(11,8),
  plan        VARCHAR(20) DEFAULT 'basic',
  is_active   BOOLEAN DEFAULT true,
  created_at  TIMESTAMPTZ DEFAULT NOW()
);

-- Product master (per tenant schema)
CREATE TABLE products (
  id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name            VARCHAR(300) NOT NULL,
  generic_name    VARCHAR(300),
  manufacturer    VARCHAR(200),
  hsn_code        VARCHAR(10),
  gst_rate        DECIMAL(5,2),  -- 5, 12, or 18
  unit            VARCHAR(20),   -- strip, bottle, tube
  requires_rx     BOOLEAN DEFAULT false,
  is_active       BOOLEAN DEFAULT true
);

-- Batch tracking (FEFO)
CREATE TABLE batches (
  id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  product_id      UUID REFERENCES products(id),
  batch_no        VARCHAR(50) NOT NULL,
  expiry_date     DATE NOT NULL,
  manufacture_date DATE,
  purchase_price  DECIMAL(10,2),
  mrp             DECIMAL(10,2),
  qty_available   INTEGER DEFAULT 0,
  qty_reserved    INTEGER DEFAULT 0,
  CONSTRAINT chk_qty CHECK (qty_available >= 0)
);

-- Customer orders
CREATE TABLE orders (
  id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  pharmacy_id     UUID REFERENCES pharmacies(id),
  customer_id     UUID REFERENCES customers(id),
  status          VARCHAR(30) DEFAULT 'placed',
  total_amount    DECIMAL(10,2),
  delivery_charge DECIMAL(10,2) DEFAULT 0,
  payment_method  VARCHAR(20),  -- online | cod
  payment_status  VARCHAR(20) DEFAULT 'pending',
  address_id      UUID REFERENCES addresses(id),
  agent_id        UUID REFERENCES delivery_agents(id),
  otp             VARCHAR(6),
  placed_at       TIMESTAMPTZ DEFAULT NOW(),
  delivered_at    TIMESTAMPTZ
);
```

### Indexing Strategy

```sql
-- Performance-critical indexes
CREATE INDEX idx_batches_expiry   ON batches(expiry_date) WHERE qty_available > 0;
CREATE INDEX idx_orders_status    ON orders(status, pharmacy_id);
CREATE INDEX idx_bills_created    ON bills(created_at DESC);
CREATE INDEX idx_products_name    ON products USING gin(to_tsvector('english', name));
-- Geospatial index for nearest pharmacy lookup
CREATE INDEX idx_pharmacies_geo   ON pharmacies USING gist(point(lng, lat));
```

---

## 7. API Design Principles

### REST API Structure

All APIs follow RESTful conventions with versioning:

```
Base URL:  https://api.mediflow.in/v1

Auth:      POST /auth/send-otp
           POST /auth/verify-otp
           POST /auth/refresh

Pharmacy:  GET    /pharmacy/profile
           PATCH  /pharmacy/profile

Products:  GET    /products?search=paracetamol&page=1&limit=20
           POST   /products
           PATCH  /products/:id
           DELETE /products/:id (soft delete)

Batches:   GET    /products/:id/batches
           POST   /batches
           PATCH  /batches/:id

Bills:     POST   /bills          (create bill)
           GET    /bills/:id      (fetch bill)
           GET    /bills          (list with filters)

Orders:    POST   /orders         (customer places order)
           PATCH  /orders/:id/status
           POST   /orders/:id/confirm-delivery  (OTP entry)

Analytics: GET    /analytics/sales?from=2025-01-01&to=2025-01-31
           GET    /analytics/inventory/near-expiry
           GET    /analytics/gst-summary?month=2025-01
```

### Response Envelope

All API responses follow a consistent envelope:

```json
{
  "success": true,
  "data": { ... },
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 340
  }
}
```

Error responses:
```json
{
  "success": false,
  "error": {
    "code": "INSUFFICIENT_STOCK",
    "message": "Only 5 strips available for Paracetamol 500mg",
    "details": { "product_id": "uuid", "available": 5, "requested": 10 }
  }
}
```

### Authentication Flow

```
1. User enters mobile number
2. POST /auth/send-otp → SMS OTP sent via Twilio/MSG91
3. POST /auth/verify-otp → returns access_token (15min) + refresh_token (7d)
4. All requests include: Authorization: Bearer <access_token>
5. POST /auth/refresh → new access_token using refresh_token
6. Refresh tokens stored in Redis with TTL
```

---

## 8. Real-Time Features

### Socket.IO Architecture

```
Rooms:
  pharmacy:{pharmacyId}   → new order notifications to pharmacy
  order:{orderId}         → status updates to customer + pharmacy
  agent:{agentId}         → delivery assignments to agent

Events (Server → Client):
  order:new               → pharmacy receives new order notification
  order:status            → status change (preparing, assigned, delivered)
  agent:location:update   → live lat/lng of delivery agent
  alert:expiry            → near-expiry alert to pharmacy dashboard
  alert:low-stock         → low stock alert

Events (Client → Server):
  agent:location          → delivery agent sends GPS coordinates (every 5s)
  pharmacy:order:accept   → pharmacy accepts incoming order
  pharmacy:order:reject   → pharmacy rejects with reason
```

### Notification Strategy

| Event | Channel |
|---|---|
| New order placed | Push (FCM) + Socket.IO to pharmacy |
| Order accepted | Push (FCM) to customer |
| Agent assigned | Push + live tracking enabled |
| OTP for delivery | SMS (critical path) |
| Expiry alert | Push + Email + Dashboard |
| Low stock alert | Push + Email |
| Payment success | Push + Email with invoice |
| Refill reminder | Push + WhatsApp (optional) |

---

## 9. Security & Compliance

### Authentication & Authorization

```
Role Hierarchy:
  SUPER_ADMIN    → Full platform access
  PHARMACY_OWNER → Full access to their tenant
  PHARMACIST     → POS, inventory, orders
  CASHIER        → POS billing only
  CUSTOMER       → Customer app features
  DELIVERY_AGENT → Delivery app features
  MEDICAL_REP    → CRM module
```

Guards implemented in NestJS:
```typescript
@UseGuards(JwtAuthGuard, RolesGuard)
@Roles(Role.PHARMACY_OWNER, Role.PHARMACIST)
@Get('/inventory/batches')
async getBatches() { ... }
```

### Data Security

| Concern | Implementation |
|---|---|
| Data isolation | PostgreSQL schema per tenant + row-level guards |
| Passwords | bcrypt (12 rounds) — OTP only, no passwords |
| Tokens | JWT RS256 (asymmetric), short-lived access tokens |
| Secrets | AWS Secrets Manager (never hardcoded) |
| File security | S3 presigned URLs (15-minute expiry for prescriptions) |
| HTTPS | TLS 1.3 enforced, HSTS headers |
| Input sanitization | class-validator DTOs on every endpoint |
| SQL injection | TypeORM parameterized queries only |
| Rate limiting | 60 req/min general, 5 req/min for OTP endpoints |

### Drug & Prescription Compliance

- Schedule H / H1 / X medicines flagged in product master
- Rx-required medicines cannot be added to cart without valid prescription
- Prescriptions stored with audit trail (uploaded at, reviewed by, approved at)
- Pharmacist name and license number recorded on Rx-linked bills
- GST invoices comply with GST Council rules (HSN codes mandatory above ₹50,000)

---

## 10. Payment Integration

### Razorpay Integration

**Online Payment Flow:**
```
Customer places order
    → Backend creates Razorpay Order (server-side)
    → Returns order_id + amount to app
    → App opens Razorpay checkout (UPI/Card/Wallet/NetBanking)
    → Payment completed
    → Razorpay sends webhook to backend
    → Backend verifies webhook signature (HMAC-SHA256)
    → Order marked as PAID
    → Pharmacy notified
```

**Webhook Verification:**
```typescript
const expectedSignature = crypto
  .createHmac('sha256', process.env.RAZORPAY_WEBHOOK_SECRET)
  .update(rawBody)
  .digest('hex');

if (expectedSignature !== receivedSignature) {
  throw new UnauthorizedException('Invalid webhook signature');
}
```

**Supported Payment Methods:**
| Method | Use Case |
|---|---|
| UPI (GPay, PhonePe, Paytm) | Primary for India |
| Debit/Credit Card | Secondary |
| Net Banking | Secondary |
| Razorpay Wallet | Loyalty + convenience |
| COD (Cash on Delivery) | Configurable per pharmacy |

### Wallet & Loyalty System

```
Earn: 1 point per ₹10 spent
Redeem: 100 points = ₹10 discount
Wallet: Top-up via Razorpay, use for checkout
Refunds: Credited to wallet within 24 hours
```

---

## 11. AI & OCR Features

### Prescription OCR Pipeline

```
Tools: AWS Textract (primary) + Tesseract.js (fallback)

Step 1: Image pre-processing
  - Convert to grayscale
  - Deskew and denoise
  - Upscale if low resolution (sharp.js)

Step 2: Text Extraction (AWS Textract)
  - Detect blocks of text
  - Identify tables (for structured prescriptions)
  - Extract key-value pairs

Step 3: NLP Parsing
  - Named Entity Recognition for:
    - Medicine names (fuzzy match against drug catalog)
    - Dosage patterns (1-0-1, twice daily, 500mg)
    - Duration (5 days, 1 month)
    - Doctor name and registration
    - Patient name and age

Step 4: Catalog Matching
  - Levenshtein distance / fuzzy-wuzzy matching
  - Brand name → generic name resolution
  - Strength normalization (500mg → 500 MG)

Step 5: Pharmacist Review
  - Extracted data shown as editable form
  - Confidence score per field
  - Low-confidence fields highlighted in yellow
```

### AI-Powered Analytics Insights

```
Features (Phase 4):
  - Demand forecasting: predict stock needs based on seasonal trends
  - Expiry risk scoring: flag batches likely to expire before sale
  - Customer churn prediction: identify at-risk customers
  - Smart reorder suggestions: auto-generate purchase orders
  - Pricing optimization: flag products priced above market average
```

---

## 12. Deployment & DevOps

### Infrastructure Setup (AWS)

```
Production Environment:

Route 53 (DNS)
    → CloudFront (CDN for web + S3)
    → Application Load Balancer
        → ECS Fargate Cluster
            → API Service (NestJS, 2-4 containers)
            → Socket.IO Service (sticky sessions via ALB)
            → Worker Service (BullMQ consumers)
    → RDS PostgreSQL (Multi-AZ, automated backups)
    → ElastiCache Redis (cluster mode)
    → S3 (private bucket for prescriptions, public bucket for assets)
```

### CI/CD Pipeline (GitHub Actions)

```yaml
# .github/workflows/deploy.yml
on:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - run: npm ci
      - run: npm run test:ci
      - run: npm run lint

  build-and-deploy:
    needs: test
    steps:
      - name: Build Docker image
        run: docker build -t mediflow-api .
      - name: Push to ECR
        run: aws ecr push ...
      - name: Deploy to ECS
        run: aws ecs update-service --force-new-deployment
      - name: Run DB migrations
        run: npm run migration:run
```

### Environment Configuration

```
Environments:
  development   → local Docker Compose (PostgreSQL + Redis local)
  staging       → AWS (scaled down, real integrations sandbox mode)
  production    → AWS (full scale, real Razorpay + SMS)

Secrets Management:
  → AWS Secrets Manager for all credentials
  → Never committed to Git
  → Injected as environment variables at container runtime
```

### Monitoring & Observability

| Tool | Purpose |
|---|---|
| **AWS CloudWatch** | Container logs, metrics, alarms |
| **Sentry** | Error tracking (backend + frontend + mobile) |
| **DataDog / New Relic** | APM, trace visualization |
| **UptimeRobot** | Uptime monitoring with alerts |
| **PgBadger** | PostgreSQL query analysis |

---

## 13. Product Roadmap

### Phase 1 — Core Foundation (Months 1–4)
**Goal:** Get pharmacies billing and managing inventory on MediFlow

| Feature | Priority |
|---|---|
| Pharmacy onboarding and tenant provisioning | P0 |
| POS billing with GST invoices | P0 |
| Barcode scanning support | P0 |
| Product and batch inventory management | P0 |
| Expiry and low-stock alerts | P0 |
| Purchase order management | P1 |
| Basic sales reports | P1 |
| Pharmacy admin web dashboard | P0 |
| Medical rep onboarding module | P1 |

**Success Metric:** 50 pharmacies actively billing on MediFlow

---

### Phase 2 — Customer App (Months 3–6)
**Goal:** Launch customer-facing mobile app with ordering

| Feature | Priority |
|---|---|
| Customer registration (OTP) | P0 |
| Medicine search and browse | P0 |
| Add to cart and checkout | P0 |
| Razorpay payment integration | P0 |
| OTC order fulfillment | P0 |
| Prescription upload (manual review) | P1 |
| Order history and invoices | P1 |
| Push notifications | P1 |
| Loyalty points system | P2 |

**Success Metric:** 500 customer orders per day

---

### Phase 3 — Delivery Platform (Months 5–9)
**Goal:** End-to-end delivery from pharmacy to customer's door

| Feature | Priority |
|---|---|
| Delivery partner app | P0 |
| Order assignment and routing | P0 |
| Live tracking (Google Maps) | P0 |
| OTP delivery confirmation | P0 |
| COD payment collection | P1 |
| Delivery zone configuration | P1 |
| Agent earnings dashboard | P1 |
| Delivery SLA monitoring | P2 |

**Success Metric:** 30-minute average delivery time, 95% success rate

---

### Phase 4 — AI & OCR (Months 8–12)
**Goal:** Smart automation to reduce manual effort

| Feature | Priority |
|---|---|
| OCR prescription reading | P0 |
| Drug name fuzzy matching | P0 |
| AI demand forecasting | P1 |
| Auto purchase order suggestions | P1 |
| Customer churn prediction | P2 |
| Smart expiry risk scoring | P2 |

---

### Phase 5 — Marketplace & Healthcare Expansion (Year 2+)
- **Medicine Marketplace** — Multi-pharmacy comparison and ordering
- **Diagnostics** — Lab test booking and home sample collection
- **Telemedicine** — Doctor consultation integration
- **Insurance** — Health insurance claim processing at pharmacy
- **B2B Procurement** — Wholesale medicine ordering for pharmacies
- **Doctor Module** — E-prescription generation, patient records

---

## 14. Revenue Model

### Subscription Plans

| Plan | Price/Month | Features |
|---|---|---|
| **Starter** | ₹999 | POS billing, basic inventory, 1 user |
| **Growth** | ₹2,499 | All Starter + customer app, delivery, 5 users |
| **Pro** | ₹4,999 | All Growth + OCR, AI insights, multi-branch, unlimited users |
| **Enterprise** | Custom | Chain pharmacies, white-label, API access, dedicated support |

### Additional Revenue Streams

| Stream | Model |
|---|---|
| **Delivery Commission** | 8–12% per delivered order |
| **Featured Listings** | Pharmacies pay to appear first in customer search |
| **Advertisements** | Pharma companies promote brands inside customer app |
| **API Integrations** | Insurance, diagnostics, ERP integrations |
| **Analytics Add-ons** | Advanced reports sold separately |
| **Data Insights (B2B)** | Aggregated anonymized market data for pharma companies |

### Revenue Projections

```
Year 1 Target:
  500 pharmacies × ₹2,000 avg subscription = ₹10L/month ARR
  + delivery commissions
  = ~₹1.5Cr ARR

Year 3 Target:
  10,000 pharmacies × ₹3,000 avg = ₹3Cr/month
  + marketplace GMV commissions
  = ₹40Cr+ ARR
```

---
