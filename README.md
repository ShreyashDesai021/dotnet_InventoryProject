namespace StoreInventory.API.Models.DTO
{
    public class ProductDto
    {
        public int Id { get; set; }

        public string Name { get; set; } = "";

        public decimal Price { get; set; }

        public int StockQuantity { get; set; }
    }
}

namespace StoreInventory.API.Models.DTO
{
    public class CreateProductRequestDto
    {
        public string Name { get; set; } = "";

        public decimal Price { get; set; }

        public int StockQuantity { get; set; }
    }
}


namespace StoreInventory.API.Models.DTO
{
    public class UpdateProductRequestDto
    {
        public string Name { get; set; } = "";

        public decimal Price { get; set; }

        public int StockQuantity { get; set; }
    }
}
