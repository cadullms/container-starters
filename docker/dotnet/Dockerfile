# Builder
FROM microsoft/dotnet:2.1-sdk AS builder 
WORKDIR /source
COPY . .
RUN dotnet publish --output package --configuration release

# Production
FROM microsoft/dotnet:2.1-aspnetcore-runtime
WORKDIR /app
COPY --from=builder /source/package .
ENTRYPOINT ["dotnet", "<name>.dll"]
