namespace StoreInventory.API.Models.DTO
{
    public class LowStockProductDto
    {
        public int Id { get; set; }

        public string Name { get; set; } = "";

        public decimal Price { get; set; }

        public int StockQuantity { get; set; }
    }
}



namespace StoreInventory.API.Models.DTO
{
    public class OrderReportDto
    {
        public int Id { get; set; }

        public int CustomerId { get; set; }

        public List<OrderItemDto> Items { get; set; } = new();

        public DateTime OrderDate { get; set; }

        public decimal TotalAmount { get; set; }

        public decimal Discount { get; set; }

        public string Status { get; set; } = "";
    }
}



using StoreInventory.API.Models.DTO;

namespace StoreInventory.API.Services.Interfaces
{
    public interface IReportService
    {
        Task<List<TopSellingProductDto>>
            GetTopSellingProductsAsync();

        Task<List<CustomerRevenueDto>>
            GetRevenueByCustomerAsync();

        Task<List<LowStockProductDto>>
            GetLowStockProductsAsync();

        Task<List<OrderReportDto>>
            GetOrdersByDateRangeAsync(
                DateTime from,
                DateTime to);
    }
}



using Microsoft.EntityFrameworkCore;
using StoreInventory.API.Data;
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
                    TotalQuantitySold =
                        group.Sum(item => item.Quantity)
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
                    TotalRevenue =
                        group.Sum(order => order.TotalAmount)
                })
                .OrderByDescending(
                    result => result.TotalRevenue)
                .ToListAsync();
        }

        public async Task<List<LowStockProductDto>>
            GetLowStockProductsAsync()
        {
            return await _context.Products
                .Where(product => product.StockQuantity < 5)
                .OrderBy(product => product.StockQuantity)
                .Select(product => new LowStockProductDto
                {
                    Id = product.Id,
                    Name = product.Name,
                    Price = product.Price,
                    StockQuantity = product.StockQuantity
                })
                .ToListAsync();
        }

        public async Task<List<OrderReportDto>>
            GetOrdersByDateRangeAsync(
                DateTime from,
                DateTime to)
        {
            var orders = await _context.Orders
                .Include(order => order.Items)
                .Where(order =>
                    order.OrderDate >= from &&
                    order.OrderDate <= to)
                .OrderBy(order => order.OrderDate)
                .ToListAsync();

            return orders.Select(order => new OrderReportDto
            {
                Id = order.Id,
                CustomerId = order.CustomerId,
                Items = order.Items.Select(item => new OrderItemDto
                {
                    Id = item.Id,
                    ProductId = item.ProductId,
                    Quantity = item.Quantity,
                    UnitPrice = item.UnitPrice
                }).ToList(),
                OrderDate = order.OrderDate,
                TotalAmount = order.TotalAmount,
                Discount = order.Discount,
                Status = order.Status.ToString()
            }).ToList();
        }
    }
}



