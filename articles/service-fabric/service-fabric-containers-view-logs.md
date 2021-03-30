---
title: Visa behållar loggar i Azure Service Fabric
description: Beskriver hur du visar behållar loggar för en pågående Service Fabric behållar tjänster som använder Service Fabric Explorer.
ms.topic: conceptual
ms.date: 05/15/2018
ms.openlocfilehash: c47a408b272f95dbfcf3d791c644bfeb52254a72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "75458177"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Visa loggar för en Service Fabric container service
Azure Service Fabric är en behållar Orchestrator och stöder både [Linux-och Windows-behållare](service-fabric-containers-overview.md).  Den här artikeln beskriver hur du visar behållar loggar för en pågående container tjänst eller en död behållare så att du kan diagnostisera och felsöka problem.

## <a name="access-the-logs-of-a-running-container"></a>Komma åt loggarna för en behållare som körs
Behållar loggar kan nås med hjälp av [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).  Öppna Service Fabric Explorer från klustrets hanterings slut punkt genom att gå till i en webbläsare `http://mycluster.region.cloudapp.azure.com:19080/Explorer` .  

Behållar loggar finns på den klusternod som behållar tjänst instansen körs på. Exempel: Hämta loggarna för webb klient delen av [exempel programmet Linux-röstning](service-fabric-quickstart-containers-linux.md). I trädvyn expanderar du **kluster** > **program** > **VotingType** > **Fabric:/röstning/azurevotefront**.  Expandera sedan partitionen (d1aa737e-f22a-e347-be16-eec90be24bc1, i det här exemplet) och se att behållaren körs på klusternoden *_lnxvm_0*.

I trädvyn letar du upp kod paketet på *_lnxvm_0* -noden genom att expandera **noder** > **_lnxvm_0** > **Fabric:/röstning** > **azurevotfrontPkg** > **kod paket** > **kod**.  Välj sedan alternativet **behållar loggar** för att Visa behållar loggarna.

![Service Fabric-plattform][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>Komma åt loggarna för en döda eller kraschad behållare
Från och med v 6.2 kan du också hämta loggarna för en död eller kraschad behållare med hjälp av [REST API: er](/rest/api/servicefabric/sfclient-index) eller [Service Fabric CLI (SFCTL)-](service-fabric-cli.md) kommandon.

### <a name="set-container-retention-policy"></a>Ange bevarandeprincip för container
Service Fabric (version 6.1 eller senare) har stöd för bevarande av containrar som har avslutats eller inte kunde starta, vilket underlättar diagnostisering av startfel. Den här principen kan anges i filen **ApplicationManifest.xml**, vilket visas i följande kodavsnitt:
```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
 ```

Inställningen **ContainersRetentionCount** anger antalet behållare som ska bevaras när de får fel. Om ett negativt värde anges kommer alla containrar med fel att bevaras. Om attributet **ContainersRetentionCount** inte anges kommer inga behållare att behållas. Attributet **ContainersRetentionCount** har även stöd för programparametrar, så att användarna kan ange olika värden för test- och produktionskluster. Använd placeringsbegränsningar för att rikta in containertjänsten på en viss nod när den här funktionen används för att förhindra att containertjänsten flyttas till andra noder. Alla containrar som bevaras med den här funktionen måste tas bort manuellt.

Inställningen **RunInteractive** motsvarar Docker- `--interactive` och- `tty` [flaggor](https://docs.docker.com/engine/reference/commandline/run/#options). När den här inställningen är inställd på True i manifest filen används dessa flaggor för att starta behållaren.  

### <a name="rest"></a>REST
Använd funktionen [Hämta container loggar som distribuerats på noden](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode) för att hämta loggarna för en kraschad behållare. Ange namnet på noden som containern kördes på, program namn, namn på tjänst manifest och kod paket namn.  Ange `&Previous=true`. Svaret innehåller behållar loggarna för den döda behållaren för kod paket instansen.

URI för begäran har följande format:

```
/Nodes/{nodeName}/$/GetApplications/{applicationId}/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName={ServiceManifestName}&CodePackageName={CodePackageName}&Previous={Previous}
```

Exempel förfrågan:
```
GET http://localhost:19080/Nodes/_Node_0/$/GetApplications/SimpleHttpServerApp/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName=SimpleHttpServerSvcPkg&CodePackageName=Code&Previous=true  
```

200 svars text:
```json
{   "Content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" } 
```

### <a name="service-fabric-sfctl"></a>Service Fabric (SFCTL)
Använd kommandot [sfctl service get-container-logs](service-fabric-sfctl-service.md) för att hämta loggarna för en kraschad behållare.  Ange namnet på noden som containern kördes på, program namn, namn på tjänst manifest och kod paket namn. Ange `--previous` flaggan.  Svaret innehåller behållar loggarna för den döda behållaren för kod paket instansen.

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –-previous
```
Svar:
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>Nästa steg
- Arbeta genom [självstudien skapa en Linux-container](service-fabric-tutorial-create-container-images.md).
- Läs mer om [Service Fabric och behållare](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
