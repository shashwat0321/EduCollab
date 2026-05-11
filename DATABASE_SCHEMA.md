# EduCollab - Database Schema Design

## 📊 Database Overview

**Database Type:** SQL Server  
**Naming Convention:** PascalCase for tables, PascalCase for columns  
**Primary Keys:** GUID (uniqueidentifier) with default NEWID()  
**Timestamps:** All tables include CreatedAt, UpdatedAt (nullable)  
**Soft Delete:** DeletedAt (nullable) for soft delete capability

---

## 🗂️ Entity Relationship Diagram (ERD)

```
┌─────────────────┐         ┌──────────────────┐         ┌─────────────────┐
│   AspNetUsers   │         │     Courses      │         │   Categories    │
│  (Identity)     │         │                  │         │                 │
├─────────────────┤         ├──────────────────┤         ├─────────────────┤
│ Id (PK)         │────┐    │ Id (PK)          │    ┌────│ Id (PK)         │
│ UserName        │    │    │ Title            │    │    │ Name            │
│ Email           │    │    │ Description      │    │    │ Description     │
│ PasswordHash    │    │    │ Price            │    │    │ CreatedAt       │
│ FirstName       │    │    │ Level            │    │    │ UpdatedAt       │
│ LastName        │    │    │ InstructorId(FK) │────┘    └─────────────────┘
│ PhoneNumber     │    │    │ CategoryId (FK)  │──────────────┘
│ Bio             │    │    │ Status           │
│ ProfilePicture  │    │    │ CreatedAt        │
│ CreatedAt       │    │    │ UpdatedAt        │
└─────────────────┘    │    │ DeletedAt        │
                       │    └──────────────────┘
                       │              │
                       │              │ 1:N
                       │              ↓
                       │    ┌──────────────────┐
                       │    │   Enrollments    │
                       │    │   (Join Table)   │
                       │    ├──────────────────┤
                       │    │ Id (PK)          │
                       └────│ StudentId (FK)   │
                            │ CourseId (FK)    │
                            │ EnrolledAt       │
                            │ Status           │
                            │ CompletedAt      │
                            └──────────────────┘

┌──────────────────┐         ┌──────────────────┐         ┌─────────────────┐
│   Assignments    │         │   Submissions    │         │     Grades      │
│                  │         │                  │         │                 │
├──────────────────┤         ├──────────────────┤         ├─────────────────┤
│ Id (PK)          │────┐    │ Id (PK)          │    ┌────│ Id (PK)         │
│ CourseId (FK)    │    └────│ AssignmentId(FK) │────┘    │ SubmissionId(FK)│
│ Title            │         │ StudentId (FK)   │         │ Score           │
│ Description      │         │ FilePath         │         │ MaxScore        │
│ DueDate          │         │ SubmittedAt      │         │ Feedback        │
│ MaxScore         │         │ Status           │         │ GradedBy (FK)   │
│ Status           │         │ CreatedAt        │         │ GradedAt        │
│ CreatedAt        │         │ UpdatedAt        │         │ CreatedAt       │
│ UpdatedAt        │         └──────────────────┘         └─────────────────┘
└──────────────────┘

┌──────────────────┐         ┌──────────────────┐
│     Comments     │         │     Reviews      │
│                  │         │                  │
├──────────────────┤         ├──────────────────┤
│ Id (PK)          │         │ Id (PK)          │
│ CourseId (FK)    │         │ CourseId (FK)    │
│ UserId (FK)      │         │ StudentId (FK)   │
│ ParentId (FK)    │ (Self)  │ Rating           │
│ Content          │         │ ReviewText       │
│ CreatedAt        │         │ CreatedAt        │
│ UpdatedAt        │         │ UpdatedAt        │
└──────────────────┘         └──────────────────┘
```

---

## 📋 Detailed Table Definitions

### 1. AspNetUsers (Extended Identity)

**Purpose:** Store user information (extends ASP.NET Core Identity)

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| Id | uniqueidentifier | PK, Default: NEWID() | User unique identifier |
| UserName | nvarchar(256) | NOT NULL, UNIQUE | Username for login |
| NormalizedUserName | nvarchar(256) | Index | Uppercase username |
| Email | nvarchar(256) | NOT NULL, UNIQUE | User email |
| NormalizedEmail | nvarchar(256) | Index | Uppercase email |
| EmailConfirmed | bit | NOT NULL, Default: 0 | Email verification status |
| PasswordHash | nvarchar(MAX) | NULL | Hashed password |
| SecurityStamp | nvarchar(MAX) | NULL | Security stamp (Identity) |
| ConcurrencyStamp | nvarchar(MAX) | NULL | Concurrency token |
| PhoneNumber | nvarchar(50) | NULL | User phone number |
| PhoneNumberConfirmed | bit | NOT NULL, Default: 0 | Phone verification status |
| TwoFactorEnabled | bit | NOT NULL, Default: 0 | 2FA enabled flag |
| LockoutEnd | datetimeoffset | NULL | Lockout end time |
| LockoutEnabled | bit | NOT NULL, Default: 1 | Lockout feature enabled |
| AccessFailedCount | int | NOT NULL, Default: 0 | Failed login attempts |
| **FirstName** | nvarchar(100) | NOT NULL | User first name |
| **LastName** | nvarchar(100) | NOT NULL | User last name |
| **Bio** | nvarchar(500) | NULL | User biography |
| **ProfilePictureUrl** | nvarchar(500) | NULL | Profile picture URL |
| **CreatedAt** | datetime2 | NOT NULL, Default: GETUTCDATE() | Record creation time |
| **UpdatedAt** | datetime2 | NULL | Last update time |

**Indexes:**
- `IX_AspNetUsers_NormalizedUserName` (UNIQUE)
- `IX_AspNetUsers_NormalizedEmail`

**Note:** Bold columns are custom extensions to Identity schema

---

### 2. Categories

**Purpose:** Course categorization

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| Id | uniqueidentifier | PK, Default: NEWID() | Category unique identifier |
| Name | nvarchar(100) | NOT NULL, UNIQUE | Category name |
| Description | nvarchar(500) | NULL | Category description |
| CreatedAt | datetime2 | NOT NULL, Default: GETUTCDATE() | Record creation time |
| UpdatedAt | datetime2 | NULL | Last update time |

**Indexes:**
- `IX_Categories_Name` (UNIQUE)

**Sample Data:**
- Web Development
- Mobile Development
- Data Science
- DevOps
- Cloud Computing
- Cybersecurity

---

### 3. Courses

**Purpose:** Store course information

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| Id | uniqueidentifier | PK, Default: NEWID() | Course unique identifier |
| Title | nvarchar(200) | NOT NULL | Course title |
| Description | nvarchar(MAX) | NOT NULL | Course description |
| ShortDescription | nvarchar(500) | NULL | Brief description |
| Price | decimal(18,2) | NOT NULL, Default: 0.00 | Course price |
| Level | int | NOT NULL | Course difficulty (enum) |
| DurationInHours | int | NULL | Estimated duration |
| Language | nvarchar(50) | NOT NULL, Default: 'English' | Course language |
| Status | int | NOT NULL, Default: 0 | Course status (enum) |
| ThumbnailUrl | nvarchar(500) | NULL | Course thumbnail URL |
| InstructorId | uniqueidentifier | FK → AspNetUsers(Id) | Course instructor |
| CategoryId | uniqueidentifier | FK → Categories(Id) | Course category |
| CreatedAt | datetime2 | NOT NULL, Default: GETUTCDATE() | Record creation time |
| UpdatedAt | datetime2 | NULL | Last update time |
| DeletedAt | datetime2 | NULL | Soft delete timestamp |

**Enums:**
- **Level:** Beginner(0), Intermediate(1), Advanced(2), Expert(3)
- **Status:** Draft(0), Published(1), Archived(2), UnderReview(3)

**Indexes:**
- `IX_Courses_InstructorId`
- `IX_Courses_CategoryId`
- `IX_Courses_Status`
- `IX_Courses_DeletedAt`

**Constraints:**
- `FK_Courses_AspNetUsers_InstructorId` ON DELETE NO ACTION
- `FK_Courses_Categories_CategoryId` ON DELETE CASCADE

---

### 4. Enrollments

**Purpose:** Many-to-Many relationship between Students and Courses

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| Id | uniqueidentifier | PK, Default: NEWID() | Enrollment unique identifier |
| StudentId | uniqueidentifier | FK → AspNetUsers(Id) | Enrolled student |
| CourseId | uniqueidentifier | FK → Courses(Id) | Enrolled course |
| EnrolledAt | datetime2 | NOT NULL, Default: GETUTCDATE() | Enrollment timestamp |
| Status | int | NOT NULL, Default: 0 | Enrollment status (enum) |
| Progress | decimal(5,2) | NOT NULL, Default: 0.00 | Completion percentage (0-100) |
| CompletedAt | datetime2 | NULL | Course completion timestamp |
| CertificateUrl | nvarchar(500) | NULL | Certificate URL (if completed) |

**Enums:**
- **Status:** Active(0), Completed(1), Dropped(2), Suspended(3)

**Indexes:**
- `IX_Enrollments_StudentId`
- `IX_Enrollments_CourseId`
- `UX_Enrollments_Student_Course` (UNIQUE on StudentId, CourseId)

**Constraints:**
- `FK_Enrollments_AspNetUsers_StudentId` ON DELETE CASCADE
- `FK_Enrollments_Courses_CourseId` ON DELETE CASCADE

---

### 5. Assignments

**Purpose:** Course assignments/tasks

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| Id | uniqueidentifier | PK, Default: NEWID() | Assignment unique identifier |
| CourseId | uniqueidentifier | FK → Courses(Id) | Parent course |
| Title | nvarchar(200) | NOT NULL | Assignment title |
| Description | nvarchar(MAX) | NOT NULL | Assignment description |
| Instructions | nvarchar(MAX) | NULL | Detailed instructions |
| DueDate | datetime2 | NULL | Submission deadline |
| MaxScore | decimal(5,2) | NOT NULL, Default: 100.00 | Maximum achievable score |
| Status | int | NOT NULL, Default: 0 | Assignment status (enum) |
| AllowLateSubmission | bit | NOT NULL, Default: 0 | Late submission allowed |
| AttachmentUrl | nvarchar(500) | NULL | Assignment file URL |
| CreatedAt | datetime2 | NOT NULL, Default: GETUTCDATE() | Record creation time |
| UpdatedAt | datetime2 | NULL | Last update time |

**Enums:**
- **Status:** Draft(0), Published(1), Closed(2)

**Indexes:**
- `IX_Assignments_CourseId`
- `IX_Assignments_DueDate`

**Constraints:**
- `FK_Assignments_Courses_CourseId` ON DELETE CASCADE

---

### 6. Submissions

**Purpose:** Student assignment submissions

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| Id | uniqueidentifier | PK, Default: NEWID() | Submission unique identifier |
| AssignmentId | uniqueidentifier | FK → Assignments(Id) | Parent assignment |
| StudentId | uniqueidentifier | FK → AspNetUsers(Id) | Submitting student |
| Content | nvarchar(MAX) | NULL | Submission text content |
| FileUrl | nvarchar(500) | NULL | Submitted file URL |
| SubmittedAt | datetime2 | NOT NULL, Default: GETUTCDATE() | Submission timestamp |
| Status | int | NOT NULL, Default: 0 | Submission status (enum) |
| IsLate | bit | NOT NULL, Default: 0 | Late submission flag |
| CreatedAt | datetime2 | NOT NULL, Default: GETUTCDATE() | Record creation time |
| UpdatedAt | datetime2 | NULL | Last update time |

**Enums:**
- **Status:** Submitted(0), Graded(1), Resubmitted(2), Rejected(3)

**Indexes:**
- `IX_Submissions_AssignmentId`
- `IX_Submissions_StudentId`
- `UX_Submissions_Assignment_Student` (UNIQUE on AssignmentId, StudentId)

**Constraints:**
- `FK_Submissions_Assignments_AssignmentId` ON DELETE CASCADE
- `FK_Submissions_AspNetUsers_StudentId` ON DELETE NO ACTION

---

### 7. Grades

**Purpose:** Assignment grades and feedback

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| Id | uniqueidentifier | PK, Default: NEWID() | Grade unique identifier |
| SubmissionId | uniqueidentifier | FK → Submissions(Id), UNIQUE | Graded submission |
| Score | decimal(5,2) | NOT NULL | Awarded score |
| MaxScore | decimal(5,2) | NOT NULL | Maximum score (copied from Assignment) |
| Feedback | nvarchar(MAX) | NULL | Instructor feedback |
| GradedById | uniqueidentifier | FK → AspNetUsers(Id) | Grading instructor |
| GradedAt | datetime2 | NOT NULL, Default: GETUTCDATE() | Grading timestamp |
| CreatedAt | datetime2 | NOT NULL, Default: GETUTCDATE() | Record creation time |

**Indexes:**
- `IX_Grades_SubmissionId` (UNIQUE)
- `IX_Grades_GradedById`

**Constraints:**
- `FK_Grades_Submissions_SubmissionId` ON DELETE CASCADE
- `FK_Grades_AspNetUsers_GradedById` ON DELETE NO ACTION
- `CK_Grades_ScoreRange` CHECK (Score >= 0 AND Score <= MaxScore)

---

### 8. Comments

**Purpose:** Course discussions (nested comments)

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| Id | uniqueidentifier | PK, Default: NEWID() | Comment unique identifier |
| CourseId | uniqueidentifier | FK → Courses(Id) | Parent course |
| UserId | uniqueidentifier | FK → AspNetUsers(Id) | Comment author |
| ParentCommentId | uniqueidentifier | FK → Comments(Id), NULL | Parent comment (for replies) |
| Content | nvarchar(1000) | NOT NULL | Comment text |
| CreatedAt | datetime2 | NOT NULL, Default: GETUTCDATE() | Record creation time |
| UpdatedAt | datetime2 | NULL | Last update time |

**Indexes:**
- `IX_Comments_CourseId`
- `IX_Comments_UserId`
- `IX_Comments_ParentCommentId`

**Constraints:**
- `FK_Comments_Courses_CourseId` ON DELETE CASCADE
- `FK_Comments_AspNetUsers_UserId` ON DELETE NO ACTION
- `FK_Comments_Comments_ParentCommentId` ON DELETE NO ACTION (Self-referencing)

---

### 9. Reviews

**Purpose:** Course ratings and reviews

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| Id | uniqueidentifier | PK, Default: NEWID() | Review unique identifier |
| CourseId | uniqueidentifier | FK → Courses(Id) | Reviewed course |
| StudentId | uniqueidentifier | FK → AspNetUsers(Id) | Reviewing student |
| Rating | int | NOT NULL | Star rating (1-5) |
| ReviewText | nvarchar(1000) | NULL | Review text |
| CreatedAt | datetime2 | NOT NULL, Default: GETUTCDATE() | Record creation time |
| UpdatedAt | datetime2 | NULL | Last update time |

**Indexes:**
- `IX_Reviews_CourseId`
- `IX_Reviews_StudentId`
- `UX_Reviews_Course_Student` (UNIQUE on CourseId, StudentId)

**Constraints:**
- `FK_Reviews_Courses_CourseId` ON DELETE CASCADE
- `FK_Reviews_AspNetUsers_StudentId` ON DELETE NO ACTION
- `CK_Reviews_RatingRange` CHECK (Rating >= 1 AND Rating <= 5)

---

## 🔗 Relationship Summary

| Relationship | Type | Description |
|-------------|------|-------------|
| User → Courses (as Instructor) | One-to-Many | One instructor can create many courses |
| Category → Courses | One-to-Many | One category contains many courses |
| User (Student) ↔ Courses | Many-to-Many | Students enroll in multiple courses (via Enrollments) |
| Course → Assignments | One-to-Many | One course has many assignments |
| Assignment → Submissions | One-to-Many | One assignment receives many submissions |
| Submission → Grade | One-to-One | Each submission gets one grade |
| Course → Comments | One-to-Many | One course has many comments |
| Comment → Comment (Parent) | One-to-Many | Comments can have nested replies |
| Course → Reviews | One-to-Many | One course has many reviews |
| User → Reviews | One-to-Many | One user can write many reviews |

---

## 📐 Database Constraints & Rules

### Business Rules Enforced by DB:
1. ✅ Student cannot enroll in the same course twice (UNIQUE constraint)
2. ✅ Student cannot submit assignment more than once (handled by application logic)
3. ✅ Score cannot exceed MaxScore (CHECK constraint)
4. ✅ Rating must be between 1-5 (CHECK constraint)
5. ✅ Student can only review courses they're enrolled in (application-level check)
6. ✅ Only course instructor or admin can grade submissions (application-level)

### Soft Delete Strategy:
- **Courses:** Use `DeletedAt` column (soft delete)
- **Users:** Identity manages user deletion
- **Other entities:** Hard delete (cascading where appropriate)

### Cascade Delete Rules:
- Delete Course → Delete Enrollments, Assignments, Comments, Reviews
- Delete Assignment → Delete Submissions, Grades
- Delete User → Keep related data (set FK to NULL or restrict)

---

## 🔍 Indexes Strategy

### Primary Indexes (Automatic):
- All Primary Keys (Clustered)
- Unique constraints (Non-clustered unique)

### Additional Indexes:
1. **Foreign Keys** - For JOIN performance
2. **Status Columns** - For filtering
3. **Date Columns** - For sorting/filtering
4. **Search Columns** - Title, Email (consider Full-Text if needed)

### Composite Indexes (Future Optimization):
- `(CourseId, StudentId)` on Enrollments
- `(CourseId, Status)` on Courses
- `(AssignmentId, StudentId)` on Submissions

---

## 📊 Sample Queries (Performance Considerations)

### Query 1: Get all courses with average rating
```sql
SELECT 
    c.Id, c.Title, c.Price,
    AVG(CAST(r.Rating AS DECIMAL(3,2))) AS AverageRating,
    COUNT(r.Id) AS ReviewCount
FROM Courses c
LEFT JOIN Reviews r ON c.Id = r.CourseId
WHERE c.DeletedAt IS NULL AND c.Status = 1
GROUP BY c.Id, c.Title, c.Price
ORDER BY AverageRating DESC
```

### Query 2: Get student's enrolled courses with progress
```sql
SELECT 
    c.Title, c.ThumbnailUrl,
    e.Progress, e.EnrolledAt, e.Status
FROM Enrollments e
INNER JOIN Courses c ON e.CourseId = c.Id
WHERE e.StudentId = @StudentId AND e.Status = 0
ORDER BY e.EnrolledAt DESC
```

### Query 3: Get pending assignments for student
```sql
SELECT 
    a.Id, a.Title, a.DueDate, c.Title AS CourseName
FROM Assignments a
INNER JOIN Courses c ON a.CourseId = c.Id
INNER JOIN Enrollments e ON c.Id = e.CourseId
LEFT JOIN Submissions s ON a.Id = s.AssignmentId AND s.StudentId = @StudentId
WHERE e.StudentId = @StudentId 
  AND a.Status = 1 
  AND s.Id IS NULL
  AND a.DueDate > GETUTCDATE()
ORDER BY a.DueDate ASC
```

---

## 🚀 Migration Strategy

### Initial Migration (Phase 1):
1. AspNetUsers (with extensions)
2. Categories
3. Courses
4. Enrollments

### Phase 2 Migration:
5. Assignments
6. Submissions
7. Grades

### Phase 3 Migration:
8. Comments
9. Reviews

### Future Tables (Phase 4+):
- Notifications
- ChatMessages
- Payments
- CourseMaterials
- Certificates

---

## 🎯 Performance Optimization Tips

1. **Use Eager Loading** for related entities when needed
   ```csharp
   .Include(c => c.Instructor)
   .Include(c => c.Category)
   ```

2. **Use Pagination** for large datasets
   ```csharp
   .Skip((page - 1) * pageSize).Take(pageSize)
   ```

3. **Use AsNoTracking()** for read-only queries
   ```csharp
   .AsNoTracking().ToListAsync()
   ```

4. **Use Projections** to select only needed columns
   ```csharp
   .Select(c => new { c.Id, c.Title })
   ```

5. **Create Indexes** on frequently queried columns

---

## 📝 Next Steps

1. ✅ Review this schema
2. ⏭️ Create EF Core Entity classes
3. ⏭️ Create Fluent API configurations
4. ⏭️ Generate initial migration
5. ⏭️ Apply migration to local SQL Server

---

**Schema Version:** 1.0  
**Last Updated:** 2025  
**Database:** SQL Server 2022 / Azure SQL
