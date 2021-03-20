---
title: Katastrof återställnings arkitektur för fysiska servrar i Azure Site Recovery
description: Den här artikeln innehåller en översikt över komponenter och arkitektur som används vid haveri beredskap för lokala fysiska servrar till Azure med tjänsten Azure Site Recovery.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 966636e269043d81912b552711635f34b412f22f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97654735"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Haveriberedskapsarkitektur för fysisk server till Azure

Den här artikeln beskriver arkitekturen och processerna som används när du replikerar, växlar över och återställer fysiska Windows-och Linux-servrar mellan en lokal plats och Azure med hjälp av tjänsten [Azure Site Recovery](site-recovery-overview.md) .

## <a name="architectural-components"></a>Arkitekturkomponenter

Följande tabell och bild ger en övergripande bild av de komponenter som används för replikering av fysiska servrar till Azure.

| **Komponent** | **Krav** | **Information** |
| --- | --- | --- |
| **Azure** | En Azure-prenumeration och ett Azure-nätverk. | Replikerade data från lokala fysiska datorer lagras på Azure Managed disks. Virtuella Azure-datorer skapas med replikerade data när du kör en redundansväxling från en lokal plats till Azure. Virtuella Azure-datorer ansluter till det virtuella Azure-nätverket när de skapas. |
| **Processerver** | Installeras som standard tillsammans med konfigurations servern. | Fungerar som en replikeringsgateway. Den tar emot replikeringsdata, optimerar dem med cachelagring, komprimering och kryptering och skickar dem till Azure Storage.<br/><br/> Processervern installerar också mobilitets tjänsten på de servrar som du vill replikera.<br/><br/> När distributionen växer kan du lägga till ytterligare separata process servrar för att hantera större volymer av replikeringstrafiken. |
| **Huvudmålservern** | Installeras som standard tillsammans med konfigurations servern. | Hanterar replikeringsdata vid återställning från Azure.<br/><br/> För stora distributioner kan du lägga till ytterligare en separat huvud mål server för återställning efter fel. |
| **Replikerade servrar** | Mobilitets tjänsten är installerad på varje server som du replikerar. | Vi rekommenderar att du tillåter automatisk installation från processervern. Du kan också installera tjänsten manuellt eller använda en automatiserad distributions metod som Configuration Manager. |

**Arkitektur för fysisk till Azure**

![Komponenter](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="set-up-outbound-network-connectivity"></a>Konfigurera utgående nätverks anslutning

För att Site Recovery ska fungera som förväntat måste du ändra den utgående nätverks anslutningen så att din miljö kan replikeras.

> [!NOTE]
> Site Recovery har inte stöd för en autentiseringsproxy för att styra nätverksanslutningar.

### <a name="outbound-connectivity-for-urls"></a>Utgående anslutning för webbadresser

Om du använder en URL-baserad brand Väggs-proxy för att kontrol lera utgående anslutning ger du åtkomst till följande URL: er:

| **Namn**                  | **Kommersiellt**                               | **Myndigheter**                                 | **Beskrivning** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Storage                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Gör att data kan skrivas från den virtuella datorn till cachelagringskontot i källregionen. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Tillhandahåller auktorisering och autentisering för Site Recovery-tjänstens webbadresser. |
| Replikering               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Låter den virtuella datorn kommunicera med Site Recovery-tjänsten. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Låter den virtuella datorn skriva övervaknings- och diagnostikdata för Site Recovery. |


## <a name="replication-process"></a>Replikeringsprocessen

1. Du konfigurerar distributionen, inklusive lokala och Azure-komponenter. I Recovery Services valvet anger du replikeringens källa och mål, konfigurerar konfigurations servern, skapar en replikeringsprincip och aktiverar replikering.
1. Datorer replikeras med replikeringsprincipen och en ursprunglig kopia av Server data replikeras till Azure Storage.
1. När den inledande replikeringen har slutförts börjar replikeringen av delta ändringar till Azure. Spårade ändringar för en dator lagras i en fil med fil namns tillägget _. HRL_ .
   - Datorer kommunicerar med konfigurations servern på HTTPS-port 443 inkommande, för hantering av replikering.
   - Datorer skickar replikeringsdata till processervern på HTTPS-port 9443 inkommande (kan ändras).
   - Konfigurations servern dirigerar hantering av replikering med Azure över HTTPS-port 443 utgående.
   - Processervern tar emot data från käll datorer, optimerar och krypterar den och skickar den till Azure Storage via HTTPS-port 443 utgående.
   - Om du aktiverar konsekvens för flera virtuella datorer kommunicerar datorer i replikeringsgruppen med varandra på port 20004. Multi-VM används om du grupperar flera datorer i replikeringsgrupper som delar kraschkonsekventa och app-konsekventa återställningspunkter när de växlar över vid fel. Dessa grupper är användbara om datorer kör samma arbets belastning och måste vara konsekvent.
1. Trafik replikeras till offentliga Azure Storage-slutpunkter, över Internet. Du kan även använda Azure ExpressRoute [offentlig peering](../expressroute/about-public-peering.md).

   > [!NOTE]
   > Replikering stöds inte för plats-till-plats-VPN från en lokal plats eller Azure ExpressRoute [privat peering](concepts-expressroute-with-site-recovery.md#on-premises-to-azure-replication-with-expressroute).

**Process för fysisk till Azure-replikering**

![Replikeringsprocessen](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processen för redundans och återställning efter fel

När replikeringen har kon figurer ATS kan du köra en haveri beredskap (testa redundans) för att kontrol lera att allt fungerar som förväntat. Sedan kan du växla över och återställa efter behov. Överväg följande objekt:

- Planerad redundans stöds inte.
- Växla tillbaka till en lokal virtuell VMware-dator krävs. Du behöver en lokal VMware-infrastruktur, även när du replikerar lokala fysiska servrar till Azure.
- Du växlar över en enskild dator eller skapar återställnings planer för att växla över flera datorer tillsammans.
- När du kör en redundansväxling skapas virtuella Azure-datorer från replikerade data i Azure Storage.
- När den inledande redundansväxlingen har utlösts, så genomför du den för att komma igång med att komma åt arbets belastningen från den virtuella Azure-datorn.
- När din primära lokala plats är tillgänglig igen, kan du återställa dit.
- Konfigurera en infrastruktur för återställning efter fel som innehåller:
  - **Tillfällig processerver i Azure**: om du vill återställa från Azure konfigurerar du en virtuell Azure-dator så att den fungerar som en processerver för att hantera replikering från Azure. Du kan ta bort den här virtuella datorn efter att återställningen har slutförts.
  - **VPN-anslutning**: om du vill återställa behöver du en VPN-anslutning (eller Azure-ExpressRoute) från Azure-nätverket till den lokala platsen.
  - **Separat huvud mål server**: återställningen återställs som standard av huvud mål servern som installerades med konfigurations servern på den lokala virtuella VMware-datorn. Om du behöver återställa stora mängder trafik måste du konfigurera en separat lokal huvud mål server.
  - **Återställningsprincip**: Om du vill replikera tillbaka till din lokala plats behöver du en återställningsprincip. Principen skapades automatiskt när du skapade replikeringsprincipen från lokal plats till Azure.
  - **VMware-infrastruktur**: om du vill återställa behöver du en VMware-infrastruktur. Du kan inte växla tillbaka till en fysisk server.
- När komponenterna har placerats på plats sker en återställning i tre steg:
  - **Steg 1**: skydda virtuella datorer i Azure så att de replikeras från Azure tillbaka till lokala virtuella VMware-datorer.
  - **Steg 2**: kör en redundansväxling till den lokala platsen.
  - **Steg 3**: när arbets belastningarna har misslyckats igen återaktiverar du replikeringen.

**VMware-återställning från Azure**

![Återställning efter fel](./media/physical-azure-architecture/enhanced-failback.png)

## <a name="next-steps"></a>Nästa steg

Information om hur du konfigurerar haveri beredskap för fysiska servrar till Azure finns i [instruktions guiden](physical-azure-disaster-recovery.md).
