---
title: Azure-snabbstart – Skapa en Azure Automation-runbook | Microsoft Docs
description: Den här artikeln hjälper dig att komma igång med att skapa en Azure Automation Runbook.
services: automation
ms.date: 02/05/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 948ca820347c7cdcd560ade46e850f66b25bc88e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90987293"
---
# <a name="create-an-azure-automation-runbook"></a>Skapa en Azure Automation-runbook

Azure Automation-runbooks kan skapas via Azure. Den här metoden ger ett webbläsarbaserat användargränssnitt för att skapa Automation-runbooks. Den här snabbstarten går igenom hur du skapar, testar och publicerar en Automation PowerShell-runbook.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure på https://portal.azure.com .

## <a name="create-the-runbook"></a>Skapa Runbook

Skapa en runbook först. Exempelrunbooken som skapas i den här snabbstarten matar ut `Hello World` som standard.

1. Öppna ditt Automation-konto.

1. Klicka på **Runbooks** under **process automatisering**. En lista med runbooks visas.

1. Klicka på **skapa en Runbook** överst i listan.

1. Ange `Hello-World` för Runbook-namnet i fältet **namn** och välj **PowerShell** för fältet Runbook- **typ** . 

   ![Ange information om din Automation-runbook på sidan](./media/automation-quickstart-create-runbook/automation-create-runbook-configure.png)

1. Klicka på **Skapa**. Runbooken skapas och sidan Redigera PowerShell-runbook öppnas.

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-edit-runbook-empty.png" alt-text="Skärm bild av sidan Redigera PowerShell-Runbook.":::

1. Skriv in eller kopiera och klistra in följande kod i redigeringsrutan. Den skapar en valfri indataparameter som kallas `Name` med standardvärdet `World` , och matar ut en sträng som använder det här indatavärdet:

   ```powershell-interactive
   param
   (
       [Parameter(Mandatory=$false)]
       [String] $Name = "World"
   )

   "Hello $Name!"
   ```

1. Klicka på **Spara** för att spara en utkast kopia av runbooken.

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-edit-runbook.png" alt-text="Skärm bild av sidan Redigera PowerShell-Runbook med ett kod exempel i det högra fönstret.":::

## <a name="test-the-runbook"></a>Testa runbooken

När runbooken har skapats måste du testa runbooken för att kontrol lera att den fungerar.

1. Öppna testfönstret genom att klicka på **Testfönster**.

1. Ange ett värde för **Namn** och klicka på **Starta**. Testjobbet startar och jobbstatus och utdata visas.

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-test-runbook.png" alt-text="Skärm bild av test fönstret med ett exempel värde i fältet namn.":::

1. Stäng test fönstret genom att klicka på **X** i det övre högra hörnet. Välj **OK** i popup-fönstret som visas.

1. På sidan Redigera PowerShell-runbook klickar du på **Publicera** för att publicera runbooken som den officiella versionen av runbooken på kontot.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job.png" alt-text="Skärm bild av sidan Redigera PowerShell-Runbook med knappen publicera vald.":::

## <a name="run-the-runbook"></a>Köra runbooken

När runbooken har publicerats visas översiktssidan.

1. På översiktssidan i runbooken klickar du på **Starta** för att öppna konfigurationssidan Starta runbook för denna runbook.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-start.png" alt-text="Skärm bild av sidan starta Runbook-konfiguration.":::

1. Lämna **Namn** tomt, så standardvärdet används, och klicka på **OK**. Runbook-jobbet skickas och sidan jobb visas.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-job-page.png" alt-text="Skärm bild av jobb sidan som visar knappen utdata vald.":::

1. När jobbets status är `Running` eller `Completed` klickar du på **utdata** för att öppna fönstret utdata och Visa Runbook-utdata.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job-output.png" alt-text="Skärm bild av fönstret utdata som visar Runbook-utdata.":::

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort din runbook när den inte längre behövs. Det gör du genom att markera runbooken i runbooklistan och klicka på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat, redigerat, testat och publicerat en runbook och startat ett runbookjobb. Om du vill lära dig mer om Automation-runbooks fortsätter du till artikeln om olika runbooktyper du kan skapa och använda i Automation.

> [!div class="nextstepaction"]
> [Azure Automation Runbook-typer](./automation-runbook-types.md)
