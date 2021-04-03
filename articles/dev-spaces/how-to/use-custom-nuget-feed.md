---
title: Använda en anpassad NuGet-feed
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Använd en anpassad NuGet-feed för att få åtkomst till och använda NuGet-paket i ett Azure dev-utrymme.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, behållare
manager: gwallace
ms.openlocfilehash: d60d7142d9b9979be76eebb3d324a448bd76638f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91960226"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>Använda en anpassad NuGet-feed med Azure dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

En NuGet-feed är ett bekvämt sätt att inkludera paket källor i ett projekt. Azure dev Spaces måste ha åtkomst till denna feed för att beroenden ska kunna installeras korrekt i Docker-behållaren.

## <a name="set-up-a-nuget-feed"></a>Konfigurera en NuGet-feed

Lägg till en [paket referens](/nuget/consume-packages/package-references-in-project-files) för ditt beroende i `*.csproj` filen under `PackageReference` noden. Exempel:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Skapa en [NuGet.Config](/nuget/reference/nuget-config-file) -fil i projektmappen och ange `packageSources` `packageSourceCredentials` avsnitten och för din NuGet-feed. `packageSources`Avsnittet innehåller din feed-URL, som måste vara tillgänglig från ditt AKS-kluster. `packageSourceCredentials`Är autentiseringsuppgifterna för att komma åt feeden. Exempel:

```xml
<packageSources>
    <add key="Contoso" value="https://contoso.com/packages/" />
</packageSources>

<packageSourceCredentials>
    <Contoso>
        <add key="Username" value="user@contoso.com" />
        <add key="ClearTextPassword" value="33f!!lloppa" />
    </Contoso>
</packageSourceCredentials>
```

Uppdatera Dockerfiles för att kopiera `NuGet.Config` filen till avbildningen. Exempel:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> I Windows, `NuGet.Config` , `Nuget.Config` , och `nuget.config` alla fungerar som giltiga fil namn. I Linux är bara `NuGet.Config` ett giltigt fil namn för den här filen. Eftersom Azure dev Spaces använder Docker och Linux måste den här filen namnges `NuGet.Config` . Du kan åtgärda namngivningen manuellt eller genom att köra `dotnet restore --configfile nuget.config` .


Om du använder git bör du inte ha autentiseringsuppgifterna för NuGet-flödet i versions kontrollen. Lägg till i `NuGet.Config` `.gitignore` för ditt projekt så att `NuGet.Config` filen inte läggs till i versions kontrollen. Azure dev Spaces behöver den här filen under build-processen för behållar avbildningen, men som standard uppfyller den de regler som definierats i `.gitignore` och `.dockerignore` under synkroniseringen. Om du vill ändra standard och låta Azure dev Spaces synkronisera `NuGet.Config` filen uppdaterar du `azds.yaml` filen:

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

Om du inte använder git kan du hoppa över det här steget.

Nästa gången du kör `azds up` eller träffar `F5` i Visual Studio Code eller Visual Studio kommer Azure dev Spaces att synkronisera `NuGet.Config` filen Använd den för att installera paket beroenden.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [NuGet och hur det fungerar](/nuget/what-is-nuget).
