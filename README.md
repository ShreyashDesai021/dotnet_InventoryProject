using StoreInventory.API.Models.Domain;

namespace StoreInventory.API.Repositories.Interfaces
{
    public interface IUserRepository
    {
        Task<ApplicationUser?> GetByUsernameAsync(string username);

        Task<ApplicationUser?> GetByEmailAsync(string email);

        Task<ApplicationUser> CreateAsync(ApplicationUser user);
    }
}


using Microsoft.EntityFrameworkCore;
using StoreInventory.API.Data;
using StoreInventory.API.Models.Domain;
using StoreInventory.API.Repositories.Interfaces;

namespace StoreInventory.API.Repositories.SQL
{
    public class UserRepository : IUserRepository
    {
        private readonly StoreInventoryDbContext _context;

        public UserRepository(StoreInventoryDbContext context)
        {
            _context = context;
        }

        public async Task<ApplicationUser?> GetByUsernameAsync(
            string username)
        {
            return await _context.Users
                .FirstOrDefaultAsync(u => u.Username == username);
        }

        public async Task<ApplicationUser?> GetByEmailAsync(
            string email)
        {
            return await _context.Users
                .FirstOrDefaultAsync(u => u.Email == email);
        }

        public async Task<ApplicationUser> CreateAsync(
            ApplicationUser user)
        {
            await _context.Users.AddAsync(user);
            await _context.SaveChangesAsync();

            return user;
        }
    }
}


builder.Services.AddScoped<IUserRepository, UserRepository>();


