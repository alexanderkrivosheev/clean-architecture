
# Infrastructure Layer Overview

The Infrastructure layer in the Clean Architecture project handles external system interactions, such as database access, API integration, logging, and security. Each folder within the Infrastructure layer plays a specific role in supporting the application without directly affecting the business logic.

## Folder Structure

**Configurations**
   - This folder contains configuration classes that handle settings and environment-specific details, such as database connection strings or third-party service configurations.
   - **Example**:
     ```csharp
     public class DatabaseConfiguration
     {
         public string ConnectionString { get; set; }
         public int Timeout { get; set; }
     }
     ```
   - Example of binding configurations:
     ```csharp
     public static class ServiceConfiguration
     {
         public static IServiceCollection ConfigureDatabase(this IServiceCollection services, IConfiguration configuration)
         {
             var dbConfig = new DatabaseConfiguration();
             configuration.Bind("Database", dbConfig);
             services.AddSingleton(dbConfig);
             services.AddDbContext<AppDbContext>(options =>
                 options.UseSqlServer(dbConfig.ConnectionString));
             return services;
         }
     }
     ```

**Logging**
   - This folder contains classes that manage logging for the application. It’s essential for tracking application activity and debugging.
   - **Example**:
     ```csharp
     public class FileLogger : ILogger
     {
         private readonly string _filePath;

         public FileLogger(string filePath)
         {
             _filePath = filePath;
         }

         public void Log(string message)
         {
             File.AppendAllText(_filePath, $"{DateTime.Now}: {message}{Environment.NewLine}");
         }
     }
     ```

**Migrations**
   - This folder holds database migration classes. Migrations allow you to change the database schema over time while keeping the data intact.
   - **Example**:
     ```bash
     dotnet ef migrations add InitialCreate
     dotnet ef database update
     ```
   - Migration code example:
     ```csharp
     public partial class InitialCreate : Migration
     {
         protected override void Up(MigrationBuilder migrationBuilder)
         {
             migrationBuilder.CreateTable(
                 name: "Users",
                 columns: table => new
                 {
                     Id = table.Column<int>(nullable: false)
                         .Annotation("SqlServer:Identity", "1, 1"),
                     Name = table.Column<string>(nullable: false),
                     Email = table.Column<string>(nullable: false)
                 },
                 constraints: table =>
                 {
                     table.PrimaryKey("PK_Users", x => x.Id);
                 });
         }

         protected override void Down(MigrationBuilder migrationBuilder)
         {
             migrationBuilder.DropTable(name: "Users");
         }
     }
     ```

**Repositories**
   - Repositories contain implementations for interacting with the database and data stores. They abstract the data access layer and perform CRUD operations.
   - **Example**:
     ```csharp
     public class UserRepository : IUserRepository
     {
         private readonly AppDbContext _context;

         public UserRepository(AppDbContext context)
         {
             _context = context;
         }

         public async Task<User> GetByIdAsync(int id)
         {
             return await _context.Users.FindAsync(id);
         }

         public async Task AddAsync(User user)
         {
             await _context.Users.AddAsync(user);
             await _context.SaveChangesAsync();
         }
     }
     ```

**Security**
   - This folder manages security aspects such as authentication, authorization, and data encryption. It can include classes for handling JWT tokens, user roles, or password hashing.
   - **Example**:
     ```csharp
     public class JwtTokenService : ITokenService
     {
         private readonly string _secret;

         public JwtTokenService(string secret)
         {
             _secret = secret;
         }

         public string GenerateToken(User user)
         {
             var tokenHandler = new JwtSecurityTokenHandler();
             var key = Encoding.ASCII.GetBytes(_secret);
             var tokenDescriptor = new SecurityTokenDescriptor
             {
                 Subject = new ClaimsIdentity(new[]
                 {
                     new Claim(ClaimTypes.Name, user.Id.ToString())
                 }),
                 Expires = DateTime.UtcNow.AddHours(1),
                 SigningCredentials = new SigningCredentials(new SymmetricSecurityKey(key), SecurityAlgorithms.HmacSha256Signature)
             };
             var token = tokenHandler.CreateToken(tokenDescriptor);
             return tokenHandler.WriteToken(token);
         }
     }
     ```

**Services**
   - This folder contains services that implement general infrastructure tasks such as interaction with external APIs, sending notifications, caching, and other utilities.
   - **Example**:
     ```csharp
     public class EmailService : IEmailService
     {
         public Task SendEmailAsync(string to, string subject, string body)
         {
             // Logic for sending email
         }
     }
     ```