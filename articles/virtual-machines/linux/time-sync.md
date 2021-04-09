---
title: Tidssynkronisering för virtuella Linux-datorer i Azure
description: Tidssynkronisering för virtuella Linux-datorer.
services: virtual-machines
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: cynthn
ms.openlocfilehash: 18c8570a8066985cab5263c4779787062dc32d75
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552651"
---
# <a name="time-sync-for-linux-vms-in-azure"></a>Tidssynkronisering för virtuella Linux-datorer i Azure

Tidssynkronisering är viktigt för säkerhet och händelse korrelation. Ibland används den för implementering av distribuerade transaktioner. Tids exakthet mellan flera dator system uppnås genom synkronisering. Synkronisering kan påverkas av flera saker, inklusive omstarter och nätverks trafik mellan tids källan och den dator som hämtar tiden. 

Azure backas upp av en infrastruktur som kör Windows Server 2016. Windows Server 2016 har förbättrat algoritmer som används för att korrigera tiden och villkor för den lokala klockan att synkroniseras med UTC.  Funktionen för korrekt tids period i Windows Server 2016 förbättrar hur VMICTimeSync-tjänsten som styr virtuella datorer med värden för korrekt tid. Förbättringarna är mer exakta start tider för VM-start eller återställning av virtuella datorer och korrigering av avbrottsbegäran. 

> [!NOTE]
> Om du vill ha en snabb översikt över Windows tids tjänst kan du ta en titt på den här [översikts videon på hög nivå](https://aka.ms/WS2016TimeVideo).
>
> Mer information finns i [korrekt tid för Windows Server 2016](/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Översikt

Precisionen för en dator klocka är en mätare för hur nära datorns klocka är till UTC-tiden (Coordinated Universal Time). UTC definieras av ett multinationellt-exempel av exakta atomiska klockor som bara kan vara av en sekund på 300 år. Men för att läsa UTC kräver direkt specialiserad maskin vara. I stället synkroniseras tid servrar till UTC och används från andra datorer för att ge skalbarhet och robusthet. Det finns tidssynkroniserings tjänsten för alla datorer som vet vilka tids servrar som ska användas och kontrollerar regelbundet om datorns klocka måste korrigeras och justerar tid vid behov. 

Azure-värdar synkroniseras till interna Microsoft-tidsservrar som tar tid från Microsoft-ägda stratum 1-enheter, med GPS-antenner. Virtuella datorer i Azure kan antingen vara beroende av deras värd för att klara den korrekta tiden (*värd tiden*) på den virtuella datorn eller den virtuella datorn kan hämta tid från en tids Server eller en kombination av båda. 

På fristående maskin vara läser Linux-operativsystemet bara värd maskin varu klockan vid start. Efter det underhålls klockan med avbrotts tids gränsen i Linux-kärnan. I den här konfigurationen kommer klockan att gå över tid. I nyare Linux-distributioner i Azure kan virtuella datorer använda VMICTimeSync-providern som ingår i Linux Integration Services (LIS) för att fråga efter klock uppdateringar från värden oftare.

Interaktionen mellan virtuella datorer och värden kan också påverka klockan. Under [underhåll av minnes underhåll](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)pausas virtuella datorer i upp till 30 sekunder. Till exempel, innan underhållet börjar VM-klockan visar 10:00:00 AM och 28 sekunder. När den virtuella datorn har återupptagits skulle klockan på den virtuella datorn fortfarande Visa 10:00:00 AM, vilket skulle vara 28 sekunder av. För att åtgärda detta övervakar VMICTimeSync-tjänsten vad som händer på värden och begär att ändringar ska ske på de virtuella datorerna för att kompensera.

Utan att synkroniseringen fungerar skulle klockan på den virtuella datorn ha fel. Om det bara finns en virtuell dator kan det hända att påverkan inte är signifikant, om inte arbets belastningen kräver hög korrekt timekeeping. Men i de flesta fall har vi flera, sammankopplade virtuella datorer som använder tid för att spåra transaktioner och tiden måste vara konsekvent i hela distributionen. När tiden mellan de virtuella datorerna är annorlunda kan du se följande effekter:

- Autentiseringen Miss fungerar. Säkerhets protokoll som Kerberos-eller certifikat beroende teknik är beroende av att tiden är konsekvent över systemen.
- Det är mycket svårt att ta reda på vad som har hänt i ett system om loggar (eller andra data) inte samtycker till tiden. Samma händelse skulle se ut som om den inträffade vid olika tidpunkter, vilket gör korrelationen svår.
- Om klockan är avstängd kan faktureringen beräknas felaktigt.



## <a name="configuration-options"></a>Konfigurationsalternativ

Det finns vanligt vis tre sätt att konfigurera tidssynkronisering för virtuella Linux-datorer som finns i Azure:

- Standard konfigurationen för Azure Marketplace-avbildningar använder både NTP-tid och VMICTimeSync-värd-tid. 
- Endast värd med VMICTimeSync.
- Använd en annan, extern tids server med eller utan att använda VMICTimeSync-värden-Time.


### <a name="use-the-default"></a>Använd standard

Som standard konfigureras de flesta Azure Marketplace-avbildningar för Linux till synkronisering från två källor: 

- NTP som primär, som hämtar tid från en NTP-server. Till exempel, Ubuntu 16,04 LTS Marketplace-bilder använder **NTP.Ubuntu.com**.
- VMICTimeSync-tjänsten som sekundär, används för att kommunicera värd tiden med de virtuella datorerna och göra ändringar efter att den virtuella datorn har pausats för underhåll. Azure-värdar använder Microsoft-ägda stratum 1-enheter för att hålla rätt tid.

I nyare Linux-distributioner tillhandahåller VMICTimeSync-tjänsten en maskinvaru-källa för precision Time Protocol (PTP), men tidigare distributioner kanske inte tillhandahåller den här klock källan och kommer att bli tillbaka till NTP för att hämta tid från värden.

Kör kommandot för att bekräfta att NTP synkroniseras korrekt `ntpq -p` .

### <a name="host-only"></a>Endast värd 

Eftersom NTP-servrar som time.windows.com och ntp.ubuntu.com är offentliga, måste synkroniseringen med dem skicka trafik via Internet. Varierande paket fördröjningar kan påverka kvaliteten på tidssynkroniseringen negativt. Att ta bort NTP genom att växla till synkronisering på värdbaserade kan ibland förbättra dina tidssynkroniserings resultat.

Om du växlar till tidssynkronisering för endast värd blir det bra om du upplever problem med tidssynkroniseringen med hjälp av standard konfigurationen. Testa den värdbaserade synkroniseringen för att se om det skulle förbättra tidssynkroniseringen på den virtuella datorn. 

### <a name="external-time-server"></a>Extern tids Server

Om du har särskilda krav för tidssynkronisering finns det också ett alternativ för att använda externa tids servrar. Externa tids servrar kan ge en viss tid, vilket kan vara användbart för test scenarier, vilket säkerställer tids enhetlighet med datorer som finns i Data Center som inte kommer från Microsoft eller som hanterar skottår på ett visst sätt.

Du kan kombinera en extern tids server med VMICTimeSync-tjänsten för att tillhandahålla resultat som liknar standard konfigurationen. Att kombinera en extern tids server med VMICTimeSync är det bästa alternativet för att hantera problem som kan uppstå när virtuella datorer pausas för underhåll. 

## <a name="tools-and-resources"></a>Verktyg och resurser

Det finns några grundläggande kommandon för att kontrol lera din tidssynkroniserings konfiguration. Dokumentationen för Linux-distribution innehåller mer information om det bästa sättet att konfigurera tidssynkronisering för den distributionen.

### <a name="integration-services"></a>Integreringstjänster

Kontrol lera om integrations tjänsten (hv_utils) har lästs in.

```bash
lsmod | grep hv_utils
```
Du bör se något som liknar detta:

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

Se om daemon för Hyper-V integrations tjänster körs.

```bash
ps -ef | grep hv
```

Du bör se något som liknar detta:

```
root        229      2  0 17:52 ?        00:00:00 [hv_vmbus_con]
root        391      2  0 17:52 ?        00:00:00 [hv_balloon]
```


### <a name="check-for-ptp-clock-source"></a>Kontrol lera om det finns en PTP-klocka

I nyare versioner av Linux är en PTP-datakälla (precision Time Protocol) tillgänglig som en del av VMICTimeSync-providern. I äldre versioner av Red Hat Enterprise Linux eller CentOS 7. x kan [Linux integrations tjänsterna](https://github.com/LIS/lis-next) hämtas och användas för att installera den uppdaterade driv rutinen. När PTP-klockan är tillgänglig kommer Linux-enheten att ha formatet/dev/PTP *x*. 

Se vilka PTP-klock källor som är tillgängliga.

```bash
ls /sys/class/ptp
```

I det här exemplet är det returnerade värdet *ptp0*, så vi använder det för att kontrol lera klock namnet. Kontrol lera att det finns ett klock namn för att verifiera enheten.

```bash
cat /sys/class/ptp/ptp0/clock_name
```

Detta bör returnera `hyperv` .

### <a name="chrony"></a>chrony

I Ubuntu 19,10 och senare versioner, Red Hat Enterprise Linux och CentOS 8. x är [chrony](https://chrony.tuxfamily.org/) konfigurerat för att använda en PTP-datakälla. I stället för chrony använder äldre Linux-versioner Network Time Protocol Daemon (ntpd), som inte stöder PTP-källor. Om du vill aktivera PTP i dessa versioner måste chrony installeras och konfigureras manuellt (i chrony. conf) med hjälp av följande kod:

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0
```

Mer information om Ubuntu och NTP finns i [tidssynkronisering](https://ubuntu.com/server/docs/network-ntp).

Mer information om Red Hat och NTP finns i [Konfigurera NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_ntpd#s1-Configure_NTP). 

Mer information om chrony finns i [använda chrony](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_the_chrony_suite#sect-Using_chrony).

Om både chrony-och VMICTimeSync-källor aktive ras samtidigt kan du markera en som **prioriterad**, vilket anger den andra källan som en säkerhets kopia. Eftersom NTP-tjänster inte uppdaterar klockan för stora förvrängningar utom efter en lång period, kommer VMICTimeSync att återställa klockan från pausade virtuella dator händelser mycket snabbare än med endast NTP-baserade verktyg.

Som standard påskyndar eller saktar chronyd system klockan för att åtgärda eventuella tids avvikelser. Om driften blir för stor kan chrony inte åtgärda driften. För att lösa detta `makestep` kan parametern i **/etc/chrony.conf** ändras för att tvinga en tidssynkronisering om driften överskrider tröskelvärdet som anges.

 ```bash
makestep 1.0 -1
```

Här kommer chrony att framtvinga en tids uppdatering om driften är större än 1 sekund. Om du vill tillämpa ändringarna startar du om chronyd-tjänsten:

```bash
systemctl restart chronyd
```

### <a name="systemd"></a>system 

På SUSE och Ubuntu-versioner före 19,10 konfigureras tidssynkroniseringen med [system](https://www.freedesktop.org/wiki/Software/systemd/). Mer information om Ubuntu finns i [tids synkronisering](https://help.ubuntu.com/lts/serverguide/NTP.html). Mer information om SUSE finns i avsnittet 4.5.8 i [SUSE Linux Enterprise Server 12 SP3-versions anteckningar](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement).

## <a name="next-steps"></a>Nästa steg

Mer information finns i [korrekt tid för Windows Server 2016](/windows-server/networking/windows-time-service/accurate-time).


