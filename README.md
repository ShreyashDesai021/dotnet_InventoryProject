namespace StoreInventory.API.Models.DTO
{
    public class CustomerDto
    {
        public int Id { get; set; }

        public string Name { get; set; } = "";

        public string Email { get; set; } = "";

        public string Phone { get; set; } = "";
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
        public string Phone { get; set; } = "";
    }
}