---
title: Lärare åtkomst till virtuella datorer i Azure Lab Services
description: 'Den här artikeln visar hur lärare kan komma åt sina elev-VM: ar från lärare-vyn. Till exempel kan en undervisnings assistent vara en lärare för en klass, men en student för andra klasser.'
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ad4f9cfd11b372e5c6da5c17eaeba82b0cd8a91f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85445108"
---
# <a name="access-virtual-machines-as-a-student-from-the-educator-view"></a>Få åtkomst till virtuella datorer som student från lärare-vyn
Den här artikeln visar hur lärare har åtkomst till sina virtuella datorer för klasser som de deltar i studenter. 

Här är ett scenario där den här funktionen kommer att hjälpa dig. En undervisnings assistent är en lärare för en klass, men en student i andra klasser. Dessutom vill undervisnings assistenten Visa och komma åt student virtuella datorer från lärare-vyn som visar de labb som de äger. 

## <a name="access-vms-from-educator-view"></a>Åtkomst till virtuella datorer från vyn lärare

1. Logga in på [Azure Lab Services webbplats](https://labs.azure.com). Du ser de labb du äger. Dessa labb kan vara labb som du själv har skapat eller de labb som administratören har tilldelat dig som ägare. Mer information finns i [så här lägger du till ytterligare ägare i ett befintligt labb](how-to-add-user-lab-owner.md)
2. Om du vill komma åt virtuella datorer för klasser som du deltar som student väljer du dator ikonen i det övre högra hörnet. Bekräfta att du ser virtuella datorer som du har åtkomst till som student. I följande exempel är användaren en undervisnings assistent för python-labbet, men en student av Java-labbet. Användaren kan alltså se den virtuella datorn från Java-labbet i list rutan. Användaren kan starta den virtuella datorn och ansluta till den. 
    
    ![Åtkomst till student virtuella datorer](./media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Anslut till en virtuell dator](how-to-use-classroom-lab.md#connect-to-the-vm)
- [Ansluta till en virtuell dator med RDP på en Mac](connect-virtual-machine-mac-remote-desktop.md)
- [Ansluta till en virtuell dator med RDP på en Chromebook](connect-virtual-machine-chromebook-remote-desktop.md)
- [Använd fjärr skrivbord för virtuella Linux-datorer](how-to-use-remote-desktop-linux-student.md)
