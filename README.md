namespace StoreInventory.API.Models.Domain
{
    public class OrderItem
    {
        public int Id { get; set; }

        public int ProductId { get; set; }

        public int Quantity { get; set; }

        public decimal UnitPrice { get; set; }
    }
}

namespace StoreInventory.API.Models.Domain
{
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
}