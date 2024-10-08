
# Domain Layer Overview

This document provides an overview of the structure and purpose of the folders within the Domain layer of the Clean Architecture project. The Domain layer represents the core business logic and rules, and it should remain independent of other layers, such as infrastructure or application services.

## Folder Structure

**Constants**
   - This folder contains static values used throughout the domain, such as configuration settings, limit values, or fixed strings. Constants help keep the code maintainable and reduce hardcoding throughout the domain logic.
   - **Example**:
     ```csharp
     public static class OrderConstants
     {
         public const int MaxOrderItems = 50;
         public const string DefaultCurrency = "USD";
     }
     ```

**Entities**
   - Entities represent core business objects in your system. These are the primary objects that contain attributes and behavior relevant to the domain. An entity typically has an identity (ID).
   - **Example**:
     ```csharp
     public class Order
     {
         public int Id { get; set; }
         public DateTime CreatedAt { get; set; }
         public List<OrderItem> Items { get; set; }
     }
     ```

**Enums**
   - This folder contains enumerations that define a set of named constants. These are often used to represent a fixed number of related values in the domain, such as statuses or categories.
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

**Exceptions**
   - Custom exceptions specific to the domain are stored in this folder. These exceptions represent business-related errors and should be handled in a meaningful way, avoiding the use of generic exceptions like `NullReferenceException`.
   - **Example**:
     ```csharp
     public class ProductOutOfStockException : Exception
     {
         public ProductOutOfStockException(string productName)
             : base($"Product {productName} is out of stock.") { }
     }
     ```

**Repositories**
   - Repositories abstract away the details of data access from the domain logic. This folder typically contains interfaces (e.g., `IOrderRepository`) and classes responsible for retrieving and storing entities.
   - **Example**:
     ```csharp
     public interface IOrderRepository
     {
         Task<Order> GetByIdAsync(int id);
         Task SaveAsync(Order order);
     }
     ```

**ValueObjects**
   - Value Objects are immutable objects that represent concepts from the domain that don’t have an identity but are defined by their values. Value objects often help prevent primitive obsession and provide more meaningful types.
   - **Example**:
     ```csharp
     public class Money
     {
         public decimal Amount { get; }
         public string Currency { get; }

         public Money(decimal amount, string currency)
         {
             Amount = amount;
             Currency = currency;
         }
     }
     ```