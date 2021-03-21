---
title: Azure VMware-lösning av CloudSimple – konfigurera ett privat moln som en katastrof återställnings plats med hjälp av VMware Site Recovery Manager
description: Beskriver hur du konfigurerar ditt CloudSimple privata moln som en katastrof återställnings plats med hjälp av VMware Site Recovery Manager.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3d71f8aec1f35514ac6c10b17b6f7b69b79b05bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97897917"
---
# <a name="set-up-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>Konfigurera ett privat moln som ett haveri beredskaps mål med VMware Site Recovery Manager

Du kan använda ditt privata moln för CloudSimple som en katastrof återställnings plats (DR) för lokala VMware-arbetsbelastningar.

DR-lösningen baseras på vSphere-replikering och VMware Site Recovery Manager (SRM). En liknande metod kan följas för att aktivera ditt privata moln som en primär plats som skyddas av din lokala återställnings plats.

CloudSimple-lösningen:

* Eliminerar behovet av att konfigurera ett Data Center specifikt för DR.
* Gör att du kan använda Azure-platser där CloudSimple distribueras för internationell geografisk återhämtning.
* Ger dig ett alternativ för att minska kostnaderna för distribution och den totala ägande kostnaden för att upprätta DR.

CloudSimple-lösningen kräver att du gör följande:

* Installera, konfigurera och hantera vSphere-replikering och SRM i ditt privata moln.
* Ange egna licenser för SRM när det privata molnet är den skyddade platsen. Du behöver inga ytterligare SRM-licenser för CloudSimple-platsen när den används som återställnings plats.

Med den här lösningen har du fullständig kontroll över vSphere-replikering och SRM. De välkända gränssnitts-, API-och CLI-gränssnitten möjliggör användning av befintliga skript och verktyg.

![Distribution av Site Recovery Manager](media/srm-deployment.png)

Du kan använda alla versioner av vRA och SRM som är kompatibla med ditt privata moln och lokala miljöer. I exemplen i den här guiden används vRA 6,5 och SRM 6,5. Dessa versioner är kompatibla med vSphere 6,5, som stöds av CloudSimple.

## <a name="deploy-the-solution"></a>Distribuera lösningen

I följande avsnitt beskrivs hur du distribuerar en DR-lösning med SRM i ditt privata moln.

1. [Kontrol lera att VMware-produktsortiment är kompatibla](#verify-that-vmware-product-versions-are-compatible)
2. [Uppskatta storleken på din DR-miljö](#estimate-the-size-of-your-dr-environment)
3. [Skapa ett privat moln för din miljö](#create-a-private-cloud-for-your-environment)
4. [Konfigurera nätverk för privat moln för SRM-lösningen](#set-up-private-cloud-networking-for-the-srm-solution)
5. [Konfigurera en plats-till-plats-VPN-anslutning mellan ditt lokala nätverk och det privata molnet och öppna nödvändiga portar](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports)
6. [Konfigurera infrastruktur tjänster i ditt privata moln](#set-up-infrastructure-services-in-your-private-cloud)
7. [Installera vSphere-replikering i din lokala miljö](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [Installera vSphere-replikering i din privata moln miljö](#install-vsphere-replication-appliance-in-your-private-cloud-environment)
9. [Installera SRM-server i din lokala miljö](#install-srm-server-in-your-on-premises-environment)
10. [Installera SRM-server i ditt privata moln](#install-srm-server-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Kontrol lera att VMware-produktsortiment är kompatibla

Konfigurationerna i den här guiden omfattas av följande krav för kompatibilitet:

* Samma version av SRM måste distribueras i ditt privata moln och i din lokala miljö.
* Samma version av vSphere-replikering måste distribueras i ditt privata moln och i den lokala miljön.
* Versionerna av Platform Services Controller (PSC) i ditt privata moln och i den lokala miljön måste vara kompatibla.
* Versioner av vCenter i ditt privata moln och i den lokala miljön måste vara kompatibla.
* Versionerna av SRM och vSphere-replikering måste vara kompatibla med varandra och med versionerna av PSC och vCenter.

Länkar till relevant VMware-dokumentation och kompatibilitetsinformation finns i dokumentationen för [vmware Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html) .

Om du vill ta reda på vilka versioner av vCenter och PSC som finns i ditt privata moln öppnar du CloudSimple-portalen. Gå till **resurser**, Välj ditt privata moln och klicka på fliken **vSphere hanterings nätverk** .

![versioner av vCenter & PSC i privata moln](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>Uppskatta storleken på din DR-miljö

1. Kontrol lera att den identifierade lokala konfigurationen ligger inom gränserna som stöds. I SRM 6,5 dokumenteras gränserna i VMwares kunskaps bas artikel om [drifts gränser för Site Recovery Manager 6,5](https://kb.vmware.com/s/article/2147110).
2. Se till att du har tillräckligt med nätverks bandbredd för att uppfylla arbets Belastningens storlek och krav för återställningen. I VMware Knowledge Base-artikeln om hur du [beräknar bandbredds krav för vSphere-replikering](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html) får du vägledning om bandbredds gränserna.
3. Använd CloudSimple storleks verktyget för att beräkna de resurser som behövs på din DR-plats för att skydda din lokala miljö.

### <a name="create-a-private-cloud-for-your-environment"></a>Skapa ett privat moln för din miljö

Skapa ett privat moln från CloudSimple-portalen genom att följa anvisningarna och storleks rekommendationerna i [skapa ett privat moln](create-private-cloud.md).

### <a name="set-up-private-cloud-networking-for-the-srm-solution"></a>Konfigurera nätverk för privat moln för SRM-lösningen

Få åtkomst till CloudSimple-portalen för att konfigurera nätverk för privat moln för SRM-lösningen.

Skapa ett virtuellt lokalt nätverk för SRM-lösningens nätverk och tilldela det ett undernät CIDR. Instruktioner finns i [skapa och hantera VLAN/undernät](create-vlan-subnet.md).

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports"></a>Konfigurera en plats-till-plats-VPN-anslutning mellan ditt lokala nätverk och det privata molnet och öppna nödvändiga portar

Konfigurera plats-till-plats-anslutning mellan ditt lokala nätverk och ditt privata moln. Instruktioner finns i [Konfigurera en VPN-anslutning till ditt CloudSimple privata moln](set-up-vpn.md).

### <a name="set-up-infrastructure-services-in-your-private-cloud"></a>Konfigurera infrastruktur tjänster i ditt privata moln

Konfigurera infrastruktur tjänster i det privata molnet för att göra det enkelt att hantera dina arbets belastningar och verktyg.

Du kan lägga till en extern identitetsprovider enligt beskrivningen i [använda Azure AD som identitets leverantör för vCenter på CloudSimple privata moln](azure-ad.md) om du vill göra något av följande:

* Identifiera användare från din lokala Active Directory (AD) i ditt privata moln.
* Konfigurera en AD i ditt privata moln för alla användare.
* Använd Azure AD.

Om du vill tillhandahålla IP-adressering, IP-adress hantering och namn matchnings tjänster för dina arbets belastningar i det privata molnet konfigurerar du en DHCP-och DNS-server enligt beskrivningen i [Konfigurera DNS och DHCP-program och arbets belastningar i ditt CloudSimple privata moln](dns-dhcp-setup.md).

Domänen *. cloudsimple.io används av virtuella hanterings datorer och värdar i ditt privata moln. För att lösa begär anden till den här domänen konfigurerar du DNS-vidarebefordran på DNS-servern enligt beskrivningen i [skapa en villkorlig vidarebefordrare](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>Installera vSphere-replikering i din lokala miljö

Installera vSphere Replication-installation (vRA) i din lokala miljö genom att följa VMware-dokumentationen. Installationen består av följande steg på hög nivå:

1. Förbered din lokala miljö för vRA-installation.

2. Distribuera vRA i din lokala miljö med hjälp av OVF i VR ISO från vmware.com. I vRA 6,5 har [den här VMware-bloggen](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) relevant information.

3. Registrera din lokala vRA med vCenter Single Sign-On på den lokala platsen. Detaljerade anvisningar för vSphere-replikering 6,5 finns i VMware Document [VMware vSphere replication 6,5 Installation and Configuration](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

## <a name="install-vsphere-replication-appliance-in-your-private-cloud-environment"></a>Installera vSphere-replikering i din privata moln miljö

Innan du börjar ska du kontrol lera att du har följande:

* IP-nåbarhet från undernät i din lokala miljö till hanterings under nätet för ditt privata moln
* IP-nåbarhet från under nätet i din lokala vSphere-miljö till SRM-lösningens undernät i ditt privata moln

Instruktioner finns i [Konfigurera en VPN-anslutning till ditt CloudSimple privata moln](set-up-vpn.md). Stegen liknar dem för den lokala installationen.

CloudSimple rekommenderar att du använder FQDN i stället för IP-adresser under installationen av vRA och SRM. Öppna CloudSimple-portalen om du vill ta reda på det fullständiga domän namnet för vCenter och PSC i ditt privata moln. Gå till **resurser**, Välj ditt privata moln och klicka på fliken **vSphere hanterings nätverk** .

![Söker efter FQDN för vCenter/PSC i privat moln](media/srm-resources.png)

CloudSimple kräver att du inte installerar vRA och SRM med standard-cloudowner-användaren, utan i stället skapa en ny användare. Detta görs för att säkerställa hög drift tid och tillgänglighet för din privata moln vCenter-miljö. Dock har cloudowner-standardanvändaren i det privata molnet vCenter does't behörighet att skapa en ny användare med administratörs behörighet.

Innan du installerar vRA och SRM måste du eskalera vCenter-behörigheterna för cloudowner-användaren och sedan skapa en användare med administratörs behörighet i vCenter SSO-domänen. Mer information om standard användar-och behörighets modellen för privata moln finns i [Lär dig mer om behörighets modellen för privata moln](learn-private-cloud-permissions.md).

Installationen består av följande steg på hög nivå:

1. [Eskalera privilegier](escalate-private-cloud-privileges.md).
2. Skapa en användare i ditt privata moln för vSphere-replikering och SRM-installation. Förklaras nedan i [VCENTER UI: skapa en användare i ett privat moln för vRA & SRM-installation](#vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation).
3. Förbered din privata moln miljö för vRA-installation.
4. Distribuera vRA i ditt privata moln med hjälp av OVF i VR ISO från vmware.com. I vRA 6,5 har [den här VMware-bloggen](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) relevant information.
5. Konfigurera brand Väggs regler för vRA. Förklaras nedan i [CloudSimple-portalen: Konfigurera brand Väggs regler för vRA](#cloudsimple-portal-configure-firewall-rules-for-vra).
6. Registrera privata moln vRA med vCenter Single Sign-On på den privata moln platsen.
7. Konfigurera vSphere för replikering mellan de två enheterna. Se till att de portar som krävs är öppna i brand väggarna. I [den här VMware Knowledge Base-artikeln](https://kb.vmware.com/s/article/2087769) finns en lista med port nummer som måste vara öppna för vSphere-replikering 6,5.

Detaljerade installations anvisningar för vSphere-replikering 6,5 finns i VMware Document [VMware vSphere replication 6,5 Installation and Configuration](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation"></a>vCenter-gränssnitt: skapa en användare i ett privat moln för vRA och SRM-installation

Logga in på vCenter med cloudowner-användarautentiseringsuppgifter efter att ha eskalerat privilegier från CloudSimple-portalen.

Skapa en ny användare, `srm-soln-admin` i vCenter och Lägg till den i gruppen Administratörer i vCenter.
Logga ut från vCenter som cloudowner-användare och logga in som *SRM-soln-admin-* användaren.

#### <a name="cloudsimple-portal-configure-firewall-rules-for-vra"></a>CloudSimple-Portal: Konfigurera brand Väggs regler för vRA

Konfigurera brand Väggs regler enligt beskrivningen i [Konfigurera brand Väggs tabeller och regler](firewall.md) för att öppna portar för att aktivera kommunikation mellan:

* vRA i SRM-lösningens nätverk och vCenter-och ESXi-värdar i hanterings nätverket.
* vRA-enheter på de två platserna.

I den här [VMware Knowledge Base-artikeln](https://kb.vmware.com/s/article/2087769) finns en lista med port nummer som måste vara öppna för vSphere-replikering 6,5.

### <a name="install-srm-server-in-your-on-premises-environment"></a>Installera SRM-server i din lokala miljö

Innan du börjar kontrollerar du följande:

* vSphere-replikering installeras i dina lokala och privata moln miljöer.
* VSphere Replication-enheter på båda platserna är anslutna till varandra.
* Du har granskat VMware-information om krav och bästa praxis. För SRM 6,5 kan du referera till VMware-dokumentets [krav och metod tips för SRM 6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

Följ VMware-dokumentationen för att utföra SRM Server installation i distributions modellens topologi med två platser med en vCenter-instans per plattforms tjänst styrenhet enligt beskrivningen i det här [VMware-dokumentet](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). Installations anvisningarna för SRM 6,5 finns i VMware-dokumentet [installera Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

### <a name="install-srm-server-in-your-private-cloud"></a>Installera SRM-server i ditt privata moln

Innan du börjar kontrollerar du följande:

* vSphere-replikering installeras i dina lokala och privata moln miljöer.
* VSphere Replication-enheter på båda platserna är anslutna till varandra.
* Du har granskat VMware-information om krav och bästa praxis. För SRM 6,5 kan du läsa [krav och metod tips för installation av Site Recovery Manager 6,5-Server](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

I följande steg beskrivs den privata moln SRM installationen.

1. [vCenter-gränssnitt: installera SRM](#vcenter-ui-install-srm)
2. [CloudSimple-Portal: Konfigurera brand Väggs regler för SRM](#cloudsimple-portal-configure-firewall-rules-for-srm)
3. [vCenter-gränssnitt: Konfigurera SRM](#vcenter-ui-configure-srm)
4. [CloudSimple Portal: deeskalera privilegier](#cloudsimple-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>vCenter-gränssnitt: installera SRM

När du har loggat in på vCenter med SRM-soln-admin-autentiseringsuppgifter följer du VMware-dokumentationen för att utföra SRM Server installation i distributions modellens topologi med två platser med en vCenter-instans per plattforms tjänst styrenhet enligt beskrivningen i det här [VMware-dokumentet](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). Installations anvisningarna för SRM 6,5 finns i VMware-dokumentet [installera Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

#### <a name="cloudsimple-portal-configure-firewall-rules-for-srm"></a>CloudSimple-Portal: Konfigurera brand Väggs regler för SRM

Konfigurera brand Väggs regler enligt beskrivningen i [Konfigurera brand Väggs tabeller och regler](firewall.md) för att tillåta kommunikation mellan:

SRM-servern och vCenter/PSC i det privata molnet.
SRM-servrarna på båda platserna

I [den här VMware Knowledge Base-artikeln](https://kb.vmware.com/s/article/2087769) finns en lista med port nummer som måste vara öppna för vSphere-replikering 6,5.

#### <a name="vcenter-ui-configure-srm"></a>vCenter-gränssnitt: Konfigurera SRM

När SRM har installerats i det privata molnet utför du följande uppgifter som beskrivs i avsnittet i installations-och konfigurations guiden för VMware Site Recovery Manager. I SRM 6,5 är instruktionerna tillgängliga i VMware-dokumentet som [installerar Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

1. Anslut Site Recovery Manager Server-instanserna på skyddade platser och återställnings platser.
2. Upprätta en klient anslutning till Remote Site Recovery Manager Server-instansen.
3. Installera licens nyckeln för Site Recovery Manager.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple Portal: deeskalera privilegier

För att ta bort privilegier, se [aveskalerade privilegier](escalate-private-cloud-privileges.md#de-escalate-privileges).

## <a name="ongoing-management-of-your-srm-solution"></a>Pågående hantering av din SRM-lösning

Du har fullständig kontroll över vSphere-replikering och SRM-programvara i din privata moln miljö och förväntas utföra den nödvändiga hantering av program livs cykeln. Se till att alla nya program versioner är kompatibla med det privata molnet vCenter och PSC innan du uppdaterar eller uppgraderar vSphere-replikering eller SRM.

> [!NOTE]
> CloudSimple är för närvarande att utforska alternativ för att erbjuda en hanterad DR-tjänst. 

## <a name="multiple-replication-configuration"></a>Konfiguration av flera replikeringar

 [Både matrisbaserade replikerings-och vSphere-tekniker kan användas tillsammans med SRM](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication) på samma gång. De måste dock tillämpas på separata uppsättningar av virtuella datorer (en virtuell dator kan skyddas med hjälp av en matris-baserad replikering eller vSphere, men inte båda). Dessutom kan CloudSimple-webbplatsen konfigureras som en återställnings plats för flera skyddade platser. Mer information om konfigurationer med flera platser finns i [SRM alternativ för flera platser](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/) .

## <a name="references"></a>Referenser

* [Dokumentation om VMware Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [Drift gränser för Site Recovery Manager 6,5](https://kb.vmware.com/s/article/2147110)
* [Beräknar bandbredds krav för vSphere-replikering](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)
* [OVF-alternativ när du distribuerar vSphere-replikering 6,5](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [VMware vSphere replikering 6,5-installation och konfiguration](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [Krav och bästa praxis för SRM 6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [Site Recovery hanteraren i en Two-Site-topologi med en vCenter Server-instans per plattforms tjänst styrenhet](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [Installations-och konfigurations guide för VMware Site Recovery Manager 6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [VMware-blogg på SRM med array-baserad replikering eller vSphere-replikering](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [VMware-blogg om SRM alternativ för flera platser](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [Port nummer som måste vara öppna för vSphere-replikering 5,8. x, 6. x och 8](https://kb.vmware.com/s/article/2147112)
