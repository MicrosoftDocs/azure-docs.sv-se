---
title: Konfigurera käll inställningar för VMware haveri beredskap till Azure med Azure Site Recovery
description: Den här artikeln beskriver hur du konfigurerar den lokala miljön för att replikera virtuella VMware-datorer till Azure med Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: afd3979690b8952c915a49099ee04b3d416031fd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88189740"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Konfigurera käll miljön för VMware till Azure-replikering

Den här artikeln beskriver hur du konfigurerar din lokala lokala miljö för att replikera virtuella VMware-datorer till Azure. Artikeln innehåller steg för att välja ett scenario för replikering, konfigurera en lokal dator som Site Recovery konfigurations Server och identifiera lokala virtuella datorer automatiskt.

## <a name="prerequisites"></a>Förutsättningar

Artikeln förutsätter att du redan har:

- Planeringen av distributionen med hjälp av [Distributionshanteraren för Azure Site Recovery](site-recovery-deployment-planner.md). Detta hjälper dig att allokera tillräckligt med bandbredd, baserat på din dagliga data ändrings hastighet, för att uppfylla önskat mål för återställnings punkt.
- [Konfigurera resurser](tutorial-prepare-azure.md) i [Azure Portal](https://portal.azure.com).
- [Konfigurera lokal VMware](vmware-azure-tutorial-prepare-on-premises.md), inklusive ett dedikerat konto för automatisk identifiering.

## <a name="choose-your-protection-goals"></a>Välj skydds mål

1. Välj valvets namn i **Recovery Services-valv**. I det här scenariot använder vi namnet **ContosoVMVault**.
2. I **Komma igång** väljer du Site Recovery. Välj sedan **Förbered infrastrukturen**.
3. I **skydds mål**  >  **där** finns datorerna väljer du **lokalt**.
4. I **Till vilken plats ska dina datorer replikeras?** väljer du **Till Azure**.
5. I **Är dina datorer virtualiserade?** väljer du **Ja, med VMware vSphere Hypervisor**. Välj sedan **OK**.

## <a name="set-up-the-configuration-server"></a>Konfigurera konfigurations servern

Du kan ställa in konfigurations servern som en lokal virtuell VMware-dator via en mall för öppen Virtualization-program (ägg). [Läs mer](./vmware-azure-architecture.md) om de komponenter som kommer att installeras på den virtuella VMware-datorn.

1. Läs om [kraven](vmware-azure-deploy-configuration-server.md#prerequisites) för distribution av konfigurations Server.
2. [Kontrol lera kapacitets nummer](vmware-azure-deploy-configuration-server.md#sizing-and-capacity-requirements) för distribution.
3. [Hämta](vmware-azure-deploy-configuration-server.md#download-the-template) och [Importera](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) den äggbaserade mallen för att konfigurera en lokal virtuell VMware-dator som kör konfigurations servern. Den licens som tillhandahölls med mallen är en utvärderings licens och är giltig i 180 dagar. Publicera den här perioden måste kunden aktivera Windows med en tillskaffad licens.
4. Aktivera den virtuella VMware-datorn och [registrera den](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) i Recovery Services valvet.

## <a name="azure-site-recovery-folder-exclusions-from-antivirus-program"></a>Azure Site Recovery mapp undantag från antivirus program

### <a name="if-antivirus-software-is-active-on-source-machine"></a>Om antivirus programmet är aktivt på käll datorn

Om käll datorn har ett antivirus program aktiverat, ska installationsmappen uteslutas. Så undantar du mappen *C:\ProgramData\ASR\agent* för mjuk replikering.

### <a name="if-antivirus-software-is-active-on-configuration-server"></a>Om antivirus programmet är aktivt på konfigurations servern

Undanta följande mappar från antivirus program för smidig replikering och Undvik anslutnings problem

- C:\Program\Microsoft Azure Recovery Services agent.
- C:\Program\Microsoft Azure Site Recovery-Provider
- C:\Program\Microsoft Azure Site Recovery Configuration Manager 
- C:\Program\Microsoft Azure Site Recovery-verktyget för fel insamling 
  - C:\thirdparty
  - C:\Temp
  - C:\strawberry
  - C:\ProgramData\MySQL
  - C:\Program Files (x86) \MySQL
  - C:\ProgramData\ASR
  - C:\ProgramData\Microsoft Azure Site Recovery
  - C:\ProgramData\ASRLogs
  - C:\ProgramData\ASRSetupLogs
  - C:\ProgramData\LogUploadServiceLogs
  - C:\inetpub
  - Installations katalog för Site Recovery Server. Till exempel: E:\Program-filer (x86) \Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-scale-out-process-servermaster-target"></a>Om antivirus program är aktiva på skalbar Processerver/huvud mål

Undanta följande mappar från antivirus program

1. C:\Program\Microsoft Azure Recovery Services agent
2. C:\ProgramData\ASR
3. C:\ProgramData\ASRLogs
4. C:\ProgramData\ASRSetupLogs
5. C:\ProgramData\LogUploadServiceLogs
6. C:\ProgramData\Microsoft Azure Site Recovery
7. Azure Site Recovery installations katalog för belastningsutjämnad process Server, exempel: C:\Program Files (x86) \Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-the-linux-master-target"></a>Om antivirus program är aktiva på Linux-huvudmålet

Undanta följande mappar från antivirus program

1.  /usr/local/ASR
2.  /usr/local/InMage
3.  /var/log/vxlogs
4.  /var/log
5.  /var/log/ApplicationPolicyLogs
6.  /var/log/ASRsetuptelemetry
7.  /var/log/ASRsetuptelemetry_uploaded


## <a name="next-steps"></a>Nästa steg
[Konfigurera mål miljön](./vmware-azure-set-up-target.md) 
