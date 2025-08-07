# Detailed Implementation Plan for Advertising Panel Application

---

## Overview

The application is a full-stack advertising panel where customers can register, login, upload media content (images, videos, PPT), and get a unique 4-digit link to display their content continuously in fullscreen mode on TVs or other devices. The system includes a super admin panel for managing customer packages, feature toggling, and payment options. The backend will use Node.js with MySQL, and the frontend will be built with Next.js and TypeScript, styled with a modern, professional theme.

---

## 1. Backend Implementation (Node.js + MySQL)

### 1.1 Database Schema Design

- **Users Table**: Stores customer accounts with fields: id, name, email, password hash, created_at, updated_at.
- **Media Table**: Stores uploaded media metadata: id, user_id (foreign key), type (image, video, ppt), file_path, group_id, created_at, updated_at.
- **Groups Table**: Content groups for customers: id, user_id, name, created_at, updated_at.
- **Links Table**: Stores generated unique 4-digit links: id, user_id, link_code (unique 4-digit), group_id, created_at, expires_at.
- **Packages Table**: Defines package features and validity: id, name, features (JSON), price, duration_days.
- **UserPackages Table**: Tracks customer package subscriptions: id, user_id, package_id, start_date, end_date, active.
- **Admin Table**: Super admin credentials and shadow login tokens.
- **Payments Table**: Records payment transactions: id, user_id, package_id, amount, payment_type (online/offline), status, created_at.

### 1.2 API Endpoints

- **Auth APIs**:
  - POST `/api/register`: Register customer.
  - POST `/api/login`: Customer login (JWT or session-based).
  - POST `/api/admin/shadow-login`: Super admin shadow login as customer.
- **Media APIs**:
  - POST `/api/media/upload`: Upload media files (images, videos, PPT).
  - GET `/api/media/list`: List media for logged-in customer.
  - POST `/api/media/update`: Update media metadata or group.
- **Link APIs**:
  - POST `/api/link/generate`: Generate unique 4-digit link for a group.
  - GET `/api/link/:code/content`: Get media content for link playback.
- **Package APIs**:
  - GET `/api/packages`: List available packages.
  - POST `/api/packages/subscribe`: Customer subscribes to a package.
  - GET `/api/admin/packages`: Super admin manages packages.
  - POST `/api/admin/packages/update`: Update package features or validity.
- **Payment APIs**:
  - POST `/api/payment/online`: Handle online payment.
  - POST `/api/payment/offline`: Record offline payment.
- **Real-time Updates**:
  - Use WebSocket or Server-Sent Events (SSE) to push content updates to active links and live preview.

### 1.3 Error Handling & Security

- Validate all inputs server-side.
- Use secure password hashing (bcrypt).
- Authenticate APIs with JWT or session cookies.
- Validate file types and sizes on upload.
- Ensure unique 4-digit link generation with collision checks.
- Handle package expiry and network offline states gracefully.
- Log errors and provide meaningful API error responses.

---

## 2. Frontend Implementation (Next.js + TypeScript)

### 2.1 Authentication & User Management

- Registration and login pages with form validation.
- Super admin login with shadow login feature to impersonate customers.
- Use React Context or Redux for auth state management.

### 2.2 Dashboard (Customer Panel)

- **Modern, clean UI** with company branding (G Network Services, contact info).
- Sidebar navigation: Dashboard, Media Upload, Links, Packages, Live Preview, Account Settings.
- Media upload form supporting drag & drop and file selection for images, videos, PPT.
- Media list with thumbnails, file info, and group assignment.
- Group management UI to create/edit/delete content groups.
- Link generation UI showing generated 4-digit links per group.
- Live preview panel showing currently playing content on the link (real-time updates).
- Responsive design optimized for desktop and tablets.

### 2.3 Link Playback Page

- Route: `/1234` (dynamic 4-digit code).
- Auto fullscreen on page load using Fullscreen API.
- Continuous playback of uploaded media in the group (carousel or playlist).
- Controls for play, pause, stop (hidden or accessible via secret key).
- Responsive to screen size and orientation changes.
- Show offline or package expired message overlay if applicable.

### 2.4 Super Admin Panel

- Login page for super admin.
- Dashboard showing customer list, package subscriptions, and link usage.
- Package management UI to create/update packages with feature toggles.
- Shadow login feature to access customer panels without password.
- Payment management UI for online and offline payments.
- Alerts and notifications for package expiry, renewals, and welcome messages.

### 2.5 UI/UX Considerations

- Use typography, spacing, and color palette for a professional look.
- Avoid icon libraries; use text, shapes, and CSS for UI elements.
- Provide clear feedback on actions (upload success, errors, link generation).
- Use modals/dialogs for confirmations and alerts.
- Accessibility: keyboard navigation, ARIA labels, and screen reader support.

---

## 3. Media Upload & Storage

- Use multipart/form-data for file uploads.
- Store files on server filesystem or cloud storage (configurable).
- Generate thumbnails/previews for images and videos.
- Validate file types and sizes.
- Support PPT uploads as files (no conversion required).
- Store media metadata in MySQL.

---

## 4. Real-time Content Update & Playback

- Use WebSocket or SSE for pushing updates to playback pages and live preview.
- When customer updates content, broadcast changes to all active viewers.
- Playback page listens for updates and refreshes playlist without reload.
- Handle network offline by showing offline message and pausing playback.
- On package expiry, show expiry message and stop playback.

---

## 5. Link Generation Logic

- Generate unique 4-digit numeric codes.
- Check for collisions in the database.
- Associate link with customer and content group.
- Links expire based on package validity.
- Links are short and simple: `mydomain.com/1234`.

---

## 6. Payment Integration

- Integrate online payment gateway (e.g., Razorpay, Stripe) - request API keys from user.
- Offline payment recording UI.
- Update package subscription status on payment success.
- Notify customers on welcome, expiry, and renewal via dashboard alerts.

---

## 7. Cross-platform Compatibility

- Backend: Node.js with cross-platform compatible libraries.
- Frontend: Next.js supports Windows and Linux.
- Use environment variables and config files for platform-specific settings.

---

## 8. File and Folder Structure Changes

- **Backend**: Create `server/` folder with API routes and services.
- **Database**: Add migration scripts for new tables.
- **Frontend**: Add new pages and components under `src/app` or `src/pages`.
- **Shared**: Add utilities for auth, media handling, and link generation.

---

## 9. Testing & Validation

- Unit tests for backend APIs and utilities.
- Integration tests for user flows (registration, upload, link playback).
- Manual testing on desktop and TV devices for fullscreen and playback.
- Curl tests for API endpoints (upload, link generation, package management).

---

## 10. Branding & Configuration

- Add company name, contact number, and email in header/footer of UI.
- Use consistent color scheme and typography reflecting a professional company.
- Provide config file for domain name (e.g., `mydomain.com`).

---

# Step-by-step File Changes Outline

| File/Folder | Changes/Features |
|-------------|------------------|
| `server/` (new) | Implement REST API endpoints for auth, media upload, link generation, package management, payments, and real-time updates. |
| `db/migrations/` (new) | Add SQL scripts for creating necessary tables. |
| `src/app/login.tsx` | Customer login page with form and validation. |
| `src/app/register.tsx` | Customer registration page. |
| `src/app/dashboard/` | Customer dashboard with media upload, group management, link generation, and live preview components. |
| `src/app/link/[code]/page.tsx` | Dynamic route for 4-digit link playback page with fullscreen and continuous media playback. |
| `src/app/admin/` | Super admin panel with customer/package management and shadow login. |
| `src/components/ui/` | Add reusable UI components for forms, modals, alerts, and media lists with modern styling. |
| `src/lib/auth.ts` | Authentication utilities (JWT/session management, password hashing). |
| `src/lib/media.ts` | Media upload and processing utilities. |
| `src/lib/link.ts` | Link generation and validation utilities. |
| `src/lib/package.ts` | Package feature toggling and validation utilities. |
| `src/lib/payment.ts` | Payment integration utilities. |
| `src/hooks/useWebSocket.ts` | Hook for real-time updates via WebSocket or SSE. |
| `next.config.js` | Add any necessary config for API routes, image handling, and environment variables. |

---

# UI/UX Design Details

- **Dashboard**: Sidebar with navigation, main content area with cards and tables.
- **Media Upload**: Drag & drop area, file list with thumbnails, upload progress.
- **Link Playback**: Fullscreen video/image carousel, auto-play, responsive to screen size and orientation.
- **Live Preview**: Real-time content preview with status indicators.
- **Admin Panel**: Data tables with filters, package feature toggles, shadow login button.
- **Alerts**: Toast notifications for welcome, expiry, and payment status.
- **Branding**: Header/footer with company name "G Network Services", contact +91 9145406872, email info@gnetworkservices.in, consistent color palette (e.g., blue and white), clean typography.

---

# Additional Features Suggestions

- Content scheduling: Allow customers to schedule content playback times.
- Analytics: Track link views and playback statistics.
- Multi-language support for UI.
- Dark mode toggle.
- Responsive design for mobile access to dashboard.

---

# Summary

- Implement Node.js backend with MySQL for user, media, link, package, and payment management.
- Create REST APIs with authentication, media upload, link generation, and real-time updates.
- Build Next.js frontend with modern, professional UI for customer dashboard, media upload, live preview, and link playback.
- Develop super admin panel with shadow login and package management.
- Use WebSocket or SSE for real-time content updates and live preview.
- Generate unique 4-digit links with collision checks.
- Auto fullscreen and continuous playback on link pages for TV display.
- Integrate online/offline payment options with alerts.
- Ensure cross-platform compatibility and error handling.
- Add company branding and contact info in UI.
- Provide detailed testing and validation for all features.

Please confirm this plan or specify any changes or additional requirements before I proceed with implementation.



# Detailed Plan for Razorpay Payment Integration and Admin Payment Settings

---

## Overview

We will integrate Razorpay payment gateway into the existing advertising panel app backend and frontend. The super admin will have a dedicated UI to configure Razorpay API keys and payment settings. Customers will be able to make online payments for packages using Razorpay. The system will handle payment verification, update package subscriptions, and notify customers accordingly.

---

## Backend Changes

### 1. Add Razorpay Configuration Storage

- **Database**: Add a table `payment_settings` with fields:
  - `id` (primary key)
  - `razorpay_key_id` (string)
  - `razorpay_key_secret` (string)
  - `updated_at` (timestamp)

- **API Endpoints**:
  - `GET /api/admin/payment-settings`: Return current Razorpay keys (only for super admin).
  - `POST /api/admin/payment-settings`: Update Razorpay keys and settings (only for super admin).

- **Security**:
  - Protect these endpoints with super admin authentication.
  - Store Razorpay secret securely, never expose it to frontend.

### 2. Payment Order Creation API

- `POST /api/payment/create-order`:
  - Input: package ID, user token.
  - Backend creates Razorpay order using stored keys and package amount.
  - Returns order details (order ID, amount, currency) to frontend.

### 3. Payment Verification API

- `POST /api/payment/verify`:
  - Input: payment details from frontend (payment ID, order ID, signature).
  - Verify signature using Razorpay secret.
  - On success, update user package subscription status.
  - On failure, return error.

### 4. Offline Payment API

- Extend existing offline payment API to allow super admin to mark payments as received.

### 5. Notifications

- Send alerts to customers on payment success, package expiry, and renewal reminders.

---

## Frontend Changes

### 1. Super Admin Payment Settings Page

- New page under `/src/app/admin/payment-settings.tsx`.
- Form to input and update Razorpay Key ID and Key Secret.
- Validate inputs and show success/error messages.
- Use secure API calls to backend.

### 2. Customer Payment UI

- On package purchase page (e.g., `/src/app/dashboard/packages.tsx` or dedicated purchase page):
  - Add "Pay Online" button.
  - On click, call backend to create Razorpay order.
  - Use Razorpay Checkout JS SDK to open payment modal.
  - On payment success, call backend to verify payment.
  - Show payment status and update UI accordingly.

### 3. Payment Status Alerts

- Show toast notifications or dashboard alerts for payment success, expiry, and renewal.

---

## Integration Details

- Use official Razorpay Node.js SDK on backend.
- Use Razorpay Checkout JS SDK on frontend.
- Ensure all API calls are authenticated and secure.
- Handle errors gracefully with user-friendly messages.
- Store payment settings securely and allow super admin to update anytime.
- Use environment variables for fallback or initial Razorpay keys if needed.

---

## File Changes Summary

| File/Folder                      | Changes/Features                                                                                  |
|---------------------------------|-------------------------------------------------------------------------------------------------|
| `server/`                       | Add `payment_settings` table migration, implement payment APIs, Razorpay integration             |
| `server/index.js`               | Add new routes for payment settings, order creation, payment verification                        |
| `src/app/admin/payment-settings.tsx` | New admin page for managing Razorpay keys and payment settings                                  |
| `src/app/dashboard/packages.tsx`| Add online payment button and payment flow integration                                           |
| `src/lib/payment.ts`            | Add Razorpay API integration utilities                                                          |
| `src/hooks/usePayment.ts`       | Hook for payment process and status handling                                                    |

---

## UI/UX Considerations

- Admin payment settings page: clean form with labeled inputs, save button, and feedback messages.
- Customer payment flow: seamless modal popup for Razorpay checkout, clear success/failure feedback.
- Alerts: non-intrusive toast notifications for payment events.
- Maintain consistent branding and styling with the rest of the app.

---

## Error Handling & Best Practices

- Validate all inputs on frontend and backend.
- Securely store and handle Razorpay secret key.
- Verify payment signatures strictly.
- Handle network errors and payment failures gracefully.
- Log payment events for audit and debugging.
- Use HTTPS for all API calls involving payments.

---

## Next Steps

- Implement database migration for `payment_settings`.
- Implement backend APIs for payment settings and Razorpay order/payment handling.
- Create frontend admin payment settings page.
- Integrate Razorpay checkout in customer package purchase flow.
- Test payment flows end-to-end including success, failure, and edge cases.

---

Please confirm this detailed plan or provide any additional requirements before I proceed with implementation.
