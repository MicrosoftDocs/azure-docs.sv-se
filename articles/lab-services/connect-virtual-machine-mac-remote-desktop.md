---
title: Så här ansluter du till en Azure Lab Services virtuell dator från Mac | Microsoft Docs
description: Lär dig hur du ansluter från en Mac till en virtuell dator i Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 674bea13093e185ae991148c2cf96599a273d0bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85444632"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-mac"></a>Ansluta till en virtuell dator med hjälp av Remote Desktop Protocol på en Mac
Det här avsnittet visar hur en student kan ansluta till en virtuell dator i klass rummet från en Mac med hjälp av RDP.

## <a name="install-microsoft-remote-desktop-on-a-mac"></a>Installera Microsoft Fjärrskrivbord på en Mac
1. Öppna App Store på din Mac och Sök efter **Microsoft fjärrskrivbord**.

    ![Microsoft Fjärrskrivbord](./media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. Installera den senaste versionen av Microsoft Fjärrskrivbord. 

## <a name="access-the-vm-from-your-mac-using-rdp"></a>Få åtkomst till den virtuella datorn från din Mac med hjälp av RDP
1. Öppna den **RDP** -fil som har laddats ned på datorn med **Microsoft fjärrskrivbord** installerat. Den ska börja ansluta till den virtuella datorn. 

    ![Ansluta till virtuell dator](./media/how-to-use-classroom-lab/connect-linux-vm.png)
1. Välj **Fortsätt** om du får följande varning. 

    ![Certifikat varning](./media/how-to-use-classroom-lab/certificate-error.png)
1. Du bör se den virtuella datorn. 

    > [!NOTE]
    > Följande exempel gäller för en virtuell CentOS Linux-dator. 

    ![Virtuell dator](./media/how-to-use-classroom-lab/vm-ui.png)


## <a name="next-steps"></a>Nästa steg
Information om hur du ansluter till virtuella Linux-datorer med RDP finns i [använda fjärr skrivbord för virtuella Linux-datorer](how-to-use-remote-desktop-linux-student.md)


