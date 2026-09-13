using AutoMapper;
using Microsoft.AspNetCore.Mvc;
using StoreInventory.API.Models.Domain;
using StoreInventory.API.Models.DTO;
using StoreInventory.API.Services.Interfaces;

namespace StoreInventory.API.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class CustomersController : ControllerBase
    {
        private readonly ICustomerService _customerService;
        private readonly IMapper _mapper;

        public CustomersController(
            ICustomerService customerService,
            IMapper mapper)
        {
            _customerService = customerService;
            _mapper = mapper;
        }

        // GET: api/customers
        [HttpGet]
        public async Task<IActionResult> GetAll()
        {
            var customers = await _customerService.GetAllAsync();

            var customerDtos =
                _mapper.Map<List<CustomerDto>>(customers);

            return Ok(customerDtos);
        }

        // GET: api/customers/5
        [HttpGet("{id:int}")]
        public async Task<IActionResult> GetById(int id)
        {
            var customer =
                await _customerService.GetByIdAsync(id);

            if (customer == null)
            {
                return NotFound();
            }

            var customerDto =
                _mapper.Map<CustomerDto>(customer);

            return Ok(customerDto);
        }

        // POST: api/customers
        [HttpPost]
        public async Task<IActionResult> Create(
            CreateCustomerRequestDto request)
        {
            var customer =
                _mapper.Map<Customer>(request);

            var createdCustomer =
                await _customerService.CreateAsync(customer);

            var customerDto =
                _mapper.Map<CustomerDto>(createdCustomer);

            return CreatedAtAction(
                nameof(GetById),
                new { id = customerDto.Id },
                customerDto);
        }

        // PUT: api/customers/5
        [HttpPut("{id:int}")]
        public async Task<IActionResult> Update(
            int id,
            UpdateCustomerRequestDto request)
        {
            var customer =
                _mapper.Map<Customer>(request);

            // Explicitly ensure the mapped object uses
            // the ID from the URL.
            customer.Id = id;

            var updatedCustomer =
                await _customerService
                    .UpdateAsync(id, customer);

            if (updatedCustomer == null)
            {
                return NotFound();
            }

            var customerDto =
                _mapper.Map<CustomerDto>(updatedCustomer);

            return Ok(customerDto);
        }

        // DELETE: api/customers/5
        [HttpDelete("{id:int}")]
        public async Task<IActionResult> Delete(int id)
        {
            var deletedCustomer =
                await _customerService.DeleteAsync(id);

            if (deletedCustomer == null)
            {
                return NotFound();
            }

            var customerDto =
                _mapper.Map<CustomerDto>(deletedCustomer);

            return Ok(customerDto);
        }
    }
}



CreateMap<Customer, CustomerDto>();

CreateMap<CreateCustomerRequestDto, Customer>();

CreateMap<UpdateCustomerRequestDto, Customer>();