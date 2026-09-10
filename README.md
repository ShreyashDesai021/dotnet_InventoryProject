// PUT: api/products/5
[HttpPut("{id:int}")]
public async Task<IActionResult> Update(
    int id,
    UpdateProductRequestDto request)
{
    var product = _mapper.Map<Product>(request);

    product.Id = id;

    var updatedProduct = await _productService
        .UpdateAsync(id, product);

    if (updatedProduct == null)
    {
        return NotFound();
    }

    var productDto = _mapper.Map<ProductDto>(updatedProduct);

    return Ok(productDto);
}