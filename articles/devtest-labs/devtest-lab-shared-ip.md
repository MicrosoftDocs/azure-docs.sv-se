---
title: Förstå delade IP-adresser i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur Azure DevTest Labs använder delade IP-adresser för att minimera de offentliga IP-adresser som krävs för att komma åt dina virtuella labb datorer.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 36a5da1b2b6252d0adb480a622c461b33425e675
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85484102"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Förstå delade IP-adresser i Azure DevTest Labs

Med Azure DevTest Labs kan virtuella labb datorer dela samma offentliga IP-adress för att minimera antalet offentliga IP-adresser som krävs för att få åtkomst till dina virtuella datorer i labbet.  Den här artikeln beskriver hur delade IP-adresser fungerar och deras relaterade konfigurations alternativ.

## <a name="shared-ip-setting"></a>Delad IP-inställning

När du skapar ett labb skapas det i ett undernät i ett virtuellt nätverk.  Som standard skapas det här under nätet med **alternativet Aktivera delad offentlig IP** till *Ja*.  Den här konfigurationen skapar en offentlig IP-adress för hela under nätet.  Mer information om hur du konfigurerar virtuella nätverk och undernät finns i [Konfigurera ett virtuellt nätverk i Azure DevTest Labs](devtest-lab-configure-vnet.md).

![Nytt labb under nät](media/devtest-lab-shared-ip/lab-subnet.png)

För befintliga labb kan du aktivera det här alternativet genom att välja **konfiguration och principer > virtuella nätverk**. Välj sedan ett virtuellt nätverk i listan och välj **aktivera delad offentlig IP** för ett valt undernät. Du kan också inaktivera det här alternativet i alla labb om du inte vill dela en offentlig IP-adress för virtuella labb datorer.

Alla virtuella datorer som skapats i den här labb standarden använder en delad IP-adress.  När du skapar den virtuella datorn kan den här inställningen observeras på sidan **Avancerade inställningar** under **IP-adresskonfiguration**.

![Ny virtuell dator](media/devtest-lab-shared-ip/new-vm.png)

- **Delad:** Alla virtuella datorer som har skapats som **delade** placeras i en resurs grupp (RG). En enskild IP-adress tilldelas för den RG och alla virtuella datorer i RG kommer att använda den IP-adressen.
- **Offentlig:** Varje virtuell dator som du skapar har sin egen IP-adress och skapas i en egen resurs grupp.
- **Privat:** Varje virtuell dator som du skapar använder en privat IP-adress. Du kan inte ansluta till den här virtuella datorn direkt från Internet med fjärr skrivbord.

När en virtuell dator med delade IP-adresser läggs till i under nätet lägger DevTest Labs automatiskt till den virtuella datorn i en belastningsutjämnare och tilldelar ett TCP-portnummer på den offentliga IP-adressen, vidarebefordrar till RDP-porten på den virtuella datorn.  

## <a name="using-the-shared-ip"></a>Använda den delade IP-adressen

- **Linux-användare:** SSH till den virtuella datorn med hjälp av IP-adressen eller det fullständigt kvalificerade domän namnet följt av ett kolon följt av porten. I bilden nedan är t. ex. RDP-adressen för att ansluta till den virtuella datorn `mydevtestlab597975021002.eastus.cloudapp.azure.com:50661` .

  ![VM-exempel](media/devtest-lab-shared-ip/vm-info.png)

- **Windows-användare:** Välj knappen **Anslut** på Azure Portal för att ladda ned en förkonfigurerad RDP-fil och komma åt den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

* [Definiera labb principer i Azure DevTest Labs](devtest-lab-set-lab-policy.md)
* [Konfigurera ett virtuellt nätverk i Azure DevTest Labs](devtest-lab-configure-vnet.md)





