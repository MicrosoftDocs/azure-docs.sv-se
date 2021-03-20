---
title: Avancerad autoskalning med Azure Virtual Machines
description: 'Använder Resource Manager och VM Scale set med flera regler och profiler, som skickar e-post och anropar webhook-URL: er med skalnings åtgärder.'
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 06/25/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 26cfdf05847b01d28a8574483acc89cfeced0ffa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101717748"
---
# <a name="advanced-autoscale-configuration-using-resource-manager-templates-for-vm-scale-sets"></a>Avancerad automatisk skalnings konfiguration med Resource Manager-mallar för VM Scale Sets
Du kan skala in och skala ut i Virtual Machine Scale Sets baserat på prestanda mätnings trösklar, med ett återkommande schema eller med ett visst datum. Du kan också konfigurera e-post-och webhook-meddelanden för skalnings åtgärder. Den här genom gången visar ett exempel på hur du konfigurerar alla dessa objekt med en Resource Manager-mall i en skalnings uppsättning för virtuella datorer.

> [!NOTE]
> I den här genom gången beskrivs stegen för VM Scale Sets, samma information gäller för automatisk skalning [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [app service-Web Apps](https://azure.microsoft.com/services/app-service/web/)och [API Management-tjänster](../../api-management/api-management-key-concepts.md) för en enkel skala in/ut-inställning på en VM Scale-uppsättning baserat på ett enkelt prestanda mått som CPU, se [Linux](../../virtual-machine-scale-sets/tutorial-autoscale-cli.md) -och [Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) -dokumenten
>
>

## <a name="walkthrough"></a>Genomgång
I den här genom gången använder vi [Azure Resource Explorer](https://resources.azure.com/) för att konfigurera och uppdatera den automatiska skalnings inställningen för en skalnings uppsättning. Azure Resource Explorer är ett enkelt sätt att hantera Azure-resurser via Resource Manager-mallar. Om du är nybörjare på Azure Resource Explorer verktyget läser du [introduktionen](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/).

1. Distribuera en ny skalnings uppsättning med en grundläggande inställning för autoskalning. I den här artikeln används en från Azure snabb starts galleriet, som har en Windows-skalnings uppsättning med en grundläggande mall för autoskalning. Linux Scale set fungerar på samma sätt.
2. När du har skapat skalnings uppsättningen går du till skalnings uppsättnings resursen från Azure Resource Explorer. Du ser följande under Microsoft. Insights-noden.

    ![Azure Explorer](media/autoscale-virtual-machine-scale-sets/azure_explorer_navigate.png)

    Körningen av mallen har skapat en standard inställning för autoskalning med namnet **autoscalewad**. På den högra sidan kan du Visa den fullständiga definitionen av den här inställningen för autoskalning. I det här fallet levereras standard inställningen för autoskalning med en CPU%-baserad skalnings-och skalnings regel.  

3. Nu kan du lägga till fler profiler och regler baserat på schemat eller särskilda krav. Vi skapar en inställning för autoskalning med tre profiler. Om du vill förstå profiler och regler i autoskalning bör du gå igenom [metod tipsen för autoskalning](autoscale-best-practices.md).  

    | Profiler & regler | Beskrivning |
    |--- | --- |
    | **Profil** |**Prestanda/mått baserat** |
    | Regel |Antal Service Bus Queue-meddelande > x |
    | Regel |Antal meddelanden i Service Bus Queue < y |
    | Regel |CPU% > n |
    | Regel |CPU% < p |
    | **Profil** |**Vardags morgon timmar (inga regler)** |
    | **Profil** |**Produkt lanserings dag (inga regler)** |

4. Här är ett hypotetiskt skalnings scenario som vi använder för den här genom gången.

   * **Load-based** – jag vill skala ut eller i baserat på belastningen på mitt program som finns på min skalnings uppsättning. *
   * **Meddelande kös Tor lek** – jag använder en Service Bus kö för inkommande meddelanden till mitt program. Jag använder köns antal meddelanden och CPU% och konfigurerar en standard profil för att utlösa en skalnings åtgärd om antingen antal meddelanden eller CPU träffar tröskelvärdet.\*
   * **Tid på vecka och dag** – jag vill ha en återkommande veckas tid för den dagliga profilen med namnet "veckodag morgon timmar". Jag vet att det är bättre att ha ett visst antal VM-instanser för att hantera appens belastning under den här tiden, baserat på historiska data.\*
   * **Särskilda datum** – jag har lagt till en "produkt lanserings dag"-profil. Jag planerar före specifika datum så att mitt program kan hantera meddelanden om belastnings marknadsföring och när vi publicerar en ny produkt i programmet.\*
   * *De två sista profilerna kan också ha andra regler för prestanda mått i dem. I det här fallet valde jag att inte ha ett och i stället förlitar sig på standardvärden för prestanda mått. Reglerna är valfria för de återkommande och datumbaserade profilerna.*

     Den automatiska skalnings motorns prioritering av profiler och regler samlas också in i artikeln [metod tips](autoscale-best-practices.md) för automatisk skalning.
     En lista över vanliga mått för autoskalning finns i [vanliga mått för autoskalning](autoscale-common-metrics.md)

5. Se till att du är i **Läs-och skriv** läge i Resursläsaren

    ![Autoscalewad, standard inställning för autoskalning](media/autoscale-virtual-machine-scale-sets/autoscalewad.png)

6. Klicka på Redigera. **Ersätt** elementet "profiler" i inställningen för autoskalning med följande konfiguration:

    ![Skärm bilden visar profil elementet.](media/autoscale-virtual-machine-scale-sets/profiles.png)

    ```
    {
            "name": "Perf_Based_Scale",
            "capacity": {
              "minimum": "2",
              "maximum": "12",
              "default": "2"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 10
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 3
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 85
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 60
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          },
          {
            "name": "Weekday_Morning_Hours_Scale",
            "capacity": {
              "minimum": "4",
              "maximum": "12",
              "default": "4"
            },
            "rules": [],
            "recurrence": {
              "frequency": "Week",
              "schedule": {
                "timeZone": "Pacific Standard Time",
                "days": [
                  "Monday",
                  "Tuesday",
                  "Wednesday",
                  "Thursday",
                  "Friday"
                ],
                "hours": [
                  6
                ],
                "minutes": [
                  0
                ]
              }
            }
          },
          {
            "name": "Product_Launch_Day",
            "capacity": {
              "minimum": "6",
              "maximum": "20",
              "default": "6"
            },
            "rules": [],
            "fixedDate": {
              "timeZone": "Pacific Standard Time",
              "start": "2016-06-20T00:06:00Z",
              "end": "2016-06-21T23:59:00Z"
            }
          }
    ```
    För fält som stöds och deras värden, se [AutoScale REST API-dokumentation](/rest/api/monitor/autoscalesettings). Nu innehåller den automatiska skalnings inställningen de tre profilerna som beskrivits tidigare.

7. Titta slutligen på avsnittet om **autoskalning** . Med automatiska skalnings aviseringar kan du göra tre saker när en utskalning eller åtgärd har utlösts.
   - Meddela administratören och medadministratörerna om din prenumeration
   - E-posta en uppsättning användare
   - Utlös ett webhook-anrop. När den utlöses skickar denna webhook metadata om autoskalning-villkoret och skalnings uppsättnings resursen. Mer information om nytto lasten för autoskalning av webhook finns i [Konfigurera webhook & e-postaviseringar för autoskalning](autoscale-webhook-email.md).

   Lägg till följande i den autoskalningsinställning som ersätter ditt **meddelande** element vars värde är null

   ```
   "notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": true,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]

   ```

   Knappen tryck på **Lägg** till i Resursläsaren för att uppdatera inställningen för autoskalning.

Du har uppdaterat en inställning för autoskalning på en skalnings uppsättning för virtuella datorer som innehåller flera skalnings profiler och skalnings meddelanden.

## <a name="next-steps"></a>Nästa steg
Använd dessa länkar om du vill veta mer om autoskalning.

[Felsöka autoskalning med Virtual Machine Scale Sets](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

[Vanliga mått för autoskalning](autoscale-common-metrics.md)

[Metod tips för Azures autoskalning](autoscale-best-practices.md)

[Hantera autoskalning med PowerShell](../powershell-samples.md#create-and-manage-autoscale-settings)

[Hantera autoskalning med CLI](../cli-samples.md#autoscale)

[Konfigurera webhook & e-postaviseringar för autoskalning](autoscale-webhook-email.md)

Referens för [Microsoft. Insights/autoscalesettings-](/azure/templates/microsoft.insights/autoscalesettings) mall