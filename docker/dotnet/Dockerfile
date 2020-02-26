# Builder
FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS builder 
WORKDIR /source
COPY . .
RUN dotnet publish --output package --configuration release

# Production
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2-alpine
WORKDIR /app
COPY --from=builder /source/package .
ENTRYPOINT ["dotnet", "<name>.dll"]
