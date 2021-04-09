---
title: Konfigurera konfigurations servern för haveri beredskap för fysiska servrar till Azure med hjälp av Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar den lokala konfigurations servern för haveri beredskap för lokala fysiska servrar till Azure.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: ramamill
ms.openlocfilehash: 327e995a8fe2f66903548fba054804768d2538ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96001579"
---
# <a name="set-up-the-configuration-server-for-disaster-recovery-of-physical-servers-to-azure"></a>Konfigurera konfigurationsservern för haveriberedskap för fysiska servrar till Azure

Den här artikeln beskriver hur du konfigurerar din lokala miljö så att du kan börja replikera fysiska servrar som kör Windows eller Linux till Azure.

## <a name="prerequisites"></a>Förutsättningar

Artikeln förutsätter att du redan har:
- Ett Recovery Services valv i [Azure Portal](https://portal.azure.com "Azure Portal").
- En fysisk dator där konfigurations servern ska installeras.
- Om du har inaktiverat TLS 1,0 på den dator där du installerar konfigurations servern kontrollerar du att TLs 1,2 är aktiverat och att .NET Framework version 4,6 eller senare är installerat på datorn (med stark kryptering aktiverat). [Läs mer](https://support.microsoft.com/help/4033999/how-to-resolve-azure-site-recovery-agent-issues-after-disabling-tls-1).

### <a name="configuration-server-minimum-requirements"></a>Minimi krav för konfigurations Server
I följande tabell visas minimi kraven för maskin vara, program vara och nätverk för en konfigurations Server.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> HTTPS-baserade proxyservrar stöds inte av konfigurations servern.

## <a name="choose-your-protection-goals"></a>Välj skydds mål

1. I Azure Portal går du till bladet **Recovery Services** valv och väljer ditt valv.
2. I menyn **resurs** för valvet klickar du på **komma igång**  >  **Site Recovery**  >  **steg 1: Förbered infrastruktur**  >  **skydds mål**.

    ![Skärm bild som visar var du väljer skydds målet.](./media/physical-azure-set-up-source/choose-goals.png)
3. I **skydds mål** väljer **du till Azure** och **inte virtualiserat/övrigt** och klickar sedan på **OK**.

    ![Välja mål](./media/physical-azure-set-up-source/physical-protection-goal.png)

## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

1. I **Förbered källa**, om du inte har en konfigurations Server, klickar du på **+ konfigurations Server** för att lägga till en.

   ![Skärm bild som visar hur du väljer konfigurations servern.](./media/physical-azure-set-up-source/plus-config-srv.png)
2. I bladet **Lägg till Server** kontrollerar du att **konfigurations servern** visas i **Server typ**.
4. Hämta installations filen Site Recovery Unified setup.
5. Ladda ned valvregistreringsnyckeln. Du behöver registrerings nyckeln när du kör enhetlig installation. Nyckeln är giltig i fem dagar efter att du har genererat den.

    ![Konfigurera källan](./media/physical-azure-set-up-source/set-source2.png)
6. På den dator som du använder som konfigurations server kör **Azure Site Recovery enhetlig installation** för att installera konfigurations servern, processervern och huvud mål servern.

#### <a name="run-azure-site-recovery-unified-setup"></a>Kör Azure Site Recovery enhetlig installation

> [!TIP]
> Registreringen av konfigurations servern Miss lyckas om tiden på datorns system klocka är mer än fem minuter av lokal tid. Synkronisera system klockan med en [tids Server](/windows-server/networking/windows-time-service/windows-time-service-top) innan du påbörjar installationen.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Konfigurations servern kan installeras via en kommando rad. [Läs mer](physical-manage-configuration-server.md#install-from-the-command-line).


## <a name="common-issues"></a>Vanliga problem

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Nästa steg

Nästa steg innebär [att konfigurera mål miljön](physical-azure-set-up-target.md) i Azure.
