---
title: Självstudie – hantera konfiguration av virtuella Windows-datorer i Azure
description: I den här självstudien får du lära dig att identifiera ändringar och hantera paket uppdateringar på en virtuell Windows-dator
author: cynthn
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: b28d7ee5d2eeb1015695e32e5918bd94f9051050
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736675"
---
# <a name="tutorial-monitor-changes-and-update-a-windows-virtual-machine-in-azure"></a>Självstudie: övervaka ändringar och uppdatera en virtuell Windows-dator i Azure

Med Azure [ändringsspårning](../../automation/change-tracking/overview.md) och [uppdateringshantering](../../automation/update-management/overview.md)kan du enkelt identifiera ändringar i dina virtuella Windows-datorer i Azure och hantera uppdateringar av operativ system för de virtuella datorerna.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Hantera Windows-uppdateringar.
> * Övervaka ändringar och inventering.

## <a name="open-azure-cloud-shell"></a>Öppna Azure Cloud Shell

Azure Cloud Shell är ett kostnads fritt interaktivt gränssnitt som du kan använda för att köra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerade och konfigurerade för användning med ditt Azure-konto.

Om du vill öppna ett kodblock i Cloud Shell väljer du **testa det** från det övre högra hörnet i kod blocket.

Du kan också öppna Cloud Shell på en separat webbläsare-flik genom att gå till [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Välj **Kopiera** för att kopiera kodblock, klistra in dem på fliken Cloud Shell och välj RETUR-tangenten för att köra koden.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

För att konfigurera övervaknings- och uppdateringshantering i Azure i den här självstudiekursen behöver du en virtuell Windows-dator i Azure.

Ange först ett administratörsanvändarnamn och lösenord för den virtuella datorn med [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Skapa sedan den virtuella datorn med [New-AzVM](/powershell/module/az.compute/new-azvm). I följande exempel skapas en virtuell dator med namnet `myVM` på `East US` platsen. Om de inte redan finns skapas resurs gruppen `myResourceGroupMonitor` och stödda nätverks resurser:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Det tar några minuter att skapa resurserna och den virtuella datorn.

## <a name="manage-windows-updates"></a>Hantera Windows-uppdateringar

Uppdateringshantering hjälper dig att hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer i Windows. Direkt från din virtuella dator kan du snabbt:

- Bedöma statusen för tillgängliga uppdateringar.
- Schemalägga installation av obligatoriska uppdateringar.
- Granska distributions resultaten för att kontrol lera att uppdateringarna har tillämpats på den virtuella datorn.

Pris information finns i avsnittet [om automatiserings priser för uppdaterings hantering](https://azure.microsoft.com/pricing/details/automation/).

### <a name="enable-update-management"></a>Aktivera uppdateringshantering

Så här aktiverar du Uppdateringshantering för din virtuella dator:

1. Välj **virtuella datorer** på sidan längst till vänster i fönstret.
1. Välj en virtuell dator i listan.
1. I fönstret **drift** i fönstret VM väljer du **uppdaterings hantering**.
1. Fönstret **aktivera uppdateringshantering** öppnas.

Verifiering görs för att avgöra om Uppdateringshantering har Aktiver ATS för den här virtuella datorn. Verifieringen innehåller kontroller för en Log Analytics arbets yta, för ett länkat Automation-konto och för om lösningen finns i arbets ytan.

Du använder en [Log Analytics](../../azure-monitor/log-query/log-query-overview.md) arbets yta för att samla in data som genereras av funktioner och tjänster, till exempel uppdateringshantering. Arbetsytan tillhandahåller en enda plats för att granska och analysera data från flera källor.

Om du vill utföra ytterligare åtgärder på virtuella datorer som kräver uppdateringar kan du använda Azure Automation för att köra Runbooks mot virtuella datorer. Sådana åtgärder omfattar att ladda ned eller tillämpa uppdateringar.

Verifierings processen kontrollerar också om den virtuella datorn är etablerad med Microsoft Monitoring Agent (MMA) och automation Hybrid Runbook Worker. Du kan använda agenten för att kommunicera med den virtuella datorn och hämta information om uppdaterings statusen.

I fönstret **aktivera uppdateringshantering** väljer du Log Analytics arbets yta och Automation-konto och väljer sedan **Aktivera**. Det tar upp till 15 minuter innan lösningen har Aktiver ATS.

Alla följande krav som saknas vid onboarding läggs till automatiskt:

* [Log Analytics](../../azure-monitor/log-query/log-query-overview.md) arbets yta
* [Automation](../../automation/index.yml)
* En [hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md), som är aktiverat på den virtuella datorn

När lösningen har Aktiver ATS öppnas fönstret **uppdaterings hantering** . Konfigurera platsen, Log Analytics arbets ytan och automation-kontot som ska användas, och välj sedan **Aktivera**. Om dessa alternativ är nedtonade är en annan automatiserings lösning aktive rad för den virtuella datorn och denna lösnings arbets yta och Automation-konto måste användas.

![Aktivera Uppdateringshantering lösning](./media/tutorial-monitoring/manageupdates-update-enable.png)

Det kan ta upp till 15 minuter innan den Uppdateringshantering lösningen aktive ras. Under tiden ska du inte stänga webbläsaren. När lösningen har aktiverats flödar information om saknade uppdateringar på den virtuella datorn till Azure Monitor-loggar. Det kan ta mellan 30 minuter och 6 timmar innan data blir tillgängliga för analys.

### <a name="view-an-update-assessment"></a>Visa en uppdateringsbedömning

När Uppdateringshantering har Aktiver ATS visas fönstret **uppdaterings hantering** . När utvärderingen av uppdateringar har avslut ATS visas en lista med uppdateringar som saknas på fliken **uppdateringar som saknas** .

 ![Visa uppdateringsstatus](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Schemalägga en uppdateringsdistribution

För att installera uppdateringar schemalägger du en distribution som passar ditt schema och servicefönster. Du kan välja vilka uppdaterings typer som ska ingå i distributionen. Du kan till exempel ta med kritiska uppdateringar eller säkerhetsuppdateringar och exkludera samlade uppdateringar.

Om du vill schemalägga en ny uppdaterings distribution för den virtuella datorn väljer du **schema uppdaterings distribution** överst i fönstret **uppdaterings hantering** . I fönstret **ny uppdaterings distribution** anger du följande information:

| Alternativ | Beskrivning |
| --- | --- |
| **Namn** |Ange ett unikt namn som identifierar uppdaterings distributionen. |
|**Operativsystem**| Välj antingen **Linux** eller **Windows**.|
| **Grupper att uppdatera** |Definiera en fråga baserat på en kombination av prenumeration, resurs grupper, platser och taggar för virtuella datorer som finns på Azure. Den här frågan skapar en dynamisk grupp med virtuella datorer i Azure som ska ingå i distributionen. </br></br>Välj en befintlig sparad sökning för virtuella datorer som inte finns på Azure. Med den här sökningen kan du välja en grupp med de virtuella datorerna som ska ingå i distributionen. </br></br> Mer information finns i [dynamiska grupper](../../automation/update-management/configure-groups.md).|
| **Datorer som ska uppdateras** |Välj **Sparad sökning**, **importerad grupp** eller **datorer**.<br/><br/>Om du väljer **datorer** kan du välja enskilda datorer i den nedrullningsbara listan. Beredskapen för varje dator visas i kolumnen **Uppdatera agent beredskap** i tabellen.</br></br> Information om de olika metoderna för att skapa datorgrupper i Azure Monitor-loggar finns i [datorgrupper i Azure Monitor-loggar](../../azure-monitor/platform/computer-groups.md) |
|**Klassificering av uppdateringar**|Välj alla nödvändiga uppdaterings klassificeringar.|
|**Inkludera/exkludera uppdateringar**|Välj det här alternativet för att öppna fönstret **Inkludera/exkludera** . Uppdateringar som ska tas med och de som ska undantas finns på separata flikar. Mer information om hur inkludering hanteras finns i [Schemalägga en uppdaterings distribution](../../automation/update-management/deploy-updates.md#schedule-an-update-deployment). |
|**Schemainställningar**|Välj tid för start och välj antingen **en gång** eller **återkommande**.|
| **Före skript + efter skript**|Välj de skript som ska köras före och efter distributionen.|
| **Underhålls period** | Ange antalet minuter som ska uppdateras. Giltiga värden är mellan 30 och 360 minuter. |
| **Starta om kontroll**| Välj hur omstarter hanteras. Tillgängliga alternativ är:<ul><li>**Starta om vid behov**</li><li>**Starta alltid om**</li><li>**Starta aldrig om**</li><li>**Starta bara om**</li></ul>**Starta om om det krävs** standard valet. Om du väljer **endast omstart** installeras inte uppdateringar.|

När du har konfigurerat schemat klickar du på **skapa** för att återgå till status instrument panelen. Den **schemalagda** tabellen visar det distributions schema som du har skapat.

Du kan också skapa uppdaterings distributioner program mässigt. Information om hur du skapar en uppdaterings distribution med REST API finns i [program uppdaterings konfiguration – skapa](/rest/api/automation/softwareupdateconfigurations/create). Det finns också en exempel-Runbook som du kan använda för att skapa en veckovis uppdaterings distribution. Mer information om denna Runbook finns i [skapa en veckovis uppdaterings distribution för en eller flera virtuella datorer i en resurs grupp](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

### <a name="view-results-of-an-update-deployment"></a>Visa resultat för en uppdateringsdistribution

När den schemalagda distributionen har startats kan du se distributions status på fliken **uppdaterings distributioner** i fönstret **uppdaterings hantering** .

Om distributionen för närvarande körs visas statusen som "pågår". När åtgärden har slutförts ändras statusen till "lyckades". Men om eventuella uppdateringar i distributionen Miss lyckas är statusen "delvis misslyckades".

Välj slutförd uppdaterings distribution för att se instrument panelen för distributionen.

![Statusinstrumentpanel för Uppdatera distribution för specifik distribution](./media/tutorial-monitoring/manageupdates-view-results.png)

I panelen **uppdaterings resultat** visas en sammanfattning av det totala antalet uppdateringar och distributions resultat på den virtuella datorn. Tabellen till höger visar en detaljerad analys av varje uppdatering och installations resultaten. Varje resultat har något av följande värden:

* **Inget försök har gjorts**: uppdateringen är inte installerad. Det fanns inte tillräckligt med tid tillgängligt baserat på den definierade varaktigheten för underhålls perioden.
* **Lyckades**: uppdateringen lyckades.
* **Misslyckades**: uppdateringen misslyckades.

Välj **Alla loggar** om du vill se alla loggposter som har skapats för distributionen.

Välj panelen **utdata** om du vill se jobb strömmen för den Runbook som ansvarar för att hantera uppdaterings distributionen på den virtuella mål datorn.

Välj **fel** om du vill visa detaljerad information om eventuella distributions fel.

## <a name="monitor-changes-and-inventory"></a>Övervaka ändringar och inventering

Du kan samla in och visa en inventering av program varan, filer, Linux-daemonar, Windows-tjänster och Windows-registernycklar på dina datorer. Genom att spåra konfigurationerna för dina datorer kan du hitta drifts problem i din miljö och bättre förstå datorernas tillstånd.

### <a name="enable-change-and-inventory-management"></a>Aktivera ändrings-och inventerings hantering

Så här aktiverar du ändrings-och inventerings hantering för din virtuella dator:

1. Välj **virtuella datorer** på sidan längst till vänster i fönstret.
1. Välj en virtuell dator i listan.
1. Under **åtgärder** i fönstret VM väljer du antingen **inventering** eller **ändrings spårning**.
1. Fönstret **aktivera ändringsspårning och inventering** öppnas.

Konfigurera platsen, Log Analytics arbets ytan och automation-kontot som ska användas, och välj sedan **Aktivera**. Om alternativen visas som inaktiverade är en automatiserings lösning redan aktive rad för den virtuella datorn. I så fall måste den redan aktiverade arbets ytan och automation-kontot användas.

Även om lösningarna visas separat på menyn, är de samma lösning. När du aktiverar den ena aktiveras båda för den virtuella datorn.

![Aktivera Ändringsspårning och lager](./media/tutorial-monitoring/manage-inventory-enable.png)

När lösningen har Aktiver ATS kan det ta lite tid för inventering att samlas in på den virtuella datorn innan data visas.

### <a name="track-changes"></a>Spåra ändringar

Välj **ändringsspårning** på den virtuella datorn under **åtgärder** och välj sedan **Redigera inställningar**. Fönstret **ändringsspårning** öppnas. Välj typ av inställning som du vill spåra och klicka sedan på **+ Lägg till** för att konfigurera inställningarna.

Alternativen för tillgängliga inställningar för Windows är:

* Windows-registret
* Windows-filer

Detaljerad information om Ändringsspårning finns i [Felsöka ändringar på en virtuell dator](../../automation/automation-tutorial-troubleshoot-changes.md).

### <a name="view-inventory"></a>Visa inventering

Välj **inventering** under **åtgärder** på den virtuella datorn. På fliken **program vara** finns det en tabell som visar den program vara som har hittats. Information på hög nivå för varje program varu post visas i tabellen. Informationen omfattar program varans namn, version, utgivare och senaste uppdaterings tid.

![Visa inventering](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Övervaka aktivitets loggar och-ändringar

I fönstret **ändrings spårning** på din virtuella dator väljer du **Hantera aktivitets logg anslutning** för att öppna fönstret **Azure aktivitets logg** . Välj **Anslut** för att ansluta ändringsspårning till Azures aktivitets logg för den virtuella datorn.

När Ändringsspårning har Aktiver ATS går du till **översikts** fönstret för den virtuella datorn och väljer **stoppa** för att stoppa den virtuella datorn. När du uppmanas väljer du **Ja** för att stoppa den virtuella datorn. När den virtuella datorn har frigjorts väljer du **Starta** för att starta om den virtuella datorn.

Om en virtuell dator stoppas och startas om loggas en händelse i aktivitets loggen. Gå tillbaka till fönstret **ändrings spårning** och välj fliken **händelser** längst ned i fönstret. Efter en stund visas händelserna i diagrammet och tabellen. Du kan välja varje händelse för att visa detaljerad information om händelsen.

![Visa händelser i aktivitetsloggen](./media/tutorial-monitoring/manage-activitylog-view-results.png)

Det föregående diagrammet visar ändringar som har inträffat över tid. När du har lagt till en Azure aktivitets logg anslutning visar linje diagrammet överst Azure aktivitets logg händelser.

Varje rad i stapeldiagram representerar en annan typ av spårnings bara ändring. Dessa typer är Linux-daemon, filer, Windows-registernycklar, program vara och Windows-tjänster. På fliken **ändra** visas ändrings informationen. Ändringarna visas i den ordning när de inträffade, med den senaste ändringen som visas först.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du konfigurerat och granskat Ändringsspårning och Uppdateringshantering för den virtuella datorn. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en resurs grupp och en virtuell dator.
> * Hantera Windows-uppdateringar.
> * Övervaka ändringar och inventering.

Gå till nästa självstudie om du vill veta mer om övervakning av den virtuella datorn.

> [!div class="nextstepaction"]
> [Övervakning av virtuella datorer](tutorial-monitor.md)
