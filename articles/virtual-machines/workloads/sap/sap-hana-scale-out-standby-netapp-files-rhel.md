---
title: SAP HANA skala ut med vänte läge med Azure NetApp Files på RHEL | Microsoft Docs
description: Hög tillgänglighets guide för SAP NetWeaver på Red Hat Enterprise Linux med Azure NetApp Files för SAP-program
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: radeltch
ms.openlocfilehash: 4947585b1f20b8142c51d9d7e28c6d8504b6d6d5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101669645"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-red-hat-enterprise-linux"></a>Distribuera ett SAP HANA skalbart system med noden vänte läge på virtuella Azure-datorer med Azure NetApp Files på Red Hat Enterprise Linux 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]:https://launchpad.support.sap.com/#/notes/1900823
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2593824
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


Den här artikeln beskriver hur du distribuerar ett SAP HANA system med hög tillgänglighet i en skalbar konfiguration med vänte läge på Azure Red Hat Enterprise Linux Virtual Machines (VM) med hjälp av [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) för de delade lagrings volymerna.  

I exemplen konfigurationer, installations kommandon och så vidare är HANA-instansen **03** och Hana-systemets ID är **HN1**. Exemplen baseras på HANA 2,0 SP4 och Red Hat Enterprise Linux för SAP 7,6. 

> [!NOTE]
> Den här artikeln innehåller referenser till villkors *huvud* och *slav*, och villkor som Microsoft inte längre använder. När de här villkoren tas bort från program varan tar vi bort dem från den här artikeln.


Läs följande SAP-anteckningar och dokument innan du börjar:

* [Azure NetApp Files dokumentation][anf-azure-doc] 
* SAP-anteckning [1928533] innehåller:  
  * En lista med storlekar för virtuella Azure-datorer som stöds för distribution av SAP-program
  * Viktig kapacitets information för Azure VM-storlekar
  * Stöd för SAP-program och operativ system (OS) och databas kombinationer
  * Den nödvändiga SAP kernel-versionen för Windows och Linux på Microsoft Azure
* SAP anmärkning [2015553]: visar krav för SAP-program distributioner som stöds i Azure
* SAP-anteckning [2002167] har rekommenderade OS-inställningar för Red Hat Enterprise Linux
* SAP NOTE [2009879] har SAP HANA rikt linjer för Red Hat Enterprise Linux
* SAP anmärkning [2178632]: innehåller detaljerad information om alla övervaknings mått som rapporter ATS för SAP i Azure
* SAP NOTE [2191498]: innehåller den nödvändiga versionen av SAP host agent för Linux i Azure
* SAP anmärkning [2243692]: innehåller information om SAP-licensiering på Linux i Azure
* SAP anmärkning [1999351]: innehåller ytterligare felsöknings information för Azure Enhanced Monitoring-tillägget för SAP
* SAP anmärkning [1900823]: innehåller information om SAP HANA lagrings krav
* [SAP community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): innehåller alla nödvändiga SAP-anteckningar för Linux
* [Azure Virtual Machines planera och implementera SAP på Linux][planning-guide]
* [Azure Virtual Machines-distribution för SAP på Linux][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP på Linux][dbms-guide]
* Allmän dokumentation om RHEL
  * [Översikt över Add-On med hög tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Add-On administration med hög tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Add-On referens för hög tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Guide för Red Hat Enterprise Linux nätverk](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
* Azure-speciell RHEL-dokumentation:
  * [Installera SAP HANA på Red Hat Enterprise Linux för användning i Microsoft Azure](https://access.redhat.com/public-cloud/microsoft-azure)
* [NetApp SAP-program på Microsoft Azure med Azure NetApp Files][anf-sap-applications-azure]
* [NFS v4.1-volymer på Azure NetApp Files för SAP HANA](./hana-vm-operations-netapp.md)

## <a name="overview"></a>Översikt

En metod för att uppnå HANA hög tillgänglighet är genom att konfigurera automatisk redundansväxling av värden. Om du vill konfigurera automatisk redundans för värd lägger du till en eller flera virtuella datorer i HANA-systemet och konfigurerar dem som vänte läges noder. När en aktiv nod kraschar tar en nod i vänte läge automatiskt över. I den sammanställda konfigurationen med Azure Virtual Machines får du automatisk redundans genom att använda [NFS på Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md).  

> [!NOTE]
> Noden vänte läge behöver åtkomst till alla databas volymer. HANA-volymerna måste monteras som NFSv4-volymer. Den förbättrade fil låne metoden för fillån i NFSv4-protokollet används för `I/O` avgränsning. 

> [!IMPORTANT]
> Om du vill bygga konfigurationen som stöds måste du distribuera HANA-data och logg volymer som NFSv 4.1-volymer och montera dem med hjälp av NFSv 4.1-protokollet. Konfigurationen av den automatiska redundansväxlingen i HANA-värden med noden vänte läge stöds inte med NFSv3.

![Översikt över SAP NetWeaver-hög tillgänglighet](./media/sap-hana-high-availability-rhel/sap-hana-scale-out-standby-netapp-files-rhel.png)

I föregående diagram, som följer SAP HANA nätverks rekommendationer, representeras tre undernät i ett virtuellt Azure-nätverk: 
* För klient kommunikation
* För kommunikation med lagrings systemet
* För intern HANA kommunikation mellan noder

Azure NetApp-volymerna finns i separata undernät, [delegerade till Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).  

I den här exempel konfigurationen är under näten:  

  - `client` 10.9.1.0/26  
  - `storage` 10.9.3.0/26  
  - `hana` 10.9.2.0/26  
  - `anf` 10.9.0.0/26 (delegerat undernät till Azure NetApp Files)

## <a name="set-up-the-azure-netapp-files-infrastructure"></a>Konfigurera Azure NetApp Files-infrastrukturen 

Innan du fortsätter med installationen av Azure NetApp Files-infrastrukturen kan du bekanta dig med [Azure NetApp Files-dokumentationen][anf-azure-doc]. 

Azure NetApp Files finns i flera [Azure-regioner](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Kontrol lera om din valda Azure-region erbjuder Azure NetApp Files.  

För information om tillgängligheten för Azure NetApp Files av Azure-regionen, se [Azure NetApp Files tillgänglighet per Azure-region][anf-avail-matrix].  

Innan du distribuerar Azure NetApp Files kan du begära onboarding till Azure NetApp Files genom att gå till [Registrera dig för Azure NetApp Files instruktioner][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Distribuera Azure NetApp Files-resurser  

Följande instruktioner förutsätter att du redan har distribuerat ditt [virtuella Azure-nätverk](../../../virtual-network/virtual-networks-overview.md). Azure NetApp Files resurser och virtuella datorer, där Azure NetApp Files resurserna ska monteras, måste distribueras i samma virtuella Azure-nätverk eller i peer-anslutna virtuella Azure-nätverk.  

1. Om du inte redan har distribuerat resurserna begär du [att registrera dig för att Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md).  

2. Skapa ett NetApp-konto i din valda Azure-region genom att följa instruktionerna i [skapa ett NetApp-konto](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md).  

3. Konfigurera en pool för Azure NetApp Files kapacitet genom att följa anvisningarna i [Konfigurera en Azure NetApp Files kapacitets pool](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md).  

   Den HANA-arkitektur som presenteras i den här artikeln använder en pool med enskild Azure NetApp Files kapacitet på *Ultra service-* nivå. För HANA-arbetsbelastningar i Azure rekommenderar vi att du använder en Azure NetApp Files *Ultra* eller *Premium* [service nivå](../../../azure-netapp-files/azure-netapp-files-service-levels.md).  

4. Delegera ett undernät till Azure NetApp Files, enligt beskrivningen i instruktionerna i [delegera ett undernät till Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).  

5. Distribuera Azure NetApp Files-volymer genom att följa anvisningarna i [skapa en NFS-volym för Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).  

   När du distribuerar volymerna, måste du välja **nfsv 4.1** -versionen. Distribuera volymerna i det angivna Azure NetApp Files- [undernätet](/rest/api/virtualnetwork/subnets). IP-adresserna för Azure NetApp-volymerna tilldelas automatiskt. 
   
   Tänk på att Azure NetApp Files-resurser och de virtuella Azure-datorerna måste finnas i samma virtuella Azure-nätverk eller i peer-nätverk med peer-nätverk. Till exempel är **HN1**-data-Mnt00001, **HN1**-log-mnt00001 och så vidare, volym namnen och NFS://10.9.0.4/**HN1**-data-mnt00001, NFS://10.9.0.4/**HN1**-log-mnt00001 och så vidare, de är fil Sök vägar för Azure NetApp Files volymerna.  

   * volym **HN1**-data-mnt00001 (NFS://10.9.0.4/**HN1**-data-mnt00001)
   * volym **HN1**-data-mnt00002 (NFS://10.9.0.4/**HN1**-data-mnt00002)
   * volym **HN1**-log-mnt00001 (NFS://10.9.0.4/**HN1**-log-mnt00001)
   * volym **HN1**-log-mnt00002 (NFS://10.9.0.4/**HN1**-log-mnt00002)
   * volym **HN1**– delad (NFS://10.9.0.4/**HN1**– delad)
   
   I det här exemplet använde vi en separat Azure NetApp Files volym för varje HANA-data och logg volym. För en mer kostnads optimerad konfiguration på mindre eller icke-produktiva system är det möjligt att placera alla data monteringar på en enda volym och alla loggar monteras på en annan volym.  

### <a name="important-considerations"></a>Att tänka på

Tänk på följande viktiga överväganden när du skapar Azure NetApp Files för SAP HANA skala ut med scenarion för stå med noder:

- Den minsta kapacitets poolen är 4 tebibyte (TiB).  
- Den minsta volym storleken är 100 gibibyte (GiB).
- Azure NetApp Files och alla virtuella datorer där Azure NetApp Files volymer ska monteras måste finnas i samma virtuella Azure-nätverk eller i peer-anslutna [virtuella nätverk](../../../virtual-network/virtual-network-peering-overview.md) i samma region.  
- Det valda virtuella nätverket måste ha ett undernät som är delegerat till Azure NetApp Files.
- Data flödet för en Azure NetApp Files volym är en funktion av volym kvoten och tjänst nivån, enligt beskrivningen i [service nivå för Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). När du ändrar storlek på HANA Azure NetApp-volymer ser du till att det resulterande data flödet uppfyller HANA-system kraven.  
- Med Azure NetApp Files [export policy](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)kan du kontrol lera tillåtna klienter, åtkomst typ (Läs-och Skriv behörighet, skrivskyddad och så vidare). 
- Azure NetApp Files funktionen är inte Zone-medveten än. Funktionen distribueras för närvarande inte i alla tillgänglighets zoner i en Azure-region. Var medveten om potentiella fördröjnings konsekvenser i vissa Azure-regioner.  

> [!IMPORTANT]
> För SAP HANA arbets belastningar är låg latens kritiskt. Arbeta med din Microsoft-representant för att säkerställa att de virtuella datorerna och Azure NetApp Files volymerna distribueras i nära närhet.  

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Storlek för HANA-databas på Azure NetApp Files

Data flödet för en Azure NetApp Files volym är en funktion av volymens storlek och tjänst nivå, enligt beskrivningen i [service nivå för Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). 

När du utformar infrastrukturen för SAP i Azure bör du vara medveten om några lägsta lagrings krav för SAP, som översätter minsta data flödes egenskaper:

- Läs-och skriv åtgärder på/Hana/log med 250 megabyte per sekund (MB/s) med 1 MB I/O-storlekar.  
- Läs aktivitet minst 400 MB/s för/Hana/data för I/O-storlekarna på 16 MB och 64 MB.  
- Skriv aktivitet minst 250 MB/s för/Hana/data med I/O-storlekar på 16 MB och 64 MB. 

[Azure NetApp Files data flödes gränser](../../../azure-netapp-files/azure-netapp-files-service-levels.md) per 1 TIB volym kvot:
- Premium Storage nivå – 64 MiB/s  
- Ultra Storage-nivå – 128 MiB/s  

För att uppfylla de lägsta data flödes kraven för SAP för data och logg, och rikt linjerna för/Hana/Shared, är de rekommenderade storlekarna:

| Volym | Storlek på<br>Premium Storage nivå | Storlek på<br>Ultra Storage-nivå | NFS-protokoll som stöds |
| --- | --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v 4.1 |
| /hana/data | 6,3 TiB | 3,2 TiB | v 4.1 |
| /hana/shared | 1xRAM per 4 arbetsnoder | 1xRAM per 4 arbetsnoder | v3 eller v 4.1 |

Den SAP HANA konfigurationen för den layout som presenteras i den här artikeln, med Azure NetApp Files Ultra Storage-nivå, är:

| Volym | Storlek på<br>Ultra Storage-nivå | NFS-protokoll som stöds |
| --- | --- | --- |
| /hana/log/mnt00001 | 2 TiB | v 4.1 |
| /hana/log/mnt00002 | 2 TiB | v 4.1 |
| /hana/data/mnt00001 | 3,2 TiB | v 4.1 |
| /hana/data/mnt00002 | 3,2 TiB | v 4.1 |
| /hana/shared | 2 TiB | v3 eller v 4.1 |

> [!NOTE]
> De Azure NetApp Files storleks rekommendationer som anges här är avsedda att uppfylla de minimi krav som SAP rekommenderar för sina infrastruktur leverantörer. I verkliga kund distributioner och arbets belastnings scenarier är det inte säkert att dessa storlekar är tillräckliga. Använd de här rekommendationerna som en start punkt och anpassa baserat på kraven för din särskilda arbets belastning.  

> [!TIP]
> Du kan ändra storlek på Azure NetApp Files volymer dynamiskt, utan att behöva *demontera* volymerna, stoppa de virtuella datorerna eller stoppa SAP HANA. Den här metoden gör det möjligt att uppfylla både förväntade och oförutsedda data flödes krav för ditt program.

## <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Distribuera virtuella Linux-datorer via Azure Portal

Först måste du skapa Azure NetApp Files volymerna. Utför sedan följande steg:
1. Skapa [Azure Virtual Network-undernät](../../../virtual-network/virtual-network-manage-subnet.md) i ditt [virtuella Azure-nätverk](../../../virtual-network/virtual-networks-overview.md). 
1. Distribuera de virtuella datorerna. 
1. Skapa ytterligare nätverks gränssnitt och koppla nätverks gränssnitten till motsvarande virtuella datorer.  

   Varje virtuell dator har tre nätverks gränssnitt som motsvarar de tre virtuella Azure-undernäten ( `client` , `storage` och `hana` ). 

   Mer information finns i [skapa en virtuell Linux-dator i Azure med flera nätverks gränssnitts kort](../../linux/multiple-nics.md).  

> [!IMPORTANT]
> För SAP HANA arbets belastningar är låg latens kritiskt. För att uppnå låg latens kan du arbeta med din Microsoft-representant för att se till att de virtuella datorerna och Azure NetApp Files volymerna distribueras i nära närhet. När du registrerar [nya SAP HANA system](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) som använder SAP HANA Azure NetApp Files, så skicka in nödvändig information. 
 
Nästa instruktioner förutsätter att du redan har skapat resurs gruppen, det virtuella Azure-nätverket och de tre virtuella Azure-undernäten: `client` , `storage` och `hana` . När du distribuerar de virtuella datorerna väljer du klient under nätet så att klient nätverks gränssnittet är det primära gränssnittet på de virtuella datorerna. Du måste också konfigurera en explicit väg till det Azure NetApp Files delegerade under nätet via gatewayen för lagrings under nätet. 

> [!IMPORTANT]
> Kontrol lera att det OS du väljer är SAP-certifierat för SAP HANA på de angivna VM-typerna som du använder. En lista över SAP HANA certifierade VM-typer och OS-versioner för dessa typer finns på webbplatsen [SAP HANA Certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) . Klicka i informationen om den virtuella dator typen som visas för att hämta den fullständiga listan med SAP HANA operativ system versioner som stöds för den typen.  

1. Skapa en tillgänglighets uppsättning för SAP HANA. Se till att ange den maximala uppdaterings domänen.  

2. Skapa tre virtuella datorer (**hanadb1**, **hanadb2**, **hanadb3**) genom att utföra följande steg:  

   a. Använd en Red Hat Enterprise Linux-avbildning i Azure-galleriet som stöds för SAP HANA. Vi har använt en RHEL-SAP-HA 7,6-avbildning i det här exemplet.  

   b. Välj den tillgänglighets uppsättning som du skapade tidigare för SAP HANA.  

   c. Välj klient under nätet Azure Virtual Network. Välj [accelererat nätverk](../../../virtual-network/create-vm-accelerated-networking-cli.md).  

   När du distribuerar de virtuella datorerna genereras nätverks gränssnitts namnet automatiskt. I dessa anvisningar för enkelhetens skull kommer vi att referera till de automatiskt genererade nätverks gränssnitten som är kopplade till klient under nätet för Azures virtuella nätverk, som **hanadb1-client**, **hanadb2-client** och **hanadb3-client**. 

3. Skapa tre nätverks gränssnitt, ett för varje virtuell dator för det `storage` virtuella nätverkets undernät (i det här exemplet **hanadb1-Storage**, **hanadb2-Storage** och **hanadb3-Storage**).  

4. Skapa tre nätverks gränssnitt, ett för varje virtuell dator för det `hana`  virtuella nätverkets undernät (i det här exemplet **hanadb1-Hana**, **hanadb2-Hana** och **hanadb3-Hana**).  

5. Koppla de nyligen skapade virtuella nätverks gränssnitten till motsvarande virtuella datorer genom att utföra följande steg:  

    a. Gå till den virtuella datorn i [Azure Portal](https://portal.azure.com/#home).  

    b. I den vänstra rutan väljer du **Virtual Machines**. Filtrera på det virtuella dator namnet (till exempel **hanadb1**) och välj sedan den virtuella datorn.  

    c. I fönstret **Översikt** väljer du **stoppa** för att frigöra den virtuella datorn.  

    d. Välj **nätverk** och Anslut sedan nätverks gränssnittet. I list rutan **bifoga nätverks gränssnitt** väljer du redan skapade nätverks gränssnitt för- `storage` och- `hana` undernät.  
    
    e. Välj **Spara**. 
 
    f. Upprepa steg b till e för de återstående virtuella datorerna (i vårt exempel  **hanadb2** och **hanadb3**).
 
    ex. Lämna kvar de virtuella datorerna i stoppat tillstånd för tillfället. Därefter aktiverar vi [accelererat nätverk](../../../virtual-network/create-vm-accelerated-networking-cli.md) för alla nyligen anslutna nätverks gränssnitt.  

6. Aktivera accelererat nätverk för de ytterligare nätverks gränssnitten för- `storage` och- `hana` undernätet genom att utföra följande steg:  

    a. Öppna [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) i [Azure Portal](https://portal.azure.com/#home).  

    b. Kör följande kommandon för att aktivera accelererat nätverk för ytterligare nätverks gränssnitt, som är kopplade till- `storage` och- `hana` undernät.  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-storage</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true

    </code></pre>

7. Starta de virtuella datorerna genom att utföra följande steg:  

    a. I den vänstra rutan väljer du **Virtual Machines**. Filtrera på det virtuella dator namnet (till exempel **hanadb1**) och markera det.  

    b. I fönstret **Översikt** väljer du **Starta**.  

## <a name="operating-system-configuration-and-preparation"></a>Konfiguration och förberedelser för operativ system

Anvisningarna i nästa avsnitt har prefixet något av följande:
* **[A]**: gäller för alla noder
* **[1]**: gäller endast nod 1
* **[2]**: gäller endast nod 2
* **[3]**: gäller endast nod 3

Konfigurera och Förbered ditt operativ system genom att utföra följande steg:

1. **[A]** underhålla värd filen på de virtuella datorerna. Inkludera poster för alla undernät. Följande poster har lagts till i `/etc/hosts` i det här exemplet.  

    <pre><code>
    # Storage
    10.9.3.4   hanadb1-storage
    10.9.3.5   hanadb2-storage
    10.9.3.6   hanadb3-storage
    # Client
    10.9.1.5   hanadb1
    10.9.1.6   hanadb2
    10.9.1.7   hanadb3
    # Hana
    10.9.2.4   hanadb1-hana
    10.9.2.5   hanadb2-hana
    10.9.2.6   hanadb3-hana
    </code></pre>

3. **[A]** Lägg till en nätverks väg så att kommunikationen till Azure NetApp Files skickas via nätverks gränssnittet för lagring.  

   I det här exemplet används `Networkmanager` för att konfigurera den ytterligare nätverks vägen. Följande instruktioner förutsätter att lagrings nätverks gränssnittet är `eth1` .  
   Börja med att fastställa anslutnings namnet för enheten `eth1` . I det här exemplet är anslutnings namnet för `eth1` enheten `Wired connection 1` .  

    <pre><code>
    # Execute as root
    nmcli connection
    # Result
    #NAME                UUID                                  TYPE      DEVICE
    #System eth0         5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  ethernet  eth0
    #Wired connection 1  4b0789d1-6146-32eb-83a1-94d61f8d60a7  ethernet  eth1
    </code></pre>

   Konfigurera sedan ytterligare väg till Azure NetApp Files delegerade nätverket via `eth1` .  

    <pre><code>
    # Add the following route 
    # ANFDelegatedSubnet/cidr via StorageSubnetGW dev StorageNetworkInterfaceDevice
    nmcli connection modify <b>"Wired connection 1"</b> +ipv4.routes <b>"10.9.0.0/26 10.9.3.1"</b>
    </code></pre>

    Starta om den virtuella datorn för att aktivera ändringarna.  

3. **[A]** installera NFS-klient paketet.  

    <pre><code>
    yum install nfs-utils
    </code></pre>

3. **[A]** Förbered operativ systemet för att köra SAP HANA på Azure NETAPP med NFS, enligt beskrivningen i [NetApp SAP-program på Microsoft Azure med Azure NetApp Files][anf-sap-applications-azure]. Skapa konfigurations filen */etc/sysctl.d/NetApp-Hana.conf* för konfigurations inställningarna för NetApp.  

    <pre><code>
    vi /etc/sysctl.d/netapp-hana.conf
    # Add the following entries in the configuration file
    net.core.rmem_max = 16777216
    net.core.wmem_max = 16777216
    net.core.rmem_default = 16777216
    net.core.wmem_default = 16777216
    net.core.optmem_max = 16777216
    net.ipv4.tcp_rmem = 65536 16777216 16777216
    net.ipv4.tcp_wmem = 65536 16777216 16777216
    net.core.netdev_max_backlog = 300000 
    net.ipv4.tcp_slow_start_after_idle=0 
    net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** skapa konfigurations filen */etc/sysctl.d/MS-AZ.conf* med ytterligare optimerings inställningar.  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    net.ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

> [!TIP]
> Undvik att ange net.ipv4.ip_local_port_range och net.ipv4.ip_local_reserved_ports uttryckligen i sysctl-konfigurationsfilerna så att SAP-värdservern kan hantera port intervallen. Mer information finns i SAP NOTE [2382421](https://launchpad.support.sap.com/#/notes/2382421).  

5. **[A]** justera inställningarna för sunrpc, som rekommenderas i [NetApp SAP-program på Microsoft Azure med Azure NetApp Files][anf-sap-applications-azure].  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

6. **[A]** Red Hat för Hana-konfiguration.

    Konfigurera RHEL enligt beskrivningen i SAP NOTE [2292690], [2455582], [2593824] och <https://access.redhat.com/solutions/2447641> .

    > [!NOTE]
    > Om du installerar HANA 2,0 SP04 måste du installera paketet `compat-sap-c++-7` enligt beskrivningen i SAP note [2593824]innan du kan installera SAP HANA. 

## <a name="mount-the-azure-netapp-files-volumes"></a>Montera Azure NetApp Files volymer

1. **[A]** skapa monterings punkter för Hana-databasens volymer.  

    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** skapa Node-/regionsspecifika kataloger för/usr/SAP på **HN1**-delad.  

    <pre><code>
    # Create a temporary directory to mount <b>HN1</b>-shared
    mkdir /mnt/tmp
    # if using NFSv3 for this volume, mount with the following command
    mount <b>10.9.0.4</b>:/<b>HN1</b>-shared /mnt/tmp
    # if using NFSv4.1 for this volume, mount with the following command
    mount -t nfs -o sec=sys,vers=4.1 <b>10.9.0.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** verifiera inställningen för NFS-domänen. Kontrol lera att domänen är konfigurerad som standard Azure NetApp Files domän, dvs. **`defaultv4iddomain.com`** mappningen är inställd på **ingen**.  

    > [!IMPORTANT]
    > Se till att ange att NFS-domänen på `/etc/idmapd.conf` den virtuella datorn ska matcha standard domän konfigurationen på Azure NetApp Files: **`defaultv4iddomain.com`** . Om det finns ett matchnings fel mellan domän konfigurationen på NFS-klienten (d.v.s. den virtuella datorn) och NFS-servern, t. ex. Azure NetApp-konfigurationen, så visas behörigheterna för filer på Azure NetApp-volymer som är monterade på de virtuella datorerna som `nobody` .  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** verifiera `nfs4_disable_idmapping` . Den måste anges till **Y**. Kör monterings kommandot för att skapa en katalog struktur där `nfs4_disable_idmapping` finns. Du kan inte skapa katalogen manuellt under/sys/modules eftersom åtkomst är reserverad för kernel/driv rutiner.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   Mer information om hur du ändrar `nfs4_disable_idmapping` parametern finns i https://access.redhat.com/solutions/1749883 .

6. **[A]** montera de delade Azure NetApp Files volymerna.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** montera de Node-/regionsspecifika volymerna på **hanadb1**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** montera de Node-/regionsspecifika volymerna på **hanadb2**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** montera de Node-/regionsspecifika volymerna på **hanadb3**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** kontrol lera att alla Hana-volymer monteras med NFS- **NFSv4**.  

    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.9.0.4:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/log/<b>HN1</b>/mnt00002 from 10.9.0.4:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/data/<b>HN1</b>/mnt00002 from 10.9.0.4:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/log/<b>HN1</b>/mnt00001 from 10.9.0.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /usr/sap/<b>HN1</b> from 10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/shared from 10.9.0.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    </code></pre>

## <a name="installation"></a>Installation  

I det här exemplet för att distribuera SAP HANA i en skalbar konfiguration med noden vänte läge med Azure har vi använt HANA 2,0 SP4.  

### <a name="prepare-for-hana-installation"></a>Förbered för HANA-installation

1. **[A]** före installationen av Hana anger du rot lösen ordet. Du kan inaktivera rot lösen ordet när installationen har slutförts. Kör som- `root` kommando `passwd` .  

2. **[1]** kontrol lera att du kan logga in via SSH till **hanadb2** och **hanadb3**, utan att uppmanas att ange ett lösen ord.  

    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** installera ytterligare paket, vilket krävs för Hana 2,0 SP4. Mer information finns i SAP NOTE [2593824](https://launchpad.support.sap.com/#/notes/2593824). 

    <pre><code>
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1 
    </code></pre>

4. **[2], [3]** Ändra ägarskap för SAP HANA `data` och `log` kataloger till **HN1** adm.   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

5. **[A]** inaktivera brand väggen tillfälligt, så att den inte störs av Hana-installationen. Du kan aktivera den igen när HANA-installationen är färdig. 
   <pre><code>
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
   </code></pre>

### <a name="hana-installation"></a>HANA-installation

1. **[1]** installera SAP HANA genom att följa anvisningarna i [installations-och uppdaterings hand boken för SAP HANA 2,0](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). I det här exemplet installerar vi SAP HANA skala ut med Master, One Worker och en nod i vänte läge.  

   a. Starta **hdblcm** -programmet från installations program katalogen Hana. Använd `internal_network` parametern och skicka adress utrymmet för under nätet som används för den interna Hana-kommunikationen mellan noderna.  

    <pre><code>
    ./hdblcm --internal_network=10.9.2.0/26
    </code></pre>

   b. Ange följande värden i prompten:

     * För **Välj en åtgärd**: ange **1** (för installation)
     * För **Ytterligare komponenter för installation**: ange **2, 3**
     * För installations Sök väg: Tryck på RETUR (Standardvärdet är/Hana/Shared)
     * För **Lokalt värdnamn**: Tryck på RETUR för att acceptera standard
     * Under **vill du lägga till värdar i systemet?**: ange **y**
     * För **kommaavgränsade värdnamn som ska läggas till**: ange **hanadb2, hanadb3**
     * För **rot användar namn** [root]: Tryck på RETUR för att acceptera standardvärdet
     * För roller för värd hanadb2: ange **1**  (för arbetare)
     * För **värd växlings gruppen** för värd hanadb2 [standard]: Tryck på RETUR för att acceptera standardvärdet
     * För **lagrings partitions nummer** för värd-hanadb2 [<<assign automatically>>]: Tryck på RETUR för att acceptera standardvärdet
     * För **arbets grupp** för värd hanadb2 [standard]: Tryck på RETUR för att acceptera standardvärdet
     * För **Välj roller** för värd hanadb3: ange **2** (för vänte läge)
     * För **värd växlings gruppen** för värd hanadb3 [standard]: Tryck på RETUR för att acceptera standardvärdet
     * För **arbets grupp** för värd hanadb3 [standard]: Tryck på RETUR för att acceptera standardvärdet
     * För **SAP HANA system-ID**: ange **HN1**
     * För **instans nummer** [00]: ange **03**
     * För **lokal värd Worker-grupp** [standard]: Tryck på RETUR för att acceptera standard
     * För **Välj system användning/ange index [4]**: ange **4** (för anpassad)
     * För **plats för data volymer** [/Hana/data/HN1]: Tryck på RETUR för att acceptera standardvärdet
     * För **plats för logg volymer** [/Hana/log/HN1]: Tryck på RETUR för att acceptera standardvärdet
     * **Begränsa maximal minnesallokering?** [n]: ange **n**
     * För **certifikat värd namnet för värd hanadb1** [hanadb1]: Tryck på RETUR för att acceptera standardvärdet
     * För **certifikat värd namnet för värd hanadb2** [hanadb2]: Tryck på RETUR för att acceptera standardvärdet
     * För **certifikat värd namnet för värd hanadb3** [hanadb3]: Tryck på RETUR för att acceptera standardvärdet
     * För **system administratörs lösen ord (hn1adm)**: Ange lösen ordet
     * För **lösen ord för system databas användare (system)**: Ange systemets lösen ord
     * För **Bekräfta System-Password (system Database User)**: Ange systemets lösen ord
     * För **omstart av systemet efter omstart av datorn?** [n]: ange **n** 
     * För vill **du fortsätta (j/n)**: validera sammanfattningen och om allt ser bra ut anger du **y**


2. **[1]** kontrol lera global.ini  

   Visa global.ini och se till att konfigurationen för den interna SAP HANA kommunikationen mellan noderna är på plats. Verifiera **kommunikations** avsnittet. Det ska ha adress utrymmet för `hana` under nätet och `listeninterface` ska vara inställt på `.internal` . Verifiera **internal_hostname_resolution** avsnittet. Den bör ha IP-adresserna för de HANA-virtuella datorer som tillhör `hana` under nätet.  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.9.2.0/26</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.9.2.4</b> = <b>hanadb1</b>
    <b>10.9.2.5</b> = <b>hanadb2</b>
    <b>10.9.2.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** Lägg till värd mappning för att säkerställa att klientens IP-adresser används för klient kommunikation. Lägg till avsnittet `public_host_resolution` och Lägg till motsvarande IP-adresser från klient under nätet.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.9.1.5</b>
    map_<b>hanadb2</b> = <b>10.9.1.6</b>
    map_<b>hanadb3</b> = <b>10.9.1.7</b>
   </code></pre>

4. **[1]** starta om SAP HANA för att aktivera ändringarna.  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** kontrol lera att klient gränssnittet kommer att använda IP-adresserna från `client` under nätet för kommunikation.  

   <pre><code>
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.9.1.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.9.1.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.9.1.5</b>"
   </code></pre>

   Information om hur du verifierar konfigurationen finns i SAP anmärkning [2183363 – konfiguration av SAP HANA internt nätverk](https://launchpad.support.sap.com/#/notes/2183363).  

5. **[A]** aktivera brand väggen igen.  
   - Stoppa HANA
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
       </code></pre>
   - Återaktivera brand väggen
       <pre><code>
        # Execute as root
        systemctl start firewalld
        systemctl enable firewalld
       </code></pre>

   - Öppna de brand Väggs portar som behövs

       > [!IMPORTANT]
       > Skapa brand Väggs regler för att tillåta att kommunikation mellan noder och klient trafik i HANA är över. De portar som krävs visas på [TCP/IP-portar för alla SAP-produkter](https://help.sap.com/viewer/ports). Följande kommandon är bara ett exempel. I det här scenariot med använt system Number 03.

       <pre><code>
        # Execute as root
        sudo firewall-cmd --zone=public --add-port=30301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30301/tcp
        sudo firewall-cmd --zone=public --add-port=30303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30303/tcp
        sudo firewall-cmd --zone=public --add-port=30306/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30306/tcp
        sudo firewall-cmd --zone=public --add-port=30307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30307/tcp
        sudo firewall-cmd --zone=public --add-port=30313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30313/tcp
        sudo firewall-cmd --zone=public --add-port=30315/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30315/tcp
        sudo firewall-cmd --zone=public --add-port=30317/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30317/tcp
        sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30340/tcp
        sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30341/tcp
        sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30342/tcp
        sudo firewall-cmd --zone=public --add-port=1128/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1128/tcp
        sudo firewall-cmd --zone=public --add-port=1129/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1129/tcp
        sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40302/tcp
        sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40301/tcp
        sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40307/tcp
        sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40303/tcp
        sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40340/tcp
        sudo firewall-cmd --zone=public --add-port=50313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50313/tcp
        sudo firewall-cmd --zone=public --add-port=50314/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50314/tcp
        sudo firewall-cmd --zone=public --add-port=30310/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30310/tcp
        sudo firewall-cmd --zone=public --add-port=30302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30302/tcp
       </code></pre>

   - Starta HANA
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
       </code></pre>

6. Ange följande SAP HANA parametrar för att optimera SAP HANA för underliggande Azure NetApp Files-lagring:

   - `max_parallel_io_requests`**128**
   - `async_read_submit`**på**
   - `async_write_submit_active`**på**
   - `async_write_submit_blocks`**alla**

   Mer information finns i [NETAPP SAP-program på Microsoft Azure med Azure NetApp Files][anf-sap-applications-azure]. 

   Från och med SAP HANA 2,0-system kan du ange parametrarna i `global.ini` . Mer information finns i SAP NOTE [1999930](https://launchpad.support.sap.com/#/notes/1999930).  
   
   För SAP HANA 1,0 Systems-versioner SPS12 och tidigare kan parametrarna anges under installationen, enligt beskrivningen i SAP NOTE [2267798](https://launchpad.support.sap.com/#/notes/2267798).  

7. Lagrings utrymmet som används av Azure NetApp Files har en fil storleks begränsning på 16 terabyte (TB). SAP HANA är inte implicit medveten om lagrings begränsningen och skapar inte automatiskt en ny datafil när fil storleks gränsen på 16 TB nås. I takt med att SAP HANA försöker växa till filen bortom 16 TB kommer det här försöket att resultera i fel och till sist i en index server krasch. 

   > [!IMPORTANT]
   > För att förhindra SAP HANA att öka datafilerna utöver [16 TB-gränsen](../../../azure-netapp-files/azure-netapp-files-resource-limits.md) för underlag rings systemet anger du följande parametrar i `global.ini` .  
   > - datavolume_striping = sant
   > - datavolume_striping_size_gb = 15000 mer information finns i SAP NOTE [2400005](https://launchpad.support.sap.com/#/notes/2400005).
   > Tänk på SAP NOTE [2631285](https://launchpad.support.sap.com/#/notes/2631285). 

## <a name="test-sap-hana-failover"></a>Testa SAP HANA redundans 

1. Simulera en nods krasch på en SAP HANA arbetsnoden. Gör följande: 

   a. Innan du simulerar en nods krasch kör du följande kommandon som **HN1** ADM för att avbilda miljöns status:  

   <pre><code>
    # Check the landscape status
    python /usr/sap/HN1/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |

    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
   </code></pre>

   b. För att simulera en nods krasch kör du följande kommando som rot på Worker-noden, som är **hanadb2** i det här fallet:  
   
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   c. Övervaka systemet för slut för ande av redundansväxling. När redundansväxlingen har slutförts fångar du statusen, som bör se ut så här:  

    <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > När en nod upplever kernel-panik, Undvik fördröjningar med SAP HANA redundans genom att ställa in `kernel.panic` på 20 sekunder på *alla* Hana-virtuella datorer. Konfigurationen görs i `/etc/sysctl` . Starta om de virtuella datorerna för att aktivera ändringen. Om den här ändringen inte utförs kan redundansväxlingen ta 10 minuter när en nod är i kernel-panik.  

2. Avsluta namnserver genom att göra följande:

   a. Innan testet kan du kontrol lera miljöns status genom att köra följande kommandon som **HN1** ADM:  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
   </code></pre>

   b. Kör följande kommandon som **HN1** adm på den aktiva huvudnoden, som är **hanadb1** i det här fallet:  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    **Hanadb3** för vänte läge tar över som huvud nod. Här är resursens tillstånd när redundansväxlingen är slutförd:  

    <pre><code>
     # Check the instance status
     sapcontrol -nr 03  -function GetSystemInstanceList
     GetSystemInstanceList
     OK
     hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
     hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
     hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
     hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
     # Check the landscape status
     python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
     | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
     |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
     |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
     | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
     | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
     | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
     | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
    </code></pre>

   c. Starta om HANA-instansen på **hanadb1** (det vill säga på samma virtuella dator, där namnserver stoppades). **Hanadb1** -noden kommer att återansluta till miljön och behåller sin vänte läges roll.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   När SAP HANA har startat på **hanadb1** förväntar du följande status:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   d. Stoppa igen namnserver på den aktuella aktiva huvudnoden (det vill säga på noden **hanadb3**).  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   Node **hanadb1** kommer att återuppta rollen för huvudnoden. När redundanstestning har slutförts ser statusen ut så här:

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

   e. Starta SAP HANA på **hanadb3**, som är redo att fungera som en nod för vänte läge.  

   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   När SAP HANA har startat på **hanadb3** ser statusen ut så här:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

## <a name="next-steps"></a>Nästa steg

* [Azure Virtual Machines planera och implementera SAP][planning-guide]
* [Azure Virtual Machines distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
* [NFS v4.1-volymer på Azure NetApp Files för SAP HANA](./hana-vm-operations-netapp.md)
* Information om hur du upprättar hög tillgänglighet och planerar för haveri beredskap för SAP HANA på virtuella Azure-datorer finns i [hög tillgänglighet för SAP HANA på Azure-Virtual Machines (VM)][sap-hana-ha].