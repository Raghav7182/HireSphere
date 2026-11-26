# HireSphere — End-to-End Project Flow & Milestones

## 1. System Overview & User Roles
HireSphere is a full-stack MERN Applicant Tracking System (ATS) supporting three roles across the complete recruitment lifecycle:

- **Admin:** Manages/monitors the platform, approves or rejects company registrations, moderates or removes job postings, reviews reported jobs/users, suspends/deactivates accounts, and views platform-wide analytics.
- **Recruiter / Employer:** Registers and maintains a company profile, creates/edits/closes/deletes job postings, reviews applicants and resumes, shortlists/rejects candidates, updates pipeline statuses, schedules/reschedules/cancels interviews, submits interview evaluations, and tracks hiring statistics.
- **Job Seeker / Candidate:** Registers/logs in, builds a profile (education, skills, certifications, projects, work experience, links) with PDF resume upload, searches/filters/saves jobs, applies to open jobs, tracks application status history, views upcoming interviews, and receives notifications.

---

## 2. Core Functional Workflows

### Flow A: Authentication & Role-Based Access Control
1. User registers (`POST /api/auth/register`) or logs in (`POST /api/auth/login`) with email and password (`bcrypt` hashing + JWT).
2. Protected middleware verifies JWT, loads current user (`GET /api/auth/me`), blocks deactivated/suspended accounts, and enforces role permissions (`admin`, `recruiter`, `candidate`).
3. Forgot-password and password-reset flow validates tokens and updates credentials securely.
4. Frontend stores auth state in Redux Toolkit and restricts routes by role.

### Flow B: Candidate Profile & Resume Management
1. Candidate updates personal details, profile picture, education, experience, skills, certifications, projects, achievements, and LinkedIn/GitHub links.
2. Candidate uploads or replaces a PDF resume (validated for MIME type, file size, and permissions via Multer + cloud storage in production).
3. System calculates profile completion percentage automatically.

### Flow C: Company Onboarding & Job Management
1. Recruiter registers a company (`POST /api/companies`) with name, logo, description, industry, location, and website (`verificationStatus` defaults to pending).
2. Admin approves or rejects the company registration.
3. Authorized recruiters create (`POST /api/jobs`), update (`PUT /api/jobs/:id`), close, or delete (`DELETE /api/jobs/:id`) jobs with title, description, job type, location, experience, salary range, required skills, deadline, vacancy count, and status.

### Flow D: Job Search, Filtering & Wishlist
1. Candidate searches open jobs (`GET /api/jobs`) by title, company, or keywords.
2. Supports combined server-side filters (location, salary, experience, job type, company), recency sorting, pagination, and clear-filters reset.
3. Candidate saves or removes jobs in `SavedJobs`.

### Flow E: Job Application & Pipeline Tracking
1. Candidate views full job details and applies (`POST /api/applications`) using a saved resume or new PDF upload.
2. Server validates that the job is open and before its deadline, blocks duplicate `(jobId, candidateId)` applications, records initial status `Applied` with timestamp/history, and notifies the recruiter.
3. Candidate tracks their applications (`GET /api/applications/my`).
4. Recruiter views applicants per job (`GET /api/applications/job/:jobId`), filters by skills/experience/status, adds internal notes, and transitions statuses (`PATCH /api/applications/:id/status`):
   `Applied` -> `Under Review` -> `Shortlisted` -> `Interview Scheduled` -> `Selected` / `Rejected`
5. Status changes trigger candidate notifications and `ActivityLogs` entries.

### Flow F: Interview Scheduling & Multi-Round Evaluation
1. Recruiter schedules an interview (`POST /api/interviews`) with interviewer assignment, date, time, interview type, and meeting link/location; supports rescheduling and cancellation.
2. Candidate and recruiter view upcoming interviews (`GET /api/interviews/my`) and receive notifications.
3. Interviewers submit round feedback (`POST /api/feedback`) with ratings (Technical Knowledge 30, Problem Solving 25, Communication 20, Practical Skills 15, Confidence & Approach 10 = 100 total), remarks, and recommendations.
4. Recruiter records the final hiring decision (`Selected` or `Rejected`).

---

## 3. Phased Build Plan
- [ ] **Phase 1 — Foundation & Auth:** Backend/Frontend scaffolding, MongoDB connection, `Users` model, JWT + bcrypt auth, RBAC middleware, centralized error handler, and Postman auth tests.
- [ ] **Phase 2 — Profiles, Companies & Jobs:** `CandidateProfiles` + Multer PDF upload, `Companies` + Admin verification, `Jobs` CRUD, server-side search/filter/pagination, and `SavedJobs`.
- [ ] **Phase 3 — Applications & Pipeline:** `Applications` model with unique index, deadline/status guards, candidate tracker, recruiter applicant table, status transitions, and `Notifications`.
- [ ] **Phase 4 — Interviews & Scorecards:** `Interviews` scheduling/reschedule/cancel, multi-round `InterviewFeedback` scoring, `ActivityLogs`, and Admin/Recruiter/Candidate dashboards.
- [ ] **Phase 5 — Deployment & Optional Challenge Features:** Aggregation analytics, resume parsing (with user review), skill match percentage display, Socket.IO real-time notifications, calendar integration, offer letter PDF generation, and Vercel/Render/Atlas deployment.