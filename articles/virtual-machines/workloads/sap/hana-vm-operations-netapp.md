---
title: SAP HANA konfiguration av Azure Virtual Machine ANF | Microsoft Docs
description: Azure NetApp Files lagrings rekommendationer för SAP HANA.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, Azure, ANF, HANA, Azure NetApp Files, ögonblicks bild
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/23/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: de8af71928ad6a83d4930e4e6e0b8dd257148111
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101666623"
---
# <a name="nfs-v41-volumes-on-azure-netapp-files-for-sap-hana"></a>NFS v4.1-volymer på Azure NetApp Files för SAP HANA

Azure NetApp Files tillhandahåller interna NFS-resurser som kan användas för **/Hana/Shared**-, **/Hana/data**-och **/Hana/log** -volymer. Att använda ANF-baserade NFS-resurser för **/Hana/data** -och **/Hana/log** -volymerna kräver att v 4.1 NFS-protokollet används. NFS-protokollet v3 stöds inte för användning av **/Hana/data** -och **/Hana/log** -volymer när du baserar resurserna på ANF. 


> [!IMPORTANT]
> Det NFS v3-protokoll som implementeras på Azure NetApp Files stöds **inte** för användning för **/Hana/data** och **/Hana/log**. Användningen av NFS 4,1 är obligatorisk för **/Hana/data** -och **/Hana/log** -volymer från en funktionell punkt i vyn. För **/Hana/Shared** -volymen kan NFS v3 eller NFS v 4.1-protokollet användas från en funktionell punkt i vyn.

## <a name="important-considerations"></a>Att tänka på

Tänk på följande viktiga överväganden när du överväger Azure NetApp Files för SAP-NetWeaver och SAP HANA:

- Pool för minsta kapacitet är 4 TiB  
- Den minsta volym storleken är 100 GiB
- Azure NetApp Files och alla virtuella datorer, där Azure NetApp Files volymer är monterade, måste finnas i samma Azure-Virtual Network eller i [peer-anslutna virtuella nätverk](../../../virtual-network/virtual-network-peering-overview.md) i samma region
- Det är viktigt att de virtuella datorerna distribueras i närheten av Azure NetApp-lagringen för låg latens.  
- Det valda virtuella nätverket måste ha ett undernät, delegerat till Azure NetApp Files
- Se till att svars tiden från databas servern till ANF-volymen mäts och under 1 millisekund
- Data flödet för en Azure NetApp-volym är en funktion av volym kvoten och tjänst nivån, enligt beskrivningen i [service nivå för Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). När du ändrar storlek på HANA Azure NetApp-volymer ser du till att det resulterande data flödet uppfyller HANA-system kraven
- Försök att "konsolidera" volymer för att uppnå mer prestanda i en större volym, till exempel använda en volym för/sapmnt,/usr/SAP/trans,... om möjligt  
- Azure NetApp Files erbjuder [export princip](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md): du kan kontrol lera tillåtna klienter, åtkomst typ (Läs&Skriv, skrivskyddad, osv.). 
- Azure NetApp Files funktionen är inte en zon medveten än. För närvarande är Azure NetApp Files funktionen inte distribuerad i alla tillgänglighets zoner i en Azure-region. Var medveten om potentiella fördröjnings konsekvenser i vissa Azure-regioner.   
- Användar-ID för <b>sid</b>-adm och grupp-ID för `sapsys` på de virtuella datorerna måste matcha konfigurationen i Azure NetApp Files. 

> [!IMPORTANT]
> För SAP HANA arbets belastningar är låg latens kritiskt. Arbeta med din Microsoft-representant för att säkerställa att de virtuella datorerna och Azure NetApp Files volymerna distribueras i nära närhet.  

> [!IMPORTANT]
> Om det finns ett matchnings fel mellan användar-ID för <b>sid</b>-adm och grupp-ID för `sapsys` mellan den virtuella datorn och Azure NetApp-konfigurationen, visas behörigheterna för filer på Azure NetApp-volymer, monterade på den virtuella datorn, som `nobody` . Se till att ange rätt användar-ID för <b>sid</b>-adm och grupp-ID för `sapsys` , när [ett nytt system](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) ska Azure NetApp Files.


## <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Storlek för HANA-databas på Azure NetApp Files

Data flödet för en Azure NetApp-volym är en funktion i volym storlek och tjänst nivå, enligt beskrivningen i [service nivå för Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). 

Viktigt att förstå är prestanda förhållandet storlek och att det finns fysiska gränser för ett LIF (logiskt gränssnitt) för SVM (virtuell lagrings dator).

Tabellen nedan visar att det kan vara bra att skapa en stor "standard"-volym för att lagra säkerhets kopior och att det inte är klokt att skapa en "Ultra"-volym som är större än 12 TB eftersom den fysiska bandbredds kapaciteten för en enskild LIF skulle överskridas. 

Maximalt data flöde för en LIF och en enda Linux-session är mellan 1,2 och 1,4 GB/s. Om du behöver mer data flöde för/Hana/data kan du använda partitioneringen SAP HANA datapartitionering för att skikta I/O-aktiviteten under data belastningen eller HANA-lagrings punkter över flera HANA-datafiler som finns på flera NFS-resurser. Mer information om HANA data Volume randering finns i följande artiklar:

- [Administratörs hand boken för HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/40b2b2a880ec4df7bac16eae3daef756.html?q=hana%20data%20volume%20partitioning)
- [Blogg om SAP HANA – partitionering av data volymer](https://blogs.sap.com/2020/10/07/sap-hana-partitioning-data-volumes/)
- [SAP-anteckning #2400005](https://launchpad.support.sap.com/#/notes/2400005)
- [SAP-anteckning #2700123](https://launchpad.support.sap.com/#/notes/2700123)


| Storlek  | Data flödes standard | Data flöde Premium | Data flöde Ultra |
| --- | --- | --- | --- |
| 1 TB | 16 MB/SEK | 64 MB/SEK | 128 MB/SEK |
| 2 TB | 32 MB/SEK | 128 MB/SEK | 256 MB/SEK |
| 4 TB | 64 MB/SEK | 256 MB/SEK | 512 MB/SEK |
| 10 TB | 160 MB/SEK | 640 MB/SEK | 1 280 MB/SEK |
| 15 TB | 240 MB/SEK | 960 MB/SEK | 1 400 MB/SEK |
| 20 TB | 320 MB/SEK | 1 280 MB/SEK | 1 400 MB/SEK |
| 40 TB | 640 MB/SEK | 1 400 MB/SEK | 1 400 MB/SEK |

Det är viktigt att förstå att data skrivs till samma SSD i lagrings Server delen. Prestanda kvoten från kapacitets gruppen skapades för att kunna hantera miljön.
KPI: er för lagring är samma för alla HANA-databas storlekar. I nästan alla fall reflekterar detta antagande inte verkligheten och kunden förväntar sig. Storleken på HANA-system innebär inte nödvändigt vis att ett litet system kräver lagrings data flöde med låg belastning – och ett stort system kräver data flöde med hög kapacitet. Men vanligt vis kan vi vänta högre data flödes krav för större HANA-databas instanser. Som ett resultat av SAP: s storleks regler för underliggande maskin vara kan sådana större HANA-instanser även ge mer processor resurser och högre parallellitet i aktiviteter som att läsa in data efter en instans omstart. Därför bör volym storlekarna antas för kundens förväntningar och krav. Och inte bara drivs av rena kapacitets krav.

När du utformar infrastrukturen för SAP i Azure bör du vara medveten om några lägsta krav för lagrings data flöde (för produktion system) av SAP, som översätts till lägsta data flödes egenskaper för:

| Typ av volym och I/O-typ | Minsta KPI som krävs av SAP | Premium service-nivå | Ultra service-nivå |
| --- | --- | --- | --- |
| Skrivning av logg volym | 250 MB/sek | 4 TB | 2 TB |
| Skrivning av data volym | 250 MB/sek | 4 TB | 2 TB |
| Läsning av data volym | 400 MB/SEK | 6,3 TB | 3,2 TB |

Eftersom alla tre KPI: er krävs måste **/Hana/data** -volymen anpassas till den större kapaciteten för att uppfylla minimi kraven för läsning.

ANF volym storlekar kan vara mindre för HANA-system, som inte kräver hög bandbredd. Och om ett HANA-system kräver mer data flöde kan volymen anpassas genom att ändra storlek på kapaciteten online. Inga KPI: er har definierats för säkerhets kopierings volymer. Data flödet för säkerhets kopiering av volymer är dock viktigt för en bra presterande miljö. Logga – och data volym prestanda måste vara utformade för kundens förväntningar.

> [!IMPORTANT]
> Oberoende av kapaciteten som du distribuerar på en enskild NFS-volym förväntas data flödet till platå i intervallet 1.2 – 1,4 GB/SEK som används av en konsument i en virtuell dator. Detta måste göra med den underliggande arkitekturen i ANF-erbjudandet och relaterade Linux-sessionsgränser runt NFS. Prestanda-och data flödes numren som dokumenteras i artikeln prestandatest [resultat för Azure NetApp Files](../../../azure-netapp-files/performance-benchmarks-linux.md) utfördes mot en delad NFS-volym med flera virtuella klient datorer och på grund av flera sessioner. Det scenariot är annorlunda för det scenario vi mäter i SAP. Där vi mäter data flödet från en enskild virtuell dator mot en NFS-volym. Finns på ANF.

För att uppfylla de lägsta data flödes kraven i SAP för data och logg, och enligt rikt linjerna för **/Hana/Shared**, skulle de rekommenderade storlekarna se ut så här:

| Volym | Storlek<br /> Premium Storage nivå | Storlek<br /> Ultra Storage-nivå | NFS-protokoll som stöds |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v 4.1 |
| /hana/data | 6,3 TiB | 3,2 TiB | v 4.1 |
| /Hana/Shared-skalning | Min (1 TB, 1 x RAM-minne)  | Min (1 TB, 1 x RAM-minne) | v3 eller v 4.1 |
| /Hana/Shared-utskalning | 1 x RAM för arbetsnoden<br /> per 4 Worker-noder  | 1 x RAM för arbetsnoden<br /> per 4 Worker-noder  | v3 eller v 4.1 |
| /hana/logbackup | 3 x RAM  | 3 x RAM | v3 eller v 4.1 |
| /hana/backup | 2 x RAM  | 2 x RAM | v3 eller v 4.1 |

För alla volymer rekommenderas NFS v 4.1 starkt

Storlekarna för säkerhets kopierings volymerna är uppskattningar. De exakta kraven måste definieras baserat på arbets belastning och drifts processer. För säkerhets kopieringar kan du konsolidera många volymer för olika SAP HANA-instanser till en (eller två) större volymer, vilket kan ha en lägre service nivå av ANF.

> [!NOTE]
> De Azure NetApp Files, de storleks rekommendationer som anges i det här dokumentet riktar sig till de lägsta kraven SAP Express gentemot sina infrastruktur leverantörer. I verkliga kund distributioner och arbets belastnings scenarier, är det inte tillräckligt. Använd de här rekommendationerna som en start punkt och anpassa baserat på kraven för din särskilda arbets belastning.  

Därför kan du överväga att distribuera liknande data flöde för de ANF-volymer som anges för Ultra disk Storage redan. Tänk också på storlekarna för de storlekar som anges för volymerna för de olika VM SKU: erna som utförda i de Ultra disk-tabellerna redan.

> [!TIP]
> Du kan ändra storlek på Azure NetApp Files volymer dynamiskt, utan att behöva `unmount` volymerna, stoppa de virtuella datorerna eller stoppa SAP HANA. Det gör det möjligt att uppfylla ditt program både förväntat och oförutsedda data flödes krav.

Dokumentation om hur du distribuerar en SAP HANA skalbar konfiguration med en standby-nod med hjälp av NFS v 4.1-volymer som finns i ANF publiceras i [SAP HANA skala ut med noden vänte läge på virtuella Azure-datorer med Azure NetApp Files på SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md).


## <a name="availability"></a>Tillgänglighet
ANF system uppdateringar och uppgraderingar tillämpas utan att påverka kundens miljö. Det definierade [service avtalet är 99,99%](https://azure.microsoft.com/support/legal/sla/netapp/).


## <a name="volumes-and-ip-addresses-and-capacity-pools"></a>Volymer och IP-adresser och kapacitets grupper
Med ANF är det viktigt att förstå hur den underliggande infrastrukturen skapas. En kapacitets grupp är bara en struktur som gör det enklare att skapa en fakturerings modell för ANF. En kapacitets grupp har ingen fysisk relation till den underliggande infrastrukturen. Om du skapar en pool för kapacitet skapas endast ett gränssnitt som kan debiteras, inte mer. När du skapar en volym skapas den första SVM (virtuell lagrings dator) på ett kluster med flera NetApp-system. En enda IP-adress skapas för den här SVM för att få åtkomst till volymen. Om du skapar flera volymer distribueras alla volymer i den här SVM över det här NetApp-klustret med flera kontrollanter. Även om du bara får en IP-adress distribueras data över flera styrenheter. ANF har en logik som automatiskt distribuerar kund arbets belastningar när volymerna eller/och kapaciteten för det konfigurerade lagrings utrymmet når en intern fördefinierad nivå. Du kan lägga märke till sådana fall eftersom en ny IP-adress tilldelas åtkomst till volymerna.

##<a name="log-volume-and-log-backup-volume"></a>Logg volym och logg säkerhets kopierings volym
"Logg volym" (**/Hana/log**) används för att skriva loggen för att göra om online-loggen. Därför finns det öppna filer som finns i den här volymen och det är ingen mening att skapa en ögonblicks bild av volymen. Online-gör om-loggfiler arkiveras eller säkerhets kopie ras till logg säkerhets kopierings volymen när logg filen för att göra om online-loggen är full eller en återställnings logg säkerhets kopia körs. För att ge rimlig säkerhets kopierings prestanda kräver logg säkerhets kopierings volymen ett bra data flöde. För att optimera lagrings kostnaderna kan det vara klokt att konsolidera logg säkerhets kopierings volymen på flera HANA-instanser. Så att flera HANA-instanser utnyttjar samma volym och skriv säkerhets kopiorna i olika kataloger. Med en sådan konsolidering kan du få mer data flöde med eftersom du behöver göra volymen lite större. 

Samma gäller för volymen som du använder för att skriva fullständiga HANA-databasens säkerhets kopior.  
 

## <a name="backup"></a>Backup
Förutom att strömma säkerhets kopieringar och Azures back tjänst som säkerhetskopierar SAP HANA databaser enligt beskrivningen i artikeln [säkerhets kopierings guide för SAP HANA på Azure Virtual Machines](./sap-hana-backup-guide.md), öppnar Azure NetApp Files möjligheten att utföra lagrings ögonblicks bild säkerhets kopior. 

SAP HANA stöder:

- Lagring-baserade ögonblicks bild säkerhets kopior från SAP HANA 1,0 SPS7 på
- Lagrings-baserad ögonblicks bilds säkerhets kopierings stöd för MDC (multidatabas container) HANA-miljöer från SAP HANA 2,0 SPS4 på


Att skapa lagrings ögonblicks bilds säkerhets kopior är en enkel procedur i fyra steg. 
1. Skapa en HANA (intern) databas ögonblicks bild – en aktivitet som du eller verktyg måste utföra 
1. SAP HANA skriver data till datafilerna för att skapa ett konsekvent tillstånd på lagrings platsen – HANA utför det här steget som ett resultat av att skapa en HANA-ögonblicksbild
1. Skapa en ögonblicks bild på **/Hana/data** -volymen på lagrings platsen – ett steg som du eller verktyg måste utföra. Du behöver inte utföra en ögonblicks bild på **/Hana/log** -volymen
1. Ta bort den här ögonblicks bilden av HANA (intern) och återuppta normal åtgärd – ett steg som du eller verktygen behöver utföra

> [!WARNING]
> Det senaste steget eller det gick inte att utföra det sista steget har en allvarlig inverkan på SAP HANAens minnes krav och kan leda till en paus av SAP HANA

```
BACKUP DATA FOR FULL SYSTEM CREATE SNAPSHOT COMMENT 'SNAPSHOT-2019-03-18:11:00';
```

![ANF ögonblicks bild säkerhets kopiering för SAP HANA](media/hana-vm-operations-netapp/storage-snapshot-scenario.png)

```
az netappfiles snapshot create -g mygroup --account-name myaccname --pool-name mypoolname --volume-name myvolname --name mysnapname 
```

![ANF ögonblicks bild säkerhets kopiering för SAP HANA part2](media/hana-vm-operations-netapp/storage-snapshot.png)

```
BACKUP DATA FOR FULL SYSTEM CLOSE SNAPSHOT BACKUP_ID 47110815 SUCCESSFUL SNAPSHOT-2020-08-18:11:00';
```

Den här proceduren för ögonblicks bilds säkerhets kopiering kan hanteras på flera olika sätt med olika verktyg. Ett exempel är python-skriptet "ntaphana_azure. py" som finns på GitHub [https://github.com/netapp/ntaphana](https://github.com/netapp/ntaphana) det här är exempel kod, som tillhandahålls "i befintligt skick" utan underhåll eller support.



> [!CAUTION]
> En ögonblicks bild i sig är inte en skyddad säkerhets kopia eftersom den finns på samma fysiska lagrings volym som den volym du just tog en ögonblicks bild av. Det är obligatoriskt att "skydda" minst en ögonblicks bild per dag till en annan plats. Detta kan göras i samma miljö, i en fjärran sluten Azure-region eller Azure Blob Storage.


För användare av CommVault backup-produkter är ett andra alternativ CommVault IntelliSnap V. 11.21 och senare. Den här eller senare versionen av CommVault-erbjudandet Azure NetApp Files support. Artikeln [CommVault IntelliSnap 11,21](https://documentation.commvault.com/11.21/essential/116350_getting_started_with_backup_and_restore_operations_for_azure_netapp_file_services_smb_shares_and_nfs_shares.html) innehåller mer information.


### <a name="back-up-the-snapshot-using-azure-blob-storage"></a>Säkerhetskopiera ögonblicks bilden med Azure Blob Storage
Säkerhetskopiera till Azure Blob Storage är en kostnads effektiv och snabb metod för att spara ANF-baserade HANA-säkerhetskopiering av ögonblicks bilder av säkerhets kopior. AzCopy-verktyget rekommenderas om du vill spara ögonblicks bilderna till Azure Blob Storage. Hämta den senaste versionen av verktyget och installera den, till exempel i katalogen bin där python-skriptet från GitHub är installerat.
Hämta det senaste AzCopy-verktyget:

```
root # wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1
Saving to: ‘azcopy_v10.tar.gz’
```

Den mest avancerade funktionen är SYNC-alternativet. Om du använder alternativet SYNC behåller AzCopy källan och mål katalogen synkroniseras. Användningen av parametern **--Delete-destination** är viktigt. Utan den här parametern tar AzCopy inte bort filer på mål platsen och utrymmes användningen på mål sidan växer. Skapa en Block Blob-behållare i ditt Azure Storage-konto. Skapa sedan SAS-nyckeln för BLOB-behållaren och synkronisera mappen ögonblicks bilder med Azure Blob-behållaren.

Till exempel, om en daglig ögonblicks bild ska synkroniseras med Azure Blob-behållaren för att skydda data. Och bara att en ögonblicks bild ska behållas kan du använda kommandot nedan.

```
root # > azcopy sync '/hana/data/SID/mnt00001/.snapshot' 'https://azacsnaptmytestblob01.blob.core.windows.net/abc?sv=2021-02-02&ss=bfqt&srt=sco&sp=rwdlacup&se=2021-02-04T08:25:26Z&st=2021-02-04T00:25:26Z&spr=https&sig=abcdefghijklmnopqrstuvwxyz' --recursive=true --delete-destination=true
```

## <a name="next-steps"></a>Nästa steg
Läs artikeln:

- [SAP HANA hög tillgänglighet för virtuella Azure-datorer](./sap-hana-availability-overview.md)