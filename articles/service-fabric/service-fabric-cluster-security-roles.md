---
title: 'Service Fabric kluster säkerhet: klient roller'
description: Den här artikeln beskriver de två klient rollerna och de behörigheter som har angetts för rollerna.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: abca19e686d39338fcaa2e0b0c8126913135170b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "75451901"
---
# <a name="role-based-access-control-for-service-fabric-clients"></a>Rollbaserad åtkomst kontroll för Service Fabric klienter
Azure Service Fabric har stöd för två olika åtkomst kontroll typer för klienter som är anslutna till ett Service Fabric-kluster: administratör och användare. Med åtkomst kontroll kan kluster administratören begränsa åtkomsten till vissa kluster åtgärder för olika grupper av användare, vilket gör klustret säkrare.  

**Administratörer** har fullständig åtkomst till hanterings funktioner (inklusive Läs-och skriv funktioner). Som standard har **användare** bara Läs behörighet till hanterings funktioner (till exempel fråge funktioner) och möjligheten att lösa program och tjänster.

Du anger de två klient rollerna (administratör och klient) när klustret skapas genom att tillhandahålla separata certifikat för var och en. Mer information om hur du konfigurerar ett säkert Service Fabric kluster finns i [Service Fabric kluster säkerhet](service-fabric-cluster-security.md) .

## <a name="default-access-control-settings"></a>Standard inställningar för åtkomst kontroll
Åtkomst kontroll typen administratör har fullständig åtkomst till alla FabricClient-API: er. Den kan utföra alla läsningar och skrivningar på Service Fabric klustret, inklusive följande åtgärder:

### <a name="application-and-service-operations"></a>Program-och tjänst åtgärder
* **CreateService**: skapa tjänst                             
* **CreateServiceFromTemplate**: skapa tjänst från mall                             
* **UpdateService**: tjänst uppdateringar                             
* **DeleteService**: borttagning av tjänst                             
* **ProvisionApplicationType**: etablering av program typ                             
* **CreateApplication**: skapa program                               
* **DeleteApplication**: program borttagning                             
* **UpgradeApplication**: startar eller avbryter program uppgraderingar                             
* **UnprovisionApplicationType**: avetablering av program typ                             
* **MoveNextUpgradeDomain**: återupptar program uppgraderingar med en explicit uppdaterings domän                             
* **ReportUpgradeHealth**: återupptar program uppgraderingar med den aktuella uppgraderings processen                             
* **ReportHealth**: rapporterings hälsa                             
* **PredeployPackageToNode**: API för för distribution                            
* **CodePackageControl**: startar om kod paket                             
* **RecoverPartition**: återställer en partition                             
* **RecoverPartitions**: återställer partitioner                             
* **RecoverServicePartitions**: återställer diskpartitioner                             
* **RecoverSystemPartitions**: återställer system tjänst partitioner                             

### <a name="cluster-operations"></a>Klusteråtgärder
* **ProvisionFabric**: MSI och/eller kluster manifest etablering                             
* **UpgradeFabric**: startar kluster uppgraderingar                             
* **UnprovisionFabric**: MSI och/eller kluster manifest avetablerar                         
* **MoveNextFabricUpgradeDomain**: återupptar kluster uppgraderingar med en explicit uppdaterings domän                             
* **ReportFabricUpgradeHealth**: återupptar kluster uppgraderingar med den aktuella uppgraderings processen                             
* **StartInfrastructureTask**: startar infrastruktur uppgifter                             
* **FinishInfrastructureTask**: Slutför infrastruktur uppgifter                             
* **InvokeInfrastructureCommand**: kommandon för hantering av infrastruktur aktiviteter                              
* **ActivateNode**: aktiverar en nod                             
* **DeactivateNode**: inaktivera en nod                             
* **DeactivateNodesBatch**: inaktivera flera noder                             
* **RemoveNodeDeactivations**: återställer inaktive ring på flera noder                             
* **GetNodeDeactivationStatus**: kontrollerar status för inaktive ring                             
* **NodeStateRemoved**: rapporterar nod-tillstånd borttaget                             
* **ReportFault**: rapporterings fel                             
* **FileContent**: klient fil överföring i bild arkiv (extern till kluster)                             
* **FileDownload**: hämtnings initiering av klient fil för avbildnings lagring (extern till kluster)                             
* **InternalList**: klient fil List åtgärd för avbildnings lager (intern)                             
* **Ta bort**: klient borttagnings åtgärd för avbildnings lager                              
* **Ladda upp**: klient överförings åtgärd i Image Store                             
* **NodeControl**: starta, stoppa och starta om noder                             
* **MoveReplicaControl**: flyttar repliker från en nod till en annan                             

### <a name="miscellaneous-operations"></a>Diverse åtgärder
* **Ping**: klient-pingar                             
* **Fråga**: alla frågor tillåts
* **NameExists**: det finns kontroller för NAMNGIVNING av URI                             

Typen av användar åtkomst kontroll är som standard begränsad till följande åtgärder: 

* **EnumerateSubnames**: NAMNGIVNING av URI-uppräkning                             
* **EnumerateProperties**: namnge egenskaps uppräkning                             
* **PropertyReadBatch**: namngivnings egenskapen Läs åtgärder                             
* **GetServiceDescription**: tjänster för lång avsöknings tjänst meddelanden och Läs tjänst beskrivningar                             
* **ResolveService**: klagomål-baserad tjänst upplösning                             
* **ResolveNameOwner**: matcha namngivnings-URI-ägare                             
* **ResolvePartition**: lösa system tjänster                             
* **ServiceNotifications**: Event-baserad tjänst aviseringar                             
* **GetUpgradeStatus**: avsökning av status för program uppgradering                             
* **GetFabricUpgradeStatus**: avsöknings status för kluster uppgradering                             
* **InvokeInfrastructureQuery**: fråga infrastruktur uppgifter                             
* **Lista**: åtgärd för klient fil lista för avbildnings lager                             
* **ResetPartitionLoad**: återställer belastning för en enhet för växling vid fel                             
* **ToggleVerboseServicePlacementHealthReporting**: växla utförlig hälso rapportering för tjänst placering                             

Administratörs åtkomst kontrollen har även åtkomst till föregående åtgärder.

## <a name="changing-default-settings-for-client-roles"></a>Ändra standardinställningarna för klient roller
I kluster manifest filen kan du ge administratörs funktioner till klienten om det behövs. Du kan ändra standardinställningarna genom att gå till alternativet för **infrastruktur inställningar** när [klustret skapas](service-fabric-cluster-creation-via-portal.md)och ange föregående inställningar i fälten **namn**, **admin**, **användare** och **värde** .

## <a name="next-steps"></a>Nästa steg
[Service Fabric kluster säkerhet](service-fabric-cluster-security.md)

[Service Fabric skapa kluster](service-fabric-cluster-creation-via-portal.md)

