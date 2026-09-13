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

        Task<Order?> UpdateAsync(int id, Order order);

        Task<Order?> DeleteAsync(int id);
    }
}


using StoreInventory.API.Exceptions;
using StoreInventory.API.Models.Domain;
using StoreInventory.API.Repositories.Interfaces;
using StoreInventory.API.Services.Interfaces;

namespace StoreInventory.API.Services
{
    public class OrderService : IOrderService
    {
        private readonly IOrderRepository _orderRepository;
        private readonly ICustomerRepository _customerRepository;
        private readonly IProductRepository _productRepository;

        public OrderService(
            IOrderRepository orderRepository,
            ICustomerRepository customerRepository,
            IProductRepository productRepository)
        {
            _orderRepository = orderRepository;
            _customerRepository = customerRepository;
            _productRepository = productRepository;
        }

        public async Task<List<Order>> GetAllAsync()
        {
            return await _orderRepository.GetAllAsync();
        }

        public async Task<Order?> GetByIdAsync(int id)
        {
            return await _orderRepository.GetByIdAsync(id);
        }

        public async Task<Order> CheckoutAsync(
            int customerId,
            List<OrderItem> items,
            decimal discountAmount)
        {
            // An order must contain at least one product.
            if (items == null || items.Count == 0)
            {
                throw new InvalidOperationException(
                    "An order must contain at least one product.");
            }

            // Check customer.
            var customer = await _customerRepository.GetByIdAsync(customerId);

            if (customer == null)
            {
                throw new KeyNotFoundException(
                    $"Customer with ID {customerId} was not found.");
            }

            // Validate all items BEFORE changing inventory.
            var products = new List<Product>();

            foreach (var item in items)
            {
                if (item.Quantity <= 0)
                {
                    throw new InvalidOperationException(
                        "Quantity must be greater than zero.");
                }

                var product = await _productRepository
                    .GetByIdAsync(item.ProductId);

                if (product == null)
                {
                    throw new KeyNotFoundException(
                        $"Product with ID {item.ProductId} was not found.");
                }

                if (product.StockQuantity < item.Quantity)
                {
                    throw new OutOfStockException(
                        $"Insufficient stock for {product.Name}. " +
                        $"Available: {product.StockQuantity}, " +
                        $"Requested: {item.Quantity}.");
                }

                // Use the actual price from the database.
                item.UnitPrice = product.Price;

                products.Add(product);
            }

            // Calculate subtotal.
            decimal subtotal = 0;

            for (int i = 0; i < items.Count; i++)
            {
                subtotal += products[i].Price * items[i].Quantity;
            }

            // Apply 10% discount if subtotal is greater than DiscountAmount.
            decimal discount = subtotal > discountAmount
                ? subtotal * 0.10m
                : 0;

            decimal total = subtotal - discount;

            // Update inventory ONLY after all stock checks passed.
            for (int i = 0; i < items.Count; i++)
            {
                products[i].StockQuantity -= items[i].Quantity;

                await _productRepository.UpdateAsync(
                    products[i].Id,
                    products[i]);
            }

            // Create order.
            // IMPORTANT:
            // We do NOT manually assign Id here.
            // SQL Server will generate the Order ID.
            var order = new Order
            {
                CustomerId = customerId,
                Items = items,
                OrderDate = DateTime.Now,
                TotalAmount = total,
                Discount = discount,
                Status = OrderStatus.Completed
            };

            await _orderRepository.CreateAsync(order);

            return order;
        }

        public async Task<Order?> UpdateAsync(int id, Order order)
        {
            return await _orderRepository.UpdateAsync(id, order);
        }

        public async Task<Order?> DeleteAsync(int id)
        {
            return await _orderRepository.DeleteAsync(id);
        }
    }
}