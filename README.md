using Microsoft.AspNetCore.Identity;
using Microsoft.EntityFrameworkCore;
using StoreInventory.API.Models.Domain;

namespace StoreInventory.API.Data
{
    public static class DbInitializer
    {
        public static async Task SeedAdminAsync(
            StoreInventoryDbContext context,
            IConfiguration configuration)
        {
            var adminUsername =
                configuration["AdminUser:Username"];

            var adminEmail =
                configuration["AdminUser:Email"];

            var adminPassword =
                configuration["AdminUser:Password"];

            if (string.IsNullOrWhiteSpace(adminUsername) ||
                string.IsNullOrWhiteSpace(adminEmail) ||
                string.IsNullOrWhiteSpace(adminPassword))
            {
                throw new InvalidOperationException(
                    "Admin user configuration is missing.");
            }

            var adminExists = await context.Users
                .AnyAsync(user => user.Role == "Admin");

            if (adminExists)
            {
                return;
            }

            var admin = new ApplicationUser
            {
                Username = adminUsername,
                Email = adminEmail,
                Role = "Admin"
            };

            var passwordHasher =
                new PasswordHasher<ApplicationUser>();

            admin.PasswordHash =
                passwordHasher.HashPassword(
                    admin,
                    adminPassword);

            await context.Users.AddAsync(admin);

            await context.SaveChangesAsync();
        }
    }
}


dotnet user-secrets init


dotnet user-secrets set "AdminUser:Username" "admin"
dotnet user-secrets set "AdminUser:Email" "admin@store.com"
dotnet user-secrets set "AdminUser:Password" "Admin@123"


using (var scope = app.Services.CreateScope())
{
    var services = scope.ServiceProvider;

    var context =
        services.GetRequiredService<StoreInventoryDbContext>();

    await DbInitializer.SeedAdminAsync(
        context,
        builder.Configuration);
}


{
  "username": "employee3",
  "email": "employee3@store.com",
  "password": "Employee@123"
}


