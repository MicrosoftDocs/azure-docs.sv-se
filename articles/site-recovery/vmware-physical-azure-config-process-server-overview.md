---
title: Om Azure Site Recovery konfiguration/process/huvud mål servrar
description: Den här artikeln innehåller en översikt över konfigurations-, bearbetnings-och huvud mål servrarna med när du konfigurerar haveri beredskap för lokala virtuella VMware-datorer till Azure med Azure Site Recovery
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: cd5ded18d1a8f1f5fd96212d37725bb5db13002f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "80062096"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Om Site Recovery-komponenter (konfiguration, process, huvud mål)

I den här artikeln beskrivs konfigurations-, process-och huvud mål servrar som används av tjänsten [Site Recovery](site-recovery-overview.md) för att replikera virtuella VMware-datorer och fysiska servrar till Azure.

## <a name="configuration-server"></a>Konfigurationsserver

För haveri beredskap för lokala virtuella VMware-datorer och fysiska servrar distribuerar du en lokal Site Recovery konfigurations Server.

**Inställning** | **Information** | **Länkar**
--- | --- | ---
**Komponenter**  | Configuration Server-datorn kör alla lokala Site Recovery-komponenter, som innehåller konfigurations servern, processervern och huvud mål servern.<br/><br/> När du konfigurerar konfigurations servern installeras alla komponenter automatiskt. | [Läs](vmware-azure-common-questions.md#configuration-server) vanliga frågor och svar om konfigurations servern.
**Role** | Konfigurationsservern samordnar kommunikationen mellan den lokala miljön och Azure och hanterar datareplikering. | Lär dig mer om arkitekturen för haveri beredskap för [VMware](vmware-azure-architecture.md) och [fysisk server](physical-azure-architecture.md) till Azure.
**Krav för VMware** | För haveri beredskap för lokala virtuella VMware-datorer måste du installera och köra konfigurations servern som en lokal, virtuell VMware-dator med hög tillgänglighet. | [Läs mer om](vmware-azure-deploy-configuration-server.md#prerequisites) kraven.
**VMware-distribution** | Vi rekommenderar att du distribuerar konfigurations servern med hjälp av en Hämtad mall för ägg. Den här metoden ger dig ett enkelt sätt att konfigurera en konfigurations server som uppfyller alla krav och förutsättningar.<br/><br/> Om du av någon anledning inte kan distribuera en virtuell VMware-dator med en ägg-mall kan du konfigurera konfigurations serverns datorer manuellt, enligt beskrivningen nedan för haveri beredskap för fysiska datorer. | [Distribuera](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template) med en ägg mall.
**Krav för fysisk server** | Vid haveri beredskap på lokala fysiska servrar distribuerar du konfigurations servern manuellt. | [Läs mer om](physical-azure-set-up-source.md#prerequisites) kraven.
**Distribution av fysisk server** | Om den inte kan installeras som en virtuell VMware-dator kan du installera den på en fysisk server. | [Distribuera](physical-azure-set-up-source.md#set-up-the-source-environment) konfigurations servern manuellt.

## <a name="process-server"></a>Processerver

En processerver hanterar replikeringsdata under redundans och återställning och installerar mobilitets tjänsten för lokala virtuella VMware-datorer och fysiska servrar.

**Inställning** | **Information** | **Länkar**
--- | --- | ---
**Distribution**  | Som standard installeras processervern när konfigurations servern distribueras. <br/><br/> En lokal processerver krävs för haveri beredskap och replikering av lokala virtuella VMware-datorer och fysiska servrar. | [Läs mer](vmware-azure-architecture.md#architectural-components).
**Roll (lokalt**) | Tar emot replikeringsdata från datorer som har Aktiver ATS för replikering. <br/><br/> Optimerar replikeringsdata med cachelagring, komprimering och kryptering och skickar dem till Azure Storage. <br/><br/> Utför en push-installation av Site Recovery mobilitets tjänsten på lokala virtuella VMware-datorer och fysiska servrar som du vill replikera. <br/><br/> Utför automatisk identifiering av lokala datorer. | [Läs mer](vmware-azure-enable-replication.md).
**Roll (återställning efter fel från Azure)** | Efter redundansväxlingen från den lokala platsen ställer du in en processerver i Azure som en virtuell Azure-dator för att hantera återställning efter fel till din lokala plats.<br/><br/> Processervern i Azure är tillfällig. Den virtuella Azure-datorn kan tas bort efter att återställning efter fel har gjorts. | [Läs mer](vmware-azure-set-up-process-server-azure.md).
**Skalning** | För större distributioner kan du konfigurera ytterligare och skalbara process servrar på plats. Ytterligare servrar skalar ut kapacitet genom att hantera ett större antal replikerings datorer och större volymer av replikeringstrafik.<br/><br/> Du kan flytta datorer mellan två processervern, för att belastningsutjämna replikeringstrafik. | [Läs mer](vmware-azure-set-up-process-server-scale.md).

## <a name="master-target-server"></a>Huvudmålservern

Huvudmålservern hanterar replikeringsdata vid återställning efter fel från Azure.

- Som standard installeras huvud mål servern på konfigurations servern.
- För stora distributioner kan du lägga till ytterligare en separat huvud mål server för återställning efter fel.

## <a name="next-steps"></a>Nästa steg

- Granska [arkitekturen](vmware-azure-architecture.md) för haveri beredskap för virtuella VMware-datorer och fysiska servrar.
- Granska [kraven och förutsättningarna](vmware-physical-azure-support-matrix.md) för haveri beredskap för virtuella VMware-datorer och fysiska servrar till Azure.
