namespace StoreInventory.API.Models.DTO
{
    public class ApiResponseDto<T>
    {
        public bool Success { get; set; }

        public string Message { get; set; } = "";

        public T? Data { get; set; }
    }
}


[HttpGet("{id:int}")]
public async Task<IActionResult> GetById(int id)
{
    var product = await _productService.GetByIdAsync(id);

    if (product == null)
    {
        var response = new ApiResponseDto<ProductDto>
        {
            Success = false,
            Message = $"Product with ID {id} was not found.",
            Data = null
        };

        return Ok(response);
    }

    var productDto = _mapper.Map<ProductDto>(product);

    var successResponse = new ApiResponseDto<ProductDto>
    {
        Success = true,
        Message = "Product found.",
        Data = productDto
    };

    return Ok(successResponse);
}