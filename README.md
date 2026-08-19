namespace StoreInventory.Models;

public class Product
{
    public int Id { get; set; }

    public string Name { get; set; } = "";

    public decimal Price { get; set; }

    public int StockQuantity { get; set; }
}

namespace StoreInventory.Models;

public class Customer
{
    public int Id { get; set; }

    public string Name { get; set; } = "";

    public string Email { get; set; } = "";

    public string Phone { get; set; } = "";
}

namespace StoreInventory.Models;

public enum OrderStatus
{
    Pending,
    Confirmed,
    Cancelled,
    Completed
}

namespace StoreInventory.Models;

public class OrderItem
{
    public int ProductId { get; set; }

    public int Quantity { get; set; }

    public decimal UnitPrice { get; set; }
}

namespace StoreInventory.Models;

public class Order
{
    public int Id { get; set; }

    public int CustomerId { get; set; }

    public List<OrderItem> Items { get; set; } = new();

    public DateTime OrderDate { get; set; }

    public decimal TotalAmount { get; set; }

    public decimal Discount { get; set; }

    public OrderStatus Status { get; set; }
}










