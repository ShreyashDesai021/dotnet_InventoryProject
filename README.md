using Microsoft.EntityFrameworkCore;
using StoreInventory.API.Data;
using StoreInventory.API.Models.Domain;
using StoreInventory.API.Models.DTO;
using StoreInventory.API.Services.Interfaces;

namespace StoreInventory.API.Services
{
    public class ReportService : IReportService
    {
        private readonly StoreInventoryDbContext _context;

        public ReportService(StoreInventoryDbContext context)
        {
            _context = context;
        }

        public async Task<List<TopSellingProductDto>>
            GetTopSellingProductsAsync()
        {
            return await _context.OrderItems
                .GroupBy(item => item.ProductId)
                .Select(group => new TopSellingProductDto
                {
                    ProductId = group.Key,
                    TotalQuantitySold = group.Sum(item => item.Quantity)
                })
                .OrderByDescending(
                    result => result.TotalQuantitySold)
                .ToListAsync();
        }

        public async Task<List<CustomerRevenueDto>>
            GetRevenueByCustomerAsync()
        {
            return await _context.Orders
                .GroupBy(order => order.CustomerId)
                .Select(group => new CustomerRevenueDto
                {
                    CustomerId = group.Key,
                    TotalRevenue = group.Sum(
                        order => order.TotalAmount)
                })
                .OrderByDescending(
                    result => result.TotalRevenue)
                .ToListAsync();
        }

        public async Task<List<Product>>
            GetLowStockProductsAsync()
        {
            return await _context.Products
                .Where(product => product.StockQuantity < 5)
                .OrderBy(product => product.StockQuantity)
                .ToListAsync();
        }

        public async Task<List<Order>>
            GetOrdersByDateRangeAsync(
                DateTime from,
                DateTime to)
        {
            return await _context.Orders
                .Include(order => order.Items)
                .Where(order =>
                    order.OrderDate >= from &&
                    order.OrderDate <= to)
                .OrderBy(order => order.OrderDate)
                .ToListAsync();
        }
    }
}