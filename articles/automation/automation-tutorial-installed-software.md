---
title: Identifiera vilken program vara som är installerad på dina virtuella datorer med Azure Automation | Microsoft Docs
description: I den här artikeln beskrivs program vara som är installerad på virtuella datorer i din miljö.
services: automation
keywords: inventering, automatisering, ändrings spårning
ms.date: 04/11/2018
ms.topic: tutorial
ms.subservice: change-inventory-management
ms.custom: mvc
ms.openlocfilehash: 8451067ae86e95269c0c2f22554e1654ac64c1e4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "100593788"
---
# <a name="discover-what-software-is-installed-on-your-vms"></a>Identifiera vilken program vara som är installerad på dina virtuella datorer

I den här självstudien får du lära dig hur du använder funktionen Azure Automation Ändringsspårning och inventering för att ta reda på vilken program vara som är installerad i din miljö. Du kan samla in och Visa inventering för program vara, filer, Linux-daemon, Windows-tjänster och Windows-registernycklar på dina datorer. Om du spårar konfigurationerna för dina datorer kan du identifiera driftproblem i miljön och bättre förstå datorernas tillstånd.

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Aktivera Ändringsspårning och inventering
> * Aktivera en virtuell Azure-dator
> * Aktivera en virtuell dator utan Azure
> * Visa installerade program
> * Sök inventeringsloggar för installerad programvara

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett [Automation-konto](./index.yml) för att lagra övervakare och åtgärds-Runbooks och övervaknings aktiviteten.
* En [virtuell dator](../virtual-machines/windows/quick-create-portal.md) som ska aktive ras för funktionen.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Aktivera Ändringsspårning och inventering

Först måste du aktivera Ändringsspårning och Inventering för den här självstudien. Om du tidigare har aktiverat funktionen behövs inte det här steget.

>[!NOTE]
>Om fälten är nedtonade är en annan automatiserings funktion aktive rad för den virtuella datorn och du måste använda samma arbets yta och Automation-konto.

1. Navigera till ditt Automation-konto och välj **inventering** eller **ändrings spårning** under **konfigurations hantering**.

2. Välj arbets ytan [Log Analytics](../azure-monitor/logs/log-query-overview.md) . Den här arbets ytan samlar in data som genereras av funktioner som Ändringsspårning och inventering. Arbetsytan tillhandahåller en enda plats för att granska och analysera data från flera källor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

3. Välj det Automation-konto som ska användas.

4. Konfigurera platsen för distributionen.

5. Klicka på **Aktivera** för att distribuera funktionen för din virtuella dator. 

    ![Banner för inventerings konfiguration](./media/automation-tutorial-installed-software/enableinventory.png)

Under installationen tillhandahålls den virtuella datorn med Log Analytics-agenten för Windows och en [hybrid Runbook Worker](automation-hybrid-runbook-worker.md). Det kan ta upp till 15 minuter att aktivera Ändringsspårning och inventering. Under tiden ska du inte stänga webbläsaren.

När funktionen har Aktiver ATS flödar information om installerad program vara och ändringar på den virtuella datorn till Azure Monitor loggar. Det kan ta mellan 30 minuter och 6 timmar innan data blir tillgängliga för analys.

## <a name="add-an-azure-vm-to-change-tracking-and-inventory"></a>Lägg till en virtuell Azure-dator i Ändringsspårning och inventering

1. I ditt Automation-konto navigerar du till **inventering** eller **ändrings spårning** under **konfigurations hantering**.

2. Välj **+ Lägg till virtuell Azure-dator**.

3. Välj din virtuella dator i listan över virtuella datorer. 

4. Klicka på **Aktivera** för att aktivera ändringsspårning och inventering på den virtuella datorn. Log Analytics agenten för Windows distribueras till den virtuella datorn och konfigurerar den virtuella datorn så att den kommunicerar med arbets ytan Log Analytics. Det kan ta några minuter att utföra installationen. 

5. Vid det här läget kan du, om du vill, välja en ny virtuell dator i listan för att aktivera funktionen.

## <a name="add-a-non-azure-machine-to-change-tracking-and-inventory"></a>Lägga till en icke-Azure-dator i Ändringsspårning och inventering

Så här aktiverar du icke-Azure-datorer för funktionen:

1. Installera [Log Analytics agent för Windows](../azure-monitor/agents/agent-windows.md) eller [Log Analytics agent för Linux](automation-linux-hrw-install.md), beroende på vilket operativ system du har. 

2. Navigera till ditt Automation-konto och gå till **lagret** eller **ändrings spårning** under **konfigurations hantering**. 

3. Klicka på **hantera datorer**. Du får en lista över datorer som rapporterar till din Log Analytics arbets yta som inte har Ändringsspårning och inventering aktive rad. Välj lämpligt alternativ för din miljö:

    * **Aktivera på alla tillgängliga datorer** – med det här alternativet aktive ras funktionen på alla datorer som rapporterar till din Log Analytics arbets yta just nu.
    * **Aktivera på alla tillgängliga datorer och framtida datorer** – med det här alternativet aktive ras funktionen på alla datorer som rapporterar till Log Analytics arbets ytan och därefter på alla kommande datorer som läggs till i arbets ytan.
    * **Aktivera på valda datorer** – med det här alternativet aktive ras funktionen bara på de datorer som du har valt.

    ![Hantera datorer](./media/automation-tutorial-installed-software/manage-machines.png)

## <a name="view-installed-software"></a>Visa installerade program

När Ändringsspårning-och inventerings funktionen har Aktiver ATS kan du visa resultatet på inventerings sidan.

1. I ditt Automation-konto väljer du **inventering** under **konfigurations hantering**.

2. På sidan Inventering klickar du på fliken **Programvara**.

3. Observera en tabell med en lista över den program vara som har hittats. Programvaran grupperas efter programnamn och version. Utförlig information för varje programpost visas i tabellen. Informationen omfattar programvarans namn, version, utgivare, senaste uppdateringstid (den senaste uppdateringstid som rapporterats av en dator i gruppen) och datorer (antalet datorer som har programvaran).

    ![Programvaruinventering](./media/automation-tutorial-installed-software/inventory-software.png)

4. Klicka på en rad om du vill visa egenskaperna för programposterna och namnen på datorerna som innehåller programvaran.

5. Om du vill söka efter en specifik programvara eller en grupp med programvara kan du söka i textrutan rakt ovanför programvarulistan.
Med filtret kan du söka utifrån programvarans namn, version eller utgivare. Om du till exempel söker **contoso** returnerar all program vara med namn, utgivare eller version som innehåller **contoso**.

## <a name="search-inventory-logs-for-installed-software"></a>Sök inventeringsloggar för installerad programvara

Ändringsspårning och inventeringen genererar loggdata som skickas till Azure Monitor loggar. Om du vill söka i loggarna genom att köra frågor väljer du **Log Analytics** överst på sidan inventering. Inventerings data lagras under typen `ConfigurationData` .

I följande exempel Log Analytics Query returnerar inventerings resultatet för utgivaren Microsoft Corporation.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| where Publisher == "Microsoft Corporation"
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
```

Mer information om hur du kör och söker efter loggfiler i Azure Monitor-loggar finns i [Azure Monitor-loggar](../azure-monitor/logs/log-query-overview.md).

## <a name="see-the-software-inventory-for-a-single-machine"></a>Se program varu inventeringen för en enskild dator

Om du vill se programvaruinventeringen för en enda dator kan du få åtkomst till Inventering från resurssidan för den virtuella Azure-datorn eller använda Azure Monitor-loggar för att filtrera fram motsvarande dator. I följande exempel Log Analytics Query returnerar listan över program vara för en dator med namnet **ContosoVM**.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
| summarize by Publisher, SoftwareName
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du visar program varu inventering:

> [!div class="checklist"]
> * Aktivera Ändringsspårning och inventering
> * Aktivera en virtuell Azure-dator
> * Aktivera en virtuell dator utan Azure
> * Visa installerade program
> * Sök inventeringsloggar för installerad programvara

Fortsätt till översikten över funktionen Ändringsspårning och inventering för att lära dig mer om det.

> [!div class="nextstepaction"]
> [Översikt över Ändringsspårning och inventering](change-tracking/overview.md)
