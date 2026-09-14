using Microsoft.EntityFrameworkCore;
using StoreInventory.API.Data;
using StoreInventory.API.Exceptions;
using StoreInventory.API.Models.Domain;
using StoreInventory.API.Repositories.Interfaces;
using StoreInventory.API.Services.Interfaces;

namespace StoreInventory.API.Services
{
    public class OrderService : IOrderService
    {
        private readonly StoreInventoryDbContext _context;
        private readonly IOrderRepository _orderRepository;
        private readonly ICustomerRepository _customerRepository;
        private readonly IProductRepository _productRepository;

        public OrderService(
            StoreInventoryDbContext context,
            IOrderRepository orderRepository,
            ICustomerRepository customerRepository,
            IProductRepository productRepository)
        {
            _context = context;
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
            // Start a database transaction.
            await using var transaction =
                await _context.Database.BeginTransactionAsync();

            try
            {
                // An order must contain at least one product.
                if (items == null || items.Count == 0)
                {
                    throw new InvalidOperationException(
                        "An order must contain at least one product.");
                }

                // Check customer.
                var customer =
                    await _customerRepository.GetByIdAsync(customerId);

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

                    var product =
                        await _productRepository.GetByIdAsync(
                            item.ProductId);

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

                    // Always use the actual database price.
                    item.UnitPrice = product.Price;

                    products.Add(product);
                }

                // Calculate subtotal.
                decimal subtotal = 0;

                for (int i = 0; i < items.Count; i++)
                {
                    subtotal +=
                        products[i].Price * items[i].Quantity;
                }

                // Apply 10% discount when subtotal
                // is greater than discountAmount.
                decimal discount = subtotal > discountAmount
                    ? subtotal * 0.10m
                    : 0;

                decimal total = subtotal - discount;

                // Reduce inventory only after all validations pass.
                for (int i = 0; i < items.Count; i++)
                {
                    products[i].StockQuantity -= items[i].Quantity;

                    await _productRepository.UpdateAsync(
                        products[i].Id,
                        products[i]);
                }

                // Create the order.
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

                // Everything succeeded.
                await transaction.CommitAsync();

                return order;
            }
            catch
            {
                // Something failed.
                // Undo every database change made in this transaction.
                await transaction.RollbackAsync();

                throw;
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
    }
}