---
title: Globala parametrar
description: Ange globala parametrar för var och en av dina Azure Data Factory miljöer
ms.service: data-factory
ms.topic: conceptual
author: dcstwh
ms.author: weetok
ms.date: 03/15/2021
ms.openlocfilehash: 3110ce8cb97379fd4690903ec769cc1dfc7f1326
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492771"
---
# <a name="global-parameters-in-azure-data-factory"></a>Globala parametrar i Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Globala parametrar är konstanter över en data fabrik som kan användas av en pipeline i valfritt uttryck. De är användbara när du har flera pipelines med identiska parameter namn och-värden. När du befordrar en data fabrik med kontinuerlig integrering och distributions process (CI/CD) kan du åsidosätta dessa parametrar i varje miljö. 

## <a name="creating-global-parameters"></a>Globala parametrar skapas

Om du vill skapa en global parameter går du till fliken *globala parametrar* i avsnittet **Hantera** . Välj **ny** för att öppna sidan för skapande av sidan – navigering.

![Skärm bild som visar knappen nytt som du väljer för att skapa globala parametrar.](media/author-global-parameters/create-global-parameter-1.png)

I sidan-nav anger du ett namn, väljer en datatyp och anger värdet för parametern.

![Skärm bild som visar var du lägger till namnet, data typen och värdet för den nya globala parametern.](media/author-global-parameters/create-global-parameter-2.png)

När du har skapat en global parameter kan du redigera den genom att klicka på parameterns namn. Om du vill ändra flera parametrar samtidigt väljer du **Redigera alla**.

![Skapa globala parametrar](media/author-global-parameters/create-global-parameter-3.png)

## <a name="using-global-parameters-in-a-pipeline"></a>Använda globala parametrar i en pipeline

Globala parametrar kan användas i alla [pipeline-uttryck](control-flow-expression-language-functions.md). Om en pipeline refererar till en annan resurs, till exempel en data uppsättning eller ett data flöde, kan du överföra det globala parametervärdet via resursens parametrar. Globala parametrar refereras till som `pipeline().globalParameters.<parameterName>` .

![Använda globala parametrar](media/author-global-parameters/expression-global-parameters.png)

## <a name="global-parameters-in-cicd"></a><a name="cicd"></a> Globala parametrar i CI/CD

Det finns två sätt att integrera globala parametrar i den kontinuerliga integrerings-och distributions lösningen:

* Inkludera globala parametrar i ARM-mallen
* Distribuera globala parametrar via ett PowerShell-skript

I de flesta användnings fall rekommenderar vi att du inkluderar globala parametrar i ARM-mallen. Detta integreras internt med den lösning som beskrivs i [CI/CD-dokumentet](continuous-integration-deployment.md). Globala parametrar läggs till som en ARM-mallparameter som standard eftersom de ofta ändras från miljö till miljö. Du kan aktivera inkludering av globala parametrar i ARM-mallen från **Hantera** hubben.

> [!NOTE]
> Konfigurationen **inkludera i arm-mallen** är bara tillgänglig i git-läge. För närvarande är den inaktive rad i läget "Live mode" eller "Data Factory". 

> [!WARNING]
>Du kan inte använda--i parameter namnet. Du får felkod "{" Code ":" BadRequest "," meddelande ":" ErrorCode = InvalidTemplate, ErrorMessage = uttrycket > pipelinen (). Dublettparameternamnet. min-dbtest-URL: en är inte giltig:.....}. Men du kan använda "_" i parameter namnet.

![Ta med i ARM-mallen](media/author-global-parameters/include-arm-template.png)

Om du lägger till globala parametrar i ARM-mallen läggs en inställning på fabriks nivå som åsidosätter andra inställningar på fabriks nivå, till exempel en kundhanterad nyckel eller git-konfiguration i andra miljöer. Om du har dessa inställningar aktiverade i en upphöjd miljö, till exempel UAT eller PROD, är det bättre att distribuera globala parametrar via ett PowerShell-skript i stegen som marker ATS nedan.

### <a name="deploying-using-powershell"></a>Distribuera med PowerShell

Följande steg beskriver hur du distribuerar globala parametrar via PowerShell. Detta är användbart när mål fabriken har en inställning på fabriks nivå, till exempel kundhanterad nyckel.

När du publicerar en fabrik eller exporterar en ARM-mall med globala parametrar, skapas en mapp med namnet *dublettparameternamnet* med en fil som heter *your-factory-name_GlobalParameters.jspå*. Den här filen är ett JSON-objekt som innehåller varje global parameter typ och värde i den publicerade fabriken.

![Globala parametrar publiceras](media/author-global-parameters/global-parameters-adf-publish.png)

Om du distribuerar till en ny miljö, t. ex. TEST eller produkt, rekommenderar vi att du skapar en kopia av den globala parameter filen och skriver över lämpliga miljöaktuella värden. När du publicerar om den ursprungliga globala parameter filen kommer att skrivas över, men kopian för den andra miljön blir orörd.

Om du till exempel har en fabrik med namnet "ADF-DEV" och en global parameter av typen String med namnet "miljö" med värdet "dev", genereras en fil som heter *ADF-DEV_GlobalParameters.jspå* . Om du distribuerar till en test fabrik med namnet ADF_TEST, skapar du en kopia av JSON-filen (till exempel med namnet ADF-TEST_GlobalParameters.jspå) och ersätter parametervärdena med de miljöaktuella värdena. Parametern "miljö" kan ha värdet "test" nu. 

![Distribuera globala parametrar](media/author-global-parameters/powershell-task.png)

Använd PowerShell-skriptet nedan för att befordra globala parametrar till ytterligare miljöer. Lägg till en Azure PowerShell DevOps-uppgift innan ARM-Malldistribution. I DevOps-aktiviteten måste du ange platsen för den nya parameter filen, mål resurs gruppen och mål data fabriken.

> [!NOTE]
> Om du vill distribuera globala parametrar med PowerShell måste du använda minst version 4.4.0 av AZ-modulen.

```powershell
param
(
    [parameter(Mandatory = $true)] [String] $globalParametersFilePath,
    [parameter(Mandatory = $true)] [String] $resourceGroupName,
    [parameter(Mandatory = $true)] [String] $dataFactoryName
)

Import-Module Az.DataFactory

$newGlobalParameters = New-Object 'system.collections.generic.dictionary[string,Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification]'

Write-Host "Getting global parameters JSON from: " $globalParametersFilePath
$globalParametersJson = Get-Content $globalParametersFilePath

Write-Host "Parsing JSON..."
$globalParametersObject = [Newtonsoft.Json.Linq.JObject]::Parse($globalParametersJson)

foreach ($gp in $globalParametersObject.GetEnumerator()) {
    Write-Host "Adding global parameter:" $gp.Key
    $globalParameterValue = $gp.Value.ToObject([Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification])
    $newGlobalParameters.Add($gp.Key, $globalParameterValue)
}

$dataFactory = Get-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Name $dataFactoryName
$dataFactory.GlobalParameters = $newGlobalParameters

Write-Host "Updating" $newGlobalParameters.Count "global parameters."

Set-AzDataFactoryV2 -InputObject $dataFactory -Force
```

## <a name="next-steps"></a>Nästa steg

* Läs mer om Azure Data Factoryens [kontinuerliga integrerings-och distributions process](continuous-integration-deployment.md)
* Lär dig hur du använder [språket för kontroll flödes uttryck](control-flow-expression-language-functions.md)
