---
title: Använd Azure Automation för att starta ett jobb i StorSimple Data Manager
description: Lär dig hur du använder Azure Automation för att utlösa StorSimple Data Manager jobb
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 3d0103857b6f759560129dbe8e693ec6c0d7291e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94961217"
---
# <a name="use-azure-automation-to-trigger-a-job"></a>Använda Azure Automation till att utlösa ett jobb

Den här artikeln förklarar hur du kan använda funktionen datatransformering i StorSimple Data Manager-tjänsten för att transformera StorSimple enhets data. Du kan starta ett data omvandlings jobb på två sätt: 

 - Använda .NET SDK
 - Använd Azure Automation Runbook
 
Den här artikeln beskriver hur du skapar en Azure Automation Runbook och sedan använder den för att initiera ett data omvandlings jobb. Om du vill veta mer om hur du initierar datatransformering via .NET SDK går du till [använda .NET SDK för att utlösa data omvandlings jobb](storsimple-data-manager-dotnet-jobs.md).

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du kontrol lera att du har:

*   Azure PowerShell installerat på klient datorn. [Ladda ned Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
*   En korrekt konfigurerad jobb definition i en StorSimple Data Manager tjänst inom en resurs grupp.
*   Ladda ned  [`DataTransformationApp.zip`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) filen från GitHub-lagringsplatsen. 
*   Hämta [`Trigger-DataTransformation-Job.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) skriptet från GitHub-lagringsplatsen.

## <a name="step-by-step-procedure"></a>Steg för steg-procedur

### <a name="set-up-the-automation-account"></a>Konfigurera Automation-kontot

1. Skapa ett Azure kör som Automation-konto i Azure Portal. Det gör du genom att gå till **Azure marketplace > allt** och sedan söka efter **Automation**. Välj **Automation-konton**.

    ![Skapa Kör som Automation-konto](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. Klicka på **+ Lägg** till för att lägga till ett nytt Automation-konto.

    ![Skapa Kör som Automation-konto 2](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. I **Lägg till Automation**:

   1. Ange **namnet** på ditt Automation-konto.
   2. Välj den **prenumeration** som är länkad till StorSimple data managers Tjänsten.
   3. Skapa en ny resurs grupp eller Välj en befintlig resurs grupp.
   4. Välj en **plats**.
   5. Låt standard alternativet **skapa kör som-konto** vara markerat.
   6. Om du vill hämta en länk till snabb åtkomst på instrument panelen, kontrollerar **du fäst på instrument panelen**. Klicka på **Skapa**.

      ![Skapa Kör som Automation-konto 3](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
      När Automation-kontot har skapats får du ett meddelande.
    
      ![Meddelande om distribution av Automation-konto](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

      Mer information finns i [skapa ett Kör som-konto](../automation/manage-runas-account.md).

3. I det nyligen skapade kontot går du till **delade resurser > moduler** och klickar på **+ Lägg till modul**.

    ![Importera modul 1](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. Bläddra till `DataTransformationApp.zip` filens plats från den lokala datorn och välj och öppna modulen. Importera modulen genom att klicka på **OK** .

    ![Importera modul 2](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   När Azure Automation importerar en modul till ditt konto extraheras metadata om modulen. Den här åtgärden kan ta några minuter.

   ![Importera modul 4](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. Du får ett meddelande om att modulen har distribuerats och ett annat meddelande när processen är klar.  Status i **moduler** ändras till **tillgänglig**.

    ![Importera modul 5](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>Importera, publicera och köra Automation-Runbook

Utför följande steg för att importera, publicera och köra runbooken för att utlösa jobb definitionen.

1. Öppna ditt Automation-konto på Azure Portal. Gå till **process automatisering > Runbooks** och klicka på **+ Lägg till en Runbook**.

    ![Lägg till Runbook 1](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. I **Lägg till Runbook** klickar du på **Importera en befintlig Runbook**.

3. Peka på Azure PowerShell skript filen `Trigger-DataTransformation-Job.ps1` för Runbook- **filen**. Runbook-typen väljs automatiskt. Ange ett namn och en valfri beskrivning för runbooken. Klicka på **Skapa**.

    ![Lägg till Runbook 2](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. Den nya runbooken visas i listan över Runbooks för Automation-kontot. Välj och klicka på denna Runbook.

    ![Lägg till Runbook 3](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. Redigera runbooken och klicka på **test** fönster.

    ![Lägg till Runbook 4](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. Ange parametrarna, till exempel namnet på din StorSimple Data Manager-tjänst, den associerade resurs gruppen och jobb definitions namnet. **Starta** testet. Rapporten skapas när körningen har slutförts. Mer information finns i så här testar du [en Runbook](../automation/learn/automation-tutorial-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Lägg till Runbook 8](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. Granska utdata från runbooken i test fönstret. Stäng fönstret om det är uppfyllt. Klicka på **publicera** och när du uppmanas att bekräfta, bekräfta och publicera runbooken.

    ![Lägg till Runbook 6](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. Gå tillbaka till **Runbooks** och välj den nyligen skapade runbooken.

    ![Lägg till Runbook 7](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. **Starta** runbooken. I **starta Runbook** anger du alla parametrar. Klicka på **OK** för att skicka och starta data omvandlings jobbet.

10. Om du vill övervaka jobb förloppet i Azure Portal går du till **jobb** i StorSimple Data Manager-tjänsten. Välj och klicka på jobbet för att visa jobb informationen.

    ![Lägg till Runbook 10](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>Nästa steg

[Använd StorSimple Data Manager användar gränssnitt för att transformera data](storsimple-data-manager-ui.md).