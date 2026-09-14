using StoreInventory.API.Models.Domain;
using StoreInventory.API.Models.DTO;

namespace StoreInventory.API.Services.Interfaces
{
    public interface IReportService
    {
        Task<List<TopSellingProductDto>> GetTopSellingProductsAsync();

        Task<List<CustomerRevenueDto>> GetRevenueByCustomerAsync();

        Task<List<Product>> GetLowStockProductsAsync();

        Task<List<Order>> GetOrdersByDateRangeAsync(
            DateTime from,
            DateTime to);
    }
}