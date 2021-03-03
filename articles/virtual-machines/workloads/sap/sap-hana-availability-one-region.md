---
title: SAP HANA tillgänglighet inom en Azure-region | Microsoft Docs
description: Beskriver SAP HANA åtgärder på virtuella Azure-datorer i en Azure-region.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 83c56c337e2b07175dec28cfefa5da75dab7b4f0
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667953"
---
# <a name="sap-hana-availability-within-one-azure-region"></a>SAP HANA tillgänglighet inom en Azure-region
I den här artikeln beskrivs flera tillgänglighets scenarier i en Azure-region. Azure har många regioner, sprids över hela världen. En lista över Azure-regioner finns i [Azure-regioner](https://azure.microsoft.com/regions/). För att distribuera SAP HANA på virtuella datorer inom en Azure-region, erbjuder Microsoft distribution av en enskild virtuell dator med en HANA-instans. För ökad tillgänglighet kan du distribuera två virtuella datorer med två HANA-instanser i en [Azures tillgänglighets uppsättning](../../windows/tutorial-availability-sets.md) som använder Hana-systemreplikering för tillgänglighet. 

För närvarande erbjuder Azure [Azure-tillgänglighetszoner](../../../availability-zones/az-overview.md). I den här artikeln beskrivs inte Tillgänglighetszoner i detalj. Men det innehåller en allmän diskussion om hur du använder tillgänglighets uppsättningar jämfört med Tillgänglighetszoner.

Azure-regioner där Tillgänglighetszoner erbjuds har flera data Center. Data centren är oberoende av utbudet av ström källor, kylning och nätverk. Skälet för att erbjuda olika zoner inom en enda Azure-region är att distribuera program över två eller tre Tillgänglighetszoner som erbjuds. Distribution över zoner, problem i kraft och nätverk som bara påverkar en Azures tillgänglighets zon infrastruktur fungerar fortfarande program distributionen i en Azure-region. Viss reducerad kapacitet kan uppstå. Till exempel kan virtuella datorer i en zon gå förlorade, men virtuella datorer i de andra två zonerna är fortfarande igång. 
 
En Azure-tillgänglighets uppsättning är en logisk grupperings funktion som hjälper till att se till att de VM-resurser som du placerar i tillgänglighets uppsättningen inte är isolerade från varandra när de distribueras i ett Azure-datacenter. Azure ser till att de virtuella datorer du placerar i en tillgänglighetsuppsättning körs över flera fysiska servrar, datarack, lagringsenheter och nätverksväxlar. I vissa Azure-dokumentation kallas den här konfigurationen för placeringar i olika [uppdaterings-och fel domäner](../../manage-availability.md). Dessa platser är vanligt vis i ett Azure-datacenter. Om du antar att strömförsörjnings källan och nätverks problem skulle påverka data centret som du distribuerar, kommer all din kapacitet i en Azure-region att påverkas.

Placeringen av data Center som representerar Azure-tillgänglighetszoner är en kompromiss mellan att leverera acceptabel nätverks fördröjning mellan tjänster som har distribuerats i olika zoner och ett avstånd mellan data Center. Naturlig katastrofer bör helst inte påverka strömförsörjningen, nätverks utbudet och infrastrukturen för alla Tillgänglighetszoner i den här regionen. Men när Monumental naturlig katastrofer har visats kan Tillgänglighetszoner inte alltid tillhandahålla den tillgänglighet som du vill ha i en region. Tänk på orkan Maria som nådde ön Republiken Puerto Rico den 20 september 2017. Orkan har i princip en nästan 100 procents inaktive rad på 90 – mil-wide-ön.

## <a name="single-vm-scenario"></a>Scenario med en virtuell dator

I ett scenario med en enda virtuell dator skapar du en virtuell Azure-dator för SAP HANA-instansen. Du använder Azure Premium Storage som värd för operativ system disken och alla dina data diskar. Service avtalet för Azure drift tid på 99,9 procent och service avtal för andra Azure-komponenter räcker för att du ska kunna uppfylla din tillgänglighets service avtal för dina kunder. I det här scenariot behöver du inte använda en Azures tillgänglighets uppsättning för virtuella datorer som kör DBMS-skiktet. I det här scenariot förlitar du dig på två olika funktioner:

- Automatisk omstart av Azure VM (kallas även Azure Service lagning)
- SAP HANA automatisk omstart

Automatisk omstart av Azure VM eller tjänst återställning är en funktion i Azure som fungerar på två nivåer:

- Azure Server-värden kontrollerar hälsan för en virtuell dator som finns på Server värden.
- Azure Fabric-kontrollanten övervakar Server värdens hälso tillstånd och tillgänglighet.

En hälso kontrolls funktion övervakar hälsan för varje virtuell dator som finns på en Azure Server-värd. Om en virtuell dator hamnar i ett icke-felfritt tillstånd, kan en omstart av den virtuella datorn initieras av Azures värd agent som kontrollerar hälso tillståndet för den virtuella datorn. Infrastruktur styrenheten kontrollerar värdens hälso tillstånd genom att kontrol lera många olika parametrar som kan tyda på problem med värd maskin varan. Den kontrollerar också om värdens tillgänglighet via nätverket. En indikation på problem med värden kan leda till följande händelser:

- Om värden signalerar ett felaktigt hälso tillstånd utlöses en omstart av värden och en omstart av de virtuella datorer som kördes på värden.
- Om värden inte är i felfritt tillstånd efter att datorn har startats om startas en omdistribution av de virtuella datorerna som ursprungligen fanns på den felaktiga noden till en felfria värd Server. I det här fallet markeras den ursprungliga värden som ej felfri. Den används inte för ytterligare distributioner förrän den har rensats eller ersatts.
- Om den felaktiga värden har problem under omstarten utlöses en omedelbar omstart av de virtuella datorerna på en felfri värd. 

Med värd-och VM-övervakningen som tillhandahålls av Azure startas virtuella Azure-datorer som upplever värd problem automatiskt på en felfri Azure-värd. 

>[!IMPORTANT]
>Azure Service lagning startar inte om virtuella Linux-datorer där gäst operativ systemet är i ett kernel-panik tillstånd. Standardinställningarna för de mest använda Linux-versionerna, startar inte om virtuella datorer eller servrar där Linux-kärnan är i panik-läge. I stället är standardvärdet att hålla operativ systemet i kernel-panik tillstånd att koppla en kernel-felsökare att analysera. Azure följer detta beteende genom att inte automatiskt starta om en virtuell dator med gäst operativ systemet i ett sådant tillstånd. Antagande är att sådana förekomster är ytterst sällsynta. Du kan skriva över standard beteendet för att aktivera en omstart av den virtuella datorn. Om du vill ändra standard beteendet aktiverar du parametern ' kernel. panik ' i/etc/sysctl.conf. Tiden som du har angett för den här parametern är i sekunder. Vanliga rekommenderade värden är att vänta i 20-30 sekunder innan omstart startas genom den här parametern. Se även <https://gitlab.com/procps-ng/procps/blob/master/sysctl.conf> .

Den andra funktionen som du förlitar dig på i det här scenariot är det faktum att den HANA-tjänst som körs i en omstartad virtuell dator startar automatiskt när den virtuella datorn har startats om. Du kan ställa in [Hana-tjänsten automatisk omstart](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) via övervaknings tjänsterna i de olika Hana-tjänsterna.

Du kan förbättra det här scenariot med en enskild virtuell dator genom att lägga till en kall redundans i en SAP HANA konfiguration. I SAP HANA-dokumentationen kallas den här inställningen för [Automatisk redundansväxling](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html). Den här konfigurationen kan vara begriplig i en lokal distributions situation där server maskin varan är begränsad och du dedicera en nod med en enda server som den nod som är värd för automatisk redundans för en uppsättning produktions värdar. Men i Azure, där den underliggande infrastrukturen i Azure tillhandahåller en felfri mål server för en lyckad VM-omstart, är det inte bra att distribuera SAP HANA värden för automatisk redundans. På grund av Azure Service relagning finns det ingen referens arkitektur som förväntar sig en standby-nod för den automatiska redundansväxlingen i HANA-värden. 

### <a name="special-case-of-sap-hana-scale-out-configurations-in-azure"></a>Specialfall av SAP HANA skalbara konfigurationer i Azure
Hög tillgänglighet för SAP HANA skalbara konfigurationer förlitar sig på service återställning av virtuella Azure-datorer och omstart av SAP HANA-instansen eftersom den virtuella datorn är igång igen. Arkitekturer med hög tillgänglighet baserat på HANA-systemreplikering kommer att införas vid ett senare tillfälle. 


## <a name="availability-scenarios-for-two-different-vms"></a>Tillgänglighets scenarier för två olika virtuella datorer

Om du använder två virtuella Azure-datorer i en Azure-tillgänglighets uppsättning kan du öka drift tiden mellan de två virtuella datorerna om de placeras i en Azures tillgänglighets uppsättning i en Azure-region. Bas konfigurationen i Azure skulle se ut så här:

![Diagram över två virtuella datorer med alla skikt](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

För att illustrera olika tillgänglighets scenarier utelämnas några av lagren i diagrammet. Diagrammet visar bara skikt som illustrerar virtuella datorer, värdar, tillgänglighets uppsättningar och Azure-regioner. Azure Virtual Network-instanser, resurs grupper och prenumerationer spelar ingen roll i de scenarier som beskrivs i det här avsnittet.

### <a name="replicate-backups-to-a-second-virtual-machine"></a>Replikera säkerhets kopior till en andra virtuell dator

En av de mest elementära-installationerna är att använda säkerhets kopior. I synnerhet kan säkerhets kopior av transaktions loggar levereras från en virtuell dator till en annan virtuell Azure-dator. Du kan välja typ av Azure Storage. I den här konfigurationen ansvarar du för skript kopian av schemalagda säkerhets kopieringar som utförs på den första virtuella datorn till den andra virtuella datorn. Om du behöver använda de andra virtuella dator instanserna måste du återställa de fullständiga, stegvisa/differentiella och transaktions logg säkerhets kopiorna till den plats du behöver. 

Arkitekturen ser ut så här:

![Diagram som visar arkitekturen för två virtuella datorer med Storage Replication.](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Den här konfigurationen passar inte bra för att uppnå utmärkta återställnings punkt mål (återställnings punkt mål) och återställnings tids mål (RTO). RTO gånger skulle särskilt bli lidande på grund av behovet av att fullständigt återställa den fullständiga databasen med hjälp av de kopierade säkerhets kopiorna. Den här installationen är dock användbar för återställning från oavsiktligt data borttagning på huvud instanserna. Med den här installationen kan du när som helst återställa till en viss tidpunkt, extrahera data och importera borttagna data till huvud instansen. Därför kan det vara bra att använda en säkerhets kopierings metod i kombination med andra funktioner med hög tillgänglighet. 

När säkerhets kopieringar kopieras kan du kanske använda en mindre virtuell dator än den primära virtuella datorn som SAP HANA-instansen körs på. Tänk på att du kan ansluta ett mindre antal virtuella hård diskar till mindre virtuella datorer. Information om gränserna för enskilda VM-typer finns i [storlekar för virtuella Linux-datorer i Azure](../../sizes.md).

### <a name="sap-hana-system-replication-without-automatic-failover"></a>SAP HANA system replikering utan automatisk redundans

De scenarier som beskrivs i det här avsnittet använder SAP HANA systemreplikering. SAP-dokumentationen finns i [systemreplikering](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html). Scenarier utan automatisk redundans är inte vanliga för konfigurationer i en Azure-region. En konfiguration utan automatisk redundans, även om du undviker en pacemaker-installation, är det obligatoriskt att övervaka och redundansväxla manuellt. Eftersom detta sker och ansträngningarna är de flesta kunder förlitade på Azure Service lagning i stället. Det finns vissa fall där den här konfigurationen kan hjälpa till med fel scenarier. Eller, i vissa fall kan en kund vilja realisera mer effektivitet.

#### <a name="sap-hana-system-replication-without-auto-failover-and-without-data-preload"></a>SAP HANA systemreplikering utan automatisk redundans och utan data inläsning

I det här scenariot använder du SAP HANA system-replikering för att flytta data på ett synkront sätt för att uppnå 0. Å andra sidan har du en tillräckligt lång RTO som du inte behöver redundans eller data för inläsning i HANA instance cache. I det här fallet är det möjligt att uppnå ytterligare ekonomi i konfigurationen genom att vidta följande åtgärder:

- Kör en annan SAP HANA instans på den andra virtuella datorn. SAP HANA-instansen i den andra virtuella datorn tar det mesta av den virtuella datorns minne. Om det finns en redundansväxling till den andra virtuella datorn måste du stänga av den aktiva SAP HANA-instansen som innehåller data som har lästs in helt i den andra virtuella datorn, så att replikerade data kan läsas in i cacheminnet för den riktade HANA-instansen i den andra virtuella datorn.
- Använd en mindre VM-storlek på den andra virtuella datorn. Om en redundansväxling inträffar har du ytterligare ett steg före den manuella redundansväxlingen. I det här steget ändrar du storlek på den virtuella datorn till storleken på den virtuella käll datorn. 
 
Scenariot ser ut så här:

![Diagram över två virtuella datorer med Storage Replication](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> Även om du inte använder inläsning av data i HANA-systemreplikeringens mål, behöver du minst 64 GB minne. Du behöver också tillräckligt med minne utöver 64 GB för att behålla rowstore-data i minnet för mål instansen.

#### <a name="sap-hana-system-replication-without-auto-failover-and-with-data-preload"></a>SAP HANA systemreplikering utan automatisk redundans och med inläsning av data

I det här scenariot är data som replikeras till HANA-instansen i den andra virtuella datorn förinstallerade. Detta eliminerar de två fördelarna med att inte för in data. I det här fallet kan du inte köra en annan SAP HANA system på den andra virtuella datorn. Du kan inte heller använda en mindre VM-storlek. Därför implementerar kunderna sällan det här scenariot.

### <a name="sap-hana-system-replication-with-automatic-failover"></a>SAP HANA system replikering med automatisk redundans

I den standard-och den vanligaste tillgänglighets konfigurationen inom en Azure-region, har två virtuella Azure-datorer som kör SLES Linux ett redundanskluster definierat. SLES Linux-klustret är baserat på [pacemaker](./high-availability-guide-suse-pacemaker.md) -ramverket, tillsammans med en [STONITH](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) -enhet. 

Från ett SAP HANA perspektiv synkroniseras replikeringsläget som används och en automatisk redundans konfigureras. I den andra virtuella datorn fungerar SAP HANA-instansen som en nod för snabb växling. Noden vänte läge får en synkron ström av ändrings poster från den primära SAP HANA-instansen. När transaktionerna allokeras av programmet på den primära HANA-noden, väntar den primära HANA-noden att bekräfta incheckningen av programmet tills den sekundära SAP HANA-noden bekräftar att posten har mottagits. SAP HANA erbjuder två synkrona replikeringsinställningar. Mer information och en beskrivning av skillnaderna mellan dessa två lägen för synkron replikering finns i [replikerings lägen för SAP-artiklar för SAP HANA system replikering](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html).

Den övergripande konfigurationen ser ut så här:

![Diagram över två virtuella datorer med Storage-replikering och redundans](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Du kan välja den här lösningen eftersom den gör det möjligt att uppnå en återställnings punkt = 0 och en låg RTO. Konfigurera SAP HANA klient anslutning så att SAP HANA klienter använder den virtuella IP-adressen för att ansluta till konfigurationen för HANA-systemreplikering. En sådan konfiguration eliminerar behovet av att konfigurera om programmet om en redundansväxling till den sekundära noden sker. I det här scenariot måste Azure VM SKU: erna för de primära och sekundära virtuella datorerna vara desamma.

## <a name="next-steps"></a>Nästa steg

Steg för steg-anvisningar om hur du konfigurerar dessa konfigurationer i Azure finns i:

- [Konfigurera SAP HANA system replikering på virtuella Azure-datorer](sap-hana-high-availability.md)
- [Hög tillgänglighet för SAP HANA med hjälp av systemreplikering](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

Mer information om SAP HANA tillgänglighet i Azure-regioner finns i:

- [SAP HANA tillgänglighet i Azure-regioner](./sap-hana-availability-across-regions.md)