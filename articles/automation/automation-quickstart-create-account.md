---
title: Azure-snabbstart – Skapa ett Azure Automation-konto | Microsoft Docs
description: Den här artikeln hjälper dig att komma igång med att skapa ett Azure Automation konto och köra en Runbook.
services: automation
ms.date: 04/04/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: a07c6ac524aa213519ace1ae204ac2d76db802aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "83836710"
---
# <a name="create-an-azure-automation-account"></a>Skapa ett Azure Automation-konto

Du kan skapa ett Azure Automation konto via Azure, med hjälp av Azure Portal, ett webbläsarbaserat användar gränssnitt som ger åtkomst till ett antal resurser. Ett Automation-konto kan hantera resurser i alla regioner och prenumerationer för en specifik klient. 

I den här snabb starten får du hjälp med att skapa ett Automation-konto och köra en Runbook i kontot. Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

[Logga in i Azure](https://portal.azure.com).

## <a name="create-automation-account"></a>Skapa ett Automation-konto

1. Välj ett namn för ditt Azure-konto. Namn på Automation-konton är unika per region och resurs grupp. Namn på Automation-konton som har tagits bort kanske inte är omedelbart tillgängliga.

    > [!NOTE]
    > Du kan inte ändra konto namnet när det har angetts i användar gränssnittet. 

2. Klicka på knappen **skapa en resurs** som finns i det övre vänstra hörnet i Azure Portal.

3. Välj **den & hanterings verktyg** och välj sedan **Automation**.

4. Ange konto informationen, inklusive det valda konto namnet. För **Skapa Kör som-konto i Azure**, väljer du **Ja** så att artefakterna för att förenkla autentisering till Azure aktiveras automatiskt. När informationen är klar klickar du på **skapa** för att starta distributionen av Automation-kontot.

    ![Ange information om ditt Automation-konto på sidan](./media/automation-quickstart-create-account/create-automation-account-portal-blade.png)  

    > [!NOTE]
    > En uppdaterad lista över platser som du kan distribuera ett Automation-konto till finns i [produkt tillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all).

5. När distributionen har slutförts klickar du på **alla tjänster**.

6. Välj **Automation-konton** och välj sedan det Automation-konto som du har skapat.

    ![Översikt över Automation-konton](./media/automation-quickstart-create-account/automation-account-overview.png)

## <a name="run-a-runbook"></a>Kör en runbook

Kör en självstudierunbook.

1. Klicka på **Runbooks** under **process automatisering**. En lista med runbooks visas. Som standard är flera själv studie-Runbooks aktiverade i kontot.

    ![Runbook-lista för Automation-konto](./media/automation-quickstart-create-account/automation-runbooks-overview.png)

1. Välj följande runbook: **AzureAutomationTutorialScript**. Den här åtgärden öppnar sidan för runbook-översikt.

    ![Runbook-översikt](./media/automation-quickstart-create-account/automation-tutorial-script-runbook-overview.png)

1. Klicka på **Starta** och på sidan Starta Runbook klickar du på **OK** för att starta en runbook.

    ![Sida för Runbook-jobb](./media/automation-quickstart-create-account/automation-tutorial-script-job.png)

1. När jobbets status blir `Running` klickar du på **utdata** eller **alla loggar** för att visa utdata från Runbook-jobbet. För denna självstudierunbook är utdata en lista över dina Azure-resurser.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat ett Automation-konto, startat ett runbook-jobb och sett jobbresultaten. Om du vill veta mer om Azure Automation kan du fortsätta att använda snabbstarten för att skapa din första runbook.

> [!div class="nextstepaction"]
> [Snabb start för Automation – skapa en Azure Automation Runbook](./automation-quickstart-create-runbook.md)

