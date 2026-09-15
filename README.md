// Combine duplicate products into a single order item.
var groupedItems = items
    .GroupBy(item => item.ProductId)
    .Select(group => new OrderItem
    {
        ProductId = group.Key,
        Quantity = group.Sum(item => item.Quantity)
    })
    .ToList();

// Validate all items BEFORE changing inventory.
var products = new List<Product>();

foreach (var item in groupedItems)
{
    if (item.Quantity <= 0)
    {
        throw new InvalidOperationException(
            "Quantity must be greater than zero.");
    }

    var product =
        await _productRepository.GetByIdAsync(item.ProductId);

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

for (int i = 0; i < groupedItems.Count; i++)
{
    subtotal +=
        products[i].Price * groupedItems[i].Quantity;
}

// Apply 10% discount when subtotal
// is greater than discountAmount.
decimal discount = subtotal > discountAmount
    ? subtotal * 0.10m
    : 0;

decimal total = subtotal - discount;

// Reduce inventory only after all validations pass.
for (int i = 0; i < groupedItems.Count; i++)
{
    products[i].StockQuantity -= groupedItems[i].Quantity;

    await _productRepository.UpdateAsync(
        products[i].Id,
        products[i]);
}



var order = new Order
{
    CustomerId = customerId,
    Items = groupedItems,
    OrderDate = DateTime.Now,
    TotalAmount = total,
    Discount = discount,
    Status = OrderStatus.Completed
};


{
  "customerId": 2,
  "items": [
    {
      "productId": 4,
      "quantity": 1
    },
    {
      "productId": 4,
      "quantity": 2
    }
  ],
  "discountAmount": 5000
}


{
  "customerId": 2,
  "items": [
    {
      "productId": 4,
      "quantity": 1
    },
    {
      "productId": 4,
      "quantity": 2
    }
  ],
  "discountAmount": 5000
}
