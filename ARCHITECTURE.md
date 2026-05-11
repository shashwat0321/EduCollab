# EduCollab - Architecture Documentation

## 🏛️ Architecture Overview

**Pattern:** Clean Architecture with Vertical Slice (per service)  
**Communication:** RESTful APIs  
**Database:** SQL Server  
**Authentication:** JWT + ASP.NET Core Identity

---

## 📁 Solution Structure

```
📦 C:\myDrive\Practice\PracticeAll\
│
├── 📄 PracticeAll.sln                          ← Solution file
│
├── 📄 PROJECT_REQUIREMENTS.md                  ← This and other docs
├── 📄 DATABASE_SCHEMA.md
├── 📄 ARCHITECTURE.md
├── 📄 LEARNING_ROADMAP.md
├── 📄 README.md
├── 📄 .gitignore
│
├── 📁 src\
│   │
│   ├── 📁 Services\                            ← Microservices (if needed)
│   │   │
│   │   └── 📁 CourseManagement\                ← Main service
│   │       │
│   │       ├── 📁 API\                         ← Presentation Layer
│   │       ├── 📁 Application\                 ← Business Logic Layer
│   │       ├── 📁 Domain\                      ← Core Domain Layer
│   │       └── 📁 Infrastructure\              ← Data Access Layer
│   │
│   └── 📁 Shared\                              ← Cross-cutting concerns
│       ├── 📁 Common\                          ← Utilities, Exceptions, Extensions
│       ├── 📁 Middleware\                      ← Reusable middlewares
│       └── 📁 Identity\                        ← Auth & Identity
│
└── 📁 tests\
    ├── 📁 Unit\
    └── 📁 Integration\
```

---

## 🏗️ Clean Architecture Layers

```
┌─────────────────────────────────────────────────────────────┐
│                      API (Presentation)                      │
│  Controllers, DTOs, Filters, Middleware Registration         │
└───────────────────┬─────────────────────────────────────────┘
                    │ depends on
                    ↓
┌─────────────────────────────────────────────────────────────┐
│                   Application (Use Cases)                    │
│  Services, DTOs, Validators, Mappings, Interfaces            │
└───────────────────┬─────────────────────────────────────────┘
                    │ depends on
                    ↓
┌─────────────────────────────────────────────────────────────┐
│                   Domain (Business Logic)                    │
│  Entities, Value Objects, Enums, Domain Events               │
│                    ⚠️ NO DEPENDENCIES                         │
└─────────────────────────────────────────────────────────────┘
                    ↑
                    │ implements
                    │
┌─────────────────────────────────────────────────────────────┐
│              Infrastructure (External Concerns)              │
│  DbContext, Repositories, External Services, Migrations      │
└─────────────────────────────────────────────────────────────┘
```

### Dependency Rule:
✅ **Inner layers NEVER depend on outer layers**
- Domain → No dependencies
- Application → Depends on Domain only
- Infrastructure → Implements Application interfaces, depends on Domain
- API → Depends on Application, Infrastructure (for DI registration only)

---

## 📂 Detailed Layer Breakdown

### **1. Domain Layer** (Core)
**Responsibility:** Pure business logic, NO infrastructure concerns

```
📁 Domain\
├── 📁 Entities\                  ← Business entities
│   ├── BaseEntity.cs             ← Base class with Id, CreatedAt, UpdatedAt
│   ├── Course.cs
│   ├── Enrollment.cs
│   ├── Assignment.cs
│   └── Submission.cs
│
├── 📁 ValueObjects\               ← Immutable value types
│   ├── Money.cs
│   └── CourseStatus.cs
│
├── 📁 Enums\                      ← Domain enumerations
│   ├── EnrollmentStatus.cs
│   ├── CourseLevel.cs
│   └── AssignmentStatus.cs
│
└── 📁 Events\                     ← Domain events (optional for now)
    ├── CourseCreatedEvent.cs
    └── StudentEnrolledEvent.cs
```

**Example Entity:**
```csharp
// Domain/Entities/Course.cs
namespace PracticeAll.CourseManagement.Domain.Entities;

public class Course : BaseEntity
{
    public string Title { get; set; } = string.Empty;
    public string Description { get; set; } = string.Empty;
    public decimal Price { get; set; }
    public CourseLevel Level { get; set; }
    public CourseStatus Status { get; set; }
    public Guid InstructorId { get; set; }
    public Guid CategoryId { get; set; }

    // Navigation properties
    public Category Category { get; set; } = null!;
    public ICollection<Enrollment> Enrollments { get; set; } = new List<Enrollment>();
    public ICollection<Assignment> Assignments { get; set; } = new List<Assignment>();
}
```

---

### **2. Application Layer** (Use Cases)
**Responsibility:** Business logic orchestration, validation, DTOs

```
📁 Application\
├── 📁 Services\                   ← Business logic implementation
│   ├── 📁 Interfaces\
│   │   ├── ICourseService.cs
│   │   ├── IEnrollmentService.cs
│   │   └── IAssignmentService.cs
│   │
│   └── 📁 Implementations\
│       ├── CourseService.cs
│       ├── EnrollmentService.cs
│       └── AssignmentService.cs
│
├── 📁 DTOs\                       ← Data Transfer Objects
│   ├── CourseDto.cs
│   ├── CreateCourseDto.cs
│   ├── UpdateCourseDto.cs
│   └── EnrollmentDto.cs
│
├── 📁 Mappings\                   ← AutoMapper profiles
│   └── CourseMappingProfile.cs
│
├── 📁 Validators\                 ← FluentValidation validators
│   ├── CreateCourseDtoValidator.cs
│   └── UpdateCourseDtoValidator.cs
│
├── 📁 Interfaces\                 ← Repository contracts
│   ├── ICourseRepository.cs
│   ├── IEnrollmentRepository.cs
│   └── IUnitOfWork.cs
│
└── 📁 Extensions\                 ← DI registration
    └── ApplicationServiceExtensions.cs
```

**Example Service:**
```csharp
// Application/Services/Implementations/CourseService.cs
namespace PracticeAll.CourseManagement.Application.Services.Implementations;

public class CourseService : ICourseService
{
    private readonly ICourseRepository _courseRepository;
    private readonly IUnitOfWork _unitOfWork;
    private readonly IMapper _mapper;

    public CourseService(
        ICourseRepository courseRepository,
        IUnitOfWork unitOfWork,
        IMapper mapper)
    {
        _courseRepository = courseRepository;
        _unitOfWork = unitOfWork;
        _mapper = mapper;
    }

    public async Task<CourseDto> CreateAsync(CreateCourseDto dto)
    {
        var course = _mapper.Map<Course>(dto);
        await _courseRepository.AddAsync(course);
        await _unitOfWork.SaveChangesAsync();
        return _mapper.Map<CourseDto>(course);
    }
}
```

**DI Registration:**
```csharp
// Application/Extensions/ApplicationServiceExtensions.cs
namespace PracticeAll.CourseManagement.Application.Extensions;

public static class ApplicationServiceExtensions
{
    public static IServiceCollection AddApplication(this IServiceCollection services)
    {
        // AutoMapper
        services.AddAutoMapper(Assembly.GetExecutingAssembly());

        // FluentValidation
        services.AddValidatorsFromAssembly(Assembly.GetExecutingAssembly());

        // Services
        services.AddScoped<ICourseService, CourseService>();
        services.AddScoped<IEnrollmentService, EnrollmentService>();

        return services;
    }
}
```

---

### **3. Infrastructure Layer** (Data & External Services)
**Responsibility:** Database access, external APIs, file storage

```
📁 Infrastructure\
├── 📁 Data\                       ← EF Core DbContext
│   ├── ApplicationDbContext.cs
│   │
│   ├── 📁 Configurations\         ← Fluent API configurations
│   │   ├── CourseConfiguration.cs
│   │   ├── EnrollmentConfiguration.cs
│   │   └── AssignmentConfiguration.cs
│   │
│   └── 📁 Migrations\             ← EF Core migrations (auto-generated)
│       └── 20250101_Initial.cs
│
├── 📁 Repositories\               ← Repository implementations
│   ├── BaseRepository.cs
│   ├── CourseRepository.cs
│   ├── EnrollmentRepository.cs
│   └── UnitOfWork.cs
│
├── 📁 Services\                   ← External service implementations
│   ├── EmailService.cs
│   └── BlobStorageService.cs
│
└── 📁 Extensions\                 ← DI registration
    └── InfrastructureServiceExtensions.cs
```

**Example DbContext:**
```csharp
// Infrastructure/Data/ApplicationDbContext.cs
namespace PracticeAll.CourseManagement.Infrastructure.Data;

public class ApplicationDbContext : DbContext
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options) { }

    public DbSet<Course> Courses => Set<Course>();
    public DbSet<Enrollment> Enrollments => Set<Enrollment>();
    public DbSet<Assignment> Assignments => Set<Assignment>();

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        // Apply all configurations from assembly
        modelBuilder.ApplyConfigurationsFromAssembly(Assembly.GetExecutingAssembly());
    }
}
```

**Example Configuration:**
```csharp
// Infrastructure/Data/Configurations/CourseConfiguration.cs
namespace PracticeAll.CourseManagement.Infrastructure.Data.Configurations;

public class CourseConfiguration : IEntityTypeConfiguration<Course>
{
    public void Configure(EntityTypeBuilder<Course> builder)
    {
        builder.HasKey(c => c.Id);

        builder.Property(c => c.Title)
            .IsRequired()
            .HasMaxLength(200);

        builder.Property(c => c.Description)
            .IsRequired();

        builder.Property(c => c.Price)
            .HasColumnType("decimal(18,2)");

        // Relationships
        builder.HasOne(c => c.Category)
            .WithMany()
            .HasForeignKey(c => c.CategoryId)
            .OnDelete(DeleteBehavior.Restrict);

        builder.HasMany(c => c.Enrollments)
            .WithOne(e => e.Course)
            .HasForeignKey(e => e.CourseId);
    }
}
```

**DI Registration:**
```csharp
// Infrastructure/Extensions/InfrastructureServiceExtensions.cs
namespace PracticeAll.CourseManagement.Infrastructure.Extensions;

public static class InfrastructureServiceExtensions
{
    public static IServiceCollection AddInfrastructure(
        this IServiceCollection services,
        IConfiguration configuration)
    {
        // DbContext
        services.AddDbContext<ApplicationDbContext>(options =>
            options.UseSqlServer(
                configuration.GetConnectionString("DefaultConnection")));

        // Repositories
        services.AddScoped<ICourseRepository, CourseRepository>();
        services.AddScoped<IEnrollmentRepository, EnrollmentRepository>();
        services.AddScoped<IUnitOfWork, UnitOfWork>();

        // External Services
        services.AddScoped<IEmailService, EmailService>();

        return services;
    }
}
```

---

### **4. API Layer** (Presentation)
**Responsibility:** HTTP endpoints, request/response handling

```
📁 API\
├── 📁 Controllers\                ← API controllers
│   ├── CoursesController.cs
│   ├── EnrollmentsController.cs
│   └── AssignmentsController.cs
│
├── 📁 Models\                     ← API-specific request/response models
│   ├── 📁 Requests\
│   │   ├── CreateCourseRequest.cs
│   │   └── UpdateCourseRequest.cs
│   │
│   └── 📁 Responses\
│       ├── CourseResponse.cs
│       └── ApiResponse.cs
│
├── 📁 Filters\                    ← Action filters
│   ├── ValidateModelAttribute.cs
│   └── CacheAttribute.cs
│
├── 📁 Extensions\                 ← API-specific DI
│   ├── SwaggerServiceExtensions.cs
│   └── CorsServiceExtensions.cs
│
├── 📁 Properties\
│   └── launchSettings.json
│
├── appsettings.json
├── appsettings.Development.json
├── appsettings.Production.json
└── Program.cs                     ← Application entry point
```

**Example Controller:**
```csharp
// API/Controllers/CoursesController.cs
namespace PracticeAll.CourseManagement.API.Controllers;

[ApiController]
[Route("api/[controller]")]
public class CoursesController : ControllerBase
{
    private readonly ICourseService _courseService;

    public CoursesController(ICourseService courseService)
    {
        _courseService = courseService;
    }

    [HttpGet]
    [ProducesResponseType(typeof(IEnumerable<CourseDto>), StatusCodes.Status200OK)]
    public async Task<IActionResult> GetAll()
    {
        var courses = await _courseService.GetAllAsync();
        return Ok(courses);
    }

    [HttpPost]
    [Authorize(Roles = "Instructor,Admin")]
    [ProducesResponseType(typeof(CourseDto), StatusCodes.Status201Created)]
    public async Task<IActionResult> Create([FromBody] CreateCourseDto dto)
    {
        var course = await _courseService.CreateAsync(dto);
        return CreatedAtAction(nameof(GetById), new { id = course.Id }, course);
    }
}
```

**Program.cs:**
```csharp
// API/Program.cs
var builder = WebApplication.CreateBuilder(args);

// ===== Add Services =====
builder.Services.AddControllers();

// Layer Registration
builder.Services.AddApplication();
builder.Services.AddInfrastructure(builder.Configuration);
builder.Services.AddSharedServices(builder.Configuration);

// API Services
builder.Services.AddSwaggerGen();
builder.Services.AddCors(options =>
{
    options.AddPolicy("AllowAll", policy =>
        policy.AllowAnyOrigin().AllowAnyMethod().AllowAnyHeader());
});

var app = builder.Build();

// ===== Configure Middleware Pipeline =====
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

// Shared Middlewares
app.UseExceptionHandling();
app.UseRequestLogging();

app.UseHttpsRedirection();
app.UseAuthentication();
app.UseAuthorization();
app.MapControllers();

app.Run();
```

---

## 🔗 Shared Projects (Cross-Cutting Concerns)

### **1. Common (Shared.Common)**
```
📁 Common\
├── 📁 Constants\
│   ├── ErrorMessages.cs
│   └── ApplicationConstants.cs
│
├── 📁 Exceptions\                 ← ALL custom exceptions
│   ├── BaseException.cs
│   ├── NotFoundException.cs
│   ├── ValidationException.cs
│   └── UnauthorizedException.cs
│
├── 📁 Extensions\
│   ├── StringExtensions.cs
│   └── QueryableExtensions.cs
│
├── 📁 Models\                     ← Shared response models
│   ├── ApiResponse.cs
│   ├── PagedResult.cs
│   └── ErrorResponse.cs
│
└── 📁 Helpers\
    └── PasswordHelper.cs
```

**Example Exception:**
```csharp
// Common/Exceptions/NotFoundException.cs
namespace PracticeAll.Shared.Common.Exceptions;

public class NotFoundException : BaseException
{
    public NotFoundException(string entityName, object key)
        : base($"{entityName} with key '{key}' was not found.")
    {
    }
}
```

---

### **2. Middleware (Shared.Middleware)**
```
📁 Middleware\
├── ExceptionHandlingMiddleware.cs
├── RequestLoggingMiddleware.cs
├── PerformanceMonitoringMiddleware.cs
│
└── 📁 Extensions\
    └── MiddlewareExtensions.cs
```

**Example Middleware:**
```csharp
// Middleware/ExceptionHandlingMiddleware.cs
namespace PracticeAll.Shared.Middleware;

public class ExceptionHandlingMiddleware
{
    private readonly RequestDelegate _next;
    private readonly ILogger<ExceptionHandlingMiddleware> _logger;

    public ExceptionHandlingMiddleware(
        RequestDelegate next,
        ILogger<ExceptionHandlingMiddleware> logger)
    {
        _next = next;
        _logger = logger;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        try
        {
            await _next(context);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "An unhandled exception occurred");
            await HandleExceptionAsync(context, ex);
        }
    }

    private static Task HandleExceptionAsync(HttpContext context, Exception exception)
    {
        context.Response.ContentType = "application/json";

        var response = exception switch
        {
            NotFoundException => (StatusCodes.Status404NotFound, "Not Found"),
            ValidationException => (StatusCodes.Status400BadRequest, "Validation Error"),
            _ => (StatusCodes.Status500InternalServerError, "Internal Server Error")
        };

        context.Response.StatusCode = response.Item1;
        return context.Response.WriteAsJsonAsync(new { error = response.Item2 });
    }
}
```

**Extension:**
```csharp
// Middleware/Extensions/MiddlewareExtensions.cs
namespace PracticeAll.Shared.Middleware.Extensions;

public static class MiddlewareExtensions
{
    public static IApplicationBuilder UseExceptionHandling(this IApplicationBuilder app)
    {
        return app.UseMiddleware<ExceptionHandlingMiddleware>();
    }

    public static IApplicationBuilder UseRequestLogging(this IApplicationBuilder app)
    {
        return app.UseMiddleware<RequestLoggingMiddleware>();
    }
}
```

---

### **3. Identity (Shared.Identity)**
```
📁 Identity\
├── 📁 Models\
│   ├── ApplicationUser.cs
│   ├── ApplicationRole.cs
│   └── RefreshToken.cs
│
├── 📁 Services\
│   ├── 📁 Interfaces\
│   │   ├── IAuthService.cs
│   │   └── ITokenService.cs
│   │
│   └── 📁 Implementations\
│       ├── AuthService.cs
│       └── TokenService.cs
│
├── 📁 Configuration\
│   └── IdentityConfiguration.cs
│
├── 📁 Constants\
│   └── Roles.cs
│
└── 📁 Extensions\
    └── IdentityServiceExtensions.cs
```

**Example Identity Setup:**
```csharp
// Identity/Extensions/IdentityServiceExtensions.cs
namespace PracticeAll.Shared.Identity.Extensions;

public static class IdentityServiceExtensions
{
    public static IServiceCollection AddIdentityServices(
        this IServiceCollection services,
        IConfiguration configuration)
    {
        // Identity
        services.AddIdentity<ApplicationUser, ApplicationRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();

        // JWT
        services.AddAuthentication(options =>
        {
            options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
            options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
        })
        .AddJwtBearer(options =>
        {
            options.TokenValidationParameters = new TokenValidationParameters
            {
                ValidateIssuer = true,
                ValidateAudience = true,
                ValidateLifetime = true,
                ValidateIssuerSigningKey = true,
                ValidIssuer = configuration["Jwt:Issuer"],
                ValidAudience = configuration["Jwt:Audience"],
                IssuerSigningKey = new SymmetricSecurityKey(
                    Encoding.UTF8.GetBytes(configuration["Jwt:Key"]!))
            };
        });

        // Services
        services.AddScoped<IAuthService, AuthService>();
        services.AddScoped<ITokenService, TokenService>();

        return services;
    }
}
```

---

## 🔄 Data Flow Example

### Create Course Flow:
```
1. HTTP POST /api/courses
   ↓
2. CoursesController.Create(CreateCourseRequest)
   ↓
3. Map Request → CreateCourseDto
   ↓
4. ICourseService.CreateAsync(CreateCourseDto)
   ↓
5. Validate DTO (FluentValidation)
   ↓
6. Map DTO → Course (Entity)
   ↓
7. ICourseRepository.AddAsync(Course)
   ↓
8. IUnitOfWork.SaveChangesAsync()
   ↓
9. Map Course → CourseDto
   ↓
10. Return CourseDto
   ↓
11. Map DTO → CourseResponse
   ↓
12. HTTP 201 Created
```

---

## 🎯 Design Patterns Used

| Pattern | Usage | Location |
|---------|-------|----------|
| **Repository** | Data access abstraction | Infrastructure/Repositories |
| **Unit of Work** | Transaction management | Infrastructure/UnitOfWork |
| **Dependency Injection** | Loose coupling | Throughout (via extensions) |
| **Factory** | Object creation | (Future: for complex entities) |
| **Strategy** | (Future: Different payment gateways) | Infrastructure/Services |
| **Specification** | (Optional: Complex queries) | Domain/Specifications |
| **CQRS** | (Optional: Command/Query separation) | Application layer |

---

## 📊 Project Dependencies Graph

```
API
 ├─→ Application
 │    └─→ Domain
 └─→ Infrastructure (DI only)
      ├─→ Application (implements interfaces)
      └─→ Domain

Shared.Common      → (No dependencies)
Shared.Middleware  → Shared.Common
Shared.Identity    → Shared.Common
```

---

## ⚙️ Configuration Files

### appsettings.json
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=EduCollabDb;Trusted_Connection=true;MultipleActiveResultSets=true"
  },
  "Jwt": {
    "Key": "YourSuperSecretKeyHere_AtLeast32Characters!",
    "Issuer": "EduCollab",
    "Audience": "EduCollabUsers",
    "ExpiryInMinutes": 60
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

---

## 🧪 Testing Strategy

```
📁 tests\
├── 📁 Unit\
│   ├── Services\
│   │   ├── CourseServiceTests.cs
│   │   └── EnrollmentServiceTests.cs
│   │
│   └── Validators\
│       └── CreateCourseDtoValidatorTests.cs
│
└── 📁 Integration\
    └── Controllers\
        └── CoursesControllerTests.cs
```

---

## 🚀 Build & Run Commands

```powershell
# Restore packages
dotnet restore

# Build solution
dotnet build

# Run API project
dotnet run --project src/Services/CourseManagement/API

# Create migration
dotnet ef migrations add InitialCreate --project src/Services/CourseManagement/Infrastructure --startup-project src/Services/CourseManagement/API

# Update database
dotnet ef database update --project src/Services/CourseManagement/Infrastructure --startup-project src/Services/CourseManagement/API

# Run tests
dotnet test
```

---

## 📝 Next Steps

1. ✅ Review architecture
2. ⏭️ Scaffold projects
3. ⏭️ Implement Domain entities
4. ⏭️ Implement Infrastructure (DbContext, Repositories)
5. ⏭️ Implement Application services
6. ⏭️ Implement API controllers

---

**Architecture Version:** 1.0  
**Pattern:** Clean Architecture  
**Framework:** .NET 10
