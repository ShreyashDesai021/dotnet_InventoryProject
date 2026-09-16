using System.ComponentModel.DataAnnotations;

namespace StoreInventory.API.Models.DTO
{
    public class RegisterRequestDto
    {
        [Required]
        [StringLength(50, MinimumLength = 3)]
        public string Username { get; set; } = "";

        [Required]
        [EmailAddress]
        public string Email { get; set; } = "";

        [Required]
        [MinLength(6)]
        public string Password { get; set; } = "";
    }
}


Task<ApplicationUser> RegisterAsync(
    string username,
    string email,
    string password);


using StoreInventory.API.Models.Domain;
using StoreInventory.API.Models.DTO;

namespace StoreInventory.API.Services.Interfaces
{
    public interface IAuthService
    {
        Task<ApplicationUser> RegisterAsync(
            string username,
            string email,
            string password);

        Task<LoginResponseDto> LoginAsync(
            string username,
            string password);
    }
}


public async Task<ApplicationUser> RegisterAsync(
    string username,
    string email,
    string password)



public async Task<ApplicationUser> RegisterAsync(
    string username,
    string email,
    string password)
{
    var existingUsername =
        await _userRepository.GetByUsernameAsync(username);

    if (existingUsername != null)
    {
        throw new InvalidOperationException(
            "Username already exists.");
    }

    var existingEmail =
        await _userRepository.GetByEmailAsync(email);

    if (existingEmail != null)
    {
        throw new InvalidOperationException(
            "Email already exists.");
    }

    var user = new ApplicationUser
    {
        Username = username,
        Email = email,
        Role = "Employee"
    };

    user.PasswordHash =
        _passwordHasher.HashPassword(user, password);

    return await _userRepository.CreateAsync(user);
}


{
  "username": "employee2",
  "email": "employee2@store.com",
  "password": "Employee@123"
}


{
  "username": "testadmin",
  "email": "testadmin@store.com",
  "password": "TestAdmin@123",
  "role": "Admin"
}