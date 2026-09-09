builder.Services.AddDbContext<StoreInventoryDbContext>(options =>
    options.UseSqlServer(
        builder.Configuration.GetConnectionString("DefaultConnection")
    ));


using StoreInventory.API.Data;