namespace StoreInventory.API.Models.DTO
{
    public class TopSellingProductDto
    {
        public int ProductId { get; set; }

        public string ProductName { get; set; } = "";

        public int TotalQuantitySold { get; set; }
    }
}



namespace StoreInventory.API.Models.DTO
{
    public class CustomerRevenueDto
    {
        public int CustomerId { get; set; }

        public string CustomerName { get; set; } = "";

        public decimal TotalRevenue { get; set; }
    }
}


public async Task<List<TopSellingProductDto>>
    GetTopSellingProductsAsync()
{
    return await _context.OrderItems
        .Join(
            _context.Products,
            item => item.ProductId,
            product => product.Id,
            (item, product) => new
            {
                ProductId = product.Id,
                ProductName = product.Name,
                Quantity = item.Quantity
            })
        .GroupBy(x => new
        {
            x.ProductId,
            x.ProductName
        })
        .Select(group => new TopSellingProductDto
        {
            ProductId = group.Key.ProductId,
            ProductName = group.Key.ProductName,
            TotalQuantitySold =
                group.Sum(x => x.Quantity)
        })
        .OrderByDescending(
            result => result.TotalQuantitySold)
        .ToListAsync();
}



public async Task<List<CustomerRevenueDto>>
    GetRevenueByCustomerAsync()
{
    return await _context.Orders
        .Join(
            _context.Customers,
            order => order.CustomerId,
            customer => customer.Id,
            (order, customer) => new
            {
                CustomerId = customer.Id,
                CustomerName = customer.Name,
                Revenue = order.TotalAmount
            })
        .GroupBy(x => new
        {
            x.CustomerId,
            x.CustomerName
        })
        .Select(group => new CustomerRevenueDto
        {
            CustomerId = group.Key.CustomerId,
            CustomerName = group.Key.CustomerName,
            TotalRevenue =
                group.Sum(x => x.Revenue)
        })
        .OrderByDescending(
            result => result.TotalRevenue)
        .ToListAsync();
}


