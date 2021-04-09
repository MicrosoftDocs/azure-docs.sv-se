---
title: Skala automatiskt upp data flödes enheter – Azure Event Hubs | Microsoft Docs
description: Aktivera automatisk ökning på ett namn område för att automatiskt skala upp data flödes enheter.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 1be564472011622b71b3066495748dfdbe6cc791
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96020814"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Skala automatiskt upp Azure Event Hubs data flödes enheter
Azure Event Hubs är en mycket skalbar data strömnings plattform. Därför ökar Event Hubs användningen ofta när tjänsten har startats. Sådan användning kräver att fördefinierade [data flödes enheter](event-hubs-scalability.md#throughput-units) ökar för att skala Event Hubs och hantera större överföringshastigheter. Funktionen för **Automatisk** ökning i Event Hubs skalas automatiskt upp genom att öka antalet data flödes enheter för att möta användnings behoven. Ökande data flödes enheter förhindrar begränsnings scenarier där:

* Data ingångs frekvensen överskrider den angivna data flödes enheten.
* Taxan för utgående begär Anden överskrider mängd enheter för data flöde.

Event Hubs tjänsten ökar data flödet när belastningen ökar utöver minimi tröskelvärdet, utan att begär Anden som misslyckats med ServerBusy-fel uppstår.

## <a name="how-auto-inflate-works"></a>Så här fungerar allt automatiskt

Event Hubs trafik styrs av [data flödes enheter](event-hubs-scalability.md#throughput-units). Med en enda data flödes enhet kan 1 MB per sekund i ingress och dubbelt så mycket utgående. Standard händelse hubbar kan konfigureras med 1-20-dataflödes enheter. Med automatisk ökning kan du starta små med de minsta nödvändiga data flödes enheter som du väljer. Funktionen skalas sedan automatiskt till den maximala gränsen för de data flödes enheter du behöver, beroende på ökningen av trafiken. Automatiskt öknings kraftigt ger följande fördelar:

- En effektiv skalnings funktion för att starta små och skala upp när du växer.
- Skala automatiskt till den angivna övre gränsen utan begränsnings problem.
- Mer kontroll över skalning, eftersom du styr när och hur mycket som ska skalas.

## <a name="enable-auto-inflate-on-a-namespace"></a>Aktivera automatisk ökning på ett namn område

Du kan aktivera eller inaktivera automatisk ökning på en standard-nivå Event Hubs namn område genom att använda någon av följande metoder:

- [Azure Portal](https://portal.azure.com).
- En [Azure Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate).

> [!NOTE]
> Basic-nivån Event Hubs namn områden har inte stöd för automatisk ökning.

### <a name="enable-auto-inflate-through-the-portal"></a>Aktivera automatiskt ökning via portalen


#### <a name="enable-at-the-time-of-creation"></a>Aktivera vid skapande tillfället 
Du kan aktivera funktionen för automatisk ökning **när du skapar en Event Hubs namnrymd**:
 
![Aktivera automatiskt bred huvud vid skapande av händelsehubben](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

När det här alternativet är aktiverat kan du starta litet med dina data flödes enheter och skala upp när användnings behoven ökar. Den övre gränsen för inflation påverkar inte direkt prissättningen, vilket beror på antalet data flödes enheter som används per timme.

#### <a name="enable-auto-inflate-for-an-existing-event-hub"></a>Aktivera automatisk ökning för en befintlig händelsehubben
Du kan också aktivera funktionen för automatisk ökning och ändra dess inställningar med hjälp av följande anvisningar: 
 
1. På sidan **Event Hubs namn område** väljer du **inaktive rad** under autoöknings **data flödes enheter**.  

    ![Välj data flödes enheter på sidan Event Hubs namn område](./media/event-hubs-auto-inflate/select-throughput-units.png)
2. På sidan **skalnings inställningar** markerar du kryss rutan för **Aktivera** (om funktionen för autoskalning inte är aktive rad).

    ![Välj Aktivera](./media/event-hubs-auto-inflate/scale-settings.png)
3. Ange det **maximala** antalet data flödes enheter eller Använd rullnings listen för att ange värdet. 
4. valfritt Uppdatera det **minsta** antalet data flödes enheter högst upp på den här sidan. 


> [!NOTE]
> När du använder konfigurationen för automatisk ökning för att öka data flödes enheter, genererar Event Hubs tjänsten diagnostikloggar som ger dig information om varför och när data flödet har ökat. Om du vill aktivera diagnostikloggning för en Event Hub väljer du **diagnostikinställningar** på den vänstra menyn på sidan Event hub i Azure Portal. Mer information finns i [Konfigurera diagnostikloggar för en Azure Event Hub](event-hubs-diagnostic-logs.md). 

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Aktivera automatisk ökning med en Azure Resource Manager-mall

Du kan aktivera automatisk ökning under en Azure Resource Manager mall-distribution. Ange till exempel `isAutoInflateEnabled` egenskapen till **Sant** och inställd `maximumThroughputUnits` på 10. Exempel:

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

En fullständig mall finns i [skapa Event Hubs namnrymd och aktivera bred mitt](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) på GitHub.


## <a name="next-steps"></a>Nästa steg

Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Översikt över Event Hubs](./event-hubs-about.md)
