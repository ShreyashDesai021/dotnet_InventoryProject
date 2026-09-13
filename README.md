using StoreInventory.API.Models.Domain;

namespace StoreInventory.API.Services.Interfaces
{
    public interface ICustomerService
    {
        Task<List<Customer>> GetAllAsync();

        Task<Customer?> GetByIdAsync(int id);

        Task<Customer> CreateAsync(Customer customer);

        Task<Customer?> UpdateAsync(int id, Customer customer);

        Task<Customer?> DeleteAsync(int id);
    }
}



using StoreInventory.API.Models.Domain;
using StoreInventory.API.Repositories.Interfaces;
using StoreInventory.API.Services.Interfaces;

namespace StoreInventory.API.Services
{
    public class CustomerService : ICustomerService
    {
        private readonly ICustomerRepository _customerRepository;

        public CustomerService(ICustomerRepository customerRepository)
        {
            _customerRepository = customerRepository;
        }

        public async Task<List<Customer>> GetAllAsync()
        {
            return await _customerRepository.GetAllAsync();
        }

        public async Task<Customer?> GetByIdAsync(int id)
        {
            return await _customerRepository.GetByIdAsync(id);
        }

        public async Task<Customer> CreateAsync(Customer customer)
        {
            return await _customerRepository.CreateAsync(customer);
        }

        public async Task<Customer?> UpdateAsync(
            int id,
            Customer customer)
        {
            return await _customerRepository
                .UpdateAsync(id, customer);
        }

        public async Task<Customer?> DeleteAsync(int id)
        {
            return await _customerRepository.DeleteAsync(id);
        }
    }
}


builder.Services.AddScoped<IProductRepository, ProductRepository>();

builder.Services.AddScoped<ICustomerRepository, CustomerRepository>();

builder.Services.AddScoped<IProductService, ProductService>();

builder.Services.AddScoped<ICustomerService, CustomerService>();