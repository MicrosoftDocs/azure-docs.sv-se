---
title: VMware-övervakning lösning i Azure Monitor | Microsoft Docs
description: Lär dig mer om hur VMware-övervakning-lösningen kan hjälpa dig att hantera loggar och övervaka ESXi-värdar.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2018
ms.openlocfilehash: 9ade5a51e2251669daee6fbaca9aa4c50f7e9bfc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101704369"
---
# <a name="vmware-monitoring-deprecated-solution-in-azure-monitor"></a>VMware-övervakning (inaktuell) lösning i Azure Monitor

![VMware-symbol](./media/vmware/vmware-symbol.png)

> [!NOTE]
> Den VMware-övervakning lösningen är föråldrad.  Kunder som redan har installerat lösningen kan fortsätta att använda den, men VMware-övervakning kan inte läggas till i nya arbets ytor.

VMware-övervakning-lösningen i Azure Monitor är en lösning som hjälper dig att skapa en centraliserad loggnings-och övervaknings metod för stora VMware-loggar. Den här artikeln beskriver hur du kan felsöka, avbilda och hantera ESXi-värdar på en enda plats med hjälp av lösningen. Med lösningen kan du se detaljerad information för alla dina ESXi-värdar på en enda plats. Du kan se antal händelser, status och trender för virtuella datorer och ESXi-värdar som tillhandahålls via värd loggarna för ESXi. Du kan felsöka genom att visa och söka i centraliserade ESXi värd loggar. Du kan också skapa aviseringar baserat på loggs öknings frågor.

Lösningen använder interna syslog-funktioner i ESXi-värden för att skicka data till en virtuell måldator, som har Log Analytics agenten. Lösningen skriver dock inte filer till syslog i den virtuella mål datorn. Log Analytics agent öppnar port 1514 och lyssnar på detta. När den tar emot data skickar Log Analytics-agenten data till Azure Monitor.

## <a name="install-and-configure-the-solution"></a>Installera och konfigurera lösningen
Använd följande information för att installera och konfigurera lösningen.

* Lägg till VMware-övervakning-lösningen i din prenumeration med hjälp av processen som beskrivs i [installera en övervaknings lösning](./solutions.md#install-a-monitoring-solution).

#### <a name="supported-vmware-esxi-hosts"></a>VMware ESXi värdar som stöds
vSphere ESXi Host 5,5, 6,0 och 6,5

#### <a name="prepare-a-linux-server"></a>Förbered en Linux-Server
Skapa en virtuell Linux-operativ system version för att ta emot alla syslog-data från ESXi-värdar. [Log Analytics Linux-agenten](../vm/quick-collect-linux-computer.md) är samlings platsen för alla ESXi-värden för syslog-data. Du kan använda flera ESXi-värdar för att vidarebefordra loggar till en enda Linux-server, som i följande exempel.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

   ![syslog-flöde](./media/vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Konfigurera syslog-samling
1. Konfigurera syslog-vidarebefordran för VSphere. Detaljerad information om hur du konfigurerar syslog-vidarebefordran finns i [Konfigurera syslog på ESXi 5,0 och högre (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Gå till **ESXi för värd konfiguration**  >  **program**  >  **Avancerade inställningar**  >  **syslog**.
   ![vsphereconfig](./media/vmware/vsphere1.png)  
1. I fältet *syslog. global. logHost* lägger du till Linux-servern och port numret *1514*. Det kan till exempel vara `tcp://hostname:1514` eller `tcp://123.456.789.101:1514`
1. Öppna ESXi-värd brand väggen för syslog. Konfiguration av ESXi- **värd**  >  **Program vara**  >  **Säkerhets profil**  >  **Brand vägg** och öppna **Egenskaper**.  

    ![vspherefw](./media/vmware/vsphere2.png)  

    ![vspherefwproperties](./media/vmware/vsphere3.png)  
1. Kontrol lera vSphere-konsolen för att kontrol lera att syslog har kon figurer ATS korrekt. Bekräfta att ESXI-värden som port **1514** är konfigurerad på.
1. Ladda ned och installera Log Analytics-agenten för Linux på Linux-servern. Mer information finns i [dokumentationen för Log Analytics agent för Linux](https://github.com/Microsoft/OMS-Agent-for-Linux).
1. När Log Analytics-agenten för Linux har installerats går du till katalogen/etc/opt/Microsoft/omsagent/sysconf/omsagent.d och kopierar filen vmware_esxi. conf till katalogen/etc/opt/Microsoft/omsagent/conf/omsagent.d och ändrar ägare/grupp och behörigheter för filen. Exempel:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
1. Starta om Log Analytics agent för Linux genom att köra `sudo /opt/microsoft/omsagent/bin/service_control restart` .
1. Testa anslutningen mellan Linux-servern och ESXi-värden med hjälp av `nc` kommandot på ESXi-värden. Exempel:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

1. I Azure Portal utför du en logg fråga för `VMware_CL` . När Azure Monitor samlar in syslog-data, behåller det syslog-formatet. I portalen samlas vissa fält in, till exempel *hostname* och *processname*.  

    ![Skärm bild som visar en logg fråga för typ = VMware_CL med ett tidsstämplat resultat.](./media/vmware/type.png)  

    Om Sök resultatet för visnings loggen liknar bilden ovan, är du inställd på att använda instrument panelen för VMware-övervaknings lösningen.  

## <a name="vmware-data-collection-details"></a>Information om VMware data insamling
VMware-övervakning lösning samlar in olika prestanda mått och loggdata från ESXi-värdar med hjälp av de Log Analyticss agenter för Linux som du har aktiverat.

I följande tabell visas metoder för data insamling och annan information om hur data samlas in.

| plattform | Log Analytics agent för Linux | System Center Operations Manager agent | Azure Storage | Operations Manager krävs. | Operations Manager agent data som skickas via hanterings gruppen | samlings frekvens |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |var 3: e minut |

I följande tabell visas exempel på data fält som samlas in av VMware-övervakning-lösningen:

| fält namn | beskrivning |
| --- | --- |
| Device_s |VMware Storage-enheter |
| ESXIFailure_s |typer av problem |
| EventTime_t |tid när händelsen inträffade |
| HostName_s |ESXi-värdnamn |
| Operation_s |Skapa virtuell dator eller ta bort virtuell dator |
| ProcessName_s |händelse namn |
| ResourceId_s |namn på VMware-värden |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Hyper-V |
| SCSIStatus_s |VMware SCSI-status |
| SyslogMessage_s |Syslog-data |
| UserName_s |användare som skapade eller tog bort virtuell dator |
| VMName_s |VM-namn |
| Dator |värddator |
| TimeGenerated |tid då data genererades |
| DataCenter_s |VMware-datacenter |
| StorageLatency_s |lagrings svars tid (MS) |

## <a name="vmware-monitoring-solution-overview"></a>Översikt över VMware-övervakning lösning
VMware-panelen visas i din Log Analytics-arbetsyta. Det ger en övergripande bild av eventuella problem. När du klickar på panelen går du till en instrument panel.

![Skärm bild som visar VMware-panelen, som visar nio problem.](./media/vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Navigera i vyn instrument panel
I vyn **VMware** Dashboard organiseras blad efter:

* Antal felaktiga status
* Högsta värden efter antal händelser
* Antal antal händelser
* Aktiviteter för virtuella datorer
* Disk händelser för ESXi-värd

![solution1](./media/vmware/solutionview1-1.png)

![solution2](./media/vmware/solutionview1-2.png)

Klicka på ett blad för att öppna Log Analytics Sök fönster som visar detaljerad information om bladet.

Härifrån kan du redigera logg frågan för att ändra den för något som är speciell. Mer information om hur du skapar logg frågor finns i [hitta data med hjälp av logg frågor i Azure Monitor](../logs/log-query-overview.md).

#### <a name="find-esxi-host-events"></a>Hitta ESXi Host-händelser
En enskild ESXi-värd genererar flera loggar baserat på deras processer. VMware-övervaknings lösningen centraliserar dem och sammanfattar antalet händelser. Den här centraliserade vyn hjälper dig att förstå vilken ESXi-värd som har en stor mängd händelser och vilka händelser som förekommer oftast i din miljö.

![händelse](./media/vmware/events.png)

Du kan öka detalj nivån genom att klicka på en ESXi-värd eller en händelse typ.

När du klickar på ett ESXi-värdnamn visar du information från den ESXi-värden. Om du vill begränsa resultatet med händelse typen lägger du till `“ProcessName_s=EVENT TYPE”` i Sök frågan. Du kan välja **processname** i Sök filtret. Detta begränsar informationen åt dig.

![Skärm bild av ESXi-värden per händelse typ och uppdelning per händelse typ blad i vyn instrument panel för VMware-övervakning.](./media/vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Hitta hög VM-aktiviteter
En virtuell dator kan skapas och tas bort på alla ESXi-värdar. Det är bra för en administratör att identifiera hur många virtuella datorer en ESXi-värd skapar. Det i sin tur hjälper till att förstå prestanda-och kapacitets planering. Det är viktigt att hålla koll på händelser för VM-aktiviteter när du hanterar din miljö.

![Skärm bild av bladet för virtuella dator aktiviteter i VMware-övervakning instrument panelen, med en graf för att skapa och ta bort virtuella datorer av ESXi-värden.](./media/vmware/vmactivities1.png)

Om du vill se ytterligare data om att skapa virtuella ESXi-värdar klickar du på ett ESXi-värdnamn.

![Skärm bild av ett fönster från VMware-övervakning-instrumentpanelen som visar en tabell med en datarad för varje virtuell dator som skapas av en ESXi-värd.](./media/vmware/createvm.png)

#### <a name="common-log-queries"></a>Vanliga logg frågor
Lösningen innehåller andra användbara frågor som kan hjälpa dig att hantera dina ESXi-värdar, till exempel lagrings utrymme, lagrings svars tid och Sök vägs fel.

![Skärm bild som visar rekommenderade sökningar, som är användbara lagrade frågor.](./media/vmware/queries.png)


#### <a name="save-queries"></a>Spara frågor
Att spara logg frågor är en standard funktion i Azure Monitor och kan hjälpa dig att hålla alla frågor som du har hittat användbara. När du har skapat en fråga som du tycker är användbar sparar du den genom att klicka på **Favoriter**. Med en sparad fråga kan du enkelt återanvända den senare från [min instrument panels](../visualize/tutorial-logs-dashboards.md) sida där du kan skapa dina egna anpassade instrument paneler.

![Skärm bild som visar en del av en anpassad instrument panel med en loggs ökning med ikoner för att ångra, exportera, Avisera, spara, favoriter och historik.](./media/vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Skapa aviseringar från frågor
När du har skapat dina frågor kanske du vill använda frågorna för att varna dig när vissa händelser inträffar. Information om hur du skapar aviseringar finns [i varningar i Log Analytics](../alerts/alerts-overview.md) . Exempel på aviserings frågor och andra fråge exempel finns i [Övervaka VMware med Log Analytics](/archive/blogs/msoms/monitor-vmware-using-oms-log-analytics) blogg inlägg.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>Vad måste jag göra på ESXi-värd inställningen? Vilken effekt kommer den att ha i min nuvarande miljö?
Lösningen använder den inbyggda mekanismen för ESXi-värd för syslog. Du behöver ingen ytterligare Microsoft-programvara på ESXi-värden för att avbilda loggarna. Den bör ha en låg inverkan på din befintliga miljö. Du måste dock ange syslog-vidarebefordran, som är ESXI-funktioner.

### <a name="do-i-need-to-restart-my-esxi-host"></a>Måste jag starta om min ESXi-värd?
Nej. Den här processen kräver ingen omstart. Ibland uppdaterar vSphere inte syslog. I sådana fall måste du logga in på ESXi-värden och läsa in syslog igen. Du behöver inte starta om värden, så den här processen stör inte din miljö.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>Kan jag öka eller minska mängden logg data som skickas till Log Analytics?
Ja, du kan. Du kan använda inställningarna för värd loggnings nivå för ESXi i vSphere. Logg insamling baseras på *informations* nivån. Så om du vill granska skapandet eller borttagning av virtuella datorer måste du behålla *informations* nivån på värd. Mer information finns i VMware- [kunskaps basen](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Varför är värddatorn inte att tillhandahålla data för att Log Analytics? Min logg inställning är inställd på info.
Det uppstod ett fel i ESXi-värden för syslog-tidsstämpeln. Mer information finns i VMware- [kunskaps basen](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). När du har tillämpat lösningen ska du vara värd för att fungera normalt.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Kan jag använda flera ESXi-värdar för att vidarebefordra syslog-data till en enda virtuell dator med omsagent?
Ja. Du kan ha flera ESXi-värdar som vidarebefordrar till en enda virtuell dator med omsagent.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Varför visas inte data som flödar in i Log Analytics?
Det kan finnas flera skäl:

* ESXi-värden skickar inte data korrekt till den virtuella datorn som kör omsagent. Utför följande steg för att testa:

  1. Bekräfta genom att logga in på ESXi-värden med SSH och kör följande kommando: `nc -z ipaddressofVM 1514`

      Om detta inte lyckas är vSphere-inställningarna i den avancerade konfigurationen förmodligen felaktiga. Se [Konfigurera syslog-samling](#configure-syslog-collection) för information om hur du konfigurerar ESXi-värden för syslog-vidarebefordran.
  1. Om syslog-port anslutningen lyckas, men du inte ser några data, laddar du om syslog på ESXi-värden med hjälp av SSH för att köra följande kommando: `esxcli system syslog reload`
* Den virtuella datorn med Log Analytics agent har inte angetts korrekt. Testa detta genom att utföra följande steg:

  1. Log Analytics lyssnar på port 1514. Kontrol lera att den är öppen genom att köra följande kommando: `netstat -a | grep 1514`
  1. Du bör se porten `1514/tcp` öppen. Om du inte gör det kontrollerar du att omsagent har installerats korrekt. Om du inte ser portinformation är syslog-porten inte öppen på den virtuella datorn.

    a. Verifiera att Log Analytics Agent körs med hjälp av `ps -ef | grep oms` . Om den inte körs startar du processen genom att köra kommandot `sudo /opt/microsoft/omsagent/bin/service_control start`

     b. Öppna filen `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf`.

     c. Kontrol lera att rätt användar-och grupp inställning är giltig, ungefär så här: `-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

     d. Om filen inte finns, eller om användar-och grupp inställningen är fel, vidtar du åtgärder genom att [förbereda en Linux-server](#prepare-a-linux-server).

## <a name="next-steps"></a>Nästa steg
* Använd [logg frågor](../logs/log-query-overview.md) i Log Analytics om du vill visa detaljerade VMware-värdar.
* [Skapa dina egna instrument paneler](../visualize/tutorial-logs-dashboards.md) som visar VMware-värd data.
* [Skapa aviseringar](../alerts/alerts-overview.md) när vissa VMware-värd händelser inträffar.

