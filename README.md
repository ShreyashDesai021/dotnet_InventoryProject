using AutoMapper;
using StoreInventory.API.Models.Domain;
using StoreInventory.API.Models.DTO;

namespace StoreInventory.API.Mappings
{
    public class AutoMapperProfile : Profile
    {
        public AutoMapperProfile()
        {
            // Domain Model → Response DTO
            CreateMap<Product, ProductDto>();

            // Create Request DTO → Domain Model
            CreateMap<CreateProductRequestDto, Product>();

            // Update Request DTO → Domain Model
            CreateMap<UpdateProductRequestDto, Product>();
        }
    }
}