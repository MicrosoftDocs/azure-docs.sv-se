---
title: Azure VMware-lösning från CloudSimple – Använd privat moln webbplats som värd för en virtuell Skriv bords infrastruktur med VMware-Horisont
description: Beskriver hur du kan använda din CloudSimple privata moln webbplats som värd för en virtuell Skriv bords infrastruktur med VMware-Horisont
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 513af1ba2e354412278fb88af4ee6527c236fae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895605"
---
# <a name="use-cloudsimple-private-cloud-site-to-host-a-virtual-desktop-infrastructure-using-vmware-horizon"></a>Använd CloudSimple privata moln webbplats som värd för en virtuell Skriv bords infrastruktur med VMware-Horisont

Du kan använda din CloudSimple privata moln webbplats som värd för en virtuell Skriv bords infrastruktur (VDI) med VMware-Horisont 7. x. Följande bild visar den logiska lösnings arkitekturen för VDI.

![Horisont distribution](media/horizon-deployment.png)

Med den här lösningen har du fullständig kontroll över Horisont View Manager och app-volym. De välkända gränssnitts-, API-och CLI-gränssnitten möjliggör användning av befintliga skript och verktyg.

CloudSimple-lösningen kräver att du gör följande:

* Installera, konfigurera och hantera VMware-Horisont 7. x i ditt privata moln.
* Ange dina egna Horisont licenser.

## <a name="deploy-the-solution"></a>Distribuera lösningen

I följande avsnitt beskrivs hur du distribuerar en VDI-lösning med hjälp av Horisont i ditt privata moln.

1. [Kontrol lera att VMware-produktsortiment är kompatibla](#verify-that-vmware-product-versions-are-compatible)
2. [Uppskatta storleken på din Skriv bords miljö](#estimate-the-size-of-your-desktop-environment)
3. [Skapa ett privat moln för din miljö](#create-a-private-cloud-for-your-environment)
4. [Installera VMware-Horisont i ditt privata moln](#install-vmware-horizon-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Kontrol lera att VMware-produktsortiment är kompatibla

* Kontrol lera att dina aktuella och planerade versioner av Horisont, app-volymer, Unified Access Gateway och User Environment Manager är kompatibla med varandra och med vCenter och PSC i det privata molnet. Kompatibilitetsinformation finns i [VMware Compatibility Matrix för horisont 7,5](https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&260=2877&0=).
* Om du vill ta reda på de aktuella versionerna av vCenter och PSC i ditt privata moln går du till **resurser** i [CloudSimple-portalen](access-cloudsimple-portal.md), väljer ditt privata moln och klickar på fliken **vSphere hanterings nätverk** .

![vCenter-och PSC-versioner](media/private-cloud-vsphere-versions.png)

### <a name="estimate-the-size-of-your-desktop-environment"></a>Uppskatta storleken på din Skriv bords miljö

* Kontrol lera att din identifierade konfiguration är i drift gränser i VMware.
* Beräkna de resurser som behövs för alla dina Skriv bord och dina komponenter för Horisont hantering.

### <a name="create-a-private-cloud-for-your-environment"></a>Skapa ett privat moln för din miljö

1. Skapa ett privat moln från CloudSimple-portalen genom att följa anvisningarna i [Konfigurera en privat moln miljö](quickstart-create-private-cloud.md).  CloudSimple skapar en standard-vCenter-användare med namnet "cloudowner" i varje nyligen skapat privat moln. Mer information om standard användar-och behörighets modellen för privata moln finns i [Lär dig mer om behörighets modellen för privata moln](learn-private-cloud-permissions.md).
2. Skapa ett VLAN i ditt privata moln för Horisont hanterings planet och tilldela det en under näts CIDR. Instruktioner finns i [skapa och hantera VLAN/undernät](create-vlan-subnet.md). Detta är det nätverk där alla lösnings komponenter (Unified Access Gateway, anslutnings Server, app Volume Server och User Environment Manager-servrar) ska installeras.
3. Bestäm om du vill använda en extern identitetsprovider med ditt privata moln vCenter. Om ja, Välj något av följande alternativ:
    * Använd din lokala Active Directory som extern identitets leverantör. Instruktioner finns i [vCenter Identity sources](set-vcenter-identity.md).
    * Konfigurera en Active Directory-server i det privata molnet i ett Horisont hanterings plan VLAN som ska användas som extern identitetsprovider. Instruktioner finns i [vCenter Identity sources](set-vcenter-identity.md).
    * Konfigurera en DHCP-och DNS-server i Horisont Management plan VLAN i det privata molnet. Instruktioner finns i [Konfigurera DNS-och DHCP-program och arbets belastningar i ditt privata moln i CloudSimple](dns-dhcp-setup.md).
4. Konfigurera DNS-vidarebefordran på den DNS-server som är installerad i det privata molnet. Instruktioner finns i [skapa en villkorlig vidarebefordrare](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vmware-horizon-in-your-private-cloud"></a>Installera VMware-Horisont i ditt privata moln

Följande distributions diagram illustrerar en horisont lösning som distribueras i ett privat moln. Unified Access Gateway, AD/DC, View och app Volume Server installeras i ett användardefinierat VLAN 234. Unified Access Gateway har en tilldelad offentlig IP-adress som kan nås från Internet. Horisont Station ära datorer för virtuella datorer distribueras i VLAN 235 för att ge ytterligare isolering och säkerhet.

![Horisont distribution i det privata molnet](media/horizon-private-cloud.png)

I följande avsnitt beskrivs hur du konfigurerar en distribution som liknar den som illustreras i bilden. Innan du börjar ska du kontrol lera att du har följande:

* Ett privat moln som skapats med CloudSimple-portalen och som har tillräckligt med kapacitet för att köra dina Skriv bords grupper.
* Tillräcklig bandbredd mellan din lokala miljö och den privata moln miljön för att stödja nätverks trafiken för dina Skriv bord.
* En plats-till-plats-VPN-tunnel som kon figurer ATS mellan ditt lokala data Center och det privata molnet.
* IP-nåbarhet från slut användar under nät i din lokala miljö till CloudSimple privata moln under nät.
* AD/DHCP/DNS har installerats för ditt privata moln.

#### <a name="cloudsimple-portal-create-a-dedicated-vlansubnet-for-desktop-pools"></a>CloudSimple-portalen: skapa ett dedikerat VLAN/undernät för Skriv bords grupper

Skapa ett virtuellt lokalt nätverk för Horisont-skrivbords bassänger och tilldela den till en under näts CIDR. Instruktioner finns i [skapa och hantera VLAN/undernät](create-vlan-subnet.md). Det här är det nätverk där alla virtuella dator datorer ska köras.

Skydda din horisont distribution genom att följa standard metod tips för säkerhet:

* Tillåt endast fjärr skrivbords RDP-trafik/SSH-trafik till dina Station ära datorer.
* Tillåt endast hanterings trafik mellan horisont hanterings plan VLAN och Desktop-pool-VLAN.
* Tillåt endast hanterings trafik från det lokala nätverket.

Du kan använda dessa metod tips genom att konfigurera [brand Väggs regler](firewall.md) från CloudSimple-portalen.

#### <a name="cloudsimple-portal-configure-firewall-rules-to-secure-horizon-management-plane"></a>CloudSimple-portalen: Konfigurera brand Väggs regler för säker Horisont hanterings plan

Konfigurera följande regler i CloudSimple-portalen. Instruktioner finns i [Konfigurera brand Väggs tabeller och regler](firewall.md).

1. Konfigurera brand Väggs regler i CloudSimple N-S-brandväggen för att tillåta kommunikation mellan lokala undernät och Horisont hanterings-VLAN så att bara de nätverks portar som anges i [listan över](https://docs.vmware.com/en/VMware-Horizon-7/7.1/com.vmware.horizon-client-agent.security.doc/GUID-52807839-6BB0-4727-A9C7-EA73DE61ADAB.html) VMware-dokuments Horisont är tillåtna.

2. Skapa E-W brand Väggs regler mellan horisont hanterings-VLAN och Desktop pool-VLAN i det privata molnet.

#### <a name="cloudsimple-portal-create-a-public-ip-address-for-unified-access-gateway"></a>CloudSimple-Portal: skapa en offentlig IP-adress för Unified Access Gateway

Skapa en offentlig IP-adress för den enhetliga åtkomst-gateway-enheten för att aktivera Skriv bords klient anslutningar från Internet. Anvisningar finns i [tilldela offentliga IP-adresser](public-ips.md).

När installationen är klar tilldelas den offentliga IP-adressen och visas på sidan offentliga IP-adresser.

#### <a name="cloudsimple-portal-escalate-privileges"></a>CloudSimple-portalen: eskalera privilegier

Standard användaren "cloudowner" har inte tillräckliga privilegier i det privata molnet vCenter för att installera horisont, så användarens vCenter-privilegier måste eskaleras. Mer information finns i [eskalera privilegier](escalate-private-cloud-privileges.md).

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-horizon-installation"></a>vCenter-gränssnitt: skapa en användare i privat moln för Horisont installation

1. Logga in på vCenter med hjälp av cloudowner-användarautentiseringsuppgifter.
2. Skapa en ny användare, "Horisont-soln-admin" i vCenter och Lägg till användaren i gruppen Administratörer i vCenter.
3. Logga ut från vCenter som "cloudowner"-användare och logga in som "Horisont-soln-admin"-användare.

#### <a name="vcenter-ui-install-vmware-horizon"></a>vCenter-gränssnitt: Installera VMware-Horisont

Som nämnts i den tidigare logiska arkitektur-sektionen har Horisont lösningen följande komponenter:

* Vyn VMware-Horisont
* VMware Unified Access Gateway
* VMware app Volume Manager
* VMware User Environment Manager

Installera komponenterna på följande sätt:

1. Installera och konfigurera Unified Access Gateway genom att följa anvisningarna i VMware-dokumentet [distribuera och konfigurera VMware Unified Access Gateway](https://docs.vmware.com/en/Unified-Access-Gateway/3.3.1/com.vmware.uag-331-deploy-config.doc/GUID-F5CE0D5E-BE85-4FA5-BBCF-0F86C9AB8A70.html).

2. Installera vyn Horisont i det privata molnet genom att följa anvisningarna i [Visa installations guide](https://docs.vmware.com/en/VMware-Horizon-7/7.4/horizon-installation/GUID-37D39B4F-5870-4188-8B11-B6C41AE9133C.html).

3. Installera app Volume Manager genom att följa anvisningarna i [Installera och konfigurera VMware-app-volymer](https://docs.vmware.com/en/VMware-App-Volumes/2.10/com.vmware.appvolumes.user.doc/GUID-5E8BAF8C-F5A6-412C-9424-266BA7109BA4.html).

4. Installera och konfigurera användar miljö hanteraren genom att följa anvisningarna i [om hur du installerar och konfigurerar VMware User Environment Manager](https://docs.vmware.com/en/VMware-User-Environment-Manager/9.4/com.vmware.user.environment.manager-install-config/GUID-DBBC82E4-483F-4B28-9D49-4D28E08715BC.html).

#### <a name="file-a-support-request-to-upload-vmware-horizon-pre-packaged-app-volumes"></a>Skicka en support förfrågan om att ladda upp program volymer för förpaketerade VMware-Horisont

Som en del av installations processen använder app Volume Manager förpaketerade volymer för att etablera app-stackar och skrivbara volymer. Dessa volymer fungerar som mallar för app-stackar och skrivbara volymer.

Att ladda upp volymerna till data lagret för det privata molnet kräver rot lösen ordet för ESXi. Om du behöver hjälp kan du skicka en [support förfrågan](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Bifoga AppVolumes Installer-paketet så att CloudSimple support personal kan ladda upp mallarna till din privata moln miljö.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple Portal: deeskalera privilegier

Du kan nu ta bort [privilegierna](escalate-private-cloud-privileges.md#de-escalate-privileges) för användaren ' cloudowner '.

## <a name="ongoing-management-of-your-horizon-solution"></a>Kontinuerlig hantering av din horisont lösning

Du har fullständig kontroll över horisont-och app Volume Manager-programvara i din privata moln miljö och förväntas utföra den nödvändiga hantering av program livs cykeln. Se till att alla nya program versioner är kompatibla med det privata molnets vCenter och PSC innan du uppdaterar eller uppgraderar Horisont-eller app-volym.
