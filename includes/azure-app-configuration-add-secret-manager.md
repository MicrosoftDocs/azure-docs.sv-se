---
ms.openlocfilehash: b1be5e903994f0a2c4a3d457f0c2bb5572a889c3
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98663037"
---
## <a name="add-secret-manager"></a>Lägga till Secret Manager

Ett verktyg som kallas Secret Manager lagrar känsliga data för utvecklings arbete utanför projekt trädet. Den här metoden hjälper till att förhindra oavsiktlig delning av apphemligheter i källkoden. Utför följande steg för att aktivera användning av Secret Manager i ASP.NET Core-projektet:

#### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)

Navigera till projektets rot Katalog och kör följande kommando för att aktivera hemligheter för lagring i projektet:

```dotnetcli
dotnet user-secrets init
```

Ett `UserSecretsId` element som innehåller ett GUID läggs till i *. CSPROJ* -filen:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    
    <PropertyGroup>
        <TargetFramework>net5.0</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

</Project>
```

#### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

Navigera till projektets rot Katalog och kör följande kommando för att aktivera hemligheter för lagring i projektet:

```dotnetcli
dotnet user-secrets init
```

Ett `UserSecretsId` element som innehåller ett GUID läggs till i *. CSPROJ* -filen:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    
    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

</Project>
```

#### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

1. Öppna *. CSPROJ* -filen.

1. Lägg till ett- `UserSecretsId` element i *. CSPROJ* -filen som visas här. Du kan använda samma GUID, eller så kan du ersätta det här värdet med ditt eget.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    
        <PropertyGroup>
            <TargetFramework>netcoreapp2.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>
    
        <ItemGroup>
            <PackageReference Include="Microsoft.AspNetCore.App" />
            <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
        </ItemGroup>
    
    </Project>
    ```
    
1. Spara *. CSPROJ* -filen.

---

> [!TIP]
> Mer information om Secret Manager finns i [säker lagring av program hemligheter i utveckling i ASP.net Core](/aspnet/core/security/app-secrets).
