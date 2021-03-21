---
title: Använda Azure Automation runbooks och moduler i PowerShell-galleriet
description: Den här artikeln beskriver hur du använder Runbooks och moduler från Microsoft och communityn i PowerShell-galleriet.
services: automation
ms.subservice: process-automation
ms.date: 03/04/2021
ms.topic: conceptual
ms.openlocfilehash: c38a6236fe3ad9164d11d94e5563a7dddf5b4b32
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452789"
---
# <a name="use-runbooks-and-modules-in-powershell-gallery"></a>Använd runbooks och moduler i PowerShell-galleriet

I stället för att skapa egna runbooks och moduler i Azure Automation kan du komma åt scenarier som redan har skapats av Microsoft och communityn. Du kan hämta PowerShell-Runbooks och [moduler](#modules-in-powershell-gallery) från PowerShell-galleriet-och [python-Runbooks](#use-python-runbooks) från Azure Automation GitHub-organisationen. Du kan också bidra till communityn genom [att dela scenarier som du utvecklar](#add-a-powershell-runbook-to-the-gallery).

> [!NOTE]
> TechNets skript Center tas ur bruk. Alla Runbooks från Script Center i Runbook-galleriet har flyttats till vår [Automation GitHub-organisation](https://github.com/azureautomation) för mer information, se [här](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337).

## <a name="runbooks-in-powershell-gallery"></a>Runbooks i PowerShell-galleriet

[PowerShell-galleriet](https://www.powershellgallery.com/packages) tillhandahåller en mängd olika Runbooks från Microsoft och communityn som du kan importera till Azure Automation. Om du vill använda en, laddar du ned en Runbook från galleriet eller så kan du importera runbooks direkt från galleriet eller från ditt Automation-konto i Azure Portal.

> [!NOTE]
> Grafiska runbooks stöds inte i PowerShell-galleriet.

Du kan bara importera direkt från PowerShell-galleriet med hjälp av Azure Portal. Du kan inte utföra den här funktionen med PowerShell.

> [!NOTE]
> Du bör kontrol lera innehållet i alla Runbooks som du får från PowerShell-galleriet och använda yttersta försiktighet i att installera och köra dem i en produktions miljö.

## <a name="modules-in-powershell-gallery"></a>Moduler i PowerShell-galleriet

PowerShell-moduler innehåller cmdletar som du kan använda i dina runbooks. Befintliga moduler som du kan installera i Azure Automation finns tillgängliga i [PowerShell-galleriet](https://www.powershellgallery.com). Du kan starta galleriet från Azure Portal och installera modulerna direkt i Azure Automation, eller så kan du hämta och installera dem manuellt.

## <a name="common-scenarios-available-in-powershell-gallery"></a>Vanliga scenarier som är tillgängliga i PowerShell-galleriet

Listan nedan innehåller några Runbooks som stöder vanliga scenarier. En fullständig lista över Runbooks som skapats av Azure Automation-teamet finns i [AzureAutomationTeam Profile](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) – importerar den senaste versionen av alla moduler i ett Automation-konto från PowerShell-galleriet.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) – konfigurerar Azure-diagnostik och Log Analytics att ta emot Azure Automation loggar som innehåller jobb status och jobb strömmar.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) – kopierar en fjärrfil från en virtuell Windows Azure-dator.
   * [Copy-ItemToAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) – kopierar en lokal fil till en virtuell Azure-dator.

## <a name="import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Importera en PowerShell-Runbook från Runbook-galleriet med Azure Portal

1. Öppna ditt Automation-konto på Azure Portal.
1. Välj **Runbook-Galleri** under **process automatisering**.
1. Välj **Källa: PowerShell-galleriet**. Visar en lista över tillgängliga Runbooks som du kan bläddra i.
1. Du kan använda sökrutan ovanför listan för att begränsa listan, eller så kan du använda filtren för att begränsa visningen efter utgivare, typ och sortering. Leta upp det Galleri objekt som du vill använda och markera det för att visa dess information.

   :::image type="content" source="media/automation-runbook-gallery/browse-gallery-sm.png" alt-text="Bläddra i Runbook-galleriet." lightbox="media/automation-runbook-gallery/browse-gallery-lg.png":::

1. Om du vill importera ett objekt klickar du på **Importera** på bladet information.

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-detail-sm.png" alt-text="Visa information om ett objekt i Runbook-galleriet." lightbox="media/automation-runbook-gallery/gallery-item-detail-lg.png":::

1. Du kan också ändra namnet på runbooken och sedan klicka på **OK** för att importera runbooken.
1. Runbooken visas på fliken **Runbooks** för Automation-kontot.

## <a name="import-a--powershell-runbook-from-github-with-the-azure-portal"></a>Importera en PowerShell-Runbook från GitHub med Azure Portal

1. Öppna ditt Automation-konto på Azure Portal.
1. Välj **Runbook-Galleri** under **process automatisering**.
1. Välj **Källa: GitHub**.
1. Du kan använda filtren ovanför listan för att begränsa visningen efter utgivare, typ och sortering. Leta upp det Galleri objekt som du vill använda och markera det för att visa dess information.

   :::image type="content" source="media/automation-runbook-gallery/browse-gallery-github-sm.png" alt-text="Bläddra i GitHub-galleriet." lightbox="media/automation-runbook-gallery/browse-gallery-github-lg.png":::

1. Om du vill importera ett objekt klickar du på **Importera** på bladet information.

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-details-blade-github-sm.png" alt-text="Detaljerad vy av en Runbook från GitHub-galleriet." lightbox="media/automation-runbook-gallery/gallery-item-details-blade-github-lg.png":::

1. Du kan också ändra namnet på runbooken och sedan klicka på **OK** för att importera runbooken.
1. Runbooken visas på fliken **Runbooks** för Automation-kontot.

## <a name="add-a-powershell-runbook-to-the-gallery"></a>Lägg till en PowerShell-Runbook i galleriet

Microsoft uppmuntrar dig att lägga till Runbooks i de PowerShell-galleriet som du tror kan vara användbara för andra kunder. PowerShell-galleriet accepterar PowerShell-moduler och PowerShell-skript. Du kan lägga till en Runbook genom [att ladda upp den till PowerShell-galleriet](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

## <a name="import-a-module-from-the-module-gallery-with-the-azure-portal"></a>Importera en modul från modul-galleriet med Azure Portal

1. Öppna ditt Automation-konto på Azure Portal.
1. Välj **moduler** under **delade resurser** för att öppna listan över moduler.
1. Klicka på **Bläddra i galleriet** överst på sidan.

      :::image type="content" source="media/automation-runbook-gallery/modules-blade-sm.png" alt-text="Vy över modulens Galleri." lightbox="media/automation-runbook-gallery/modules-blade-lg.png":::

1. På sidan bläddra i galleriet kan du söka efter matchningar i något av följande fält med hjälp av sökrutan:

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

## <a name="use-python-runbooks"></a>Använda python-Runbooks

Python-Runbooks är tillgängliga i [Azure Automation GitHub-organisationen](https://github.com/azureautomation). När du bidrar till vår GitHub-lagrings platsen lägger du till taggen **(avsnittet GitHub): python3** när du laddar upp ditt bidrag.

## <a name="request-a-runbook-or-module"></a>Begär en Runbook eller modul

Du kan skicka förfrågningar till [användar rösten](https://feedback.azure.com/forums/246290-azure-automation/).  Om du behöver hjälp med att skriva en Runbook eller om du har en fråga om PowerShell, kan du skicka en fråga till vår [sida om Microsoft Q&en fråga](/answers/topics/azure-automation.html).

## <a name="next-steps"></a>Nästa steg

* Information om hur du kommer igång med en PowerShell-Runbook finns i [Självstudier: skapa en PowerShell-Runbook](learn/automation-tutorial-runbook-textual-powershell.md).
* Information om hur du arbetar med Runbooks finns [i hantera Runbooks i Azure Automation](manage-runbooks.md).
* Mer information om PowerShell finns i [PowerShell-dokument](/powershell/scripting/overview).
* En PowerShell-cmdlet-referens finns i [AZ. Automation](/powershell/module/az.automation).
