using Microsoft.AspNetCore.Mvc;
using StoreInventory.API.Models.DTO;
using StoreInventory.API.Services.Interfaces;

namespace StoreInventory.API.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class AuthController : ControllerBase
    {
        private readonly IAuthService _authService;

        public AuthController(IAuthService authService)
        {
            _authService = authService;
        }

        [HttpPost("register")]
        public async Task<IActionResult> Register(
            RegisterRequestDto request)
        {
            try
            {
                var user = await _authService.RegisterAsync(
                    request.Username,
                    request.Email,
                    request.Password,
                    request.Role);

                var response = new ApiResponseDto<object>
                {
                    Success = true,
                    Message = "User registered successfully.",
                    Data = new
                    {
                        user.Id,
                        user.Username,
                        user.Email,
                        user.Role
                    }
                };

                return Ok(response);
            }
            catch (InvalidOperationException ex)
            {
                var response = new ApiResponseDto<object>
                {
                    Success = false,
                    Message = ex.Message,
                    Data = null
                };

                return Ok(response);
            }
        }

        [HttpPost("login")]
        public async Task<IActionResult> Login(
            LoginRequestDto request)
        {
            try
            {
                var token = await _authService.LoginAsync(
                    request.Username,
                    request.Password);

                var response = new ApiResponseDto<LoginResponseDto>
                {
                    Success = true,
                    Message = "Login successful.",
                    Data = new LoginResponseDto
                    {
                        Token = token,
                        Username = request.Username
                    }
                };

                return Ok(response);
            }
            catch (UnauthorizedAccessException ex)
            {
                var response = new ApiResponseDto<LoginResponseDto>
                {
                    Success = false,
                    Message = ex.Message,
                    Data = null
                };

                return Ok(response);
            }
        }
    }
}


using StoreInventory.API.Models.Domain;
using StoreInventory.API.Models.DTO;

namespace StoreInventory.API.Services.Interfaces
{
    public interface IAuthService
    {
        Task<ApplicationUser> RegisterAsync(
            string username,
            string email,
            string password,
            string role);

        Task<LoginResponseDto> LoginAsync(
            string username,
            string password);
    }
}


public async Task<LoginResponseDto> LoginAsync(
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

    return new LoginResponseDto
    {
        Token = GenerateJwtToken(user),
        UserId = user.Id,
        Username = user.Username,
        Role = user.Role
    };
}



[HttpPost("login")]
public async Task<IActionResult> Login(
    LoginRequestDto request)
{
    try
    {
        var loginResponse =
            await _authService.LoginAsync(
                request.Username,
                request.Password);

        var response =
            new ApiResponseDto<LoginResponseDto>
            {
                Success = true,
                Message = "Login successful.",
                Data = loginResponse
            };

        return Ok(response);
    }
    catch (UnauthorizedAccessException ex)
    {
        var response =
            new ApiResponseDto<LoginResponseDto>
            {
                Success = false,
                Message = ex.Message,
                Data = null
            };

        return Ok(response);
    }
}

