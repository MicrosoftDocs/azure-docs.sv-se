---
title: Hantera Service Fabric kluster uppgraderingar
description: Hantera när och hur Service Fabric kluster körningen uppdateras
ms.topic: how-to
ms.date: 03/26/2021
ms.openlocfilehash: 98c3300e5cc51c32d894397839879e25190d979b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731175"
---
# <a name="manage-service-fabric-cluster-upgrades"></a>Hantera Service Fabric kluster uppgraderingar

Ett Azure Service Fabric-kluster är en resurs som du äger, men den hanteras delvis av Microsoft. Så här hanterar du när och hur Microsoft uppdaterar ditt Azure Service Fabric-kluster.

Mer bakgrunds information om koncept och processer för kluster uppgradering finns i [uppgradera och uppdatera Azure Service Fabric-kluster](service-fabric-cluster-upgrade.md)

## <a name="set-upgrade-mode"></a>Ange uppgraderings läge

Du kan ange att klustret ska ta emot automatiska Service Fabric uppgraderingar när de släpps av Microsoft, eller så kan du manuellt välja från en lista över versioner som stöds för tillfället genom att ställa in uppgraderings läget för klustret. Detta kan göras via kontrollen *Fabric Upgrade mode* i Azure Portal eller `upgradeMode` inställningen i mallen för kluster distribution.

### <a name="azure-portal"></a>Azure Portal

Med hjälp av Azure Portal väljer du mellan automatiska eller manuella uppgraderingar när du skapar ett nytt Service Fabric-kluster.

:::image type="content" source="media/service-fabric-cluster-upgrade/portal-new-cluster-upgrade-mode.png" alt-text="Välj mellan automatiska eller manuella uppgraderingar när du skapar ett nytt kluster i Azure Portal från avancerade alternativ":::

Du kan också växla mellan automatiska eller manuella uppgraderingar från avsnittet **Fabric-uppgraderingar** i en befintlig kluster resurs.

:::image type="content" source="./media/service-fabric-cluster-upgrade/fabric-upgrade-mode.png" alt-text="Välj automatiska eller manuella uppgraderingar i avsnittet &quot;Fabric-uppgraderingar&quot; i kluster resursen i Azure Portal":::

### <a name="manual-upgrades-with-azure-portal"></a>Manuella uppgraderingar med Azure Portal

När du väljer alternativet för manuell uppgradering är allt som krävs för att initiera en uppgradering att välja i list rutan tillgängliga versioner och sedan *Spara*. Därifrån kommer kluster uppgraderingen att inaktive ras omedelbart.

[Kluster hälso principerna](#custom-policies-for-manual-upgrades) (en kombination av nods hälsa och hälso tillståndet alla program som körs i klustret) följer under uppgraderingen. Om kluster hälso principer inte uppfylls kommer uppgraderingen att återställas.

När du har åtgärdat problemen som resulterade i återställningen måste du starta uppgraderingen igen, genom att följa samma steg som ovan.

### <a name="resource-manager-template"></a>Resource Manager-mall

Om du vill ändra kluster uppgraderings läget med en Resource Manager-mall anger du antingen *Automatisk* eller *manuell* för  `upgradeMode` egenskapen för resurs definitionen *Microsoft. ServiceFabric/Clusters* . Om du väljer manuella uppgraderingar anger du även `clusterCodeVersion` en [infrastruktur version som stöds](#query-for-supported-cluster-versions)för närvarande.

:::image type="content" source="./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG" alt-text="Skärm bild som visar en mall, som är i klartext indraget för att återspegla strukturen. Egenskaperna ' clusterCodeVersion ' och ' upgradeMode ' är markerade.":::

När mallen har distribuerats kommer ändringar i kluster uppgraderings läget att tillämpas. Om klustret är i manuellt läge startar kluster uppgraderingen automatiskt.

[Kluster hälso principerna](#custom-policies-for-manual-upgrades) (en kombination av nods hälsa och hälso tillståndet alla program som körs i klustret) följer under uppgraderingen. Om kluster hälso principer inte uppfylls, återställs uppgraderingen.

När du har åtgärdat problemen som resulterade i återställningen måste du starta uppgraderingen igen, genom att följa samma steg som ovan.

## <a name="wave-deployment-for-automatic-upgrades"></a>Wave-distribution för automatiska uppgraderingar

Med läget för automatisk uppgradering har du möjlighet att aktivera klustret för Wave-distribution. Med Wave-distribution kan du skapa en pipeline för att uppgradera dina test-, Stadium-och produktions kluster i sekvenser, åtskilda med inbyggd "bageri tid" för att validera kommande Service Fabric-versioner innan dina produktions kluster uppdateras.

### <a name="enable-wave-deployment"></a>Aktivera våg distribution

> [!NOTE]
> Wave-distributionen kräver `2020-12-01-preview` API-versionen (eller senare) för din *Microsoft. ServiceFabric/Clusters-* resurs.

För att aktivera Wave-distribution för automatisk uppgradering ska du först avgöra vilken Wave som ska tilldelas klustret:

* **Wave 0** ( `Wave0` ): kluster uppdateras så snart som en ny Service Fabric version lanseras. Avsett för test-/dev-kluster.
* **Våg 1** ( `Wave1` ): kluster uppdateras en vecka (sju dagar) efter att en ny version har släppts. Avsett för för produktions-och mellanlagrings kluster.
* **Wave 2** ( `Wave2` ): kluster uppdateras två veckor (14 dagar) efter att en ny version har släppts. Avsedd för produktions kluster.

Lägg sedan bara till en `upgradeWave` egenskap i kluster resurs mal len med någon av de våg värden som anges ovan. Se till att klustrets resurs-API-version är `2020-12-01-preview` eller senare.

```json
{
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
     ...
        "fabricSettings": [...],
        "managementEndpoint": ...,
        "nodeTypes": [...],
        "provisioningState": ...,
        "reliabilityLevel": ...,
        "upgradeMode": "Automatic",
        "upgradeWave": "Wave1",
       ...
```

När du har distribuerat den uppdaterade mallen registreras klustret i den angivna vågen för nästa uppgraderings period och efter det.

Du kan [Registrera dig för e-postaviseringar](#register-for-notifications) med länkar för att få hjälp om uppgraderingen av klustret Miss lyckas.

### <a name="register-for-notifications"></a>Registrera sig för meddelanden

Du kan registrera dig för meddelanden när det inte går att uppgradera kluster. Ett e-postmeddelande skickas till din angivna e-postadress med ytterligare information om uppgraderings fel och länkar till ytterligare hjälp.

> [!NOTE]
> Registrering i Wave-distribution krävs inte för att ta emot meddelanden om uppgraderings fel.

Om du vill registrera dig i meddelanden lägger du till ett `notifications` avsnitt i kluster resurs mal len och anger en eller flera e-postadresser (*mottagare*) för att ta emot meddelanden:

```json
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
     ...
        "upgradeMode": "Automatic",
        "upgradeWave": "Wave1",
        "notifications": [
        {
            "isEnabled": true,
            "notificationCategory": "WaveProgress",
            "notificationLevel": "Critical",
            "notificationTargets": [
            {
                "notificationChannel": "EmailUser",
                "receivers": [
                    "devops@contoso.com"
                ]
            }]
        }]
```

När du har distribuerat den uppdaterade mallen kommer du att registreras för meddelanden om uppgraderings problem.

## <a name="custom-policies-for-manual-upgrades"></a>Anpassade principer för manuella uppgraderingar

Du kan ange anpassade hälso principer för manuella kluster uppgraderingar. Dessa principer används varje gång du väljer en ny körnings version, som utlöser systemet för att starta uppgraderingen av klustret. Om du inte åsidosätter principerna används standardvärdena.

Du kan ange anpassade hälso principer eller granska de aktuella inställningarna under avsnittet **infrastruktur uppgraderingar** i kluster resursen i Azure Portal genom att välja *anpassat* alternativ för **uppgraderings princip**.

:::image type="content" source="./media/service-fabric-cluster-upgrade/custom-upgrade-policy.png" alt-text="Välj alternativet Anpassad uppgraderings princip i avsnittet infrastruktur uppgraderingar i kluster resursen i Azure Portal för att ange anpassade hälso principer under uppgraderingen":::

## <a name="query-for-supported-cluster-versions"></a>Fråga om kluster versioner som stöds

Du kan använda [Azure REST API](/rest/api/azure/) för att visa en lista över alla tillgängliga Service Fabric runtime-versioner ([clusterVersions](/rest/api/servicefabric/sfrp-api-clusterversions_list)) som är tillgängliga för den angivna platsen och din prenumeration.

Du kan också referera till [Service Fabric-versioner](service-fabric-versions.md) för mer information om versioner som stöds och operativ system.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2018-02-01

"value": [
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
    "name": "5.0.1427.9490",
    "type": "Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "5.0.1427.9490",
      "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
      "environment": "Windows"
    }
  },
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
    "name": "5.1.1427.9490",
    "type": " Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "5.1.1427.9490",
      "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
      "environment": "Windows"
    }
  },
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
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

`supportExpiryUtc`I utmatnings rapporterna när en specifik version upphör att gälla eller har upphört att gälla. De senaste versionerna kommer inte att ha ett giltigt datum, utan värdet *9999-12-31T23:59:59.9999999*, vilket innebär att utgångs datumet inte har angetts än.


## <a name="next-steps"></a>Nästa steg

* [Hantera Service Fabric uppgraderingar](service-fabric-cluster-upgrade-version-azure.md)
* Anpassa [Service Fabric kluster inställningar](service-fabric-cluster-fabric-settings.md)
* [Skala ditt kluster in och ut](service-fabric-cluster-scale-in-out.md)
* Lär dig mer om [program uppgraderingar](service-fabric-application-upgrade.md)


<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
