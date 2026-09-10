using AutoMapper;
using StoreInventory.API.Models.Domain;
using StoreInventory.API.Models.DTO;

namespace StoreInventory.API.Mappings
{
    public class AutoMapperProfile : Profile
    {
        public AutoMapperProfile()
        {
            CreateMap<Product, ProductDto>();

            CreateMap<CreateProductRequestDto, Product>();

            CreateMap<UpdateProductRequestDto, Product>();
        }
    }
}