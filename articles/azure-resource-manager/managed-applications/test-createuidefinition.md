---
title: Testa definitions filen för användar gränssnittet
description: Beskriver hur du testar användar upplevelsen för att skapa ditt Azure-hanterade program via portalen.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: f76d3b81c2d5425f7bb91c5c86a79faa097794e3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96435009"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Testa Portal gränssnittet för Azure Managed Applications

När du har [skapat createUiDefinition.jsfilen](create-uidefinition-overview.md) för det hanterade programmet måste du testa användar upplevelsen. För att förenkla testningen använder du en sandbox-miljö som läser in din fil i portalen. Du behöver inte distribuera ditt hanterade program. Sand boxen visar ditt användar gränssnitt i den aktuella, kompletta Portal upplevelsen. Eller så kan du använda ett skript för att testa gränssnittet. Båda metoderna visas i den här artikeln. Sand boxen är det rekommenderade sättet att förhandsgranska gränssnittet.

## <a name="prerequisites"></a>Förutsättningar

* En **createUiDefinition.jspå** filen. Om du inte har den här filen kopierar du [exempel filen](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json).

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="use-sandbox"></a>Använd sandbox

1. Öppna [begränsat läge för att skapa gränssnitts definitioner](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade).

   ![Visa sandbox](./media/test-createuidefinition/show-sandbox.png)

1. Ersätt den tomma definitionen med innehållet i din createUiDefinition.jsi filen. Välj för **hands version**.

   ![Välj för hands version](./media/test-createuidefinition/select-preview.png)

1. Formuläret som du har skapat visas. Du kan gå igenom användar upplevelsen och fylla i värdena.

   ![Visa formulär](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>Felsökning

Om formuläret inte visas när du har valt för **hands version** kan du ha ett syntaxfel. Leta upp den röda indikatorn i den högra rullnings listen och navigera till den.

![Visa syntaxfel](./media/test-createuidefinition/show-syntax-error.png)

Om ditt formulär inte visas och du ser en ikon i ett moln med Riva Drop, har formuläret ett fel, till exempel en saknad egenskap. Öppna webb Utvecklarverktyg i webbläsaren. - **Konsolen** visar viktiga meddelanden om ditt gränssnitt.

![Visa fel](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>Använd test skript

Om du vill testa ditt gränssnitt i portalen kopierar du något av följande skript till den lokala datorn:

* [PowerShell-skript för sid inläsning – AZ-modul](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-AzCreateUIDefinition.ps1)
* [PowerShell-skript för inläsning – Azure-modul](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Skript för Azure CLI-sidans inläsning](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

Om du vill se gränssnitts filen i portalen kör du det nedladdade skriptet. Skriptet skapar ett lagrings konto i din Azure-prenumeration och laddar upp createUiDefinition.jspå filen till lagrings kontot. Lagrings kontot skapas första gången du kör skriptet eller om lagrings kontot har tagits bort. Om lagrings kontot redan finns i din Azure-prenumeration återanvänder skriptet det. Skriptet öppnar portalen och läser in filen från lagrings kontot.

Ange en plats för lagrings kontot och ange den mapp som innehåller createUiDefinition.jspå filen.

Om du använder PowerShell använder du:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

Om du använder Azure CLI använder du:

```bash
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

Om createUiDefinition.jsi filen finns i samma mapp som skriptet och du redan har skapat lagrings kontot, behöver du inte ange dessa parametrar.

Om du använder PowerShell använder du:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1
```

Om du använder Azure CLI använder du:

```bash
./sideload-createuidef.sh
```

Skriptet öppnar en ny flik i webbläsaren. Den visar portalen med ditt gränssnitt för att skapa det hanterade programmet.

Ange värden för fälten. När du är klar visas de värden som skickas till mallen som du hittar i webbläsarens verktyg för utvecklarverktyg.

![Visa värden](./media/test-createuidefinition/show-json.png)

Du kan använda dessa värden som parameter fil för att testa distributions mal len.

Om portalen låser sig på sidan Sammanfattning kan det finnas en bugg i avsnittet utdata. Du kan till exempel ha refererat till en kontroll som inte finns. Om en parameter i utdata är tom kan parametern referera till en egenskap som inte finns. Till exempel är referensen till kontrollen giltig, men ogiltig egenskaps referens.

## <a name="test-your-solution-files"></a>Testa dina lösningsfiler

Nu när du har kontrollerat att ditt Portal gränssnitt fungerar som förväntat, är det dags att verifiera att din createUiDefinition-fil är korrekt integrerad med din mainTemplate.jsi filen. Du kan köra ett verifierings skript test för att testa innehållet i dina lösningsfiler, inklusive createUiDefinition-filen. Skriptet verifierar JSON-syntaxen, söker efter regex-uttryck i textfält och kontrollerar att utmatnings värden för Portal gränssnittet matchar parametrarna i mallen. Information om hur du kör skriptet finns i [köra statiska verifierings kontroller för mallar](https://github.com/Azure/azure-quickstart-templates/tree/master/test).

## <a name="next-steps"></a>Nästa steg

När du har verifierat ditt Portal gränssnitt kan du läsa om hur du gör ditt [Azure-hanterade program tillgängligt på Marketplace](../../marketplace/create-new-azure-apps-offer.md).