---
id: FRS-4
iid: 4
milestone: null
actor: Company Super Admin
goal: "Provide an interface for customers to request new user accounts with bank approval"
preconditions:
  - Customer has authenticated portal access
  - Bank has designated reviewers configured
success_outcomes:
  - User request submitted and visible in tracking
  - Bank reviewer can approve/reject request
  - User created and invitation sent on approval
failure_outcomes:
  - Invalid form input rejected with error message
  - Email delivery failure logged
  - Concurrent modification detected and user warned
source_issue: http://gitlab.local:8080/root/artifacts/-/issues/4
source_type: frs
intended_nodes:
  - ACT-CompanySuperAdmin
  - ACT-BankReviewer
  - ACT-System
  - ENT-UserRequest
  - ENT-User
  - CMD-SubmitUserRequest
  - CMD-ApproveRequest
  - CMD-RejectRequest
  - CMD-GenerateUserIdentity
  - FLOW-RequestSubmission
  - FLOW-RequestApproval
  - FLOW-UserIdentityGeneration
monolith_risk: true
monolith_exception: CNF-002
fetched_at: 2026-04-09
---

# Functional Requirements Specification: User Requests Management

**Feature:** User Requests Management  
**Complexity Tier:** Moderate  
**Portals:** Customer Portal  
**Date:** 2026-04-03

---

## 1. Overview

### 1.1 Business Objective

Provide an interface for customers (company admins) to request new user accounts from the customer portal, enabling controlled user onboarding with bank approval.

### 1.2 Target Audience

- Primary: Company Super Admins (customer portal users)
- Secondary: Bank Reviewers (back-office portal users)
- Indirect: New users who receive invitation emails

### 1.3 Business Goals

- Streamline user account creation process for customers
- Maintain bank oversight and approval control
- Ensure unique user identification across the platform
- Provide transparency through request tracking

---

## 2. Actors

| Actor | Type | Portal | Permissions | Description |
|-------|------|--------|-------------|-------------|
| Company Super Admin | human | customer | Can submit requests, view own requests only | Initiates user creation requests |
| Bank Reviewer | human | back-office | Can view all requests, approve/reject requests | Reviews and processes requests |
| System | automated | both | Generates User Login ID, sends emails | Handles automated identity creation and notifications |

---

## 3. Scope

### 3.1 In-Scope

- Request form submission with fields: Name, Email, Role, Remark
- Request tracking list with status display (pending/approved/rejected)
- Approval workflow (approve/reject actions by bank reviewers)
- Email notifications (invitation to new users)
- User Login ID generation (`USR` + 6 alphanumeric characters)
- Display of review metadata (reviewer name, review date, rejection reason)

### 3.2 Out-of-Scope

- Bulk request upload
- Request editing after submission
- Multi-entity requests in single submission
- Request deletion/cancellation
- Email notification to requesters on approval/rejection (optional)

---

## 4. Functional Requirements

### FR-001: Submit User Request

- **Actor:** Company Super Admin
- **Trigger:** User navigates to `/portal/requests` and clicks "Submit New Request"
- **Inputs:** Name (string), Email (string), Role (enum: `company_super_admin` or `company_user`), Remark (string, optional)
- **Processing Logic:**
  1. Validate all required fields present
  2. Validate email format
  3. Validate role is one of allowed values
  4. Create UserRequest record with status "pending"
  5. Generate unique request ID
  6. Display confirmation message
- **Outputs:** Request saved, confirmation displayed
- **Dependencies:** None
- **Aggregate Hint:** `UserRequest` aggregate (root)
- **Error Handling:** Validation errors shown inline; technical errors logged and user-friendly message displayed

### FR-002: View Request Tracking List

- **Actor:** Company Super Admin, Bank Reviewer
- **Trigger:** User navigates to `/portal/requests` page loads
- **Inputs:** Optional status filter, optional date range
- **Processing Logic:**
  1. Fetch requests based on user role and active entity
  2. Apply filters if provided
  3. Sort by submission date (descending)
  4. Format data for display
- **Outputs:** Table showing: Name, Email, Role, Submitted Date, Status, Reviewer, Review Date, Rejection Reason; pending requests show waiting indicator
- **Dependencies:** None
- **Aggregate Hint:** `UserRequest` aggregate (root)
- **Error Handling:** Empty state shown if no requests; loading indicator during fetch

### FR-003: Approve/Reject Request

- **Actor:** Bank Reviewer
- **Trigger:** Reviewer clicks Approve or Reject button on a pending request
- **Inputs:** Action (approve/reject), rejection reason (required if reject)
- **Processing Logic (Approve):**
  1. Verify user has approval permission
  2. Update request status to "approved"
  3. Record reviewer name and current timestamp
  4. Trigger User Identity Generation (FR-004)
  5. (Optional) Send notification to requester
- **Processing Logic (Reject):**
  1. Verify user has approval permission
  2. Validate rejection reason provided
  3. Update request status to "rejected"
  4. Record reviewer name, review date, and rejection reason
  5. (Optional) Send notification to requester
- **Outputs:** Status updated, confirmation message
- **Dependencies:** FR-004 (for approve path)
- **Aggregate Hint:** `UserRequest` aggregate with `Approval` entity/component
- **Error Handling:** Permission errors, concurrent modification detection

### FR-004: Generate User Identity & Send Invitation

- **Actor:** System (automated)
- **Trigger:** Request status changes to "approved"
- **Inputs:** Approved request data (Name, Email, Role)
- **Processing Logic:**
  1. Generate unique User Login ID (`USR` + 6 alphanumeric, e.g., `USR1A2B3C`)
  2. Ensure uniqueness (retry with new random if collision)
  3. Create new User record with status "invited"
  4. Send invitation email with login instructions to the email address
  5. (Optional) Add User to Team Management listing
- **Outputs:** User created, invitation email sent
- **Dependencies:** Email service, User management API
- **Aggregate Hint:** `User` aggregate (new user created as root)
- **Error Handling:** Email retry logic, compensation if user creation fails

---

## 5. UI/Inputs Requirements

### 5.1 Screen Catalog

| Screen | Portal | Purpose | Entry Point | Exit Point |
|--------|--------|---------|-------------|------------|
| Request Form | customer | Capture new user request details | Click "Submit New Request" from tracking page | Form submission → confirmation, or cancel → return to tracking |
| Request Tracking List | customer | View submitted requests and their status | Navigate to `/portal/requests` | Click request details (if implemented), or navigate away |
| Approval Management | back-office | Review and process pending requests | Navigate to back-office request queue | Approve/Reject action, or navigate away |

### 5.2 UI Components

#### Request Form Screen

- **Layout:** Single-column, responsive (mobile-first)
- **Components:**
  - Header: "Request New User"
  - Form fields:
    - Name: text input, required, maxlength 100
    - Email: email input, required, maxlength 255, pattern validation
    - Role: dropdown/select, required, options: `company_super_admin`, `company_user`
    - Remark: textarea, optional, maxlength 500, character count
  - Submit button (primary)
  - Cancel button (secondary)
- **States:** Idle, Loading (submit in progress), Success (confirmation shown), Error (validation or server error)
- **Responsive:** Full-width on mobile, centered with max-width 600px on desktop

#### Request Tracking List Screen (Customer)

- **Layout:** Table/grid with toolbar
- **Components:**
  - Toolbar: Status filter dropdown (All, Pending, Approved, Rejected), date range picker (optional)
  - Table columns: Name, Email, Role, Submitted Date, Status, Reviewer, Review Date, Rejection Reason
  - Status badges with color coding: pending (yellow), approved (green), rejected (red)
  - Pending rows show waiting/spinner indicator in status column
  - Refresh button
- **States:** Loading (spinner), Empty (no requests), Loaded
- **Responsive:** Horizontal scroll for table on mobile, stacked cards alternative (optional)

#### Approval Management Screen (Back-Office)

- **Layout:** Tabbed or filtered table
- **Components:**
  - Tabs: Pending, Approved, Rejected (or single list with status filter)
  - Table columns: Name, Email, Role, Submitted Date, Status, Actions
  - Action buttons per row: Approve, Reject (for pending only)
  - Reject modal: textarea for rejection reason, Confirm/Cancel buttons
- **States:** Loading, Pending list, Lists populated
- **Responsive:** Similar to tracking list with action buttons clearly visible

### 5.3 Field Validations

| Field | Type | Validations | Error Messages |
|-------|------|-------------|----------------|
| Name | text | required, maxlength 100, letters/spaces only | "Name is required", "Name cannot exceed 100 characters" |
| Email | email | required, maxlength 255, valid email pattern | "Email is required", "Please enter a valid email address" |
| Role | select | required, one of enum | "Please select a role" |
| Remark | textarea | maxlength 500 | "Remark cannot exceed 500 characters" |
| Rejection Reason | textarea | required when rejecting | "Please provide a rejection reason" |

---

## 6. Data Handling

### 6.1 Data Model Summary

**Primary Aggregates:**
- `UserRequest`: root with status (pending/approved/rejected), metadata
- `User`: created upon approval, status "invited"

**Key Entities:**
- Request metadata (requester, submission date, approval date)
- Approval record (reviewer, decision, reason)
- User identity (login ID, status, email)

**State Transitions:**
```
UserRequest: pending → approved | rejected
User: invited → active (post-acceptance)
```

