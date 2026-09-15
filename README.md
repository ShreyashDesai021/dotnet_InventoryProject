using System.ComponentModel.DataAnnotations;

namespace StoreInventory.API.Models.DTO
{
    public class CreateProductRequestDto
    {
        [Required]
        [StringLength(100, MinimumLength = 2)]
        public string Name { get; set; } = "";

        [Range(0.01, double.MaxValue)]
        public decimal Price { get; set; }

        [Range(0, int.MaxValue)]
        public int StockQuantity { get; set; }
    }
}


using System.ComponentModel.DataAnnotations;

namespace StoreInventory.API.Models.DTO
{
    public class UpdateProductRequestDto
    {
        [Required]
        [StringLength(100, MinimumLength = 2)]
        public string Name { get; set; } = "";

        [Range(0.01, double.MaxValue)]
        public decimal Price { get; set; }

        [Range(0, int.MaxValue)]
        public int StockQuantity { get; set; }
    }
}


using System.ComponentModel.DataAnnotations;

namespace StoreInventory.API.Models.DTO
{
    public class CreateCustomerRequestDto
    {
        [Required]
        [StringLength(100, MinimumLength = 2)]
        public string Name { get; set; } = "";

        [Required]
        [EmailAddress]
        public string Email { get; set; } = "";

        [Required]
        [Phone]
        public string Phone { get; set; } = "";
    }
}



using System.ComponentModel.DataAnnotations;

namespace StoreInventory.API.Models.DTO
{
    public class UpdateCustomerRequestDto
    {
        [Required]
        [StringLength(100, MinimumLength = 2)]
        public string Name { get; set; } = "";

        [Required]
        [EmailAddress]
        public string Email { get; set; } = "";

        [Required]
        [Phone]
        public string Phone { get; set; } = "";
    }
}



using System.ComponentModel.DataAnnotations;

namespace StoreInventory.API.Models.DTO
{
    public class OrderItemRequestDto
    {
        [Range(1, int.MaxValue)]
        public int ProductId { get; set; }

        [Range(1, int.MaxValue)]
        public int Quantity { get; set; }
    }
}


using System.ComponentModel.DataAnnotations;

namespace StoreInventory.API.Models.DTO
{
    public class CreateOrderRequestDto
    {
        [Range(1, int.MaxValue)]
        public int CustomerId { get; set; }

        [Required]
        [MinLength(1)]
        public List<OrderItemRequestDto> Items { get; set; } = new();

        [Range(0, double.MaxValue)]
        public decimal DiscountAmount { get; set; }
    }
}



{
  "name": "",
  "price": -100,
  "stockQuantity": -5
}




{
  "name": "",
  "email": "not-an-email",
  "phone": ""
}



{
  "customerId": 0,
  "items": [
    {
      "productId": 1,
      "quantity": 0
    }
  ],
  "discountAmount": -100
}


