---
title: Felsöka problem med hög CPU-användning för virtuella Azure Windows-datorer
description: .
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: mnanda
manager: dcscontentpm
editor: v-jesits
tags: azure-resource-manager
ms.assetid: 9971cc26-916f-4e49-83cd-71eca74804f0
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 9/24/2020
ms.author: mnanda
ms.openlocfilehash: 124650f4570608efabba3d8002c14ad06c4782ad
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571499"
---
# <a name="troubleshoot-high-cpu-issues-for-azure-windows-virtual-machines"></a>Felsöka problem med hög CPU-användning för virtuella Azure Windows-datorer

## <a name="summary"></a>Sammanfattning

Prestanda problem uppstår i olika operativ system eller program, och varje problem kräver en unik metod för fel sökning. De flesta av dessa problem kretsar kring processor, minne, nätverk och indata/utdata (I/O) som viktiga platser där problemet uppstår. Vart och ett av dessa områden genererar olika symptom (ibland samtidigt) och kräver en annan diagnos och lösning.

Den här artikeln beskriver problem med hög processor användning som inträffar i Azure Virtual Machines (VM) som kör Windows-operativsystemet.

### <a name="high-cpu-issues-on-azure-windows-vms"></a>Problem med hög processor på virtuella Windows-datorer i Azure

Förutom i/O-och nätverks latens-problemen kräver processor-och minnes fel sökning samma verktyg och steg som de lokala servrarna. Ett av de verktyg som Microsoft vanligt vis stöder är PerfInsights (tillgängligt för både Windows och Linux). PerfInsights kan tillhandahålla en diagnostik för bästa praxis för Azure VM i en användarvänlig rapport. PerfInsights är också ett wrapper-verktyg som kan användas för att samla in perfmon-, XPerf-och Netmon-data, beroende på vilka flaggor som valts i verktyget.

De flesta av dina befintliga prestanda fel söknings verktyg, till exempel perfmon eller Procmon, som används för lokala servrar fungerar på virtuella Azure-datorer i Windows. PerfInsights är dock uttryckligen utformad för virtuella Azure-datorer för att ge mer insikter, inklusive Azures bästa praxis, metod tips för hög upplöst I/O-latens, processor-och minnes-flikar och så vidare.

Oavsett om det körs som User-Mode eller kernelläge, kräver alla trådar i en aktiv process CPU-cykler för att köra koden som den bygger på. Många problem är direkt relaterade till arbets belastningen. Den typ av arbets belastning som finns på serverns resurs förbrukning, inklusive CPU.

#### <a name="common-factors"></a>Vanliga faktorer

Följande faktorer är vanliga i en hög CPU-situation:

- En nyligen utförd kod ändring eller distribution som gäller för appar som Internet Information Services (IIS), Microsoft SharePoint, Microsoft SQL Server eller program från tredje part.

- En ny uppdatering som kan vara relaterad till en uppdatering på operativ system nivå eller till kumulativa uppdateringar och korrigeringar på program nivå.

- En fråga som ändrar eller inaktuella index. SQL Server och Oracle data Tier-program har också en optimering av frågeplan som en annan faktor. Data ändringar eller brist på lämpliga index kan orsaka att flera frågor blir mer beräknings intensiva.

- Azure VM-/regionsspecifika. Det finns vissa processer som RDAgent och särskilda processer som övervaknings agent, MMA-agent eller säkerhets klient som kan orsaka hög processor förbrukning. De här processerna måste visas antingen från en konfiguration eller kända problem.

## <a name="troubleshoot-the-issue"></a>Felsöka problemet

Den här artikeln fokuserar på att isolera den problematiska processen. Ytterligare analyser är till för den process som driver hög CPU-förbrukning.

Om processen till exempel är SQL Server (sqlservr.exe), är nästa steg att analysera vilken fråga som använde flest CPU-cykler under en viss tids period.

### <a name="scope-the-issue"></a>Omfattning av problemet

Här följer några frågor att fråga när du felsöker problemet:

- Finns det något mönster för problemet? Till exempel uppstår det mycket processor problem vid en viss tidpunkt varje dag, vecka eller månad? I så fall kan du korrelera det här problemet med ett jobb, en rapport eller en användar inloggning?

- Startade problemet med hög processor efter en senaste kod ändring? Tillämpade du en uppdatering i Windows eller i ett program?

- Startade problemet med hög processor efter en förändring i arbets belastningen, till exempel en ökning av antalet användare, ett högre data flöde eller ett stort antal rapporter?

- För Azure var problemet med hög processor användning under något av följande villkor?

  - Efter en ny omdistribution eller omstart
  - När en SKU eller VM-typ har ändrats
  - När ett nytt tillägg har lagts till
  - När belastnings Utjämnings ändringarna har gjorts

### <a name="azure-caveats"></a>Azure-varningar

Förstå din arbets belastning. När du väljer en virtuell dator kan det hända att du funktionen underskattar det virtuella processor antalet (vCPU) när du tittar på den övergripande kostnaden för varje månad. Om din arbets belastning är beräknings intensiv kan du välja en mindre VM-SKU som har en eller två virtuella processorer kan orsaka problem med arbets belastningen. Testa olika konfigurationer för arbets belastningen för att fastställa den bästa dator kapaciteten som krävs.

Det finns vissa VM-serier, till exempel B-serien (burst-läge) som rekommenderas för kvalitets säkring (frågor och svar) och testning. Om du använder dessa serier i produktions miljön begränsas dator kapaciteten när processor krediterna är slut.

För kända program, till exempel SQL Server, Oracle, RDS (Fjärrskrivbordstjänster), Windows Virtual Desktop, IIS eller SharePoint, finns det Azure Best Practices-artiklar som innehåller rekommendationer för minimal konfiguration för dessa arbets belastningar.

### <a name="ongoing-high-cpu-issues"></a>Pågående frågor med hög processor

Om problemet inträffar just nu, är det här den bästa möjligheten att samla in process spårningen för att avgöra vad som orsakar problemet. Du kan använda befintliga verktyg som du har använt för lokala Windows-servrar för att hitta processen. Följande verktyg rekommenderas av Azure-supporten för virtuella Azure-datorer.

### <a name="perfinsights"></a>PerfInsights

PerfInsights är det rekommenderade verktyget från Azure-supporten för prestanda problem med virtuella datorer. Den har utformats för att kunna utnyttja bästa praxis och dedikerade analys flikar för processor, minne och I/O-diagram med hög upplösning. Du kan köra den OnDemand genom Azure Portal eller från den virtuella datorn. Du kan dela data med support teamet för Azure.

#### <a name="run-perfinsights"></a>Kör PerfInsights

PerfInsights är tillgängligt för både [Windows](./how-to-use-perfinsights.md) -och [Linux](./how-to-use-perfinsights-linux.md) -operativsystem. För Windows finns alternativen.

#### <a name="run-and-analyze-reports-through-azure-portal"></a>Köra och analysera rapporter via Azure Portal

När den [installeras via Azure Portal](./performance-diagnostics.md)installerar faktiskt ett tillägg på den virtuella datorn. Användare kan också installera PerfInsights som ett tillägg genom att gå direkt till [tillägg på VM-bladet](./performance-diagnostics-vm-extension.md)och sedan välja ett alternativ för prestanda diagnos.

#### <a name="azure-portal-option-1"></a>Azure Portal alternativ 1

Bläddra i bladet VM och välj alternativet **prestanda diagnos** . Du uppmanas att installera alternativet (använder tillägg) på den virtuella dator som du har valt.

  ![Installera prestandadiagnostik](./media/troubleshoot-high-cpu-issues-azure-windows-vm/1-install-performance-diagnostics.png)

#### <a name="azure-portal-option-2"></a>Azure Portal alternativ 2

Bläddra för att **diagnostisera och lösa problem** på VM-bladet och leta efter **prestanda problem för virtuella datorer**.

  ![Felsöka prestanda problem med virtuella datorer](./media/troubleshoot-high-cpu-issues-azure-windows-vm/2-troubleshoot-vm-performance-issues.png)

Om du väljer **Felsök** laddas installations skärmen för PerfInsights.

Om du väljer **Installera** tillhandahåller installationen olika samlings alternativ.

  ![Prestandaanalys](./media/troubleshoot-high-cpu-issues-azure-windows-vm/3-performance-analysis.png)

De numrerade alternativen i skärm bilden är relaterade till följande kommentarer:

1. För alternativet för **hög CPU** väljer du **prestanda analys** eller **Avancerat**.

2. När du lägger till symtom här läggs de till i rapporten, som hjälper dig att dela information med Azure-supporten.

3. Välj varaktighet för data insamling. För alternativet för hög CPU väljer du minst 15 minuter eller mer. I Azure Portal läge kan du samla upp till 15 minuters data. För längre perioder av samling måste du köra programmet som en körbar fil på den virtuella datorn.

4. Om du uppmanas av Azure-supporten för att samla in dessa data kan du lägga till biljett numret här. Det här fältet är valfritt.

5. Välj det här fältet om du vill godkänna licens avtalet för slutanvändare (EULA).

6. Välj det här fältet om du vill att den här rapporten ska vara tillgänglig för support teamet i Azure, vilket hjälper dig i det här fallet.

Rapporten lagras på ett av lagrings kontona under din prenumeration. Det finns att visa och hämta senare.

#### <a name="run-perfinsights-from-within-the-vm"></a>Kör PerfInsights inifrån den virtuella datorn

Den här metoden kan användas om du vill köra PerfInsights under längre perioder. [PerfInsights-artikeln](./how-to-use-perfinsights.md#how-do-i-run-perfinsights) innehåller en detaljerad genom gång av de olika kommandon och flaggor som krävs för att köra PerfInsights som körbar fil. I syfte att använda hög processor användning behöver du något av följande lägen:

- Avancerat scenario

  - `PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics`

- Scenario för långsam virtuell dator (prestanda)

  - `PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>`

Kommandoutdata visas i samma mapp som du sparade PerfInsights-körbara filen på.

#### <a name="what-to-look-for-in-the-report"></a>Vad du letar efter i rapporten

När du har kört rapporten beror platsen för innehållet på om den kördes via Azure Portal eller som en körbar fil. För båda alternativen får du åtkomst till den genererade loggmappen eller ladda ned (om Azure Portal) lokalt för analys.

### <a name="run-through-the-azure-portal"></a>Kör genom Azure Portal

  ![Prestanda diagnostik med hög påverkan](./media/troubleshoot-high-cpu-issues-azure-windows-vm/4-high-impact-performance-diagnostics.png)

  ![Hämta rapport](./media/troubleshoot-high-cpu-issues-azure-windows-vm/5-download-report.png)

#### <a name="run-from-within-vm"></a>Kör från virtuell dator

Mappstrukturen bör likna följande bilder:

  ![Välj utdata](./media/troubleshoot-high-cpu-issues-azure-windows-vm/6-select-output.png)

  ![Mappstruktur](./media/troubleshoot-high-cpu-issues-azure-windows-vm/7-folder-structure.png)

1. Eventuella ytterligare samlingar, till exempel perfmon, XPerf, Netmon, SMB-loggar, händelse loggar och så vidare, finns i mappen utdata.

1. Den faktiska rapporten tillsammans med analyser och rekommendationer.

1. För både prestanda (VMslow) och avancerad samlar rapporten in **perfmon** -information för varaktigheten för PerfInsights-körningen.

1. Händelse loggarna visar en snabb överblick över användbar information på system nivå eller process krasch.

#### <a name="where-to-start"></a>Här börjar du

Öppna PerfInsights-rapporten. Fliken **konstaterande** loggar alla avvikande värden vad gäller resursförbrukning. Om det finns instanser av hög CPU-användning, kommer fliken **konstaterandes** att kategorisera den antingen som hög påverkan eller medelhög påverkan.

  ![Resurser på effekt nivå](./media/troubleshoot-high-cpu-issues-azure-windows-vm/8-impact-level-resources.png)

I likhet med föregående exempel kördes PerfInsights i 30 minuter. Under den tiden har den markerade processen förbrukat CPU på den högre sidan. Om samma process hade körts under samlings tiden hade effekt nivån ändrats till **hög**.

Om du expanderar **resultat** händelsen visas flera viktiga uppgifter. Fliken listar processerna i fallande ordning, per **Genomsnittlig CPU** -förbrukning och visar om processen var relaterad till systemet, en Microsoft-ägd app (SQL, IIS) eller en process från tredje part.

#### <a name="more-details"></a>Mer information

Det finns en dedikerad underfliken under **CPU** som kan användas för detaljerad mönster analys, per kärna eller per process.

Fliken för de **vanligaste processor förbrukarna** har två separata avsnitt av intresse och du kan visa processor statistik per processor här. Program designen är ofta antingen Single-Threaded eller PIN-filer till en enda processor. I det här scenariot körs ett eller flera kärnor vid 100 procent, medan andra kärnor körs på förväntade nivåer. De här scenarierna är mer komplexa eftersom den genomsnittliga CPU: n på servern verkar köras som förväntat, men de processer som är fästa på kärnor som har hög användning går långsammare än förväntat.

  ![hög CPU-användning](./media/troubleshoot-high-cpu-issues-azure-windows-vm/9-high-cpu-usage.png)

Det andra avsnittet (lika viktigt) är de **vanligaste processor förbrukarna**. I det här avsnittet visas både process information och deras processor användnings mönster. Listan sorteras genom att ha höga genomsnittliga processor konsumenter överst.

  ![Tom tids krävande processor konsumenter](./media/troubleshoot-high-cpu-issues-azure-windows-vm/10-top-long-running-cpu-consumers.png)

De här två flikarna är tillräckligt stora för att ange sökvägen till nästa fel söknings steg. Beroende på processen som kör hög CPU-tillståndet måste du åtgärda frågorna som du har bett om tidigare. Processer som SQL Server (sqlservr.exe) eller IIS (w3wp.exe) kräver en detaljerad visning av frågor eller kod ändringar som orsakar det här tillståndet. För system processer som WMI eller Lsass.exe måste du följa en annan sökväg.

För Azure VM-relaterade processer som RDAgent, OMS och övervaknings tillägg körbara filer kan du behöva åtgärda en ny version eller version genom att få hjälp från support teamet för Azure.

### <a name="perfmon"></a>Perfmon

**Perfmon** är ett av de tidigaste verktygen för att felsöka ett resurs problem på Windows Server. Det ger inte en tydlig rapport som innehåller rekommendationer eller resultat. I stället måste användaren utforska insamlade data och använda ett särskilt filter under de olika räknar kategorierna.

PerfInsights samlar in perfmon som en extra logg för VMSlow och avancerade scenarier. Perfmon kan dock samlas in separat och har följande ytterligare fördelar:

- Den kan samlas in via fjärr anslutning.

- Den kan schemaläggas med hjälp av **uppgifter**.

- Den kan samlas in för längre varaktighet eller i kontinuerligt läge med hjälp av funktionen för överrullning.

Överväg samma exempel som visas i PerfInsights för att se hur perfmon visar dessa data. De begärda räknar kategorierna är följande:

- Processor information >% processor tid > _Total

- Process >% ProcessorTime > alla instanser

#### <a name="where-to-start"></a>Här börjar du

Utdatafilernas namn för perfmon har ett `.blg` fil namns tillägg. Du kan samla in dessa filer oberoende av varandra eller genom att använda PerfInsights. I den här diskussionen använder du perfmon `.blg` som ingår i PerfInsights-data och som samlats in enligt föregående exempel.

Det finns inga standard rapporter för användar klara som är tillgängliga i perfmon. Det finns olika vyer som ändrar diagram typen, men process filtreringen (eller det arbete som krävs för att identifiera orsaken-processer) är manuell.

> [!NOTE]
> [Verktyget PAL](https://github.com/clinthuffman/PAL) kan använda `.blg` filer och generera detaljerade rapporter.

Starta genom att välja kategorin **Lägg till räknare** .

1. Under **tillgängliga räknare** väljer du räknaren **% ProcessorTime** i kategorin **processor information** .

1. Välj **_Total** som innehåller statistik över alla kombinerade kärnor.

1. Välj **Lägg till**. I fönstret visas **% ProcessorTime** under  **tillagda räknare**.

  ![Lägg till processor tid](./media/troubleshoot-high-cpu-issues-azure-windows-vm/11-add-processor-time.png)

När räknarna har lästs in visas linje trend diagram i tids ramen för samlingen. Du kan markera eller avmarkera räknarna. Hittills har du bara lagt till en räknare.

  ![Inställningar för prestanda övervakaren](./media/troubleshoot-high-cpu-issues-azure-windows-vm/12-performance-monitor-1.png)

Varje räknare har **medel**, **lägsta** och **högsta** värden. Fokusera på både **genomsnitts** -och **Max** värden, eftersom det genomsnittliga värdet kan variera beroende på data insamlingens varaktighet. Om aktiviteten med hög processor setts i 10 minuter medan den övergripande samlingen var 40 minuter, blir de genomsnittliga värdena mycket lägre.

I föregående trend diagram visas att den **totala processorn** var i intervallet 80 procent under cirka 15 minuter.

#### <a name="identify-the-process"></a>Identifiera processen

Vi har identifierat att servern hade en hög processor förbrukning under en angiven tids period, men vi har ännu inte identifierat driv rutinen. Till skillnad från PerfInsights måste du manuellt söka efter orsaken-processen i det här fallet.

För den här uppgiften måste du avmarkera eller ta bort de **% ProcessorTime** räknare som du har lagt till och sedan lägga till en ny kategori:

- Process >% ProcessorTime > alla instanser

Den här kategorin läser in räknare för alla processer som körs vid den tidpunkten.

  ![Lägg till räknare](./media/troubleshoot-high-cpu-issues-azure-windows-vm/13-add-counters.png)

På en typisk produktions dator kan hundratals eller processer köras. Det kan därför ta en stund att ta bort varje räknare som verkar ha ett lågt eller Plant trend diagram.

För att påskynda den här processen använder du vyn **histogram** och ändrar visnings typen från **linje** till **histogram**, vilket ger dig ett stapeldiagram. Det är enklare att välja de processer som upplever hög CPU-användning under samlings tiden.

Eftersom det alltid kommer att finnas en stapel för **Total**, fokuserar du på staplar som visar en hög uttömdhet. Du kan ta bort de andra staplarna för att rensa vyn. Växla nu tillbaka till vyn **linje** .

  ![Indikatorer för prestanda övervakaren](./media/troubleshoot-high-cpu-issues-azure-windows-vm/14-performance-monitor-2.png)

Nu är det enklare att fånga orsaken-processen. Som standard är **Max** -och **min** -värden multiplar av antalet kärnor på servern eller trådar i processen.

  ![Resultat för prestanda övervakaren](./media/troubleshoot-high-cpu-issues-azure-windows-vm/15-performance-monitor-3.png)

Listan över tillgängliga verktyg slutar inte på PerfInsights för perfmon. Du har åtkomst till andra verktyg, till exempel **ProcessMonitor** (Procmon) eller **XPerf**. Det finns många verktyg från tredje part som du kan använda vid behov.

### <a name="azure-monitoring-tools"></a>Övervakningsverktyg i Azure

Virtuella Azure-datorer har pålitliga mått som innehåller grundläggande information, till exempel CPU, nätverks-I/O och I/O byte. För avancerade mått, till exempel Azure Monitor, behöver du bara göra några val för att konfigurera och använda ett lagrings konto som du anger.

#### <a name="basic-default-counters"></a>Basic (standard) räknare

  ![Diagram mått](./media/troubleshoot-high-cpu-issues-azure-windows-vm/16-chart-metrics.png)

#### <a name="enabling-azure-monitor"></a>Aktivera Azure Monitor

När du har aktiverat Azure Monitor mått installerar programmet ett tillägg på den virtuella datorn och börjar sedan samla in detaljerade mått som innehåller perfmon-räknare.

  ![Lagringskonto för diagnostik](./media/troubleshoot-high-cpu-issues-azure-windows-vm/17-diagnostics-storage-account.png)

De **grundläggande** räknar kategorierna anges som **standard**. Du kan dock också ange en **anpassad** samling.

  ![Prestandaräknare](./media/troubleshoot-high-cpu-issues-azure-windows-vm/18-performance-counters.png)

När inställningarna har Aktiver ATS kan du visa dessa **gäst** räknare i avsnittet **mått** . Du kan också ställa in **aviseringar** (inklusive e-postmeddelanden) om måtten når ett visst tröskelvärde.

  ![Mått namn område](./media/troubleshoot-high-cpu-issues-azure-windows-vm/19-metrics-namespace.png)

Mer information om hur du använder Azure Monitor för att hantera virtuella Azure-datorer finns i [övervaka virtuella Azure-datorer med Azure Monitor](../../azure-monitor/vm/monitor-vm-azure.md).

### <a name="reactive-troubleshooting"></a>Reaktiv fel sökning

Om problemet redan har inträffat måste du identifiera vad som orsakade det höga CPU-problemet på den första platsen. Den reaktiva virtualiseringsprodukter från kan vara knepig. Data insamlings läget är inte lika användbart eftersom problemet redan har inträffat.

Om det här problemet var ett engångs inträffar det kan vara svårt att avgöra vilken app som orsakade det. Om den virtuella Azure-datorn har kon figurer ATS för att använda OMS eller annan diagnostisk spårning kan du fortfarande få insikter om vad som orsakade problemet.

Om du hanterar ett upprepat mönster samlar du in data under den tid som problemet troligt vis kommer att inträffa härnäst.

PerfInsights har inte en **schemalagd körnings** funktion än. Perfmon kan dock köras och schemaläggas via kommando raden.

### <a name="logman-command"></a>Logman-kommando

Kommandot **logman Create Counter** används för att köra perfmon-insamling via kommando raden för att schemalägga den via **aktivitets hanteraren**, eller för att köra den via en fjärr anslutning.

**Exempel** (inklusive fjärrsamlings läge)

`Logman create counter LOGNAME -u DOMAIN\USERNAME * -f bincirc -v mmddhhmm -max 300 -c "\\SERVERNAME\LogicalDisk(*)\*" "\\SERVERNAME\Memory\*" "\\SERVERNAME\Network Interface(*)\*" "\\SERVERNAME\Paging File(*)\*" "\\SERVERNAME\PhysicalDisk(*)\*" "\\SERVERNAME\Process(*)\*" "\\SERVERNAME\Redirector\*" "\\SERVERNAME\Server\*" "\\SERVERNAME\System\*" "\\SERVERNAME\Terminal Services\*" "\\SERVERNAME\Processor(*)\*" "\\SERVERNAME\Cache\*" -si 00:01:00`

Logman.exe kan också startas från en peer Azure VM-dator i samma VNET.

Mer information om dessa parametrar finns i [logman Create Counter](/windows-server/administration/windows-commands/logman-create-counter).

När perfmon-data har samlats in medan problemet inträffar är de återstående stegen för att analysera data desamma som de som diskuterats tidigare.

## <a name="conclusion"></a>Slutsats

För prestanda problem är det viktigt att förstå din arbets belastning för att lösa problemet. Alternativen på olika VM SKU: er och olika disk lagrings alternativ måste utvärderas genom att fokusera på produktions belastningen. Processen för att testa lösningar på olika virtuella datorer kan hjälpa dig att fatta det bästa beslutet.

Eftersom användar åtgärderna och mängden data varierar bör du alltid ha en buffert i datorns data behandling, nätverk och I/O-funktioner. Nu har alla plötsliga förändringar i arbets belastningen inte lika bra en påverkan.

Om du förväntar dig att arbets belastningen ökar snart kan du gå vidare till en högre SKU som har mer data behandlings kraft. Om arbets belastningen ska vara beräknings intensiv väljer du den virtuella datorns SKU: er.