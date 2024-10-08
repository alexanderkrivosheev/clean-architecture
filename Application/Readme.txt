
# Application Layer Overview

This document provides an overview of the structure and purpose of the folders within the Application layer of the Clean Architecture project. The Application layer is responsible for handling the coordination of commands and queries and interacting with external data sources.

## Folder Structure

**Exceptions**
   - In this folder, you'll find custom exceptions used to handle business-related errors in a meaningful way. These exceptions allow for clear separation of domain errors from technical exceptions like `NullReferenceException`.
   - **Example**:
     ```csharp
     public class UserNotFoundException : Exception
     {
         public UserNotFoundException(int userId)
             : base($"User with ID {userId} was not found.") { }
     }
     ```

**Constants**
   - This folder contains static values used throughout the project, such as configuration settings, limit values, or fixed strings. Constants help keep the code maintainable and reduce hardcoding throughout the project.
   - **Example**:
     ```csharp
     public static class OrderConstants
     {
         public const int MaxOrderItems = 50;
         public const string DefaultCurrency = "USD";
     }
     ```

**Enums**
   - This folder contains enumerations that define a set of named constants. These are often used to represent a fixed number of related values in the project, such as statuses or categories.
   - **Example**:
     ```csharp
     public enum OrderStatus
     {
         Pending,
         Processed,
         Shipped,
         Delivered
     }
     ```

**Models**
   - Models or DTOs (Data Transfer Objects) in this folder represent the data structures used to transfer information between application layers or to external systems. They are typically simplified compared to domain entities.
   - **Example**:
     ```csharp
     public class UserModel
     {
         public int Id { get; set; }
         public string Name { get; set; }
         public string Email { get; set; }
     }
     ```

**Services**
   - This folder contains services that implement the core business logic of the application. These services are responsible for handling commands, queries, and processing data. They often use the dependencies defined in the `Dependencies` folder to perform their tasks.
   - **Example**:
     ```csharp
     public class UserService : IUserService
     {
         private readonly IDatabaseService _databaseService;

         public UserService(IDatabaseService databaseService)
         {
             _databaseService = databaseService;
         }

         public async Task<UserModel> GetUserByIdAsync(int userId)
         {
             var user = await _databaseService.GetAsync<User>(userId);
             if (user == null)
             {
                 throw new UserNotFoundException(userId);
             }
             
             return new UserModel
             {
                 Id = user.Id,
                 Name = user.Name,
                 Email = user.Email
             };
         }
     }
     ```

**Readme.txt**
   - This file contains documentation or a description of the structure and purpose of the Application layer. It is useful for developers who are new to the project to understand the overall architecture and the role of each folder.