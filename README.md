using StoreInventory.API.Models.Domain;

namespace StoreInventory.API.Services.Interfaces
{
    public interface IReportService
    {
        Task<List<object>> GetTopSellingProductsAsync();

        Task<List<object>> GetRevenueByCustomerAsync();

        Task<List<Product>> GetLowStockProductsAsync();

        Task<List<Order>> GetOrdersByDateRangeAsync(
            DateTime from,
            DateTime to);
    }
}