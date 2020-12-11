---
title: Så här använder du PerfInsights Linux i Microsoft Azure | Microsoft Docs
description: Lär dig hur du använder PerfInsights för att felsöka prestanda problem med virtuella Linux-datorer.
services: virtual-machines-linux'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 7/10/2019
ms.author: genli
ms.openlocfilehash: 340164ef86d34f273b3d5a98b62300bc6cc50df7
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109427"
---
# <a name="how-to-use-perfinsights"></a>Så här använder du PerfInsights

[PerfInsights Linux](https://aka.ms/perfinsightslinuxdownload) är ett verktyg för självhjälps diagnos som samlar in och analyserar diagnostikdata och ger en rapport som hjälper dig att felsöka prestanda problem i Linux Virtual Machine i Azure. PerfInsights kan köras på virtuella datorer som stöds som ett fristående verktyg eller direkt från portalen med hjälp av [prestandadiagnostik för Azure Virtual Machines](performance-diagnostics.md).

Om du upplever prestanda problem med virtuella datorer måste du köra det här verktyget innan du kontaktar supporten.

## <a name="supported-troubleshooting-scenarios"></a>Fel söknings scenarier som stöds

PerfInsights kan samla in och analysera flera typer av information. Följande avsnitt beskriver vanliga scenarier.

### <a name="quick-performance-analysis"></a>Snabb prestanda analys

Det här scenariot samlar in grundläggande information, till exempel lagrings-och maskin varu konfiguration för din virtuella dator, olika loggar, inklusive:

- Information om operativ system

- Information om PCI-enhet

- Allmänna gäst operativ system loggar

- Konfigurationsfiler

- Lagrings information

- Konfiguration av virtuell Azure-dator (samlas in med [Azure instance metadata service](../windows/instance-metadata-service.md))

- Lista över processer som körs, disk, minne och CPU-användning

- Nätverksinformation

Detta är en passiv insamling av information som inte påverkar systemet.

>[!Note]
>Scenariot för snabb prestanda analys ingår automatiskt i följande scenarier:

### <a name="performance-analysis"></a>Prestanda analys

Det här scenariot liknar snabb prestanda analys, men gör det möjligt att samla in diagnostikinformation under längre tid.

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Vilken typ av information som samlas in av PerfInsights

Information om den virtuella Linux-datorn, operativ systemet, blockera enheter, hög resurs konsumenter, konfiguration och olika loggar samlas in. Här finns mer information:

- Operativsystem
  - Linux-distribution och version
  - Kernel-information
  - Driv rutins information

- Maskinvara
  - PCI-enheter [ `*` ]

- Processer och minne
  - Lista över processer (uppgifts namn, använt minne, öppna filer)
  - Totalt, tillgängligt och ledigt fysiskt minne
  - Totalt, tillgängligt och kostnads fritt växlings minne
  - Profilering av hämtning av CPU och bearbetar CPU-användning med 5 sekunders intervall
  - Profilering av hämtning av processer minnes användning med 5 sekunders intervall

- Nätverk  
  - Lista över nätverkskort med kort statistik
  - Routningstabell för nätverk
  - Öppna portar och status

- Lagring
  - Blockera enhets lista
  - Lista med partitioner
  - Lista över monterings punkter
  - MDADM volym information
  - LVM volym information
  - Profilering av hämtning på alla diskar med 5 sekunders intervall

- Loggar
  - /var/log/Messages
  - /var/log/syslog
  - /var/log/kern.log
  - /var/log/cron.log
  - /var/log/boot.log
  - /var/log/yum.log
  - /var/log/dpkg.log
  - /var/log/sysstat eller/var/log/sa [ `**` ]
  - /var/log/cloud-init.log
  - /var/log/cloud-init-output.log
  - /var/log/gpu-manager.log
  - /var/log/waagent.log
  - /var/log/Azure/[mapp för tillägg]/ \* logg\*
  - /var/opt/microsoft/omsconfig/omsconfig.log
  - /var/opt/microsoft/omsagent/log/omsagent.log
  - /etc/waagent.config
  - Utdata från journalctl under de senaste fem dagarna

- [Metadata för virtuell Azure-dator](../windows/instance-metadata-service.md)

>[!Note]
>[ `*` ] PCI-information har inte samlats in på Debian-och SLES-distributioner.
> 
>[ `**` ]/var/log/sysstat eller/var/log/sa innehåller filer för system aktivitets rapport (SAR) som samlas in av sysstat-paketet. Om sysstat-paketet inte är installerat på den virtuella datorn, ger verktyget PerfInsights en rekommendation att installera det.

## <a name="run-the-perfinsights-linux-on-your-vm"></a>Kör PerfInsights Linux på den virtuella datorn

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Vad måste jag känna till innan jag kör verktyget

#### <a name="tool-requirements"></a>Verktygs krav

- Det här verktyget måste köras på den virtuella datorn som har prestanda problemet.
- Python 3. x eller python 2,7 måste vara installerat på den virtuella datorn.

- Följande distributioner stöds för närvarande:

    | Distribution               | Version                                         |
    |----------------------------|-------------------------------------------------|
    | Oracle Linux Server        | 6,10 [ `*` ], 7,3, 7,5, 7,6, 7,7, 7,8 |
    | CentOS                     | 6,5 [ `*` ], 7,6, 7,7, 7,8                                    |
    | RHEL                       | 7,2, 7,5, 8,0 [ `*` ], 8,1, 8,2                               |
    | Ubuntu                     | 14,04, 16,04, 18,04, 20,04                               |
    | Debian                     | 8, 9, 10 [ `*` ]                                    |
    | SLES                       | 12 SP4 [ `*` ], 12 SP5 [ `*` ], 15 [ `*` ], 15 SP1 [ `*` ], 15 SP2 [ `*` ]                                      |
    |                            |                                                   |

>[!Note]
>[ `*` ] Se avsnittet [kända problem](#known-issues)

### <a name="known-issues"></a>Kända problem

- RHEL 8 har inte python installerat som standard. Om du vill köra PerfInsights Linux måste du först installera python 2,7

- Det går inte att samla in gäst agent information på CentOS 6. x

- Information om PCI-enheter samlas inte in på Debian-baserade distributioner

- LVM-information samlas delvis in på vissa distributioner

### <a name="how-do-i-run-perfinsights"></a>Hur gör jag för att köra PerfInsights

Du kan köra PerfInsights på en virtuell dator genom att installera Azure-prestandadiagnostik från Azure Portal. Du kan också köra det som ett fristående verktyg.

>[!Note]
>PerfInsights samlar bara in och analyserar data. Det gör inga ändringar i systemet.

#### <a name="install-and-run-perfinsights-from-the-azure-portal"></a>Installera och kör PerfInsights från Azure Portal

Mer information om det här alternativet finns i [Azure Performance Diagnostics](performance-diagnostics.md).  

#### <a name="run-perfinsights-in-standalone-mode"></a>Köra PerfInsights i fristående läge

Följ dessa steg om du vill köra PerfInsights-verktyget:

1. Hämta [PerfInsights. tar. gz](https://aka.ms/perfinsightslinuxdownload) till en mapp på den virtuella datorn och extrahera innehållet med hjälp av nedanstående kommandon från terminalen.

   ```bash
   wget https://download.microsoft.com/download/9/F/8/9F80419C-D60D-45F1-8A98-718855F25722/PerfInsights.tar.gz
   ```

   ```bash
   tar xzvf PerfInsights.tar.gz
   ```

2. Navigera till mappen som innehåller `perfinsights.py` filen och kör sedan `perfinsights.py` för att visa de tillgängliga kommando rads parametrarna.

    ```bash
    cd <the path of PerfInsights folder>
    sudo python perfinsights.py
    ```

    ![Skärm bild av PerfInsights Linux kommando rad utdata](media/how-to-use-perfinsights-linux/perfinsights-linux-commandline.png)

    Den grundläggande syntaxen för att köra PerfInsights-scenarier är:

    ```bash
    sudo python perfinsights.py -r <ScenarioName> -d [duration]<H | M | S> [AdditionalOptions]
    ```

    Du kan använda exemplet nedan för att köra scenariot med snabb prestanda analys i 1 minut och skapa resultatet under/tmp/output-mappen:

    ```bash
    sudo python perfinsights.py -r quick -d 1M -a -o /tmp/output
    ```

    Du kan använda exemplet nedan för att köra scenariot för prestanda analys för 5 minuter och överföra resultatet tar en kula till lagrings kontot:

    ```bash
    sudo python perfinsights.py -r vmslow -d 300S -a -t <StorageAccountName> -k <StorageAccountKey> -i <full resource Uri of the current VM>
    ```

    >[!Note]
    >Innan du kör ett scenario, kräver PerfInsights att användaren samtycker till att dela diagnostikinformation och godkänna licens avtalet. Använd alternativet **-a eller--accept-och-Share-Diagnostics** för att hoppa över dessa prompter.
    >
    >Om du har ett aktivt support ärende med Microsoft och kör PerfInsights per begäran av support teknikern som du arbetar med, måste du ange support ärende numret med alternativet **-s eller--Support-Request** .

När körningen har slutförts visas en ny tar-fil i samma mapp som PerfInsights, om ingen mapp för utdata har angetts. Namnet på filen är **PerformanceDiagnostics \_ åååå-mm-dd \_ HH-mm-SS-FFF. tar. gz.** Du kan skicka den här filen till support agenten för analys eller öppna rapporten i filen för att granska resultat och rekommendationer.

## <a name="review-the-diagnostics-report"></a>Granska den diagnostiska rapporten

I filen **PerformanceDiagnostics \_ åååå-mm-dd \_ HH-mm-SS-FFF. tar. gz** kan du hitta en HTML-rapport som innehåller information om resultaten av PerfInsights. Om du vill granska rapporten expanderar du filen **PerformanceDiagnostics \_ åååå-mm-dd \_ HH-mm-SS-FFF. tar. gz** och öppnar sedan filen **PerfInsights Report.html** .

### <a name="overview-tab"></a>Fliken Översikt

Fliken **Översikt** innehåller grundläggande körnings information och information om virtuella datorer. På fliken **konstaterande** visas en sammanfattning av rekommendationerna från alla de olika avsnitten i PerfInsights-rapporten.

![Skärm bild av fliken Översikt i PerfInsights-rapporten.](media/how-to-use-perfinsights-linux/perfinsights-linux-overview.png)  
![Skärm bild av Linux-fliken i PerfInsights-rapporten.](media/how-to-use-perfinsights-linux/perfinsights-linux-findings-tab.png)

> [!NOTE]
> Avgöranden kategoriserade som höga är kända problem som kan orsaka prestanda problem. Resultat som kategoriseras som medel representerar icke-optimala konfigurationer som inte nödvändigt vis orsakar prestanda problem. Resultat som kategoriseras som låg är endast informativa rapporter.

Granska rekommendationer och länkar för alla höga och medeliga resultat. Lär dig mer om hur de kan påverka prestanda och även om metod tips för prestanda optimering.

### <a name="cpu-tab"></a>Fliken processor

Fliken **CPU** innehåller information om processor förbrukning i hela systemet under PerfInsights-körningen. Information om höga CPU-användnings perioder och Top långvariga processor konsumenter kommer att vara till hjälp för att felsöka höga CPU-relaterade problem.

![Skärm bild av fliken PerfInsights Report CPU](media/how-to-use-perfinsights-linux/perfinsights-linux-cpu-tab.png)

### <a name="storage-tab"></a>Fliken lagring

I avsnittet om **resultat** visas olika resultat och rekommendationer relaterade till lagring.

**Block enheterna** och andra relaterade avsnitt, till exempel **partitioner**, **LVM** och **MDADM** , beskriver hur block enheter konfigureras och är relaterade till varandra.

![Skärm bild av fliken lagring](media/how-to-use-perfinsights-linux/perfinsights-linux-storage-tab.png)  
![Skärm bild av fliken MDADM](media/how-to-use-perfinsights-linux/perfinsights-linux-mdadm-config.png)

### <a name="linux-tab"></a>Fliken Linux

Fliken **Linux** innehåller information om maskin vara och operativ system som körs på den virtuella datorn. Information innehåller en lista över processer som körs och information om gästa Gent, PCI, CPU, driv rutiner och LIS-drivrutiner.

![Skärm bild av fliken Linux](media/how-to-use-perfinsights-linux/perfinsights-linux-tab.png)

## <a name="next-steps"></a>Nästa steg

Du kan ladda upp diagnostikloggar och rapporter till Microsoft Support för ytterligare granskning. När du arbetar med Microsoft Support personal kan de begära att du överför utdata som genereras av PerfInsights för att hjälpa till med fel söknings processen.

Följande skärm bild visar ett meddelande som liknar det du kan få:

![Skärm bild av exempel meddelande från Microsoft Support](media/how-to-use-perfinsights-linux/support-email.png)

Följ anvisningarna i meddelandet för att komma åt fil överförings arbets ytan. För ytterligare säkerhet måste du ändra ditt lösen ord vid första användningen.

När du har loggat in hittar du en dialog ruta där du kan ladda upp **PerformanceDiagnostics \_ åååå-mm-dd \_ HH-mm-SS-FFF. tar. gz-** filen som samlats in av PerfInsights.
