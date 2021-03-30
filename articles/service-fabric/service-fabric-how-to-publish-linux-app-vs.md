---
title: Skapa och publicera a.Net Core-appen till ett fjärran slutet Linux-kluster
description: Skapa och publicera .net Core-appar som riktar sig mot ett fjärran slutet Linux-kluster från Visual Studio
author: peterpogorski
ms.topic: troubleshooting
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: c30eedb6782e4172d677f16e27441f28c78cdd89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "75614357"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>Använd Visual Studio för att skapa och publicera .net Core-program som riktar sig mot ett fjärran slutet Linux Service Fabric-kluster
Med Visual Studio-verktyg kan du utveckla och publicera Service Fabric .net Core-program som riktar sig mot ett Linux Service Fabric-kluster. SDK-versionen måste vara 3,4 eller senare för att du ska kunna distribuera ett .net Core-program riktade Linux Service Fabric-kluster från Visual Studio.

> [!Note]
> Visual Studio stöder inte fel sökning Service Fabric program som är riktade till Linux.
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>Skapa en .net Core för Service Fabric Application Target
1. Starta Visual Studio som **administratör**.
2. Skapa ett projekt med **fil->projekt med ny >**.
3. I dialog rutan **nytt projekt** väljer du **Cloud-> Service Fabric program**.
![Skapa program]
4. Ge programmet ett namn och klicka på **OK**.
5. På sidan **ny Service Fabric tjänst** väljer du den typ av tjänst som du vill skapa under **avsnittet .net Core**.
![Skapa-tjänst]

## <a name="deploy-to-a-remote-linux-cluster"></a>Distribuera till ett fjärran slutet Linux-kluster
1. I Solution Explorer högerklickar du på programmet och väljer **build**.
![Build-Application]
2. När build-processen för programmet har slutförts högerklickar du på tjänsten och väljer Redigera **filen CSPROJ**.
![redigera-CSPROJ]
3. Redigera egenskapen UpdateServiceFabricManifestEnabled från true till **false** om tjänsten är en **aktörs projekt typ**. Om programmet inte har någon aktörs tjänst går du vidare till steg 4.
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> Om du anger UpdateServiceFabricManifestEnabled till falskt inaktive ras uppdateringar av ServiceManifest.xml under en version. Ändringar som att lägga till, ta bort eller byta namn på tjänsten visas inte i ServiceManifest.xml. Om några ändringar görs måste du antingen uppdatera ServiceManifest manuellt eller tillfälligt ange UpdateServiceFabricManifestEnabled till true och skapa den tjänst som ska uppdatera ServiceManifest.xml och sedan återgå tillbaka till falskt.
>

4. Uppdatera RuntimeIndetifier från Win7-x64 till mål plattformen i tjänste projektet.
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. Uppdatera EntryPoint-programmet i ServiceManifest för att ta bort. exe. 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. I Solution Explorer högerklickar du på programmet och väljer **publicera**. Dialogrutan **Publish** (Publicera) visas.
7. I **anslutnings slut punkt** väljer du slut punkten för det fjärran Service Fabric Linux-kluster som du vill använda som mål.
![Publicera – program]

<!--Image references-->
[Skapa program]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[Skapa-tjänst]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[Build-Application]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[redigera-CSPROJ]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[Publicera – program]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om att [komma igång med Service Fabric med .net Core](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)
