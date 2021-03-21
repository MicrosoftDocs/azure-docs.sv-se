---
title: Felsöka ändringar på en virtuell Azure-dator i Azure Automation | Microsoft Docs
description: Den här artikeln beskriver hur du felsöker ändringar på en virtuell Azure-dator.
services: automation
ms.subservice: change-inventory-management
keywords: ändring, spårning, ändrings spårning, inventering, automatisering
ms.date: 12/05/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: a223ac4296dd160bbdd904e1d3443552d4f49a23
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "100587930"
---
# <a name="troubleshoot-changes-on-an-azure-vm"></a>Felsöka ändringar på en virtuell Azure-dator

I den här självstudien får du lära dig att felsöka ändringar i en virtuell Azure-dator. Genom att aktivera Ändringsspårning och inventering kan du spåra ändringar i program vara, filer, Linux-daemon, Windows-tjänster och Windows-registernycklar på dina datorer.
Om du identifierar dessa konfigurationsändringar kan du få hjälp med att precisera driftproblem i miljön.

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Aktivera Ändringsspårning och inventering för en virtuell dator
> * Sök i ändringsloggar efter stoppade enheter
> * Konfigurera spårning av ändringar
> * Aktivera aktivitetslogganslutning
> * Utlösa en händelse
> * Visa ändringar
> * Konfigurera varningar

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett [Automation-konto](./index.yml) för att lagra övervakare och åtgärds-Runbooks och övervaknings aktiviteten.
* En [virtuell dator](../virtual-machines/windows/quick-create-portal.md) som ska aktive ras för funktionen.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Aktivera Ändringsspårning och inventering

Först måste du aktivera Ändringsspårning och inventering för den här självstudien. Om du tidigare har aktiverat funktionen behövs inte det här steget.

>[!NOTE]
>Om fälten är nedtonade är en annan automatiserings funktion aktive rad för den virtuella datorn och du måste använda samma arbets yta och Automation-konto.

1. Välj **virtuella datorer** och välj en virtuell dator i listan.
2. På den vänstra menyn väljer du **inventering** under **åtgärder**. Sidan inventering öppnas.

    ![Aktivera ändring](./media/automation-tutorial-troubleshoot-changes/enableinventory.png)

3. Välj arbets ytan [Log Analytics](../azure-monitor/logs/log-query-overview.md) . Den här arbets ytan samlar in data som genereras av funktioner som Ändringsspårning och inventering. Arbetsytan tillhandahåller en enda plats för att granska och analysera data från flera källor.

    [!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

4. Välj det Automation-konto som ska användas.

5. Konfigurera platsen för distributionen.

5. Klicka på **Aktivera** för att distribuera funktionen för din virtuella dator. 

Under installationen tillhandahålls den virtuella datorn med Log Analytics-agenten för Windows och en [hybrid Runbook Worker](automation-hybrid-runbook-worker.md). Det kan ta upp till 15 minuter att aktivera Ändringsspårning och inventering. Under tiden ska du inte stänga webbläsaren.

När funktionen har Aktiver ATS flödar information om installerad program vara och ändringar på den virtuella datorn till Azure Monitor loggar.
Det kan ta mellan 30 minuter och 6 timmar innan data blir tillgängliga för analys.

## <a name="use-change-tracking-and-inventory-in-azure-monitor-logs"></a>Använda Ändringsspårning och inventering i Azure Monitor loggar

Ändringsspårning och inventeringen genererar loggdata som skickas till Azure Monitor loggar. Om du vill söka i loggarna genom att köra frågor väljer du **Log Analytics** överst på sidan ändrings spårning. Ändrings spårnings data lagras under typen `ConfigurationChange` .

I följande exempel Log Analytics Query returnerar alla Windows-tjänster som har stoppats.

```loganalytics
ConfigurationChange
| where ConfigChangeType == "WindowsServices" and SvcState == "Stopped"
```

Mer information om hur du kör och söker efter loggfiler i Azure Monitor-loggar finns i [Azure Monitor-loggar](../azure-monitor/logs/log-query-overview.md).

## <a name="configure-change-tracking"></a>Konfigurera spårning av ändringar

Med ändrings spårning väljer du de filer och register nycklar som ska samlas in och spåras med hjälp av **redigerings inställningarna** överst på sidan för ändrings spårning på den virtuella datorn. Du kan lägga till Windows-registernycklar, Windows-filer eller Linux-filer som ska spåras på konfigurations sidan för arbets ytan.

> [!NOTE]
> Både ändrings spårning och inventering använder samma samlings inställningar, och inställningarna konfigureras på en arbets ytans nivå.

### <a name="add-a-windows-registry-key"></a>Lägg till en register nyckel för Windows

1. På fliken **Windows-registret** väljer du **Lägg till**. 

1. På sidan Lägg till Windows-register för Ändringsspårning anger du informationen för nyckeln som ska spåras och klickar på **Spara**

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |Enabled     | Fastställer om inställningen tillämpas        |
    |Objektnamn     | Eget namn på filen som ska spåras        |
    |Group     | Ett gruppnamn för att gruppera filer logiskt        |
    |Windows-registernyckel   | Sökvägen för att söka efter filen Till exempel: ”HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup”      |

### <a name="add-a-windows-file"></a>Lägga till en Windows-fil

1. På fliken **Windows-filer** väljer du **Lägg till**. 

1. På sidan Lägg till Windows-fil för Ändringsspårning anger du informationen för filen eller katalogen som ska spåras och klickar på **Spara**

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |Enabled     | Fastställer om inställningen tillämpas        |
    |Objektnamn     | Eget namn på filen som ska spåras        |
    |Group     | Ett gruppnamn för att gruppera filer logiskt        |
    |Ange sökväg     | Sökvägen för att söka efter filen Till exempel: "c:\temp\\\*.txt"<br>Du kan också använda miljövariabler som "%winDir%\System32\\\*.*"         |
    |Rekursion     | Avgör om rekursion används när du letar efter objektet som ska spåras.        |
    |Ladda upp filinnehåll för alla inställningar| Aktiverar eller inaktiverar uppladdning av filinnehåll vid spårade ändringar. Tillgängliga alternativ: **True** eller **False**.|

### <a name="add-a-linux-file"></a>Lägga till en Linux-fil

1. På fliken **Linux-filer** väljer du **Lägg till**. 

1. På sidan Lägg till Linux-fil för Ändringsspårning anger du informationen för filen eller katalogen som ska spåras och klickar på **Spara**.

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |Enabled     | Fastställer om inställningen tillämpas        |
    |Objektnamn     | Eget namn på filen som ska spåras        |
    |Group     | Ett gruppnamn för att gruppera filer logiskt        |
    |Ange sökväg     | Sökvägen för att söka efter filen Till exempel: ”/etc/*.conf”       |
    |Sökvägstyp     | Typ av objekt som ska spåras, möjliga värden är Fil och Katalog        |
    |Rekursion     | Avgör om rekursion används när du letar efter objektet som ska spåras.        |
    |Använda Sudo     | Den här inställningen styr om sudo ska användas vid sökningen efter objektet.         |
    |Länkar     | Den här inställningen styr hur symboliska länkar ska hanteras när de passerar kataloger.<br> **Ignorera** – Ignorerar symboliska länkar och inkluderar inte refererade filer/kataloger<br>**Följ** – Följer de symboliska länkarna under rekursion och inkluderar refererade filer/kataloger<br>**Hantera** – Följer de symboliska länkarna och tillåter ändring av behandling av returnerat innehåll      |
    |Ladda upp filinnehåll för alla inställningar| Aktiverar eller inaktiverar uppladdning av filinnehåll vid spårade ändringar. Tillgängliga alternativ: True eller False.|

   > [!NOTE]
   > Du rekommenderas inte att **Hantera** värdet för **Links** -egenskapen. Hämtning av filinnehåll stöds inte.

## <a name="enable-activity-log-connection"></a>Aktivera aktivitetslogganslutning

1. Från sidan Ändringsspårning på din virtuella dator väljer du **Hantera aktivitetslogganslutning**. 

2. På sidan Azure aktivitets logg klickar du på **Anslut** för att ansluta ändringsspårning och inventering till Azure aktivitets loggen för den virtuella datorn.

3. Gå till översikts sidan för den virtuella datorn och välj **stoppa** för att stoppa den virtuella datorn. 

4. När du uppmanas väljer du **Ja** för att stoppa den virtuella datorn. 

5. När den virtuella datorn har frigjorts väljer du **Starta** för att starta om den. Att stoppa och starta en virtuell dator loggar en händelse i aktivitets loggen. 

## <a name="view-changes"></a>Visa ändringar

1. Gå tillbaka till sidan ändrings spårning och välj fliken **händelser** längst ned på sidan. 

2. Efter ett tag visas ändrings spårnings händelser i diagrammet och tabellen. Diagrammet visar ändringar som har skett över tid. Linje diagrammet överst visar Azure aktivitets logg händelser. Varje rad i stapeldiagram representerar en annan typ av spårnings bara ändring. Dessa typer är Linux-daemon, filer, Windows-registernycklar, program vara och Windows-tjänster. På fliken Ändra visas information om de ändringar som visas, med den senaste ändringen som visas först.

    ![Visa händelser i portalen](./media/automation-tutorial-troubleshoot-changes/viewevents.png)

3. Observera att det har gjorts flera ändringar i systemet, inklusive ändringar av tjänster och program vara. Du kan använda filtren högst upp på sidan för att filtrera resultatet efter **Typ av ändring** eller efter ett tidsintervall.

    ![Lista över ändringar i den virtuella datorn](./media/automation-tutorial-troubleshoot-changes/change-tracking-list.png)

4. Välj en **WindowsServices** -ändring. Det här alternativet öppnar sidan ändrings information och visar information om ändringen och värdena före och efter ändringen. I den här instansen stoppades tjänsten Software Protection.

    ![Visa information om ändringar i portalen](./media/automation-tutorial-troubleshoot-changes/change-details.png)

## <a name="configure-alerts"></a>Konfigurera varningar

Det kan vara användbart att granska ändringar i Azure-portalen, men det är bättre att kunna få aviseringar när en ändring sker, till exempel en stoppad tjänst. Nu ska vi lägga till en avisering för en stoppad tjänst. 

1. I Azure Portal går du till **övervaka**. 

2. Välj **aviseringar** under **delade tjänster** och klicka på **+ ny varnings regel**.

3. Klicka på **Markera** för att välja en anslutning. 

4. På sidan Välj en resurs väljer du **Log Analytics** från List Rute menyn **Filtrera efter resurs typ** . 

5. Välj din Log Analytics arbets yta och klicka sedan på **färdig**.

    ![Välj en resurs](./media/automation-tutorial-troubleshoot-changes/select-a-resource.png)

6. Klicka på **Lägg till villkor**.

7. I tabellen på sidan Konfigurera signal logik väljer du **anpassad loggs ökning**. 

8. Ange följande fråga i text rutan Sök fråga:

    ```loganalytics
    ConfigurationChange | where ConfigChangeType == "WindowsServices" and SvcName == "W3SVC" and SvcState == "Stopped" | summarize by Computer
    ```

    Den här frågan returnerar de datorer där W3SVC-tjänsten stoppades i det angivna tidsintervallet.

9. Ange **0** för **tröskel** under **aviserings logik**. När du är klar klickar du på **klar**.

    ![Konfigurera signallogiken](./media/automation-tutorial-troubleshoot-changes/configure-signal-logic.png)

10. Välj **Skapa ny** under **Åtgärds grupper**. En åtgärdsgrupp är en grupp av åtgärder som kan användas i flera aviseringar. Dessa åtgärder kan inkludera, men är inte begränsade till, e-postmeddelanden, runbooks, webhooks och mycket mer. Mer information om åtgärds grupper finns i [skapa och hantera åtgärds grupper](../azure-monitor/alerts/action-groups.md).

11. Under **Aviseringsinformation** anger du ett namn och en beskrivning för aviseringen. 

12. Ställ in **Allvarlighetsgrad** på **Information (Sev 2)**, **Varning (Sev 1)** eller **Kritisk (Sev 0)**.

13. I rutan **Åtgärdsgruppnamn** anger du ett namn för aviseringen och ett kortnamn. Det korta namnet används i stället för ett fullständigt åtgärdsgruppnamn när meddelanden skickas med den här gruppen.

14. För **åtgärder** anger du ett namn för åtgärden, t. ex. **e-postadministratörer**. 

15. För **Åtgärds typ** väljer du **e-post/SMS/push/röst**. 

16. Välj **Redigera information** om du vill ha mer **information**.

    ![Lägg till åtgärdsgrupp](./media/automation-tutorial-troubleshoot-changes/add-action-group.png)

17. I fönstret e-post/SMS/push/Voice anger du ett namn, markerar kryss rutan **e-** postadress och anger sedan en giltig e-postadress. När du är färdig klickar du på **OK** i fönstret och sedan på **OK** på sidan Lägg till åtgärds grupp.

18. Om du vill anpassa ämnet för aviserings meddelandet väljer du **Anpassa åtgärder**. 

19. Välj **e-postämne** för **Skapa regel** och välj sedan **skapa aviserings regel**. Varningen berättar när en distribution lyckas och vilka datorer som var en del av denna uppdaterade distributionskörning. Följande bild är ett exempel på ett e-postmeddelande som tas emot när W3SVC-tjänsten stoppas.

    ![Skärm dum par visar ett e-postmeddelande som tas emot när d-tjänsterna på d-serien stoppas.](./media/automation-tutorial-troubleshoot-changes/email.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Aktivera Ändringsspårning och inventering för en virtuell dator
> * Sök i ändringsloggar efter stoppade enheter
> * Konfigurera spårning av ändringar
> * Aktivera aktivitets logg anslutning
> * Utlösa en händelse
> * Visa ändringar
> * Konfigurera varningar

Fortsätt till översikten över funktionen Ändringsspårning och inventering för att lära dig mer om det.

> [!div class="nextstepaction"]
> [Översikt över Ändringsspårning och inventering](change-tracking/overview.md)
