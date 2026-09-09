using Microsoft.EntityFrameworkCore;
using StoreInventory.API.Data;
using StoreInventory.API.Models.Domain;
using StoreInventory.API.Repositories.Interfaces;

namespace StoreInventory.API.Repositories.SQL
{
    public class ProductRepository : IProductRepository
    {
        private readonly StoreInventoryDbContext _context;

        public ProductRepository(StoreInventoryDbContext context)
        {
            _context = context;
        }

        public async Task<List<Product>> GetAllAsync()
        {
            return await _context.Products.ToListAsync();
        }

        public async Task<Product?> GetByIdAsync(int id)
        {
            return await _context.Products
                .FirstOrDefaultAsync(p => p.Id == id);
        }

        public async Task<Product> CreateAsync(Product product)
        {
            await _context.Products.AddAsync(product);
            await _context.SaveChangesAsync();

            return product;
        }

        public async Task<Product?> UpdateAsync(int id, Product product)
        {
            var existingProduct = await _context.Products
                .FirstOrDefaultAsync(p => p.Id == id);

            if (existingProduct == null)
                return null;

            existingProduct.Name = product.Name;
            existingProduct.Price = product.Price;
            existingProduct.StockQuantity = product.StockQuantity;

            await _context.SaveChangesAsync();

            return existingProduct;
        }

        public async Task<Product?> DeleteAsync(int id)
        {
            var product = await _context.Products
                .FirstOrDefaultAsync(p => p.Id == id);

            if (product == null)
                return null;

            _context.Products.Remove(product);
            await _context.SaveChangesAsync();

            return product;
        }
    }
}