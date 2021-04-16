---
title: Azure-snabbstart – Skapa en Azure Automation-runbook | Microsoft Docs
description: Den här artikeln hjälper dig att komma igång med att Azure Automation en runbook.
services: automation
ms.date: 02/05/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom:
- mvc
- mode-api
ms.openlocfilehash: 151199d8389130b85e917a09fd8e8d7b5f56d90c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536553"
---
# <a name="create-an-azure-automation-runbook"></a>Skapa en Azure Automation-runbook

Azure Automation-runbooks kan skapas via Azure. Den här metoden ger ett webbläsarbaserat användargränssnitt för att skapa Automation-runbooks. Den här snabbstarten går igenom hur du skapar, testar och publicerar en Automation PowerShell-runbook.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure på https://portal.azure.com .

## <a name="create-the-runbook"></a>Skapa runbooken

Skapa en runbook först. Exempelrunbooken som skapas i den här snabbstarten matar ut `Hello World` som standard.

1. Öppna ditt Automation-konto.

1. Klicka **på Runbooks** under **Processautomatisering.** En lista med runbooks visas.

1. Klicka **på Skapa en runbook** överst i listan.

1. Ange `Hello-World` som runbooknamn i fältet **Namn** och välj **PowerShell** som **typfält för Runbook.** 

   ![Ange information om din Automation-runbook på sidan](./media/automation-quickstart-create-runbook/automation-create-runbook-configure.png)

1. Klicka på **Skapa**. Runbooken skapas och sidan Redigera PowerShell-runbook öppnas.

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-edit-runbook-empty.png" alt-text="Skärmbild av sidan Redigera PowerShell-runbook.":::

1. Skriv in eller kopiera och klistra in följande kod i redigeringsrutan. Den skapar en valfri indataparameter `Name` som anropas med standardvärdet `World` och matar ut en sträng som använder det här indatavärdet:

   ```powershell-interactive
   param
   (
       [Parameter(Mandatory=$false)]
       [String] $Name = "World"
   )

   "Hello $Name!"
   ```

1. Klicka **på Spara** för att spara ett utkast av runbooken.

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-edit-runbook.png" alt-text="Skärmbild av sidan Redigera PowerShell-runbook med ett kodexempel i det högra fönstret.":::

## <a name="test-the-runbook"></a>Testa runbooken

När runbooken har skapats måste du testa runbooken för att verifiera att den fungerar.

1. Öppna testfönstret genom att klicka på **Testfönster**.

1. Ange ett värde för **Namn** och klicka på **Starta**. Testjobbet startar och jobbstatus och utdata visas.

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-test-runbook.png" alt-text="Skärmbild av fönstret Test med ett exempelvärde i namnfältet.":::

1. Stäng testfönstret genom att klicka på **X i** det övre högra hörnet. Välj **OK** i popup-fönstret som visas.

1. På sidan Redigera PowerShell-runbook klickar du på **Publicera** för att publicera runbooken som den officiella versionen av runbooken på kontot.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job.png" alt-text="Skärmbild av sidan Redigera PowerShell-runbook som visar knappen Publicera markerad.":::

## <a name="run-the-runbook"></a>Köra runbooken

När runbooken har publicerats visas översiktssidan.

1. På översiktssidan i runbooken klickar du på **Starta** för att öppna konfigurationssidan Starta runbook för denna runbook.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-start.png" alt-text="Skärmbild av konfigurationssidan Starta runbook.":::

1. Lämna **Namn** tomt, så standardvärdet används, och klicka på **OK**. Runbook-jobbet skickas och sidan Jobb visas.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-job-page.png" alt-text="Skärmbild av sidan Jobb som visar knappen Utdata vald.":::

1. När jobbets status är `Running` eller klickar du på `Completed` **Utdata** för att öppna fönstret Utdata och visa runbook-utdata.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job-output.png" alt-text="Skärmbild av fönstret Utdata som visar runbook-utdata.":::

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort din runbook när den inte längre behövs. Det gör du genom att markera runbooken i runbooklistan och klicka på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat, redigerat, testat och publicerat en runbook och startat ett runbookjobb. Om du vill lära dig mer om Automation-runbooks fortsätter du till artikeln om olika runbooktyper du kan skapa och använda i Automation.

> [!div class="nextstepaction"]
> [Azure Automation runbooktyper](./automation-runbook-types.md)
