namespace StoreInventory.API.Models.DTO
{
    public class OrderItemDto
    {
        public int Id { get; set; }

        public int ProductId { get; set; }

        public int Quantity { get; set; }

        public decimal UnitPrice { get; set; }
    }
}




using StoreInventory.API.Models.Domain;

namespace StoreInventory.API.Models.DTO
{
    public class OrderDto
    {
        public int Id { get; set; }

        public int CustomerId { get; set; }

        public List<OrderItemDto> Items { get; set; } = new();

        public DateTime OrderDate { get; set; }

        public decimal TotalAmount { get; set; }

        public decimal Discount { get; set; }

        public OrderStatus Status { get; set; }
    }
}




CreateMap<Order, OrderDto>();
CreateMap<OrderItem, OrderItemDto>();
CreateMap<OrderItemRequestDto, OrderItem>();




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

            var orderDtos = _mapper.Map<List<OrderDto>>(orders);

            return Ok(orderDtos);
        }

        // GET: api/Orders/1
        [HttpGet("{id:int}")]
        public async Task<IActionResult> GetById(int id)
        {
            var order = await _orderService.GetByIdAsync(id);

            if (order == null)
                return NotFound();

            var orderDto = _mapper.Map<OrderDto>(order);

            return Ok(orderDto);
        }

        // POST: api/Orders/checkout
        [HttpPost("checkout")]
        public async Task<IActionResult> Checkout(
            CreateOrderRequestDto request)
        {
            var items = _mapper.Map<
                List<Models.Domain.OrderItem>
            >(request.Items);

            var order = await _orderService.CheckoutAsync(
                request.CustomerId,
                items,
                request.DiscountAmount);

            var orderDto = _mapper.Map<OrderDto>(order);

            return CreatedAtAction(
                nameof(GetById),
                new { id = order.Id },
                orderDto);
        }
    }
}



