using System.ComponentModel.DataAnnotations;

namespace StoreInventory.API.Models.DTO
{
    public class CreateProductRequestDto
    {
        [Required]
        [StringLength(100, MinimumLength = 2)]
        public string Name { get; set; } = "";

        [Range(0.01, 999999999.99)]
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

        [Range(0.01, 999999999.99)]
        public decimal Price { get; set; }

        [Range(0, int.MaxValue)]
        public int StockQuantity { get; set; }
    }
}