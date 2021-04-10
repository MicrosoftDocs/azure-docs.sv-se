---
title: Övervaka och diagnostisera Windows-behållare
description: I den här självstudien konfigurerar du Azure Monitor loggar för övervakning och diagnostik av Windows-behållare på Azure Service Fabric.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: b7689d6e259055137a8d1d3c61552790ab9f28d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100588234"
---
# <a name="tutorial-monitor-windows-containers-on-service-fabric-using-azure-monitor-logs"></a>Självstudie: övervaka Windows-behållare på Service Fabric med hjälp av Azure Monitor loggar

Detta är del tre i en själv studie kurs och vägleder dig genom att konfigurera Azure Monitor loggar för att övervaka Windows-behållare som dirigeras på Service Fabric.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera Azure Monitor loggar för ditt Service Fabric-kluster
> * Använda en Log Analytics-arbetsyta till att visa och fråga loggar från containrar och noder
> * Konfigurera Log Analytics-agenten så att containrar och nodvärden hämtas in

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar de här självstudierna bör du:

* ha ett kluster i Azure, eller [skapa ett via den här självstudiekursen](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Distribuera ett program i en container till det](service-fabric-host-app-in-a-container.md).

## <a name="setting-up-azure-monitor-logs-with-your-cluster-in-the-resource-manager-template"></a>Konfigurera Azure Monitor loggar med ditt kluster i Resource Manager-mallen

Om du använde [mallen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) i den första delen av självstudiekursen bör den omfatta följande tillägg till en allmän Service Fabric Azure Resource Manager-mall. Om du har ett eget kluster som du vill konfigurera för övervakning av behållare med Azure Monitor loggar:

* göra följande ändringar i Resource Manager-mallen
* distribuera med PowerShell för att uppgradera klustret genom att [distribuera mallen](./service-fabric-cluster-creation-via-arm.md). Azure Resource Manager ser att resursen finns, så den lanseras som en uppgradering.

### <a name="adding-azure-monitor-logs-to-your-cluster-template"></a>Lägga till Azure Monitor loggar i kluster mal len

Gör följande ändringar i *template.json*:

1. Lägga till Log Analytics-arbetsytan, position och namn, i avsnittet *parametrar*:

    ```json
    "omsWorkspacename": {
      "type": "string",
      "defaultValue": "[toLower(concat('sf',uniqueString(resourceGroup().id)))]",
      "metadata": {
        "description": "Name of your Log Analytics Workspace"
      }
    },
    "omsRegion": {
      "type": "string",
      "defaultValue": "East US",
      "allowedValues": [
        "West Europe",
        "East US",
        "Southeast Asia"
      ],
      "metadata": {
        "description": "Specify the Azure Region for your Log Analytics workspace"
      }
    }
    ```

    Om du vill ändra något av värdena lägger du till samma parametrar i *template.parameters.json* och ändrar värdena som används där.

2. Lägg till lösningsnamnet och lösningen i dina *variabler*:

    ```json
    "omsSolutionName": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "omsSolution": "ServiceFabric"
    ```

3. Lägg till Microsoft Monitoring Agent som ett tillägg för virtuella datorer. Hitta resurs för skalnings uppsättningar för virtuella datorer: *resurser*  >  *"API version": "[Variables (' vmssApiVersion ')]"*. Under *egenskaperna*  >  *virtualMachineProfile*  >  *extensionProfile*  >  *Extensions* lägger du till följande tilläggs Beskrivning under *ServiceFabricNode* -tillägget: 
    
    ```json
    {
        "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
        "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
            },
            "protectedSettings": {
                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
            }
        }
    },
    ```

4. Lägg till Log Analytics-arbetsytan som enskild resurs. I *resources*, efter resursen för skalningsuppsättningar för virtuella datorer, lägger du till följande:

    ```json
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "name": "System",
                "type": "datasources",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsEvent",
                "properties": {
                    "eventLogName": "System",
                    "eventTypes": [
                        {
                            "eventType": "Error"
                        },
                        {
                            "eventType": "Warning"
                        },
                        {
                            "eventType": "Information"
                        }
                    ]
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('omsSolutionName')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('omsSolutionName')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('omsSolution'))]",
            "promotionCode": ""
        }
    },
    ```

[Här](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/d2ffa318581fc23ac7f1b0ab2b52db1a0d7b4ba7/5-VM-Windows-OMS-UnSecure/sfclusteroms.json) är en exempelmall (används i del 1 i den här guiden). I den finns alla ändringarna, att referera till vid behov. De här ändringarna lägger till Log Analytics-arbetsytan i resursgruppen. Arbetsytan konfigureras så att den hämtar upp Service Fabric-plattformshändelser från lagringstabeller som har konfigurerats med [Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md)-agenten. Log Analytics-agenten (Microsoft Monitoring Agent) har också lagts till i varje nod i klustret som ett tillägg för virtuella datorer. Det innebär att agenten konfigureras automatiskt på varje dator och kopplas till samma arbetsyta när du skalar klustret.

Distribuera mallen med dina ändringar för att uppgradera det aktuella klustret. Du bör se Log Analytics-resurserna i resurs gruppen när detta har slutförts. När klustret är klart kan du distribuera ditt containerprogram till det. I nästa steg ställer vi in containerövervakning.

## <a name="add-the-container-monitoring-solution-to-your-log-analytics-workspace"></a>Lägga till övervakningslösning för containrar i Log Analytics-arbetsytan

När du vill konfigurera containerlösningen i arbetsytan söker du efter *Övervakningslösning för container* och skapar en containerresurs (i kategorin Övervakning och hantering).

![Lägga till containerlösning](./media/service-fabric-tutorial-monitoring-wincontainers/containers-solution.png)

När du uppmanas till *arbets ytan Log Analytics* väljer du den arbets yta som skapades i resurs gruppen och väljer **skapa**. Detta lägger till en *lösning för övervakning av behållare* till din arbets yta, som initierar den Log Analytics agent som distribueras av mallen för att börja samla in Docker-loggar och statistik.

Navigera tillbaka till *resursgruppen*. Du bör nu se den nyligen tillagda övervakningslösningen. Om du väljer det ska landnings sidan Visa dig antalet behållar avbildningar som du har kört.

*Observera att vi körde fem instanser av Fabrikam-behållaren från [del två](service-fabric-host-app-in-a-container.md) i självstudien*

![Landningssida för containerlösning](./media/service-fabric-tutorial-monitoring-wincontainers/solution-landing.png)

Genom att välja **container Monitor-lösningen** går du till en mer detaljerad instrument panel, som gör att du kan bläddra igenom flera paneler och köra frågor i Azure Monitor loggar.

Eftersom agenten plockar upp dockerloggar är standardinställningen att *stdout* och *stderr* visas. Om du rullar horisontellt ser du behållar avbildnings inventering, status, mått och exempel frågor som du kan köra för att få mer användbara data.

![Instrumentpanel för containerlösning](./media/service-fabric-tutorial-monitoring-wincontainers/container-metrics.png)

Om du klickar på någon av dessa paneler tas du till den Kusto-fråga som genererar det visade värdet. Ändra frågan till *\** om du vill se alla de olika typerna av loggar som hämtas. Härifrån kan du fråga eller filtrera efter containrarnas prestanda och loggar och titta på händelser för Service Fabric-plattformen. Agenterna avger dessutom ständigt pulsslag från varje nod. Du kan ta en titt på dem och kontrollera att data fortfarande samlas in från alla datorer om klusterkonfigurationen ändras.

![Containerfråga](./media/service-fabric-tutorial-monitoring-wincontainers/query-sample.png)

## <a name="configure-log-analytics-agent-to-pick-up-performance-counters"></a>Konfigurera Log Analytics-agenten för att hämta prestandaräknare

En annan fördel med att använda Log Analytics-agenten är möjligheten att ändra de prestanda räknare som du vill hämta via UI-gränssnittet i Log Analytics, i stället för att konfigurera Azure Diagnostics-agenten och göra en Resource Manager-mall baserad uppgradering varje gång. Det gör du genom att välja på **OMS-arbetsyta** på landnings sidan för din behållar övervakning (eller Service Fabric) lösning.

Det leder till Log Analytics-arbetsytan där du kan se dina lösningar, skapa anpassade instrumentpaneler och konfigurera Log Analytics-agenten. 
* Välj **Avancerade inställningar** för att öppna menyn Avancerade inställningar.
* Välj **anslutna källor**  >  **Windows-servrar** för att kontrol lera att du har *5 anslutna Windows-datorer*.
* Välj **data**  >  **Windows-prestandaräknare** för att söka efter och lägga till nya prestanda räknare. Här visas en lista med rekommendationer från Azure Monitor loggar för prestanda räknare som du kan samla in samt alternativet för att söka efter andra räknare. Kontrollera att räknarna **Processor(_Total)\% Processortid** och **Minne (*) \Tillgängliga megabyte** samlas in.

**Uppdatera** övervakningslösningen för containrar efter ett par minuter. Du ska nu se information om *Datorprestanda* komma in. Det här hjälper dig att förstå hur dina resurser används. Du kan också använda de här måtten till att fatta rätt beslut om skalning av klustret och för att bekräfta om ett kluster balanserar ut belastningen som förväntat.

*Obs! Kontrollera tidsfiltren är inställda på rätt sätt så att du kan använda de här måtten.*

![Prestandaräknare 2](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters2.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera Azure Monitor loggar för ditt Service Fabric-kluster
> * Använda en Log Analytics-arbetsyta till att visa och fråga loggar från containrar och noder
> * Konfigurera Log Analytics-agenten så att containrar och nodvärden hämtas in

Nu när du har konfigurerat övervakning för ditt program i behållare, prova:

* Konfigurera Azure Monitor loggar för ett Linux-kluster genom att följa liknande steg som den här självstudien. Referera till [den här mallen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeType-Secure-OMS) och gör ändringar i Resource Manager-mallen.
* Konfigurera Azure Monitor loggar för att ställa in [automatiserad avisering](../azure-monitor/alerts/alerts-overview.md) för identifiering och diagnostik.
* Utforska Service Fabric-listan över [rekommenderade prestandaräknare](service-fabric-diagnostics-event-generation-perf.md) för att konfigurera klustren.
* Bekanta dig med funktionerna för [loggs ökning och frågor](../azure-monitor/logs/log-query-overview.md) som erbjuds som en del av Azure Monitor loggar.
