using StoreInventory.API.Models.Domain;

namespace StoreInventory.API.Services.Interfaces
{
    public interface IProductService
    {
        Task<List<Product>> GetAllAsync();
        Task<Product?> GetByIdAsync(int id);
        Task<Product> CreateAsync(Product product);
        Task<Product?> UpdateAsync(int id, Product product);
        Task<Product?> DeleteAsync(int id);
    }
}


using StoreInventory.API.Models.Domain;
using StoreInventory.API.Repositories.Interfaces;
using StoreInventory.API.Services.Interfaces;

namespace StoreInventory.API.Services
{
    public class ProductService : IProductService
    {
        private readonly IProductRepository _productRepository;

        public ProductService(IProductRepository productRepository)
        {
            _productRepository = productRepository;
        }

        public async Task<List<Product>> GetAllAsync()
        {
            return await _productRepository.GetAllAsync();
        }

        public async Task<Product?> GetByIdAsync(int id)
        {
            return await _productRepository.GetByIdAsync(id);
        }

        public async Task<Product> CreateAsync(Product product)
        {
            return await _productRepository.CreateAsync(product);
        }

        public async Task<Product?> UpdateAsync(int id, Product product)
        {
            return await _productRepository.UpdateAsync(id, product);
        }

        public async Task<Product?> DeleteAsync(int id)
        {
            return await _productRepository.DeleteAsync(id);
        }
    }
}