---
title: 'Azure-skydds: Visa virtuell dator-session: hel skärms läge'
description: Lär dig hur du ändrar vyn för virtuella datorer till full skärm och tillbaka i webbläsaren för en RDP-eller SSH-anslutning i Azure skydds.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 7db85c5e1d5f67c30adf08969c51bb4c57838fb5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92079147"
---
# <a name="change-to-full-screen-view-for-a-vm-session-azure-bastion"></a>Ändra till hel skärms läge för en VM-session: Azure skydds

Den här artikeln hjälper dig att ändra den virtuella datorns vy till full skärm och tillbaka i webbläsaren. Innan du arbetar med en virtuell dator bör du kontrol lera att du har följt stegen för att [skapa en skydds-värd](./tutorial-create-host-portal.md). Anslut sedan till den virtuella dator som du vill arbeta med via [RDP](bastion-connect-vm-rdp.md) eller [SSH](bastion-connect-vm-ssh.md).

## <a name="launch-the-clipboard-tool"></a>Starta urklipps verktyget

Under fjärrsessionen startar du skydds urklipps åtkomst verktyg genom att välja de två pilarna som finns längst till vänster i sessionen.

![tools](./media/bastion-vm-manage/left.png)

## <a name="select-full-screen"></a>Välj hel skärms läge

Välj knappen **hel** skärm för att växla sessionen till en hel skärms upplevelse. När du har växlat ominitieras sessionen till full skärm.

![helskärm](./media/bastion-vm-manage/full-screen.png)
 
## <a name="next-steps"></a>Nästa steg

Läs [vanliga frågor och svar om skydds](bastion-faq.md).
Lär dig hur du [kopierar och klistrar in](bastion-vm-copy-paste.md) till och från en virtuell Azure-dator.
