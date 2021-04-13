---
title: SAP HANA skala ut med HSR och pacemaker på SLES | Microsoft Docs
description: SAP HANA skala ut med HSR och pacemaker på SLES.
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
ms.date: 04/12/2021
ms.author: radeltch
ms.openlocfilehash: 637616c3698cc9ec0cd13a4584bad24b6ed02c34
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315622"
---
# <a name="high-availability-for-sap-hana-scale-out-system-with-hsr-on-suse-linux-enterprise-server"></a>Hög tillgänglighet för SAP HANA skalbart system med HSR på SUSE Linux Enterprise Server 

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

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


Den här artikeln beskriver hur du distribuerar ett SAP HANA system med hög tillgänglighet i en skalbar konfiguration med HANA-systemreplikering (HSR) och pacemaker på Azure SUSE Linux Enterprise Server Virtual Machines (VM). De delade fil systemen i den angivna arkitekturen tillhandahålls av [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) och monteras över NFS.  

I exemplen konfigurationer, installations kommandon och så vidare är HANA-instansen **03** och Hana-systemets ID är **HN1**. Exemplen baseras på HANA 2,0 SP4 och SUSE Linux Enterprise Server 12 SP5. 

Läs följande SAP-anteckningar och dokument innan du börjar:

* [Azure NetApp Files dokumentation][anf-azure-doc] 
* SAP-anteckning [1928533] innehåller:  
  * En lista med storlekar för virtuella Azure-datorer som stöds för distribution av SAP-program
  * Viktig kapacitets information för Azure VM-storlekar
  * Stöd för SAP-program och operativ system (OS) och databas kombinationer
  * Den nödvändiga SAP kernel-versionen för Windows och Linux på Microsoft Azure
* SAP anmärkning [2015553]: visar krav för SAP-program distributioner som stöds i Azure
* SAP anmärkning [2205917]: innehåller rekommenderade OS-inställningar för SUSE Linux Enterprise Server för SAP-program
* SAP anmärkning [1944799]: innehåller SAP-rikt linjer för SUSE Linux Enterprise Server för SAP-program
* SAP anmärkning [2178632]: innehåller detaljerad information om alla övervaknings mått som rapporter ATS för SAP i Azure
* SAP NOTE [2191498]: innehåller den nödvändiga versionen av SAP host agent för Linux i Azure
* SAP anmärkning [2243692]: innehåller information om SAP-licensiering på Linux i Azure
* SAP anmärkning [1984787]: innehåller allmän information om SUSE Linux Enterprise Server 12
* SAP anmärkning [1999351]: innehåller ytterligare felsöknings information för Azure Enhanced Monitoring-tillägget för SAP
* SAP anmärkning [1900823]: innehåller information om SAP HANA lagrings krav
* [SAP community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): innehåller alla nödvändiga SAP-anteckningar för Linux
* [Azure Virtual Machines planera och implementera SAP på Linux][planning-guide]
* [Azure Virtual Machines-distribution för SAP på Linux][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP på Linux][dbms-guide]
* Översikt över [SUSE SAP-Hälsorikt linjer][suse-ha-guide]: innehåller all nödvändig information för att konfigurera NetWeaver hög tillgänglighet och SAP HANA systemreplikering lokalt (som ska användas som en allmän bas linje. de innehåller mer detaljerad information)
* [Viktig information om SUSEt hög tillgänglighets tillägg 12 SP5](https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP5/)
* [Hanterar felaktig NFS-resurs i SUSEt HA-klustret för HANA-systemreplikering](https://www.suse.com/support/kb/doc/?id=000019904)
* [NetApp SAP-program på Microsoft Azure med Azure NetApp Files][anf-sap-applications-azure]
* [NFS v4.1-volymer på Azure NetApp Files för SAP HANA](./hana-vm-operations-netapp.md)

## <a name="overview"></a>Översikt

En metod för att uppnå HANA-hög tillgänglighet för HANA-programinstallationer är att konfigurera HANA-systemreplikering och skydda lösningen med pacemaker-kluster för att tillåta automatisk redundans. När en aktiv nod Miss lyckas växlar klustret över HANA-resurserna till den andra platsen.  
Den uppvisade konfigurationen visar tre HANA-noder på varje plats, plus majoritetsnoduppsättning-noden för att förhindra det delade-hjärna-scenariot. Anvisningarna kan anpassas för att inkludera fler virtuella datorer som HANA DB-noder.  

Det delade fil systemet HANA `/hana/shared` i den presenterade arkitekturen tillhandahålls av [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md). Den monteras via NFSv 4.1 på varje HANA-nod på samma HANA-plats för system replikering. Fil system `/hana/data` och `/hana/log` är lokala fil system och delas inte mellan Hana DB-noderna. SAP HANA kommer att installeras i icke-delat läge. 

> [!TIP]
> Rekommenderade SAP HANA Storage-konfigurationer finns i [SAP HANA Azure VM Storage-konfigurationer](./hana-vm-operations-storage.md).   

[![SAP HANA skala ut med HSR och pacemaker-kluster på SLES](./media/sap-hana-high-availability/sap-hana-high-availability-scale-out-hsr-suse.png)](./media/sap-hana-high-availability/sap-hana-high-availability-scale-out-hsr-suse-detail.png#lightbox)

I föregående diagram representeras tre undernät i ett virtuellt Azure-nätverk enligt rekommendationerna för SAP HANA nätverk: 
* för klient kommunikation – `client` 10.23.0.0/24  
* för intern HANA kommunikation mellan noder – `inter` 10.23.1.128/26  
* för HANA-systemreplikering – `hsr` 10.23.1.192/26  

Som `/hana/data` och `/hana/log` distribueras på lokala diskar är det inte nödvändigt att distribuera separata undernät och separata virtuella nätverks kort för kommunikation till lagringen.  

Azure NetApp-volymerna distribueras i ett separat undernät som [delegerats till Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet): `anf` 10.23.1.0/26.   

> [!IMPORTANT]
> Systemreplikering till en tredje plats stöds inte. Mer information finns i avsnittet viktiga krav i [SLES-SAP HANA system replikering skalbara prestanda optimerings scenario](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-scaleOut-PerfOpt-12/index.html#_important_prerequisites).     

## <a name="set-up-the-infrastructure"></a>Konfigurera infrastrukturen

I anvisningarna nedan förutsätter vi att du redan har skapat resurs gruppen, det virtuella Azure-nätverket med tre undernät för Azure-nätverk: `client` , `inter` och `hsr` .

### <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Distribuera virtuella Linux-datorer via Azure Portal
1. Distribuera de virtuella Azure-datorerna.  
För konfigurationen som presenteras i det här dokumentet distribuerar du sju virtuella datorer: 
   - tre virtuella datorer som fungerar som HANA DB-noder för HANA-replikering plats 1: **Hana-S1-DB1**, **Hana-S1-DB2** och **Hana-S1-DB3**  
   - tre virtuella datorer som fungerar som HANA DB-noder för HANA-replikering plats 2: **Hana-S2-DB1**, **Hana-S2-DB2** och **Hana-S2-DB3**  
   - en liten virtuell dator som ska fungera som *majoritets tillverkare*: **Hana-s-mm**

   De virtuella datorerna, distribuerade som SAP DB HANA-noder, bör certifieras av SAP för HANA som publicerat i [SAP HANA maskin varu katalog](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). När du distribuerar HANA DB-noderna kontrollerar du att det [accelererade nätverket](../../../virtual-network/create-vm-accelerated-networking-cli.md) är markerat.  
  
   För majoritetsnoduppsättning-noden kan du distribuera en liten virtuell dator eftersom den virtuella datorn inte kör någon av SAP HANA resurserna. Majoriteten av den virtuella datorn i klustret används i kluster konfigurationen för att uppnå ett udda antal klusternoder i ett scenario med delad hjärna. Majoriteten av den virtuella datorn behöver bara ett virtuellt nätverks gränssnitt i `client` under nätet i det här exemplet.        

   Distribuera lokala hanterade diskar för `/hana/data` och `/hana/log` . Den minsta rekommenderade lagrings konfigurationen för `/hana/data` och `/hana/log` beskrivs i [SAP HANA Storage-konfigurationer för virtuella Azure-datorer](./hana-vm-operations-storage.md).

   Distribuera det primära nätverks gränssnittet för varje virtuell dator i det `client` virtuella nätverkets undernät.  
   När den virtuella datorn distribueras via Azure Portal genereras nätverks gränssnittets namn automatiskt. I dessa anvisningar för enkelhetens skull kommer vi att referera till de automatiskt genererade primära nätverks gränssnitten, som är kopplade till det `client` virtuella nätverkets undernät i Azure som **Hana-S1-DB1-client**, **Hana-S1-DB2-client**, **Hana-S1-DB3-client** och så vidare.  


   > [!IMPORTANT]
   > Kontrol lera att det OS du väljer är SAP-certifierat för SAP HANA på de angivna VM-typerna som du använder. En lista över SAP HANA certifierade VM-typer och OS-versioner för dessa typer finns på webbplatsen [SAP HANA Certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) . Klicka i informationen om den virtuella dator typen som visas för att hämta den fullständiga listan med SAP HANA operativ system versioner som stöds för den typen.  
  

2. Skapa sex nätverks gränssnitt, ett för varje HANA DB-dator, i det `inter` virtuella nätverkets undernät (i det här exemplet **Hana-S1-DB1 – mellan**, **Hana-S1-DB2-Inter**, **Hana-S1-DB3-Inter**, **Hana-S2-DB1-Inter**, **Hana-S2-DB2-** Inter, och **Hana-S2-DB3-Inter**).  

3. Skapa sex nätverks gränssnitt, ett för varje HANA DB-dator i det `hsr` virtuella nätverkets undernät (i det här exemplet **Hana-S1-DB1-HSR**, **Hana-S1-DB2-HSR**, **Hana-S1-DB3-HSR**, **Hana-S2-DB1-HSR**, **Hana-S2-DB2-HSR** och **Hana-S2-DB3-HSR**).  

4. Koppla de nyligen skapade virtuella nätverks gränssnitten till motsvarande virtuella datorer:  

    a. Gå till den virtuella datorn i [Azure Portal](https://portal.azure.com/#home).  

    b. I den vänstra rutan väljer du **Virtual Machines**. Filtrera på namnet på den virtuella datorn (till exempel **Hana-S1-DB1**) och välj sedan den virtuella datorn.  

    c. I fönstret **Översikt** väljer du **stoppa** för att frigöra den virtuella datorn.  

    d. Välj **nätverk** och Anslut sedan nätverks gränssnittet. I list rutan **bifoga nätverks gränssnitt** väljer du redan skapade nätverks gränssnitt för- `inter` och- `hsr` undernät.  
    
    e. Välj **Spara**. 
 
    f. Upprepa steg b till e för de återstående virtuella datorerna (i vårt exempel  **Hana-S1-DB2**, **Hana-S1-DB3**, **Hana-S2-DB1**, **Hana-S2-DB2** och **Hana-S2-DB3**).
 
    ex. Lämna kvar de virtuella datorerna i stoppat tillstånd för tillfället. Därefter aktiverar vi [accelererat nätverk](../../../virtual-network/create-vm-accelerated-networking-cli.md) för alla nyligen anslutna nätverks gränssnitt.  

5. Aktivera accelererat nätverk för de ytterligare nätverks gränssnitten för- `inter` och- `hsr` undernätet genom att utföra följande steg:  

    a. Öppna [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) i [Azure Portal](https://portal.azure.com/#home).  

    b. Kör följande kommandon för att aktivera accelererat nätverk för ytterligare nätverks gränssnitt, som är kopplade till- `inter` och- `hsr` undernät.  

    ```azurecli
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-inter --accelerated-networking true
    
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-hsr --accelerated-networking true
    ```

7. Starta de virtuella HANA-databas datorerna

### <a name="deploy-azure-load-balancer"></a>Distribuera Azure Load Balancer

1. Vi rekommenderar att du använder standard belastnings utjämning. Följ dessa konfigurations steg för att distribuera standard Load Balancer:
   1. Börja med att skapa en IP-pool på klient sidan:

      1. Öppna belastningsutjämnaren, Välj **klient delens IP-pool** och välj **Lägg till**.
      1. Ange namnet på den nya frontend-IP-poolen (till exempel **Hana-frontend**).
      1. Ange **tilldelningen** till **statisk** och ange IP-adressen (till exempel **10.23.0.27**).
      1. Välj **OK**.
      1. När den nya frontend-IP-poolen har skapats noterar du poolens IP-adress.

   1. Skapa sedan en backend-pool och Lägg till alla virtuella kluster datorer i backend-poolen:

      1. Öppna belastningsutjämnaren, Välj **backend-pooler** och välj **Lägg till**.
      1. Ange namnet på den nya backend-poolen (till exempel **Hana-backend**).
      1. Välj **Lägg till en virtuell dator**.
      1. Välj **Virtuell dator**.
      1. Välj de virtuella datorerna i SAP HANA klustret och deras IP-adresser för `client` under nätet.
      1. Välj **Lägg till**.

   1. Skapa sedan en hälso avsökning:

      1. Öppna belastningsutjämnaren, Välj **hälso avsökningar** och välj **Lägg till**.
      1. Ange namnet på den nya hälso avsökningen (till exempel **Hana-HP**).
      1. Välj **TCP** som protokoll och port 625 **03**. Behåll värdet för **Interval** inställt på 5 och **tröskelvärdet för tröskelvärdet** har värdet 2.
      1. Välj **OK**.

   1. Skapa sedan reglerna för belastnings utjämning:
   
      1. Öppna belastningsutjämnaren, Välj **belastnings Utjämnings regler** och välj **Lägg till**.
      1. Ange namnet på den nya belastnings Utjämnings regeln (till exempel **Hana-lb**).
      1. Välj IP-adressen för klient delen, backend-poolen och hälso avsökningen som du skapade tidigare (till exempel **Hana-frontend**, **Hana-backend** och **Hana-HP**).
      1. Välj **ha-portar**.
      1. Se till att **Aktivera flytande IP**.
      1. Välj **OK**.

   > [!IMPORTANT]
   > Flytande IP stöds inte på en sekundär NIC-IP-konfiguration i belastnings Utjämnings scenarier. Mer information finns i [begränsningar för Azure Load Balancer](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Om du behöver ytterligare IP-adress för den virtuella datorn distribuerar du ett andra nätverkskort.    
   
   > [!Note]
   > När virtuella datorer utan offentliga IP-adresser placeras i backend-poolen för intern (ingen offentlig IP-adress) standard Azure-belastningsutjämnare, kommer det inte att finnas någon utgående Internet anslutning, om inte ytterligare konfiguration utförs för att tillåta routning till offentliga slut punkter. Mer information om hur du uppnår utgående anslutningar finns i Översikt över [offentliga slut punkter för Virtual Machines med Azure standard Load Balancer i SAP-scenarier med hög tillgänglighet](./high-availability-guide-standard-load-balancer-outbound-connections.md).  


   > [!IMPORTANT]
   > Aktivera inte TCP-tidsstämplar på virtuella Azure-datorer som placerats bakom Azure Load Balancer. Om du aktiverar TCP-tidsstämplar kommer hälso avsökningarna att Miss skadas. Ange parametern **net.IPv4.tcp_timestamps** till **0**. Mer information finns i [Load Balancer hälso avsökningar](../../../load-balancer/load-balancer-custom-probe-overview.md).
   > Se även SAP anmärkning [2382421](https://launchpad.support.sap.com/#/notes/2382421).  

### <a name="deploy-the-azure-netapp-files-infrastructure"></a>Distribuera Azure NetApp Files-infrastrukturen 

Distribuera ANF-volymerna för `/hana/shared` fil systemet. Du behöver en separat `/hana/shared` volym för varje Hana-plats för system replikering. Mer information finns i [konfigurera Azure NetApp Files-infrastrukturen](./sap-hana-scale-out-standby-netapp-files-suse.md#set-up-the-azure-netapp-files-infrastructure).

I det här exemplet användes följande Azure NetApp Files volymer: 

* volym **HN1**-delad – s1 (NFS://10.23.1.7/**HN1**-Shared-S1)
* volym **HN1**– delad – s2 (NFS://10.23.1.7/**HN1**-Shared-S2)


## <a name="operating-system-configuration-and-preparation"></a>Konfiguration och förberedelser för operativ system

Anvisningarna i nästa avsnitt föregås av en av följande förkortningar:
* **[A]**: gäller för alla noder
* **[Ah]**: gäller för alla Hana DB-noder
* **[M]**: gäller för majoritetsnoduppsättning-noden
* **[AH1]**: gäller för alla Hana DB-noder på plats 1
* **[AH2]**: gäller för alla Hana DB-noder på plats 2
* **[1]**: gäller endast för Hana DB Node 1, plats 1
* **[2]**: gäller endast för Hana DB Node 1, plats 2

Konfigurera och Förbered ditt operativ system genom att utföra följande steg:

1. **[A]** underhålla värd filen på de virtuella datorerna. Inkludera poster för alla undernät. Följande poster har lagts till i `/etc/hosts` i det här exemplet.  

    ```bash
     # Client subnet
     10.23.0.19      hana-s1-db1
     10.23.0.20      hana-s1-db2
     10.23.0.21      hana-s1-db3
     10.23.0.22      hana-s2-db1
     10.23.0.23      hana-s2-db2
     10.23.0.24      hana-s2-db3
     10.23.0.25      hana-s-mm    
     # Internode subnet
     10.23.1.132     hana-s1-db1-inter
     10.23.1.133     hana-s1-db2-inter
     10.23.1.134     hana-s1-db3-inter
     10.23.1.135     hana-s2-db1-inter
     10.23.1.136     hana-s2-db2-inter
     10.23.1.137     hana-s2-db3-inter
     # HSR subnet
     10.23.1.196     hana-s1-db1-hsr
     10.23.1.197     hana-s1-db2-hsr
     10.23.1.198     hana-s1-db3-hsr
     10.23.1.199     hana-s2-db1-hsr
     10.23.1.200     hana-s2-db2-hsr
     10.23.1.201     hana-s2-db3-hsr
    ```

2. **[A]** SUSE levererar särskilda resurs agenter för SAP HANA och som standard agenter för SAP HANA ScaleUp installeras. Avinstallera paketen för ScaleUp, om de är installerade och installera paketen för scenario SAP HANAScaleOut. Steget måste utföras på alla virtuella kluster datorer, inklusive majoritets Maker.   

    ```bash
     # Uninstall ScaleUp packages and patterns
     zypper remove patterns-sap-hana
     zypper remove SAPHanaSR 
     zypper remove SAPHanaSR-doc
     zypper remove yast2-sap-ha
     # Install the ScaleOut packages and patterns
     zypper in SAPHanaSR-ScaleOut  SAPHanaSR-ScaleOut-doc 
     zypper in -t pattern ha_sles
    ```

3. **[Ah]** Förbered de virtuella datorerna – Använd de rekommenderade inställningarna per SAP NOTE [2205917] för SUSE Linux Enterprise Server för SAP-program.  

## <a name="prepare-the-file-systems"></a>Förbereda fil systemen
### <a name="mount-the-shared-file-systems"></a>Montera de delade fil systemen

I det här exemplet distribueras de delade HANA-fil systemen på Azure NetApp Files och monteras över NFSv4.  

1. **[Ah]** Skapa monterings punkter för HANA-databasens volymer.  

    ```bash
    mkdir -p /hana/shared
    ```

2. **[Ah]** Verifiera NFS-domän inställningen. Kontrol lera att domänen är konfigurerad som standard Azure NetApp Files domän, det vill säga och att **`defaultv4iddomain.com`** mappningen är inställd på **ingen**.  
   Det här steget behövs bara om du använder Azure NetAppFiles NFSv 4.1.  

    > [!IMPORTANT]
    > Se till att ange att NFS-domänen på `/etc/idmapd.conf` den virtuella datorn ska matcha standard domän konfigurationen på Azure NetApp Files: **`defaultv4iddomain.com`** . Om det finns ett matchnings fel mellan domän konfigurationen på NFS-klienten (d.v.s. den virtuella datorn) och NFS-servern, t. ex. Azure NetApp-konfigurationen, så visas behörigheterna för filer på Azure NetApp-volymer som är monterade på de virtuella datorerna som `nobody` .  

    ```bash
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

3. **[Ah]** Verifiera `nfs4_disable_idmapping` . Den måste anges till **Y**. Kör monterings kommandot för att skapa en katalog struktur där `nfs4_disable_idmapping` finns. Du kan inte skapa katalogen manuellt under/sys/modules eftersom åtkomst är reserverad för kernel/driv rutiner.  
   Det här steget behövs bara om du använder Azure NetAppFiles NFSv 4.1.  

    ```bash
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.23.1.7:/HN1-share-s1 /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

4. **[AH1]** Montera de delade Azure NetApp Files volymerna på de virtuella SITE1 HANA-DATABASerna.  

    ```bash
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.7:/HN1-shared-s1 /hana/shared nfs rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    ```

5. **[AH2]** Montera de delade Azure NetApp Files volymerna på de virtuella SITE2 HANA-DATABASerna.  

    ```bash
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.7:/HN1-shared-s2 /hana/shared nfs rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    ```


10. **[Ah]** Kontrol lera att motsvarande `/hana/shared/` fil system är monterade på alla Hana DB VM: ar med NFS- **NFSv4**.  

    ```bash
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

### <a name="prepare-the-data-and-log-local-file-systems"></a>Förbereda data och logga lokala fil system
I den `/hana/data` `/hana/log` sammanställda konfigurationen och distribueras fil systemen på den hanterade disken och bifogas lokalt till varje Hana DB-VM. Du måste utföra stegen för att skapa lokala data och logg volymer på varje HANA DB-dator. 

Konfigurera disklayouten med  **Logical Volume Manager (LVM)**. I följande exempel förutsätts att varje HANA-virtuell dator har tre data diskar anslutna, som används för att skapa två volymer.

1. **[Ah]** Lista alla tillgängliga diskar:
    ```bash
    ls /dev/disk/azure/scsi1/lun*
    ```

   Exempel på utdata:

    ```bash
    /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2 
    ```

2. **[Ah]** Skapa fysiska volymer för alla diskar som du vill använda:
    ```bash
    sudo pvcreate /dev/disk/azure/scsi1/lun0
    sudo pvcreate /dev/disk/azure/scsi1/lun1
    sudo pvcreate /dev/disk/azure/scsi1/lun2
    ```

3. **[Ah]** Skapa en volym grupp för datafilerna. Använd en volym grupp för loggfilerna och en för den delade katalogen för SAP HANA:
    ```bash
    sudo vgcreate vg_hana_data_HN1 /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
    sudo vgcreate vg_hana_log_HN1 /dev/disk/azure/scsi1/lun2
    ```

4. **[Ah]** Skapa de logiska volymerna. 
   En linjär volym skapas när du använder `lvcreate` utan `-i` växeln. Vi rekommenderar att du skapar en stripe-volym för bättre I/O-prestanda och justerar stripe-storlekarna mot värdena som dokumenteras i [SAP HANA VM Storage-konfigurationer](./hana-vm-operations-storage.md). `-i`Argumentet ska vara antalet underliggande fysiska volymer och `-I` argumentet är stripe-storleken. I det här dokumentet används två fysiska volymer för data volymen, så `-i` växel argumentet är inställt på **2**. Stripe-storleken för data volymen är **256 KiB**. En fysisk volym används för logg volymen, så inga `-i` eller `-I` växlar används explicit för logg volym kommandona.  

   > [!IMPORTANT]
   > Använd `-i` växeln och Ställ in den på den underliggande fysiska volymens nummer när du använder mer än en fysisk volym för varje data-eller logg volym. Använd `-I` växeln för att ange stripe-storlek när du skapar en stripe-volym.  
   > Se [SAP HANA VM Storage-konfigurationer](./hana-vm-operations-storage.md) för rekommenderade diskpartitionskonfigurationer, inklusive stripe-storlekar och antal diskar.  

    ```bash
    sudo lvcreate -i 2 -I 256 -l 100%FREE -n hana_data vg_hana_data_HN1
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_HN1
    sudo mkfs.xfs /dev/vg_hana_data_HN1/hana_data
    sudo mkfs.xfs /dev/vg_hana_log_HN1/hana_log
    ```

5. **[Ah]** Skapa monterings kataloger och kopiera UUID: n för alla logiska volymer:
    ```bash
    sudo mkdir -p /hana/data/HN1
    sudo mkdir -p /hana/log/HN1
    # Write down the ID of /dev/vg_hana_data_HN1/hana_data and /dev/vg_hana_log_HN1/hana_log
    sudo blkid
    ```

6. **[Ah]** Skapa `fstab` poster för de logiska volymerna och montera:
    ```bash
    sudo vi /etc/fstab
    ```

   Lägg till följande rad i `/etc/fstab` filen:

    ```bash
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_data_HN1-hana_data /hana/data/HN1 xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_log_HN1-hana_log /hana/log/HN1 xfs  defaults,nofail  0  2
    ```

   Montera de nya volymerna:

    ```bash
    sudo mount -a
    ```

## <a name="create-a-pacemaker-cluster"></a>Skapa ett pacemaker-kluster

Följ stegen i [Konfigurera pacemaker på SUSE Linux Enterprise Server i Azure](high-availability-guide-suse-pacemaker.md) för att skapa ett grundläggande pacemaker-kluster för den här Hana-servern.
Ta med alla virtuella datorer, inklusive majoriteten i klustret.  

> [!IMPORTANT]
> Ange inte `quorum expected-votes` 2 eftersom det inte är ett kluster med två noder.  
> Se till att kluster egenskapen `concurrent-fencing`  är aktive rad, så att nod-avgränsningen avserialiseras.   

## <a name="installation"></a>Installation  

I det här exemplet har vi använt HANA 2,0 SP4 i det här exemplet för att distribuera SAP HANA i en skalbar konfiguration med HSR på virtuella Azure-datorer.  

### <a name="prepare-for-hana-installation"></a>Förbered för HANA-installation

1. **[Ah]** Ange rot lösen ordet före HANA-installationen. Du kan inaktivera rot lösen ordet när installationen har slutförts. Kör som- `root` kommando `passwd` .  

2. **[1, 2]** ändra behörigheter för `/hana/shared` 
    ```bash
    chmod 775 /hana/shared
    ```

3. **[1]** kontrol lera att du kan logga in via SSH till de virtuella Hana-databaserna på den här platsen **Hana-S1-DB2** och **Hana-S1-DB3**, utan att uppmanas att ange ett lösen ord. Om så inte är fallet, Exchange SSH-nycklar enligt beskrivningen i [Aktivera SSH-åtkomst via offentlig nyckel](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-scaleOut-PerfOpt-12/index.html#_enable_ssh_access_via_public_key_optional).  
    ```bash
    ssh root@hana-s1-db2
    ssh root@hana-s1-db3
    ```

4. **[2]** kontrol lera att du kan logga in via SSH till de virtuella Hana-databaserna på den här platsen **Hana-S2-DB2** och **Hana-S2-DB3**, utan att uppmanas att ange ett lösen ord.  
   Om så inte är fallet, Exchange SSH-nycklar.    
    ```bash
    ssh root@hana-s2-db2
    ssh root@hana-s2-db3
    ```

5. **[Ah]** Installera ytterligare paket, vilket krävs för HANA 2,0 SP4. Mer information finns i SAP NOTE [2593824](https://launchpad.support.sap.com/#/notes/2593824) för din SLES-version. 

    ```bash
    # In this example, using SLES12 SP5
    sudo zypper install libgcc_s1 libstdc++6 libatomic1
    ```
### <a name="hana-installation-on-the-first-node-on-each-site"></a>HANA-installation på den första noden på varje plats

1. **[1]** installera SAP HANA genom att följa anvisningarna i [installations-och uppdaterings hand boken för SAP HANA 2,0](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). I anvisningarna nedan visar vi SAP HANA installationen på den första noden på plats 1.   

   a. Starta **hdblcm** -programmet som `root` från installations program katalogen Hana. Använd `internal_network` parametern och skicka adress utrymmet för under nätet som används för den interna Hana-kommunikationen mellan noderna.  

    ```bash
    ./hdblcm --internal_network=10.23.1.128/26
    ```

   b. Ange följande värden i prompten:

     * För **Välj en åtgärd**: ange **1** (för installation)
     * För **Ytterligare komponenter för installation**: ange **2, 3**
     * För installations Sök väg: Tryck på RETUR (Standardvärdet är/Hana/Shared)
     * För **Lokalt värdnamn**: Tryck på RETUR för att acceptera standard
     * För vill **du lägga till värdar i systemet?**: ange **n**
     * För **SAP HANA system-ID**: ange **HN1**
     * För **instans nummer** [00]: ange **03**
     * För **lokal värd Worker-grupp** [standard]: Tryck på RETUR för att acceptera standard
     * För **Välj system användning/ange index [4]**: ange **4** (för anpassad)
     * För **plats för data volymer** [/Hana/data/HN1]: Tryck på RETUR för att acceptera standardvärdet
     * För **plats för logg volymer** [/Hana/log/HN1]: Tryck på RETUR för att acceptera standardvärdet
     * **Begränsa maximal minnesallokering?** [n]: ange **n**
     * För **certifikat värd namnet för värden Hana-S1-DB1** [Hana-S1-DB1]: Tryck på RETUR för att acceptera standardvärdet
     * För **SAP host agent-användare (sapadm) lösen ord**: Ange lösen ordet
     * För att **Bekräfta lösen ordet för SAP host agent-användare (sapadm)**: Ange lösen ordet
     * För **system administratörs lösen ord (hn1adm)**: Ange lösen ordet
     * För **system administratörens Hem Katalog** [/usr/SAP/HN1/Home]: Tryck på RETUR för att acceptera standardvärdet
     * För **system administratörens inloggnings gränssnitt** [/bin/sh]: Tryck på RETUR för att acceptera standardvärdet
     * För **system administratörens användar-ID** [1001]: Tryck på RETUR för att acceptera standardvärdet
     * För **Ange ID för användar gruppen (sapsys)** [79]: Tryck på RETUR för att acceptera standardvärdet
     * För **lösen ord för system databas användare (system)**: Ange systemets lösen ord
     * För **Bekräfta System-Password (system Database User)**: Ange systemets lösen ord
     * För **omstart av systemet efter omstart av datorn?** [n]: ange **n** 
     * För vill **du fortsätta (j/n)**: validera sammanfattningen och om allt ser bra ut anger du **y**

2. **[2]** upprepa föregående steg för att installera SAP HANA på den första NODEN på plats 2.   

3. **[1, 2]** kontrol lera global.ini  

   Visa global.ini och se till att konfigurationen för den interna SAP HANA kommunikationen mellan noderna är på plats. Verifiera **kommunikations** avsnittet. Det ska ha adress utrymmet för `inter` under nätet och `listeninterface` ska vara inställt på `.internal` . Verifiera **internal_hostname_resolution** avsnittet. Den bör ha IP-adresserna för de HANA-virtuella datorer som tillhör `inter` under nätet.  

   ```bash
     sudo cat /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
     # Example from SITE1 
     [communication]
     internal_network = 10.23.1.128/26
     listeninterface = .internal
     [internal_hostname_resolution]
     10.23.1.132 = hana-s1-db1
     10.23.1.133 = hana-s1-db2
     10.23.1.134 = hana-s1-db3
   ```

4. **[1, 2]** Förbered `global.ini` för installation i icke-delad miljö, enligt beskrivningen i SAP anmärkning [2080991](https://launchpad.support.sap.com/#/notes/0002080991).  

   ```bash
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    [persistence]
    basepath_shared = no
   ```

4. **[1, 2]** starta om SAP HANA för att aktivera ändringarna.  

   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem
   ```

6. **[1, 2]** kontrol lera att klient gränssnittet kommer att använda IP-adresserna från `client` under nätet för kommunikation.  

    ```bash
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "password" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result - example from SITE 2
    "hana-s2-db1","net_publicname","10.23.0.22"
   ```

   Information om hur du verifierar konfigurationen finns i SAP anmärkning [2183363 – konfiguration av SAP HANA internt nätverk](https://launchpad.support.sap.com/#/notes/2183363).  

7. **[Ah]** Ändra behörigheter för data-och logg kataloger för att undvika HANA-installations fel.  

   ```bash
    sudo chmod o+w -R /hana/data /hana/log
   ```

8. **[1]** installera de sekundära Hana-noderna. Exempel instruktionerna i det här steget är för plats 1.  
   a. Starta det inhemska **hdblcm** -programmet som `root` .    
    ```bash
     cd /hana/shared/HN1/hdblcm
     ./hdblcm 
    ```

   b. Ange följande värden i prompten:

     * För **Välj en åtgärd**: ange **2** (för Lägg till värdar)
     * För **att ange kommaavgränsade värdnamn som ska läggas** till: Hana-S1-DB2, Hana-S1-DB3
     * För **Ytterligare komponenter för installation**: ange **2, 3**
     * För **Ange rot användar namn [root]**: Tryck på RETUR för att acceptera standardvärdet
     * För **Välj roller för värden: Hana-S1-DB2 [1]**: 1 (för arbetare)
     * För **Ange värd för redundans grupp för värden ' Hana-S1-DB2 ' [standard]**: Tryck på RETUR för att acceptera standardvärdet
     * För **Ange lagrings partitions nummer för värden ' Hana-S1-DB2 ' [<<assign automatically>>]**: Tryck på RETUR för att acceptera standardvärdet
     * För **Ange arbets grupp för värden ' Hana-S1-DB2 ' [standard]**: Tryck på RETUR för att acceptera standardvärdet
     * För **Välj roller för värden: Hana-S1-DB3 [1]**: 1 (för arbetare)
     * För **Ange värd för redundans grupp för värden ' Hana-S1-DB3 ' [standard]**: Tryck på RETUR för att acceptera standardvärdet
     * För **Ange lagrings partitions nummer för värden ' Hana-S1-DB3 ' [<<assign automatically>>]**: Tryck på RETUR för att acceptera standardvärdet
     * För **Ange arbets grupp för värden ' Hana-S1-DB3 ' [standard]**: Tryck på RETUR för att acceptera standardvärdet
     * För **system administratörs lösen ord (hn1adm)**: Ange lösen ordet
     * Ange lösen ord för **SAP host agent-användare (sapadm)**: Ange lösen ordet
     * För att **Bekräfta lösen ordet för SAP host agent-användare (sapadm)**: Ange lösen ordet
     * För **certifikat värd namnet för värden Hana-S1-DB2** [Hana-S1-DB2]: Tryck på RETUR för att acceptera standardvärdet
     * För **certifikat värd namnet för värden Hana-S1-DB3** [Hana-S1-DB3]: Tryck på RETUR för att acceptera standardvärdet
     * För vill **du fortsätta (j/n)**: validera sammanfattningen och om allt ser bra ut anger du **y**

9. **[2]** upprepa föregående steg för att installera de sekundära SAP HANA noderna på plats 2.   

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurera SAP HANA 2,0-systemreplikering

1. **[1]** konfigurera systemreplikering på plats 1:

   Säkerhetskopiera databaserna som **HN1** ADM:

    ```bash
    hdbsql -d SYSTEMDB -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupSYS')"
    hdbsql -d HN1 -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupHN1')"
    ```

   Kopiera systemets PKI-filer till den sekundära platsen:

    ```bash
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/data/SSFS_HN1.DAT hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/data/
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/key/SSFS_HN1.KEY  hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/key/
    ```

   Skapa den primära platsen:

    ```bash
    hdbnsutil -sr_enable --name=HANA_S1
    ```

2. **[2]** konfigurera systemreplikering på plats 2:
    
   Registrera den andra platsen för att starta systemreplikeringen. Kör följande kommando som <hanasid \> ADM:

    ```bash
    sapcontrol -nr 03 -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=hana-s1-db1 --remoteInstance=03 --replicationMode=sync --name=HANA_S2
    sapcontrol -nr 03 -function StartSystem
    ```

3. **[1]** kontrol lera replikeringsstatus

   Kontrol lera replikeringsstatus och vänta tills alla databaser är synkroniserade.

    ```bash
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    # | Database | Host          | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
    # |          |               |       |              |           |         |           | Host          | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
    # | -------- | ------------- | ----- | ------------ | --------- | ------- | --------- | ------------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
    # | HN1      | hana-s1-db3   | 30303 | indexserver  |         5 |       1 | HANA_S1   | hana-s2-db3   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | SYSTEMDB | hana-s1-db1   | 30301 | nameserver   |         1 |       1 | HANA_S1   | hana-s2-db1   |     30301 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30307 | xsengine     |         2 |       1 | HANA_S1   | hana-s2-db1   |     30307 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30303 | indexserver  |         3 |       1 | HANA_S1   | hana-s2-db1   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db2   | 30303 | indexserver  |         4 |       1 | HANA_S1   | hana-s2-db2   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    #
    # status system replication site "2": ACTIVE
    # overall system replication status: ACTIVE
    #
    # Local System Replication State
    #   
    # mode: PRIMARY
    # site id: 1
    # site name: HANA_S1
    ```

4. **[1, 2]** ändra Hana-konfigurationen så att kommunikation för Hana-systemreplikering om det riktas mot de virtuella nätverks gränssnitten Hana System Replication.   
   - Stoppa HANA på båda platserna
    ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem HDB
    ```

   - Redigera global.ini för att lägga till värd mappningen för HANA-systemreplikering: Använd IP-adresserna från `hsr` under nätet.  
    ```bash
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [system_replication_hostname_resolution]
    10.23.1.196 = hana-s1-db1
    10.23.1.197 = hana-s1-db2
    10.23.1.198 = hana-s1-db3
    10.23.1.199 = hana-s2-db1
    10.23.1.200 = hana-s2-db2
    10.23.1.201 = hana-s2-db3
    ```

   - Starta HANA på båda platserna
   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem HDB
   ```

   Mer information finns i [värd namns matchning för systemreplikering](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/1.0.12/en-US/c0cba1cb2ba34ec89f45b48b2157ec7b.html).  

## <a name="create-file-system-resources"></a>Skapa fil system resurser

Skapa en fil system kluster resurs som ska övervakas och rapportera fel, om det uppstår problem med att komma åt det NFS-monterade fil systemet `/hana/shared` . Det gör att klustret kan utlösa redundans, om det uppstår problem med åtkomsten `/hana/shared` . Mer information finns i [Hantera felaktig NFS-resurs i suset ha-kluster för Hana-systemreplikering](https://www.suse.com/support/kb/doc/?id=000019904) 

1. **[1]** placera pacemaker i underhålls läge, som förberedelse för att skapa Hana-klusterresurser.  
    ```bash
    crm configure property maintenance-mode=true
    ```

2. **[1, 2]** skapa katalogen på ANF/Hana/sahred-volymen som kommer att användas i den särskilda resursen för övervakning av fil system. Katalogerna måste skapas på båda platserna.  
    ```bash
    mkdir -p /hana/shared/HN1/check
    ```

2. **[Ah]** Skapa katalogen som ska användas för att montera den särskilda resursen för övervakning av fil system. Katalogen måste skapas på alla HANA-klusternoder.  
    ```bash
    mkdir -p /hana/check
    ```

3. **[1]** skapa fil systemets kluster resurser.     

    ```bash
    crm configure primitive fs_HN1_HDB03_fscheck Filesystem \
      params device="/hana/shared/HN1/check" \
      directory="/hana/check" fstype=nfs4 \
      options="bind,defaults,rw,hard,proto=tcp,intr,noatime,vers=4.1,lock" \
      op monitor interval=120 timeout=120 on-fail=fence \
      op_params OCF_CHECK_LEVEL=20 \
      op start interval=0 timeout=120 op stop interval=0 timeout=120

    crm configure clone cln_fs_HN1_HDB03_fscheck fs_HN1_HDB03_fscheck \
      meta clone-node-max=1 interleave=true

    crm configure location loc_cln_fs_HN1_HDB03_fscheck_not_on_mm \
      cln_fs_HN1_HDB03_fscheck -inf: hana-s-mm    
    ```
 
   `OCF_CHECK_LEVEL=20` attribut läggs till i övervaknings åtgärden, så att övervaknings åtgärder utför ett Skriv-/skriv test i fil systemet. Utan det här attributet verifierar övervaknings åtgärden bara att fil systemet är monterat. Detta kan vara ett problem eftersom när anslutningen bryts, kan fil systemet fortsätta att vara monterat, trots att det inte går att komma åt dem.  

   `on-fail=fence` attributet läggs också till i övervaknings åtgärden. Med det här alternativet, om övervaknings åtgärden Miss lyckas på en nod, stängs den noden omedelbart.   

## <a name="create-sap-hana-cluster-resources"></a>Skapa SAP HANA kluster resurser

1. **[1, 2]** installera Hana-"systemets replikerings-Hook". Hooken måste installeras på en HANA DB-nod på varje plats för system replikering.         

   1. Förbered hooken som `root` 
    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR-ScaleOut/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. Stoppa HANA på båda platserna för system replikering. Kör som <sid- \> ADM:
    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

   3. Automatiskt `global.ini`
    ```bash
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

2. **[Ah]** Klustret kräver sudoers-konfiguration på klusternoden för <sid \> adm. I det här exemplet som uppnås genom att skapa en ny fil. Kör-kommandona som `root` .    
    ```bash
    cat << EOF > /etc/sudoers.d/20-saphana
    # SAPHanaSR-ScaleOut needs for srHook
    Cmnd_Alias SOK = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SOK -t crm_config -s SAPHanaSR
    Cmnd_Alias SFAIL = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SFAIL -t crm_config -s SAPHanaSR
    hn1adm ALL=(ALL) NOPASSWD: SOK, SFAIL
    EOF
    ```

3. **[1, 2]** starta SAP HANA på båda replikerings platserna. Kör som <sid \> adm.  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

4. **[1]** kontrol lera Hook-installationen. Kör som <sid \> adm på den aktiva webbplatsen för Hana-systemreplikering.   

    ```bash
    cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*

     # 2021-03-31 01:02:42.695244 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:02:58.966856 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:03:04.453100 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:03:04.619768 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:03:04.743444 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:04:15.062181 ha_dr_SAPHanaSR SOK

    ```

5. **[1]** skapa kluster resurserna Hana. Kör följande kommandon som `root` .    
   1. Kontrol lera att klustret redan är i underhålls läge.  
    
   2. Skapa sedan resursen HANA-topologi.  
      ```bash
      sudo crm configure primitive rsc_SAPHanaTopology_HN1_HDB03 ocf:suse:SAPHanaTopology \
        op monitor interval="10" timeout="600" \
        op start interval="0" timeout="600" \
        op stop interval="0" timeout="300" \
        params SID="HN1" InstanceNumber="03"

      sudo crm configure clone cln_SAPHanaTopology_HN1_HDB03 rsc_SAPHanaTopology_HN1_HDB03 \
       meta clone-node-max="1" target-role="Started" interleave="true"
      ```

   3. Skapa sedan resursen HANA-instans.  
      > [!NOTE] 
      > Den här artikeln innehåller referenser till villkors *huvud* och *slav*, och villkor som Microsoft inte längre använder. När de här villkoren tas bort från program varan tar vi bort dem från den här artikeln.
 
      ```bash
      sudo crm configure primitive rsc_SAPHana_HN1_HDB03 ocf:suse:SAPHanaController \
        op start interval="0" timeout="3600" \
        op stop interval="0" timeout="3600" \
        op promote interval="0" timeout="3600" \
        op monitor interval="60" role="Master" timeout="700" \
        op monitor interval="61" role="Slave" timeout="700" \
        params SID="HN1" InstanceNumber="03" PREFER_SITE_TAKEOVER="true" \
        DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

      sudo crm configure ms msl_SAPHana_HN1_HDB03 rsc_SAPHana_HN1_HDB03 \
        meta clone-node-max="1" master-max="1" interleave="true"
      ```
      > [!IMPORTANT]
      > Vi rekommenderar att du bara anger AUTOMATED_REGISTER till **Nej**, samtidigt som du utför omfattande fel söknings test, för att förhindra att den primära instansen registreras som sekundär automatiskt. När de misslyckade testerna har slutförts ställer du in AUTOMATED_REGISTER till **Ja**, så att när uppköps system replikering kan återupptas automatiskt. 

   4. Skapa virtuell IP-adress och associerade resurser.  
      ```bash
      sudo crm configure primitive rsc_ip_HN1_HDB03 ocf:heartbeat:IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="10.23.0.27"

      sudo crm configure primitive rsc_nc_HN1_HDB03 azure-lb port=62503 \
        meta resource-stickiness=0

      sudo crm configure group g_ip_HN1_HDB03 rsc_ip_HN1_HDB03 rsc_nc_HN1_HDB03
      ```

   5. Skapa kluster begränsningar  
      ```bash
      # Colocate the IP with HANA master
      sudo crm configure colocation col_saphana_ip_HN1_HDB03 4000: g_ip_HN1_HDB03:Started \
        msl_SAPHana_HN1_HDB03:Master  

      # Start HANA Topology before HANA  instance
      sudo crm configure order ord_SAPHana_HN1_HDB03 Optional: cln_SAPHanaTopology_HN1_HDB03 \
        msl_SAPHana_HN1_HDB03

      # HANA resources don't run on the majority maker node
      sudo crm configure location loc_SAPHanaCon_not_on_majority_maker msl_SAPHana_HN1_HDB03 -inf: hana-s-mm
      sudo crm configure location loc_SAPHanaTop_not_on_majority_maker cln_SAPHanaTopology_HN1_HDB03 -inf: hana-s-mm
      ```

6. **[1]** konfigurera ytterligare kluster egenskaper   
    ```bash
    sudo crm configure rsc_defaults resource-stickiness=1000
    sudo crm configure rsc_defaults migration-threshold=50
    ```
7. **[1]** kontrol lera kommunikationen mellan hooken och klustret
    ```bash
    crm_attribute -G -n hana_hn1_glob_srHook
    # Expected result
    # crm_attribute -G -n hana_hn1_glob_srHook
    # scope=crm_config  name=hana_hn1_glob_srHook value=SOK
    ```

8. **[1]** placera klustret ur underhålls läge. Kontrol lera att klustrets status är OK och att alla resurser har startats.  
    ```bash
    # Cleanup any failed resources - the following command is example 
    crm resource cleanup rsc_SAPHana_HN1_HDB03

    # Place the cluster out of maintenance mode
    sudo crm configure property maintenance-mode=false
    ```
  
   > [!NOTE]
   > Tids gränsen i konfigurationen ovan är bara exempel och kan behöva anpassas till den angivna HANA-installationen. Du kan till exempel behöva öka tids gränsen för start, om det tar längre tid att starta SAP HANA databasen.
  

## <a name="test-sap-hana-failover"></a>Testa SAP HANA redundans 

> [!NOTE]
> Den här artikeln innehåller referenser till villkors *huvud* och *slav*, och villkor som Microsoft inte längre använder. När de här villkoren tas bort från program varan tar vi bort dem från den här artikeln.

1. Innan du startar ett test kontrollerar du klustrets och SAP HANA systemets replikeringsstatus.    

   a. Kontrol lera att det inte finns några misslyckade kluster åtgärder  
     ```bash
     #Verify that there are no failed cluster actions
     crm status
     # Example 
     #7 nodes configured
     #24 resource instances configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Full list of resources:
     #
     # stonith-sbd    (stonith:external/sbd): Started hana-s-mm
     # Clone Set: cln_fs_HN1_HDB03_fscheck [fs_HN1_HDB03_fscheck]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #     Stopped: [ hana-s-mm ]
     # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #     Stopped: [ hana-s-mm ]
     # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     #     Masters: [ hana-s1-db1 ]
     #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #     Stopped: [ hana-s-mm ]
     # Resource Group: g_ip_HN1_HDB03
     #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
     #     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hana-s1-db1
     ```

   b. Kontrol lera att SAP HANA system replikering är synkroniserat

     ```bash
     # Verify HANA HSR is in sync
     sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
     #| Database | Host         | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary    | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
     #|          |              |       |              |           |         |           | Host         | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
     #| -------- | ------------ | ----- | ------------ | --------- | ------- | --------- | ------------ | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
     #| SYSTEMDB | hana-s1-db1  | 30301 | nameserver   |         1 |       1 | HANA_S1   | hana-s2-db1  |     30301 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db1  | 30307 | xsengine     |         2 |       1 | HANA_S1   | hana-s2-db1  |     30307 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db1  | 30303 | indexserver  |         3 |       1 | HANA_S1   | hana-s2-db1  |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db3  | 30303 | indexserver  |         4 |       1 | HANA_S1   | hana-s2-db3  |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db2  | 30303 | indexserver  |         5 |       1 | HANA_S1   | hana-s2-db2  |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #
     #status system replication site "1": ACTIVE
     #overall system replication status: ACTIVE
     #
     #Local System Replication State
     #~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
     #
     #mode: PRIMARY
     #site id: 1
     #site name: HANA_S1
     ```

2. Vi rekommenderar att noggrant validera SAP HANA kluster konfigurationen genom att utföra testerna, som dokumenterats i [ha för SAP HANA på virtuella Azure-datorer på SLES](./sap-hana-high-availability.md#test-the-cluster-setup) och i [SLES för replikering av-replikering](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-scaleOut-PerfOpt-12/index.html#_testing_the_cluster).

3. Verifiera kluster konfigurationen för ett fel scenario, när en nod förlorar åtkomst till NFS-resursen ( `/hana/shared` ).  

   SAP HANA resurs agenter är beroende av binärfiler, lagrade på `/hana/shared` för att utföra åtgärder under redundansväxlingen. Fil systemet `/hana/shared` är monterat över NFS i den uppvisade konfigurationen. Ett test som kan utföras är att skapa en tillfällig brand Väggs regel för att blockera åtkomsten till `/hana/shared` ANF-volymen på en av de virtuella datorerna för den primära platsen. Den här metoden verifierar att klustret kommer att växlas över, om åtkomsten till går `/hana/shared` förlorad på den aktiva system replikerings platsen.  

   **Förväntat resultat**: när du blockerar åtkomsten till `/hana/shared` ANF-volymen på en av de virtuella datorerna på den primära platsen, Miss lyckas den övervaknings åtgärd som utför Läs-/skriv åtgärder i fil systemet, eftersom det inte går att komma åt fil systemet och kommer att utlösa Hana-resurs växling vid fel. Samma resultat förväntas när HANA-noden förlorar åtkomst till NFS-resursen.  
     
   Du kan kontrol lera status för kluster resurserna genom att köra `crm_mon` eller `crm status` . Resurs tillstånd innan du startar testet:
     ```bash
     # Output of crm_mon
     #7 nodes configured
     #24 resource instances configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Active resources:
     #
     #stonith-sbd     (stonith:external/sbd): Started hana-s-mm
     # Clone Set: cln_fs_HN1_HDB03_fscheck [fs_HN1_HDB03_fscheck]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     #     Masters: [ hana-s1-db1 ]
     #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Resource Group: g_ip_HN1_HDB03
     #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
     #     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hana-s2-db1     
      ```

   Om du vill simulera problem för ska du `/hana/shared` först bekräfta IP-adressen för `/hana/shared` ANF-volymen på den primära platsen. Det kan du göra genom att köra `df -kh|grep /hana/shared` . 
   Konfigurera sedan en tillfällig brand Väggs regel för att blockera åtkomst till IP-adressen för `/hana/shared` ANF-volymen genom att köra följande kommando på en av de primära virtuella Hana-systemreplikerings-platserna.
   I det här exemplet kördes kommandot på Hana-S1-DB1.

     ```bash
     iptables -A INPUT -s 10.23.1.7 -j DROP; iptables -A OUTPUT -d 10.23.1.7 -j DROP
     ```

   Kluster resurserna kommer att migreras till den andra HANA-systemreplikerings platsen.    
              
   Om du anger AUTOMATED_REGISTER = "false" måste du konfigurera SAP HANA system replikering på den sekundära platsen. I så fall kan du köra dessa kommandon för att konfigurera om SAP HANA som sekundär.   

     ```bash
     # Execute on the secondary 
     su - hn1adm
     # Make sure HANA is not running on the secondary site. If it is started, stop HANA
     sapcontrol -nr 03 -function StopWait 600 10
     # Register the HANA secondary site
     hdbnsutil -sr_register --name=HANA_S1 --remoteHost=hana-s2-db1 --remoteInstance=03 --replicationMode=sync
     # Switch back to root and cleanup failed resources
     crm resource cleanup SAPHana_HN1_HDB03
     ```

   Resursens tillstånd, efter testet: 

     ```bash
     # Output of crm_mon
     #7 nodes configured
     #24 resource instances configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Active resources:
     #
     #stonith-sbd     (stonith:external/sbd): Started hana-s-mm
     # Clone Set: cln_fs_HN1_HDB03_fscheck [fs_HN1_HDB03_fscheck]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     #     Masters: [ hana-s2-db1 ]
     #     Slaves: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db2 hana-s2-db3 ]
     # Resource Group: g_ip_HN1_HDB03
     #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
     #     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hana-s2-db1
     ```


## <a name="next-steps"></a>Nästa steg

* [Azure Virtual Machines planera och implementera SAP][planning-guide]
* [Azure Virtual Machines distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
* [NFS v4.1-volymer på Azure NetApp Files för SAP HANA](./hana-vm-operations-netapp.md)
* Information om hur du upprättar hög tillgänglighet och planerar för haveri beredskap för SAP HANA på virtuella Azure-datorer finns i [hög tillgänglighet för SAP HANA på Azure-Virtual Machines (VM)][sap-hana-ha].