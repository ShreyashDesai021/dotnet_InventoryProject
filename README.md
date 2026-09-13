using StoreInventory.API.Models.Domain;

namespace StoreInventory.API.Repositories.Interfaces
{
    public interface ICustomerRepository
    {
        Task<List<Customer>> GetAllAsync();

        Task<Customer?> GetByIdAsync(int id);

        Task<Customer> CreateAsync(Customer customer);

        Task<Customer?> UpdateAsync(int id, Customer customer);

        Task<Customer?> DeleteAsync(int id);
    }
}


using Microsoft.EntityFrameworkCore;
using StoreInventory.API.Data;
using StoreInventory.API.Models.Domain;
using StoreInventory.API.Repositories.Interfaces;

namespace StoreInventory.API.Repositories.SQL
{
    public class CustomerRepository : ICustomerRepository
    {
        private readonly StoreInventoryDbContext _context;

        public CustomerRepository(StoreInventoryDbContext context)
        {
            _context = context;
        }

        public async Task<List<Customer>> GetAllAsync()
        {
            return await _context.Customers
                .ToListAsync();
        }

        public async Task<Customer?> GetByIdAsync(int id)
        {
            return await _context.Customers
                .FirstOrDefaultAsync(c => c.Id == id);
        }

        public async Task<Customer> CreateAsync(Customer customer)
        {
            await _context.Customers.AddAsync(customer);

            await _context.SaveChangesAsync();

            return customer;
        }

        public async Task<Customer?> UpdateAsync(
            int id,
            Customer customer)
        {
            var existingCustomer = await _context.Customers
                .FirstOrDefaultAsync(c => c.Id == id);

            if (existingCustomer == null)
            {
                return null;
            }

            existingCustomer.Name = customer.Name;
            existingCustomer.Email = customer.Email;
            existingCustomer.Phone = customer.Phone;

            await _context.SaveChangesAsync();

            return existingCustomer;
        }

        public async Task<Customer?> DeleteAsync(int id)
        {
            var customer = await _context.Customers
                .FirstOrDefaultAsync(c => c.Id == id);

            if (customer == null)
            {
                return null;
            }

            _context.Customers.Remove(customer);

            await _context.SaveChangesAsync();

            return customer;
        }
    }
}

builder.Services.AddScoped<ICustomerRepository, CustomerRepository>();