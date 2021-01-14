---
title: Offentlig slut punkts anslutning för virtuella Azure-datorer&standard ILB i SAP HA-scenarier
description: Offentlig slut punkts anslutning för Virtual Machines med Azure Standard Load Balancer i SAP-scenarier med hög tillgänglighet
services: virtual-machines-windows,virtual-network,storage,
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: radeltch
ms.openlocfilehash: 525c705db81dc0f152c8dbc55de5ebac841e5d57
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201747"
---
# <a name="public-endpoint-connectivity-for-virtual-machines-using-azure-standard-load-balancer-in-sap-high-availability-scenarios"></a>Offentlig slut punkts anslutning för Virtual Machines med Azure Standard Load Balancer i SAP-scenarier med hög tillgänglighet

Omfånget för den här artikeln är att beskriva konfigurationer som kommer att aktivera utgående anslutning till offentliga slut punkter. Konfigurationerna är huvudsakligen i sammanhanget med hög tillgänglighet med pacemaker för SUSE/RHEL.  

Om du använder pacemaker med Azure stängsel-agenten i din lösning för hög tillgänglighet måste de virtuella datorerna ha utgående anslutning till Azures hanterings-API. Artikeln visar flera alternativ som gör att du kan välja det alternativ som passar bäst för ditt scenario.  

## <a name="overview"></a>Översikt

När du implementerar hög tillgänglighet för SAP-lösningar via klustring är en av de nödvändiga komponenterna [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md). Azure erbjuder två SKU: er för belastnings utjämning: standard och Basic.

Standard Azure Load Balancer erbjuder vissa fördelar jämfört med den grundläggande belastningsutjämnaren. Till exempel fungerar det över Azures tillgänglighets zoner och har bättre övervaknings-och loggnings funktioner för enklare fel sökning, kortare latens. Funktionen "HA portar" omfattar alla portar, det vill säga det är inte längre nödvändigt att lista alla enskilda portar.  

Det finns några viktiga skillnader mellan Basic-och standard-SKU: n för Azure Load Balancer. En av dem är hanteringen av utgående trafik till en offentlig slut punkt. För fullständiga bas-och standardsku: er för belastningsutjämnare, se [load BALANCER SKU-jämförelse](../../../load-balancer/load-balancer-overview.md).  
 
När virtuella datorer utan offentliga IP-adresser placeras i backend-poolen för interna (ingen offentlig IP-adress) standard Azure-belastningsutjämnare finns det ingen utgående anslutning till offentliga slut punkter, om inte ytterligare konfiguration görs.  

Om en virtuell dator tilldelas en offentlig IP-adress, eller om den virtuella datorn finns i backend-poolen för en belastningsutjämnare med en offentlig IP-adress, kommer den att ha utgående anslutning till offentliga slut punkter.  

SAP-system innehåller ofta känsliga affärs data. Det är sällan acceptabelt för virtuella datorer som är värdar för SAP-system som kan nås via offentliga IP-adresser. På samma gång finns det scenarier som kräver utgående anslutning från den virtuella datorn till offentliga slut punkter.  

Exempel på scenarier som kräver åtkomst till Azures offentliga slut punkt är:  
- Azure-stängsel-agenten kräver åtkomst till **Management.Azure.com** och **login.microsoftonline.com**  
- [Azure Backup](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#set-up-network-connectivity)
- [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-urls)  
- Använda offentliga lagrings platser för att korrigera operativ systemet
- Data flödet SAP-program kan kräva utgående anslutning till den offentliga slut punkten

Om din SAP-distribution inte kräver utgående anslutning till offentliga slut punkter, behöver du inte implementera den ytterligare konfigurationen. Det räcker att skapa interna standard-SKU-Azure Load Balancer för scenariot med hög tillgänglighet, förutsatt att det inte finns några behov av inkommande anslutningar från offentliga slut punkter.  

> [!Note]
> När virtuella datorer utan offentliga IP-adresser placeras i backend-poolen för intern (ingen offentlig IP-adress) standard Azure-belastningsutjämnare, kommer det inte att finnas någon utgående Internet anslutning, om inte ytterligare konfiguration utförs för att tillåta routning till offentliga slut punkter.  
>Om de virtuella datorerna har antingen offentliga IP-adresser eller redan finns i backend-poolen för Azure Load Balancer med offentlig IP-adress, kommer den virtuella datorn redan ha utgående anslutning till offentliga slut punkter.


Läs följande dokument först:

* Azure-Standard Load Balancer
  * [Översikt över azure standard Load Balancer](../../../load-balancer/load-balancer-overview.md) – omfattande översikt över Azure standard Load Balancer, viktiga principer, begrepp och självstudier 
  * [Utgående anslutningar i Azure](../../../load-balancer/load-balancer-outbound-connections.md#scenarios) – scenarier för hur du uppnår utgående anslutningar i Azure
  * [Utgående regler för belastnings utjämning](../../../load-balancer/load-balancer-outbound-connections.md#outboundrules)– förklarar begreppen utgående regler för belastningsutjämnare och hur du skapar utgående regler
* Azure Firewall
  * [Översikt över Azure Firewall](../../../firewall/overview.md)– översikt över Azure-brandväggen
  * [Självstudie: Distribuera och konfigurera Azure Firewall](../../../firewall/tutorial-firewall-deploy-portal.md) – instruktioner för hur du konfigurerar Azure firewall via Azure Portal
* [Virtuella nätverk – användardefinierade regler](../../../virtual-network/virtual-networks-udr-overview.md#user-defined) – Azure-routning – koncept och regler  
* [Säkerhets grupper tjänst Taggar](../../../virtual-network/network-security-groups-overview.md#service-tags) – hur du fören klar nätverks säkerhets grupperna och brand Väggs konfigurationen med service märken

## <a name="option-1-additional-external-azure-standard-load-balancer-for-outbound-connections-to-internet"></a>Alternativ 1: ytterligare extern Azure-Standard Load Balancer för utgående anslutningar till Internet

Ett alternativ för att få en utgående anslutning till offentliga slut punkter, utan att tillåta inkommande anslutning till den virtuella datorn från den offentliga slut punkten, är att skapa en andra belastningsutjämnare med offentlig IP-adress, lägga till de virtuella datorerna i backend-poolen för den andra belastningsutjämnaren och definiera enbart [utgående regler](../../../load-balancer/load-balancer-outbound-connections.md#outboundrules).  
Använd [nätverks säkerhets grupper](../../../virtual-network/network-security-groups-overview.md) för att kontrol lera de offentliga slut punkterna som är tillgängliga för utgående samtal från den virtuella datorn.  
Mer information finns i scenario 2 i dokument [utgående anslutningar](../../../load-balancer/load-balancer-outbound-connections.md#scenarios).  
Konfigurationen skulle se ut så här:  

![Kontrol lera anslutningen till offentliga slut punkter med nätverks säkerhets grupper](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-public.png)

### <a name="important-considerations"></a>Att tänka på

- Du kan använda ytterligare en offentlig Load Balancer för flera virtuella datorer i samma undernät för att få en utgående anslutning till den offentliga slut punkten och optimera kostnaden  
- Använd [nätverks säkerhets grupper](../../../virtual-network/network-security-groups-overview.md) för att styra vilka offentliga slut punkter som är tillgängliga från de virtuella datorerna. Du kan tilldela nätverks säkerhets gruppen antingen till under nätet eller till varje virtuell dator. Använd om möjligt [service märken](../../../virtual-network/network-security-groups-overview.md#service-tags) för att minska komplexiteten i säkerhets reglerna.  
- Azure standard Load Balancer med offentlig IP-adress och utgående regler ger direkt åtkomst till den offentliga slut punkten. Om du har företags säkerhets krav för all utgående trafik överföring via centraliserad företags lösning för granskning och loggning, kanske du inte kan uppfylla kraven i det här scenariot.  

>[!TIP]
>Använd om möjligt [service märken](../../../virtual-network/network-security-groups-overview.md#service-tags) för att minska komplexiteten i nätverks säkerhets gruppen. 

### <a name="deployment-steps"></a>Distributionssteg

1. Skapa Load Balancer  
   1. Klicka på alla resurser i [Azure Portal](https://portal.azure.com) , Lägg till och Sök sedan efter **Load Balancer**  
   1. Klicka på **Skapa** 
   1. Load Balancer namn **MyPublicILB**  
   1. Välj **offentlig** som en typ, **standard** som SKU  
   1. Välj **skapa offentlig IP-adress** och ange som namn **MyPublicILBFrondEndIP**  
   1. Välj **zonen är redundant** som tillgänglighets zon  
   1. Klicka på granska och skapa och klicka sedan på skapa  
2. Skapa **MyBackendPoolOfPublicILB** för backend-poolen och Lägg till de virtuella datorerna.  
   1. Välj det virtuella nätverket  
   1. Välj de virtuella datorerna och deras IP-adresser och Lägg till dem i backend-poolen  
3. [Skapa utgående regler](../../../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard%3ftabs%3doption-1-create-load-balancer-standard#create-outbound-rule-configuration). Det går för närvarande inte att skapa utgående regler från Azure Portal. Du kan skapa utgående regler med [Azure CLI](../../../cloud-shell/overview.md).  

   ```azurecli
    az network lb outbound-rule create --address-pool MyBackendPoolOfPublicILB --frontend-ip-configs MyPublicILBFrondEndIP --idle-timeout 30 --lb-name MyPublicILB --name MyOutBoundRules  --outbound-ports 10000 --enable-tcp-reset true --protocol All --resource-group MyResourceGroup
   ```

4. Skapa regler för nätverks säkerhets grupper för att begränsa åtkomsten till särskilda offentliga slut punkter. Om det finns en befintlig nätverks säkerhets grupp kan du justera den. Exemplet nedan visar hur du aktiverar åtkomst till Azures hanterings-API: 
   1. Navigera till nätverks säkerhets gruppen
   1. Klicka på utgående säkerhets regler
   1. Lägg till en regel för att **neka** all utgående åtkomst till **Internet**.
   1. Lägg till en regel för att **tillåta** åtkomst till **AzureCloud**, med prioritet som är lägre än regelns prioritet för att neka all Internet åtkomst.


   Utgående säkerhets regler skulle se ut så här: 

   ![Utgående anslutning med andra Load Balancer med offentlig IP](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-network-security-groups.png)

   Mer information om nätverks säkerhets grupper i Azure finns i [säkerhets grupper ](../../../virtual-network/network-security-groups-overview.md). 

## <a name="option-2-azure-firewall-for-outbound-connections-to-internet"></a>Alternativ 2: Azure-brandvägg för utgående anslutningar till Internet

Ett annat alternativ för att få en utgående anslutning till offentliga slut punkter, utan att tillåta inkommande anslutning till den virtuella datorn från offentliga slut punkter, är med Azure-brandväggen. Azure-brandväggen är en hanterad tjänst med inbyggd hög tillgänglighet och kan omfatta flera Tillgänglighetszoner.  
Du måste också distribuera en [användardefinierad väg](../../../virtual-network/virtual-networks-udr-overview.md#custom-routes)som är associerad med ett undernät där virtuella datorer och Azure Load Balancer distribueras, peka på Azure-brandväggen för att dirigera trafik via Azure-brandväggen.  
Mer information om hur du distribuerar Azure-brandväggen finns i [distribuera och konfigurera Azure](../../../firewall/tutorial-firewall-deploy-portal.md)-brandväggen.  

Arkitekturen skulle se ut så här:

![Utgående anslutning med Azure-brandvägg](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall.png)

### <a name="important-considerations"></a>Att tänka på

- Azure-brandväggen är en molnbaserad inbyggd tjänst med inbyggd hög tillgänglighet och har stöd för zonindelade-distribution.
- Kräver ytterligare ett undernät som måste ha namnet AzureFirewallSubnet. 
- Om du överför stora data uppsättningar utgående från det virtuella nätverket där de virtuella SAP-datorerna finns, till en virtuell dator i ett annat virtuellt nätverk eller till en offentlig slut punkt, kanske det inte är en kostnads effektiv lösning. Ett exempel är att kopiera stora säkerhets kopior i virtuella nätverk. Mer information finns i priser för Azure-brandvägg.  
- Om företags brand Väggs lösningen inte är Azure-brandvägg och du har säkerhets krav för all utgående trafik, även om den centraliserade företags lösningen, är den här lösningen kanske inte praktisk.  

>[!TIP]
>Använd om möjligt [service märken](../../../virtual-network/network-security-groups-overview.md#service-tags) för att minska komplexiteten i Azures brand Väggs regler.  

### <a name="deployment-steps"></a>Distributionssteg

1. Distributions stegen förutsätter att du redan har ett virtuellt nätverk och ett undernät definierat för dina virtuella datorer.  
2. Skapa undernäts- **AzureFirewallSubnet** i samma Virtual Network, där de virtuella datorerna och standard Load Balancer distribueras.  
   1. I Azure Portal navigerar du till Virtual Network: Klicka på alla resurser, Sök efter Virtual Network, klicka på Virtual Network, Välj undernät.  
   1. Klicka på Lägg till undernät. Ange **AzureFirewallSubnet** som namn. Ange lämpligt adress intervall. Spara.  
3. Skapa Azure-brandvägg.  
   1. I Azure Portal väljer du alla resurser klickar du på Lägg till, brand vägg, skapa. Välj resurs grupp (Välj samma resurs grupp där Virtual Network är).  
   1. Ange ett namn för Azure Firewall-resursen. Till exempel **MyAzureFirewall**.  
   1. Välj region och välj minst två tillgänglighets zoner, justerade med de tillgänglighets zoner där de virtuella datorerna distribueras.  
   1. Välj din Virtual Network, där de virtuella SAP-datorerna och Azure standard Load Balancer distribueras.  
   1. Offentlig IP-adress: Klicka på Skapa och ange ett namn. Till exempel **MyFirewallPublicIP**.  
4. Skapa en Azure Firewall-regel för att tillåta utgående anslutning till angivna offentliga slut punkter. Exemplet visar hur du tillåter åtkomst till den offentliga slut punkten för Azure Management API.  
   1. Välj regler, nätverks regel samling och klicka sedan på Lägg till regel samling för nätverk.  
   1. Namn: **MyOutboundRule**, ange prioritet, Välj åtgärden **Tillåt**.  
   1. Tjänst: name **ToAzureAPI**.  Protokoll: Välj **valfritt**. Käll adress: Ange intervallet för under nätet där de virtuella datorerna och Standard Load Balancer distribueras till exempel: **11.97.0.0/24**. Mål portar: ange <b>*</b> .  
   1. Spara
   1. När du fortfarande är placerad i Azure-brandväggen väljer du Översikt. Anteckna den privata IP-adressen för Azure-brandväggen.  
5. Skapa väg till Azure-brandvägg  
   1. I Azure Portal väljer du alla resurser och klickar sedan på Lägg till, routningstabell, skapa.  
   1. Ange namn MyRouteTable, Välj prenumeration, resurs grupp och plats (matchar platsen för ditt virtuella nätverk och brand vägg).  
   1. Spara  

   Brand Väggs regeln skulle se ut så här: ![ diagram som visar vad brand väggen skulle se ut.](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall-rule.png)

6. Skapa användardefinierad väg från under nätet för dina virtuella datorer till den privata IP-adressen för **MyAzureFirewall**.
   1. När du är placerad i routningstabellen klickar du på vägar. Välj Lägg till. 
   1. Väg namn: ToMyAzureFirewall, adressprefix: **0.0.0.0/0**. Typ av nästa hopp: Välj virtuell installation. Adress till nästa hopp: Ange den privata IP-adressen för den brand vägg som du konfigurerade: **11.97.1.4**.  
   1. Spara

## <a name="option-3-using-proxy-for-pacemaker-calls-to-azure-management-api"></a>Alternativ 3: använda proxy för pacemaker-anrop till Azure Management API

Du kan använda proxy för att tillåta pacemaker-anrop till den offentliga slut punkten för Azure Management API.  

### <a name="important-considerations"></a>Att tänka på

  - Om det redan finns företags-proxy på plats kan du dirigera utgående anrop till offentliga slut punkter via den. Utgående anrop till offentliga slut punkter hamnar i företags kontroll punkten.  
  - Se till att proxykonfigurationen tillåter utgående anslutning till Azure Management API: `https://management.azure.com` och `https://login.microsoftonline.com`  
  - Se till att det finns en väg från de virtuella datorerna till proxyn  
  - Proxyn hanterar endast HTTP/HTTPS-anrop. Om det finns ytterligare behov att göra utgående samtal till en offentlig slut punkt över olika protokoll (som RFC) behövs en alternativ lösning  
  - Proxyservern måste ha hög tillgänglighet för att undvika instabilitet i pacemaker-klustret  
  - Beroende på var proxyservern finns kan den införa ytterligare svars tid i anropen från Azure-stängsel-agenten till Azure Management-API: et. Om din företags proxy fortfarande finns lokalt, medan ditt pacemaker-kluster finns i Azure, kan du mäta svars tider och överväga om den här lösningen passar dig  
  - Om det inte redan finns en företags proxyserver med hög tillgänglighet rekommenderar vi inte det här alternativet eftersom kunden skulle ta extra kostnad och komplexitet. Om du däremot bestämmer dig för att distribuera ytterligare en proxyserver, för att tillåta utgående anslutning från pacemaker till en offentlig Azure Management-API, kontrollerar du att proxyn har hög tillgänglighet och att svars tiden från de virtuella datorerna till proxyn är låg.  

### <a name="pacemaker-configuration-with-proxy"></a>Pacemaker-konfiguration med proxy 

Det finns många olika proxy-alternativ i branschen. Stegvisa instruktioner för proxy-distributionen ligger utanför det här dokumentets omfattning. I exemplet nedan förutsätter vi att din proxy svarar på **MyProxyService** och lyssnar på port **MyProxyPort**.  
Om du vill tillåta att pacemaker kommunicerar med Azures hanterings-API utför du följande steg på alla noder i klustret:  

1. Redigera konfigurations filen för pacemaker/etc/sysconfig/pacemaker och Lägg till följande rader (alla klusternoder):

   ```console
   sudo vi /etc/sysconfig/pacemaker
   # Add the following lines
   http_proxy=http://MyProxyService:MyProxyPort
   https_proxy=http://MyProxyService:MyProxyPort
   ```

2. Starta om pacemaker-tjänsten på **alla** klusternoder.  
  - SUSE
 
     ```console
     # Place the cluster in maintenance mode
     sudo crm configure property maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo crm configure property maintenance-mode=true
     ```

  - Red Hat  

     ```console
     # Place the cluster in maintenance mode
     sudo pcs property set maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo pcs property set maintenance-mode=false
     ```

## <a name="other-options"></a>Andra alternativ

Om utgående trafik dirigeras via tredje part, URL-baserad brand Väggs-proxy:

- Om du använder Azure stängsel-agenten kontrollerar du att brand Väggs konfigurationen tillåter utgående anslutning till Azure Management API: `https://management.azure.com` och `https://login.microsoftonline.com`   
- Om du använder uppdaterings infrastrukturen för Azures offentliga Azure-moln för att tillämpa uppdateringar och uppdateringar, se [Azures offentliga moln uppdaterings infrastruktur 101](https://suse.com/c/azure-public-cloud-update-infrastructure-101/)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du konfigurerar pacemaker på SUSE i Azure](./high-availability-guide-suse-pacemaker.md)
* [Lär dig hur du konfigurerar pacemaker i Red Hat i Azure](./high-availability-guide-rhel-pacemaker.md)