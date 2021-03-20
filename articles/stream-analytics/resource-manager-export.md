---
title: Exportera en Azure Stream Analytics jobb Azure Resource Manager mall
description: I den här artikeln beskrivs hur du exporterar en Azure Resource Manager-mall för ditt Azure Stream Analytics-jobb.
services: stream-analytics
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: aa17d83dcc14675db5ff6aa4597314baffbffdbb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98015427"
---
# <a name="export-an-azure-stream-analytics-job-azure-resource-manager-template"></a>Exportera en Azure Stream Analytics jobb Azure Resource Manager mall

Med [Azure Resource Manager mallar](../azure-resource-manager/templates/overview.md) kan du implementera infrastruktur som kod. Mallen är en JavaScript Object Notation-fil (JSON) som definierar infrastrukturen och konfigurationen för dina resurser. Du anger vilka resurser som ska distribueras och egenskaperna för dessa resurser.

Du kan distribuera om ett Azure Stream Analytics jobb genom att exportera Azure Resource Managers mal len.

## <a name="open-a-job-in-vs-code"></a>Öppna ett jobb i VS Code

Innan du kan exportera en mall måste du först öppna ett befintligt Stream Analytics-jobb i Visual Studio Code. 

Om du vill exportera ett jobb till ett lokalt projekt letar du reda på det jobb som du vill exportera i **Stream Analytics Explorer** i Azure Portal. Från sidan **fråga** väljer du **Öppna i Visual Studio**. Välj sedan **Visual Studio Code**.

![Öppna Stream Analytics jobb i Visual Studio Code](./media/resource-manager-export/open-job-vs-code.png)

Mer information om hur du använder Visual Studio Code för att hantera Stream Analytics-jobb finns i snabb starten för [Visual Studio Code](quick-create-visual-studio-code.md).

## <a name="compile-the-script"></a>Kompilera skriptet 

Nästa steg är att kompilera jobb skriptet till en Azure Resource Manager-mall. Innan du kompilerar skriptet måste du kontrol lera att jobbet har minst en indata och att en utmatning har kon figurer ATS. Om ingen indata eller utdata har kon figurer ATS måste du konfigurera indata och utdata först.

1. I Visual Studio Code navigerar du till jobbets *transformering. asaql* -fil.

   ![Transformera. asaql-fil i Visual Studio Code](./media/resource-manager-export/transformation-asaql.png)

1. Högerklicka på filen *Transformation. asaql* och välj **ASA: kompilera skriptet** på menyn.

1. Observera att en **Deploy** -mapp visas i arbets ytan Stream Analytics jobb.

1. Utforska *JobTemplate.jsi* filen, som är den Azure Resource Management-mall som används för att distribuera.

## <a name="complete-the-parameters-file"></a>Slutför parameter filen

Sedan slutför du filen med parametrar för Azure Resource Management-mallen.

1. Öppna filen *JobTemplate.parameters.jspå* filen som finns i mappen **distribuera** på arbets ytan Stream Analytics jobb i Visual Studio Code.

1. Observera att indata-och utmatnings nycklar är null. Ersätt null-värdena med de faktiska åtkomst nycklarna för dina indata-och utmatnings resurser.

1. Spara parameter filen.

## <a name="deploy-using-templates"></a>Distribuera med mallar

Du är redo att distribuera ditt Azure Stream Analytics-jobb med hjälp av Azure Resource Manager mallar som du skapade i föregående avsnitt.

Kör följande kommando i ett PowerShell-fönster. Se till att reaplce *ResourceGroupName*, *TemplateFile* och *TemplateParameterFile* med det faktiska resurs grupps namnet och de fullständiga sök vägarna till *JobTemplate.jspå* och *JobTemplate.parameters.jspå* filer i **mappen distribuera** i arbets ytan jobb.

Om du inte har Azure PowerShell konfigurerat följer du stegen i [installera Azure PowerShell-modulen](/powershell/azure/install-Az-ps).

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName "<your resource group>" -TemplateFile "<path to JobTemplate.json>" -TemplateParameterFile "<path to JobTemplate.parameters.json>"
```

## <a name="next-steps"></a>Nästa steg

* [Testa Azure Stream Analytics jobb lokalt med Live-indatamängden med Visual Studio Code](visual-studio-code-local-run-live-input.md)

* [Utforska Azure Stream Analytics-jobb med Visual Studio Code (för hands version)](visual-studio-code-explore-jobs.md)