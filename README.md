namespace StoreInventory.API.Models.DTO
{
    public class OrderItemRequestDto
    {
        public int ProductId { get; set; }

        public int Quantity { get; set; }
    }
}


namespace StoreInventory.API.Models.DTO
{
    public class CreateOrderRequestDto
    {
        public int CustomerId { get; set; }

        public List<OrderItemRequestDto> Items { get; set; } = new();

        public decimal DiscountAmount { get; set; }
    }
}





using StoreInventory.API.Models.Domain;

namespace StoreInventory.API.Models.DTO
{
    public class OrderDto
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



