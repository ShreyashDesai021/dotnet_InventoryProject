using Microsoft.AspNetCore.Mvc;
using StoreInventory.API.Services.Interfaces;

namespace StoreInventory.API.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class ReportsController : ControllerBase
    {
        private readonly IReportService _reportService;

        public ReportsController(IReportService reportService)
        {
            _reportService = reportService;
        }

        // GET: api/Reports/top-selling-products
        [HttpGet("top-selling-products")]
        public async Task<IActionResult> GetTopSellingProducts()
        {
            var result = await _reportService
                .GetTopSellingProductsAsync();

            return Ok(result);
        }

        // GET: api/Reports/revenue-by-customer
        [HttpGet("revenue-by-customer")]
        public async Task<IActionResult> GetRevenueByCustomer()
        {
            var result = await _reportService
                .GetRevenueByCustomerAsync();

            return Ok(result);
        }

        // GET: api/Reports/low-stock-products
        [HttpGet("low-stock-products")]
        public async Task<IActionResult> GetLowStockProducts()
        {
            var result = await _reportService
                .GetLowStockProductsAsync();

            return Ok(result);
        }

        // GET: api/Reports/orders-by-date
        [HttpGet("orders-by-date")]
        public async Task<IActionResult> GetOrdersByDateRange(
            [FromQuery] DateTime from,
            [FromQuery] DateTime to)
        {
            if (from > to)
            {
                return BadRequest(
                    "The 'from' date cannot be later than the 'to' date.");
            }

            var result = await _reportService
                .GetOrdersByDateRangeAsync(from, to);

            return Ok(result);
        }
    }
}