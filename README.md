using StoreInventory.API.Models.Domain;

namespace StoreInventory.API.Services.Interfaces
{
    public interface IOrderService
    {
        Task<List<Order>> GetAllAsync();

        Task<Order?> GetByIdAsync(int id);

        Task<Order> CheckoutAsync(
            int customerId,
            List<OrderItem> items,
            decimal discountAmount);
    }
}


public async Task<Order?> UpdateAsync(int id, Order order)
{
    return await _orderRepository.UpdateAsync(id, order);
}

public async Task<Order?> DeleteAsync(int id)
{
    return await _orderRepository.DeleteAsync(id);
}


using StoreInventory.API.Models.Domain;

namespace StoreInventory.API.Repositories.Interfaces
{
    public interface IOrderRepository
    {
        Task<List<Order>> GetAllAsync();

        Task<Order?> GetByIdAsync(int id);

        Task<Order> CreateAsync(Order order);
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
    }
}



using AutoMapper;
using Microsoft.AspNetCore.Mvc;
using StoreInventory.API.Models.DTO;
using StoreInventory.API.Services.Interfaces;

namespace StoreInventory.API.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class OrdersController : ControllerBase
    {
        private readonly IOrderService _orderService;
        private readonly IMapper _mapper;

        public OrdersController(
            IOrderService orderService,
            IMapper mapper)
        {
            _orderService = orderService;
            _mapper = mapper;
        }

        // GET: api/Orders
        [HttpGet]
        public async Task<IActionResult> GetAll()
        {
            var orders = await _orderService.GetAllAsync();

            var orderDtos =
                _mapper.Map<List<OrderDto>>(orders);

            return Ok(orderDtos);
        }

        // GET: api/Orders/1
        [HttpGet("{id:int}")]
        public async Task<IActionResult> GetById(int id)
        {
            var order = await _orderService.GetByIdAsync(id);

            if (order == null)
                return NotFound();

            var orderDto =
                _mapper.Map<OrderDto>(order);

            return Ok(orderDto);
        }

        // POST: api/Orders/checkout
        [HttpPost("checkout")]
        public async Task<IActionResult> Checkout(
            CreateOrderRequestDto request)
        {
            var items =
                _mapper.Map<List<Models.Domain.OrderItem>>(
                    request.Items);

            var order =
                await _orderService.CheckoutAsync(
                    request.CustomerId,
                    items,
                    request.DiscountAmount);

            var orderDto =
                _mapper.Map<OrderDto>(order);

            return CreatedAtAction(
                nameof(GetById),
                new { id = order.Id },
                orderDto);
        }
    }
}


