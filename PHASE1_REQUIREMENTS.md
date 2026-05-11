# Phase 1: Course Management - Basic CRUD

## 🎯 Phase Overview

**Goal:** Build a working Course Management API with basic CRUD operations (Create, Read, Update, Delete)

**Duration:** 2-3 days  
**Complexity:** Beginner-friendly  
**Authentication:** ❌ None (anyone can access)  
**Authorization:** ❌ None (no roles)

---

## 📋 What You'll Build

A simple REST API that allows:
- ✅ Create a new course
- ✅ View all courses (with pagination)
- ✅ View a single course by ID
- ✅ Update an existing course
- ✅ Delete a course (soft delete)
- ✅ Filter courses by category
- ✅ Search courses by title

---

## 🗄️ Database Tables/Entities Required

### **Entities to Create:**

#### 1. **Category** (Required as FK for Course)
```
From DATABASE_SCHEMA.md - Use FULL schema:
- Id (Guid)
- Name (string, 100 chars, required, unique)
- Description (string, 500 chars, nullable)
- CreatedAt (DateTime)
- UpdatedAt (DateTime, nullable)
```

#### 2. **Course** (Main entity)
```
From DATABASE_SCHEMA.md - Use FULL schema:
- Id (Guid)
- Title (string, 200 chars, required)
- Description (string, max, required)
- ShortDescription (string, 500 chars, nullable)
- Price (decimal, 18,2, required, default 0.00)
- Level (int/enum, required) → CourseLevel enum
- DurationInHours (int, nullable)
- Language (string, 50 chars, default "English")
- Status (int/enum, required, default Draft) → CourseStatus enum
- ThumbnailUrl (string, 500 chars, nullable)
- InstructorId (Guid, FK → will be NULL in Phase 1)
- CategoryId (Guid, FK → Categories)
- CreatedAt (DateTime, required)
- UpdatedAt (DateTime, nullable)
- DeletedAt (DateTime, nullable) ← For soft delete
```

#### 3. **BaseEntity** (Abstract base class)
```
Common properties for all entities:
- Id (Guid, PK)
- CreatedAt (DateTime)
- UpdatedAt (DateTime, nullable)
```

### **Enums to Create:**

#### 1. **CourseLevel** (Domain/Enums/)
```csharp
public enum CourseLevel
{
    Beginner = 0,
    Intermediate = 1,
    Advanced = 2,
    Expert = 3
}
```

#### 2. **CourseStatus** (Domain/Enums/)
```csharp
public enum CourseStatus
{
    Draft = 0,
    Published = 1,
    Archived = 2,
    UnderReview = 3
}
```

---

## 📂 Project Structure (Phase 1)

```
📁 C:\myDrive\Practice\PracticeAll\
│
├── 📄 PHASE1_REQUIREMENTS.md          ← This file
│
├── 📁 src\
│   │
│   ├── 📁 CourseManagement.Academic\   ← Single project
│   │   │
│   │   ├── 📁 API\
│   │   │   ├── 📁 Controllers\
│   │   │   │   └── CoursesController.cs
│   │   │   │   └── CategoriesController.cs
│   │   │   │
│   │   │   ├── 📁 Models\              ← Request/Response DTOs
│   │   │   │   ├── 📁 Requests\
│   │   │   │   │   ├── CreateCourseRequest.cs
│   │   │   │   │   └── UpdateCourseRequest.cs
│   │   │   │   └── 📁 Responses\
│   │   │   │       ├── CourseResponse.cs
│   │   │   │       └── CategoryResponse.cs
│   │   │   │
│   │   │   ├── 📁 Extensions\
│   │   │   │   └── ServiceCollectionExtensions.cs
│   │   │   │
│   │   │   ├── 📁 Properties\
│   │   │   │   └── launchSettings.json
│   │   │   │
│   │   │   ├── appsettings.json
│   │   │   ├── appsettings.Development.json
│   │   │   └── Program.cs
│   │   │
│   │   ├── 📁 Application\
│   │   │   ├── 📁 Services\
│   │   │   │   ├── 📁 Interfaces\
│   │   │   │   │   ├── ICourseService.cs
│   │   │   │   │   └── ICategoryService.cs
│   │   │   │   └── 📁 Implementations\
│   │   │   │       ├── CourseService.cs
│   │   │   │       └── CategoryService.cs
│   │   │   │
│   │   │   ├── 📁 DTOs\
│   │   │   │   ├── CourseDto.cs
│   │   │   │   ├── CreateCourseDto.cs
│   │   │   │   ├── UpdateCourseDto.cs
│   │   │   │   └── CategoryDto.cs
│   │   │   │
│   │   │   ├── 📁 Mappings\
│   │   │   │   ├── CourseMappingProfile.cs
│   │   │   │   └── CategoryMappingProfile.cs
│   │   │   │
│   │   │   ├── 📁 Validators\
│   │   │   │   ├── CreateCourseDtoValidator.cs
│   │   │   │   └── UpdateCourseDtoValidator.cs
│   │   │   │
│   │   │   ├── 📁 Interfaces\
│   │   │   │   ├── ICourseRepository.cs
│   │   │   │   ├── ICategoryRepository.cs
│   │   │   │   └── IUnitOfWork.cs
│   │   │   │
│   │   │   └── 📁 Extensions\
│   │   │       └── ApplicationServiceExtensions.cs
│   │   │
│   │   ├── 📁 Domain\
│   │   │   ├── 📁 Entities\
│   │   │   │   ├── BaseEntity.cs
│   │   │   │   ├── Course.cs
│   │   │   │   └── Category.cs
│   │   │   │
│   │   │   └── 📁 Enums\
│   │   │       ├── CourseLevel.cs
│   │   │       └── CourseStatus.cs
│   │   │
│   │   └── 📁 Infrastructure\
│   │       ├── 📁 Data\
│   │       │   ├── ApplicationDbContext.cs
│   │       │   │
│   │       │   ├── 📁 Configurations\
│   │       │   │   ├── CourseConfiguration.cs
│   │       │   │   └── CategoryConfiguration.cs
│   │       │   │
│   │       │   └── 📁 Migrations\        ← Auto-generated
│   │       │
│   │       ├── 📁 Repositories\
│   │       │   ├── BaseRepository.cs
│   │       │   ├── CourseRepository.cs
│   │       │   ├── CategoryRepository.cs
│   │       │   └── UnitOfWork.cs
│   │       │
│   │       └── 📁 Extensions\
│   │           └── InfrastructureServiceExtensions.cs
│   │
│   └── 📁 Shared\                        ← Cross-cutting concerns
│       │
│       ├── 📁 Common\
│       │   ├── 📁 Exceptions\
│       │   │   ├── BaseException.cs
│       │   │   ├── NotFoundException.cs
│       │   │   └── ValidationException.cs
│       │   │
│       │   ├── 📁 Models\
│       │   │   ├── ApiResponse.cs
│       │   │   └── PagedResult.cs
│       │   │
│       │   └── 📁 Constants\
│       │       └── ErrorMessages.cs
│       │
│       └── 📁 Middleware\
│           ├── ExceptionHandlingMiddleware.cs
│           └── 📁 Extensions\
│               └── MiddlewareExtensions.cs
│
└── 📁 tests\                             ← (Optional for Phase 1)
    └── (Add later)
```

---

## 📦 NuGet Packages Required (Phase 1)

### **CourseManagement.Academic Project:**

```xml
<!-- EF Core -->
<PackageReference Include="Microsoft.EntityFrameworkCore" Version="10.0.0" />
<PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer" Version="10.0.0" />
<PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="10.0.0">
  <PrivateAssets>all</PrivateAssets>
</PackageReference>
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="10.0.0">
  <PrivateAssets>all</PrivateAssets>
</PackageReference>

<!-- AutoMapper -->
<PackageReference Include="AutoMapper" Version="13.0.1" />
<PackageReference Include="AutoMapper.Extensions.Microsoft.DependencyInjection" Version="13.0.3" />

<!-- FluentValidation -->
<PackageReference Include="FluentValidation.AspNetCore" Version="11.3.0" />

<!-- Swagger (Already included in Web API template) -->
<PackageReference Include="Swashbuckle.AspNetCore" Version="7.2.0" />
```

### **Shared Projects:**
```xml
<!-- Common & Middleware projects need: -->
<PackageReference Include="Microsoft.AspNetCore.Http.Abstractions" Version="2.2.0" />
```

---

## 🔌 API Endpoints (Phase 1)

### **Base URL:** `https://localhost:7xxx/api`

### **Categories:**
| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| GET | `/categories` | Get all categories | None |
| GET | `/categories/{id}` | Get category by ID | None |
| POST | `/categories` | Create category | None |
| PUT | `/categories/{id}` | Update category | None |
| DELETE | `/categories/{id}` | Delete category | None |

### **Courses:**
| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| GET | `/courses` | Get all courses (paginated) | None |
| GET | `/courses/{id}` | Get course by ID | None |
| POST | `/courses` | Create course | None |
| PUT | `/courses/{id}` | Update course | None |
| DELETE | `/courses/{id}` | Soft delete course | None |

### **Query Parameters (GET /courses):**
```
?pageNumber=1           ← Pagination
&pageSize=10            ← Items per page
&categoryId={guid}      ← Filter by category
&level=1                ← Filter by level (0-3)
&status=1               ← Filter by status (0-3)
&searchTerm=web         ← Search in title/description
```

---

## 📊 Sample Request/Response

### **Create Course (POST /api/courses)**

**Request Body:**
```json
{
  "title": "Introduction to ASP.NET Core",
  "description": "Learn the fundamentals of building web APIs with ASP.NET Core",
  "shortDescription": "Master ASP.NET Core basics",
  "price": 49.99,
  "level": 0,
  "durationInHours": 10,
  "language": "English",
  "status": 0,
  "thumbnailUrl": "https://example.com/thumbnail.jpg",
  "categoryId": "3fa85f64-5717-4562-b3fc-2c963f66afa6"
}
```

**Response (201 Created):**
```json
{
  "id": "8d7c9e1a-2b3c-4d5e-6f7a-8b9c0d1e2f3a",
  "title": "Introduction to ASP.NET Core",
  "description": "Learn the fundamentals of building web APIs with ASP.NET Core",
  "shortDescription": "Master ASP.NET Core basics",
  "price": 49.99,
  "level": 0,
  "levelName": "Beginner",
  "durationInHours": 10,
  "language": "English",
  "status": 0,
  "statusName": "Draft",
  "thumbnailUrl": "https://example.com/thumbnail.jpg",
  "categoryId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
  "categoryName": "Web Development",
  "createdAt": "2025-01-15T10:30:00Z",
  "updatedAt": null
}
```

### **Get All Courses (GET /api/courses?pageNumber=1&pageSize=10)**

**Response (200 OK):**
```json
{
  "data": [
    {
      "id": "8d7c9e1a-2b3c-4d5e-6f7a-8b9c0d1e2f3a",
      "title": "Introduction to ASP.NET Core",
      "shortDescription": "Master ASP.NET Core basics",
      "price": 49.99,
      "level": 0,
      "levelName": "Beginner",
      "status": 0,
      "statusName": "Draft",
      "thumbnailUrl": "https://example.com/thumbnail.jpg",
      "categoryName": "Web Development"
    }
  ],
  "pageNumber": 1,
  "pageSize": 10,
  "totalCount": 1,
  "totalPages": 1,
  "hasPreviousPage": false,
  "hasNextPage": false
}
```

---

## ✅ Acceptance Criteria (Phase 1)

### **Functional:**
- ✅ User can create a course with all required fields
- ✅ User can view list of courses (paginated)
- ✅ User can view a single course by ID
- ✅ User can update a course
- ✅ User can delete a course (soft delete - sets DeletedAt)
- ✅ Deleted courses don't appear in GET requests
- ✅ User can filter courses by category
- ✅ User can search courses by title
- ✅ Validation errors return 400 Bad Request with details
- ✅ Not found returns 404 with error message
- ✅ Server errors return 500 with friendly message

### **Technical:**
- ✅ Clean Architecture maintained (Domain → Application → Infrastructure → API)
- ✅ Repository pattern implemented
- ✅ Unit of Work pattern implemented
- ✅ AutoMapper configured and working
- ✅ FluentValidation configured and working
- ✅ Global exception handling middleware working
- ✅ EF Core migrations created and applied
- ✅ Database created with correct schema
- ✅ Swagger documentation accessible at `/swagger`
- ✅ All CRUD operations tested via Swagger

### **Code Quality:**
- ✅ Proper namespaces (PracticeAll.CourseManagement.Academic.*)
- ✅ Async/await used throughout
- ✅ DTOs used (no entities exposed in API)
- ✅ Proper HTTP status codes returned
- ✅ Consistent naming conventions

---

## 🎓 Learning Objectives (Phase 1)

By completing Phase 1, you will learn:

1. **Clean Architecture**
   - Layer separation (Domain, Application, Infrastructure, API)
   - Dependency flow (inner layers don't depend on outer)
   - Where to put what code

2. **Entity Framework Core**
   - DbContext configuration
   - Entity classes with navigation properties
   - Fluent API configurations (IEntityTypeConfiguration<T>)
   - One-to-Many relationships (Category → Courses)
   - Migrations (Add, Update, Remove)
   - Connection strings
   - CRUD operations with EF Core

3. **Repository Pattern**
   - Generic repository interface
   - Base repository implementation
   - Specific repository implementation
   - Why use repositories?

4. **Unit of Work Pattern**
   - Transaction management
   - SaveChanges coordination
   - Why use Unit of Work?

5. **Dependency Injection**
   - Service registration (AddScoped, AddTransient, AddSingleton)
   - Extension methods for DI registration
   - Constructor injection

6. **AutoMapper**
   - Profile creation
   - Mapping configurations
   - Using IMapper in services

7. **FluentValidation**
   - Validator classes
   - Validation rules
   - Automatic validation in API pipeline

8. **API Development**
   - REST conventions (GET, POST, PUT, DELETE)
   - HTTP status codes (200, 201, 400, 404, 500)
   - Controller actions
   - DTOs for request/response
   - Swagger/OpenAPI documentation

9. **Exception Handling**
   - Custom exceptions
   - Global exception middleware
   - Error response formatting

10. **LINQ Basics**
    - Where, Select, OrderBy
    - ToListAsync, FirstOrDefaultAsync
    - Pagination (Skip, Take)

---

## 🚫 What's NOT in Phase 1

- ❌ Authentication (no login)
- ❌ Authorization (no roles)
- ❌ Users / Identity
- ❌ Enrollments
- ❌ Assignments
- ❌ File uploads
- ❌ Email notifications
- ❌ Background jobs
- ❌ Caching
- ❌ SignalR
- ❌ Unit tests (add in Phase 6)
- ❌ Docker
- ❌ Azure deployment

**Focus:** Master the fundamentals first!

---

## 📝 Shared Folder Components (Phase 1)

### **What to Create in `src\Shared\`:**

#### **1. Common Project** (Class Library)

**Files Needed:**
```
📁 Common\
├── 📁 Exceptions\
│   ├── BaseException.cs              ← Base for all custom exceptions
│   ├── NotFoundException.cs          ← For 404 errors
│   └── ValidationException.cs        ← For validation errors
│
├── 📁 Models\
│   ├── ApiResponse.cs                ← Standard API response wrapper
│   └── PagedResult.cs                ← Pagination wrapper
│
└── 📁 Constants\
    └── ErrorMessages.cs              ← Centralized error messages
```

**Purpose:** Reusable across all future services

#### **2. Middleware Project** (Class Library)

**Files Needed:**
```
📁 Middleware\
├── ExceptionHandlingMiddleware.cs   ← Global exception handler
└── 📁 Extensions\
    └── MiddlewareExtensions.cs       ← app.UseExceptionHandling()
```

**Purpose:** Centralized exception handling

---

## 🔄 Implementation Steps (High-Level)

**You'll do these step-by-step with my guidance:**

1. ✅ Create project structure (you're doing this manually)
2. ⏭️ Create Domain entities (Course, Category, BaseEntity, Enums)
3. ⏭️ Create Infrastructure (DbContext, Configurations, Repositories)
4. ⏭️ Create first migration and update database
5. ⏭️ Create Application layer (Services, DTOs, Mappings, Validators)
6. ⏭️ Create Shared components (Exceptions, Middleware)
7. ⏭️ Create API layer (Controllers, Request/Response models)
8. ⏭️ Register all services in DI (Program.cs)
9. ⏭️ Test with Swagger
10. ⏭️ Fix bugs and refine

---

## 🎯 Success Metrics

**You'll know Phase 1 is complete when:**
- ✅ You can create a category via API
- ✅ You can create a course via API
- ✅ You can see all courses with pagination
- ✅ You can update a course
- ✅ You can delete a course (and it disappears from GET)
- ✅ Validation errors show proper messages
- ✅ 404 errors show proper messages
- ✅ Database has data matching your API calls

---

## 📚 Resources for Phase 1

**Official Microsoft Docs:**
- [EF Core - DbContext](https://learn.microsoft.com/en-us/ef/core/dbcontext-configuration/)
- [EF Core - Migrations](https://learn.microsoft.com/en-us/ef/core/managing-schemas/migrations/)
- [EF Core - Relationships](https://learn.microsoft.com/en-us/ef/core/modeling/relationships)
- [ASP.NET Core - Dependency Injection](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection)

**Quick Reference:**
- AutoMapper: https://docs.automapper.org/
- FluentValidation: https://docs.fluentvalidation.net/

---

## ⏭️ Next Steps

**Right now:**
1. ✅ Review this document
2. ✅ Finish creating project structure manually
3. ✅ List your topics to cross-check with the plan
4. ⏭️ I'll guide you through implementation step-by-step

---

**Let's build something awesome! 🚀**

**Phase 1 Version:** 1.0  
**Estimated Time:** 2-3 days  
**Difficulty:** ⭐⭐ (Beginner-friendly)
