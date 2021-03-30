---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: fe5daa38c43723c85fb464e191ee4a3e85700e0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "67187299"
---
1. När den virtuella Azure-datorn har skapats och körs klickar du på symbolen för virtuella datorer på Azure Portal för att visa dina virtuella datorer.

1. Klicka på de tre punkterna **...** vid den nya virtuella datorn.

1. Klicka på **Anslut**.

   ![Anslut till virtuell dator i portalen](./media/virtual-machines-sql-server-remote-desktop-connect/azure-virtual-machine-connect.png)

1. Öppna **RDP**-filen som du har laddat ned för den virtuella datorn.

1. Anslutning till fjärrskrivbord meddelar dig att det inte går att identifiera utgivaren av fjärranslutningen. Anslut genom att klicka på **Anslut**.

1. I dialogrutan **Windows-säkerhet** klickar du på **Använd ett annat konto**. Du måste kanske klicka på **Fler alternativ** för att se det här. Ange det användarnamn och lösenord du ställde in när du skapade den virtuella datorn. Du måste lägga till ett omvänt snedstreck framför användarnamnet.

   ![Fjärrskrivbordsautentisering](./media/virtual-machines-sql-server-remote-desktop-connect/remote-desktop-connect.png)

1. Klicka på **OK** för att ansluta.
