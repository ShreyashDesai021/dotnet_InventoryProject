1. Interfaces/IEntity.cs
namespace StoreInventory.Interfaces;

public interface IEntity
{
    int Id { get; set; }
}
2. Interfaces/IRepository.cs
namespace StoreInventory.Interfaces;

public interface IRepository<T> where T : IEntity
{
    Task AddAsync(T item);

    Task<T?> GetByIdAsync(int id);

    Task<List<T>> GetAllAsync();

    Task UpdateAsync(T item);

    Task DeleteAsync(int id);
}
3. Models/Product.cs
using StoreInventory.Interfaces;

namespace StoreInventory.Models;

public class Product : IEntity
{
    public int Id { get; set; }

    public string Name { get; set; } = "";

    public decimal Price { get; set; }

    public int StockQuantity { get; set; }
}
4. Models/Customer.cs
using StoreInventory.Interfaces;

namespace StoreInventory.Models;

public class Customer : IEntity
{
    public int Id { get; set; }

    public string Name { get; set; } = "";

    public string Email { get; set; } = "";

    public string Phone { get; set; } = "";
}
5. Models/OrderStatus.cs
namespace StoreInventory.Models;

public enum OrderStatus
{
    Pending,
    Confirmed,
    Cancelled,
    Completed
}
6. Models/OrderItem.cs
namespace StoreInventory.Models;

public class OrderItem
{
    public int ProductId { get; set; }

    public int Quantity { get; set; }

    public decimal UnitPrice { get; set; }
}
7. Models/Order.cs
using StoreInventory.Interfaces;

namespace StoreInventory.Models;

public class Order : IEntity
{
    public int Id { get; set; }

    public int CustomerId { get; set; }

    public List<OrderItem> Items { get; set; } = new();

    public DateTime OrderDate { get; set; }

    public decimal TotalAmount { get; set; }

    public decimal Discount { get; set; }

    public OrderStatus Status { get; set; }
}
8. Exceptions/OutOfStockException.cs
namespace StoreInventory.Exceptions;

public class OutOfStockException : Exception
{
    public OutOfStockException(string message)
        : base(message)
    {
    }
}
9. Repositories/InMemoryRepository.cs

This is the generic in-memory CRUD repository.

using StoreInventory.Interfaces;

namespace StoreInventory.Repositories;

public class InMemoryRepository<T> : IRepository<T>
    where T : IEntity
{
    private readonly List<T> items = new();

    public async Task AddAsync(T item)
    {
        await Task.Delay(500);

        if (items.Any(x => x.Id == item.Id))
        {
            throw new InvalidOperationException(
                $"An item with ID {item.Id} already exists.");
        }

        items.Add(item);
    }

    public async Task<T?> GetByIdAsync(int id)
    {
        await Task.Delay(500);

        return items.FirstOrDefault(x => x.Id == id);
    }

    public async Task<List<T>> GetAllAsync()
    {
        await Task.Delay(500);

        return items.ToList();
    }

    public async Task UpdateAsync(T item)
    {
        await Task.Delay(500);

        var existingItem = items.FirstOrDefault(x => x.Id == item.Id);

        if (existingItem == null)
        {
            throw new KeyNotFoundException(
                $"Item with ID {item.Id} was not found.");
        }

        int index = items.IndexOf(existingItem);

        items[index] = item;
    }

    public async Task DeleteAsync(int id)
    {
        await Task.Delay(500);

        var item = items.FirstOrDefault(x => x.Id == id);

        if (item == null)
        {
            throw new KeyNotFoundException(
                $"Item with ID {id} was not found.");
        }

        items.Remove(item);
    }
}
This file demonstrates:
Generics
   ↓
IRepository<T>
   ↓
InMemoryRepository<T>
   ↓
List<T>
   ↓
CRUD
   ↓
async/await
   ↓
Task.Delay(500)
10. Services/StoreService.cs

This is the most important business-logic file.

using StoreInventory.Exceptions;
using StoreInventory.Interfaces;
using StoreInventory.Models;

namespace StoreInventory.Services;

public class StoreService
{
    private readonly IRepository<Product> productRepository;
    private readonly IRepository<Customer> customerRepository;
    private readonly IRepository<Order> orderRepository;

    public StoreService(
        IRepository<Product> productRepository,
        IRepository<Customer> customerRepository,
        IRepository<Order> orderRepository)
    {
        this.productRepository = productRepository;
        this.customerRepository = customerRepository;
        this.orderRepository = orderRepository;
    }

    // =========================
    // PRODUCT OPERATIONS
    // =========================

    public async Task AddProductAsync(Product product)
    {
        await productRepository.AddAsync(product);
    }

    public async Task<List<Product>> GetProductsAsync()
    {
        return await productRepository.GetAllAsync();
    }

    public async Task<Product?> GetProductAsync(int id)
    {
        return await productRepository.GetByIdAsync(id);
    }

    public async Task UpdateProductAsync(Product product)
    {
        await productRepository.UpdateAsync(product);
    }

    public async Task DeleteProductAsync(int id)
    {
        await productRepository.DeleteAsync(id);
    }

    // =========================
    // CUSTOMER OPERATIONS
    // =========================

    public async Task AddCustomerAsync(Customer customer)
    {
        await customerRepository.AddAsync(customer);
    }

    public async Task<List<Customer>> GetCustomersAsync()
    {
        return await customerRepository.GetAllAsync();
    }

    public async Task<Customer?> GetCustomerAsync(int id)
    {
        return await customerRepository.GetByIdAsync(id);
    }

    public async Task UpdateCustomerAsync(Customer customer)
    {
        await customerRepository.UpdateAsync(customer);
    }

    public async Task DeleteCustomerAsync(int id)
    {
        await customerRepository.DeleteAsync(id);
    }

    // =========================
    // ORDER / CHECKOUT
    // =========================

    public async Task<Order> CheckoutAsync(
        int customerId,
        List<OrderItem> items)
    {
        if (items == null || items.Count == 0)
        {
            throw new InvalidOperationException(
                "An order must contain at least one product.");
        }

        // Check customer
        var customer =
            await customerRepository.GetByIdAsync(customerId);

        if (customer == null)
        {
            throw new KeyNotFoundException(
                $"Customer with ID {customerId} was not found.");
        }

        // Validate all items BEFORE changing inventory.
        var products = new List<Product>();

        foreach (var item in items)
        {
            if (item.Quantity <= 0)
            {
                throw new InvalidOperationException(
                    "Quantity must be greater than zero.");
            }

            var product =
                await productRepository.GetByIdAsync(item.ProductId);

            if (product == null)
            {
                throw new KeyNotFoundException(
                    $"Product with ID {item.ProductId} was not found.");
            }

            if (product.StockQuantity < item.Quantity)
            {
                throw new OutOfStockException(
                    $"Insufficient stock for {product.Name}. " +
                    $"Available: {product.StockQuantity}, " +
                    $"Requested: {item.Quantity}.");
            }

            products.Add(product);
        }

        // Calculate subtotal
        decimal subtotal = 0;

        for (int i = 0; i < items.Count; i++)
        {
            items[i].UnitPrice = products[i].Price;

            subtotal +=
                products[i].Price * items[i].Quantity;
        }

        // Apply 10% discount if subtotal is greater than ₹5,000.
        decimal discount = subtotal > 5000
            ? subtotal * 0.10m
            : 0;

        decimal total = subtotal - discount;

        // Update inventory ONLY after all stock checks passed.
        for (int i = 0; i < items.Count; i++)
        {
            products[i].StockQuantity -= items[i].Quantity;

            await productRepository.UpdateAsync(products[i]);
        }

        // Generate next order ID.
        var existingOrders =
            await orderRepository.GetAllAsync();

        int nextOrderId = existingOrders.Count == 0
            ? 1
            : existingOrders.Max(order => order.Id) + 1;

        // Create order.
        var order = new Order
        {
            Id = nextOrderId,
            CustomerId = customerId,
            Items = items,
            OrderDate = DateTime.Now,
            TotalAmount = total,
            Discount = discount,
            Status = OrderStatus.Completed
        };

        await orderRepository.AddAsync(order);

        return order;
    }
}
11. Services/ReportService.cs

This contains the required LINQ reports.

using StoreInventory.Models;

namespace StoreInventory.Services;

public class ReportService
{
    // ==========================================
    // 1. TOP-SELLING PRODUCTS
    // ==========================================

    public List<(int ProductId, int QuantitySold)>
        GetTopSellingProducts(List<Order> orders)
    {
        return orders
            .SelectMany(order => order.Items)
            .GroupBy(item => item.ProductId)
            .Select(group => (
                ProductId: group.Key,
                QuantitySold: group.Sum(item => item.Quantity)
            ))
            .OrderByDescending(x => x.QuantitySold)
            .ToList();
    }

    // ==========================================
    // 2. REVENUE PER CUSTOMER
    // ==========================================

    public List<(int CustomerId, decimal Revenue)>
        GetRevenuePerCustomer(List<Order> orders)
    {
        return orders
            .GroupBy(order => order.CustomerId)
            .Select(group => (
                CustomerId: group.Key,
                Revenue: group.Sum(order => order.TotalAmount)
            ))
            .OrderByDescending(x => x.Revenue)
            .ToList();
    }

    // ==========================================
    // 3. LOW-STOCK ALERTS
    // ==========================================

    public List<Product> GetLowStockProducts(
        List<Product> products)
    {
        return products
            .Where(product => product.StockQuantity < 5)
            .OrderBy(product => product.StockQuantity)
            .ToList();
    }

    // ==========================================
    // 4. ORDERS BY DATE RANGE
    // ==========================================

    public List<Order> GetOrdersByDateRange(
        List<Order> orders,
        DateTime from,
        DateTime to)
    {
        return orders
            .Where(order =>
                order.OrderDate >= from &&
                order.OrderDate <= to)
            .OrderBy(order => order.OrderDate)
            .ToList();
    }
}
12. Program.cs

This is the biggest file.

Replace your entire Program.cs with the following.

using StoreInventory.Interfaces;
using StoreInventory.Models;
using StoreInventory.Repositories;
using StoreInventory.Services;
using StoreInventory.Exceptions;

// ============================================================
// REPOSITORIES
// ============================================================

IRepository<Product> productRepository =
    new InMemoryRepository<Product>();

IRepository<Customer> customerRepository =
    new InMemoryRepository<Customer>();

IRepository<Order> orderRepository =
    new InMemoryRepository<Order>();

// ============================================================
// SERVICES
// ============================================================

StoreService storeService = new(
    productRepository,
    customerRepository,
    orderRepository);

ReportService reportService = new();

// ============================================================
// PRODUCT MANAGEMENT
// ============================================================

async Task AddProduct()
{
    Console.WriteLine();
    Console.WriteLine("---------- ADD PRODUCT ----------");

    Console.Write("Enter Product ID: ");

    if (!int.TryParse(Console.ReadLine(), out int id) || id <= 0)
    {
        Console.WriteLine("Invalid Product ID.");
        return;
    }

    try
    {
        var existingProduct =
            await storeService.GetProductAsync(id);

        if (existingProduct != null)
        {
            Console.WriteLine(
                "A product with this ID already exists.");
            return;
        }

        Console.Write("Enter Product Name: ");
        string? name = Console.ReadLine();

        if (string.IsNullOrWhiteSpace(name))
        {
            Console.WriteLine(
                "Product name cannot be empty.");
            return;
        }

        Console.Write("Enter Price: ");

        if (!decimal.TryParse(
                Console.ReadLine(),
                out decimal price) ||
            price <= 0)
        {
            Console.WriteLine("Invalid price.");
            return;
        }

        Console.Write("Enter Stock Quantity: ");

        if (!int.TryParse(
                Console.ReadLine(),
                out int stock) ||
            stock < 0)
        {
            Console.WriteLine("Invalid stock quantity.");
            return;
        }

        Product product = new()
        {
            Id = id,
            Name = name.Trim(),
            Price = price,
            StockQuantity = stock
        };

        await storeService.AddProductAsync(product);

        Console.WriteLine(
            "Product added successfully.");
    }
    catch (Exception ex)
    {
        Console.WriteLine($"Error: {ex.Message}");
    }
}

async Task ViewProducts()
{
    Console.WriteLine();
    Console.WriteLine("---------- ALL PRODUCTS ----------");

    try
    {
        var products =
            await storeService.GetProductsAsync();

        if (products.Count == 0)
        {
            Console.WriteLine("No products found.");
            return;
        }

        foreach (var product in products)
        {
            Console.WriteLine(
                $"ID: {product.Id} | " +
                $"Name: {product.Name} | " +
                $"Price: ₹{product.Price:F2} | " +
                $"Stock: {product.StockQuantity}");
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine($"Error: {ex.Message}");
    }
}

async Task FindProduct()
{
    Console.WriteLine();
    Console.WriteLine("---------- FIND PRODUCT ----------");

    Console.Write("Enter Product ID: ");

    if (!int.TryParse(
            Console.ReadLine(),
            out int id))
    {
        Console.WriteLine("Invalid Product ID.");
        return;
    }

    try
    {
        var product =
            await storeService.GetProductAsync(id);

        if (product == null)
        {
            Console.WriteLine("Product not found.");
            return;
        }

        Console.WriteLine();
        Console.WriteLine($"ID: {product.Id}");
        Console.WriteLine($"Name: {product.Name}");
        Console.WriteLine($"Price: ₹{product.Price:F2}");
        Console.WriteLine(
            $"Stock: {product.StockQuantity}");
    }
    catch (Exception ex)
    {
        Console.WriteLine($"Error: {ex.Message}");
    }
}

async Task UpdateProduct()
{
    Console.WriteLine();
    Console.WriteLine("---------- UPDATE PRODUCT ----------");

    Console.Write("Enter Product ID: ");

    if (!int.TryParse(
            Console.ReadLine(),
            out int id))
    {
        Console.WriteLine("Invalid Product ID.");
        return;
    }

    try
    {
        var product =
            await storeService.GetProductAsync(id);

        if (product == null)
        {
            Console.WriteLine("Product not found.");
            return;
        }

        Console.Write(
            $"Enter new name ({product.Name}): ");

        string? name = Console.ReadLine();

        if (!string.IsNullOrWhiteSpace(name))
        {
            product.Name = name.Trim();
        }

        Console.Write(
            $"Enter new price ({product.Price}): ");

        string? priceInput = Console.ReadLine();

        if (!string.IsNullOrWhiteSpace(priceInput))
        {
            if (!decimal.TryParse(
                    priceInput,
                    out decimal price) ||
                price <= 0)
            {
                Console.WriteLine("Invalid price.");
                return;
            }

            product.Price = price;
        }

        Console.Write(
            $"Enter new stock ({product.StockQuantity}): ");

        string? stockInput = Console.ReadLine();

        if (!string.IsNullOrWhiteSpace(stockInput))
        {
            if (!int.TryParse(
                    stockInput,
                    out int stock) ||
                stock < 0)
            {
                Console.WriteLine("Invalid stock.");
                return;
            }

            product.StockQuantity = stock;
        }

        await storeService.UpdateProductAsync(product);

        Console.WriteLine(
            "Product updated successfully.");
    }
    catch (Exception ex)
    {
        Console.WriteLine($"Error: {ex.Message}");
    }
}

async Task DeleteProduct()
{
    Console.WriteLine();
    Console.WriteLine("---------- DELETE PRODUCT ----------");

    Console.Write("Enter Product ID: ");

    if (!int.TryParse(
            Console.ReadLine(),
            out int id))
    {
        Console.WriteLine("Invalid Product ID.");
        return;
    }

    try
    {
        var product =
            await storeService.GetProductAsync(id);

        if (product == null)
        {
            Console.WriteLine("Product not found.");
            return;
        }

        Console.Write(
            $"Are you sure you want to delete " +
            $"'{product.Name}'? (Y/N): ");

        string? confirmation = Console.ReadLine();

        if (confirmation?.Trim().ToUpper() != "Y")
        {
            Console.WriteLine("Delete cancelled.");
            return;
        }

        await storeService.DeleteProductAsync(id);

        Console.WriteLine(
            "Product deleted successfully.");
    }
    catch (Exception ex)
    {
        Console.WriteLine($"Error: {ex.Message}");
    }
}

async Task ProductManagementMenu()
{
    while (true)
    {
        Console.WriteLine();
        Console.WriteLine(
            "========== PRODUCT MANAGEMENT ==========");
        Console.WriteLine("1. Add Product");
        Console.WriteLine("2. View All Products");
        Console.WriteLine("3. Find Product");
        Console.WriteLine("4. Update Product");
        Console.WriteLine("5. Delete Product");
        Console.WriteLine("0. Back");
        Console.Write("Enter your choice: ");

        string? input = Console.ReadLine();

        if (!int.TryParse(
                input,
                out int choice))
        {
            Console.WriteLine(
                "Invalid input. Please enter a number.");
            continue;
        }

        switch (choice)
        {
            case 1:
                await AddProduct();
                break;

            case 2:
                await ViewProducts();
                break;

            case 3:
                await FindProduct();
                break;

            case 4:
                await UpdateProduct();
                break;

            case 5:
                await DeleteProduct();
                break;

            case 0:
                return;

            default:
                Console.WriteLine("Invalid choice.");
                break;
        }
    }
}

// ============================================================
// CUSTOMER MANAGEMENT
// ============================================================

async Task AddCustomer()
{
    Console.WriteLine();
    Console.WriteLine("---------- ADD CUSTOMER ----------");

    Console.Write("Enter Customer ID: ");

    if (!int.TryParse(
            Console.ReadLine(),
            out int id) ||
        id <= 0)
    {
        Console.WriteLine("Invalid Customer ID.");
        return;
    }

    try
    {
        var existingCustomer =
            await storeService.GetCustomerAsync(id);

        if (existingCustomer != null)
        {
            Console.WriteLine(
                "A customer with this ID already exists.");
            return;
        }

        Console.Write("Enter Customer Name: ");

        string? name = Console.ReadLine();

        if (string.IsNullOrWhiteSpace(name))
        {
            Console.WriteLine(
                "Customer name cannot be empty.");
            return;
        }

        Console.Write("Enter Email: ");

        string? email = Console.ReadLine();

        if (string.IsNullOrWhiteSpace(email))
        {
            Console.WriteLine("Email cannot be empty.");
            return;
        }

        Console.Write("Enter Phone: ");

        string? phone = Console.ReadLine();

        if (string.IsNullOrWhiteSpace(phone))
        {
            Console.WriteLine("Phone cannot be empty.");
            return;
        }

        Customer customer = new()
        {
            Id = id,
            Name = name.Trim(),
            Email = email.Trim(),
            Phone = phone.Trim()
        };

        await storeService.AddCustomerAsync(customer);

        Console.WriteLine(
            "Customer added successfully.");
    }
    catch (Exception ex)
    {
        Console.WriteLine($"Error: {ex.Message}");
    }
}

async Task ViewCustomers()
{
    Console.WriteLine();
    Console.WriteLine(
        "---------- ALL CUSTOMERS ----------");

    try
    {
        var customers =
            await storeService.GetCustomersAsync();

        if (customers.Count == 0)
        {
            Console.WriteLine("No customers found.");
            return;
        }

        foreach (var customer in customers)
        {
            Console.WriteLine(
                $"ID: {customer.Id} | " +
                $"Name: {customer.Name} | " +
                $"Email: {customer.Email} | " +
                $"Phone: {customer.Phone}");
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine($"Error: {ex.Message}");
    }
}

async Task FindCustomer()
{
    Console.WriteLine();
    Console.WriteLine(
        "---------- FIND CUSTOMER ----------");

    Console.Write("Enter Customer ID: ");

    if (!int.TryParse(
            Console.ReadLine(),
            out int id))
    {
        Console.WriteLine("Invalid Customer ID.");
        return;
    }

    try
    {
        var customer =
            await storeService.GetCustomerAsync(id);

        if (customer == null)
        {
            Console.WriteLine("Customer not found.");
            return;
        }

        Console.WriteLine();
        Console.WriteLine($"ID: {customer.Id}");
        Console.WriteLine($"Name: {customer.Name}");
        Console.WriteLine($"Email: {customer.Email}");
        Console.WriteLine($"Phone: {customer.Phone}");
    }
    catch (Exception ex)
    {
        Console.WriteLine($"Error: {ex.Message}");
    }
}

async Task UpdateCustomer()
{
    Console.WriteLine();
    Console.WriteLine(
        "---------- UPDATE CUSTOMER ----------");

    Console.Write("Enter Customer ID: ");

    if (!int.TryParse(
            Console.ReadLine(),
            out int id))
    {
        Console.WriteLine("Invalid Customer ID.");
        return;
    }

    try
    {
        var customer =
            await storeService.GetCustomerAsync(id);

        if (customer == null)
        {
            Console.WriteLine("Customer not found.");
            return;
        }

        Console.Write(
            $"Enter new name ({customer.Name}): ");

        string? name = Console.ReadLine();

        if (!string.IsNullOrWhiteSpace(name))
        {
            customer.Name = name.Trim();
        }

        Console.Write(
            $"Enter new email ({customer.Email}): ");

        string? email = Console.ReadLine();

        if (!string.IsNullOrWhiteSpace(email))
        {
            customer.Email = email.Trim();
        }

        Console.Write(
            $"Enter new phone ({customer.Phone}): ");

        string? phone = Console.ReadLine();

        if (!string.IsNullOrWhiteSpace(phone))
        {
            customer.Phone = phone.Trim();
        }

        await storeService.UpdateCustomerAsync(customer);

        Console.WriteLine(
            "Customer updated successfully.");
    }
    catch (Exception ex)
    {
        Console.WriteLine($"Error: {ex.Message}");
    }
}

async Task DeleteCustomer()
{
    Console.WriteLine();
    Console.WriteLine(
        "---------- DELETE CUSTOMER ----------");

    Console.Write("Enter Customer ID: ");

    if (!int.TryParse(
            Console.ReadLine(),
            out int id))
    {
        Console.WriteLine("Invalid Customer ID.");
        return;
    }

    try
    {
        var customer =
            await storeService.GetCustomerAsync(id);

        if (customer == null)
        {
            Console.WriteLine("Customer not found.");
            return;
        }

        Console.Write(
            $"Are you sure you want to delete " +
            $"'{customer.Name}'? (Y/N): ");

        string? confirmation = Console.ReadLine();

        if (confirmation?.Trim().ToUpper() != "Y")
        {
            Console.WriteLine("Delete cancelled.");
            return;
        }

        await storeService.DeleteCustomerAsync(id);

        Console.WriteLine(
            "Customer deleted successfully.");
    }
    catch (Exception ex)
    {
        Console.WriteLine($"Error: {ex.Message}");
    }
}

async Task CustomerManagementMenu()
{
    while (true)
    {
        Console.WriteLine();
        Console.WriteLine(
            "========== CUSTOMER MANAGEMENT ==========");
        Console.WriteLine("1. Add Customer");
        Console.WriteLine("2. View All Customers");
        Console.WriteLine("3. Find Customer");
        Console.WriteLine("4. Update Customer");
        Console.WriteLine("5. Delete Customer");
        Console.WriteLine("0. Back");
        Console.Write("Enter your choice: ");

        string? input = Console.ReadLine();

        if (!int.TryParse(
                input,
                out int choice))
        {
            Console.WriteLine(
                "Invalid input. Please enter a number.");
            continue;
        }

        switch (choice)
        {
            case 1:
                await AddCustomer();
                break;

            case 2:
                await ViewCustomers();
                break;

            case 3:
                await FindCustomer();
                break;

            case 4:
                await UpdateCustomer();
                break;

            case 5:
                await DeleteCustomer();
                break;

            case 0:
                return;

            default:
                Console.WriteLine("Invalid choice.");
                break;
        }
    }
}

// ============================================================
// ORDER / CHECKOUT
// ============================================================

async Task OrderManagementMenu()
{
    Console.WriteLine();
    Console.WriteLine(
        "========== CREATE ORDER / CHECKOUT ==========");

    try
    {
        var customers =
            await storeService.GetCustomersAsync();

        if (customers.Count == 0)
        {
            Console.WriteLine(
                "No customers available.");
            Console.WriteLine(
                "Please add a customer first.");
            return;
        }

        var products =
            await storeService.GetProductsAsync();

        if (products.Count == 0)
        {
            Console.WriteLine(
                "No products available.");
            Console.WriteLine(
                "Please add products first.");
            return;
        }

        Console.WriteLine();
        Console.WriteLine("Available Customers:");

        foreach (var customer in customers)
        {
            Console.WriteLine(
                $"ID: {customer.Id} | " +
                $"Name: {customer.Name}");
        }

        Console.WriteLine();
        Console.Write("Enter Customer ID: ");

        if (!int.TryParse(
                Console.ReadLine(),
                out int customerId))
        {
            Console.WriteLine(
                "Invalid Customer ID.");
            return;
        }

        var selectedCustomer =
            await storeService.GetCustomerAsync(
                customerId);

        if (selectedCustomer == null)
        {
            Console.WriteLine(
                "Customer not found.");
            return;
        }

        List<OrderItem> orderItems = new();

        while (true)
        {
            // Refresh products so the displayed stock is current.
            products =
                await storeService.GetProductsAsync();

            Console.WriteLine();
            Console.WriteLine("Available Products:");

            foreach (var product in products)
            {
                Console.WriteLine(
                    $"ID: {product.Id} | " +
                    $"Name: {product.Name} | " +
                    $"Price: ₹{product.Price:F2} | " +
                    $"Stock: {product.StockQuantity}");
            }

            Console.WriteLine();
            Console.Write("Enter Product ID: ");

            if (!int.TryParse(
                    Console.ReadLine(),
                    out int productId))
            {
                Console.WriteLine(
                    "Invalid Product ID.");
                continue;
            }

            var selectedProduct =
                await storeService.GetProductAsync(
                    productId);

            if (selectedProduct == null)
            {
                Console.WriteLine(
                    "Product not found.");
                continue;
            }

            Console.Write("Enter Quantity: ");

            if (!int.TryParse(
                    Console.ReadLine(),
                    out int quantity))
            {
                Console.WriteLine(
                    "Invalid quantity.");
                continue;
            }

            if (quantity <= 0)
            {
                Console.WriteLine(
                    "Quantity must be greater than zero.");
                continue;
            }

            // If the same product is selected twice,
            // combine the quantities.
            var existingItem =
                orderItems.FirstOrDefault(
                    item => item.ProductId == productId);

            if (existingItem != null)
            {
                existingItem.Quantity += quantity;
            }
            else
            {
                orderItems.Add(new OrderItem
                {
                    ProductId = productId,
                    Quantity = quantity,
                    UnitPrice = selectedProduct.Price
                });
            }

            Console.Write(
                "Add another product? (Y/N): ");

            string? answer = Console.ReadLine();

            if (answer?.Trim().ToUpper() != "Y")
            {
                break;
            }
        }

        if (orderItems.Count == 0)
        {
            Console.WriteLine(
                "No products were added to the order.");
            return;
        }

        Order order =
            await storeService.CheckoutAsync(
                customerId,
                orderItems);

        decimal subtotal =
            order.TotalAmount + order.Discount;

        Console.WriteLine();
        Console.WriteLine(
            "========== ORDER COMPLETED ==========");

        Console.WriteLine(
            $"Order ID: {order.Id}");

        Console.WriteLine(
            $"Customer: {selectedCustomer.Name}");

        Console.WriteLine(
            $"Order Date: {order.OrderDate:yyyy-MM-dd HH:mm:ss}");

        Console.WriteLine(
            $"Subtotal: ₹{subtotal:F2}");

        Console.WriteLine(
            $"Discount: ₹{order.Discount:F2}");

        Console.WriteLine(
            $"Final Total: ₹{order.TotalAmount:F2}");

        Console.WriteLine(
            $"Status: {order.Status}");
    }
    catch (OutOfStockException ex)
    {
        Console.WriteLine();
        Console.WriteLine(
            "========== CHECKOUT FAILED ==========");

        Console.WriteLine(
            $"OUT OF STOCK: {ex.Message}");
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine(
            "========== CHECKOUT FAILED ==========");

        Console.WriteLine(
            $"ERROR: {ex.Message}");
    }
}

// ============================================================
// REPORTS
// ============================================================

async Task ShowTopSellingProducts()
{
    Console.WriteLine();
    Console.WriteLine(
        "========== TOP-SELLING PRODUCTS ==========");

    try
    {
        var orders =
            await orderRepository.GetAllAsync();

        if (orders.Count == 0)
        {
            Console.WriteLine(
                "No completed orders available.");
            return;
        }

        var results =
            reportService.GetTopSellingProducts(
                orders);

        foreach (var item in results)
        {
            var product =
                await storeService.GetProductAsync(
                    item.ProductId);

            string name =
                product?.Name ??
                $"Product ID {item.ProductId}";

            Console.WriteLine(
                $"{name} → {item.QuantitySold} units sold");
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine($"Error: {ex.Message}");
    }
}

async Task ShowRevenuePerCustomer()
{
    Console.WriteLine();
    Console.WriteLine(
        "========== REVENUE PER CUSTOMER ==========");

    try
    {
        var orders =
            await orderRepository.GetAllAsync();

        if (orders.Count == 0)
        {
            Console.WriteLine(
                "No completed orders available.");
            return;
        }

        var results =
            reportService.GetRevenuePerCustomer(
                orders);

        foreach (var item in results)
        {
            var customer =
                await storeService.GetCustomerAsync(
                    item.CustomerId);

            string name =
                customer?.Name ??
                $"Customer ID {item.CustomerId}";

            Console.WriteLine(
                $"{name} → ₹{item.Revenue:F2}");
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine($"Error: {ex.Message}");
    }
}

async Task ShowLowStockProducts()
{
    Console.WriteLine();
    Console.WriteLine(
        "========== LOW-STOCK ALERTS ==========");

    try
    {
        var products =
            await storeService.GetProductsAsync();

        var results =
            reportService.GetLowStockProducts(
                products);

        if (results.Count == 0)
        {
            Console.WriteLine(
                "No low-stock products.");
            return;
        }

        foreach (var product in results)
        {
            Console.WriteLine(
                $"{product.Name} → " +
                $"Stock: {product.StockQuantity}");
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine($"Error: {ex.Message}");
    }
}

async Task ShowOrdersByDateRange()
{
    Console.WriteLine();
    Console.WriteLine(
        "========== ORDERS BY DATE RANGE ==========");

    Console.Write(
        "Enter start date (yyyy-MM-dd): ");

    if (!DateTime.TryParse(
            Console.ReadLine(),
            out DateTime from))
    {
        Console.WriteLine(
            "Invalid start date.");
        return;
    }

    Console.Write(
        "Enter end date (yyyy-MM-dd): ");

    if (!DateTime.TryParse(
            Console.ReadLine(),
            out DateTime to))
    {
        Console.WriteLine(
            "Invalid end date.");
        return;
    }

    from = from.Date;

    to = to.Date
        .AddDays(1)
        .AddTicks(-1);

    if (from > to)
    {
        Console.WriteLine(
            "Start date cannot be after end date.");
        return;
    }

    try
    {
        var orders =
            await orderRepository.GetAllAsync();

        var results =
            reportService.GetOrdersByDateRange(
                orders,
                from,
                to);

        if (results.Count == 0)
        {
            Console.WriteLine(
                "No orders found in this date range.");
            return;
        }

        foreach (var order in results)
        {
            var customer =
                await storeService.GetCustomerAsync(
                    order.CustomerId);

            string customerName =
                customer?.Name ??
                $"Customer ID {order.CustomerId}";

            Console.WriteLine(
                $"Order #{order.Id} | " +
                $"Customer: {customerName} | " +
                $"Date: {order.OrderDate:yyyy-MM-dd HH:mm} | " +
                $"Total: ₹{order.TotalAmount:F2} | " +
                $"Status: {order.Status}");
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine($"Error: {ex.Message}");
    }
}

async Task ReportsMenu()
{
    while (true)
    {
        Console.WriteLine();
        Console.WriteLine(
            "========== REPORTS ==========");
        Console.WriteLine(
            "1. Top-Selling Products");
        Console.WriteLine(
            "2. Revenue Per Customer");
        Console.WriteLine(
            "3. Low-Stock Alerts");
        Console.WriteLine(
            "4. Orders By Date Range");
        Console.WriteLine(
            "0. Back");

        Console.Write(
            "Enter your choice: ");

        string? input = Console.ReadLine();

        if (!int.TryParse(
                input,
                out int choice))
        {
            Console.WriteLine(
                "Invalid input. Please enter a number.");
            continue;
        }

        switch (choice)
        {
            case 1:
                await ShowTopSellingProducts();
                break;

            case 2:
                await ShowRevenuePerCustomer();
                break;

            case 3:
                await ShowLowStockProducts();
                break;

            case 4:
                await ShowOrdersByDateRange();
                break;

            case 0:
                return;

            default:
                Console.WriteLine(
                    "Invalid choice.");
                break;
        }
    }
}

// ============================================================
// MAIN MENU
// ============================================================

Console.WriteLine();
Console.WriteLine(
    "==============================================");
Console.WriteLine(
    "      STORE INVENTORY & ORDER CONSOLE");
Console.WriteLine(
    "==============================================");

while (true)
{
    Console.WriteLine();
    Console.WriteLine(
        "============== MAIN MENU ==============");
    Console.WriteLine(
        "1. Product Management");
    Console.WriteLine(
        "2. Customer Management");
    Console.WriteLine(
        "3. Create Order / Checkout");
    Console.WriteLine(
        "4. Reports");
    Console.WriteLine(
        "0. Exit");

    Console.Write(
        "Enter your choice: ");

    string? input = Console.ReadLine();

    if (!int.TryParse(
            input,
            out int choice))
    {
        Console.WriteLine(
            "Invalid input. Please enter a number.");
        continue;
    }

    switch (choice)
    {
        case 1:
            await ProductManagementMenu();
            break;

        case 2:
            await CustomerManagementMenu();
            break;

        case 3:
            await OrderManagementMenu();
            break;

        case 4:
            await ReportsMenu();
            break;

        case 0:
            Console.WriteLine(
                "Thank you for using Store Inventory & Order Console.");
            return;

        default:
            Console.WriteLine(
                "Invalid choice.");
            break;
    }
}
Your final file structure

Make sure your Solution Explorer looks like this:

StoreInventorySolution
│
└── StoreInventory
    │
    ├── Dependencies
    │
    ├── Interfaces
    │   ├── IEntity.cs
    │   └── IRepository.cs
    │
    ├── Models
    │   ├── Product.cs
    │   ├── Customer.cs
    │   ├── OrderItem.cs
    │   ├── Order.cs
    │   └── OrderStatus.cs
    │
    ├── Repositories
    │   └── InMemoryRepository.cs
    │
    ├── Exceptions
    │   └── OutOfStockException.cs
    │
    ├── Services
    │   ├── StoreService.cs
    │   └── ReportService.cs
    │
    └── Program.cs
Important: do this before running

After replacing the files:

Press Ctrl + Shift + B
Wait for Build succeeded
If there are errors, don't run the program yet.
Send me a screenshot of the Error List.

If it builds successfully, run with Ctrl + F5.

Then we'll do a proper end-to-end test using sample data, including:

Product CRUD
Customer CRUD
₹5,000 discount condition
Insufficient stock exception
Inventory reduction
Top-selling report
Revenue report
Low-stock report
Date-range report

That will give you a reliable final project rather than just code that compiles.
