---
title: Fel söknings guide för Azure-filresurser
description: Felsök kända prestanda problem med Azure-filresurser. Identifiera möjliga orsaker och tillhör ande lösningar när dessa problem påträffas.
author: gunjanj
ms.service: storage
ms.topic: troubleshooting
ms.date: 11/16/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 9f858549f36d196c6412aec549d0ab2e2d864145
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2021
ms.locfileid: "103417679"
---
# <a name="troubleshoot-azure-file-shares-performance-issues"></a>Felsöka prestanda problem i Azure-filresurser

Den här artikeln innehåller några vanliga problem som rör Azure-filresurser. Det ger potentiella orsaker och lösningar när du stöter på de här problemen.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Hög latens, lågt data flöde och allmänna prestanda problem

### <a name="cause-1-share-was-throttled"></a>Orsak 1: resursen har begränsats

Begär Anden begränsas när i/O-åtgärder per sekund (IOPS), ingångs-eller utgående gränser för en fil resurs nås. Information om begränsningar för fil resurser för standard och Premium finns i [fil resurs-och fil skalnings mål](./storage-files-scale-targets.md#azure-file-share-scale-targets).

Du kan kontrol lera om din resurs har begränsats genom att komma åt och använda Azure-mått i portalen.

1. I Azure Portal går du till ditt lagrings konto.

1. Välj **mått** i den vänstra rutan under **övervakning**.

1. Välj **fil** som mått namn område för ditt lagrings konto omfång.

1. Välj **transaktioner** som mått.

1. Lägg till ett filter för **svars typ** och kontrol lera sedan för att se om några begär Anden har begränsats. 

    För standard fil resurser loggas följande svars typer om en begäran begränsas:

    - SuccessWithThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareIopsThrottlingError

    För Premium-filresurser loggas följande svars typer om en begäran begränsas:

    - SuccessWithShareEgressThrottling
    - SuccessWithShareIngressThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareEgressThrottlingError
    - ClientShareIngressThrottlingError
    - ClientShareIopsThrottlingError

    Om du vill veta mer om varje svars typ, se [mått mått](./storage-files-monitoring-reference.md#metrics-dimensions).

    ![Skärm bild av mått alternativen för Premium-filresurser som visar egenskaps filtret "svars typ".](media/storage-troubleshooting-premium-fileshares/metrics.png)

    > [!NOTE]
    > Information om hur du får en avisering finns i avsnittet ["så här skapar du en avisering om en fil resurs är begränsad"](#how-to-create-an-alert-if-a-file-share-is-throttled) senare i den här artikeln.

### <a name="solution"></a>Lösning

- Om du använder en standard fil resurs aktiverar du [stora fil resurser](./storage-files-how-to-create-large-file-share.md?tabs=azure-portal) på ditt lagrings konto. Stora fil resurser har stöd för upp till 10 000 IOPS per resurs.
- Om du använder en Premium-filresurs ökar du den etablerade fil resurs storleken för att öka IOPS-gränsen. Mer information finns i avsnittet [om etablering av Premium-filresurser](./understanding-billing.md#provisioned-model).

### <a name="cause-2-metadata-or-namespace-heavy-workload"></a>Orsak 2: metadata eller namnrymd med hög belastning

Om de flesta av dina begär Anden är metadata-centrerade (till exempel CreateFile, OpenFile, closefile, queryinfo eller querydirectory), kommer svars tiden att bli sämre än för Läs-och skriv åtgärder.

För att avgöra om de flesta av dina begär Anden är metadata-centrerade, börja genom att följa steg 1-4 som tidigare beskrivs i orsak 1. I steg 5, i stället för att lägga till ett filter för **svars typ**, lägger du till ett egenskaps filter för **API-namn**.

![Skärm bild av mått alternativen för Premium-filresurser som visar egenskaps filtret "API-namn".](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Lösning

- Kontrol lera om programmet kan ändras för att minska antalet metadata-åtgärder.
- Lägg till en virtuell hård disk (VHD) på fil resursen och montera den virtuella hård disken över SMB från klienten för att utföra fil åtgärder mot data. Den här metoden fungerar för en enskild skrivare och flera läsare scenarier och tillåter att metadata-åtgärder är lokala. Installations programmet har samma prestanda som för en lokal direktansluten lagring.

### <a name="cause-3-single-threaded-application"></a>Orsak 3: program med enkel tråd

Om det program som du använder är en enkel tråds teknik kan den här konfigurationen leda till betydligt lägre IOPS-genomflöde än det högsta möjliga data flödet, beroende på den etablerade resurs storleken.

### <a name="solution"></a>Lösning

- Öka programmets parallellitet genom att öka antalet trådar.
- Växla till program där parallellitet är möjligt. För kopierings åtgärder kan du till exempel använda AzCopy eller RoboCopy från Windows-klienter eller **parallellt** kommando från Linux-klienter.

## <a name="very-high-latency-for-requests"></a>Mycket hög svars tid för begär Anden

### <a name="cause"></a>Orsak

Det gick inte att hitta den virtuella datorn för klienten (VM) i en annan region än fil resursen. Andra orsaker till hög latens kan bero på svars tiden som orsakas av klienten eller nätverket.

### <a name="solution"></a>Lösning

- Kör programmet från en virtuell dator som finns i samma region som fil resursen.
- För ditt lagrings konto granskar du transaktions måtten **SuccessE2ELatency** och  **SuccessServerLatency** via **Azure Monitor** i Azure Portal. En stor skillnad mellan SuccessE2ELatency-och SuccessServerLatency-mått är en indikation på svars tiden som troligt vis orsakas av nätverket eller klienten. Se [transaktions mått](storage-files-monitoring-reference.md#transaction-metrics) i Azure Files övervaknings data referens.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Klienten kunde inte uppnå maximalt data flöde som stöds av nätverket

### <a name="cause"></a>Orsak
En möjlig orsak till brist på SMB-stöd för flera kanaler för standard fil resurser. För närvarande har Azure Files endast stöd för en enda kanal, så det finns bara en anslutning från den virtuella klient datorn till servern. Den här enskilda anslutningen peggas till en enda kärna på den virtuella klient datorn, så det maximala data flödet som kan nås från en virtuell dator binds till en enda kärna.

### <a name="workaround"></a>Lösning

- För Premium-filresurser [aktiverar du SMB Multichannel på ett FileStorage-konto](storage-files-enable-smb-multichannel.md).
- Att hämta en virtuell dator med en större kärna kan hjälpa till att förbättra data flödet.
- Genom att köra klient programmet från flera virtuella datorer ökar du data flödet.
- Använd REST-API: er där det är möjligt.

## <a name="throughput-on-linux-clients-is-significantly-lower-than-that-of-windows-clients"></a>Data flödet på Linux-klienter är betydligt lägre än för Windows-klienter

### <a name="cause"></a>Orsak

Detta är ett känt problem med implementeringen av SMB-klienten i Linux.

### <a name="workaround"></a>Lösning

- Sprida belastningen över flera virtuella datorer.
- Använd flera monterings punkter med ett **nosharesock** -alternativ på samma virtuella dator och sprid belastningen över dessa monterings punkter.
- På Linux kan du prova att montera med ett **nostrictsync** -alternativ för att undvika en SMB-tömning på varje **fsync** -anrop. För Azure Files stör inte det här alternativet data konsekvens, men det kan resultera i inaktuella fil-metadata på katalog listor (**ls-l-** kommando). Om du direkt frågar efter fil-metadata med hjälp av **stat** kommandot returneras de senaste aktuella fil-metadata.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Hög fördröjning för metadata – tungt arbets belastningar som involverar omfattande öppna/stäng-åtgärder

### <a name="cause"></a>Orsak

Saknar stöd för katalog lån.

### <a name="workaround"></a>Lösning

- Undvik om möjligt att använda en överdriven inledande/avslutande referens på samma katalog inom en kort tids period.
- För virtuella Linux-datorer ökar du timeout-värdet för katalog post genom att ange **actimeo = \<sec>** som ett monterings alternativ. Som standard är tids gränsen 1 sekund, så ett större värde, till exempel 3 eller 5 sekunder, kan hjälpa dig.
- För virtuella datorer med CentOS Linux eller Red Hat Enterprise Linux (RHEL) uppgraderar du systemet till CentOS Linux 8,2 eller RHEL 8,2. För andra virtuella Linux-datorer uppgraderar du kernel till 5,0 eller senare.

## <a name="low-iops-on-centos-linux-or-rhel"></a>Låga IOPS på CentOS Linux eller RHEL

### <a name="cause"></a>Orsak

Ett I/O-djup på större än 1 stöds inte på CentOS Linux eller RHEL.

### <a name="workaround"></a>Lösning

- Uppgradera till CentOS Linux 8 eller RHEL 8.
- Ändra till Ubuntu.

## <a name="slow-file-copying-to-and-from-azure-file-shares-in-linux"></a>Långsam fil kopiering till och från Azure-filresurser i Linux

Om du har långsam fil kopiering kan du läsa avsnittet "långsam fil kopiering till och från Azure-filresurser i Linux" i [fel söknings guiden för Linux](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux).

## <a name="jittery-or-sawtooth-pattern-for-iops"></a>Skaknings-eller sågtandade-mönster för IOPS

### <a name="cause"></a>Orsak

Klient programmet överskrider konsekventa bas linje IOPS. För närvarande finns det ingen utjämning av belastningen på tjänst sidan. Om klienten överskrider en bas linje för en bas linje kommer den att få en begränsning av tjänsten. Begränsningen kan resultera i att klienten har ett skaknings-eller sågtandade IOPS-mönster. I det här fallet kan den genomsnittliga IOPS som uppnås av klienten vara lägre än bas linjen IOPS.

### <a name="workaround"></a>Lösning
- Minska belastningen på begäran från klient programmet, så att resursen inte får någon begränsning.
- Öka kvoten för resursen så att resursen inte får någon begränsning.

## <a name="excessive-directoryopendirectoryclose-calls"></a>För många DirectoryOpen/DirectoryClose-anrop

### <a name="cause"></a>Orsak

Om antalet **DirectoryOpen/DirectoryClose-** anrop är bland de främsta API-anropen och du inte förväntar dig att klienten ska göra många anrop, kan problemet orsakas av antivirus program varan som är installerad på den virtuella Azure-klientdatorn.

### <a name="workaround"></a>Lösning

- En korrigering för det här problemet finns i [april Platform Update för Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform).

## <a name="file-creation-is-slower-than-expected"></a>Filen har skapats långsammare än förväntat

### <a name="cause"></a>Orsak

Arbets belastningar som förlitar sig på att skapa ett stort antal filer ser inte en betydande skillnad i prestanda mellan Premium fil resurser och standard fil resurser.

### <a name="workaround"></a>Lösning

- Inga.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Långsamma prestanda från Windows 8,1 eller Server 2012 R2

### <a name="cause"></a>Orsak

Högre än förväntad fördröjning vid åtkomst till Azure-filresurser för I/O-intensiva arbets belastningar.

### <a name="workaround"></a>Lösning

- Installera den tillgängliga [snabb korrigeringen](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1).

## <a name="smb-multichannel-option-not-visible-under-file-share-settings"></a>Alternativet SMB Multichannel är inte synligt under fil resurs inställningar. 

### <a name="cause"></a>Orsak

Antingen har prenumerationen inte registrerats för funktionen, eller så stöds inte regionen och konto typen.

### <a name="solution"></a>Lösning

Se till att din prenumeration är registrerad för SMB Multichannel-funktionen. Se [komma igång](storage-files-enable-smb-multichannel.md#getting-started) se till att konto typen är FileStorage (Premium File Account) på konto översikts sidan. 

## <a name="smb-multichannel-is-not-being-triggered"></a>SMB Multichannel utlöses inte.

### <a name="cause"></a>Orsak

Nya ändringar av SMB Multichannel Config-inställningar utan ommontering.

### <a name="solution"></a>Lösning
 
-   Efter ändringar i Windows SMB-klienten eller kontot SMB Multichannel konfigurations inställningar måste du demontera resursen, vänta i 60 sekunder och montera om resursen för att utlösa Multichannel.
-   För Windows Client OS genererar du IO-belastning med hög ködjup, till exempel KÖDJUP = 8, till exempel att kopiera en fil för att utlösa SMB Multichannel.  För server-OS utlöses SMB Multichannel med KÖDJUP = 1, vilket innebär att så snart som du startar i/o till resursen.

## <a name="high-latency-on-web-sites-hosted-on-file-shares"></a>Hög latens på webbplatser som finns på fil resurser 

### <a name="cause"></a>Orsak  

Hög antal fil ändrings meddelanden på fil resurser kan resultera i avsevärd hög fördröjning. Detta inträffar vanligt vis med webbplatser som finns på fil resurser med en djup kapslad katalog struktur. Ett typiskt scenario är IIS-värdbaserade webb program där fil ändrings meddelanden installeras för varje katalog i standard konfigurationen. Varje ändring ([ReadDirectoryChangesW](/windows/win32/api/winbase/nf-winbase-readdirectorychangesw)) på den resurs som SMB-klienten är registrerad för skickar ett ändrings meddelande från fil tjänsten till klienten, som tar system resurser, och utfärdar förvärrade med antalet ändringar. Detta kan orsaka resurs begränsning och därmed resultera i högre svars tid på klient sidan. 

För att bekräfta kan du använda Azure-mått i portalen – 

1. I Azure Portal går du till ditt lagrings konto. 
1. Välj mått i den vänstra menyn under övervakning. 
1. Välj fil som mått namn område för ditt lagrings konto omfång. 
1. Välj transaktioner som mått. 
1. Lägg till ett filter för ResponseType och kontrol lera om det finns några begär Anden som har svars koden SuccessWithThrottling (för SMB) eller ClientThrottlingError (för REST).

### <a name="solution"></a>Lösning 

- Om fil ändrings meddelandet inte används inaktiverar du fil ändrings aviseringen (önskad).
    - [Inaktivera fil ändrings meddelande](https://support.microsoft.com/help/911272/fix-asp-net-2-0-connected-applications-on-a-web-site-may-appear-to-sto) genom att uppdatera FCNMode. 
    - Uppdatera IIS-arbetsprocessens (W3WP) avsöknings intervall till 0 genom att ange `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\W3SVC\Parameters\ConfigPollMilliSeconds ` i registret och starta om W3wp-processen. Mer information om den här inställningen finns i [vanliga register nycklar som används av många delar av IIS](/troubleshoot/iis/use-registry-keys#registry-keys-that-apply-to-iis-worker-process-w3wp).
- Ökar frekvensen för avsöknings intervallet för fil ändrings meddelanden för att minska volymen.
    - Uppdatera W3WP arbets process avsöknings intervall till ett högre värde (t. ex. 10mins eller 30mins) baserat på ditt krav. Ange `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\W3SVC\Parameters\ConfigPollMilliSeconds ` [i registret](/troubleshoot/iis/use-registry-keys#registry-keys-that-apply-to-iis-worker-process-w3wp) och starta om W3wp-processen.
- Om webbplatsens mappade fysiska katalog har en kapslad katalog struktur kan du försöka begränsa omfattningen av fil ändrings meddelanden för att minska meddelande volymen. Som standard använder IIS konfiguration från Web.config filer i den fysiska katalogen som den virtuella katalogen mappas till, samt i alla underordnade kataloger i den fysiska katalogen. Om du inte vill använda Web.config filer i underordnade kataloger anger du falskt för attributet allowSubDirConfig i den virtuella katalogen. Mer information hittar du [här](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories). 
    - Ange inställningen för IIS virtuell katalog "allowSubDirConfig" i Web.Config till *false* för att undanta mappade fysiska underordnade kataloger från omfånget.  

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Så här skapar du en avisering om en fil resurs är begränsad

1. Gå till ditt **lagrings konto** i **Azure Portal**.
2. I avsnittet **övervakning** klickar du på **aviseringar** och klickar sedan på **+ ny varnings regel**.
3. Klicka på **Redigera resurs**, Välj **fil resurs typ** för lagrings kontot och klicka sedan på **färdig**. Om lagrings kontots namn till exempel är `contoso` väljer du `contoso/file` resursen.
4. Lägg till ett villkor genom att klicka på **Lägg till villkor** .
5. Du kommer att se en lista över signaler som stöds för lagrings kontot. Välj måttet **transaktioner** .
6. På bladet **Konfigurera signal logik** klickar du på list rutan **Dimensions namn** och väljer **svarstyp**.
7. Klicka på list rutan **Dimensions värden** och Välj lämpliga svars typer för din fil resurs.

    För standard fil resurser väljer du följande svars typer:

    - SuccessWithThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareIopsThrottlingError

    Välj följande svars typer för Premium File-resurser:

    - SuccessWithShareEgressThrottling
    - SuccessWithShareIngressThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareEgressThrottlingError
    - ClientShareIngressThrottlingError
    - ClientShareIopsThrottlingError

   > [!NOTE]
   > Om svars typerna inte visas i list rutan **Dimensions värden** innebär det att resursen inte har begränsats. Om du vill lägga till dimensionsvärdena väljer du **Lägg till anpassat värde** bredvid List rutan **Dimensions värden** , anger svaret-typen (till exempel **SuccessWithThrottling**), väljer **OK** och upprepar sedan de här stegen för att lägga till alla lämpliga svars typer för din fil resurs.

8. För **Premium fil resurser** klickar du på list rutan **Dimensions namn** och väljer **fil resurs**. För **standard fil resurser** går du vidare till **steg #10**.

   > [!NOTE]
   > Om fil resursen är en standard fil resurs, kommer **fil** resurs dimensionen inte att visa en lista över fil resurserna eftersom det inte finns några tillgängliga resurs mått för standard fil resurser. Begränsnings varningar för standard fil resurser utlöses om någon fil resurs på lagrings kontot är begränsad och aviseringen inte kommer att identifiera vilken fil resurs som har begränsats. Eftersom per resurs-mått inte är tillgängliga för standard fil resurser, är rekommendationen att ha en fil resurs per lagrings konto.

9. Klicka på list rutan **Dimensions värden** och välj den eller de fil resurser som du vill Avisera om.
10. Definiera **aviserings parametrarna** (tröskelvärde, Operator, agg regerings precision och frekvens för utvärderingen) och klicka på **Slutför**.

    > [!TIP]
    > Om du använder ett statiskt tröskelvärde kan mått diagrammet hjälpa till att fastställa ett rimligt tröskelvärde om fil resursen för närvarande begränsas. Om du använder ett dynamiskt tröskelvärde visar mått diagrammet de beräknade tröskelvärdena baserat på aktuella data.

11. Klicka på **Lägg till åtgärds grupper** för att lägga till en **Åtgärds grupp** (e-post, SMS osv.) till aviseringen antingen genom att välja en befintlig åtgärds grupp eller skapa en ny åtgärds grupp.
12. Fyll i **aviserings informationen** som **aviserings regelns namn**, **Beskrivning** och **allvarlighets grad**.
13. Klicka på **skapa aviserings regel** för att skapa aviseringen.

Mer information om hur du konfigurerar aviseringar i Azure Monitor finns i [Översikt över aviseringar i Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="how-to-create-alerts-if-a-premium-file-share-is-trending-toward-being-throttled"></a>Så här skapar du aviseringar om en Premium-filresurs tendenser ut mot begränsning

1. I Azure Portal går du till ditt lagrings konto.
2. I avsnittet **övervakning** väljer du **aviseringar** och väljer sedan **ny aviserings regel**.
3. Välj **Redigera resurs**, Välj **fil resurs typ** för lagrings kontot och välj sedan **Slutför**. Om lagrings konto namnet till exempel är *contoso* väljer du Contoso/File-resursen.
4. Klicka på **Välj villkor** för att lägga till ett villkor.
5. Välj **utgående** mått i listan över signaler som stöds för lagrings kontot.

   > [!NOTE]
   > Du måste skapa tre separata aviseringar för att få en avisering när värdena för ingångs-, utgångs-eller transaktions värden överskrider tröskelvärdena som du har angett. Detta beror på att en avisering endast utlöses när alla villkor är uppfyllda. Om du till exempel placerar alla villkor i en avisering, blir du bara aviserad om ingress, utgående och transaktioner överskrider tröskelvärdena.

6. Rulla nedåt. I list rutan **Dimensions namn** väljer du **fil resurs**.
7. I list rutan **Dimensions värden** väljer du den fil resurs eller de resurser som du vill Avisera om.
8. Definiera aviserings parametrar genom att välja värden i **operatorn**, **tröskelvärdet**, **agg regerings precisionen** och **frekvensen för utvärderings** List rutorna och välj sedan **Slutför**.

   Utgångs-, ingångs-och transaktions mått uttrycks per minut, även om du är etablerad, ingående och I/O per sekund. Om till exempel din tillhandahållna utgång är 90 &nbsp; mebibytes per sekund (MIB/s) och du vill att ditt tröskelvärde ska vara 80 &nbsp; procent av etableringen, väljer du följande aviserings parametrar: 
   - För **tröskel värde**: *75497472* 
   - För **operatorn**: *större än eller lika* med
   - För **sammansättnings typ**: *genomsnitt*
   
   Beroende på hur mycket brus du vill att din avisering ska vara, kan du också välja värden för **agg regerings precision** och **utvärderings frekvens**. Om du till exempel vill att din avisering ska titta på det genomsnittliga ingångs intervallet under en viss tids period på 1 timme, och om du vill att varnings regeln ska köras varje timme, väljer du följande:
   - För **agg regerings granularitet**: *1 timme*
   - För **utvärderings frekvens**: *1 timme*

9. Välj **Lägg till åtgärds grupper** och Lägg sedan till en åtgärds grupp (till exempel e-post eller SMS) till aviseringen antingen genom att välja en befintlig åtgärds grupp eller genom att skapa en ny.
10. Ange aviserings information, till exempel namn, **Beskrivning** och **allvarlighets grad** för **aviserings regel**.
11. Välj **skapa aviserings regel** för att skapa aviseringen.

    > [!NOTE]
    > - Om du vill få ett meddelande om att Premium-filresursen är nära begränsad till begränsning *på grund av* insamlade ingångar följer du de föregående anvisningarna, men med följande ändring:
    >    - I steg 5 väljer du måttet **ingress** i stället för **utgående**.
    >
    > - Om du vill få ett meddelande om att Premium-filresursen är nära begränsad till begränsning *på grund av etablerade IOPS* följer du anvisningarna ovan, men med följande ändringar:
    >    - I steg 5 väljer du **transaktions** måttet i stället för **utgående**.
    >    - I steg 10 är det enda alternativet för **agg regerings typ** *Total*. Därför beror tröskelvärdet på din valda agg regerings kornig het. Om du till exempel vill att tröskelvärdet ska vara 80 &nbsp; procent av etablerade bas linje IOPS och du väljer *1 timme* för **agg regerings precision**, skulle ditt **tröskelvärde** vara din bas linje IOPS (i byte) &times; &nbsp; 0,8 &times; &nbsp; 3600. 

Mer information om hur du konfigurerar aviseringar i Azure Monitor finns i [Översikt över aviseringar i Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="see-also"></a>Se även
- [Felsöka Azure Files i Windows](storage-troubleshoot-windows-file-connection-problems.md)  
- [Felsöka Azure Files i Linux](storage-troubleshoot-linux-file-connection-problems.md)  
- [Vanliga frågor och svar om Azure Files](storage-files-faq.md)
