# EduCollab - Project Requirements Document

## 📋 Executive Summary

**Project Name:** EduCollab  
**Solution Name:** PracticeAll  
**Type:** Learning & Collaboration Platform (Hybrid LMS + Project Management)  
**Target Framework:** .NET 10  
**Architecture:** Clean Architecture with Microservices  
**Database:** SQL Server  
**Primary Goal:** Comprehensive ASP.NET Core learning through real-world application development

---

## 🎯 Project Vision

Build a production-grade learning platform that enables:
- Instructors to create and manage courses
- Students to enroll, learn, and submit assignments
- Administrators to oversee the entire platform
- Real-time collaboration and communication

**Learning Objectives:**
- Master ASP.NET Core Web API development
- Deep understanding of Entity Framework Core
- Authentication & Authorization (Identity, JWT, OAuth)
- Clean Architecture & Design Patterns
- RESTful API design
- Testing, Deployment, and CI/CD

---

## 👥 User Roles & Permissions

### 1. **Guest (Unauthenticated)**
- View public course catalog
- View course details
- Register/Login

### 2. **Student (Authenticated)**
- All Guest permissions +
- Enroll in courses
- View enrolled courses
- Submit assignments
- View grades and feedback
- Update own profile
- Participate in discussions

### 3. **Instructor (Authenticated)**
- All Student permissions +
- Create and manage own courses
- Create assignments for own courses
- Grade student submissions
- Provide feedback
- View course analytics

### 4. **Admin (Authenticated)**
- Full system access
- User management (CRUD)
- Course approval/rejection
- Platform analytics
- System configuration

---

## 🏗️ Core Features (Phased Approach)

### **Phase 1: Foundation (Weeks 1-2)**
**Learning Focus:** EF Core, Repository Pattern, Basic CRUD, Clean Architecture

#### Features:
1. **User Management**
   - User registration (email/password)
   - Login/Logout
   - Profile management
   - Email confirmation
   - Password reset

2. **Course Management (Basic)**
   - Create course (Instructor only)
   - View all courses (public)
   - View course details
   - Update course (Owner only)
   - Delete course (Owner/Admin only)
   - Course categories

3. **Basic Authorization**
   - Role-based access control
   - Resource-based authorization (own resources)

#### Technical Implementations:
- ASP.NET Core Identity setup
- EF Core with SQL Server
- DbContext configuration
- Entity relationships (One-to-Many)
- Repository Pattern
- Unit of Work Pattern
- DTOs and AutoMapper
- FluentValidation
- Global exception handling middleware
- Request logging middleware

---

### **Phase 2: Enrollment & Assignments (Weeks 3-4)**
**Learning Focus:** Many-to-Many relationships, File handling, Business logic

#### Features:
1. **Course Enrollment**
   - Student enrolls in course
   - View enrolled courses
   - Unenroll from course
   - Enrollment status tracking

2. **Assignment Management**
   - Create assignments (Instructor)
   - View assignments (Students)
   - Set due dates
   - Assignment status (Draft, Published, Closed)

3. **Assignment Submission**
   - Upload submission files
   - Submit assignment
   - View submission status
   - Resubmit (if allowed)

4. **File Management**
   - Upload course materials
   - Download course materials
   - File size limits
   - Allowed file types

#### Technical Implementations:
- Many-to-Many relationships (Student-Course)
- File upload/download
- Azure Blob Storage integration (or local for dev)
- Background jobs for file processing
- Policy-based authorization
- Specification pattern

---

### **Phase 3: Grading & Feedback (Week 5)**
**Learning Focus:** Complex business logic, Notifications

#### Features:
1. **Grading System**
   - Grade submissions (Instructor)
   - Grading rubrics
   - Pass/Fail status
   - Grade statistics

2. **Feedback & Comments**
   - Instructor feedback on submissions
   - Comments on courses
   - Reply to comments (nested)

3. **Notifications**
   - Email notifications
     - New enrollment
     - Assignment published
     - Assignment graded
     - Course updates
   - In-app notifications (future)

#### Technical Implementations:
- Background job processing (Hangfire)
- Email service (SMTP/SendGrid)
- Domain events
- Event handlers
- Complex queries with LINQ

---

### **Phase 4: Advanced Features (Week 6)**
**Learning Focus:** Real-time communication, External integrations

#### Features:
1. **Discussion Forums**
   - Create discussion threads
   - Reply to threads
   - Nested comments
   - Vote/Like system

2. **Real-time Chat (Optional)**
   - Course-specific chat rooms
   - Direct messaging
   - Online status

3. **Course Reviews & Ratings**
   - Rate courses (1-5 stars)
   - Write reviews
   - Average ratings

4. **Payment Integration (Simulated)**
   - Course pricing
   - Enrollment payment flow
   - Payment history
   - Webhooks handling

#### Technical Implementations:
- SignalR for real-time features
- Stripe/PayPal integration (test mode)
- Webhook handling
- Caching (Redis or In-Memory)
- Rate limiting

---

### **Phase 5: Performance & Quality (Week 7)**
**Learning Focus:** Optimization, Best practices, Security

#### Features:
1. **Performance Optimization**
   - Response caching
   - Distributed caching
   - Database query optimization
   - Lazy loading vs Eager loading

2. **Advanced Authentication**
   - JWT token authentication
   - Refresh tokens
   - OAuth 2.0 (Google/Microsoft login)
   - Two-Factor Authentication (2FA)

3. **Admin Dashboard**
   - User statistics
   - Course statistics
   - Enrollment trends
   - System health monitoring

4. **Advanced Search & Filtering**
   - Search courses by title/description
   - Filter by category, level, price
   - Sort by rating, popularity, date

#### Technical Implementations:
- Response caching middleware
- Redis distributed cache
- Query optimization techniques
- OAuth providers integration
- JWT with refresh token rotation
- TOTP for 2FA
- Complex LINQ queries
- Full-text search (SQL Server)

---

### **Phase 6: Testing (Week 8)**
**Learning Focus:** Unit testing, Integration testing, Test-Driven Development

#### Test Coverage:
1. **Unit Tests**
   - Service layer tests
   - Repository tests
   - Validator tests
   - Domain logic tests

2. **Integration Tests**
   - API endpoint tests
   - Database integration tests
   - Authentication tests

3. **Tools:**
   - xUnit
   - Moq (mocking)
   - FluentAssertions
   - In-Memory Database for testing

---

### **Phase 7: DevOps & Deployment (Weeks 9-10)**
**Learning Focus:** Containerization, Cloud deployment, CI/CD

#### Features:
1. **Containerization**
   - Dockerfile for API
   - Docker Compose for local environment
   - Multi-stage builds

2. **Cloud Deployment (Azure)**
   - Azure App Service
   - Azure SQL Database
   - Azure Blob Storage
   - Application Insights (monitoring)

3. **CI/CD Pipeline**
   - GitHub Actions workflow
   - Automated testing
   - Automated deployment
   - Environment management (Dev/Staging/Prod)

#### Technical Implementations:
- Docker & Docker Compose
- Azure CLI
- ARM templates or Bicep
- GitHub Actions YAML
- Environment variables management
- Secrets management (Azure Key Vault)

---

## 📊 Non-Functional Requirements

### Performance
- API response time < 200ms (95th percentile)
- Support 100+ concurrent users
- Database queries optimized with proper indexing

### Security
- HTTPS only
- Password hashing (Identity default)
- SQL injection prevention (EF Core parameterized queries)
- XSS prevention (built-in ASP.NET Core)
- CSRF protection
- Rate limiting on authentication endpoints
- Input validation on all endpoints

### Scalability
- Stateless API (horizontal scaling ready)
- Caching strategy for frequent queries
- Asynchronous operations for long-running tasks

### Maintainability
- Clean Architecture (testable, maintainable)
- Comprehensive documentation
- Code comments where necessary
- Consistent naming conventions
- SOLID principles

### Usability (API)
- RESTful conventions
- Clear error messages
- API versioning
- Swagger/OpenAPI documentation
- Consistent response format

---

## 🗄️ Data Models (Overview)

### Core Entities:
1. **User** (from Identity)
2. **Course**
3. **Enrollment**
4. **Assignment**
5. **Submission**
6. **Grade**
7. **Category**
8. **Comment**
9. **Review**
10. **Notification**

*Detailed schema in `DATABASE_SCHEMA.md`*

---

## 🔌 API Structure

### Base URL: `https://localhost:5001/api`

### Endpoints (Phase 1):

#### Authentication
- `POST /auth/register` - Register new user
- `POST /auth/login` - Login
- `POST /auth/logout` - Logout
- `POST /auth/refresh-token` - Refresh JWT
- `POST /auth/forgot-password` - Request password reset
- `POST /auth/reset-password` - Reset password
- `GET /auth/confirm-email` - Confirm email

#### Courses
- `GET /courses` - List all courses (paginated, filtered)
- `GET /courses/{id}` - Get course details
- `POST /courses` - Create course [Instructor]
- `PUT /courses/{id}` - Update course [Instructor/Admin]
- `DELETE /courses/{id}` - Delete course [Instructor/Admin]

#### Enrollments
- `POST /enrollments` - Enroll in course [Student]
- `GET /enrollments/my-courses` - Get enrolled courses [Student]
- `DELETE /enrollments/{id}` - Unenroll [Student]

#### Users
- `GET /users/profile` - Get own profile [Authenticated]
- `PUT /users/profile` - Update own profile [Authenticated]
- `GET /users/{id}` - Get user by ID [Admin]
- `GET /users` - List all users [Admin]

*More endpoints added in later phases*

---

## 🎓 Learning Outcomes by Phase

| Phase | Topics Covered |
|-------|---------------|
| **Phase 1** | EF Core basics, DbContext, Migrations, Relationships (1-to-many), Repository Pattern, UnitOfWork, Clean Architecture, Identity basics, Role-based auth, Middleware, DTOs, AutoMapper, FluentValidation |
| **Phase 2** | Many-to-Many relationships, File handling, Blob storage, Policy-based authorization, Resource-based authorization, Background jobs, Business logic layer |
| **Phase 3** | Domain events, Event handlers, Email services, Complex LINQ, Aggregations, Nested entities |
| **Phase 4** | SignalR, Real-time communication, External API integration, Webhooks, Caching strategies, Rate limiting |
| **Phase 5** | JWT authentication, Refresh tokens, OAuth 2.0, 2FA, Query optimization, Indexing, Full-text search, Performance monitoring |
| **Phase 6** | Unit testing, Integration testing, Mocking, Test fixtures, TDD principles |
| **Phase 7** | Docker, Azure services, CI/CD pipelines, GitHub Actions, Environment management, Monitoring |

---

## 📈 Success Criteria

### Technical Success:
- ✅ All API endpoints functional and tested
- ✅ Database properly normalized and indexed
- ✅ Authentication & Authorization working correctly
- ✅ Clean Architecture maintained throughout
- ✅ 70%+ test coverage
- ✅ Successfully deployed to Azure
- ✅ CI/CD pipeline functional

### Learning Success:
- ✅ Understand and implement EF Core configurations
- ✅ Master Clean Architecture principles
- ✅ Implement authentication/authorization confidently
- ✅ Write testable, maintainable code
- ✅ Deploy and manage cloud applications
- ✅ Build production-ready APIs

---

## 🛠️ Technology Stack

### Backend
- **Framework:** ASP.NET Core 10 Web API
- **Language:** C# 13
- **ORM:** Entity Framework Core 10
- **Database:** SQL Server 2022 / Azure SQL

### Authentication & Authorization
- **Identity:** ASP.NET Core Identity
- **JWT:** System.IdentityModel.Tokens.Jwt
- **OAuth:** Microsoft.AspNetCore.Authentication.Google/Microsoft

### Libraries & Tools
- **Mapping:** AutoMapper
- **Validation:** FluentValidation
- **Background Jobs:** Hangfire
- **Email:** MailKit or SendGrid
- **File Storage:** Azure.Storage.Blobs
- **Caching:** StackExchange.Redis (or Microsoft.Extensions.Caching.Memory)
- **Real-time:** Microsoft.AspNetCore.SignalR
- **API Documentation:** Swashbuckle.AspNetCore (Swagger)
- **Logging:** Serilog

### Testing
- **Framework:** xUnit
- **Mocking:** Moq
- **Assertions:** FluentAssertions
- **Test DB:** Microsoft.EntityFrameworkCore.InMemory

### DevOps
- **Containerization:** Docker
- **CI/CD:** GitHub Actions
- **Cloud:** Microsoft Azure
- **Monitoring:** Application Insights

### Frontend (Future)
- **Framework:** Angular 18+
- **UI Library:** Angular Material

---

## 📅 Development Timeline (Flexible)

| Week | Phase | Focus |
|------|-------|-------|
| 1-2 | Phase 1 | Foundation, Basic CRUD, Identity |
| 3-4 | Phase 2 | Enrollments, Assignments, Files |
| 5 | Phase 3 | Grading, Notifications |
| 6 | Phase 4 | Advanced Features |
| 7 | Phase 5 | Performance, Security |
| 8 | Phase 6 | Testing |
| 9-10 | Phase 7 | Deployment, CI/CD |

*Note: Timeline is flexible based on learning pace and topic complexity*

---

## 🎯 Next Steps

1. ✅ Review this requirements document
2. ✅ Review database schema (`DATABASE_SCHEMA.md`)
3. ✅ Review architecture (`ARCHITECTURE.md`)
4. ✅ Review learning roadmap (`LEARNING_ROADMAP.md`)
5. ⏭️ Scaffold project structure
6. ⏭️ Begin Phase 1 implementation

---

## 📝 Document Control

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2025 | Initial requirements document |

---

**Ready to build something amazing! 🚀**
