---
title: Begrepp – nätverks diagram för Azure Red Hat på OpenShift 4
description: Nätverks diagram och översikt för Azure Red Hat OpenShift-nätverk
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: azure-redhat-openshift
ms.date: 11/23/2020
ms.openlocfilehash: b785a6b73f595072f5d723bad8c119ddc4dc0f11
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100636346"
---
# <a name="network-concepts-for-azure-red-hat-openshift-aro"></a>Nätverks koncept för Azure Red Hat OpenShift (ARO)

Den här guiden omfattar en översikt över nätverk i Azure Red Hat OpenShift på OpenShift 4-kluster, tillsammans med ett diagram och en lista över viktiga slut punkter. Mer information om kärn skiftets nätverks koncept finns i avsnittet om [nätverks dokumentation för Azure Red Hat OpenShift 4](https://docs.openshift.com/container-platform/4.6/networking/understanding-networking.html).

![Nätverks diagram för Azure Red Hat OpenShift 4](./media/concepts-networking/aro4-networking-diagram.png)

När du distribuerar Azure Red Hat på OpenShift 4, finns hela klustret i ett virtuellt nätverk. I det här virtuella nätverket, noderna huvudnoder och arbetare varje Live i sitt eget undernät. Varje undernät använder en intern belastningsutjämnare och en offentlig belastningsutjämnare.

## <a name="networking-components"></a>Nätverkskomponenter

I följande lista beskrivs viktiga nätverks komponenter i ett kluster med OpenShift i Azure Red Hat.

* **aro-pls**
    * Det här är en Azure Private-slutpunkt som används av Microsoft och Red Hat Sites Tillförlitlighets tekniker för att hantera klustret.
* **Aro – internt – lb**
    * Den här slut punkten balanserar trafiken till API-servern. Huvudnoderna finns i backend-poolen för den här belastningsutjämnaren.
* **Aro – offentligt – lb**
    * Om API: t är offentligt dirigerar den här slut punkten vägen och balanserar trafiken till API-servern. Den här slut punkten tilldelar en offentlig utgående IP-adress så att Masters kan komma åt Azure Resource Manager och rapportera tillbaka på kluster hälsan.
* **Aro – internt**
    * Den här slut punkten balanserar intern tjänst trafik. Arbetsnoderna i den här belastningsutjämnaren finns i backend-poolen.
    * Den här belastningsutjämnaren skapas inte som standard. Den här belastningsutjämnaren skapas när du skapar en tjänst av typen LoadBalancer med rätt anteckningar. Till exempel: service.beta.kubernetes.io/azure-load-balancer-internal: "true".
* **Aro – internt – lb**
    * Den här slut punkten används för all offentlig trafik. När du skapar och program och en väg, är detta sökvägen för ingångs trafik.
    * Denna belastningsutjämnare omfattar även utgående Internet anslutning från alla Pod som körs i arbetsnoderna genom Azure Load Balancer utgående regler.
        * För tillfället utgående regler kan inte konfigureras. De tilldelar 1 024 TCP-portar till varje nod.
        * DisableOutboundSnat har inte kon figurer ATS i LB-reglerna, så poddar kan få som utgående IP-adress för alla offentliga IP-adresser i den här ALB.
        * Som en följd av de två föregående punkterna är det enda sättet att lägga till tillfälliga SNAT-portar genom att lägga till offentliga LoadBalancer-typ tjänster i ARO.
* **Aro-utgående – pip**
    * Den här slut punkten fungerar som en offentlig IP-adress (PIP) för arbetsnoderna.
    * Den här slut punkten gör det möjligt för tjänster att lägga till en speciell IP-adress från ett kluster med OpenShift i Azure i en lista med tillåtna.
* **Aro – NSG**
    * När du exponerar en tjänst skapar API: t en regel i den här nätverks säkerhets gruppen så trafikflödet passerar och når kontroll planet och noderna.
    * Som standard tillåter den här nätverks säkerhets gruppen all utgående trafik. För närvarande kan utgående trafik bara begränsas till Azure Red Hat OpenShift-kontroll planet.
* **Aro-controlplane-NSG**
  * Den här slut punkten tillåter endast trafik att gå in via port 6443 för huvudnoderna.
* **Azure Container Registry**
    * Detta är ett behållar register som tillhandahålls och används av Microsoft internt. Det här registret är skrivskyddat och är inte avsett att användas av Azure Red Hat OpenShift-användare.
        * Det här registret tillhandahåller värd plattforms avbildningar och kluster komponenter. Till exempel övervakning eller loggning av behållare.
        * Anslutningar till det här registret sker över tjänstens slut punkt (intern anslutning mellan Azure-tjänster).
        * Detta interna register är inte tillgängligt utanför klustret som standard.
* **Private Link**
    * Tillåter nätverks anslutning från hanterings planet till ett kluster för Microsoft och Red Hat Sites Tillförlitlighets tekniker som hjälper dig att hantera klustret.

## <a name="networking-policies"></a>Nätverks principer

* **Ingress**: nätverks principen för ingångs nätverk stöds som en del av [OpenShift-SDN](https://docs.openshift.com/container-platform/4.5/networking/openshift_sdn/about-openshift-sdn.html). Den här nätverks principen är aktive rad som standard och tvång utförs av användare. När den inkommande nätverks principen är v1 NetworkPolicy-kompatibel, stöds inte de utgående och IPBlock typerna.

* **Utgående**: de utgående nätverks principerna stöds med hjälp av funktionen [utgående brand vägg](https://docs.openshift.com/container-platform/4.5/networking/openshift_sdn/configuring-egress-firewall.html) i OpenShift. Det finns bara en utgående princip per namnrymd/projekt. Utgående principer stöds inte i namn området "default" och utvärderas i ordning (först till sista).

## <a name="networking-basics-in-openshift"></a>Grundläggande nätverk i OpenShift

OpenShift Software Defined Networking [(SDN)](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/about-openshift-sdn.html) används för att konfigurera ett överlägg nätverk med öppen VSwitch [(OVS)](https://www.openvswitch.org/), en OpenFlow-implementering baserad på cni-specifikationen (container Network Interface). SDN har stöd för olika plugin--nätverks principer är plugin-programmet som används i Azure Red Hat på OpenShift 4. All nätverkskommunikation hanteras av SDN, så inga extra vägar behövs i dina virtuella nätverk för att uppnå Pod för Pod kommunikation.

## <a name="networking--for-azure-red-hat-openshift"></a>Nätverk för Azure Red Hat OpenShift

Följande nätverksfunktioner är bara för Azure Red Hat OpenShift:  
* Användare kan skapa sitt ARO-kluster i ett befintligt virtuellt nätverk eller skapa ett virtuellt nätverk när de skapar sina ARO-kluster.
* Pod-och service Network-CIDRs kan konfigureras.
* Noder och hanterare finns i olika undernät.
* Noder och huvud undernät för virtuella nätverk ska vara minst/27.
* Standard-Pod CIDR är 10.128.0.0/14.
* Standard-CIDR för tjänst är 172.30.0.0/16.
* Pod-och service Network-CIDR-data ska inte överlappa andra adress intervall som används i nätverket och får inte ligga inom det virtuella nätverkets IP-adressintervall för klustret.
* Pod CIDR bör vara minst/18 i storlek. (Pod-nätverket är icke-dirigerbart IP-adresser och används bara inuti OpenShift-SDN.)
* Varje nod tilldelas/23 under nätet (512 IP-adresser) för sin poddar. Värdet kan inte ändras.
* Du kan inte koppla en POD till flera nätverk.
* Du kan inte konfigurera utgående statisk IP. (Det här är en OpenShift-funktion. Mer information finns i [Konfigurera utgående IP-adresser](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/assigning-egress-ips.html).

## <a name="network-settings"></a>Nätverksinställningar

Följande nätverks inställningar är tillgängliga för Azure Red Hat OpenShift 4-kluster:

* **API-synlighet** – Ange API-synlighet när du kör [kommandot AZ Aro Create](tutorial-create-cluster.md#create-the-cluster).
    * "Offentlig" – API-servern kan nås av externa nätverk.
    * "Privat"-API-servern har tilldelats en privat IP-adress från huvud under nätet, endast tillgängligt med anslutna nätverk (peer-kopplade virtuella nätverk, andra undernät i klustret). En privat DNS-zon kommer att skapas för kundens räkning.
* **Ingress-synlighet** – Ange API-synlighet när du kör [kommandot AZ Aro Create](tutorial-create-cluster.md#create-the-cluster).
    * "Offentliga" vägar är som standard en offentlig Standard Load Balancer (detta kan ändras).
    * "Privata" vägar kommer att användas som standard till intern belastningsutjämnare (detta kan ändras).

## <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper
Nätverks säkerhets grupper skapas i nodens resurs grupp, som är låst för användare. Nätverks säkerhets grupperna tilldelas direkt till under näten, inte på nodens nätverkskort. Nätverks säkerhets grupperna är oföränderliga och användarna har inte behörighet att ändra dem.

Med en offentligt synlig API-Server kan du inte skapa nätverks säkerhets grupper och tilldela dem till nätverkskorten.

## <a name="domain-forwarding"></a>Domän vidarebefordring
Azure Red Hat OpenShift använder CoreDNS. Domän vidarebefordran kan konfigureras. Du kan inte ta med din egen DNS till dina virtuella nätverk. Mer information finns i dokumentationen om hur du [använder DNS-vidarebefordring](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html#nw-dns-forward_dns-operator).

## <a name="whats-new-in-openshift-45"></a>Vad är nytt i OpenShift 4,5

Med stöd för OpenShift 4,5 introducerade Azure Red Hat OpenShift några viktiga förändringar i arkitekturen. Dessa ändringar gäller endast för nyligen skapade kluster som kör OpenShift 4,5. Befintliga kluster som har uppgraderats till OpenShift 4,5 kommer inte att ha någon nätverks arkitektur ändrad av uppgraderings processen. Användarna måste återskapa sina kluster för att kunna använda den nya arkitekturen.

![Nätverks diagram för Azure Red Hat OpenShift 4,5](./media/concepts-networking/aro-4-5-networking-diagram.png)

Som du ser i diagrammet ovan visas några ändringar:
* Tidigare använde ARO två offentliga belastningsutjämnare: en för API-servern och en för arbets nodens resurspool. Med den här arkitektur uppdateringen har det sammanställts under en enda LoadBalancer. 
* För att minska komplexiteten har dedikerade IP-adressresurser tagits bort.
* ARO-kontroll planet delar nu samma nätverks säkerhets grupp som ARO Worker-noderna.

Mer information om OpenShift 4,5 finns i viktig information om [OpenShift 4,5](https://docs.openshift.com/container-platform/4.5/release_notes/ocp-4-5-release-notes.html).

## <a name="next-steps"></a>Nästa steg
Mer information om utgående trafik och vilka Azure Red Hat OpenShift som har stöd för utgående finns i dokumentationen om [support policys](support-policies-v4.md) .
