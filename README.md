using StoreInventory.Models;

namespace StoreInventory.Services;

public class ReportService
{
    public List<(int ProductId, int QuantitySold)> GetTopSellingProducts(
        List<Order> orders)
    {
        return orders
            .SelectMany(order => order.Items)
            .GroupBy(item => item.ProductId)
            .Select(group => (
                ProductId: group.Key,
                QuantitySold: group.Sum(item => item.Quantity)
            ))
            .OrderByDescending(x => x.QuantitySold)
            .ToList();
    }

    public List<(int CustomerId, decimal Revenue)> GetRevenuePerCustomer(
        List<Order> orders)
    {
        return orders
            .GroupBy(order => order.CustomerId)
            .Select(group => (
                CustomerId: group.Key,
                Revenue: group.Sum(order => order.TotalAmount)
            ))
            .OrderByDescending(x => x.Revenue)
            .ToList();
    }

    public List<Product> GetLowStockProducts(
        List<Product> products)
    {
        return products
            .Where(product => product.StockQuantity < 5)
            .ToList();
    }

    public List<Order> GetOrdersByDateRange(
        List<Order> orders,
        DateTime from,
        DateTime to)
    {
        return orders
            .Where(order =>
                order.OrderDate >= from &&
                order.OrderDate <= to)
            .OrderBy(order => order.OrderDate)
            .ToList();
    }
}