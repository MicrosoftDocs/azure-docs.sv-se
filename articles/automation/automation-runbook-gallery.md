---
title: Använda Azure Automation runbooks och moduler i PowerShell-galleriet
description: Den här artikeln beskriver hur du använder Runbooks och moduler från Microsoft GitHub databaser och PowerShell-galleriet.
services: automation
ms.subservice: process-automation
ms.date: 04/07/2021
ms.topic: conceptual
ms.openlocfilehash: 2df019888d293cd8a25a34e6f0f4e7dd215c6a41
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030687"
---
# <a name="use-existing-runbooks-and-modules"></a>Använda befintliga Runbooks och moduler

I stället för att skapa egna runbooks och moduler i Azure Automation kan du komma åt scenarier som redan har skapats av Microsoft och communityn. Du kan få Azure-relaterade PowerShell-och python-Runbooks från Runbook-galleriet i Azure Portal, och [moduler](#modules-in-the-powershell-gallery) och [Runbooks](#runbooks-in-the-powershell-gallery) (som kan vara olika för Azure) från PowerShell-galleriet. Du kan också bidra till communityn genom [att dela scenarier som du utvecklar](#contribute-to-the-community).

> [!NOTE]
> TechNets skript Center tas ur bruk. Alla Runbooks från Script Center i Runbook-galleriet har flyttats till vår [Automation GitHub-organisation](https://github.com/azureautomation) för mer information, se [Azure Automation runbooks som flyttas till GitHub](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337).

## <a name="import-runbooks-from-github-with-the-azure-portal"></a>Importera runbooks från GitHub med Azure Portal

1. Öppna ditt Automation-konto på Azure Portal.
2. Välj **Runbook-Galleri** under **process automatisering**.
3. Välj **Källa: GitHub**.
4. Du kan använda filtren ovanför listan för att begränsa visningen efter utgivare, typ och sortering. Leta upp det Galleri objekt som du vill använda och markera det för att visa dess information.

   :::image type="content" source="./media/automation-runbook-gallery/browse-gallery-github.png" alt-text="Söker i Runbook-galleriet." lightbox="./media/automation-runbook-gallery/browse-gallery-github-expanded.png":::

5. Klicka på **Importera** på informations sidan om du vill importera ett objekt.

   :::image type="content" source="./media/automation-runbook-gallery/gallery-item-import.png" alt-text="Galleri objekt import.":::

6. Du kan också ändra namnet på runbooken på bladet importera och sedan klicka på **OK** för att importera runbooken.

   :::image type="content" source="./media/automation-runbook-gallery/gallery-item-import-blade.png" alt-text="Bladet importera Galleri objekt.":::

7. Runbooken visas på fliken **Runbooks** för Automation-kontot.
 
## <a name="runbooks-in-the-powershell-gallery"></a>Runbooks i PowerShell-galleriet

> [!IMPORTANT]
> Du bör verifiera innehållet i alla Runbooks som du får från PowerShell-galleriet. Använd yttersta försiktighet när du installerar och kör dem i en produktions miljö.

[PowerShell-galleriet](https://www.powershellgallery.com/packages) innehåller olika Runbooks från Microsoft och communityn som du kan importera till Azure Automation. Om du vill använda en, laddar du ned en Runbook från galleriet eller så kan du importera runbooks direkt från galleriet eller från ditt Automation-konto i Azure Portal.

> [!NOTE]
> Grafiska runbooks stöds inte i PowerShell-galleriet.

Du kan bara importera direkt från PowerShell-galleriet med hjälp av Azure Portal. Du kan inte utföra den här funktionen med PowerShell. Proceduren är densamma som i [importera runbooks från GitHub med Azure Portal](#import-runbooks-from-github-with-the-azure-portal), förutom att **källan** kommer att **PowerShell-galleriet**.

:::image type="content" source="./media/automation-runbook-gallery/source-runbook-gallery-small.png" alt-text="Visar käll valet för Runbook-galleriet." lightbox="./media/automation-runbook-gallery/source-runbook-gallery-large.png":::

## <a name="modules-in-the-powershell-gallery"></a>Moduler i PowerShell-galleriet

PowerShell-moduler innehåller cmdletar som du kan använda i dina runbooks. Befintliga moduler som du kan installera i Azure Automation finns tillgängliga i [PowerShell-galleriet](https://www.powershellgallery.com). Du kan starta galleriet från Azure Portal och installera modulerna direkt i Azure Automation, eller så kan du hämta och installera dem manuellt.

Du kan också hitta moduler som ska importeras i Azure Portal. De är listade för ditt Automation-konto i **modulen moduler** under **delade resurser**.

## <a name="common-scenarios-available-in-the-powershell-gallery"></a>Vanliga scenarier som är tillgängliga i PowerShell-galleriet

Listan nedan innehåller några Runbooks som stöder vanliga scenarier. En fullständig lista över Runbooks som skapats av Azure Automation-teamet finns i [AzureAutomationTeam Profile](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) – importerar den senaste versionen av alla moduler i ett Automation-konto från PowerShell-galleriet.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) – konfigurerar Azure-diagnostik och Log Analytics att ta emot Azure Automation loggar som innehåller jobb status och jobb strömmar.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) – kopierar en fjärrfil från en virtuell Windows Azure-dator.
   * [Copy-ItemToAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) – kopierar en lokal fil till en virtuell Azure-dator.

## <a name="contribute-to-the-community"></a>Bidra till communityn

Vi rekommenderar starkt att du bidrar till och hjälper till att växa Azure Automation communityn. Dela de fantastiska Runbooks som du har skapat med communityn. Dina bidrag kommer att uppskattas!

### <a name="add-a-runbook-to-the-github-runbook-gallery"></a>Lägga till en Runbook i GitHub Runbook-galleriet

Du kan lägga till nya PowerShell-eller python-Runbooks i Runbook-galleriet med det här GitHub-arbetsflödet.

1. Skapa ett offentligt lager på GitHub och Lägg till en Runbook och eventuella andra nödvändiga filer (t. ex. readme.md, beskrivning osv.).
1. Lägg till ämnet `azureautomationrunbookgallery` för att se till att lagrings platsen identifieras av vår tjänst, så att den kan visas i Automation Runbook-galleriet.
1. Om den Runbook som du har skapat är ett PowerShell-arbetsflöde lägger du till ämnet `PowerShellWorkflow` . Om det är en python 3-Runbook lägger du till `Python3` . Inga andra specifika ämnen krävs för Azure-Runbooks, men vi rekommenderar att du lägger till andra ämnen som kan användas för kategorisering och sökning i Runbook-galleriet.

   >[!NOTE]
   >Kolla in befintliga Runbooks i galleriet för saker som formatering, rubriker och befintliga taggar som du kan använda (t. ex. `Azure Automation` eller `Linux Azure Virtual Machines` ).

Om du vill föreslå ändringar i en befintlig Runbook kan du skicka en pull-begäran till den. 

Om du bestämmer dig för att klona och redigera en befintlig Runbook, är det bäst att ge det ett annat namn. Om du använder det gamla namnet igen visas det två gånger i listan med Runbook-gallerier.

>[!NOTE]
>Tillåt minst 12 timmar för synkronisering mellan GitHub och Automation Runbook-galleriet för både uppdaterade och nya Runbooks.

### <a name="add-a-powershell-runbook-to-the-powershell-gallery"></a>Lägga till en PowerShell-Runbook i PowerShell-galleriet

Microsoft uppmuntrar dig att lägga till Runbooks i de PowerShell-galleriet som du tror kan vara användbara för andra kunder. PowerShell-galleriet accepterar PowerShell-moduler och PowerShell-skript. Du kan lägga till en Runbook genom [att ladda upp den till PowerShell-galleriet](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

## <a name="import-a-module-from-the-modules-gallery-in-the-azure-portal"></a>Importera en modul från modulens galleri i Azure Portal

1. Öppna ditt Automation-konto på Azure Portal.
1. Under **delade resurser** väljer du **moduler Galleri** för att öppna listan över moduler.

      :::image type="content" source="media/automation-runbook-gallery/modules-blade-sm.png" alt-text="Vy över modulens Galleri." lightbox="media/automation-runbook-gallery/modules-blade-lg.png":::

1. På sidan bläddra i galleriet kan du söka efter följande fält:

   * Modulnamn
   * Taggar
   * Författare
   * Namn på cmdlet/DSC-resurs

1. Leta upp en modul som du är intresse rad av och välj den för att visa information om den.

   När du ökar detalj nivån i en speciell modul kan du Visa mer information. Den här informationen innehåller en länk tillbaka till PowerShell-galleriet, eventuella nödvändiga beroenden och alla cmdletar eller DSC-resurser som modulen innehåller.

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-details-blade-sm.png" alt-text="Detaljerad vy av en modul från galleriet." lightbox="media/automation-runbook-gallery/gallery-item-details-blade-lg.png":::

1. Klicka på **Importera** för att installera modulen direkt i Azure Automation.
1. I fönstret Importera kan du se namnet på den modul som ska importeras. Om alla beroenden är installerade aktive ras knappen **OK** . Om du saknar beroenden måste du importera dessa beroenden innan du kan importera den här modulen.
1. I fönstret Importera klickar du på **OK** för att importera modulen. Medan Azure Automation importerar en modul till ditt konto extraheras metadata om modulen och cmdletarna. Den här åtgärden kan ta några minuter eftersom varje aktivitet behöver extraheras.
1. Du får ett första meddelande om att modulen har distribuerats och en annan avisering när den har slutförts.
1. När modulen har importer ATS kan du se tillgängliga aktiviteter. Du kan använda modul resurser i dina runbooks och DSC-resurser.

> [!NOTE]
> Moduler som bara stöder PowerShell-kärnan stöds inte i Azure Automation och kan inte importeras i Azure Portal eller distribueras direkt från PowerShell-galleriet.

## <a name="request-a-runbook-or-module"></a>Begär en Runbook eller modul

Du kan skicka förfrågningar till [användar rösten](https://feedback.azure.com/forums/246290-azure-automation/).  Om du behöver hjälp med att skriva en Runbook eller om du har en fråga om PowerShell, kan du skicka en fråga till vår [sida om Microsoft Q&en fråga](/answers/topics/azure-automation.html).

## <a name="next-steps"></a>Nästa steg

* Information om hur du kommer igång med PowerShell-Runbooks finns i [Självstudier: skapa en PowerShell-Runbook](learn/automation-tutorial-runbook-textual-powershell.md).
* Information om hur du arbetar med Runbooks finns [i hantera Runbooks i Azure Automation](manage-runbooks.md).
* Mer information om PowerShell-skript finns i [PowerShell-dokument](/powershell/scripting/overview).
* En PowerShell-cmdlet-referens finns i [AZ. Automation](/powershell/module/az.automation).
