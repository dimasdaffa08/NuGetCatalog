# NuGetCatalog.PackageVersions — How to Use

This repository provides a **BOM-style NuGet package** to centrally manage dependency versions for all microservices.

---

## 1 Add or Update Library Version

Edit: build/NuGetCatalog.PackageVersions.props

Add or change version:

```xml
<PropertyGroup>
  <SerilogVersion>4.3.0</SerilogVersion>
  <OpenApiVersion>10.0.1</OpenApiVersion>
  <StackExchangeRedisVersion>2.7.33</StackExchangeRedisVersion>
</PropertyGroup>
```

Save and commit

---

## 2 Build & Pack the BOM

From repo root:
```bash
dotnet pack --configuration Release
```
This generates:
```bash
bin/Release/NuGetCatalog.PackageVersions.1.0.0.nupkg
```

---

## 3 Publish to NuGet Feed

Local Feed
```bash
dotnet nuget push bin/Release/NuGetCatalog.PackageVersions.1.0.0.nupkg --source C:\LocalNuGetFeed
```

If you using Private Feed
```bash
dotnet nuget push NuGetCatalog.PackageVersions.1.0.0.nupkg --source https://your-feed/nuget/v3/index.json --api-key YOUR_TOKEN
```

---


## 4 Configure Microservice NuGet Source

Create NuGet.Config in microservice repo:
```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <packageSources>
    <clear />
    <add key="LocalCompanyFeed" value="C:\LocalNuGetFeed" />
    <add key="nuget.org" value="https://api.nuget.org/v3/index.json" />
  </packageSources>
</configuration>
```

---

## 5 Reference the BOM
In microservice`.csproj`, add this code inside `<ItemGroup>`
```xml
<ItemGroup>
  <PackageReference Include="NuGetCatalog.PackageVersions" Version="1.0.0">
    <PrivateAssets>all</PrivateAssets>
    <IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>
  </PackageReference>
</ItemGroup>
```

After that, add packages using variables inside your microservice`.csproj`. Example:
```xml
<ItemGroup>
  <PackageReference Include="Serilog" Version="$(SerilogVersion)" />
  <PackageReference Include="Microsoft.AspNetCore.OpenApi" Version="$(OpenApiVersion)" />
</ItemGroup>
```
