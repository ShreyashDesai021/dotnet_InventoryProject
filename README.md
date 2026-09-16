using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.OpenApi;
using Microsoft.OpenApi;

namespace StoreInventory.API.OpenApi
{
    internal sealed class BearerSecuritySchemeTransformer
        : IOpenApiDocumentTransformer
    {
        private readonly IAuthenticationSchemeProvider _authenticationSchemeProvider;

        public BearerSecuritySchemeTransformer(
            IAuthenticationSchemeProvider authenticationSchemeProvider)
        {
            _authenticationSchemeProvider =
                authenticationSchemeProvider;
        }

        public async Task TransformAsync(
            OpenApiDocument document,
            OpenApiDocumentTransformerContext context,
            CancellationToken cancellationToken)
        {
            var authenticationSchemes =
                await _authenticationSchemeProvider
                    .GetAllSchemesAsync();

            if (authenticationSchemes.Any(
                scheme => scheme.Name == "Bearer"))
            {
                document.Components ??= new OpenApiComponents();

                document.Components.SecuritySchemes =
                    new Dictionary<string, IOpenApiSecurityScheme>
                    {
                        ["Bearer"] = new OpenApiSecurityScheme
                        {
                            Type = SecuritySchemeType.Http,
                            Scheme = "bearer",
                            In = ParameterLocation.Header,
                            BearerFormat = "JWT"
                        }
                    };

                foreach (var path in document.Paths.Values)
                {
                    foreach (var operation in path.Operations.Values)
                    {
                        operation.Security ??=
                            new List<OpenApiSecurityRequirement>();

                        operation.Security.Add(
                            new OpenApiSecurityRequirement
                            {
                                [
                                    new OpenApiSecuritySchemeReference(
                                        "Bearer",
                                        document)
                                ] = []
                            });
                    }
                }
            }
        }
    }
}



builder.Services.AddOpenApi(options =>
{
    options.AddDocumentTransformer<
        StoreInventory.API.OpenApi.BearerSecuritySchemeTransformer>();
});


{
  "username": "admin",
  "password": "Admin@123"
}


