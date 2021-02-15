---
title: 'ML Studio (klassisk): PowerShell-moduler – Azure'
description: Använd PowerShell för att skapa och hantera Azure Machine Learning Studio (klassiska) arbets ytor, experiment, webb tjänster och mycket annat.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 04/25/2019
ms.openlocfilehash: 684299d61ba6e9e27e16a162c9f226a7ea3b5f58
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518019"
---
# <a name="powershell-modules-for-azure-machine-learning-studio-classic"></a>PowerShell-moduler för Azure Machine Learning Studio (klassisk)

**gäller för:** ![ Gäller för. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klassisk) ![ gäller inte för.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Med hjälp av PowerShell-moduler kan du hantera dina Studio (klassiska) resurser och till gångar program mässigt, till exempel arbets ytor, data uppsättningar och webb tjänster.

Du kan interagera med Studio (klassiska) resurser med tre PowerShell-moduler:

* [Azure PowerShell AZ](#az-rm) som släpptes i 2018, innehåller alla funktioner i AzureRM, men med olika cmdlet-namn
* [AzureRM](#az-rm) släpptes i 2016, ersatt av PowerShell AZ
* [Azure Machine Learning PowerShell Classic](#classic) lanseras i 2016

Även om dessa PowerShell-moduler har en del likheter, är var och en utformad för specifika scenarier. Den här artikeln beskriver skillnaderna mellan PowerShell-modulerna och hjälper dig att bestämma vilka du vill välja.  

Se efter i [support tabellen](#support-table) nedan om vilka resurser som stöds av varje modul. 

## <a name="azure-powershell-az-and-azurerm"></a><a name="az-rm"></a> Azure PowerShell AZ och AzureRM

AZ är nu den avsedda PowerShell-modulen för att interagera med Azure och innehåller alla tidigare funktioner i AzureRM. AzureRM kommer att fortsätta att ta emot fel korrigeringar, men det får inga nya cmdletar eller funktioner.  AZ och AzureRM båda hanterar lösningar som distribueras med **Azure Resource Manager** distributions modell. Dessa resurser innehåller Studio (klassiska) arbets ytor och Studio (klassiska) "nya" webb tjänster. 

PowerShell Classic kan installeras tillsammans med antingen AZ eller AzureRM för att inkludera både "nya" och "klassiska" resurs typer. Men vi rekommenderar inte att du har AZ och AzureRM installerade samtidigt. För att kunna välja mellan AZ och AzureRM rekommenderar Microsoft AZ för alla framtida distributioner.  Läs mer om AZ jämfört med AzureRM och sökvägen för migrering i [Introduktion till Azure PowerShell AZ](/powershell/azure/new-azureps-module-az).

Kom igång med AZ genom att följa [installations anvisningarna för Azure AZ](/powershell/azure/install-az-ps).

## <a name="powershell-classic"></a><a name="classic"></a> Klassisk PowerShell

Den [klassiska PowerShell-modulen](https://aka.ms/amlps) för Studio (klassisk) gör att du kan hantera resurser som distribueras med den **klassiska distributions modellen**. Dessa resurser inkluderar Studio (klassiska) användar till gångar, "klassiska" webb tjänster och "klassiska" webb tjänst slut punkter.

Microsoft rekommenderar dock att du använder distributions modellen för Resource Manager för alla framtida resurser för att förenkla distributionen och hanteringen av resurser. Om du vill lära dig mer om distributions modellerna kan du läsa artikeln [Azure Resource Manager kontra klassisk distribution](../../azure-resource-manager/management/deployment-models.md) .

Kom igång med PowerShell Classic genom att ladda ned [versions paketet](https://github.com/hning86/azuremlps/releases) från GitHub och följa [installations anvisningarna](https://github.com/hning86/azuremlps/blob/master/README.md). Anvisningarna beskriver hur du avblockerar den nedladdade/zippade DLL-filen och importerar den sedan till din PowerShell-miljö.

PowerShell Classic kan installeras tillsammans med antingen AZ eller AzureRM för att inkludera både "nya" och "klassiska" resurs typer.

## <a name="powershell-support-table"></a><a name="support-table"></a> PowerShell-support tabell


| Uppgift | **AZ** |  **Klassisk PowerShell** |
| --- | --- | --- |
| Skapa/ta bort arbets ytor | [Mallar för Resurshanteraren](./deploy-with-resource-manager-template.md) |  |
| Hantera åtagande planer för arbets ytan | [New-AzMlCommitmentPlan](/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| Hantera arbets ytans användare |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Hantera webbtjänster | [New-AzMlWebService](/powershell/module/az.machinelearning/new-azmlwebservice) <br>("nya" webb tjänster)| [New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>("klassiska" webb tjänster) |
| Hantera webb tjänst slut punkter/nycklar |  [Get-AzMlWebServiceKey](/powershell/module/az.machinelearning/get-azmlwebservicekey)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| Hantera användar data uppsättningar/utbildade modeller| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Hantera användar experiment |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Hantera anpassade moduler | | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Nästa steg
Läs den fullständiga dokumentationen i PowerShell-modulen:
* [Klassisk PowerShell](https://aka.ms/amlps)
* [Azure PowerShell Az](/powershell/module/az.machinelearning/#machine_learning)