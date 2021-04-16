---
title: Testa ui-definitionsfilen
description: Beskriver hur du testar användarupplevelsen för att skapa ditt Azure-hanterade program via portalen.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: eeef454e4c5706b39d07261ade1c2f0ffbc942ad
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478904"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Testa portalgränssnittet för Azure Managed Applications

När [du har createUiDefinition.jspå filen](create-uidefinition-overview.md) för det hanterade programmet måste du testa användarupplevelsen. För att förenkla testningen använder du en sandbox-miljö som läser in filen i portalen. Du behöver inte distribuera det hanterade programmet. Sandbox-miljön visar ditt användargränssnitt i den aktuella helskärmsportalen. Eller så kan du använda ett skript för att testa gränssnittet. Båda metoderna visas i den här artikeln. Sandbox-miljön är det rekommenderade sättet att förhandsgranska gränssnittet.

## <a name="prerequisites"></a>Förutsättningar

* En **createUiDefinition.jspå** fil. Om du inte har den här filen kopierar du [exempelfilen](https://github.com/Azure/azure-quickstart-templates/blob/master/demos/100-marketplace-sample/createUiDefinition.json).

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="use-sandbox"></a>Använda sandbox-miljö

1. Öppna [sandbox-miljön för att skapa användargränssnittsdefinition.](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade)

   ![Visa sandbox-miljö](./media/test-createuidefinition/show-sandbox.png)

1. Ersätt den tomma definitionen med innehållet i createUiDefinition.jspå filen. Välj **Förhandsgranska**.

   ![Välj förhandsgranskning](./media/test-createuidefinition/select-preview.png)

1. Formuläret som du skapade visas. Du kan gå igenom användarupplevelsen och fylla i värdena.

   ![Visa formulär](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>Felsökning

Om formuläret inte visas när du har valt **Förhandsversion** kan du få ett syntaxfel. Leta efter den röda indikatorn i den högra rullningslisten och navigera till den.

![Visa syntaxfel](./media/test-createuidefinition/show-syntax-error.png)

Om formuläret inte visas, och du i stället ser en ikon i ett moln med en drop-avslitning, har formuläret ett fel, till exempel en egenskap som saknas. Öppna webbappen Utvecklarverktyg webbläsaren. Konsolen **visar** viktiga meddelanden om ditt gränssnitt.

![Visa fel](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>Använda testskript

Om du vill testa gränssnittet i portalen kopierar du något av följande skript till den lokala datorn:

* [PowerShell-skript för sidbelastning – Az-modul](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-AzCreateUIDefinition.ps1)
* [PowerShell-skript för sidbelastning – Azure-modul](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Azure CLI-skript för sidbelastning](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

Om du vill se gränssnittsfilen i portalen kör du det nedladdade skriptet. Skriptet skapar ett lagringskonto i din Azure-prenumeration och laddar upp createUiDefinition.jsfil till lagringskontot. Lagringskontot skapas första gången du kör skriptet eller om lagringskontot har tagits bort. Om lagringskontot redan finns i din Azure-prenumeration återanvänder skriptet det. Skriptet öppnar portalen och läser in filen från lagringskontot.

Ange en plats för lagringskontot och ange den mapp som innehåller createUiDefinition.jsfilen.

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

Om ditt createUiDefinition.jsfilen finns i samma mapp som skriptet och du redan har skapat lagringskontot behöver du inte ange dessa parametrar.

Om du använder PowerShell använder du:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1
```

Om du använder Azure CLI använder du:

```bash
./sideload-createuidef.sh
```

Skriptet öppnar en ny flik i webbläsaren. Portalen visas med ditt gränssnitt för att skapa det hanterade programmet.

Ange värden för fälten. När du är klar visas de värden som skickas till mallen som finns i webbläsarens konsol för utvecklingsverktyg.

![Visa värden](./media/test-createuidefinition/show-json.png)

Du kan använda dessa värden som parameterfil för att testa distributionsmallen.

Om portalen låser sig på sammanfattningsskärmen kan det finnas en bugg i utdataavsnittet. Du kan till exempel ha refererat till en kontroll som inte finns. Om en parameter i utdata är tom kan parametern referera till en egenskap som inte finns. Till exempel är referensen till kontrollen giltig, men egenskapsreferensen är inte giltig.

## <a name="test-your-solution-files"></a>Testa lösningsfilerna

Nu när du har kontrollerat att portalgränssnittet fungerar som förväntat är det dags att verifiera att filen createUiDefinition är korrekt integrerad med mainTemplate.jspå filen. Du kan köra ett valideringsskripttest för att testa innehållet i lösningsfilerna, inklusive filen createUiDefinition. Skriptet validerar JSON-syntaxen, söker efter regex-uttryck i textfält och ser till att utdatavärdena för portalgränssnittet matchar parametrarna för mallen. Information om hur du kör det här skriptet finns [i Köra statiska verifieringskontroller för mallar.](https://aka.ms/arm-ttk)

## <a name="next-steps"></a>Nästa steg

När du har verifierat portalgränssnittet kan du lära dig hur du gör [ditt Azure-hanterade program tillgängligt på Marketplace.](../../marketplace/create-new-azure-apps-offer.md)
