# EduCollab - Learning Roadmap

## 🎯 Learning Philosophy

**"Learn by Doing"** - Build a real-world application while mastering ASP.NET Core concepts.

**Approach:**
- 📚 Learn concept → 💻 Implement in project → 🧪 Test → 🔄 Refine
- Practice each concept multiple times in different contexts
- Progress from simple to complex
- Build incrementally, one feature at a time

---

## 📅 Phase-wise Learning Path

### **Phase 1: Foundation (Weeks 1-2)** ⭐ START HERE

#### **What You'll Learn:**
1. **Project Setup & Clean Architecture**
   - Solution structure
   - Project dependencies
   - Layer separation
   - Dependency Injection fundamentals

2. **Entity Framework Core Basics**
   - DbContext configuration
   - Entity classes
   - Fluent API configurations
   - Relationships (One-to-Many)
   - Migrations (Add, Update, Remove)
   - Connection strings

3. **Repository Pattern**
   - Generic repository
   - Specific repositories
   - Unit of Work pattern

4. **Basic CRUD Operations**
   - Create (POST)
   - Read (GET)
   - Update (PUT)
   - Delete (DELETE)

5. **DTOs & Mapping**
   - Data Transfer Objects
   - AutoMapper configuration
   - Mapping profiles

6. **Validation**
   - FluentValidation setup
   - Custom validators
   - Validation rules

7. **Exception Handling**
   - Custom exceptions
   - Global exception middleware
   - Error responses

8. **ASP.NET Core Identity (Basics)**
   - User registration
   - Login/Logout
   - Password hashing
   - Email confirmation

#### **Topics Deep Dive:**

##### **1.1 EF Core DbContext Configuration** 🔥 IMPORTANT
**What to Learn:**
- What is DbContext?
- How to configure connection strings
- DbSet properties
- OnModelCreating method
- ApplyConfigurationsFromAssembly

**Practice Locations:**
1. Create `ApplicationDbContext` (Infrastructure/Data)
2. Configure `Categories` table
3. Configure `Courses` table
4. Configure relationships

**Resources:**
- Official Docs: [DbContext Lifetime](https://learn.microsoft.com/en-us/ef/core/dbcontext-configuration/)
- Official Docs: [Model Configuration](https://learn.microsoft.com/en-us/ef/core/modeling/)

**Code Example:**
```csharp
public class ApplicationDbContext : DbContext
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options) { }

    public DbSet<Course> Courses => Set<Course>();
    public DbSet<Category> Categories => Set<Category>();

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);
        modelBuilder.ApplyConfigurationsFromAssembly(Assembly.GetExecutingAssembly());
    }
}
```

---

##### **1.2 Fluent API Entity Configuration** 🔥 IMPORTANT
**What to Learn:**
- IEntityTypeConfiguration<T>
- Property configurations (Required, MaxLength, HasColumnType)
- Key configuration
- Index creation
- Relationships (HasOne, WithMany, HasForeignKey)
- OnDelete behavior

**Practice Locations:**
1. `CourseConfiguration.cs` - Configure Course entity
2. `CategoryConfiguration.cs` - Configure Category entity
3. `EnrollmentConfiguration.cs` - Configure many-to-many (Phase 2)

**Resources:**
- Official Docs: [Fluent API](https://learn.microsoft.com/en-us/ef/core/modeling/)

**Code Example:**
```csharp
public class CourseConfiguration : IEntityTypeConfiguration<Course>
{
    public void Configure(EntityTypeBuilder<Course> builder)
    {
        builder.HasKey(c => c.Id);

        builder.Property(c => c.Title)
            .IsRequired()
            .HasMaxLength(200);

        builder.HasOne(c => c.Category)
            .WithMany()
            .HasForeignKey(c => c.CategoryId)
            .OnDelete(DeleteBehavior.Restrict);
    }
}
```

---

##### **1.3 EF Core Migrations**
**What to Learn:**
- What are migrations?
- Add-Migration command
- Update-Database command
- Rollback migrations
- Migration history

**Practice:**
```powershell
# Add migration
dotnet ef migrations add InitialCreate --project Infrastructure --startup-project API

# Update database
dotnet ef database update --project Infrastructure --startup-project API

# Remove last migration (if not applied)
dotnet ef migrations remove --project Infrastructure --startup-project API
```

**Resources:**
- Official Docs: [Migrations](https://learn.microsoft.com/en-us/ef/core/managing-schemas/migrations/)

---

##### **1.4 Repository Pattern**
**What to Learn:**
- Generic repository vs specific repositories
- Interface segregation
- Async methods (AddAsync, GetByIdAsync, etc.)
- Expression-based queries

**Practice Locations:**
1. Create `IRepository<T>` interface (Application/Interfaces)
2. Create `BaseRepository<T>` (Infrastructure/Repositories)
3. Create `ICourseRepository` (Application/Interfaces)
4. Implement `CourseRepository` (Infrastructure/Repositories)

**Code Example:**
```csharp
public interface IRepository<T> where T : class
{
    Task<T?> GetByIdAsync(Guid id);
    Task<IEnumerable<T>> GetAllAsync();
    Task AddAsync(T entity);
    void Update(T entity);
    void Delete(T entity);
}
```

---

##### **1.5 AutoMapper**
**What to Learn:**
- Profile creation
- Mapping configurations
- ReverseMap
- Using IMapper in services

**Practice Locations:**
1. Create `CourseMappingProfile` (Application/Mappings)
2. Map `Course → CourseDto`
3. Map `CreateCourseDto → Course`

**Code Example:**
```csharp
public class CourseMappingProfile : Profile
{
    public CourseMappingProfile()
    {
        CreateMap<Course, CourseDto>();
        CreateMap<CreateCourseDto, Course>();
        CreateMap<UpdateCourseDto, Course>();
    }
}
```

---

##### **1.6 FluentValidation**
**What to Learn:**
- AbstractValidator<T>
- Validation rules (NotEmpty, MaxLength, Must, etc.)
- Custom validators
- Automatic validation in API

**Practice Locations:**
1. Create `CreateCourseDtoValidator`
2. Create `UpdateCourseDtoValidator`
3. Validate price, title, description

**Code Example:**
```csharp
public class CreateCourseDtoValidator : AbstractValidator<CreateCourseDto>
{
    public CreateCourseDtoValidator()
    {
        RuleFor(x => x.Title)
            .NotEmpty()
            .MaximumLength(200);

        RuleFor(x => x.Price)
            .GreaterThanOrEqualTo(0);
    }
}
```

---

##### **1.7 ASP.NET Core Identity**
**What to Learn:**
- UserManager<T>
- SignInManager<T>
- ApplicationUser (extending IdentityUser)
- Registration flow
- Login flow
- Password policies

**Practice Locations:**
1. Extend `IdentityUser` to `ApplicationUser` (Shared.Identity)
2. Create `AuthService` (Shared.Identity)
3. Implement Register endpoint
4. Implement Login endpoint

**Resources:**
- Official Docs: [Identity](https://learn.microsoft.com/en-us/aspnet/core/security/authentication/identity)

---

#### **Phase 1 Implementation Order:**

**Week 1:**
1. ✅ Scaffold project structure
2. ✅ Create Domain entities (Course, Category)
3. ✅ Configure DbContext
4. ✅ Create Fluent API configurations
5. ✅ Create first migration
6. ✅ Implement Repository pattern
7. ✅ Implement Unit of Work

**Week 2:**
8. ✅ Setup AutoMapper
9. ✅ Create DTOs
10. ✅ Implement CourseService (CRUD)
11. ✅ Setup FluentValidation
12. ✅ Create API controllers
13. ✅ Setup Identity
14. ✅ Implement Auth endpoints (Register/Login)
15. ✅ Test everything

---

### **Phase 2: Enrollments & Assignments (Weeks 3-4)**

#### **What You'll Learn:**
1. **Many-to-Many Relationships**
   - Join tables
   - Navigation properties
   - Explicit vs implicit join tables

2. **File Upload/Download**
   - IFormFile
   - File validation
   - Local storage
   - (Optional) Azure Blob Storage

3. **Authorization**
   - [Authorize] attribute
   - Role-based authorization
   - Policy-based authorization
   - Resource-based authorization

4. **Complex Business Logic**
   - Enrollment validation
   - Assignment submission rules
   - Due date handling

5. **LINQ Advanced**
   - Include/ThenInclude
   - Where, Select, Any, All
   - GroupBy, OrderBy
   - Projections

#### **Topics Deep Dive:**

##### **2.1 Many-to-Many Relationships** 🔥 IMPORTANT
**What to Learn:**
- Explicit join table with payload
- Configure many-to-many in Fluent API
- Navigation properties

**Practice Location:**
- `Enrollment` table (Student ↔ Course)

**Code Example:**
```csharp
public class EnrollmentConfiguration : IEntityTypeConfiguration<Enrollment>
{
    public void Configure(EntityTypeBuilder<Enrollment> builder)
    {
        builder.HasKey(e => e.Id);

        builder.HasOne(e => e.Student)
            .WithMany()
            .HasForeignKey(e => e.StudentId);

        builder.HasOne(e => e.Course)
            .WithMany(c => c.Enrollments)
            .HasForeignKey(e => e.CourseId);

        builder.HasIndex(e => new { e.StudentId, e.CourseId })
            .IsUnique();
    }
}
```

---

##### **2.2 File Upload**
**What to Learn:**
- IFormFile interface
- File size limits
- Allowed file types
- Save to local folder
- Return file path/URL

**Practice Locations:**
1. Assignment creation (instructor uploads)
2. Assignment submission (student uploads)
3. Profile picture upload

**Code Example:**
```csharp
[HttpPost("upload")]
public async Task<IActionResult> Upload(IFormFile file)
{
    if (file == null || file.Length == 0)
        return BadRequest("No file uploaded");

    var uploadsFolder = Path.Combine(_webHostEnvironment.WebRootPath, "uploads");
    var fileName = $"{Guid.NewGuid()}_{file.FileName}";
    var filePath = Path.Combine(uploadsFolder, fileName);

    using (var stream = new FileStream(filePath, FileMode.Create))
    {
        await file.CopyToAsync(stream);
    }

    return Ok(new { FilePath = $"/uploads/{fileName}" });
}
```

---

##### **2.3 Role-based Authorization**
**What to Learn:**
- [Authorize(Roles = "Admin")]
- Check user roles in services
- Create roles (Admin, Instructor, Student)

**Practice Locations:**
1. Only Instructors can create courses
2. Only Admins can delete users
3. Only Students can enroll

**Code Example:**
```csharp
[Authorize(Roles = "Instructor")]
[HttpPost]
public async Task<IActionResult> CreateCourse([FromBody] CreateCourseDto dto)
{
    // Only instructors can access this
}
```

---

##### **2.4 Policy-based Authorization**
**What to Learn:**
- Define custom policies
- IAuthorizationRequirement
- AuthorizationHandler<T>

**Practice Location:**
- "CanGradeAssignment" policy (only instructor of that course)

**Code Example:**
```csharp
// Policy definition
services.AddAuthorization(options =>
{
    options.AddPolicy("CanGradeAssignment", policy =>
        policy.Requirements.Add(new MustBeInstructorOfCourse()));
});

// Handler
public class MustBeInstructorOfCourseHandler 
    : AuthorizationHandler<MustBeInstructorOfCourse, Assignment>
{
    protected override Task HandleRequirementAsync(
        AuthorizationHandlerContext context,
        MustBeInstructorOfCourse requirement,
        Assignment assignment)
    {
        var userId = context.User.FindFirst(ClaimTypes.NameIdentifier)?.Value;

        if (assignment.Course.InstructorId.ToString() == userId)
            context.Succeed(requirement);

        return Task.CompletedTask;
    }
}
```

---

#### **Phase 2 Implementation Order:**

**Week 3:**
1. ✅ Create Enrollment entity
2. ✅ Configure many-to-many relationship
3. ✅ Implement EnrollmentService
4. ✅ Create Enroll/Unenroll endpoints
5. ✅ Add role-based authorization
6. ✅ Create Assignment entity
7. ✅ Implement file upload for assignments

**Week 4:**
8. ✅ Create Submission entity
9. ✅ Implement submission service
10. ✅ File upload for submissions
11. ✅ Policy-based authorization
12. ✅ Business logic (due dates, status checks)
13. ✅ Test all scenarios

---

### **Phase 3: Grading & Notifications (Week 5)**

#### **What You'll Learn:**
1. **Background Jobs**
   - Hangfire setup
   - Fire-and-forget jobs
   - Recurring jobs
   - Queue processing

2. **Email Services**
   - SMTP configuration
   - MailKit/SendGrid
   - Email templates
   - Async email sending

3. **Domain Events (Optional)**
   - Event-driven architecture
   - Publish/Subscribe
   - Event handlers

4. **Complex LINQ**
   - Aggregations (Average, Sum, Count)
   - Subqueries
   - GroupBy with multiple keys

---

### **Phase 4: Advanced Features (Week 6)**

#### **What You'll Learn:**
1. **SignalR**
   - Hub configuration
   - Real-time messaging
   - Broadcast to groups

2. **Caching**
   - In-Memory cache
   - Distributed cache (Redis)
   - Cache invalidation

3. **External API Integration**
   - HttpClient
   - Stripe API (payments)
   - Webhooks

4. **Rate Limiting**
   - AspNetCoreRateLimit
   - Per-endpoint limits

---

### **Phase 5: Performance & Security (Week 7)**

#### **What You'll Learn:**
1. **JWT Authentication**
   - Token generation
   - Refresh tokens
   - Token validation

2. **OAuth 2.0**
   - Google login
   - Microsoft login

3. **Two-Factor Authentication**
   - TOTP (Time-based OTP)
   - QR code generation

4. **Query Optimization**
   - AsNoTracking()
   - Select projections
   - Pagination
   - Indexes

5. **Logging**
   - Serilog configuration
   - Structured logging
   - Log levels

---

### **Phase 6: Testing (Week 8)**

#### **What You'll Learn:**
1. **Unit Testing**
   - xUnit basics
   - Moq for mocking
   - FluentAssertions
   - Test repositories
   - Test services

2. **Integration Testing**
   - WebApplicationFactory
   - In-Memory database
   - Test API endpoints

3. **Test Coverage**
   - Coverlet
   - Generate reports

---

### **Phase 7: DevOps (Weeks 9-10)**

#### **What You'll Learn:**
1. **Docker**
   - Dockerfile
   - Docker Compose
   - Multi-stage builds

2. **Azure Deployment**
   - Azure App Service
   - Azure SQL Database
   - Application Insights

3. **CI/CD**
   - GitHub Actions
   - Automated testing
   - Automated deployment

---

## 📚 Learning Resources

### Official Microsoft Docs:
- [ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/)
- [Entity Framework Core](https://learn.microsoft.com/en-us/ef/core/)
- [ASP.NET Core Identity](https://learn.microsoft.com/en-us/aspnet/core/security/authentication/identity)

### Recommended YouTube Channels:
- [Nick Chapsas](https://www.youtube.com/@nickchapsas)
- [Milan Jovanović](https://www.youtube.com/@MilanJovanovicTech)
- [IAmTimCorey](https://www.youtube.com/@IAmTimCorey)

### Books (Optional):
- "Clean Architecture" by Robert C. Martin
- "Domain-Driven Design" by Eric Evans (Advanced)

---

## 🎯 Learning Checklist (Phase 1)

### EF Core:
- [ ] Understand DbContext
- [ ] Configure connection string
- [ ] Create entity classes
- [ ] Write Fluent API configurations
- [ ] Create migrations
- [ ] Update database
- [ ] Query data with LINQ

### Clean Architecture:
- [ ] Understand layer separation
- [ ] Implement Repository pattern
- [ ] Implement Unit of Work
- [ ] Use Dependency Injection

### API Development:
- [ ] Create controllers
- [ ] Implement CRUD endpoints
- [ ] Use DTOs
- [ ] Add validation
- [ ] Handle exceptions
- [ ] Test with Swagger

### Identity:
- [ ] Setup ASP.NET Core Identity
- [ ] Implement registration
- [ ] Implement login
- [ ] Hash passwords
- [ ] Send confirmation emails

---

## 💡 Learning Tips

1. **One Concept at a Time**
   - Don't rush, master each topic before moving on

2. **Read Error Messages**
   - Errors are your best teachers
   - Google error messages

3. **Experiment**
   - Try different approaches
   - Break things and fix them

4. **Use Debugger**
   - Set breakpoints
   - Inspect variables
   - Step through code

5. **Ask Questions**
   - When stuck, ask me!
   - Provide context and error details

6. **Practice, Practice, Practice**
   - Code every day
   - Repetition builds muscle memory

---

## 🚦 How to Start

1. ✅ Read `PROJECT_REQUIREMENTS.md`
2. ✅ Review `DATABASE_SCHEMA.md`
3. ✅ Understand `ARCHITECTURE.md`
4. ✅ Read this roadmap
5. ⏭️ Say **"I'm ready to start Phase 1"**
6. ⏭️ I'll guide you step-by-step!

---

**Remember:** Learning takes time. Be patient with yourself! 🌱

**You got this! 💪🚀**
