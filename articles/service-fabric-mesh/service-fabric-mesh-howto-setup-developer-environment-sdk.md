---
title: Konfigurera en Windows dev-miljö för Service Fabric nät
description: Ställa in Windows-utvecklingsmiljön så att du kan skapa ett Service Fabric Mesh-program och distribuera det till Azure Service Fabric Mesh.
author: georgewallace
ms.author: gwallace
ms.date: 12/12/2018
ms.topic: conceptual
ms.openlocfilehash: fc234f6954cf263423cc517bb3dda2ba2efa3358
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625743"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-mesh-apps"></a>Konfigurera en Windows-utvecklingsmiljö för att skapa Service Fabric Mesh-program

> [!IMPORTANT]
> Förhands granskningen av Azure Service Fabric-nätet har dragits tillbaka. Nya distributioner kommer inte längre att tillåtas via Service Fabric nät-API. Stöd för befintliga distributioner fortsätter till 28 april 2021.
> 
> Mer information finns i [förhands granskning av Azure Service Fabric nät](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

För att kunna skapa och köra Azure Service Fabric Mesh-program på en Windows-utvecklingsdator behöver du:

* Docker
* Visual Studio 2017 eller senare
* Service Fabric Mesh-runtime
* Service Fabric Mesh SDK och verktyg

Och någon av följande versioner av Windows:

* Windows 10 (Enterprise, Professional eller Education) version 1709 (Fall Creators Update) eller 1803 (Windows 10 April 2018 Update)
* Windows Server version 1709
* Windows Server version 1803

Följande instruktioner hjälper dig att få allt installerat baserat på den version av Windows som du kör.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="visual-studio"></a>Visual Studio

Visual Studio 2017 eller senare krävs för att distribuera Service Fabric nätprogram. [Installera version 15.6.0][download-visual-studio] eller senare och aktivera följande arbetsbelastningar:

* ASP.NET och webbutveckling
* Azure Development

## <a name="install-docker"></a>Installera Docker

Om du redan har Docker installerat kontrollerar du att du har den senaste versionen. Docker kan du bli ombedd när en ny version är slut, men kontrol lera manuellt för att se till att du har den senaste versionen.

#### <a name="install-docker-on-windows-10"></a>Installera Docker för Windows 10

Ladda ned och installera den senaste versionen av [Docker Community Edition för Windows][download-docker] för att stödja de containerbaserade Service Fabric-appar som används av Service Fabric Mesh.

Under installationen väljer du att **använda Windows-container i stället för Linux-container** när frågan dyker upp.

Om Hyper-V inte är aktiverat på datorn kommer Docker-installationsprogrammet att erbjudas att aktivera det. Klicka på **OK** för att göra det om du tillfrågas.

#### <a name="install-docker-on-windows-server-2016"></a>Installera Docker för Windows Server 2016

Om du inte har Hyper-V-rollen aktiverad öppnar du PowerShell som administratör och kör följande kommando för att aktivera Hyper-V, och startar sedan om datorn. Mer information finns i dokumentationen om [Docker Enterprise Edition för Windows Server][download-docker-server].

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

Starta om datorn.

Öppna PowerShell som administratör och kör följande kommando för att installera Docker:

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

## <a name="sdk-and-tools"></a>SDK och verktyg

Installera Service Fabric Mesh-runtime, SDK och verktyg i följande ordning.

1. Installera [SDK för Service Fabric Mesh][download-sdkmesh] med installationsprogrammet för webbplattform. Det här installerar dessutom Microsoft Azure Service Fabric SDK och runtime.
2. Installera tillägget [Visual Studio Service Fabric Mesh Tools (förhandsversion)][download-tools] från Visual Studio Marketplace.

## <a name="build-a-cluster"></a>Bygga ett kluster

> [!IMPORTANT]
> Docker **måste** köras innan du kan skapa ett kluster.
> Testa att Docker körs genom att öppna ett terminalfönster och köra `docker ps` för att se om det uppstår något fel. Om svaret inte indikerar något fel körs Docker och du är redo att skapa ett kluster.

> [!Note]
> Om du utvecklar på en dator med Windows Fall Creators Update (version 1709) kan du bara använda Docker-avbildningar för Windows version 1709.
> Om du utvecklar på en dator med Windows 10 April 2018 Update (version 1803) kan du använda Docker-avbildningar för antingen Windows version 1709 eller 1803.

Om du använder Visual Studio kan du hoppa över det här avsnittet eftersom Visual Studio skapar ett lokalt kluster åt dig om du inte har ett.

För bästa möjliga fel söknings prestanda när du skapar och kör en enda Service Fabric app i taget, skapar du ett lokalt utvecklings kluster med en nod. Om du kör flera program i taget skapar du ett lokalt utvecklings kluster med fem noder. Klustret måste köras varje gång du distribuerar eller felsöker ett Service Fabric Mesh-projekt.

När du har installerat runtime, SDK:er, Visual Studio Tools, Docker och har Docker igång skapar du ett utvecklingskluster.

1. Stäng PowerShell-fönstret.
2. Öppna ett nytt, upphöjt PowerShell-fönster som administratör. Det här steget behövs för att läsa in de Service Fabric-moduler som installerades nyligen.
3. Kör följande PowerShell-kommando för att skapa till ett utvecklingskluster:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateMeshCluster -CreateOneNodeCluster
    ```
4. Kör följande PowerShell-kommando för att starta det lokala klusterhanteringsverktyget:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```
5. När tjänstens klusterhanteringsverktyg körs (visas i systemfältet) högerklickar du på det och klickar på **Start Local Cluster** (Starta lokalt cluster).

![Bild 1 – Starta det lokala klustret](./media/service-fabric-mesh-howto-setup-developer-environment-sdk/start-local-cluster.png)

Nu är du redo att skapa Service Fabric Mesh-program!

## <a name="next-steps"></a>Nästa steg

Läs igenom självstudien om att [skapa Azure Service Fabric-program](service-fabric-mesh-tutorial-create-dotnetcore.md).

Få svar på [vanliga frågor och kända problem](service-fabric-mesh-faq.md).

[azure-cli-install]: /cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://aka.ms/sfmesh_vs2017tools
[download-visual-studio]: https://www.visualstudio.com/downloads/
