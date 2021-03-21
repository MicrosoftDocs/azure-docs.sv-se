---
title: Ansluta till en virtuell Windows-dator med Azure skydds
description: I den här artikeln får du lära dig hur du ansluter till en virtuell Azure-dator som kör Windows med hjälp av Azure-skydds.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/21/2020
ms.author: cherylmc
ms.openlocfilehash: 708bd1f61da2f3973333f8e68cabdceee0717bee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92521550"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Ansluta till en virtuell Windows-dator med Azure skydds

Med hjälp av Azure skydds kan du på ett säkert och smidigt sätt ansluta till dina virtuella datorer via SSL direkt i Azure Portal. När du använder Azure skydds kräver inte de virtuella datorerna någon klient, agent eller ytterligare program vara. Den här artikeln visar hur du ansluter till dina virtuella Windows-datorer. Information om hur du ansluter till en virtuell Linux-dator finns i [ansluta till en virtuell Linux-dator](bastion-connect-vm-ssh.md).

Azure skydds ger säker anslutning till alla virtuella datorer i det virtuella nätverk där det är etablerad. Med hjälp av Azure skydds kan du skydda dina virtuella datorer från att exponera RDP/SSH-portar till utsidan, samtidigt som du ger säker åtkomst med RDP/SSH. Mer information finns i [Vad är Azure skydds?](bastion-overview.md).

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du kontrol lera att du har uppfyllt följande kriterier:

* Ett VNet med skydds-värden har redan installerats.

   Kontrol lera att du har konfigurerat en Azure skydds-värd för det virtuella nätverk där den virtuella datorn finns. När skydds-tjänsten har tillhandahållits och distribuerats i det virtuella nätverket kan du använda den för att ansluta till en virtuell dator i det virtuella nätverket. Information om hur du konfigurerar en Azure skydds-värd finns i [skapa en skydds-värd](tutorial-create-host-portal.md#createhost).
* En virtuell Windows-dator i det virtuella nätverket.
* Följande nödvändiga roller:
  * Rollen läsare på den virtuella datorn.
  * Rollen läsare på NÄTVERKSKORTet med den virtuella datorns privata IP-adress.
  * Rollen läsare på Azure skydds-resursen.
* Portar: för att ansluta till den virtuella Windows-datorn måste du ha följande portar öppna på din virtuella Windows-dator:
  * Inkommande portar: RDP (3389)

## <a name="connect"></a><a name="rdp"></a>Ansluta

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]
 
## <a name="next-steps"></a>Nästa steg

Läs [vanliga frågor och svar om skydds](bastion-faq.md).