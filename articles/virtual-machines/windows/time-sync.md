---
title: Tidssynkronisering för virtuella Windows-datorer i Azure
description: Tidssynkronisering för virtuella Windows-datorer.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 85abc77757d31f3b1054a0670ea3f65a4fcb2e52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555966"
---
# <a name="time-sync-for-windows-vms-in-azure"></a>Tidssynkronisering för virtuella Windows-datorer i Azure

Tidssynkronisering är viktigt för säkerhet och händelse korrelation. Ibland används den för implementering av distribuerade transaktioner. Tids exakthet mellan flera dator system uppnås genom synkronisering. Synkronisering kan påverkas av flera saker, inklusive omstarter och nätverks trafik mellan tids källan och den dator som hämtar tiden. 

Azure backas nu upp av en infrastruktur som kör Windows Server 2016. Windows Server 2016 har förbättrat algoritmer som används för att korrigera tiden och villkor för den lokala klockan att synkroniseras med UTC.  Windows Server 2016 har också förbättrat VMICTimeSync-tjänsten som styr hur VM synkroniseras med värden för korrekt tid. Förbättringarna är mer exakta start tider för VM-start eller återställning av virtuella datorer och svars tids korrigering för exempel som tillhandahålls till Windows Time (W32time). 


>[!NOTE]
>Om du vill ha en snabb översikt över Windows tids tjänst kan du ta en titt på den här [översikts videon på hög nivå](https://aka.ms/WS2016TimeVideo).
>
> Mer information finns i [korrekt tid för Windows Server 2016](/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Översikt

Precisionen för en dator klocka är en mätare för hur nära datorns klocka är till UTC-tiden (Coordinated Universal Time). UTC definieras av ett multinationellt-exempel av exakta atomiska klockor som bara kan vara av en sekund på 300 år. Men för att läsa UTC kräver direkt specialiserad maskin vara. I stället synkroniseras tid servrar till UTC och används från andra datorer för att ge skalbarhet och robusthet. Det finns tidssynkroniserings tjänsten för alla datorer som vet vilka tids servrar som ska användas och kontrollerar regelbundet om datorns klocka måste korrigeras och justerar tid vid behov. 

Azure-värdar synkroniseras till interna Microsoft-tidsservrar som tar tid från Microsoft-ägda stratum 1-enheter, med GPS-antenner. Virtuella datorer i Azure kan antingen vara beroende av deras värd för att klara den korrekta tiden (*värd tiden*) på den virtuella datorn eller den virtuella datorn kan hämta tid från en tids Server eller en kombination av båda. 

Interaktionen mellan virtuella datorer och värden kan också påverka klockan. Under [underhåll av minnes underhåll](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)pausas virtuella datorer i upp till 30 sekunder. Till exempel, innan underhållet börjar VM-klockan visar 10:00:00 AM och 28 sekunder. När den virtuella datorn har återupptagits skulle klockan på den virtuella datorn fortfarande Visa 10:00:00 AM, vilket skulle vara 28 sekunder av. För att åtgärda detta övervakar VMICTimeSync-tjänsten vad som händer på värden och begär att ändringar ska ske på de virtuella datorerna för att kompensera.

VMICTimeSync-tjänsten körs i antingen ett exempel-eller synkroniseringstillstånd och påverkar bara klockan vidarebefordran. I exempel läge, som kräver att W32Time körs, avsöker VMICTimeSync-tjänsten värden var femte sekund och ger tids exempel till W32Time. I ungefär var 30: e sekund tar W32Time-tjänsten det senaste tids exemplet och använder den för att påverka gästens klocka. I Sync-läget aktive ras om en gäst har återupptagits eller om en gästs klocka går över 5 sekunder bakom värdens klocka. I de fall där W32Time-tjänsten körs på rätt sätt bör det senare fallet aldrig inträffa.

Utan att synkroniseringen fungerar skulle klockan på den virtuella datorn ha fel. Om det bara finns en virtuell dator kan det hända att påverkan inte är signifikant, om inte arbets belastningen kräver hög korrekt timekeeping. Men i de flesta fall har vi flera, sammankopplade virtuella datorer som använder tid för att spåra transaktioner och tiden måste vara konsekvent i hela distributionen. När tiden mellan de virtuella datorerna är annorlunda kan du se följande effekter:

- Autentiseringen Miss fungerar. Säkerhets protokoll som Kerberos-eller certifikat beroende teknik är beroende av att tiden är konsekvent över systemen. 
- Det är mycket svårt att ta reda på vad som har hänt i ett system om loggar (eller andra data) inte samtycker till tiden. Samma händelse skulle se ut som om den inträffade vid olika tidpunkter, vilket gör korrelationen svår.
- Om klockan är avstängd kan faktureringen beräknas felaktigt.

Det bästa resultatet för Windows-distributioner uppnås genom att använda Windows Server 2016 som gäst operativ system, vilket garanterar att du kan använda de senaste förbättringarna i tidssynkroniseringen.

## <a name="configuration-options"></a>Konfigurationsalternativ

Det finns tre alternativ för att konfigurera tidssynkronisering för virtuella Windows-datorer som finns i Azure:

- Värd tid och time.windows.com. Detta är standard konfigurationen som används i Azure Marketplace-avbildningar.
- Endast värd.
- Använd en annan, extern tids server med eller utan att använda värd tiden.


### <a name="use-the-default"></a>Använd standard

Som standard konfigureras virtuella Windows OS-avbildningar för W32Time för synkronisering från två källor: 

- NtpClient-providern, som hämtar information från time.windows.com.
- VMICTimeSync-tjänsten används för att kommunicera värd tiden med de virtuella datorerna och göra ändringar efter att den virtuella datorn har pausats för underhåll. Azure-värdar använder Microsoft-ägda stratum 1-enheter för att hålla rätt tid.

W32Time föredrar Tidsprovidern i följande prioritetsordning: stratum-nivå, rot fördröjning, rot-spridning, tids förskjutning. I de flesta fall föredrar W32Time på en virtuell Azure-dator värd tiden på grund av utvärderingen att det skulle göra att jämföra båda tids källorna. 

För domänanslutna datorer upprättar själva domänen tidssynkronisering, men skogs roten måste fortfarande ta tid från en plats och följande överväganden skulle fortfarande vara sant.


### <a name="host-only"></a>Endast värd 

Eftersom time.windows.com är en offentlig NTP-server måste synkroniseringen av den tid som krävs för att skicka trafik via Internet påverka kvaliteten negativt på hur lång tid synkroniseringen går. Om du tar bort time.windows.com genom att växla till synkronisering på värdbaserade kan du ibland förbättra dina tidssynkroniserings resultat.

Om du växlar till tidssynkronisering för endast värd blir det bra om du upplever problem med tidssynkroniseringen med hjälp av standard konfigurationen. Testa den värdbaserade synkroniseringen för att se om det skulle förbättra tidssynkroniseringen på den virtuella datorn. 

### <a name="external-time-server"></a>Extern tids Server

Om du har särskilda krav för tidssynkronisering finns det också ett alternativ för att använda externa tids servrar. Externa tids servrar kan ge en viss tid, vilket kan vara användbart för test scenarier, vilket säkerställer tids enhetlighet med datorer som finns i Data Center som inte kommer från Microsoft eller som hanterar skottår på ett visst sätt.

Du kan kombinera externa servrar med VMICTimeSync-tjänsten och VMICTimeProvider för att ge resultat som liknar standard konfigurationen. 

## <a name="check-your-configuration"></a>Kontrollera konfigurationen


Kontrol lera om NtpClient-tidsleverantören har kon figurer ATS för att använda explicita NTP-servrar (NTP) eller NT5DS (Domain Time Sync).

```
w32tm /dumpreg /subkey:Parameters | findstr /i "type"
```

Om den virtuella datorn använder NTP visas följande utdata:

```
Value Name                 Value Type          Value Data
Type                       REG_SZ              NTP
```

Om du vill se vilken tids server som är NtpClient-tidsleverantören använder du följande i en upphöjd kommando tolk:

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

Om den virtuella datorn använder standardvärdet så ser utdata ut så här:

```
NtpServer                  REG_SZ              time.windows.com,0x8
```


För att se vilken tidsprovider som används för närvarande.

```
w32tm /query /source
```


Här är de utdata du kan se och vad det skulle innebära:
    
- **Time.Windows.com** – i standard konfigurationen skulle W32Time Hämta tid från Time.Windows.com. Tidssynkroniseringens kvalitet beror på Internet anslutning till den och påverkas av paket fördröjningar. Detta är de vanligaste utdata som du skulle få på en fysisk dator.
- **Provider för konc.int. tidssynkronisering för virtuell dator**  – den virtuella datorn synkroniseras tid från värden. Detta är de vanligaste utdata som du skulle få på en virtuell dator som körs på Azure. 
- *Din domän Server* – den aktuella datorn finns i en domän och domänen definierar tidssynkroniseringens hierarki.
- *Vissa andra server* -W32Time konfigurerades explicit för att hämta tiden från den andra servern. Tidssynkroniseringens kvalitet beror på denna tids Server kvalitet.
- **Lokal CMOS-klocka** är inte synkroniserad. Du kan hämta dessa utdata om W32Time inte hade tillräckligt med tid för att starta efter en omstart eller när alla konfigurerade tids källor inte är tillgängliga.


## <a name="opt-in-for-host-only-time-sync"></a>Anmäl dig till endast värdbaserade Time-synkronisering

Azure arbetar ständigt med att förbättra tidssynkronisering på värdar och kan garantera att all tidssynkroniserings infrastruktur är samordnad i Microsoft-ägda Data Center. Om du har tidssynkroniserings problem med standard konfigurationen som föredrar att använda time.windows.com som primär tids källa, kan du använda följande kommandon för att välja att endast vara värd för tidssynkronisering.

Markera VMIC-providern som aktive rad. 

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\VMICTimeProvider /v Enabled /t REG_DWORD /d 1 /f
```

Markera att NTPClient-providern är inaktive rad.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\NtpClient /v Enabled /t REG_DWORD /d 0 /f
```

Starta om W32Time-tjänsten.

```
net stop w32time && net start w32time
```


## <a name="windows-server-2012-and-r2-vms"></a>Virtuella Windows Server 2012-och R2-datorer 

Windows Server 2012 och Windows Server 2012 R2 har olika standardinställningar för tidssynkronisering. W32Time är som standard konfigurerat på ett sätt som prioriterar tjänsternas låga kostnader över till exakt tid. 

Om du vill flytta dina Windows Server 2012-och 2012 R2-distributioner så att de använder de nyare standardvärdena som föredras precis som tiden kan du använda följande inställningar.

Uppdatera W32Time-röstningen och uppdaterings intervallen så att de matchar inställningarna för Windows Server 2016.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MinPollInterval /t REG_DWORD /d 6 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MaxPollInterval /t REG_DWORD /d 10 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v UpdateInterval /t REG_DWORD /d 100 /f
w32tm /config /update
```

För att W32Time ska kunna använda de nya avsöknings intervallen måste NtpServers markeras som att använda dem. Om servrarna är kommenterade med 0x1 bitflag-masken åsidosätter den här mekanismen och W32Time använder SpecialPollInterval i stället. Se till att de angivna NTP-servrarna antingen använder flaggan 0x8 eller ingen flagga alls:

Kontrol lera vilka flaggor som används för använda NTP-servrar.

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

## <a name="next-steps"></a>Nästa steg

Nedan visas länkar till mer information om tidssynkroniseringen:

- [Verktyg och inställningar för Windows tidstjänst](/windows-server/networking/windows-time-service/windows-time-service-tools-and-settings)
- [Förbättringar i Windows Server 2016 ](/windows-server/networking/windows-time-service/windows-server-2016-improvements)
- [Korrekt tid för Windows Server 2016](/windows-server/networking/windows-time-service/accurate-time)
- [Stöd gräns för att konfigurera Windows tids tjänst för miljöer med hög noggrannhet](/windows-server/networking/windows-time-service/support-boundary)
