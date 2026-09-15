Install-Package Microsoft.AspNetCore.Authentication.JwtBearer

namespace StoreInventory.API.Models.Domain
{
    public class ApplicationUser
    {
        public int Id { get; set; }

        public string Username { get; set; } = "";

        public string Email { get; set; } = "";

        public string PasswordHash { get; set; } = "";

        public string Role { get; set; } = "Employee";
    }
}


public DbSet<ApplicationUser> Users { get; set; }


Add-Migration AddApplicationUsers


Update-Database
