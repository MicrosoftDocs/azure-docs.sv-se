---
title: Konfigurera en processor Server VMware/fysisk återställning efter fel i Azure Site Recovery
description: Den här artikeln beskriver hur du konfigurerar en processerver i Azure för att återställa virtuella Azure-datorer till VMware.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: ramamill
ms.openlocfilehash: 16214fa07b14c8db2f32fa2c69739b7fa3dc1907
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96008503"
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>Konfigurera en processerver i Azure för återställning efter fel

När du har växlat över virtuella VMware-datorer eller fysiska servrar till Azure med [Site Recovery](site-recovery-overview.md)kan du återställa dem till den lokala platsen när den är igång igen. För att återställa igen måste du konfigurera en tillfällig processerver i Azure för att hantera replikering från Azure till lokala enheter. Du kan ta bort den här virtuella datorn efter att återställning efter fel har slutförts.

## <a name="before-you-start"></a>Innan du börjar

Läs mer om [återställnings](vmware-azure-failback.md) [-och](vmware-azure-reprotect.md) återställnings processen.

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]


## <a name="deploy-a-process-server-in-azure"></a>Distribuera en processerver i Azure

1. I valvet > **Site Recovery infrastruktur** >  **Hantera**  >  **konfigurations servrar** väljer du konfigurations servern.
2. På sidan Server klickar du på **+ processerver**
3. På sidan **Lägg till processerver** och välj att distribuera processervern i Azure.
4. Ange Azure-inställningar, inklusive den prenumeration som används för redundans, en resurs grupp, Azure-regionen som används för redundans och det virtuella nätverk där de virtuella Azure-datorerna finns. Om du har använt flera Azure-nätverk behöver du en processerver i var och en.

   ![Lägg till Server Galleri objekt för processervern](./media/vmware-azure-set-up-process-server-azure/add-ps-page-1.png)

4. I **Server namn**, **användar namn** och **lösen ord** anger du ett namn för processervern och autentiseringsuppgifter som ska tilldelas administratörs behörighet på servern.
5. Ange ett lagrings konto som ska användas för de virtuella server diskarna, under nätet där den virtuella datorns virtuella process Server ska placeras och serverns IP-adress som ska tilldelas när den virtuella datorn startas.
6. Klicka på **OK** för att börja distribuera den virtuella process servern. Processervern kommer att distribueras på Standard_A8_v2 SKU. Se till att den här VM-SKU: n är tillgänglig för din prenumeration.

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Registrera processervern (körs i Azure) till en konfigurations Server (körs lokalt)

När den virtuella process Server-datorn är igång måste du registrera den med den lokala konfigurations servern på följande sätt:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]


