---
title: Så här använder du PerfInsights i Microsoft Azure | Microsoft Docs
description: Lär dig hur du använder PerfInsights för att felsöka prestanda problem i Windows VM.
services: virtual-machines-windows
documentationcenter: ''
author: anandhms
manager: dcscontentpm
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 9e298bf39446024f384b9af142fe3000e936bb6d
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656860"
---
# <a name="how-to-use-perfinsights-in-azure"></a>Så här använder du PerfInsights i Azure

[PerfInsights](https://aka.ms/perfinsightsdownload) är ett verktyg för självhjälp som samlar in och analyserar diagnostikdata och ger en rapport som hjälper dig att felsöka prestanda problem i Windows Virtual Machine i Azure. PerfInsights kan köras på virtuella datorer som ett fristående verktyg, direkt från portalen med hjälp av [prestandadiagnostik för Azure Virtual Machines](performance-diagnostics.md), eller genom att installera [Azure Performance Diagnostics VM-tillägget](performance-diagnostics-vm-extension.md).

Om du upplever prestanda problem med virtuella datorer måste du köra det här verktyget innan du kontaktar supporten.

## <a name="supported-troubleshooting-scenarios"></a>Fel söknings scenarier som stöds

PerfInsights kan samla in och analysera flera typer av information. Följande avsnitt beskriver vanliga scenarier.

### <a name="quick-performance-analysis"></a>Snabb prestanda analys

Det här scenariot samlar in disk konfigurationen och annan viktig information, inklusive:

-   Händelseloggar

-   Nätverks status för alla inkommande och utgående anslutningar

-   Konfigurations inställningar för nätverk och brand vägg

-   Uppgifts lista för alla program som för närvarande körs i systemet

-   Microsoft SQL Server konfigurations inställningar för databasen (om den virtuella datorn identifieras som en server som kör SQL Server)

-   Tillförlitlighets räknare för lagring

-   Viktiga Windows-snabbkorrigeringar

-   Installerade filter driv rutiner

Detta är en passiv insamling av information som inte påverkar systemet. 

>[!Note]
>Det här scenariot ingår automatiskt i följande scenarier:

### <a name="benchmarking"></a>Prestanda mätningar

Det här scenariot kör [Diskspd](https://github.com/Microsoft/diskspd) benchmark-test (IOPS och Mbps) för alla enheter som är anslutna till den virtuella datorn. 

> [!Note]
> Det här scenariot kan påverka systemet och bör inte köras i ett pågående produktions system. Om det behövs kan du köra det här scenariot i ett dedikerat underhålls fönster för att undvika eventuella problem. En ökad arbets belastning som orsakas av ett spårnings-eller benchmark-test kan påverka prestandan för den virtuella datorn negativt.
>

### <a name="performance-analysis"></a>Prestanda analys

Det här scenariot kör en spårning av [prestanda räknare](/windows/win32/perfctrs/performance-counters-portal) med hjälp av de räknare som anges i RuleEngineConfig.jsi filen. Om den virtuella datorn identifieras som en server som kör SQL Server, körs en spårning av prestanda räknaren. Det gör det genom att använda de räknare som finns i RuleEngineConfig.jspå filen. Det här scenariot omfattar även prestanda diagnostikdata.

### <a name="azure-files-analysis"></a>Azure Files analys

Det här scenariot kör en särskild prestanda räknare som samlas in med ett nätverks spår. Avbildningen innehåller alla räknare för Server Message Block (SMB)-klient resurser. Nedan följer några viktiga prestanda räknare för SMB-klient resurser som ingår i avbildningen:

| **Typ**     | **Räknare för SMB-klient resurser** |
|--------------|-------------------------------|
| IOPS         | Data begär anden/SEK             |
|              | Läs begär anden/SEK             |
|              | Skriv begär anden/SEK            |
| Svarstid      | Genomsn. s/data-begäran         |
|              | Genomsn. s/läsning                 |
|              | Genomsn. s/skrivning                |
| I/o-storlek      | Genomsnittlig byte/data-begäran       |
|              | Genomsnittligt antal byte/läsning               |
|              | Genomsnittligt antal byte/skrivning              |
| Dataflöde   | Data-byte/s                |
|              | Lästa byte/sek                |
|              | Skrivna byte/sek               |
| Kölängd | Genomsnittlig längd på Läs kön        |
|              | Genomsnittlig längd på Skriv kön       |
|              | Genomsnittlig längd på data kön        |

### <a name="advanced-performance-analysis"></a>Avancerad prestanda analys

När du kör en avancerad prestanda analys väljer du spårningar som ska köras parallellt. Om du vill kan du köra dem alla (prestanda räknare, XPerf, nätverk och StorPort).  

> [!Note]
> Det här scenariot kan påverka systemet och bör inte köras i ett pågående produktions system. Om det behövs kan du köra det här scenariot i ett dedikerat underhålls fönster för att undvika eventuella problem. En ökad arbets belastning som orsakas av ett spårnings-eller benchmark-test kan påverka prestandan för den virtuella datorn negativt.
>

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Vilken typ av information samlas in av PerfInsights?

Information om virtuella Windows-datorer, diskar eller lagringspooler konfiguration, prestanda räknare, loggar och olika spår samlas in. Det beror på vilket prestanda scenario du använder. Följande tabell innehåller mer information:

| Insamlade data | Snabb prestanda analys | Prestanda mätningar | Prestanda analys | Azure Files analys | Avancerad prestanda analys |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|
| Information från händelse loggar       | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Systeminformation                | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Volym karta                        | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Disk karta                          | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Aktiviteter som körs                     | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Tillförlitlighets räknare för lagring      | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Lagrings information               | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Fsutil-utdata                     | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Filter driv rutins information                | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Netstat-utdata                    | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Konfiguration av nätverk             | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Konfigurering av brandvägg            | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| SQL Server-konfiguration          | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Spårning av prestanda diagnoser *  | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Spårning av prestanda räknare * *      |                            |                                    | Ja                      |                      | Ja                  |
| Spårning av SMB-räknare * *              |                            |                                    |                          | Yes                  |                      |
| Spårning av SQL Server räknare * *       |                            |                                    | Ja                      |                      | Ja                  |
| XPerf-spårning                       |                            |                                    |                          |                      | Yes                  |
| StorPort-spårning                    |                            |                                    |                          |                      | Yes                  |
| Nätverks spårning                     |                            |                                    |                          | Ja                  | Ja                  |
| Diskspd benchmark-spårning * * _       |                            | Yes                                |                          |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-_"></a>Spårning av prestanda diagnostik (_)

Kör en regel baserad motor i bakgrunden för att samla in data och diagnostisera pågående prestanda problem. Följande regler stöds för närvarande:

- HighCpuUsage-regel: identifierar hög processor användnings perioder och visar de högsta processor användnings förbrukarna under dessa perioder.
- HighDiskUsage-regel: identifierar hög disk användnings perioder på fysiska diskar och visar de mest disk användnings konsumenter under dessa perioder.
- HighResolutionDiskMetric-regel: visar IOPS, data flöde och I/O-latens mått per 50 millisekunder för varje fysisk disk. Det hjälper dig att snabbt identifiera disk begränsnings perioder.
- HighMemoryUsage-regel: identifierar hög minnes användnings perioder och visar de högsta minnes användnings konsumenter under dessa perioder.

> [!NOTE] 
> För närvarande stöds Windows-versioner som innehåller .NET Framework 4,5 eller senare versioner.

### <a name="performance-counter-trace-"></a>Spårning av prestanda räknare (* *)

Samlar in följande prestanda räknare:

- \Process, \Processor, \Memory, \Thread, \PhysicalDisk och \Logisk disk
- \Cache\Dirty-sidor, \Cache\Lazy Skriv tömningar/s, \Server\Pool som inte är växlade, felsidor och \Server\Pool växlings Bart haveri
- Valda räknare under \Network-gränssnittet, \IPv4\Datagrams, \IPv6\Datagrams, \TCPv4\Segments, \TCPv6\Segments, \Network adapter, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \Network QoS Policy\Packets, \Per processor Network Interface Card-aktivitet och \Microsoft Winsock BSP

#### <a name="for-sql-server-instances"></a>För SQL Server instanser
- \SQL Server: buffer Manager, \SQLServer: Resource pool statistik och \SQLServer: SQL-statistik \
- \SQLServer: lås, \SQLServer: Allmänt, statistik
- \SQLServer: åtkomst metoder

#### <a name="for-azure-files"></a>För Azure Files
\SMB klient resurser

### <a name="diskspd-benchmark-trace-_"></a>Diskspd benchmark-spårning (* * _)
Diskspd I/O-arbetsbelastnings test (OS-disk [Write] och pool enheter [läsa/skriva])

## <a name="run-the-perfinsights-tool-on-your-vm"></a>Kör verktyget PerfInsights på den virtuella datorn

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Vad måste jag känna till innan jag kör verktyget? 

#### <a name="tool-requirements"></a>Verktygs krav

-  Det här verktyget måste köras på den virtuella datorn som har prestanda problemet. 

-  Följande operativ system stöds: _ Windows Server 2019
   * Windows Server 2016
   * Windows Server 2012 R2
   * Windows Server 2012
   * Windows Server 2008 R2
   * Windows 10
   * Windows 8,1
   * Windows 8

#### <a name="possible-problems-when-you-run-the-tool-on-production-vms"></a>Möjliga problem när du kör verktyget på virtuella produktions datorer

-  För scenariot för benchmarking eller "avancerad prestanda analys" som är konfigurerat för att använda XPerf eller Diskspd kan verktyget påverka den virtuella datorns prestanda negativt. De här scenarierna bör inte köras i en pågående produktions miljö.

-  För benchmarking-scenariot eller scenariot "avancerad prestanda analys" som är konfigurerat för att använda Diskspd, se till att ingen annan bakgrunds aktivitet stör i/O-arbetsbelastningen.

-  Som standard använder verktyget den tillfälliga lagrings enheten för att samla in data. Om spårningen förblir aktive rad under en längre tid, kan mängden data som samlas in vara relevanta. Detta kan minska tillgängligheten för utrymme på den temporära disken och kan därför påverka alla program som förlitar sig på den här enheten.

### <a name="how-do-i-run-perfinsights"></a>Hur gör jag för att köra PerfInsights? 

Du kan köra PerfInsights på en virtuell dator genom att installera [Azure Performance Diagnostics VM-tillägget](performance-diagnostics-vm-extension.md). Du kan också köra det som ett fristående verktyg. 

**Installera och kör PerfInsights från Azure Portal**

Mer information om det här alternativet finns i [Installera Azure Performance Diagnostics VM-tillägg](performance-diagnostics-vm-extension.md#install-the-extension).  

**Köra PerfInsights i fristående läge**

Följ dessa steg om du vill köra PerfInsights-verktyget:


1. Ladda ned [PerfInsights.zip](https://aka.ms/perfinsightsdownload).

2. Avblockera PerfInsights.zips filen. Det gör du genom att högerklicka på PerfInsights.zip-filen och välja **Egenskaper**. På fliken **Allmänt** väljer du **Häv blockering** och väljer sedan **OK**. Detta säkerställer att verktyget körs utan några ytterligare säkerhets meddelanden.  

    ![Skärm bild av PerfInsights-egenskaper, med avblockera markerade](media/how-to-use-perfInsights/pi-unlock-file.png)

3.  Expandera den komprimerade PerfInsights.zip-filen till den temporära enheten (som standard är detta vanligt vis D-enheten). 

4.  Öppna kommando tolken i Windows som administratör och kör sedan PerfInsights.exe för att visa de tillgängliga kommando rads parametrarna.

    ```
    cd <the path of PerfInsights folder>
    PerfInsights
    ```
    ![Skärm bild av PerfInsights kommandorad-utdata](media/how-to-use-perfInsights/pi-commandline.png)
    
    Den grundläggande syntaxen för att köra PerfInsights-scenarier är:
    
    ```
    PerfInsights /run <ScenarioName> [AdditionalOptions]
    ```

    Du kan använda exemplet nedan för att köra scenariot för prestanda analys för 5 minuter:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Du kan använda följande exempel för att köra det avancerade scenariot med spårning av XPerf och prestanda räknare för 5 minuter:
    
    ```
    PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Du kan använda exemplet nedan för att köra scenariot för prestanda analys för 5 minuter och överföra resultat-zip-filen till lagrings kontot:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>
    ```

    Du kan söka efter alla tillgängliga scenarier och alternativ med kommandot **/list** :
    
    ```
    PerfInsights /list
    ```

    >[!Note]
    >Innan du kör ett scenario, kräver PerfInsights att användaren samtycker till att dela diagnostikinformation och godkänna licens avtalet. Använd alternativet **/AcceptDisclaimerAndShareDiagnostics** för att hoppa över dessa prompter. 
    >
    >Om du har ett aktivt support ärende med Microsoft och kör PerfInsights per begäran av support teknikern som du arbetar med, måste du ange support ärende numret med hjälp av alternativet **/SR** .
    >
    >PerfInsights kommer som standard att försöka uppdatera sig till den senaste versionen om den är tillgänglig. Använd **/SkipAutoUpdate** -eller **/SAU** -parametern för att hoppa över automatisk uppdatering.  
    >
    >Om du inte anger **parametern duration** PerfInsights uppmanas du att återskapnings problemet när du kör vmslow, migreringsåtgärden och avancerade scenarier. 

När spårningen eller åtgärderna har slutförts visas en ny fil i samma mapp som PerfInsights. Namnet på filen är **PerformanceDiagnostics \_ åååå-mm-dd \_hh-mm-ss-fff.zip.** Du kan skicka filen till support agenten för analys eller öppna rapporten i zip-filen för att granska resultat och rekommendationer.

## <a name="review-the-diagnostics-report"></a>Granska den diagnostiska rapporten

I **PerformanceDiagnostics \_ åååå-mm-dd \_hh-mm-ss-fff.zip-** filen kan du hitta en HTML-rapport som innehåller information om resultaten av PerfInsights. Om du vill granska rapporten expanderar du **PerformanceDiagnostics \_ åååå-mm-dd \_hh-mm-ss-fff.zip-** filen och öppnar sedan filen **PerfInsights Report.html** .

Välj fliken **resultat** .

![Skärm bild av fliken Översikt i PerfInsights-rapporten. ](media/how-to-use-perfInsights/pi-finding-tab.png)
 ![ Skärm bild av fliken lagring i PerfInsights-rapporten.](media/how-to-use-perfInsights/pi-findings.png)

> [!NOTE] 
> Avgöranden kategoriserade som höga är kända problem som kan orsaka prestanda problem. Resultat som kategoriseras som medel representerar icke-optimala konfigurationer som inte nödvändigt vis orsakar prestanda problem. Resultat som kategoriseras som låg är endast informativa rapporter.

Granska rekommendationer och länkar för alla höga och medeliga resultat. Lär dig mer om hur de kan påverka prestanda och även om metod tips för prestanda optimering.

### <a name="storage-tab"></a>Fliken lagring

I avsnittet om **resultat** visas olika resultat och rekommendationer relaterade till lagring.

I avsnitten **disk karta** och **volym översikt** beskrivs hur logiska volymer och fysiska diskar är relaterade till varandra.

I det fysiska disk perspektivet (disk karta) visar tabellen alla logiska volymer som körs på disken. I följande exempel kör **PhysicalDrive2** två logiska volymer som skapats på flera partitioner (J och H):

![Skärm bild av fliken disk](media/how-to-use-perfInsights/pi-disk-tab.png)

I volym perspektivet (volym karta) visar tabellerna alla fysiska diskar under varje logisk volym. Observera att du kan köra en logisk volym på flera fysiska diskar för RAID/dynamiska diskar. I följande exempel är *C: \\ montera* en monterings punkt som kon figurer ATS som *SpannedDisk* på fysiska diskar 2 och 3:

![Skärm bild av fliken volym](media/how-to-use-perfInsights/pi-volume-tab.png)

### <a name="sql-tab"></a>Fliken SQL

Om den virtuella mål datorn är värd för några SQL Server instanser visas ytterligare en flik i rapporten med namnet **SQL**:

![Skärm bild av fliken SQL](media/how-to-use-perfInsights/pi-sql-tab.png)

Det här avsnittet innehåller **en flik** och ytterligare flikar för var och en av de SQL Server instanser som finns på den virtuella datorn.

Fliken **konstaterande** innehåller en lista över alla SQL-relaterade prestanda problem som har hittats, tillsammans med rekommendationerna.

I följande exempel visas **PhysicalDrive0** (som kör C-enheten). Detta beror på att både **modeldev** -och **modellog** -filerna finns på C-enheten och att de är av olika typer (till exempel data filen och transaktions loggen).

![Skärm bild av logg information](media/how-to-use-perfInsights/pi-log-info.png)

Flikarna för vissa instanser av SQL Server innehåller ett allmänt avsnitt som visar grundläggande information om den valda instansen. Flikarna innehåller också ytterligare avsnitt för avancerad information, inklusive inställningar, konfigurationer och användar alternativ.

### <a name="diagnostic-tab"></a>Fliken diagnostik
Fliken **diagnostik** innehåller information om Top processor-, disk-och minnes konsumenter på datorn under den tid som PerfInsights körs. Du kan också hitta information om viktiga korrigeringar som systemet kan sakna, uppgifts listan och viktiga system händelser. 

## <a name="references-to-the-external-tools-used"></a>Referenser till de externa verktyg som används

### <a name="diskspd"></a>Diskspd

Diskspd är ett verktyg för lagrings belastnings Generator och prestanda testning från Microsoft. Mer information finns i [Diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>Xperf

XPerf är ett kommando rads verktyg för att avbilda spårningar från Windows Performance Toolkit. Mer information finns i [Windows Performance Toolkit – XPerf](/archive/blogs/ntdebugging/windows-performance-toolkit-xperf).

## <a name="next-steps"></a>Nästa steg

Du kan ladda upp diagnostikloggar och rapporter till Microsoft Support för ytterligare granskning. Support kan begära att du överför utdata som genereras av PerfInsights för att hjälpa till med fel söknings processen.

Följande skärm bild visar ett meddelande som liknar det du kan få:

![Skärm bild av exempel meddelande från Microsoft Support](media/how-to-use-perfInsights/pi-support-email.png)

Följ anvisningarna i meddelandet för att komma åt fil överförings arbets ytan. För ytterligare säkerhet måste du ändra ditt lösen ord vid första användningen.

När du har loggat in hittar du en dialog ruta där du kan ladda upp **PerformanceDiagnostics \_ åååå-mm-dd \_hh-mm-ss-fff.zip-** filen som samlats in av PerfInsights.
