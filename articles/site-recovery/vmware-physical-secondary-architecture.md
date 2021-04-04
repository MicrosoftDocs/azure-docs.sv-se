---
title: Arkitektur – VMware/fysisk haveri beredskap till en sekundär plats med Azure Site Recovery
description: Den här artikeln innehåller en översikt över komponenter och arkitektur som används vid haveri beredskap för lokala virtuella VMware-datorer eller fysiska Windows/Linux-servrar till en sekundär VMware-plats med Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: d400e6bcda0a2114d798a3289f01f52b677a6f94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97656503"
---
# <a name="architecture-for-vmwarephysical-server-replication-to-a-secondary-on-premises-site"></a>Arkitektur för replikering av VMware/fysisk server till en sekundär lokal plats

Den här artikeln beskriver arkitekturen och processerna som används när du konfigurerar replikering av haveri beredskap, redundans och återställning av lokala virtuella VMware-datorer (VM) eller fysiska Windows/Linux-servrar till en sekundär VMware-plats med hjälp av [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Arkitekturkomponenter

**Område** | **Komponent** | **Information**
--- | --- | ---
**Azure** | Du kan distribuera det här scenariot med InMage Scout. | För att hämta InMage Scout behöver du en Azure-prenumeration.<br/><br/> När du har skapat ett Recovery Services-valv laddar du ned InMage Scout och installerar de senaste uppdateringarna för att konfigurera distributionen.
**Processerver** | Finns på primär plats | Du distribuerar processervern för att hantera cachelagring, komprimering och dataoptimering.<br/><br/> Den hanterar också push-installationen av Unified Agent till de datorer som du vill skydda.
**Konfigurationsserver** | Finns på sekundär plats | Konfigurationsservern hanterar, konfigurerar och övervakar distributionen, antingen med hjälp av hanteringswebbplatsen eller vContinuum-konsolen.
**vContinuum-server** | Valfritt. Installeras på samma plats som konfigurationsservern. | Den innehåller en konsol för att hantera och övervaka din skyddade miljö.
**Huvudmålservern** | Finns på den sekundära platsen | Huvudmålservern innehåller replikerade data. Den tar emot data från processervern, skapar en replikdator på den sekundära platsen och innehåller datakvarhållningspunkterna.<br/><br/> Hur många huvudmålservrar du behöver beror på hur många datorer du skyddar.<br/><br/> Om du vill växla tillbaka till den primära platsen behöver du en huvudmålserver även där. Unified-agenten är installerad på den här servern.
**VMware ESX/ESXi och vCenter-server** |  Virtuella datorer finns på ESX-/ESXi-värdar. Värdar hanteras med en vCenter-server | Du behöver en VMware-infrastruktur för att replikera virtuella VMware-datorer.
**Virtuella datorer/fysiska servrar** |  Unified Agent installeras på virtuella VMware-datorer eller fysiska servrar som du vill replikera. | Agenten fungerar som en kommunikationsprovider mellan alla komponenter.

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

1. Du konfigurerar komponentservrarna på varje plats (konfiguration, process, huvudmålserver) och installerar Unified Agent på de datorer som du vill replikera.
2. Efter den första replikeringen skickar agenten på varje dator förändringsreplikeringar till processervern.
3. Processervern optimerar data och överför dem till huvudmålservern på den sekundära platsen. Konfigurationsservern hanterar replikeringen.

![Diagram som visar replikering av virtuella VMware-datorer och fysiska servrar till ett sekundärt Data Center](./media/site-recovery-components/vmware-to-vmware.png)



## <a name="next-steps"></a>Nästa steg

[Konfigurera](vmware-physical-secondary-disaster-recovery.md) katastrof återställning av virtuella VMware-datorer och fysiska servrar till en sekundär plats.
