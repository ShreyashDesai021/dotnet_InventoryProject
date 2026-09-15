RegisterRequestDto.cs

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

        public string Role { get; set; } = "Employee";
    }
}




LoginRequestDto.cs


using System.ComponentModel.DataAnnotations;

namespace StoreInventory.API.Models.DTO
{
    public class LoginRequestDto
    {
        [Required]
        public string Username { get; set; } = "";

        [Required]
        public string Password { get; set; } = "";
    }
}


LoginResponseDto.cs


namespace StoreInventory.API.Models.DTO
{
    public class LoginResponseDto
    {
        public string Token { get; set; } = "";

        public int UserId { get; set; }

        public string Username { get; set; } = "";

        public string Role { get; set; } = "";
    }
}






using StoreInventory.API.Models.Domain;

namespace StoreInventory.API.Services.Interfaces
{
    public interface IAuthService
    {
        Task<ApplicationUser> RegisterAsync(
            string username,
            string email,
            string password,
            string role);

        Task<string> LoginAsync(
            string username,
            string password);
    }
}



using System.IdentityModel.Tokens.Jwt;
using System.Security.Claims;
using System.Text;
using Microsoft.AspNetCore.Identity;
using Microsoft.IdentityModel.Tokens;
using StoreInventory.API.Models.Domain;
using StoreInventory.API.Repositories.Interfaces;
using StoreInventory.API.Services.Interfaces;

namespace StoreInventory.API.Services
{
    public class AuthService : IAuthService
    {
        private readonly IUserRepository _userRepository;
        private readonly IConfiguration _configuration;
        private readonly PasswordHasher<ApplicationUser> _passwordHasher;

        public AuthService(
            IUserRepository userRepository,
            IConfiguration configuration)
        {
            _userRepository = userRepository;
            _configuration = configuration;
            _passwordHasher = new PasswordHasher<ApplicationUser>();
        }

        public async Task<ApplicationUser> RegisterAsync(
            string username,
            string email,
            string password,
            string role)
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
                Role = role
            };

            user.PasswordHash =
                _passwordHasher.HashPassword(user, password);

            return await _userRepository.CreateAsync(user);
        }

        public async Task<string> LoginAsync(
            string username,
            string password)
        {
            var user =
                await _userRepository.GetByUsernameAsync(username);

            if (user == null)
            {
                throw new UnauthorizedAccessException(
                    "Invalid username or password.");
            }

            var passwordResult =
                _passwordHasher.VerifyHashedPassword(
                    user,
                    user.PasswordHash,
                    password);

            if (passwordResult ==
                PasswordVerificationResult.Failed)
            {
                throw new UnauthorizedAccessException(
                    "Invalid username or password.");
            }

            return GenerateJwtToken(user);
        }

        private string GenerateJwtToken(ApplicationUser user)
        {
            var jwtSettings =
                _configuration.GetSection("Jwt");

            var key =
                jwtSettings["Key"]
                ?? throw new InvalidOperationException(
                    "JWT key is not configured.");

            var issuer =
                jwtSettings["Issuer"]
                ?? throw new InvalidOperationException(
                    "JWT issuer is not configured.");

            var audience =
                jwtSettings["Audience"]
                ?? throw new InvalidOperationException(
                    "JWT audience is not configured.");

            var expiryMinutes =
                int.Parse(
                    jwtSettings["ExpiryMinutes"] ?? "60");

            var claims = new List<Claim>
            {
                new Claim(
                    ClaimTypes.NameIdentifier,
                    user.Id.ToString()),

                new Claim(
                    ClaimTypes.Name,
                    user.Username),

                new Claim(
                    ClaimTypes.Email,
                    user.Email),

                new Claim(
                    ClaimTypes.Role,
                    user.Role)
            };

            var securityKey =
                new SymmetricSecurityKey(
                    Encoding.UTF8.GetBytes(key));

            var credentials =
                new SigningCredentials(
                    securityKey,
                    SecurityAlgorithms.HmacSha256);

            var token = new JwtSecurityToken(
                issuer: issuer,
                audience: audience,
                claims: claims,
                expires: DateTime.UtcNow.AddMinutes(expiryMinutes),
                signingCredentials: credentials);

            return new JwtSecurityTokenHandler()
                .WriteToken(token);
        }
    }
}



builder.Services.AddScoped<IAuthService, AuthService>();


