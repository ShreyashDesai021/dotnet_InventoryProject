using AutoMapper;
using Microsoft.AspNetCore.Mvc;
using StoreInventory.API.Models.Domain;
using StoreInventory.API.Models.DTO;
using StoreInventory.API.Services.Interfaces;

namespace StoreInventory.API.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class ProductsController : ControllerBase
    {
        private readonly IProductService _productService;
        private readonly IMapper _mapper;

        public ProductsController(
            IProductService productService,
            IMapper mapper)
        {
            _productService = productService;
            _mapper = mapper;
        }

        // GET: api/products
        [HttpGet]
        public async Task<IActionResult> GetAll()
        {
            var products = await _productService.GetAllAsync();

            var productDtos = _mapper.Map<List<ProductDto>>(products);

            return Ok(productDtos);
        }

        // GET: api/products/5
        [HttpGet("{id:int}")]
        public async Task<IActionResult> GetById(int id)
        {
            var product = await _productService.GetByIdAsync(id);

            if (product == null)
            {
                return NotFound();
            }

            var productDto = _mapper.Map<ProductDto>(product);

            return Ok(productDto);
        }

        // POST: api/products
        [HttpPost]
        public async Task<IActionResult> Create(
            CreateProductRequestDto request)
        {
            var product = _mapper.Map<Product>(request);

            var createdProduct = await _productService
                .CreateAsync(product);

            var productDto = _mapper.Map<ProductDto>(createdProduct);

            return CreatedAtAction(
                nameof(GetById),
                new { id = productDto.Id },
                productDto);
        }

        // PUT: api/products/5
        [HttpPut("{id:int}")]
        public async Task<IActionResult> Update(
            int id,
            UpdateProductRequestDto request)
        {
            var product = _mapper.Map<Product>(request);

            var updatedProduct = await _productService
                .UpdateAsync(id, product);

            if (updatedProduct == null)
            {
                return NotFound();
            }

            var productDto = _mapper.Map<ProductDto>(updatedProduct);

            return Ok(productDto);
        }

        // DELETE: api/products/5
        [HttpDelete("{id:int}")]
        public async Task<IActionResult> Delete(int id)
        {
            var deletedProduct = await _productService
                .DeleteAsync(id);

            if (deletedProduct == null)
            {
                return NotFound();
            }

            var productDto = _mapper.Map<ProductDto>(deletedProduct);

            return Ok(productDto);
        }
    }
}