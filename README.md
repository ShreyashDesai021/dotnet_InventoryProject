using StoreInventory.API.Models.Domain;

namespace StoreInventory.API.Repositories.Interfaces
{
    public interface IProductRepository
    {
        Task<List<Product>> GetAllAsync();

        Task<Product?> GetByIdAsync(int id);

        Task<Product> CreateAsync(Product product);

        Task<Product?> UpdateAsync(int id, Product product);

        Task<Product?> DeleteAsync(int id);
    }
}