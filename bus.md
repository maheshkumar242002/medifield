# Education Business OS — Complete Technical Blueprint

> **The Operating System for Education & Training Businesses in India**
> A modular, multi-tenant SaaS platform where every institute enables only the modules it needs — from student management and attendance to AI-powered assessments, CRM, and analytics.

---

## Table of Contents

1. [Project Overview](#1-project-overview)
2. [Problem & Solution](#2-problem--solution)
3. [System Architecture](#3-system-architecture)
4. [Tech Stack Deep Dive](#4-tech-stack-deep-dive)
5. [Module Breakdown](#5-module-breakdown)
   - 5.1 Authentication & Roles
   - 5.2 Student Management
   - 5.3 Attendance
   - 5.4 Timetable & Batches
   - 5.5 Fee Management & Billing
   - 5.6 Communication
   - 5.7 Documents & Storage
   - 5.8 Assessment Engine
   - 5.9 Progress Tracking
   - 5.10 Certificates
   - 5.11 Report Cards
   - 5.12 Tournament Management
   - 5.13 Compliance
   - 5.14 Admissions CRM
   - 5.15 Marketing
   - 5.16 AI Assistant Engine
   - 5.17 Analytics Dashboard
6. [User Portals](#6-user-portals)
7. [Database Design](#7-database-design)
8. [API Design Principles](#8-api-design-principles)
9. [Real-Time Features](#9-real-time-features)
10. [Security & Compliance](#10-security--compliance)
11. [Payment Integration](#11-payment-integration)
12. [AI & Automation Features](#12-ai--automation-features)
13. [Deployment & DevOps](#13-deployment--devops)
14. [Product Roadmap](#14-product-roadmap)
15. [Revenue Model](#15-revenue-model)
16. [Go-to-Market Strategy](#16-go-to-market-strategy)
17. [Risks & Mitigations](#17-risks--mitigations)
18. [Long-Term Vision](#18-long-term-vision)

---

## 1. Project Overview

**Education Business OS** is a multi-tenant, modular SaaS platform built for India's fragmented education and training industry. It replaces manual registers, WhatsApp groups, Excel sheets, and disconnected tools with a single intelligent platform tailored to each institute's type and size.

| Attribute | Details |
|---|---|
| Target Market | Indian education & training institutes (small to mid-size) |
| Deployment Model | Multi-tenant cloud SaaS |
| Pricing Model | Monthly subscription (module-based) |
| Platform | Web (admin, teacher), Mobile (parent, student) |
| Primary Geography | India (Phase 1), expandable to SE Asia & Middle East |
| Key Differentiator | Modular — each institute activates only what it needs |

### Target Institute Types

| Type | Key Needs |
|---|---|
| Tuition / Coaching Centres | Attendance, fees, timetable, assessments |
| Dance Academies | Batch scheduling, progress tracking, recitals |
| Music Schools | Instrument-wise batches, certificates, recitals |
| Chess Academies | Tournaments, ratings, progress tracking |
| Abacus Centres | Level-based assessments, certificates, franchise support |
| Spoken English Institutes | Speaking assessments, progress reports |
| Computer Training Centres | Course completion, certificates, job placement tracking |
| Driving Schools | Lesson scheduling, compliance documents, license tracking |
| Yoga / Fitness Academies | Membership management, class scheduling, attendance |
| Play Schools | Parent communication, daily diary, portfolio |

---

## 2. Problem & Solution

### The Problem

India has millions of small education businesses — most run entirely on paper, WhatsApp, or basic Excel:

| Pain Point | Impact |
|---|---|
| Manual attendance in registers | Lost data, no history, no analytics |
| Fee collection via cash/WhatsApp | No receipts, poor tracking, delayed reminders |
| Timetable shared as images on WhatsApp | Constant confusion, no conflict detection |
| No parent communication system | Parents feel disconnected from progress |
| Report cards made manually in Word/Excel | Hours of effort, inconsistent format |
| Zero visibility into business health | No analytics, no revenue tracking |
| No CRM for admissions | Leads lost in notebooks and WhatsApp chats |
| Certificates printed manually | Inconsistent design, time-consuming |
| No online payment collection | Cash dependency, reconciliation nightmares |
| Each institute type needs different features | Generic software doesn't fit niche needs |

### The Solution

Education Business OS provides **one platform, configurable for every institute type**:

```
Owner/Admin  →  Dashboard + Fee Mgmt + Analytics + CRM + Staff Management
Teacher      →  Attendance + Timetable + Assessments + Report Cards
Parent       →  Fee Payments + Attendance View + Communication + Progress
Student      →  Timetable + Assignments + Certificates + Progress
```

The modular engine means:
- A **chess academy** activates Tournament module but not Driving Compliance
- A **driving school** activates Compliance + License Tracking but not Certificates
- A **tuition centre** activates Assessments + Report Cards but not Tournament

---

## 3. System Architecture

### High-Level Architecture

```
┌──────────────────────────────────────────────────────────────────────┐
│                          CLIENT LAYER                                │
│                                                                      │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌───────────┐  │
│  │  Owner/Admin│  │   Teacher   │  │  Parent App │  │ Student   │  │
│  │  Web App    │  │  Web App    │  │(React Native│  │   App     │  │
│  │  (React)    │  │  (React)    │  │    / PWA)   │  │(React     │  │
│  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘  │ Native)  │  │
└─────────┼────────────────┼────────────────┼──────────┴─────┬─────┘
          │                │                │                │
          ▼                ▼                ▼                ▼
┌──────────────────────────────────────────────────────────────────────┐
│                      API GATEWAY (NestJS)                            │
│         Auth | Rate Limiting | Tenant Routing | Logging | CORS       │
└───────────────────────────────┬──────────────────────────────────────┘
                                │
          ┌─────────────────────┼──────────────────────┐
          ▼                     ▼                      ▼
┌──────────────────┐  ┌──────────────────┐  ┌──────────────────────┐
│  Core Services   │  │ Background Jobs  │  │  Real-Time Layer     │
│ (NestJS Modules) │  │  (BullMQ/Redis)  │  │  (Socket.IO)         │
│                  │  │                  │  │                      │
│  - Auth          │  │  - Fee reminders │  │  - Live notifications│
│  - Students      │  │  - Report gen    │  │  - Attendance alerts │
│  - Attendance    │  │  - Cert gen      │  │  - Chat              │
│  - Fees          │  │  - AI jobs       │  │  - Live class        │
│  - Timetable     │  │  - Email/SMS     │  │                      │
│  - Assessments   │  │  - Bulk exports  │  │                      │
│  - CRM           │  │                  │  │                      │
└────────┬─────────┘  └────────┬─────────┘  └──────────────────────┘
         │                     │
         ▼                     ▼
┌──────────────────────────────────────────────────────────────────────┐
│                           DATA LAYER                                 │
│                                                                      │
│  PostgreSQL (primary)    Redis (cache/queue)    AWS S3 (files)       │
│  Multi-tenant schemas    Session store          Documents            │
│                          BullMQ queues          Certificates         │
│                                                 Profile photos       │
└──────────────────────────────────────────────────────────────────────┘
```

### Multi-Tenancy Model

Each institute (tenant) gets its own isolated PostgreSQL schema. A shared `public` schema holds global reference data.

```
postgres/
  ├── public/                  ← shared: plans, countries, institute types
  ├── tenant_institute_001/    ← isolated: students, fees, attendance, staff
  ├── tenant_institute_002/
  └── tenant_institute_xxx/
```

Tenant routing via subdomain or `X-Tenant-ID` header:
```
brightminds.edubizos.in   → resolves to tenant_institute_001
starsdance.edubizos.in    → resolves to tenant_institute_002
```

### Module Activation per Institute Type

Each tenant has a `modules` config stored in the `public.tenants` table. The API gateway checks this before routing to any module endpoint.

```json
{
  "tenantId": "uuid",
  "instituteType": "chess_academy",
  "activeModules": [
    "students", "attendance", "fees", "timetable",
    "assessments", "tournament", "certificates", "analytics"
  ],
  "disabledModules": ["driving_compliance", "report_cards", "marketing"]
}
```

---

## 4. Tech Stack Deep Dive

### Frontend — React (Web Dashboard)

| Technology | Purpose |
|---|---|
| **React 18** | Owner and teacher web dashboards |
| **React Router v6** | Client-side routing with role-based guards |
| **Redux Toolkit** | Global state (auth, active tenant, module config) |
| **React Query (TanStack)** | Server-state caching, background sync |
| **Tailwind CSS** | Utility-first responsive styling |
| **shadcn/ui** | Accessible, composable UI components |
| **Recharts / Chart.js** | Analytics charts, attendance graphs, fee trends |
| **React Hook Form + Zod** | Form handling with strict schema validation |
| **FullCalendar** | Timetable and batch scheduling UI |
| **React PDF / pdfmake** | Certificate and report card PDF generation |

### Mobile — React Native (Parent & Student Apps)

| Technology | Purpose |
|---|---|
| **React Native (Expo)** | Cross-platform iOS + Android apps |
| **React Navigation** | Stack, tab, bottom drawer navigation |
| **Zustand** | Lightweight client state management |
| **Axios** | HTTP client with JWT interceptors |
| **Firebase Messaging (FCM)** | Push notifications for fee reminders, alerts |
| **Expo Camera / Image Picker** | Document upload, profile photos |
| **React Native Calendars** | Attendance and timetable views for parents/students |

### Backend — Node.js / NestJS

| Technology | Purpose |
|---|---|
| **NestJS** | Modular framework — each feature is an isolated NestJS module |
| **TypeScript** | End-to-end type safety |
| **Passport.js + JWT** | Authentication — OTP login + role-based guards |
| **Prisma ORM** | Type-safe DB access with schema migrations |
| **class-validator** | DTO validation on every request |
| **Swagger / OpenAPI** | Auto-generated interactive API docs |
| **Helmet + CORS** | Security hardening |
| **Winston** | Structured JSON logging |
| **node-cron / NestJS Schedule** | Cron jobs for fee reminders, report generation |

### Database & Caching

| Technology | Purpose |
|---|---|
| **PostgreSQL 15+** | Primary relational database (schema-per-tenant) |
| **Redis 7** | Caching, session store, rate limiting counters |
| **BullMQ** | Async job queues (certificate generation, bulk SMS, AI tasks) |

### Infrastructure & Cloud (AWS)

| Service | Purpose |
|---|---|
| **AWS ECS Fargate** | Containerized NestJS API services |
| **AWS RDS (PostgreSQL)** | Managed DB with Multi-AZ and daily backups |
| **AWS ElastiCache (Redis)** | Managed Redis cluster |
| **AWS S3** | File storage — certificates, report cards, documents, photos |
| **AWS CloudFront** | CDN for static web assets and S3 public files |
| **AWS SES** | Transactional emails (fee receipts, report cards) |
| **AWS Lambda** | Lightweight event-driven tasks (webhook handlers) |

### Third-Party Integrations

| Service | Purpose |
|---|---|
| **Razorpay** | Fee collection (UPI, cards, net banking, EMI) |
| **Firebase (FCM)** | Push notifications on parent/student mobile apps |
| **Twilio / MSG91** | SMS OTP, fee reminders, attendance alerts |
| **WhatsApp Business API** | Rich notifications — share report cards, fee receipts |
| **Google Calendar API** | Two-way timetable sync for teachers |
| **OpenAI API / Gemini** | AI — question generation, report comments, lesson plans |

---

## 5. Module Breakdown

---

### 5.1 Authentication & Roles

**Key Features:**
- OTP-based login via mobile number (no passwords)
- Role-based access control (RBAC) with 4 portal types
- Single institute owner can manage multiple branches
- Staff can be assigned partial roles (e.g., fee-only cashier)
- Session management with access token (15 min) + refresh token (7 days)
- Audit log of every login, role change, and sensitive action

**Role Hierarchy:**
```
SUPER_ADMIN      → Platform-level access (MediFlow internal team)
INSTITUTE_OWNER  → Full access to their tenant + all branches
BRANCH_MANAGER   → Full access to a specific branch
TEACHER          → Attendance, timetable, assessments, their batches only
ACCOUNTANT       → Fee management, receipts, reports only
FRONT_DESK       → Admissions CRM, student registration only
PARENT           → Their child's data only (read + pay fees)
STUDENT          → Own timetable, assignments, certificates (read only)
```

**NestJS Guard Example:**
```typescript
@UseGuards(JwtAuthGuard, RolesGuard, TenantGuard)
@Roles(Role.INSTITUTE_OWNER, Role.BRANCH_MANAGER)
@Get('/analytics/revenue')
async getRevenueReport() { ... }
```

---

### 5.2 Student Management

The central registry — every other module links back to a student record.

**Key Features:**
- Student profile: name, photo, DOB, parent info, emergency contact
- Enrollment to batches with start date, fees, and discount
- Student ID generation (auto or custom format per institute)
- Document storage (birth certificate, ID proof, photos)
- Sibling linking (multiple children under one parent account)
- Student status tracking: active, on-leave, graduated, dropped
- Transfer history (batch changes, branch transfers)
- Custom fields per institute type (e.g., chess rating, instrument, license number)

**Key Database Tables:**
```sql
students        (id, tenant_id, name, dob, photo_url, status, enrolled_at)
parents         (id, student_id, name, phone, email, relation)
enrollments     (id, student_id, batch_id, start_date, fee_plan_id, discount)
student_docs    (id, student_id, doc_type, file_url, uploaded_at)
```

---

### 5.3 Attendance

**Key Features:**
- Mark attendance per batch per session (present / absent / late / leave)
- Bulk mark (mark all present, then unmark absentees)
- QR code-based or biometric attendance (optional integration)
- Real-time push notification to parent when child is marked absent
- Monthly attendance summary per student (percentage, streak)
- Teacher can add remarks per session
- Holiday calendar integration (auto-skip attendance on holidays)
- Attendance export to Excel/PDF for records

**Attendance Flow:**
```
Teacher opens batch for today's session
    → All enrolled students listed
    → Mark each: Present / Absent / Late / Leave
    → Submit attendance
    → BullMQ job triggered:
        → Notify parents of absent students (SMS + Push)
        → Update student attendance percentage
        → Flag if attendance < configurable threshold (e.g., 75%)
```

**Key Database Tables:**
```sql
sessions        (id, batch_id, date, topic, teacher_id, notes)
attendance      (id, session_id, student_id, status, remark, marked_at)
holidays        (id, tenant_id, date, name, applies_to_batches)
```

---

### 5.4 Timetable & Batches

**Key Features:**
- Create batches with name, subject/course, teacher, capacity, room/slot
- Weekly recurring schedule per batch (Mon/Wed/Fri 4–5 PM)
- One-time session overrides (reschedule, cancel, add extra class)
- Conflict detection — alerts if teacher or room double-booked
- Student-facing timetable view (upcoming sessions, next class countdown)
- Teacher calendar sync (Google Calendar two-way)
- Substitute teacher assignment for a session
- Batch capacity tracking (seats filled vs available)
- Holiday-aware scheduling (auto-skip or reschedule)

**Conflict Detection Logic:**
```typescript
// Before creating/updating a schedule slot
const conflict = await this.db.scheduleSlots.findFirst({
  where: {
    teacherId: dto.teacherId,
    dayOfWeek: dto.dayOfWeek,
    AND: [
      { startTime: { lt: dto.endTime } },
      { endTime:   { gt: dto.startTime } }
    ]
  }
});
if (conflict) throw new ConflictException('Teacher already scheduled in this slot');
```

**Key Database Tables:**
```sql
batches         (id, tenant_id, name, subject, teacher_id, capacity, room, status)
schedule_slots  (id, batch_id, day_of_week, start_time, end_time, recurrence)
session_overrides (id, batch_id, original_date, new_date, reason, substitute_teacher_id)
```

---

### 5.5 Fee Management & Billing

**Key Features:**
- Flexible fee plans: monthly, quarterly, annually, per-course
- Sibling discounts, scholarship discounts, custom discounts per student
- Auto-generate fee invoices on due date (BullMQ cron)
- Payment collection: online (Razorpay) and cash/cheque (manual entry)
- Automated fee reminders: 5 days before due, on due date, 3 days overdue
- Partial payment recording with outstanding balance tracking
- Late fee penalty rules (flat or percentage per day)
- Fee receipt generation (PDF) sent via WhatsApp + email
- Day-book report (daily collections summary)
- Outstanding dues report with age analysis
- GST invoicing (if institute is GST registered)

**Fee Reminder Pipeline (BullMQ):**
```typescript
// Cron: runs daily at 8 AM
@Cron('0 8 * * *')
async triggerFeeReminders() {
  const dueSoon    = await this.getFeesdueSoon(5);     // 5 days before
  const dueToday   = await this.getFeesDueToday();
  const overdue    = await this.getOverdueFees(3);     // 3 days overdue

  for (const fee of [...dueSoon, ...dueToday, ...overdue]) {
    await this.reminderQueue.add('send', {
      feeId: fee.id, type: fee.type
    });
  }
}
```

**Key Database Tables:**
```sql
fee_plans       (id, tenant_id, name, amount, frequency, due_day, late_fee_rule)
fee_invoices    (id, student_id, fee_plan_id, amount, due_date, status, generated_at)
payments        (id, invoice_id, amount, method, reference, paid_at, receipt_url)
discounts       (id, student_id, type, value, reason, valid_from, valid_to)
```

---

### 5.6 Communication

**Key Features:**
- Broadcast announcements to all parents / specific batch / individual
- Channels: in-app, push notification, SMS, WhatsApp, email
- Teacher-to-parent direct messaging
- Institute notice board (pinned announcements)
- Daily diary for play schools (photos, activities, mood log per child)
- Homework / assignment posting by teacher
- Emergency alerts with high-priority delivery
- Message read receipts and delivery status
- Scheduled messages (announce holiday 3 days in advance)

**Channel Priority:**
```
EMERGENCY    → SMS + Push + WhatsApp simultaneously
IMPORTANT    → Push + WhatsApp
GENERAL      → In-app + Push
INFO         → In-app only
```

---

### 5.7 Documents & Storage

**Key Features:**
- Upload and store student documents (ID proof, birth certificate, photos)
- Institute document templates (admission forms, consent letters)
- Auto-generated documents: fee receipts, certificates, report cards (S3)
- Access control: parent can only access their child's documents
- Expiry tracking for time-bound documents (driving licence, permits)
- Bulk download (export all certificates for a batch as ZIP)

**S3 Folder Structure:**
```
s3://edubizos-files/
  └── tenants/
        └── {tenantId}/
              ├── students/{studentId}/docs/
              ├── certificates/{batchId}/
              ├── report-cards/{term}/
              └── institute/templates/
```

---

### 5.8 Assessment Engine

**Key Features:**
- Create assessments: MCQ, subjective, oral, practical, graded performance
- Assign to batch or individual students
- Set marks, weightage, passing criteria
- Online exam mode (time-bound, auto-submit)
- Teacher manually enters marks for offline exams
- AI-assisted question generation from topic/chapter
- Automated result calculation and grade assignment
- Configurable grading scales (A/B/C/D, 1–10, percentage, custom)
- Result notifications to parents

**Assessment Types by Institute:**
| Institute Type | Assessment Style |
|---|---|
| Tuition/Coaching | MCQ + Subjective + Marks |
| Chess Academy | Tournament results + ELO rating |
| Music School | Performance grading + examiner remarks |
| Dance Academy | Routine scores + judge feedback |
| Abacus Centre | Speed test scores + level pass/fail |
| Driving School | Theory test + practical evaluation |

---

### 5.9 Progress Tracking

**Key Features:**
- Visual progress timeline per student (month-wise)
- Skill matrix — map student ability against defined skill levels
- Subject/module-wise progress (e.g., per chess opening, per music raga)
- Attendance trend + assessment trend combined view
- Teacher adds progress remarks per student per period
- Parent-facing progress summary card (simplified view)
- Milestone tracking (e.g., passed Level 3 Abacus, completed Basic Chess)
- Progress comparison across batch (anonymized)

---

### 5.10 Certificates

**Key Features:**
- Design certificate templates (drag-drop builder or upload custom design)
- Auto-fill student name, course, date, achievement from DB
- Bulk certificate generation for entire batch (BullMQ job → PDF → S3)
- Digital certificates with QR code for verification
- Share via WhatsApp, email, or download link
- Certificate types: completion, achievement, participation, merit
- Track which students have received which certificates

**Certificate Generation Flow:**
```
Teacher triggers "Generate Certificates" for a batch
    → BullMQ job queued
    → For each student: fetch name, course, date, signature
    → Merge into PDF template (pdfmake / puppeteer)
    → Upload to S3
    → Send download link to parent via WhatsApp + push
    → Mark certificate as issued in DB
```

---

### 5.11 Report Cards

**Key Features:**
- Configurable report card templates per institute type
- Auto-populate from assessment scores, attendance, teacher remarks
- AI-generated personalized comments per student
- Multi-term support (monthly, quarterly, semester, annual)
- Owner/principal approval workflow before publishing
- Published report cards visible to parents in their app
- Share as PDF via WhatsApp + email
- Custom sections: co-curricular activities, behaviour, attendance summary

**Report Card Generation Flow:**
```
Owner finalizes term assessments
    → Trigger "Generate Report Cards" for a batch
    → System pulls: marks, attendance %, teacher remarks
    → AI generates personalized comment per student
    → Owner/principal reviews and approves
    → BullMQ job: generate PDF per student → upload S3
    → Notify parents: report card is ready
    → Parent downloads from app
```

---

### 5.12 Tournament Management

*Active for: Chess Academies, Sports institutes, Abacus Centres*

**Key Features:**
- Create tournaments: name, format (round-robin, knockout, Swiss), date
- Register participants (students or teams)
- Auto-generate draw/fixtures
- Record match results round by round
- Live standings/leaderboard update
- Rating updates post-tournament (Elo for chess)
- Certificate generation for winners/participants
- Tournament history per student

---

### 5.13 Compliance

*Active for: Driving Schools, Play Schools, regulated institutes*

**Key Features:**
- Required document checklist per student (track completion)
- Regulatory deadline tracking (renewal dates for licences, permits)
- Audit-ready document storage with timestamps
- Alerts for upcoming compliance deadlines
- Digital consent form collection from parents
- Driving school specific: track theory hours, practical hours, road test status

---

### 5.14 Admissions CRM

**Key Features:**
- Capture leads from website widget, WhatsApp, walk-in, referral
- Lead pipeline: New → Demo Scheduled → Demo Done → Enrolled → Lost
- Assign leads to staff for follow-up
- Follow-up reminders and task management
- Track lead source (organic, referral, ad campaign, social)
- Conversion rate analytics (leads → demos → enrollments)
- Automated welcome message on enrollment
- Referral tracking (existing student refers new student → reward)

**Lead Pipeline:**
```
LEAD_CAPTURED → CONTACTED → DEMO_SCHEDULED → DEMO_DONE
             → NEGOTIATION → ENROLLED (student record created)
             → LOST (with reason: fees, timing, competitor)
```

---

### 5.15 Marketing

**Key Features:**
- WhatsApp broadcast campaigns (promotions, new batches, results)
- Email campaigns with template builder
- SMS bulk campaigns
- Track open rates, click rates, conversion
- Campaign audience filters (by batch, age group, status, area)
- Google Ads integration (track which leads came from which ad)
- Automated re-engagement for dropped/inactive students
- Referral program setup and tracking

---

### 5.16 AI Assistant Engine

See full detail in [Section 12](#12-ai--automation-features).

**Summary of AI capabilities:**
- Question paper generation from topic/chapter
- Personalized report card comments
- Lesson plan suggestions for teachers
- Attendance anomaly detection
- Parent communication drafting
- Fee default risk prediction
- Demand forecasting for new batches

---

### 5.17 Analytics Dashboard

**Owner-Level Analytics:**
- Revenue: daily/weekly/monthly collection, outstanding dues
- Enrollment trends: new admissions, dropouts, net growth
- Attendance heatmap across all batches
- Teacher performance (classes conducted vs scheduled)
- Assessment score distribution per batch
- Lead conversion funnel (CRM)
- Module usage analytics (which features are used most)
- Branch comparison (for multi-branch institutes)

**Tech Used:**
- PostgreSQL window functions + CTEs for aggregations
- Redis caching for dashboard queries (TTL: 30 minutes)
- Recharts for interactive charts
- CSV/Excel export for all reports
- Scheduled automated reports emailed to owner (weekly summary)

---

## 6. User Portals

### Portal 1 — Owner / Admin Web App

```
Sidebar Navigation:
  ├── Dashboard          (revenue, enrollment, attendance at-a-glance)
  ├── Students           (list, profile, enrollment, documents)
  ├── Staff              (teachers, accountants, front desk)
  ├── Batches            (create, schedule, manage capacity)
  ├── Fees               (invoices, collections, outstanding, receipts)
  ├── Attendance         (batch-wise, student-wise, monthly report)
  ├── Assessments        (create, results, grade sheets)
  ├── Report Cards       (generate, approve, publish)
  ├── Certificates       (templates, bulk generate, issue history)
  ├── CRM                (leads, pipeline, follow-ups)
  ├── Communication      (announcements, messages, campaigns)
  ├── Analytics          (all charts, exports, trends)
  └── Settings           (institute profile, modules, integrations)
```

### Portal 2 — Teacher Web App

```
Sidebar Navigation:
  ├── My Schedule        (today's classes, weekly timetable)
  ├── Attendance         (mark for today's sessions)
  ├── My Batches         (student roster per batch)
  ├── Assessments        (create tests, enter marks)
  ├── Report Cards       (add remarks, review drafts)
  ├── Homework           (post assignments, track submissions)
  └── Messages           (communicate with parents)
```

### Portal 3 — Parent Mobile App

```
Bottom Tabs:
  ├── Home       (child summary: today's class, next fee due, recent marks)
  ├── Attendance (monthly calendar view, percentage, session history)
  ├── Fees       (invoices, pay online, receipts, outstanding)
  ├── Progress   (assessment results, report cards, certificates)
  └── Messages   (notifications, announcements, chat with teacher)
```

### Portal 4 — Student Mobile App

```
Bottom Tabs:
  ├── Home       (next class countdown, recent announcements)
  ├── Timetable  (weekly schedule, upcoming sessions)
  ├── Assignments(posted homework, submit work)
  ├── Results    (assessment scores, grade history)
  └── My Profile (certificates, achievements, progress milestones)
```

---

## 7. Database Design

### Core Entity Relationships

```
tenants ──< students ──< enrollments >── batches >── schedule_slots
            │                            │
            └── parents                 └── teachers
            └── student_docs
            └── attendance >── sessions

students ──< fee_invoices >── payments
students ──< assessment_results >── assessments >── batches
students ──< certificates
students ──< report_cards

leads ──> students (on enrollment)
tenants ──< staff ──< roles
```

### Key Table Schemas

```sql
-- Tenant (institute) root table
CREATE TABLE tenants (
  id               UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name             VARCHAR(200) NOT NULL,
  subdomain        VARCHAR(100) UNIQUE,
  institute_type   VARCHAR(50),          -- tuition, chess, dance, driving, etc.
  active_modules   TEXT[],               -- array of enabled module names
  plan             VARCHAR(20) DEFAULT 'starter',
  owner_phone      VARCHAR(15),
  gst_no           VARCHAR(15),
  logo_url         TEXT,
  is_active        BOOLEAN DEFAULT true,
  created_at       TIMESTAMPTZ DEFAULT NOW()
);

-- Students
CREATE TABLE students (
  id               UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name             VARCHAR(200) NOT NULL,
  dob              DATE,
  gender           VARCHAR(10),
  photo_url        TEXT,
  student_code     VARCHAR(30),          -- auto or custom ID
  status           VARCHAR(20) DEFAULT 'active', -- active, on_leave, dropped, graduated
  custom_fields    JSONB,                -- institute-specific: chess rating, instrument, etc.
  enrolled_at      DATE,
  created_at       TIMESTAMPTZ DEFAULT NOW()
);

-- Parents (linked to students)
CREATE TABLE parents (
  id               UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  student_id       UUID REFERENCES students(id),
  name             VARCHAR(200) NOT NULL,
  phone            VARCHAR(15) UNIQUE NOT NULL,
  email            VARCHAR(200),
  relation         VARCHAR(20),          -- father, mother, guardian
  is_primary       BOOLEAN DEFAULT false
);

-- Batches
CREATE TABLE batches (
  id               UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name             VARCHAR(200) NOT NULL,
  subject          VARCHAR(200),
  teacher_id       UUID REFERENCES staff(id),
  capacity         INTEGER,
  room             VARCHAR(100),
  start_date       DATE,
  end_date         DATE,
  status           VARCHAR(20) DEFAULT 'active'
);

-- Enrollments (student ↔ batch)
CREATE TABLE enrollments (
  id               UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  student_id       UUID REFERENCES students(id),
  batch_id         UUID REFERENCES batches(id),
  fee_plan_id      UUID REFERENCES fee_plans(id),
  discount_id      UUID REFERENCES discounts(id),
  start_date       DATE NOT NULL,
  end_date         DATE,
  status           VARCHAR(20) DEFAULT 'active',
  UNIQUE(student_id, batch_id)
);

-- Fee invoices
CREATE TABLE fee_invoices (
  id               UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  student_id       UUID REFERENCES students(id),
  fee_plan_id      UUID REFERENCES fee_plans(id),
  amount           DECIMAL(10,2) NOT NULL,
  late_fee         DECIMAL(10,2) DEFAULT 0,
  discount_amount  DECIMAL(10,2) DEFAULT 0,
  due_date         DATE NOT NULL,
  status           VARCHAR(20) DEFAULT 'pending', -- pending, paid, partial, overdue
  generated_at     TIMESTAMPTZ DEFAULT NOW()
);

-- Payments
CREATE TABLE payments (
  id               UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  invoice_id       UUID REFERENCES fee_invoices(id),
  amount           DECIMAL(10,2) NOT NULL,
  method           VARCHAR(20),          -- online, cash, cheque, bank_transfer
  reference        VARCHAR(100),         -- Razorpay payment ID or cheque no
  receipt_url      TEXT,
  paid_at          TIMESTAMPTZ DEFAULT NOW(),
  collected_by     UUID REFERENCES staff(id)
);

-- Assessments
CREATE TABLE assessments (
  id               UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  batch_id         UUID REFERENCES batches(id),
  title            VARCHAR(300) NOT NULL,
  type             VARCHAR(30),          -- mcq, subjective, practical, performance
  total_marks      DECIMAL(6,2),
  pass_marks       DECIMAL(6,2),
  grading_scale    VARCHAR(20),          -- percentage, grade, custom
  scheduled_at     TIMESTAMPTZ,
  is_published     BOOLEAN DEFAULT false
);

-- Assessment results
CREATE TABLE assessment_results (
  id               UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  assessment_id    UUID REFERENCES assessments(id),
  student_id       UUID REFERENCES students(id),
  marks_obtained   DECIMAL(6,2),
  grade            VARCHAR(10),
  remarks          TEXT,
  entered_by       UUID REFERENCES staff(id),
  entered_at       TIMESTAMPTZ DEFAULT NOW(),
  UNIQUE(assessment_id, student_id)
);

-- Attendance sessions
CREATE TABLE sessions (
  id               UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  batch_id         UUID REFERENCES batches(id),
  date             DATE NOT NULL,
  topic            TEXT,
  teacher_id       UUID REFERENCES staff(id),
  notes            TEXT,
  UNIQUE(batch_id, date)
);

-- Attendance records
CREATE TABLE attendance (
  id               UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  session_id       UUID REFERENCES sessions(id),
  student_id       UUID REFERENCES students(id),
  status           VARCHAR(10) NOT NULL, -- present, absent, late, leave
  remark           TEXT,
  marked_at        TIMESTAMPTZ DEFAULT NOW(),
  UNIQUE(session_id, student_id)
);

-- CRM Leads
CREATE TABLE leads (
  id               UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name             VARCHAR(200) NOT NULL,
  phone            VARCHAR(15),
  email            VARCHAR(200),
  source           VARCHAR(50),          -- walk_in, website, referral, whatsapp, ad
  stage            VARCHAR(30) DEFAULT 'new',
  assigned_to      UUID REFERENCES staff(id),
  interest         VARCHAR(200),         -- which batch/course they are interested in
  referrer_id      UUID REFERENCES students(id),
  notes            TEXT,
  created_at       TIMESTAMPTZ DEFAULT NOW(),
  enrolled_at      TIMESTAMPTZ,
  student_id       UUID REFERENCES students(id) -- set on enrollment
);
```

### Indexing Strategy

```sql
-- Attendance lookups
CREATE INDEX idx_attendance_session    ON attendance(session_id);
CREATE INDEX idx_attendance_student    ON attendance(student_id);
CREATE INDEX idx_sessions_batch_date   ON sessions(batch_id, date DESC);

-- Fee queries
CREATE INDEX idx_invoices_status       ON fee_invoices(status, due_date);
CREATE INDEX idx_invoices_student      ON fee_invoices(student_id);

-- Student search (full-text)
CREATE INDEX idx_students_name         ON students USING gin(to_tsvector('english', name));

-- Enrollment active lookups
CREATE INDEX idx_enrollments_active    ON enrollments(batch_id) WHERE status = 'active';

-- Assessment results
CREATE INDEX idx_results_assessment    ON assessment_results(assessment_id);

-- Lead pipeline
CREATE INDEX idx_leads_stage           ON leads(stage, assigned_to);
```

---

## 8. API Design Principles

### REST API Structure

All APIs are versioned, tenant-scoped, and role-guarded:

```
Base URL:  https://api.edubizos.in/v1

Auth:
  POST  /auth/send-otp
  POST  /auth/verify-otp
  POST  /auth/refresh
  POST  /auth/logout

Students:
  GET   /students                    (list with search/filter/pagination)
  POST  /students                    (create + enroll)
  GET   /students/:id                (full profile)
  PATCH /students/:id                (update)
  GET   /students/:id/attendance     (attendance history)
  GET   /students/:id/fees           (invoice + payment history)
  GET   /students/:id/results        (all assessment results)

Batches:
  GET   /batches
  POST  /batches
  GET   /batches/:id/students        (enrolled roster)
  GET   /batches/:id/schedule        (timetable slots)
  POST  /batches/:id/sessions        (create today's session)

Attendance:
  POST  /sessions/:id/attendance     (bulk mark attendance)
  GET   /sessions/:id/attendance     (view marked attendance)
  GET   /attendance/monthly-report   (?studentId=&month=)

Fees:
  GET   /fee-invoices                (filter by status, student, date)
  POST  /fee-invoices/generate       (manual trigger for a student)
  POST  /payments                    (record cash/cheque payment)
  GET   /fee-invoices/:id/receipt    (PDF receipt download)

Assessments:
  POST  /assessments
  POST  /assessments/:id/results     (bulk enter marks)
  GET   /assessments/:id/results     (grade sheet)

Report Cards:
  POST  /report-cards/generate       (trigger batch generation)
  PATCH /report-cards/:id/approve    (principal approval)
  GET   /report-cards/:id            (fetch PDF URL)

CRM:
  GET   /leads
  POST  /leads
  PATCH /leads/:id/stage
  POST  /leads/:id/enroll            (convert lead → student)

Analytics:
  GET   /analytics/revenue           (?from=&to=)
  GET   /analytics/enrollment-trend  (?months=6)
  GET   /analytics/attendance-summary
  GET   /analytics/crm-funnel
```

### Standard Response Envelope

```json
{
  "success": true,
  "data": { ... },
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 148
  }
}
```

Error response:
```json
{
  "success": false,
  "error": {
    "code": "BATCH_CAPACITY_FULL",
    "message": "This batch has reached maximum capacity of 25 students",
    "details": { "batchId": "uuid", "capacity": 25, "enrolled": 25 }
  }
}
```

### Authentication Flow

```
1. User (owner/teacher/parent/student) enters mobile number
2. POST /auth/send-otp   → OTP sent via MSG91/Twilio
3. POST /auth/verify-otp → returns { access_token, refresh_token, user, role, tenant }
4. All requests: Authorization: Bearer <access_token>
5. Tenant resolved from token payload (no extra header needed)
6. POST /auth/refresh    → new access_token (refresh_token valid 7 days)
7. Refresh tokens stored in Redis keyed by userId with TTL
```

---

## 9. Real-Time Features

### Socket.IO Room Architecture

```
Rooms:
  institute:{tenantId}          → owner/admin receives live alerts
  batch:{batchId}               → teacher + enrolled parents get session updates
  student:{studentId}           → parent receives their child's alerts
  staff:{staffId}               → individual teacher/staff notifications

Events (Server → Client):
  attendance:marked             → parent notified when child marked absent/late
  fee:reminder                  → real-time fee due alert to parent
  fee:paid                      → owner dashboard updates live on payment
  lead:new                      → CRM dashboard notified of new lead
  report_card:published         → parent notified report card is ready
  certificate:ready             → parent/student notified certificate generated
  announcement:new              → all connected clients in institute room
  assessment:result_published   → students/parents notified of new results

Events (Client → Server):
  teacher:session:start         → teacher starts a live session
  teacher:attendance:submit     → triggers parent notification pipeline
  owner:lead:assign             → reassigns lead to staff member
```

### Notification Strategy

| Event | SMS | Push | WhatsApp | In-App |
|---|---|---|---|---|
| Child marked absent | Yes | Yes | Yes | Yes |
| Fee due reminder | Yes | Yes | Yes | Yes |
| Fee payment confirmed | No | Yes | Yes | Yes |
| Report card published | No | Yes | Yes | Yes |
| Certificate ready | No | Yes | Yes | Yes |
| New announcement | No | Yes | No | Yes |
| Assessment result | No | Yes | Yes | Yes |
| Timetable changed | No | Yes | Yes | Yes |
| Emergency alert | Yes | Yes | Yes | Yes |

### BullMQ Queue Architecture

```
Queues:
  notification-queue     → all SMS / push / WhatsApp dispatch
  report-gen-queue       → PDF report card generation (heavy, async)
  certificate-queue      → bulk certificate PDF generation
  fee-reminder-queue     → daily fee reminder processing
  ai-queue               → AI question gen, comment generation
  email-queue            → bulk email campaigns
  export-queue           → heavy data exports (Excel, CSV)

Queue Config:
  notification-queue:    concurrency 20, retries 3, backoff exponential
  report-gen-queue:      concurrency 5  (CPU-heavy PDF work)
  certificate-queue:     concurrency 5
  ai-queue:              concurrency 10, timeout 30s per job
```

---

## 10. Security & Compliance

### Role-Based Access Control

```
Permission Matrix:
                        OWNER  BRANCH_MGR  TEACHER  ACCOUNTANT  FRONT_DESK  PARENT  STUDENT
View all students         ✓        ✓          ✗         ✗           ✓          ✗       ✗
Mark attendance           ✓        ✓          ✓         ✗           ✗          ✗       ✗
Create assessments        ✓        ✓          ✓         ✗           ✗          ✗       ✗
Collect fees              ✓        ✓          ✗         ✓           ✗          ✗       ✗
View fee reports          ✓        ✓          ✗         ✓           ✗          ✗       ✗
Manage leads (CRM)        ✓        ✓          ✗         ✗           ✓          ✗       ✗
View analytics            ✓        ✓          ✗         ✓           ✗          ✗       ✗
Approve report cards      ✓        ✓          ✗         ✗           ✗          ✗       ✗
Run campaigns             ✓        ✓          ✗         ✗           ✓          ✗       ✗
View own child data       ✗        ✗          ✗         ✗           ✗          ✓       ✗
View own data             ✗        ✗          ✗         ✗           ✗          ✗       ✓
```

### Data Security

| Concern | Implementation |
|---|---|
| Tenant data isolation | PostgreSQL schema per tenant — cross-tenant queries impossible |
| Authentication | OTP-only, no password storage |
| Token security | JWT RS256, access token 15 min, refresh token 7 days in Redis |
| File access | S3 presigned URLs with 30-min expiry — no public bucket for student data |
| Secrets | AWS Secrets Manager — never in codebase or env files |
| Input validation | class-validator DTOs on every endpoint, Zod on frontend forms |
| SQL injection | Prisma parameterized queries exclusively |
| Rate limiting | 60 req/min per IP, 5 req/min on OTP endpoints |
| HTTPS | TLS 1.3 enforced, HSTS, no HTTP fallback |
| Audit logs | Every sensitive action logged: who, what, when, from which IP |
| GDPR / DPDP | Data export and deletion endpoints for compliance |

### Child Data Protection

Since the platform handles minors' data, extra safeguards apply:

- Parent must verify their phone number before accessing child data
- Student photos stored in private S3 bucket — never public URLs
- No student PII exposed in API responses to roles without permission
- All document access logged with actor ID and timestamp
- Deleted institute data purged after 90-day grace period

---

## 11. Payment Integration

### Razorpay Fee Collection Flow

```
Parent opens fee invoice in app
    → Backend creates Razorpay Order (server-side with amount)
    → Returns { razorpay_order_id, amount, currency } to app
    → App launches Razorpay checkout (UPI / Card / NetBanking / Wallet)
    → Payment completed by parent
    → Razorpay fires webhook → POST /webhooks/razorpay
    → Backend verifies HMAC-SHA256 signature
    → Invoice marked PAID
    → Payment record created in DB
    → PDF receipt generated → uploaded to S3
    → Parent receives WhatsApp + push with receipt link
    → Owner dashboard updates in real-time (Socket.IO)
```

### Webhook Signature Verification

```typescript
verifyWebhook(rawBody: Buffer, signature: string): boolean {
  const expected = crypto
    .createHmac('sha256', process.env.RAZORPAY_WEBHOOK_SECRET)
    .update(rawBody)
    .digest('hex');
  return crypto.timingSafeEqual(
    Buffer.from(expected),
    Buffer.from(signature)
  );
}
```

### Supported Payment Methods

| Method | Use Case |
|---|---|
| UPI (GPay, PhonePe, Paytm) | Primary — most parents prefer this |
| Debit / Credit Card | Secondary |
| Net Banking | Tertiary |
| Razorpay Payment Link | Collect via WhatsApp — no app needed |
| Cash / Cheque | Manual entry by accountant |
| EMI (Razorpay) | Annual fee plans |

### Partial Payments & Outstanding Tracking

```
Invoice Amount:  ₹3,000
Payment 1:       ₹1,500  → status: partial, outstanding: ₹1,500
Payment 2:       ₹1,500  → status: paid, outstanding: ₹0

Late fee logic:
  If unpaid after due_date + grace_days:
    late_fee = flat ₹50 OR (outstanding × daily_rate% × days_overdue)
    Invoice updated with late_fee; parent re-notified
```

---

## 12. AI & Automation Features

### AI Stack

| Tool | Purpose |
|---|---|
| **OpenAI GPT-4o / Gemini 1.5** | Report comments, lesson plans, communication drafts |
| **OpenAI Structured Outputs** | Question paper generation (JSON schema enforced) |
| **BullMQ AI Queue** | All AI tasks run async — never blocks the API response |
| **Redis Caching** | Cache identical AI outputs (TTL: 24 hours) to save cost |

### Feature 1 — Question Paper Generation

```
Teacher provides: subject, topic, level, question types, difficulty
AI returns structured JSON:

{
  "questions": [
    {
      "type": "mcq",
      "text": "What are the roots of x² - 5x + 6 = 0?",
      "options": ["(2,3)", "(1,6)", "(2,4)", "(3,4)"],
      "answer": "(2,3)",
      "marks": 1
    },
    {
      "type": "short_answer",
      "text": "Explain the nature of roots when discriminant < 0.",
      "answer": "No real roots — roots are complex/imaginary.",
      "marks": 3
    }
  ]
}

Teacher reviews → edits if needed → publishes as assessment
```

### Feature 2 — Personalized Report Card Comments

```
Input per student:
  attendance_pct: 88%, avg_marks: 76/100
  top_subject: Science, weak_subject: Mathematics
  teacher_remark: "Participates well, needs to focus on algebra"

AI Output:
  "Priya has shown excellent dedication this term, particularly
   excelling in Science with consistent curiosity and effort.
   With a little more focus on algebra practice, she is well
   on track to achieve outstanding results."

Caching: SHA256 hash of input → served from Redis if seen before
```

### Feature 3 — Lesson Plan Generator

```
Input: subject, topic, duration, student age, learning objectives
Output:
  {
    "objectives": ["...", "..."],
    "introduction":  { "duration": "5 min",  "activity": "..." },
    "main_content":  [
      { "duration": "15 min", "activity": "...", "method": "explanation" },
      { "duration": "10 min", "activity": "...", "method": "group work" }
    ],
    "assessment":    "Exit ticket: 3 questions on ...",
    "homework":      "...",
    "resources":     ["video suggestion", "worksheet idea"]
  }
```

### Feature 4 — Attendance Anomaly Detection

```sql
-- Weekly job: flag students below threshold or with consecutive absences
SELECT student_id,
  COUNT(*) FILTER (WHERE status = 'absent') AS absences,
  ROUND(100.0 * COUNT(*) FILTER (WHERE status = 'present') / COUNT(*), 1) AS pct
FROM attendance
JOIN sessions ON sessions.id = attendance.session_id
WHERE sessions.date >= NOW() - INTERVAL '30 days'
GROUP BY student_id
HAVING pct < 75;
-- Results → alert queue → notify owner + teacher
```

### Feature 5 — Fee Default Risk Scoring

```
Weekly scoring per student:
  + 30 pts → paid late 2+ consecutive months
  + 25 pts → partial payment outstanding > 15 days
  + 20 pts → attendance below 60% (disengaged)
  + 25 pts → no response to last 2 reminders

Score ≥ 70  → HIGH RISK   → highlighted in owner dashboard
Score 40–69 → MEDIUM RISK → extra WhatsApp reminder sent
Score < 40  → LOW RISK    → standard reminder only
```

### Feature 6 — Parent Communication Drafting

```
Input: { tone: "friendly", context: "Summer batch starting June 1" }

Output (WhatsApp format):
  "Hello! 🌟 We're excited to announce our Summer Special Batch
   starting June 1st. Limited seats available — enroll your
   child today and give them a productive summer!
   Reply YES to reserve a seat."

Output (SMS format — 160 chars):
  "Summer Batch starts June 1 - limited seats! Reply YES to
   confirm enrollment. Call 98765-XXXXX for details."
```

---

## 13. Deployment & DevOps

### Infrastructure Setup (AWS)

```
Production Environment:

Route 53 (DNS)
    → CloudFront (CDN for React web app + S3 assets)
    → Application Load Balancer (HTTPS, SSL termination)
        → ECS Fargate Cluster
            → API Service        (NestJS, 2–4 containers, auto-scaling)
            → Socket.IO Service  (sticky sessions via ALB target group)
            → Worker Service     (BullMQ consumers, 2 containers)
    → RDS PostgreSQL    (Multi-AZ, automated daily backups, 7-day retention)
    → ElastiCache Redis (cluster mode, 2 nodes)
    → S3 Buckets:
        edubizos-private   (student docs, report cards, certificates)
        edubizos-public    (institute logos, template assets via CloudFront)
    → SES               (transactional emails)
    → SNS               (SMS fallback routing)
    → Secrets Manager   (all credentials)
```

### Docker Setup

```dockerfile
# Dockerfile (NestJS API)
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:20-alpine AS runner
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
EXPOSE 3000
CMD ["node", "dist/main.js"]
```

Local development with Docker Compose:
```yaml
# docker-compose.yml
services:
  api:
    build: .
    ports: ["3000:3000"]
    environment:
      DATABASE_URL: postgresql://postgres:password@db:5432/edubizos
      REDIS_URL: redis://redis:6379
    depends_on: [db, redis]

  db:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: edubizos
      POSTGRES_PASSWORD: password
    volumes: [pgdata:/var/lib/postgresql/data]

  redis:
    image: redis:7-alpine
    ports: ["6379:6379"]

volumes:
  pgdata:
```

### CI/CD Pipeline (GitHub Actions)

```yaml
# .github/workflows/deploy.yml
name: Build & Deploy

on:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:15
        env: { POSTGRES_PASSWORD: test }
      redis:
        image: redis:7
    steps:
      - uses: actions/checkout@v4
      - run: npm ci
      - run: npm run test:ci
      - run: npm run lint
      - run: npm run build

  deploy:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4

      - name: Build & push Docker image to ECR
        run: |
          docker build -t edubizos-api .
          docker tag edubizos-api $ECR_REGISTRY/edubizos-api:$GITHUB_SHA
          docker push $ECR_REGISTRY/edubizos-api:$GITHUB_SHA

      - name: Run DB migrations
        run: npx prisma migrate deploy

      - name: Deploy to ECS
        run: |
          aws ecs update-service \
            --cluster edubizos-prod \
            --service edubizos-api \
            --force-new-deployment
```

### Environment Configuration

```
Environments:
  development  → Docker Compose locally (PostgreSQL + Redis + mocked SMS)
  staging      → AWS ECS (scaled down, Razorpay test mode, real SMS sandbox)
  production   → AWS ECS (full scale, live integrations)

Feature Flags:
  → Stored in Redis per tenant
  → Toggle AI features, new modules, beta features without redeploy
  → Admin panel UI to manage flags per tenant
```

### Monitoring & Observability

| Tool | Purpose |
|---|---|
| **AWS CloudWatch** | Container CPU/memory metrics, log groups, alarms |
| **Sentry** | Error tracking across API, React web, React Native apps |
| **Datadog** | APM, distributed tracing, dashboard |
| **UptimeRobot** | External uptime checks every 60 seconds |
| **PgHero / pgBadger** | PostgreSQL slow query analysis |
| **BullMQ Dashboard** | Queue depth, failed jobs, retry monitoring |

### Scaling Strategy

```
API Service:
  Min containers: 2 (always on)
  Max containers: 10 (auto-scale on CPU > 70%)
  Scale-out cooldown: 60s

Worker Service:
  Scales based on BullMQ queue depth
  > 500 pending jobs → add container
  < 50 pending jobs  → scale in

Database:
  Start: db.t3.medium (RDS)
  Scale: db.t3.large → db.r6g.large as tenants grow
  Read replica added when read traffic > 60% of load

Redis:
  Start: cache.t3.micro
  Scale: cache.r6g.large for large notification volume
```

---

## 14. Product Roadmap

### MVP — Core Operations (Months 1–4)

**Goal:** Get institutes off paper and WhatsApp — into a working digital system

| Feature | Priority |
|---|---|
| Institute onboarding + tenant provisioning | P0 |
| Student registration and profiles | P0 |
| Batch and timetable management | P0 |
| Attendance marking + parent notification | P0 |
| Fee invoicing and collection (Razorpay) | P0 |
| Fee reminders (automated) | P0 |
| Owner web dashboard | P0 |
| Teacher web portal | P0 |
| Parent mobile app (attendance + fees) | P0 |
| Basic analytics (revenue, attendance) | P1 |
| Document storage | P1 |
| Communication — announcements | P1 |

**Success Metric:** 100 institutes actively using attendance + fee collection

---

### V2 — Engagement Layer (Months 3–7)

**Goal:** Make parents and students active users of the platform

| Feature | Priority |
|---|---|
| Parent portal — full progress view | P0 |
| Student portal (timetable, results) | P0 |
| Assessments — create, enter marks, publish results | P0 |
| Report card generation + approval workflow | P0 |
| Certificate templates + bulk generation | P0 |
| Teacher-to-parent direct messaging | P1 |
| Homework/assignment posting | P1 |
| Daily diary (for play schools) | P1 |
| Google Calendar sync for teachers | P2 |

**Success Metric:** 70% parent app adoption across active institutes

---

### V3 — Intelligence & Growth (Months 6–12)

**Goal:** AI-powered automation + tools to grow the institute's business

| Feature | Priority |
|---|---|
| AI question paper generation | P0 |
| AI report card comment generation | P0 |
| Admissions CRM (lead pipeline) | P0 |
| Marketing campaigns (WhatsApp + SMS) | P1 |
| AI lesson plan generator | P1 |
| Attendance anomaly detection | P1 |
| Fee default risk scoring | P1 |
| Advanced analytics + trend charts | P1 |
| Referral program | P2 |
| Multi-branch management | P2 |

**Success Metric:** 500 institutes, 50,000 students on platform

---

### V4 — Vertical Expansion (Year 2+)

- **Tournament Engine** — Chess, sports, abacus competitions
- **Driving School Compliance** — Lesson hour tracking, license status
- **Franchise Management** — Multi-location chains (abacus, coding academies)
- **Online Classes** — Live class integration (Zoom/Jitsi embed)
- **Marketplace** — Teachers find students, institutes find staff
- **Parent Community** — Forum, events, polls per institute
- **B2B API** — White-label platform for large franchise chains

---

## 15. Revenue Model

### Subscription Plans

| Plan | Price / Month | Best For | Included |
|---|---|---|---|
| **Starter** | ₹499 | Single-teacher small institute | Students, attendance, basic fees, 1 teacher, 50 students |
| **Growth** | ₹999 | Growing institute | All Starter + parent app, assessments, report cards, 5 staff, 200 students |
| **Professional** | ₹1,999 | Established institute | All Growth + AI features, CRM, marketing, certificates, unlimited staff, 500 students |
| **Enterprise** | Custom | Franchise chains, schools | White-label, API access, multi-branch, dedicated support, SLA |

### Add-On Revenue

| Stream | Model |
|---|---|
| **AI Credits** | Pay-per-use for high-volume AI (question gen, bulk comments) above plan limit |
| **WhatsApp Business API** | Pass-through cost + ₹0.10 margin per message |
| **Extra Student Packs** | ₹199/month per 100 additional students above plan limit |
| **Custom Certificate Templates** | ₹999 one-time design service |
| **Data Migration Service** | ₹2,000 one-time fee to migrate from old software |
| **Priority Support** | ₹499/month for dedicated support channel |

### Revenue Projections

```
Year 1:
  500 institutes × ₹800 avg/month = ₹4L/month
  + add-ons = ~₹50L ARR

Year 2:
  3,000 institutes × ₹1,000 avg/month = ₹30L/month
  + enterprise deals = ~₹4Cr ARR

Year 3:
  10,000 institutes × ₹1,200 avg/month = ₹1.2Cr/month
  + marketplace + franchise = ~₹18Cr ARR
```

---

## 16. Go-to-Market Strategy

### Phase 1 — Direct Outreach (Months 1–6)

Target 3 institute types with highest pain: **tuition centres**, **dance academies**, **abacus centres**

- Direct outreach to institute owners via WhatsApp and local networks
- Free 30-day trial with full onboarding assistance
- Founder does first 20 demos personally to gather feedback
- Focus on one city (e.g., Chennai or Bangalore) to build density

### Phase 2 — Referral & Word of Mouth (Months 4–12)

```
Referral Program:
  Existing institute refers another institute
  → Referrer gets 1 free month subscription
  → New institute gets 30-day free trial extended to 60 days
  → Tracked via unique referral link in owner dashboard
```

- Happy institute owners are the best salespeople — friction-free referral flow
- Case study videos from early adopters for social proof
- YouTube channel: "How to run your coaching centre efficiently"

### Phase 3 — Digital Acquisition (Months 6+)

- Google Ads targeting: "coaching centre management software", "tuition centre app"
- Instagram / Facebook ads targeting institute owners
- SEO content: guides on running tuition centres, fee management tips
- Partnerships with teacher training institutes and education franchises

### Franchise & Chain Sales

```
Target: Abacus chains (UCMAS, SIP Abacus), chess franchises, coding bootcamps

Pitch:
  - White-label the platform with their branding
  - Centralized admin sees all franchise locations
  - Franchise owners get their own dashboard
  - Standardized report cards and certificates across all centres

Pricing: Enterprise plan per location + platform fee to HQ
```

---

## 17. Risks & Mitigations

| Risk | Impact | Mitigation |
|---|---|---|
| **Low tech adoption by institute owners** | High | WhatsApp-first UX, voice support, in-person onboarding assistance |
| **Resistance to online fee collection** | High | Support cash recording too — don't force online-only |
| **Data migration from existing software** | Medium | CSV import tool + migration service offering |
| **Competition from generic tools (Google Sheets, Tally)** | Medium | Highlight automation, parent app, and AI — no manual work |
| **Competition from established EdTech SaaS** | Medium | Focus on niche institutes (dance, chess, abacus) they don't serve |
| **WhatsApp API cost and reliability** | Medium | Fallback to SMS; cache templates; bulk batching |
| **AI cost overrun (OpenAI pricing)** | Low | Redis caching of AI outputs, rate limits per plan, cost monitoring |
| **Data breach / student PII leak** | High | Encryption, private S3, no public URLs, annual pen testing |
| **Internet connectivity in tier-3 cities** | Medium | PWA with offline attendance sync when connectivity restores |
| **DPDP Act compliance (India)** | Medium | Data export, deletion endpoints, consent flows, privacy policy |
| **Churn due to poor support** | High | In-app help widget, WhatsApp support group per region, video tutorials |

---

## 18. Long-Term Vision

> **"Education Business OS will be the Shopify for education institutes — every tuition centre, dance academy, music school, and coaching institute in India runs on us."**

### 5-Year Expansion Map

```
2025 — MVP: Attendance + Fees + Timetable (replace paper)
2026 — V2: Parent app + Assessments + Report cards + AI
2027 — V3: CRM + Marketing + Franchise management
2028 — Online classes + Marketplace (find teachers / students)
2029 — SE Asia expansion (Malaysia, Sri Lanka, Bangladesh)
2030 — B2B API: white-label for large education chains
```

### The Platform Flywheel

```
More Institutes  → More students and parents on platform
More Parents     → Higher retention (parents love the app → institutes stay)
More Data        → Better AI (smarter reports, better predictions)
Better AI        → Institutes save more time → willing to pay more
More Revenue     → Better product → attract bigger franchise chains
Franchise Chains → Thousands of institutes onboarded at once
```

### Network Effects by Institute Type

```
Chess Academy uses Tournament module
    → Students from other academies participate in tournaments
    → Those academies discover the platform
    → They sign up to manage their own students
```

---

## Appendix A — Monorepo Folder Structure

```
edubizos/
├── apps/
│   ├── api/                         ← NestJS backend
│   │   └── src/
│   │       ├── auth/
│   │       ├── students/
│   │       ├── batches/
│   │       ├── attendance/
│   │       ├── fees/
│   │       ├── assessments/
│   │       ├── report-cards/
│   │       ├── certificates/
│   │       ├── crm/
│   │       ├── communication/
│   │       ├── analytics/
│   │       ├── ai/
│   │       ├── notifications/
│   │       └── tenants/
│   ├── web/                         ← React owner + teacher dashboard
│   │   └── src/
│   │       ├── pages/
│   │       ├── components/
│   │       ├── store/               ← Redux Toolkit slices
│   │       └── hooks/
│   ├── parent-app/                  ← React Native parent app
│   └── student-app/                 ← React Native student app
├── packages/
│   ├── shared-types/                ← TypeScript DTOs shared across apps
│   ├── ui-kit/                      ← Shared React components
│   └── utils/                       ← Date helpers, formatters, validators
├── infra/
│   ├── terraform/                   ← AWS infrastructure as code
│   └── docker/                      ← Docker Compose for local dev
├── docs/
│   └── api/                         ← Swagger/OpenAPI spec
└── .github/
    └── workflows/                   ← CI/CD pipelines
```

---

## Appendix B — Key Environment Variables

```bash
# Database
DATABASE_URL=postgresql://user:pass@host:5432/edubizos
REDIS_URL=redis://host:6379

# Auth
JWT_SECRET=...
JWT_REFRESH_SECRET=...

# AWS
AWS_ACCESS_KEY_ID=...
AWS_SECRET_ACCESS_KEY=...
AWS_REGION=ap-south-1
AWS_S3_PRIVATE_BUCKET=edubizos-private
AWS_S3_PUBLIC_BUCKET=edubizos-public

# Razorpay
RAZORPAY_KEY_ID=...
RAZORPAY_KEY_SECRET=...
RAZORPAY_WEBHOOK_SECRET=...

# Firebase
FIREBASE_SERVICE_ACCOUNT=...

# SMS / WhatsApp
MSG91_API_KEY=...
MSG91_TEMPLATE_FEE_REMINDER=...
MSG91_TEMPLATE_ATTENDANCE=...
WHATSAPP_API_TOKEN=...
WHATSAPP_PHONE_NUMBER_ID=...

# AI
OPENAI_API_KEY=...
OPENAI_MODEL=gpt-4o

# App URLs
WEB_URL=https://app.edubizos.in
API_URL=https://api.edubizos.in
```

---

## Appendix C — Architecture Diagram (Mermaid)

The full system flow diagram is available in:
`final_education_platform_architecture.mermaid`

It shows how each institute type (Tuition, Music, Dance, Sports, School,
Driving, Play School, Vocational, Certification) flows into the shared Core
Platform, selects from Business Engines, and exposes data through four User
Portals — all backed by the same Infrastructure layer.

---

*Document Version: 1.0 | Created: July 2026 | Education Business OS — Technical Blueprint*
