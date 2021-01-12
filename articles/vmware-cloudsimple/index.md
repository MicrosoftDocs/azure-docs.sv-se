---
title: Azure VMware Solution by CloudSimple
description: Få mer information om Azure VMware Solutions från CloudSimple, inklusive en översikt, snabbstarter, koncept, självstudier och instruktioner.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.custom: seo-azure-migrate
keywords: stöd för virtuella datorer, Azure VMware Solution by CloudSimple, cloudsimple Azure, virtuella datorer, vmware-dokumentation
ms.openlocfilehash: a3514a011f940b82702984737d0a93c5da402b55
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108389"
---
# <a name="azure-vmware-solution-by-cloudsimple"></a>Azure VMware Solution by CloudSimple

Välkommen till en gemensam portal där du får hjälp med Azure VMware Solution by CloudSimple.
På dokumentationswebbplatsen kan du lära dig mer om följande ämnen:

## <a name="overview"></a>Översikt

Ta reda på mer om Azure VMware Solution by CloudSimple

* Lär dig mer om funktionerna, fördelarna och användningsscenarierna på [What is Azure VMware Solution by CloudSimple](cloudsimple-vmware-solutions-overview.md) (Vad är Azure VMware Solution från CloudSimple)
* Ta en titt på [viktiga begrepp för administration](key-concepts.md)

## <a name="quickstart"></a>Snabbstart

Lär dig hur du kommer igång med lösningen

* Förstå hur du [initierar tjänsten och köper kapacitet](quickstart-create-cloudsimple-service.md)
* Lär dig hur du skapar en ny VMware-miljö på [Configure a Private Cloud Environment](quickstart-create-private-cloud.md) (Konfigurera en privat molnmiljö)
* Lär dig hur du kan förena hanteringen över VMware och Azure genom att läsa artikeln [Använda virtuella VMware-datorer på Azure](quickstart-create-vmware-virtual-machine.md)

## <a name="concepts"></a>Begrepp

Lär dig mer om följande begrepp

* En [CloudSimple-tjänst](cloudsimple-service.md) (kallas även Azure VMware Solution by CloudSimple – tjänst). Den här resursen måste skapas en gång per region.
* Köp kapaciteten för din miljö genom att skapa en eller flera [CloudSimple-nodresurser](cloudsimple-node.md). De här resurserna kallas även för Azure VMware Solution by CloudSimple – nod.
* Initiera och konfigurera VMware-miljön med hjälp av [privata moln](cloudsimple-private-cloud.md).
* Förena hanteringen med [Virtuella CloudSimple-datorer](cloudsimple-virtual-machines.md) (kallas även Azure VMware Solution by CloudSimple – virtuell dator).
* Utforma det underliggande nätverket med hjälp av [VLAN/undernät](cloudsimple-vlans-subnets.md).
* Segmentera och skydda ditt underliggande nätverk med hjälp av resursen [Brandväggstabell](cloudsimple-firewall-tables.md).
* Få säker åtkomst till dina VMware-miljöer via WAN med [VPN-gatewayer](cloudsimple-vpn-gateways.md).
* Aktivera offentlig åtkomst för arbetsbelastningar med [offentlig IP](cloudsimple-public-ip-address.md).
* Upprätta anslutningar till virtuella Azure-nätverk och lokala nätverk med en [Azure-nätverksanslutning](cloudsimple-azure-network-connection.md).
* Konfigurera aviseringsmål via e-post med [Kontohantering.](cloudsimple-account.md)
* Visa loggar för användar-och systemaktivitet med [Aktivitetsledning](cloudsimple-activity.md)-skärmar.
* Förstå de olika [VMware-komponenterna](vmware-components.md).

## <a name="tutorials"></a>Självstudier

Lär dig hur du utför vanliga uppgifter, till exempel:

* [Skapa en CloudSimple-tjänst](create-cloudsimple-service.md) en gång per region där du vill distribuera VMware-miljöer.
* Hantera grundläggande tjänstfunktioner i [CloudSimple-portalen](access-cloudsimple-portal.md).
* Aktivera kapacitet och optimera faktureringen för din infrastruktur genom att [köpa CloudSimple-noder](create-nodes.md).
* Hantera konfiguration av VMware-miljöer med hjälp av privata moln. Du kan [skapa](create-private-cloud.md), [hantera](manage-private-cloud.md), [expandera](expand-private-cloud.md) eller [krympa](shrink-private-cloud.md) privata moln.
* Aktivera enhetlig hantering genom att [mappa Azure-prenumerationer](azure-subscription-mapping.md).
* Övervaka användar- och systemaktivitet med [aktivitetssidorna](monitor-activity.md).
* Konfigurera nätverk för dina miljöer genom att [skapa och hantera undernät](create-vlan-subnet.md).
* Segmentera och skydda din miljö med [brandväggstabeller och regler.](firewall.md)
* Aktivera inkommande Internetåtkomst för arbetsbelastningar genom att [allokera offentliga IP-adresser](public-ips.md).
* Aktivera anslutning från dina interna nätverk eller klientarbetsstationer genom att [konfigurera VPN.](vpn-gateway.md)
* Aktivera kommunikation från dina [lokala miljöer](on-premises-connection.md) och [virtuella Azure-nätverk](virtual-network-connection.md).
* Konfigurera aviseringsmål och visa den totala inköpta kapaciteten i [kontosammanfattningen](account.md)
* Visa [användare](users.md) som har åtkomst till CloudSimple-portalen.
* Hantera virtuella VMware-datorer från Azure-portalen:
    * [Skapa virtuella datorer](azure-create-vm.md) i Azure-portalen.
    * [Hantera virtuella datorer](azure-manage-vm.md) som du har skapat.

## <a name="how-to-guides"></a>Instruktionsguider

De här riktlinjerna beskriver lösningar på mål som:

* [Skydda din miljö](private-cloud-secure.md)
* Installera verktyg från tredje part, aktivera ytterligare användare och extern autentiseringskälla i vSphere med [privilegieeskalering](escalate-privileges.md).
* Konfigurera åtkomst till olika VMware-tjänster [genom att konfigurera lokal DNS](on-premises-dns-setup.md).
* Aktivera namn- och adressallokering för dina arbetsbelastningar genom att [konfigurera arbetsbelastningens DNS och DHCP.](dns-dhcp-setup.md)
* Förstå hur tjänsten säkerställer säkerhet och funktionalitet på din plattform genom [tjänstuppdateringar och uppgraderingar.](vmware-components.md#updates-and-upgrades)
* Spara TCO vid säkerhetskopiering genom att skapa en exempelsäkerhetskopieringsarkitektur med [programvara från tredje part, till exempel Veeam.](backup-workloads-veeam.md)
* Skapa en säker miljö genom att aktivera kryptering i vila med [en KMS-krypteringsprogramvara från tredje part](vsan-encryption.md).
* Utöka Azure Active Directory-hantering (Azure AD) i VMware genom att konfigurera [identitetskällan för Azure AD](azure-ad.md).
