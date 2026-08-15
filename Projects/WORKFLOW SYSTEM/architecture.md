# OGIGRID Workflow System — Architecture

## 1. Purpose

The OGIGRID Workflow System is an internal company workflow and productivity platform for managing employees, projects, tasks, schedules, meetings, documents, daily reports, notifications, and administrative operations.

The system is intended for real company use, not merely as a demonstration.

The application should provide one connected workspace where administrators can manage company work and employees can manage and track their own assigned work.

---

## 2. Core Architecture

The application is a web-based Next.js application.

### Current MVP

The current MVP uses:

- Next.js
- React
- TypeScript
- Tailwind CSS
- Client-side application state
- Local persistence for the MVP

The MVP is designed so that the persistence/authentication layer can later be replaced with a real backend and database without rebuilding the user interface.

### Production Direction

For real company-wide deployment, the system should eventually use:

- Real authentication
- Secure password handling
- A shared database
- Server-side authorization
- Persistent server-side notifications
- API/server actions for data operations
- Multi-device synchronization
- Production deployment

The local MVP architecture must not be treated as the final production authentication/database architecture.

---

## 3. User Roles

There are two workspace roles:

### Administrator

Administrators manage company-wide workflow.

They can:

- Manage employees
- Create projects
- Manage projects
- Create tasks
- Assign tasks
- Reassign tasks
- Manage meetings
- Manage documents
- View company-wide daily reports
- View company-wide dashboard information
- Manage workspace settings
- Promote employees to Administrator
- Demote other Administrators to Employee
- Remove/deactivate employees
- View relevant company activity
- Manage their own account

### Employee

Employees manage their own assigned work.

They can:

- View their profile
- Edit their own profile where permitted
- View assigned tasks
- Update permitted task statuses
- View relevant projects
- View their schedule
- Attend/view relevant meetings
- Access permitted documents
- Submit daily reports
- View their own reports
- Receive personal notifications
- Manage their own account
- Log out
- Delete their own account where permitted

Employees must not have access to Administrator-only operations.

---

## 4. Primary Administrator / Workspace Owner

The first Administrator who creates the workspace is the Primary Administrator.

The Primary Administrator has protected ownership of the workspace.

### Primary Administrator rules

The Primary Administrator:

- Cannot be removed
- Cannot be deleted by another user
- Cannot be demoted to Employee
- Must always remain an Administrator
- Cannot be removed through normal employee-management actions

The system must never allow the workspace to reach a state with zero Administrators.

Other Administrators can be promoted or demoted by an authorized Administrator.

---

## 5. Authentication

The system requires:

- Registration
- Login
- Logout
- Current-user/session state
- User profile
- Role identification
- Account management

### Registration

If no workspace/user exists:

The first account becomes the Primary Administrator.

After the workspace exists:

New users may register according to the workspace's configured registration rules.

Duplicate email addresses must not be allowed.

### Production authentication

The MVP's local authentication is temporary.

Production authentication must use:

- Secure password hashing
- Server-side authentication
- Secure session/token handling
- Server-side role verification
- Shared account storage

Never treat browser localStorage as secure production authentication.

---

## 6. Employee Management

Administrators manage employees through the Employees section.

Employee management includes:

- Add employee
- View employee
- View employee profile
- Edit employee
- Remove/deactivate employee
- Promote employee to Administrator
- Demote Administrator to Employee

### Employee removal

Removing an employee must not create broken references.

If the employee has active tasks, the system should allow the Administrator to:

- Reassign the tasks
- Or safely leave them unassigned

Historical records should remain intact where appropriate.

---

## 7. Projects

Projects represent larger company initiatives or pieces of work.

A project can contain:

- Name
- Description
- Status
- Progress
- Members
- Tasks
- Dates
- Relevant activity

Projects must use live task data.

### Project progress

Project progress must be calculated from its tasks rather than manually hardcoded.

Conceptually:

Completed tasks / Total tasks × 100

Examples:

- 0/5 completed → 0%
- 1/5 completed → 20%
- 3/5 completed → 60%
- 5/5 completed → 100%

Project status should reflect progress:

- 0% → Not Started
- 1–99% → In Progress
- 100% → Completed

When task status changes, project progress must update automatically.

---

## 8. Tasks

Tasks are the primary unit of work.

A task can contain:

- Title
- Description
- Project
- Assignee
- Status
- Priority
- Deadline
- Creation information
- Completion information

### Task lifecycle

Typical statuses include:

- Not Started
- In Progress
- Completed

Administrators can:

- Create tasks
- Assign tasks
- Reassign tasks
- Edit tasks
- Delete tasks
- Change task status

Employees can:

- View assigned tasks
- Open task details
- Update permitted statuses
- Complete assigned work

### Task relationships

Tasks must remain connected to:

- Employees
- Projects
- Schedule
- Notifications
- Dashboard metrics
- Activity history

There should be one source of truth for task data.

---

## 9. Schedule

The Schedule provides a time-based view of work.

Tasks with relevant dates/deadlines should appear in the appropriate schedule view.

Administrators can see the team schedule.

Employees should primarily see their own assigned work.

When a task is:

- Assigned
- Reassigned
- Edited
- Deleted
- Rescheduled

the Schedule should update accordingly.

---

## 10. Meetings

Meetings are company/workflow events.

Meeting data may include:

- Title
- Date
- Time
- Participants
- Description
- Related project/work where applicable

Administrators can manage company meetings.

Employees should only see meetings relevant to them according to permissions.

---

## 11. Documents

Documents provide access to company/workflow documentation.

Document access must respect user permissions.

Administrators can manage appropriate company documents.

Employees should only access documents they are permitted to access.

---

## 12. Daily Reports

Employees submit daily reports describing their work.

Employees:

- Submit their own report
- Cannot submit a report pretending to be another employee
- Can view their own submitted reports

Administrators:

- Can view company-wide daily reports
- Can monitor reporting activity

Daily report submission should generate an appropriate personal notification and activity record.

---

## 13. Notifications

Notifications are user-specific.

An employee must only receive notifications intended for that employee.

Examples:

### Task assignment

If an Administrator assigns a task to Employee A:

Employee A receives:

> A task has been assigned to you.

Employee B must not receive Employee A's private notification.

### Daily report

If Employee A submits a daily report:

Employee A receives a confirmation such as:

> Your daily report for today was submitted successfully. Well done!

### Task completion

When an employee completes a task, appropriate activity/notifications should be generated.

### Notification properties

Notifications should support:

- Recipient
- Message
- Type
- Timestamp
- Read/unread state
- Optional related entity
- Navigation to the related task/report/profile where appropriate

---

## 14. Activity History

Important workflow events should be trackable.

Examples:

- Employee created
- Employee removed
- Employee promoted
- Employee demoted
- Project created
- Task created
- Task assigned
- Task reassigned
- Task completed
- Daily report submitted

Administrators should have appropriate visibility into company activity.

Employees should only see activity appropriate to their permissions.

---

## 15. Dashboard

The Dashboard is role-aware.

### Administrator Dashboard

Shows company-wide information such as:

- Active projects
- Pending tasks
- Overdue tasks
- Project progress
- Daily reports
- Reports missing today
- Meetings
- Employees
- Recent activity

### Employee Dashboard

Shows personalized information such as:

- Assigned tasks
- Relevant projects
- Personal reports
- Relevant meetings
- Personal notifications
- Current work progress

Dashboard metrics must be derived from live application data.

Do not hardcode operational metrics.

---

## 16. Search

Search should provide access to relevant workspace information.

Search results must respect permissions.

Employees must not be able to use search to access Administrator-only or restricted information.

---

## 17. Profile and Account

The top-right user menu represents the current authenticated user.

It should provide:

- User initials/avatar
- Name
- Email
- Role
- View Profile
- Account/Settings
- Logout

The current user's information must come from the authenticated user/session state.

It must never be hardcoded.

---

## 18. Settings

Settings should contain user-specific account settings.

Administrators may also have access to workspace-level settings.

Employees must not be given Administrator-only configuration controls.

---

## 19. Navigation

The main application navigation includes the major workflow areas:

- Dashboard
- Tasks
- Projects
- Schedule
- Meetings
- Documents
- Daily Reports
- Employees
- Settings

Detail pages should provide clear navigation back to their parent pages.

Examples:

Project detail → Projects

Task detail → Tasks

Employee profile → Employees

---

## 20. Data Consistency

The system must behave as one connected application.

Examples:

### Assigning a task

Assign task →

Task appears for employee →

Employee receives notification →

Task appears in appropriate project →

Task appears in schedule where applicable →

Dashboard metrics update.

### Completing a task

Complete task →

Task status changes →

Project progress recalculates →

Dashboard updates →

Relevant activity/notifications update.

### Submitting daily report

Submit report →

Report is stored →

Employee receives confirmation notification →

Administrator can see report →

Dashboard/report metrics update.

### Removing employee

Remove/deactivate employee →

Employee status changes →

Active assignments are handled →

Project memberships update where appropriate →

Schedule updates →

Historical records remain safe.

### Changing role

Promote/demote user →

Role changes →

Permissions update →

Navigation updates →

Admin-only access updates.

---

## 21. Persistence

The MVP currently uses client-side persistence.

This is acceptable for development/MVP testing.

For actual company deployment, data must be moved to shared server-side storage.

Production data should include at minimum:

- Users
- Roles
- Workspace
- Projects
- Tasks
- Project memberships
- Meetings
- Documents
- Daily reports
- Notifications
- Activity history

The production database must become the single source of truth.

---

## 22. Production Backend Direction

The production architecture should separate:

### Frontend

Next.js / React / TypeScript UI.

### Authentication

Secure server-side authentication and sessions.

### API / Server Layer

Handles:

- Authorization
- CRUD operations
- Business rules
- Notifications
- Validation
- Audit/activity records

### Database

Persistent shared storage for all company data.

### Deployment

The application must be deployable as a production web application accessible to OGIGRID employees from their own devices.

---

## 23. Critical Business Rules

These rules must never be broken:

1. There must always be at least one Administrator.
2. The Primary Administrator cannot be removed.
3. The Primary Administrator cannot be demoted.
4. Employees cannot perform Administrator-only actions.
5. Employees only see their own private notifications and reports where applicable.
6. Administrators can manage employees.
7. Administrators can promote employees to Administrator.
8. Administrators can demote other Administrators where permitted.
9. Task assignment must identify the actual employee.
10. Project progress must come from real task completion.
11. Operational metrics must not be hardcoded.
12. Removing an employee must not corrupt historical records.
13. The current user must never be hardcoded.
14. The application must keep one source of truth for shared data.
15. Production authentication must not rely on insecure browser-only storage.

---

## 24. Design and Branding

The application belongs to OGIGRID.

The interface should remain:

- Clean
- Professional
- Modern
- Consistent
- Responsive
- Easy for employees to understand

Use the official OGIGRID logo asset supplied for the project.

Do not invent a replacement brand identity.

---

## 25. Development Principles

Any AI agent or developer working on this project should:

- Inspect existing code before changing it.
- Preserve working functionality.
- Avoid unnecessary rewrites.
- Avoid introducing fake/hardcoded operational data.
- Keep business logic centralized.
- Maintain role-based authorization.
- Keep components reusable.
- Keep the persistence layer replaceable.
- Run the production build after significant changes.
- Fix TypeScript and compile errors before considering a change complete.
- Test affected workflows after changes.
- Never remove an existing feature simply to make a new feature easier.

## 26. Database Architecture

The workflow system uses PostgreSQL as its relational database.

The database is responsible for persistent storage of application data. Unlike client-side state or local browser storage, PostgreSQL allows the data to remain available after a user logs out, closes the browser, or accesses the application again later.

### 26.1 Local PostgreSQL Development

The current development environment uses a local PostgreSQL 16 instance.

The development database is:

- Database: `ogigrid_workflow`
- Database engine: PostgreSQL
- ORM / database toolkit: Prisma

The local database is used during development so that the database structure and relationships can be understood and tested before moving to a hosted production database.

The architecture is intentionally designed so the database can later be moved from local PostgreSQL to a hosted PostgreSQL provider without changing the fundamental relational model.

### 26.2 Database Concepts

The database is divided into tables, with each table representing a particular type of information.

A row represents one record in a table.

For example, a `users` table may contain one row for Temidayo and another row for Femi.

A column represents a specific property of a record, such as:

- `id`
- `name`
- `email`
- `role`
- `createdAt`

An `id` is used to uniquely identify a record.

### 26.3 Primary Keys

Each major entity has a primary key.

The primary key uniquely identifies a record within its table.

For example:

```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL
);
```

In this example, `id` is the primary key.

The database guarantees that two users cannot have the same primary key.

### 26.4 Foreign Keys and Relationships

Foreign keys connect records between related tables.

For example, a task can belong to a project and be assigned to a user.

Conceptually:

```text
User
  |
  | user_id
  v
Task
  |
  | project_id
  v
Project
```

The `user_id` and `project_id` values stored in the task record reference records in other tables.

This prevents the application from treating related data as unrelated pieces of information.

### 26.5 Many-to-Many Relationships

Some relationships require a separate join table.

For example, multiple employees can belong to multiple projects.

Instead of storing multiple project IDs directly inside a user record, a join table can represent the relationship:

```text
Users
  |
  | user_id
  v
Project Members
  ^
  | project_id
  |
Projects
```

This keeps the database normalized and makes the relationship easier to query and maintain.

### 26.6 Constraints

Database constraints are used to protect the integrity of the data.

Examples include:

- `PRIMARY KEY` ensures unique record identification.
- `UNIQUE` prevents duplicate values such as duplicate email addresses.
- `NOT NULL` prevents required fields from being empty.
- `FOREIGN KEY` prevents references to records that do not exist.

For example, a task should not be allowed to reference a project that does not exist.

The database should enforce important rules rather than relying entirely on the frontend.

### 26.7 Primary Administrator Protection

The system has a special Primary Administrator role.

Only one Primary Administrator is allowed.

This rule is enforced at the database/server level rather than only hiding a button in the frontend.

The Primary Administrator cannot be removed through normal employee-management operations.

An administrator can promote another employee to administrator, but the Primary Administrator remains protected.

### 26.8 Persistent Authentication

Authentication is now server-side.

User sessions are stored using the database and protected using HTTP-only cookies.

This means authentication is no longer dependent on client-side application state alone.

The expected flow is:

```text
User signs up
     ↓
User record stored in PostgreSQL
     ↓
User logs in
     ↓
Server creates authenticated session
     ↓
Session stored in database
     ↓
HTTP-only cookie identifies the session
     ↓
User can return later and log in again
```

Passwords are not stored as plain text.

### 26.9 Prisma

Prisma is used as the application's database access layer.

The general architecture is:

```text
Next.js UI
     ↓
Server/API Routes
     ↓
Repository / Data Access Layer
     ↓
Prisma
     ↓
PostgreSQL
```

This separates the user interface from direct database operations.

The frontend does not directly connect to PostgreSQL.

### 26.10 Migrations

Database schema changes are managed through migrations.

A migration records structural changes to the database, such as:

- creating tables
- adding columns
- adding relationships
- adding constraints
- changing indexes

This makes database changes repeatable and allows the development database to be recreated consistently.

### 26.11 Indexes and Efficient Queries

Indexes can be used to make frequently searched fields faster to find.

Instead of scanning every record in a large table, PostgreSQL can use an index to locate matching records more efficiently.

For example, an index on a task's assigned user can make queries for a user's assigned tasks faster.

Indexes should be added to fields that are frequently searched, filtered, joined, or used for lookups.

### 26.12 Current Database Direction

The current system uses local PostgreSQL for development and learning.

The production system can later use a hosted PostgreSQL database.

The important principle is that PostgreSQL remains the relational database technology while the hosting location can change.

```text
Development

Next.js
   ↓
Prisma
   ↓
Local PostgreSQL


Production

Next.js
   ↓
Prisma
   ↓
Hosted PostgreSQL
```

This approach allows the developer to understand the database fundamentals locally before introducing managed database infrastructure.

## 27. Database Learning Notes

The database work introduced the following concepts:

- Database
- Tables
- Rows / records
- Columns / fields
- Primary keys
- Foreign keys
- Relationships
- One-to-many relationships
- Many-to-many relationships
- Join tables
- Constraints
- Unique constraints
- Referential integrity
- Indexes
- Migrations
- ORM
- Prisma
- PostgreSQL
- Server-side authentication
- Persistent sessions

The main principle learned is that the database is not simply a place to store files. It is a structured system that stores related records and enforces rules about how those records can be created, connected, updated, and deleted.


The goal is a reliable connected company workflow system, not merely a collection of visually complete pages.