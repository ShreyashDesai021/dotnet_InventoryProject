[HttpPut("{id:int}")]
public async Task<IActionResult> Update(
    int id,
    UpdateProductRequestDto request)
{
    var product = _mapper.Map<Product>(request);

    var updatedProduct =
        await _productService.UpdateAsync(id, product);

    if (updatedProduct == null)
    {
        var response = new ApiResponseDto<ProductDto>
        {
            Success = false,
            Message = $"Product with ID {id} was not found.",
            Data = null
        };

        return Ok(response);
    }

    var productDto =
        _mapper.Map<ProductDto>(updatedProduct);

    var successResponse = new ApiResponseDto<ProductDto>
    {
        Success = true,
        Message = "Product updated successfully.",
        Data = productDto
    };

    return Ok(successResponse);
}



[HttpDelete("{id:int}")]
public async Task<IActionResult> Delete(int id)
{
    var deletedProduct =
        await _productService.DeleteAsync(id);

    if (deletedProduct == null)
    {
        var response = new ApiResponseDto<ProductDto>
        {
            Success = false,
            Message = $"Product with ID {id} was not found.",
            Data = null
        };

        return Ok(response);
    }

    var productDto =
        _mapper.Map<ProductDto>(deletedProduct);

    var successResponse = new ApiResponseDto<ProductDto>
    {
        Success = true,
        Message = "Product deleted successfully.",
        Data = productDto
    };

    return Ok(successResponse);
}


[HttpPut("{id:int}")]
public async Task<IActionResult> Update(
    int id,
    UpdateCustomerRequestDto request)
{
    var customer =
        _mapper.Map<Customer>(request);

    var updatedCustomer =
        await _customerService.UpdateAsync(id, customer);

    if (updatedCustomer == null)
    {
        var response = new ApiResponseDto<CustomerDto>
        {
            Success = false,
            Message = $"Customer with ID {id} was not found.",
            Data = null
        };

        return Ok(response);
    }

    var customerDto =
        _mapper.Map<CustomerDto>(updatedCustomer);

    var successResponse =
        new ApiResponseDto<CustomerDto>
        {
            Success = true,
            Message = "Customer updated successfully.",
            Data = customerDto
        };

    return Ok(successResponse);
}


[HttpDelete("{id:int}")]
public async Task<IActionResult> Delete(int id)
{
    var deletedCustomer =
        await _customerService.DeleteAsync(id);

    if (deletedCustomer == null)
    {
        var response = new ApiResponseDto<CustomerDto>
        {
            Success = false,
            Message = $"Customer with ID {id} was not found.",
            Data = null
        };

        return Ok(response);
    }

    var customerDto =
        _mapper.Map<CustomerDto>(deletedCustomer);

    var successResponse =
        new ApiResponseDto<CustomerDto>
        {
            Success = true,
            Message = "Customer deleted successfully.",
            Data = customerDto
        };

    return Ok(successResponse);
}


