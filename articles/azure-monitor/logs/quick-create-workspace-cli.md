---
title: Skapa en Log Analytics-arbetsyta med hjälp av Azure CLI | Microsoft Docs
description: Lär dig hur du skapar en Log Analytics arbets yta för att aktivera hanterings lösningar och data insamling från molnet och lokala miljöer med Azure CLI.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: 8e1fc816e32e563161e1604bdcd7a7006353e4ed
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047393"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>Skapa en Log Analytics arbets yta med Azure CLI 2,0

Azure CLI 2.0 används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här snabb starten visar hur du använder Azure CLI 2,0 för att distribuera en Log Analytics arbets yta i Azure Monitor. En Log Analytics-arbetsyta är en unik miljö för Azure Monitor loggdata. Varje arbets yta har sin egen data lagrings plats och konfiguration, och data källor och lösningar har kon figurer ATS för att lagra data i en viss arbets yta. Du behöver en Log Analytics arbets yta om du vill samla in data från följande källor:

* Azure-resurser i din prenumeration  
* Lokala datorer som övervakas av System Center Operations Manager  
* Enhets samlingar från Configuration Manager  
* Diagnostik- eller loggdata från Azure Storage  

För andra källor, till exempel virtuella Azure-datorer och virtuella Windows-eller Linux-datorer i din miljö, se följande avsnitt:

* [Samla in data från virtuella Azure-datorer](../vm/quick-collect-azurevm.md)
* [Samla in data från hybrid Linux-datorer](../vm/quick-collect-linux-computer.md)
* [Samla in data från hybrid Windows-dator](../vm/quick-collect-windows-computer.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.30 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-workspace"></a>Skapa en arbetsyta
Skapa en arbets yta med [AZ distributions grupp skapa](/cli/azure/deployment/group#az_deployment_group_create). I följande exempel skapas en arbets yta på den *östra* platsen med hjälp av en Resource Manager-mall från den lokala datorn. JSON-mallen har kon figurer ATS för att bara uppmana dig att ange namnet på arbets ytan och anger ett standardvärde för de andra parametrarna som skulle kunna användas som standard konfiguration i din miljö. Eller så kan du lagra mallen i ett Azure Storage-konto för delad åtkomst i din organisation. Mer information om hur du arbetar med mallar finns i [distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Information om regioner som stöds finns i [regioner Log Analytics finns i](https://azure.microsoft.com/regions/services/) och söka efter Azure Monitor från fältet **Sök efter ett produkt** .

Följande parametrar anger ett standardvärde:

* plats – standardvärdet för USA, östra
* SKU – standardvärdet för den nya pris nivån per GB som lanseras i pris sättnings modellen från april 2018

>[!WARNING]
>Om du skapar eller konfigurerar en Log Analytics arbets yta i en prenumeration som har valt att ha en ny pris modell på april 2018 är den enda giltiga Log Analytics pris nivån **PerGB2018**.
>

### <a name="create-and-deploy-template"></a>Skapa och distribuera mall

1. Kopiera och klistra in följande JSON-syntax i filen:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
          }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```

2. Redigera mallen så att den uppfyller dina krav. Granska [mallen Microsoft. OperationalInsights/arbetsytes](/azure/templates/microsoft.operationalinsights/2015-11-01-preview/workspaces) för att lära dig vilka egenskaper och värden som stöds.
3. Spara den här filen som **deploylaworkspacetemplate.jspå** en lokal mapp.   
4. Nu är det dags att distribuera den här mallen. Använd följande kommandon från mappen som innehåller mallen. När du uppmanas att ange ett namn på arbets ytan anger du ett namn som är globalt unikt för alla Azure-prenumerationer.

    ```azurecli
    az deployment group create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

Det kan ta några minuter att slutföra distributionen. När det är klart visas ett meddelande som liknar följande som innehåller resultatet:

![Exempel på resultat när distributionen är klar](media/quick-create-workspace-cli/template-output-01.png)

## <a name="troubleshooting"></a>Felsökning
När du skapar en arbets yta som har tagits bort under de senaste 14 dagarna och i [läget för mjuk borttagning](../logs/delete-workspace.md#soft-delete-behavior)kan åtgärden ha olika resultat beroende på konfigurationen för arbets ytan:
1. Om du anger samma namn på arbets ytan, resurs gruppen, prenumerationen och regionen som i den borttagna arbets ytan återställs din arbets yta, inklusive dess data, konfiguration och anslutna agenter.
2. Om du använder samma arbets ytans namn, men en annan resurs grupp, prenumeration eller region, får du ett fel meddelande om att arbets ytans namn *är inte unikt* eller *i konflikt*. Om du vill åsidosätta den mjuka borttagningen och permanent ta bort arbets ytan och skapa en ny arbets yta med samma namn, följer du dessa steg för att återställa arbets ytan och utföra permanent borttagning:
   * [Återställa](../logs/delete-workspace.md#recover-workspace) din arbetsyta
   * [Ta bort arbetsytan permanent](../logs/delete-workspace.md#permanent-workspace-delete)
   * Skapa en ny arbets yta med samma arbets ytans namn

## <a name="next-steps"></a>Nästa steg
Nu när du har en arbets yta tillgänglig kan du konfigurera insamling av övervakning, köra loggs ökningar för att analysera dessa data och lägga till en hanterings lösning för att tillhandahålla ytterligare data och analytiska insikter.  

* Information om hur du aktiverar data insamling från Azure-resurser med Azure-diagnostik eller Azure Storage finns i [samla in Azure Service-loggar och mått för användning i Log Analytics](../essentials/resource-logs.md#send-to-log-analytics-workspace).  
* Lägg till [System Center Operations Manager som en data källa](../agents/om-agents.md) för att samla in data från agenter som rapporterar din Operations Manager hanterings grupp och lagra den i din Log Analytics-arbetsyta.  
* Anslut [Configuration Manager](../logs/collect-sccm.md) för att importera datorer som är medlemmar i samlingar i hierarkin.  
* Granska de tillgängliga [övervaknings lösningarna](../insights/solutions.md) och Lägg till eller ta bort en lösning från din arbets yta.

