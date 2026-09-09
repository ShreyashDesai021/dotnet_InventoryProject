using Microsoft.EntityFrameworkCore;
using StoreInventory.API.Models.Domain;

namespace StoreInventory.API.Data
{
    public class StoreInventoryDbContext : DbContext
    {
        public StoreInventoryDbContext(
            DbContextOptions<StoreInventoryDbContext> options)
            : base(options)
        {
        }

        public DbSet<Product> Products { get; set; }

        public DbSet<Customer> Customers { get; set; }

        public DbSet<Order> Orders { get; set; }

        public DbSet<OrderItem> OrderItems { get; set; }
    }
}