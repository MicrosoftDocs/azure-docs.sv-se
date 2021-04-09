---
title: Vanliga frågor för Azure Service Fabric-nät
description: Läs om vanliga frågor och svar om Azure Service Fabric nät.
ms.author: pepogors
ms.date: 4/23/2019
ms.topic: troubleshooting
ms.openlocfilehash: 8e53ab0ae4cc463bea8a6a8cb6d339f94fdcac6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99626044"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Vanliga frågor och svar om Service Fabric nät

> [!IMPORTANT]
> Förhands granskningen av Azure Service Fabric-nätet har dragits tillbaka. Nya distributioner kommer inte längre att tillåtas via Service Fabric nät-API. Stöd för befintliga distributioner fortsätter till 28 april 2021.
> 
> Mer information finns i [förhands granskning av Azure Service Fabric nät](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Azure Service Fabric Mesh är en fullständigt hanterad tjänst som gör att utvecklare kan distribuera mikrotjänstprogram utan att hantera virtuella datorer, lagring eller nätverk. Den här artikeln innehåller svar på vanliga frågor.

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>Hur gör jag för att rapportera ett problem eller Ställ en fråga?

Ställ frågor, få svar från Microsofts tekniker och rapportera problem i [Service-Fabric-nät-för hands version GitHub lagrings platsen](https://aka.ms/sfmeshissues).

## <a name="quota-and-cost"></a>Kvot och kostnad

### <a name="what-is-the-cost-of-participating-in-the-preview"></a>Vad är kostnaden för att delta i förhands granskningen?

Det finns för närvarande inga avgifter för att distribuera program eller behållare till nät för hands versionen. Se efter om det finns uppdateringar i maj för fakturering. Vi rekommenderar dock att du tar bort de resurser du distribuerar och inte låter dem köras, såvida du inte testar dem aktivt.

### <a name="is-there-a-quota-limit-of-the-number-of-cores-and-ram"></a>Finns det en kvot gräns för antalet kärnor och RAM-minne?

Ja. Kvoterna för varje prenumeration är:

- Antal program: 5
- Kärnor per program: 12
- Totalt RAM-minne per program: 48 GB
- Nätverks-och ingångs slut punkter: 5
- Azure-volymer som du kan koppla: 10
- Antal tjänst repliker: 3
- Den största behållaren som du kan distribuera är begränsad till 4 kärnor och 16 GB RAM-minne.
- Du kan allokera delar av kärnor till dina behållare i steg om 0,5 kärnor, upp till högst 6 kärnor.

### <a name="how-long-can-i-leave-my-application-deployed"></a>Hur länge kan jag låta mitt program distribueras?

Vi har för närvarande begränsat livs längden för ett program till två dagar. Detta är för att maximera användningen av de kostnads fria kärnor som allokerats till för hands versionen. Det innebär att du bara får köra en specifik distribution kontinuerligt i 48 timmar, efter vilken tid den stängs.

Om detta inträffar kan du kontrol lera att systemet stängs av genom att köra `az mesh app show` kommandot i Azure CLI. Kontrol lera om den returnerar `"status": "Failed", "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue."` 

Exempel: 

```azurecli
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

```output
{
  "debugParams": null,
  "description": "Service Fabric Mesh HelloWorld Application!",
  "diagnostics": null,
  "healthState": "Ok",
  "id": "/subscriptions/1134234-b756-4979-84re-09d671c0c345/resourcegroups/myResourceGroup/providers/Microsoft.ServiceFabricMesh/applications/helloWorldApp",
  "location": "eastus",
  "name": "helloWorldApp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "serviceNames": [
    "helloWorldService"
  ],
  "services": null,
  "status": "Failed",
  "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue.",
  "tags": {},
  "type": "Microsoft.ServiceFabricMesh/applications",
  "unhealthyEvaluation": null
}
```

Om du vill ta bort resurs gruppen använder du `az group delete <nameOfResourceGroup>` kommandot.

## <a name="deployments"></a>Distributioner

### <a name="what-container-images-are-supported"></a>Vilka behållar avbildningar stöds?

Om du utvecklar på en Windows-dator som har Creators Update (version 1709) kan du bara använda Windows-version 1709 Docker-avbildningar.

Om du utvecklar på en Windows 10 april 2018 uppdaterings dator (version 1803) kan du använda antingen Windows version 1709 eller Windows version 1803 Docker-avbildningar.

Följande behållar-OS-avbildningar kan användas för att distribuera tjänster:
- Windows-windowsservercore och nanoserver
    - Windows Server 1709
    - Windows Server 1803
    - Windows Server 1809
    - Windows Server 2019 LTSC
- Linux
    - Inga kända begränsningar

> [!NOTE]
> Visual Studio-verktyg för nät stöder ännu inte distribution till Windows Server 2019 och 1809 behållare.

### <a name="what-types-of-applications-can-i-deploy"></a>Vilka typer av program kan jag distribuera? 

Du kan distribuera allt som körs i behållare som passar inom de begränsningar som har lagts till i en program resurs (se ovan för mer information om kvoter). Om vi upptäcker att du använder nät för att köra illegala arbets belastningar eller grovt system (t. ex. utvinning), förbehåller vi oss rätten att avsluta dina distributioner och blockeringslistan din prenumeration från att köras på tjänsten. Kontakta oss om du har frågor om att köra en speciell arbets belastning. 

## <a name="developer-experience-issues"></a>Problem med utvecklare

### <a name="dns-resolution-from-a-container-doesnt-work"></a>DNS-matchning från en behållare fungerar inte

Utgående DNS-frågor från en behållare till Service Fabric DNS-tjänsten kan Miss lyckas under vissa omständigheter. Detta unders öks. Minimera:

- Använd Windows är Creators Update (version 1709) eller högre som din bas behållar avbildning.
- Om enbart tjänst namnet inte fungerar, försök med det fullständiga namnet: ServiceName. ApplicationName.
- I Docker-filen för din tjänst lägger du till `EXPOSE <port>` var porten är porten som du exponerar din tjänst på. Exempel:

```Dockerfile
EXPOSE 80
```

### <a name="dns-does-not-work-the-same-as-it-does-for-service-fabric-development-clusters-and-in-mesh"></a>DNS fungerar inte på samma sätt som för Service Fabric utvecklings kluster och i ett nät

Du kan behöva referera till tjänster på ett annat sätt i ditt lokala utvecklings kluster än i Azure-nät.

Använd i det lokala utvecklings klustret `{serviceName}.{applicationName}` . I Azure Service Fabric nät använder du `{servicename}` . 

Azure-nät stöder för närvarande inte DNS-matchning mellan program.

För andra kända DNS-problem med att köra ett Service Fabric utvecklings kluster i Windows 10, se: [Felsöka Windows-behållare](../service-fabric/service-fabric-how-to-debug-windows-containers.md) och [kända DNS-problem](../service-fabric/service-fabric-dnsservice.md#known-issues).

### <a name="networking"></a>Nätverk

ServiceFabric Network NAT kan försvinna när du använder appen som körs på den lokala datorn. Du kan diagnostisera om detta har hänt genom att köra följande från en kommando tolk:

`docker network ls` och observera om visas i `servicefabric_nat` listan.  Annars kör du följande kommando: `docker network create -d=nat --subnet 10.128.0.0/24 --gateway 10.128.0.1 servicefabric_nat`

Detta löser problemet även om appen redan har distribuerats lokalt och är i ett ohälsosamt tillstånd.

### <a name="issues-running-multiple-apps"></a>Problem som kör flera appar

Du kan stöta på CPU-tillgänglighet och begränsningar som är fasta för alla program. Minimera:
- Skapa ett kluster med fem noder.
- Minska CPU-användningen i tjänster över den distribuerade appen. Till exempel, i tjänstens tjänst. yaml-fil, ändra `cpu: 1.0` till `cpu: 0.5`

Det går inte att distribuera flera program till ett kluster med en nod. Minimera:
- Använd ett kluster med fem noder när du distribuerar flera appar till ett lokalt kluster.
- Ta bort appar som du inte testar för närvarande.

### <a name="vs-tooling-has-limited-support-for-windows-containers"></a>VS-verktyg har begränsat stöd för Windows-behållare

Visual Studio-verktyget stöder bara distribution av Windows-behållare med en grundläggande OS-version av Windows Server 1709 och 1803 idag. 

## <a name="feature-gaps-and-other-known-issues"></a>Funktions luckor och andra kända problem

### <a name="after-deploying-my-application-the-network-resource-associated-with-it-does-not-have-an-ip-address"></a>När du har distribuerat mitt program har nätverks resursen som är kopplad till den ingen IP-adress

Det finns ett känt problem där IP-adressen inte blir tillgänglig direkt. Kontrol lera nätverks resursens status om några minuter för att se den associerade IP-adressen.

### <a name="my-application-fails-to-access-the-right-networkvolume-resource"></a>Mitt program har inte åtkomst till rätt nätverks-/volym resurs

I din program modell använder du det fullständiga resurs-ID: t för nätverk och volymer för att kunna komma åt den associerade resursen. Här är ett exempel från snabb starts exemplet:

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

### <a name="when-i-scale-out-all-of-my-containers-are-affected-including-running-ones"></a>När jag skalar ut påverkas alla mina behållare, inklusive de som körs

Detta är ett fel och en korrigering implementeras.

## <a name="next-steps"></a>Nästa steg

Läs mer om att Service Fabric nät i [översikten](service-fabric-mesh-overview.md).
