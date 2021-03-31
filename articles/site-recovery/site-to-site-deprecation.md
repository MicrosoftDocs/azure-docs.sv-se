---
title: Utfasning av haveri beredskap mellan Kundhanterade platser (med VMM) med Azure Site Recovery | Microsoft Docs
description: Information om kommande utfasning av DR mellan kundägda webbplatser med Hyper-V och mellan platser som hanteras av SCVMM till Azure och alternativa alternativ
services: site-recovery
author: Sharmistha-Rai
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2020
ms.author: sharrai
ms.openlocfilehash: 9ffe7a3158b1de6828350947dcf81ef41d08708d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87421849"
---
# <a name="deprecation-of-disaster-recovery-between-customer-managed-sites-with-vmm-using-azure-site-recovery"></a>Utfasning av haveri beredskap mellan Kundhanterade platser (med VMM) med hjälp av Azure Site Recovery

Den här artikeln beskriver den kommande avskrivnings planen, motsvarande konsekvenser och de alternativa alternativ som är tillgängliga för kunderna i följande scenario:

DR mellan kundägda webbplatser som hanteras av System Center Virtual Machine Manager (SCVMM) med Site Recovery

> [!IMPORTANT]
> Kunderna uppmanas att vidta åtgärder för att åtgärda det tidigaste för att undvika störningar i deras miljö. 

## <a name="what-changes-should-you-expect"></a>Vilka ändringar bör du förvänta dig?

- Från och med mars 2020 får du Azure Portal meddelanden & e-postkommunikation med den kommande utfasningen av plats-till-plats-replikering av virtuella Hyper-V-datorer. Utfasningen planeras för mars 2023.

- Om du har en befintlig konfiguration påverkas inte konfigurationen.

- När scenarierna är inaktuella, såvida inte kunden följer de alternativa metoderna, kan de befintliga replikeringarna bli störande. Kunder kan inte Visa, hantera eller utföra några DR-relaterade åtgärder via Azure Site Recovery upplevelse i Azure Portal.
 
## <a name="alternatives"></a>Alternativ 

Nedan visas de alternativ som kunden kan välja mellan för att säkerställa att deras DR-strategi inte påverkas när scenariot är föråldrat. 

- Alternativ 1 (rekommenderas): Välj att [börja använda Azure som Dr-mål](hyper-v-vmm-azure-tutorial.md).


- Alternativ 2: Välj att fortsätta med plats-till-plats-replikering med den underliggande [lösningen för Hyper-V-replikering](/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica), men du kan inte hantera Dr-konfigurationer med Azure Site Recovery i Azure Portal. 


## <a name="remediation-steps"></a>Reparationssteg

Om du väljer att gå med alternativ 1 ska du köra följande steg:

1. [Inaktivera skyddet av alla virtuella datorer som är kopplade till VMMS](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario). Använd alternativet **inaktivera replikering och ta bort** eller kör skripten som nämns för att se till att replikeringsinställningarna är rensade. 

2. [Avregistrera alla VMM-servrar](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) från konfiguration av plats-till-plats-replikering.

3. [Förbered Azure-resurser](tutorial-prepare-azure-for-hyperv.md) för att aktivera replikering av dina virtuella datorer.
4. [Förbereda lokala Hyper-V-servrar](hyper-v-prepare-on-premises-tutorial.md)
5. [Konfigurera replikering för de virtuella datorerna i VMM-molnet](hyper-v-vmm-azure-tutorial.md)
6. Valfritt men rekommenderat: [kör en Dr-granskning](tutorial-dr-drill-azure.md)

Om du väljer att gå med alternativ 2 för att använda Hyper-V-replikering utför du följande steg:

1. I **skyddade objekt**  >  **replikerade objekt** högerklickar du på datorn > **inaktiverar replikering**.
2. I **inaktivera replikering** väljer du **ta bort**.

    Detta tar bort det replikerade objektet från Azure Site Recovery (faktureringen stoppas). Det **går inte** att rensa konfigurationen på den lokala virtuella datorn. 

## <a name="next-steps"></a>Nästa steg
Planera för utfasningen och välj ett alternativt alternativ som passar din infrastruktur och ditt företag bäst. Om du har frågor om detta kan du kontakta Microsoft Support

