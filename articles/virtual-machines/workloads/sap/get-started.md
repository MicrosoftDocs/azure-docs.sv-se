---
title: Kom igång med SAP på virtuella Azure-datorer | Microsoft Docs
description: Lär dig mer om SAP-lösningar som körs på virtuella datorer (VM) i Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/17/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5cee26f9c1674030903e8be6dac80f6615317a67
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551675"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Använd Azure för att vara värd för och köra SAP-arbetsbelastnings scenarier

När du använder Microsoft Azure kan du på ett tillförlitligt sätt köra dina verksamhets kritiska SAP-arbetsbelastningar och scenarier på en skalbar, kompatibel och företags beprövad plattform. Du får skalbarhet, flexibilitet och kostnads besparingar i Azure. Med det utökade partnerskapet mellan Microsoft och SAP kan du köra SAP-program över utvecklings-och testnings-och produktions scenarier i Azure och få fullständig support. Från SAP NetWeaver till SAP S/4HANA, SAP BI på Linux till Windows och SAP HANA till SQL, har vi lärt dig.

Förutom att vara värd för SAP NetWeaver-scenarier med olika DBMS i Azure kan du vara värd för andra SAP-arbetsbelastnings scenarier som SAP BI på Azure. 

Azures unikhet för SAP HANA är ett erbjudande som ställer in Azure. För att kunna vara värd för mer minne och processor resurs krävande SAP-scenarier som omfattar SAP HANA, erbjuder Azure användningen av kunddedikerat Bare Metal-maskinvara. Använd den här lösningen för att köra SAP HANA distributioner som kräver upp till 24 TB (120 TB skalbart) minne för S/4HANA eller annan SAP HANA arbets belastning. 

Att vara värd för SAP-arbetsbelastnings scenarier i Azure kan också skapa krav på identitets integrering och enkel inloggning. Den här situationen kan inträffa när du använder Azure Active Directory (Azure AD) för att ansluta olika SAP-komponenter och SAP-SaaS (Software-as-a-Service) eller PaaS-erbjudanden (Platform-as-a-Service). En lista över scenarier för integration och enkel inloggning med Azure AD och SAP-entiteter beskrivs och dokumenteras i avsnittet "AAD SAP Identity Integration och enkel inloggning".

## <a name="changes-to-the-sap-workload-section"></a>Ändringar i avsnittet SAP-arbetsbelastning
Ändringar av dokument i avsnittet SAP på Azure-arbetsbelastningar visas i slutet av den här artikeln. Posterna i ändrings loggen sparas i cirka 180 dagar.

## <a name="you-want-to-know"></a>Du vill veta
Om du har vissa frågor ska vi peka dig på vissa dokument eller flöden i det här avsnittet på Start sidan. Du vill veta:

- Det finns stöd för de virtuella Azure-datorer och HANA-instanser som stöds för vilka SAP-program versioner och vilka operativ system versioner som används. Läs dokumentet [vilka SAP-program som stöds för Azure-distribution](./sap-supported-product-on-azure.md) för svar och processen för att hitta informationen
- Vilka SAP-distributions scenarier som stöds med virtuella Azure-datorer och HANA-stora instanser. Information om de scenarier som stöds finns i dokumenten:
    - [SAP-arbetsbelastning på en virtuell Azure-dator – scenarier som stöds](./sap-planning-supported-configurations.md)
    - [Scenarier som stöds för HANA stor instans](./hana-supported-scenario.md)
- Vilka Azure-tjänster, Azure VM-typer och Azure Storage-tjänster är tillgängliga i olika Azure-regioner, kontrollerar du de plats [produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/) 
- Fungerar tredje part HA-ramen, förutom Windows och pacemaker som stöds? Kontrol lera den nedre delen av [SAP support note #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- Vad Azure Storage passar bäst för mitt scenario? Läs [Azure Storage typer för SAP-arbetsbelastningar](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide-storage)

 
## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA på Azure (stora instanser)

En serie dokument vägleder dig genom SAP HANA på Azure (stora instanser) eller för korta, HANA stora instanser. För information om HANA-stora instanser börjar du med dokument [översikten och arkitekturen i SAP HANA på Azure (stora instanser)](./hana-overview-architecture.md) och går igenom den relaterade dokumentationen i avsnittet Hana stor instans



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA på virtuella Azure-datorer
I det här avsnittet av dokumentationen beskrivs olika aspekter av SAP HANA. Som en förutsättning bör du vara bekant med huvud tjänsterna för Azure som tillhandahåller grundläggande tjänster för Azure IaaS. Så du behöver kunskap om Azure Compute, Storage och Networking. Många av dessa ämnen hanteras i SAP NetWeaver-relaterade [Azure Planning Guide](./planning-guide.md). 

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP-NetWeaver distribueras på virtuella Azure-datorer
Det här avsnittet innehåller planerings-och distributions dokumentation för SAP NetWeaver, SAP LaMa och Business One på Azure. Dokumentationen fokuserar på grunderna och användningen av icke-HANA-databaser med en SAP-arbetsbelastning på Azure. Dokument och artiklar för hög tillgänglighet är också grunden för SAP HANA hög tillgänglighet i Azure

Information om hög tillgänglighet för en SAP-arbetsbelastning i Azure finns i:

- [Azure Virtual Machines hög tillgänglighet för SAP NetWeaver](./sap-high-availability-guide-start.md)



Information om integration mellan Azure Active Directory (Azure AD) och SAP-tjänster och enkel inloggning finns i:

- [Självstudie: Azure Active Directory integration med SAP Cloud för kunden](../../../active-directory/saas-apps/sap-customer-cloud-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Självstudie: Azure Active Directory integration med SAP Cloud Platform Identity Authentication](../../../active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Självstudie: Azure Active Directory integration med SAP Cloud Platform](../../../active-directory/saas-apps/sap-hana-cloud-platform-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Självstudier: Azure Active Directory-integration med SAP NetWeaver](../../../active-directory/saas-apps/sap-netweaver-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Självstudier: Azure Active Directory-integration med SAP Business ByDesign](../../../active-directory/saas-apps/sapbusinessbydesign-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Självstudie: Azure Active Directory integration med SAP HANA](../../../active-directory/saas-apps/saphana-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Din S/4HANA-miljö: Fiori starter SAML enkel inloggning med Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Information om hur du integrerar Azure-tjänster i SAP-komponenter finns i:

- [Använda SAP HANA i Power BI Desktop](/power-bi/desktop-sap-hana)
- [DirectQuery och SAP HANA](/power-bi/desktop-directquery-sap-hana)
- [Använda SAP BW-anslutningsappen i Power BI Desktop](/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory erbjuder dataintegrering för SAP HANA och Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>Ändrings logg

- 08/17/2020: Lägg till information om hur du använder Azure Site Recovery för att flytta SAP NetWeaver-system från lokala datorer till Azure i artikel [azure Virtual Machines planera och implementera för SAP NetWeaver](./planning-guide.md)
- 08/14/2020: lägga till disk konfigurations rådgivning för DB2 i artikel [IBM DB2 Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- 08/11/2020: lägger till RHEL 7,6 i [kompatibla operativ system för Hana-stora instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-compatibility-matrix-hana-large-instance) som tillgängligt operativ system för HLI-enheter av typen i
- 08/10/2020: Vi presenterar kostnads medveten SAP HANA lagrings konfiguration i [SAP HANA konfigurationer för virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) och gör vissa uppdateringar av [SAP-arbetsbelastningar på Azure: planering och distribution check lista](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- 08/04/2020: ändra i [ställa in pacemaker på SLES i Azure](./high-availability-guide-suse-pacemaker.md) och konfigurera [pacemaker på RHEL i Azure](./high-availability-guide-rhel-pacemaker.md) för att betona vikten av tillförlitlig namn matchning för pacemaker-kluster
- 08/04/2020: ändra i [SAP NW ha på WFCS med fil resurs](./sap-high-availability-installation-wsfc-file-share.md), [SAP NW-ha på WFCS med delad disk](./sap-high-availability-installation-wsfc-shared-disk.md), [ha för SAP NW på virtuella Azure-datorer](./high-availability-guide.md), [ha för SAP NW på Azure VM](./high-availability-guide-suse.md): ar på SLES, ha [för SAP NW på virtuella Azure-datorer på SLES med ANF](./high-availability-guide-suse-netapp-files.md), [ha för SAP NW på virtuella Azure-datorer på SLES multi-sid-guide](./high-availability-guide-suse-multi-sid.md), [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på RHEL](./high-availability-guide-rhel.md), [ha för SAP NW på virtuella Azure-datorer på RHEL med ANF](./high-availability-guide-rhel-netapp-files.md) och ha för SAP NW på virtuella Azure-datorer i RHEL med och [ha för SAP NW på multi](./high-availability-guide-rhel-multi-sid.md)`enque/encni/set_so_keepalive`
- 07/23/2020: lade till [Spara på SAP HANA – stora instanser med en Azure reservation](../../../cost-management-billing/reservations/prepay-hana-large-instances-reserved-capacity.md) -artikel som förklarar vad du behöver veta innan du köper en SAP HANA – stora instanser reservation och hur du gör köpet
- 07/16/2020: beskriver hur du använder Azure PowerShell för att installera nytt VM-tillägg för SAP i [distributions guiden](deployment-guide.md)
- 7/04/2020: version av  [Azure Monitor för SAP-lösningar (för hands version)](./azure-monitor-overview.md)
- 07/01/2020: föreslår billigare lagrings konfiguration baserat på Azure Premium Storage burst-funktioner i dokument [SAP HANA lagrings konfiguration för virtuella Azure-datorer](./hana-vm-operations-storage.md) 
- 06/24/2020: ändra i [ställa in pacemaker på SLES i Azure](./high-availability-guide-suse-pacemaker.md) för att frigöra ny förbättrad Azure-stängsel och mer elastisk STONITH-konfiguration för enheter, baserat på Azure stängsel-agenten 
- 06/24/2020: ändra i [ställa in pacemaker på RHEL i Azure](./high-availability-guide-rhel-pacemaker.md) för att frigöra mer elastisk STONITH-konfiguration
- 06/23/2020: ändringar i [Azure Virtual Machines planering och implementering för SAP NetWeaver](./planning-guide.md) -guide och introduktion av [Azure Storage typer för SAP-arbetsbelastnings](./planning-guide-storage.md) guide
- 06/22/2020: Lägg till installations steg för nytt VM-tillägg för SAP i [distributions guiden](deployment-guide.md)
- 06/16/2020: ändring i [offentlig slut punkts anslutning för virtuella datorer med Azure standard-ILB i SAP ha-scenarier](./high-availability-guide-standard-load-balancer-outbound-connections.md) för att lägga till en länk till SUSE offentlig Cloud infrastructure 101 dokumentation 
- 06/10/2020: lägga till nya HLI SKU: er i [tillgängliga SKU: er för HLI](./hana-available-skus.md) och [SAP HANA (stora instanser) lagrings arkitektur](./hana-storage-architecture.md)
- 05/21/2020: ändra i [ställa in pacemaker på SLES i Azure](./high-availability-guide-suse-pacemaker.md) och konfigurera [pacemaker på RHEL i Azure](./high-availability-guide-rhel-pacemaker.md) för att lägga till en länk till en [offentlig slut punkts anslutning för virtuella datorer med Azure standard ILB i SAP ha-scenarier](./high-availability-guide-standard-load-balancer-outbound-connections.md)  
- 05/19/2020: Lägg till viktigt meddelande för att inte använda rot volym gruppen när du använder LVM för HANA-relaterade volymer i [SAP HANA Storage-konfigurationer för virtuella Azure-datorer](./hana-vm-operations-storage.md)
- 05/19/2020: Lägg till ett nytt operativ system som stöds för HANA stor instans typ II i [kompatibla operativ system för Hana-stora instanser](/- azure/virtual-machines/workloads/sap/os-compatibility-matrix-hana-large-instance)
- 05/12/2020: ändring i [offentlig slut punkts anslutning för virtuella datorer med Azure standard-ILB i SAP ha-scenarier](./high-availability-guide-standard-load-balancer-outbound-connections.md) för att uppdatera länkar och lägga till information för brand Väggs konfiguration från tredje part
- 05/11/2020: ändra [hög tillgänglighet för SAP HANA på virtuella Azure-datorer på SLES](./sap-hana-high-availability.md) för att ange Resource varaktighet till 0 för resursen netcat, som leder till mer strömlinjeformad redundans 
- 05/05/2020: ändringar i [Azure Virtual Machines planering och implementering för SAP NetWeaver](./planning-guide.md) för att uttrycka att Gen2-distributioner är tillgängliga för virtuella datorer i Mv1-serien
- 04/24/2020: ändringar i [SAP HANA skalas ut med noden vänte läge på virtuella Azure-datorer med ANF på SLES](./sap-hana-scale-out-standby-netapp-files-suse.md), i [SAP HANA skala ut med noden vänte läge på virtuella Azure-datorer med ANF på RHEL](./sap-hana-scale-out-standby-netapp-files-rhel.md), [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer i SLES med ANF](./high-availability-guide-suse-netapp-files.md) och [hög tillgänglighet för SAP NetWeaver på virtuella](./high-availability-guide-rhel-netapp-files.md) Azure-datorer i RHEL med ANF för att lägga till information om att IP-
- 04/22/2020: ändra [hög tillgänglighet för SAP HANA på virtuella Azure-datorer på SLES](./sap-hana-high-availability.md) för att ta bort meta-attributet `is-managed` från instruktionerna, eftersom det står i konflikt med att placera klustret i eller ur underhålls läge
- 04/21/2020: lade till SQL Azure DB som stöd för DBMS för SAP (Hybris) Commerce Platform 1811 och senare i artiklar [vilka SAP-program som stöds för Azure-distributioner](./sap-supported-product-on-azure.md) och [SAP-certifieringar och konfigurationer som körs på Microsoft Azure](./sap-certifications.md)
- 04/16/2020: har lagts till SAP HANA as-DBMS som stöds för SAP (Hybris) Commerce Platform i artiklar [vilka SAP-program som stöds för Azure-distributioner](./sap-supported-product-on-azure.md) och [SAP-certifieringar och konfigurationer som körs på Microsoft Azure](./sap-certifications.md)
- 04/13/2020: korrigera till exakta SAP ASE-release-nummer i [SAP ASE Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastningar](./dbms_guide_sapase.md)
- 04/07/2020: ändra i [ställa in pacemaker på SLES i Azure](./high-availability-guide-suse-pacemaker.md) för att klargöra Cloud-netconfig-Azure-instruktioner
- 04/06/2020: ändringar i [SAP HANA skalas ut med noden vänte läge på virtuella Azure-datorer med Azure NetApp Files på SLES](./sap-hana-scale-out-standby-netapp-files-suse.md) och i [SAP HANA skala ut med noden vänte läge på virtuella Azure-datorer med Azure NetApp Files på RHEL](./sap-hana-scale-out-standby-netapp-files-rhel.md) för att ta bort referenser till NetApp [tr-4435](https://www.netapp.com/us/media/tr-4746.pdf) (ersätts av [tr-4746](https://www.netapp.com/us/media/tr-4746.pdf))
- 31 mars 2020: ändra [hög tillgänglighet för SAP HANA på virtuella Azure-datorer på SLES](./sap-hana-high-availability.md) och [hög tillgänglighet för SAP HANA på virtuella Azure-datorer på RHEL](./sap-hana-high-availability-rhel.md) för att lägga till instruktioner för hur du anger stripe-storlek när du skapar stripe-volymer
- 27 mars 2020: ändring i [hög tillgänglighet för SAP NW på virtuella Azure-datorer på SLES med ANF för SAP-program](./high-availability-guide-suse-netapp-files.md) för att justera fil systemets monterings alternativ till NetApp TR-4746 (ta bort alternativet Sync-montering)
- 26 mars 2020: ändring i [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SLES multi-sid-guide](./high-availability-guide-suse-multi-sid.md) för att lägga till referens till NetApp TR-4746
- 26 mars 2020: ändring i [hög tillgänglighet för SAP-NetWeaver på virtuella Azure-datorer på SLES för SAP-program](./high-availability-guide-suse.md), [hög tillgänglighet för SAP-NetWeaver på virtuella Azure-datorer på SLES med Azure NetApp Files för SAP-program](./high-availability-guide-suse-netapp-files.md), [hög tillgänglighet för NFS på virtuella Azure-datorer på SLES](./high-availability-guide-suse-nfs.md), [hög tillgänglighet för SAP NETWEAVER på virtuella Azure-datorer på RHEL multi-sid-guide](./high-availability-guide-suse-multi-sid.md), [hög tillgänglighet för SAP NETWEAVER på virtuella Azure-datorer på RHEL för SAP-program](./high-availability-guide-rhel.md) och [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer i RHEL med Azure NetApp Files för](./high-availability-guide-rhel-netapp-files.md) SAP-program för att uppdatera diagram och för tydliga instruktioner för att Azure Load Balancer-adresspoolen skapas
- 19 mars 2020: större revidering av [snabb start för dokument: manuell installation av en instans SAP HANA på azure Virtual Machines](./hana-get-started.md) för att [Installera SAP HANA på Azure Virtual Machines](./hana-get-started.md)
- 17 mars 2020: ändra konfigurations inställningar [för pacemaker på SUSE Linux Enterprise Server i Azure](./high-availability-guide-suse-pacemaker.md) för att ta bort SBD-konfigurationsfilen som inte längre behövs
- Mars 16 2020: klargörande av kolumn certifierings scenario i SAP HANA IaaS Certified Platform i [vilken SAP-program vara stöds för Azure-distributioner](./sap-supported-product-on-azure.md)
- 03/11/2020: ändring i [SAP-arbetsbelastning i scenarier med virtuella Azure-datorer som stöds](./sap-planning-supported-configurations.md) för att klargöra flera databaser per instans stöd för DBMS
- 11 mars 2020: ändring i [Azure Virtual Machines planering och implementering för SAP NetWeaver](./planning-guide.md) som förklarar generation 1 och generation 2 virtuella datorer
- 10 mars 2020: ändra i [SAP HANA lagrings konfiguration för virtuella Azure-datorer](./hana-vm-operations-storage.md) för att klargöra verkliga befintliga data flödes gränser för ANF
- 9 mars 2020: ändring i [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program](./high-availability-guide-suse.md), [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server med Azure NetApp Files för SAP-program](./high-availability-guide-suse-netapp-files.md), [hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md), [Konfigurera pacemaker på SUSE Linux Enterprise Server i Azure](./high-availability-guide-suse-pacemaker.md), [hög tillgänglighet för IBM DB2 LUW på virtuella Azure-datorer på SUSE Linux Enterprise Server med pacemaker](./dbms-guide-ha-ibm.md), [hög tillgänglighet för SAP HANA på virtuella Azure-datorer med SUSE Linux Enterprise Server](./sap-hana-high-availability.md) och [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SLES multi-sid-guide](./high-availability-guide-suse-multi-sid.md) för att uppdatera kluster resurser med Resource agent Azure-lb 
- Den 05 mars 2020: struktur ändringar och innehålls ändringar för Azure-regioner och Azure Virtual Machines i [azure Virtual Machines planera och implementera för SAP NetWeaver](./planning-guide.md)
- 03/03/2020: ändring i [hög tillgänglighet för SAP NW på virtuella Azure-datorer på SLES med ANF för SAP-program](./high-availability-guide-suse-netapp-files.md) för att ändra till mer effektiv ANF
- 01 mars 2020: en [återhands säkerhets kopierings guide för SAP HANA på Azure Virtual Machines](./sap-hana-backup-guide.md) att inkludera Azure Backup-tjänsten. Minska och kondenserat innehåll i [SAP HANA Azure Backup på filnivå](./sap-hana-backup-file-level.md) och ta bort ett tredje dokument som hanterar säkerhets kopiering genom disk ögonblicks bilder. Innehållet hanteras i säkerhets kopierings guiden för SAP HANA på Azure Virtual Machines 
- 27 februari 2020: ändring i [hög tillgänglighet för SAP NW på virtuella Azure-datorer på SLES för SAP-program](./high-availability-guide-suse.md), [hög tillgänglighet för SAP NW på virtuella Azure-datorer på SLES med ANF för SAP-program](./high-availability-guide-suse-netapp-files.md) och [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SLES multi-sid-guide](./high-availability-guide-suse-multi-sid.md) för att justera "On failover"-kluster parametern
- 26 februari 2020: ändra i [SAP HANA lagrings konfiguration för Azure virtuella datorer](./hana-vm-operations-storage.md) för att klargör fil system val för Hana på Azure
- 26 februari 2020: ändring av [arkitektur och scenarier med hög tillgänglighet för SAP](./sap-high-availability-architecture-scenarios.md) för att inkludera länken till ha för SAP NetWeaver på virtuella Azure-datorer på RHEL multi-sid-guide
- 26 februari 2020: ändra hög [tillgänglighet för SAP NW på virtuella Azure-datorer på SLES för SAP-program](./high-availability-guide-suse.md), [hög tillgänglighet för SAP NW på virtuella Azure-datorer på SLES med ANF för SAP-program](./high-availability-guide-suse-netapp-files.md), [Azure VM hög tillgänglighet för SAP NetWeaver på RHEL](./high-availability-guide-rhel.md) och [virtuella Azure-datorer hög tillgänglighet för SAP NetWeaver på RHEL med Azure NetApp Files](./high-availability-guide-rhel-netapp-files.md) för att ta bort instruktionen att flera-sid ASCS/ers-kluster inte
- 26 februari 2020: utgåva av  [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på RHEL med multi-sid-guide](./high-availability-guide-rhel-multi-sid.md) för att lägga till en länk till SUSE multi-sid-kluster guiden
- 02/25/2020: ändra i [arkitektur och scenarier med hög tillgänglighet för SAP](./sap-high-availability-architecture-scenarios.md) för att lägga till länkar till nya artiklar i ha
- 25 februari 2020: ändring i [hög tillgänglighet för IBM DB2-LUW på virtuella Azure-datorer på SUSE Linux Enterprise Server med pacemaker](./dbms-guide-ha-ibm.md) för att peka på dokument som beskriver åtkomst till den offentliga slut punkten med standard Azure Load Balancer
- 21 februari 2020: fullständig revidering av artikeln [SAP ASE Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning](./dbms_guide_sapase.md)
- 21 februari 2020: ändra i [SAP HANA lagrings konfiguration för Azure virtuell dator](./hana-vm-operations-storage.md) för att representera ny rekommendation i stripe-storlek för/Hana/data och lägga till inställningen för i/O Scheduler
- 21 februari 2020: ändringar i HANA-stora instans dokument som representerar nyligen certifierade SKU: er av S224 och S224m
- 21 februari 2020: ändring av de virtuella Azure-datorerna med [hög tillgänglighet för SAP NetWeaver på RHEL](./high-availability-guide-rhel.md) och [virtuella Azure-datorer med hög tillgänglighet för SAP NetWeaver på RHEL med Azure NetApp Files](./high-availability-guide-rhel-netapp-files.md) för att justera kluster begränsningarna för att köa Server replikering 2-arkitekturen (ENSA2)
- 20 februari 2020: ändring i [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SLES multi-sid-guide](./high-availability-guide-suse-multi-sid.md) för att lägga till en länk till SUSE multi-sid-kluster guiden
- 13 februari 2020: ändringar i [Azure Virtual Machines planering och implementering för SAP-NetWeaver](./planning-guide.md) för att implementera länkar till nya dokument
- 13 februari 2020: nya dokument SAP- [arbetsbelastningar har lagts till i Azure Virtual Machine support scenario](./sap-planning-supported-configurations.md)
- 13 februari 2020: har lagt till det nya dokumentet [vilka SAP-program som stöds för Azure-distribution](./sap-supported-product-on-azure.md)
- 13 februari 2020: ändring i [hög tillgänglighet för IBM DB2-LUW på virtuella Azure-datorer på Red Hat Enterprise Linux server](./high-availability-guide-rhel-ibm-db2-luw.md) för att peka på dokument som beskriver åtkomst till den offentliga slut punkten med Azures standard-belastningsutjämnare
- 13 februari 2020: Lägg till de nya VM-typerna i [SAP-certifieringar och konfigurationer som körs på Microsoft Azure](./sap-certifications.md)
- 13 februari 2020: Lägg till nya SAP-support anteckningar [SAP-arbetsbelastningar på Azure: planering och distribution check lista](./sap-deployment-checklist.md)
- 13 februari 2020: ändring av Azure-VM: ar [hög tillgänglighet för SAP NetWeaver på RHEL](./high-availability-guide-rhel.md) och [virtuella Azure-datorer med hög tillgänglighet för SAP NetWeaver på RHEL med Azure NetApp Files](./high-availability-guide-rhel-netapp-files.md) för att justera kluster resurserna timeout till rekommendationerna för Red Hat-timeout
- 11 februari 2020: lansering av [SAP HANA om migrering av stora Azure-instanser till azure Virtual Machines](./hana-large-instance-virtual-machine-migration.md)
- Februari 07 2020: ändring i [offentlig slut punkts anslutning för virtuella datorer med Azure standard ILB i SAP ha-scenarier](./high-availability-guide-standard-load-balancer-outbound-connections.md) för att uppdatera exempel NSG skärm bild
- Den 03 februari 2020: ändring i [hög tillgänglighet för SAP NW på virtuella Azure-datorer på SLES för SAP-program](./high-availability-guide-suse.md) och [hög tillgänglighet för SAP NW på virtuella Azure-datorer på SLES med ANF för SAP-program](./high-availability-guide-suse-netapp-files.md) för att ta bort varningen om att använda bindestreck i värdnamn för klusternoder på SLES
- 28 januari 2020: ändring i [hög tillgänglighet för SAP HANA på virtuella Azure-datorer på RHEL](./sap-hana-high-availability-rhel.md) för att justera SAP HANA kluster resursernas timeout till rekommendationerna för Red Hat-timeout
- 17 januari 2020: ändring i [Azure närhets placerings grupper för optimal nätverks fördröjning med SAP-program](./sap-proximity-placement-scenarios.md) för att ändra avsnittet om att flytta befintliga virtuella datorer till en närhets placerings grupp
- 17 januari 2020: ändring i [SAP-arbetsbelastningar med Azure-tillgänglighetszoner](./sap-ha-availability-zones.md) för att peka på en procedur som automatiserar mätningar av svars tider mellan Tillgänglighetszoner
- 16 januari 2020: ändra i [så här installerar och konfigurerar du SAP HANA (stora instanser) på Azure](./hana-installation.md) för att anpassa OS-versioner till Hana IaaS Hardware Directory
- 16 januari 2020: ändringar i [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SLES med multi-sid-guide](./high-availability-guide-suse-multi-sid.md) för att lägga till instruktioner för SAP-system med hjälp av ENSA2 (Server Queue server 2 Architecture)
- 10 januari 2020: ändringar i [SAP HANA skalas ut med noden vänte läge på virtuella Azure-datorer med Azure NetApp Files på SLES](./sap-hana-scale-out-standby-netapp-files-suse.md) och i [SAP HANA skala ut med noden vänte läge på virtuella Azure-datorer med Azure NetApp Files på RHEL](./sap-hana-scale-out-standby-netapp-files-rhel.md) för att lägga till instruktioner om hur du gör `nfs4_disable_idmapping` ändringar permanent.
- 10 januari 2020: ändringar i [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SLES med Azure NetApp Files för SAP-program](./high-availability-guide-suse-netapp-files.md) och i [Azure Virtual Machines hög tillgänglighet för SAP NetWeaver på RHEL med Azure NetApp Files för SAP-program](./high-availability-guide-rhel-netapp-files.md) för att lägga till instruktioner för hur du monterar Azure NetApp Files NFSv4-volymer.
- 23 december 2019: version av [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SLES multi-sid-guide](./high-availability-guide-suse-multi-sid.md)
- 18 december 2019: lansering av [SAP HANA skalas ut med noden vänte läge på virtuella Azure-datorer med Azure NetApp Files på RHEL](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- 21 november 2019: ändringar i [SAP HANA skalas ut med noden vänte läge på virtuella Azure-datorer med Azure NetApp Files på SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md) för att förenkla konfigurationen för NFS-ID-mappning och ändra det rekommenderade primära nätverks gränssnittet för att förenkla routning.
- 15 november 2019: mindre ändringar i [hög tillgänglighet för SAP NetWeaver på SUSE Linux Enterprise Server med Azure NetApp Files för SAP-program](high-availability-guide-suse-netapp-files.md) och [hög tillgänglighet för sap NetWeaver på Red Hat Enterprise Linux med Azure NetApp Files för SAP-program](high-availability-guide-rhel-netapp-files.md) för att klargöra storleks begränsningar för kapacitet och ta bort instruktion som endast NFSv3-versionen stöds.
- 12 november 2019: version av [hög tillgänglighet för SAP NetWeaver på Windows med Azure NetApp Files (SMB)](high-availability-guide-windows-netapp-files-smb.md)
- 8 november 2019: ändringar i [hög tillgänglighet för SAP HANA på virtuella Azure-datorer på SUSE Linux Enterprise Server](sap-hana-high-availability.md), [Konfigurera SAP HANA system replikering på virtuella Azure-datorer (vm)](sap-hana-high-availability-rhel.md) [Azure Virtual Machines hög tillgänglighet för SAP NetWeaver på SUSE Linux Enterprise Server för sap-program](high-availability-guide-suse.md), [Azure Virtual Machines hög tillgänglighet för SAP NetWeaver på SUSE Linux Enterprise Server med Azure NETAPP Files](high-availability-guide-suse-netapp-files.md), [azure Virtual Machines hög tillgänglighet för SAP-NetWeaver på Red Hat Enterprise Linux](high-availability-guide-rhel.md), [Azure Virtual Machines hög tillgänglighet för SAP NetWeaver på Red Hat Enterprise Linux med Azure NetApp Files](high-availability-guide-rhel-netapp-files.md), [hög tillgänglighet för NFS på virtuella Azure-datorer SUSE Linux Enterprise Server](high-availability-guide-suse-nfs.md), [GlusterFS på virtuella Azure-datorer i Red Hat Enterprise Linux för SAP NetWeaver](high-availability-guide-rhel-glusterfs.md) för att rekommendera Azure standard Load Balancer  
- 8 november 2019: ändringar i [SAP-arbetsbelastnings planering och distributions check lista](sap-deployment-checklist.md) för att klargöra krypterings rekommendation  
- 4 november 2019: ändringar i [Konfigurera pacemaker på SUSE Linux Enterprise Server i Azure](high-availability-guide-suse-pacemaker.md) för att skapa klustret direkt med unicast-konfiguration  
