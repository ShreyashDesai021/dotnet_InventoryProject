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


[HttpGet("{id:int}")]
public async Task<IActionResult> GetById(int id)
{
    var customer = await _customerService.GetByIdAsync(id);

    if (customer == null)
    {
        var response = new ApiResponseDto<CustomerDto>
        {
            Success = false,
            Message = $"Customer with ID {id} was not found.",
            Data = null
        };

        return Ok(response);
    }

    var customerDto = _mapper.Map<CustomerDto>(customer);

    var successResponse = new ApiResponseDto<CustomerDto>
    {
        Success = true,
        Message = "Customer found.",
        Data = customerDto
    };

    return Ok(successResponse);
}



[HttpGet("{id:int}")]
public async Task<IActionResult> GetById(int id)
{
    var order = await _orderService.GetByIdAsync(id);

    if (order == null)
    {
        var response = new ApiResponseDto<OrderDto>
        {
            Success = false,
            Message = $"Order with ID {id} was not found.",
            Data = null
        };

        return Ok(response);
    }

    var orderDto = _mapper.Map<OrderDto>(order);

    var successResponse = new ApiResponseDto<OrderDto>
    {
        Success = true,
        Message = "Order found.",
        Data = orderDto
    };

    return Ok(successResponse);
}


