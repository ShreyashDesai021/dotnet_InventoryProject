using StoreInventory.API.Models.Domain;

namespace StoreInventory.API.Repositories.Interfaces
{
    public interface IOrderRepository
    {
        Task<List<Order>> GetAllAsync();

        Task<Order?> GetByIdAsync(int id);

        Task<Order> CreateAsync(Order order);

        Task<Order?> UpdateAsync(int id, Order order);

        Task<Order?> DeleteAsync(int id);
    }
}


using Microsoft.EntityFrameworkCore;
using StoreInventory.API.Data;
using StoreInventory.API.Models.Domain;
using StoreInventory.API.Repositories.Interfaces;

namespace StoreInventory.API.Repositories.SQL
{
    public class OrderRepository : IOrderRepository
    {
        private readonly StoreInventoryDbContext _context;

        public OrderRepository(StoreInventoryDbContext context)
        {
            _context = context;
        }

        public async Task<List<Order>> GetAllAsync()
        {
            return await _context.Orders
                .Include(o => o.Items)
                .ToListAsync();
        }

        public async Task<Order?> GetByIdAsync(int id)
        {
            return await _context.Orders
                .Include(o => o.Items)
                .FirstOrDefaultAsync(o => o.Id == id);
        }

        public async Task<Order> CreateAsync(Order order)
        {
            await _context.Orders.AddAsync(order);

            await _context.SaveChangesAsync();

            return order;
        }

        public async Task<Order?> UpdateAsync(int id, Order order)
        {
            var existingOrder = await _context.Orders
                .Include(o => o.Items)
                .FirstOrDefaultAsync(o => o.Id == id);

            if (existingOrder == null)
                return null;

            existingOrder.CustomerId = order.CustomerId;
            existingOrder.OrderDate = order.OrderDate;
            existingOrder.TotalAmount = order.TotalAmount;
            existingOrder.Discount = order.Discount;
            existingOrder.Status = order.Status;

            await _context.SaveChangesAsync();

            return existingOrder;
        }

        public async Task<Order?> DeleteAsync(int id)
        {
            var order = await _context.Orders
                .Include(o => o.Items)
                .FirstOrDefaultAsync(o => o.Id == id);

            if (order == null)
                return null;

            _context.Orders.Remove(order);

            await _context.SaveChangesAsync();

            return order;
        }
    }
}


modelBuilder.Entity<Order>()
    .HasMany(o => o.Items)
    .WithOne()
    .HasForeignKey("OrderId")
    .OnDelete(DeleteBehavior.Cascade);


