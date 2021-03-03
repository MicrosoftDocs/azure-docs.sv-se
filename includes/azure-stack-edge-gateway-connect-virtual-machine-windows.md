---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/04/2020
ms.author: alkohli
ms.openlocfilehash: 6e42fef2aa4415373ed0bac39284f36ba330a4d9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730655"
---
Anslut till din virtuella Windows-dator med hjälp av Remote Desktop Protocol (RDP) via IP-adressen som du skickade när du skapade den virtuella datorn.

1. Öppna RDP på klienten. 
1. Gå till **Start** och skriv **Mstsc**.
1. I fönstret **anslutning till fjärrskrivbord** anger du IP-adressen för den virtuella datorn och de autentiseringsuppgifter som du använde i parameter filen för VM-mallen och väljer sedan **Anslut**.

   ![Skärm bild av Anslutning till fjärrskrivbords fönstret för att ansluta via RDP till din virtuella Windows-dator.](media/azure-stack-edge-gateway-connect-vm-windows/connect-vm-rdp-1.png)

   > [!NOTE]
   > Du kan behöva godkänna att ansluta till en dator som inte är betrodd. 

Du är nu inloggad på den virtuella datorn som körs på-enheten. 
