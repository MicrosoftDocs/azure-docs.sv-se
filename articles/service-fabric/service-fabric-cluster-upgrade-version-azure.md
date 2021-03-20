---
title: Uppgradera ett klusters Azure Service Fabric-version
description: Uppgradera Service Fabric koden och/eller konfigurationen som kör ett Service Fabric kluster, inklusive inställning av kluster uppdaterings läge, uppgradera certifikat, lägga till program portar, utföra OS-uppdateringar och så vidare. Vad kan du förväntar dig när uppgraderingen utförs?
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: 01fe916f0ee78c8481ac6b17b8f7409b47c852ee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90564295"
---
# <a name="upgrade-the-service-fabric-version-of-a-cluster"></a>Uppgradera Service Fabric-versionen av ett kluster

För alla moderna system är det viktigt att utforma för att kunna uppnå långsiktig framgång för din produkt. Ett Azure Service Fabric-kluster är en resurs som du äger, men som hanteras delvis av Microsoft. Den här artikeln beskriver hur du uppgraderar den version av Service Fabric som körs i ditt Azure-kluster.

Du kan ange att klustret ska ta emot automatiska Fabric-uppgraderingar när de släpps av Microsoft, eller så kan du välja en infrastruktur version som stöds som du vill att klustret ska vara på.

Du gör detta genom att ange kluster konfigurationen "upgradeMode" på portalen eller använda Resource Manager vid tidpunkten för skapandet eller senare i ett aktivt kluster 

> [!NOTE]
> Se till att låta klustret köra en infrastruktur version som stöds alltid. När vi meddelar lanseringen av en ny version av Service Fabric, markeras den tidigare versionen för slut för ande av support efter minst 60 dagar från det datumet. De nya versionerna visas [i Service Fabric-teamets blogg](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric). Den nya versionen är tillgänglig och kan väljas. 
> 
> 

14 dagar före utgången av den version som klustret körs på, genereras en hälso händelse som placerar klustret i ett varnings hälso tillstånd. Klustret är i ett varnings tillstånd tills du uppgraderar till en infrastruktur version som stöds.

## <a name="set-the-upgrade-mode-in-the-azure-portal"></a>Ange uppgraderings läget i Azure Portal
Du kan ställa in klustret på automatisk eller manuell när du skapar klustret.

![Skärm bild som visar fönstret Skapa Service Fabric kluster med alternativet 2 kluster konfiguration valt och fönstret kluster konfiguration öppnas.][Create_Manualmode]

Du kan ställa in klustret på automatisk eller Manuell inloggning i ett Live-kluster med hjälp av funktionen Hantera upplevelse. 

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Uppgradera till en ny version på ett kluster som är inställt på manuellt läge via portalen.
Om du vill uppgradera till en ny version behöver du bara välja den tillgängliga versionen i list rutan och spara. Fabric-uppgraderingen startar automatiskt. Kluster hälso principerna (en kombination av nods hälsa och hälso tillståndet alla program som körs i klustret) följer under uppgraderingen.

Om klustrets hälso principer inte uppfylls, återställs uppgraderingen. Rulla ned det här dokumentet om du vill läsa mer om hur du ställer in de anpassade hälso principerna. 

När du har åtgärdat problemen som resulterade i återställningen måste du starta uppgraderingen igen, genom att följa samma steg som ovan.

![Skärm bild som visar fönstret Service Fabric kluster med fönstret Fabric-uppgraderingar öppna och uppgraderings alternativen är markerade, inklusive automatisk och manuell.][Manage_Automaticmode]

## <a name="set-the-upgrade-mode-using-a-resource-manager-template"></a>Ange uppgraderings läget med en Resource Manager-mall
Lägg till konfigurationen "upgradeMode" i resurs definitionen Microsoft. ServiceFabric/Clusters och ange "clusterCodeVersion" till någon av de infrastruktur versioner som stöds enligt nedan och distribuera sedan mallen. Giltiga värden för "upgradeMode" är "Manuell" eller "automatisk"

![Skärm bild som visar en mall, som är i klartext indraget för att återspegla strukturen och clusterCodeVersion och upgradeMode är markerade.][ARMUpgradeMode]

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Uppgradera till en ny version på ett kluster som är inställt på manuellt läge via en Resource Manager-mall.
När klustret är i manuellt läge, för att uppgradera till en ny version, ändrar du "clusterCodeVersion" till en version som stöds och distribuerar den. Distribution av mallen, som används för att starta om Fabric-uppgraderingen, inaktive ras automatiskt. Kluster hälso principerna (en kombination av nods hälsa och hälso tillståndet alla program som körs i klustret) följer under uppgraderingen.

Om klustrets hälso principer inte uppfylls, återställs uppgraderingen.  

När du har åtgärdat problemen som resulterade i återställningen måste du starta uppgraderingen igen, genom att följa samma steg som ovan.

## <a name="set-custom-health-polices-for-upgrades"></a>Ange anpassade hälso principer för uppgraderingar
Du kan ange anpassade hälso principer för uppgradering av infrastrukturen. Om du har konfigurerat klustret till automatiska Fabric-uppgraderingar, kommer dessa principer att tillämpas på [fas 1 av de automatiska Fabric-uppgraderingarna](service-fabric-cluster-upgrade.md#fabric-upgrade-behavior-during-automatic-upgrades).
Om du har ställt in klustret för manuella Fabric-uppgraderingar, kommer dessa principer att tillämpas varje gång du väljer en ny version som aktiverar systemet för att starta uppgraderingen av Fabric i klustret. Om du inte åsidosätter principerna används standardvärdena.

Du kan ange anpassade hälso principer eller granska de aktuella inställningarna på bladet "Fabric Upgrade" genom att välja avancerade uppgraderings inställningar. Läs följande bild om hur du gör. 

![Hantera anpassade hälso principer][HealthPolices]

## <a name="list-all-available-versions-for-all-environments-for-a-given-subscription"></a>Visa en lista över alla tillgängliga versioner för alla miljöer för en specifik prenumeration
Kör följande kommando och hämta utdata som liknar detta.

"supportExpiryUtc" visar när en specifik version upphör att gälla eller har upphört att gälla. Den senaste versionen har inte något giltigt datum-det har värdet "9999-12-31T23:59:59.9999999", vilket innebär att utgångs datumet inte har angetts än.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2016-09-01

Example: https://management.azure.com/subscriptions/1857f442-3bce-4b96-ad95-627f76437a67/providers/Microsoft.ServiceFabric/locations/eastus/clusterVersions?api-version=2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }
```

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du anpassar några av [inställningarna för Service Fabric-klustrets infrastruktur resurser](service-fabric-cluster-fabric-settings.md)
* Lär dig hur du [skalar upp och ut ditt kluster](service-fabric-cluster-scale-in-out.md)
* Lär dig mer om [program uppgraderingar](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
