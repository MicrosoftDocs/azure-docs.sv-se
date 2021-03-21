---
title: Azure VMware-lösning av CloudSimple-säkerhet för CloudSimple-tjänster
description: Beskriver de delade ansvars modellerna för säkerhet för CloudSimple Services
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d62eec72a884470ade03897a0533e62c6f1b0f71
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97898801"
---
# <a name="cloudsimple-security-overview"></a>CloudSimple säkerhets översikt

Den här artikeln innehåller en översikt över hur säkerhet implementeras på Azure VMware-lösningen av CloudSimple-tjänst, infrastruktur och data Center. Du lär dig mer om data skydd och säkerhet, nätverks säkerhet och hur sårbarheter och korrigeringar hanteras.

## <a name="shared-responsibility"></a>Delat ansvar

Azure VMware-lösningen från CloudSimple använder en delad ansvars modell för säkerhet. Betrodd säkerhet i molnet uppnås genom de delade ansvars områdena hos kunder och Microsoft som en tjänst leverantör. Den här matrisen med ansvar ger högre säkerhet och eliminerar enskilda felpunkter.

## <a name="azure-infrastructure"></a>Azure-infrastruktur

Säkerhets överväganden för Azure-infrastruktur omfattar data Center och utrustnings platsen.

### <a name="datacenter-security"></a>Data Center säkerhet

Microsoft har en hel avdelning för att utforma, skapa och driva fysiska anläggningar som stöder Azure. Det här teamet investeras i att underhålla den avancerade fysiska säkerheten. Mer information om fysisk säkerhet finns i [Azure-anläggningar, lokal och fysisk säkerhet](../security/fundamentals/physical-security.md).

### <a name="equipment-location"></a>Utrustnings plats

Maskin varu utrustningen Bare Metal som kör dina privata moln finns på Azure datacenter-platser.  Burarna där utrustningen är, kräver bio metrisk baserad tvåfaktorautentisering för att få åtkomst.

## <a name="dedicated-hardware"></a>Dedikerad maskinvara

Som en del av CloudSimple-tjänsten får alla CloudSimple-kunder dedikerade Bare Metal-värdar med lokala anslutna diskar som är fysiskt isolerade från andra klient maskin vara. En ESXi-hypervisor med virtuellt San körs på varje nod. Noderna hanteras via kund dedikerade VMware vCenter och NSX. Att inte dela maskin vara mellan klienter ger ett ytterligare lager av isolering och säkerhets skydd.

## <a name="data-security"></a>Datasäkerhet

Kunderna behåller sin data kontroll och ägande rätt. Kundernas data vård är kund informationens ansvar.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Data skydd för data i vila och data i rörelse i interna nätverk

För data i vila i den privata moln miljön kan du använda virtuellt San-kryptering. Virtuellt San-kryptering fungerar med VMware Certified external Key Management-servrar (KMS) i ditt eget virtuella nätverk eller lokalt.  Du styr data krypterings nycklarna själv. För data i rörelse i det privata molnet stöder vSphere kryptering av data via kabeln för all VMkernel-trafik (inklusive vMotion trafik).

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>Data skydd för data som krävs för att flytta via offentliga nätverk

Om du vill skydda data som flyttas via offentliga nätverk kan du skapa IPsec-och TLS VPN-tunnlar för dina privata moln. Vanliga krypterings metoder stöds, inklusive 128-byte och 256-byte AES. Data under överföring (inklusive autentisering, administrativ åtkomst och kunddata) krypteras med standard krypterings metoder (SSH, TLS 1,2 och Secure RDP). Kommunikation som transporterar känslig information använder standard krypterings metoder.

### <a name="secure-disposal"></a>Säker kasse ring

Om din CloudSimple-tjänst går ut eller avslutas, ansvarar du för att ta bort eller ta bort dina data. CloudSimple kommer att samar beta med dig för att ta bort eller returnera alla kund uppgifter som anges i kund avtalet, med undantag för omfattningen CloudSimple krävs enligt gällande lag för att behålla vissa eller alla personliga uppgifter. Om det behövs för att spara personliga data kommer CloudSimple att arkivera data och vidta rimliga åtgärder för att förhindra att kunddata bearbetas ytterligare.

### <a name="data-location"></a>Dataplats

När du konfigurerar dina privata moln väljer du den Azure-region där de ska distribueras. Data för virtuella VMware-datorer flyttas inte från det fysiska data centret om du inte utför datamigrering eller data säkerhets kopiering på annan plats. Du kan också vara värd för arbets belastningar och lagra data i flera Azure-regioner om det behövs.

De kunddata som är bosatta i det privata molnets Hyper-konvergerade noder går inte över platser utan den uttryckliga åtgärden av klient organisations administratören. Det är ditt ansvar att implementera dina arbets belastningar på ett mycket tillgängligt sätt.

### <a name="data-backups"></a>Säkerhetskopior av data

CloudSimple säkerhets kopie ras eller arkiveras inte kund information. CloudSimple utför regelbunden säkerhets kopiering av vCenter-och NSX-data för att tillhandahålla hög tillgänglighet för hanterings servrar. Före säkerhets kopieringen krypteras alla data på vCenter-källan med VMware-API: er. Krypterade data transporteras och lagras i Azure blob. Krypterings nycklar för säkerhets kopior lagras i ett mycket säkert CloudSimple hanterat valv som körs i det virtuella CloudSimple-nätverket i Azure.

## <a name="network-security"></a>Nätverkssäkerhet

CloudSimple-lösningen använder lager av nätverks säkerhet.

### <a name="azure-edge-security"></a>Azure Edge-säkerhet

CloudSimple-tjänsterna skapas ovanpå den grundläggande nätverks säkerhet som tillhandahålls av Azure. Azure använder skydds bara metoder för att upptäcka och reagera på nätverksbaserade attacker som är kopplade till avvikande inkommande eller utgående trafik mönster och DDoS-attacker (distributed denial-of-Service). Den här säkerhets kontrollen gäller för privata moln miljöer och kontroll Plans program vara som har utvecklats av CloudSimple.

### <a name="segmentation"></a>Segmentering

CloudSimple-tjänsten har logiskt separata Layer 2-nätverk som begränsar åtkomsten till dina egna privata nätverk i din privata moln miljö. Du kan skydda dina privata moln nätverk ytterligare med hjälp av en brand vägg. Med CloudSimple-portalen kan du definiera regler för ny och utgående nätverks trafik för all nätverks trafik, inklusive trafik i privata moln, trafik mellan privata moln, allmän trafik till Internet och nätverks trafik till lokalt via IPsec VPN eller ExpressRoute-anslutning.

## <a name="vulnerability-and-patch-management"></a>Sårbarhet och korrigerings hantering

CloudSimple ansvarar för regelbunden säkerhets korrigering av hanterade VMware-program (ESXi, vCenter och NSX).

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

Kunder kan autentisera sig på sina Azure-konton (i Azure AD) med hjälp av Multi-Factor Authentication eller SSO som önskade. Från Azure Portal kan du starta CloudSimple-portalen utan att ange autentiseringsuppgifter igen.

CloudSimple stöder valfri konfiguration av en identitets källa för det privata molnet vCenter. Du kan använda en [lokal identitets källa](set-vcenter-identity.md), en ny identitets källa för det privata molnet eller [Azure AD](azure-ad.md).

Som standard får kunderna de privilegier som krävs för dagliga åtgärder för vCenter i det privata molnet. Den här behörighets nivån inkluderar inte administrativ åtkomst till vCenter. Om administrativ åtkomst tillfälligt krävs kan du [eskalera dina privilegier](escalate-private-cloud-privileges.md) under en begränsad period medan du slutför de administrativa uppgifterna.
