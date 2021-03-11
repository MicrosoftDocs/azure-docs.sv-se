---
title: Snabb start – skapa en virtuell Windows-dator i Azure Portal
description: I den här snabbstarten lär du dig hur du skapar en virtuell Windows-dator med hjälp av Azure Portal
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 11/05/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 5e52fab081a94ad58e91c629f4092ae889d38e7a
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102560930"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-the-azure-portal"></a>Snabbstart: Skapa en virtuell Windows-dator i Azure Portal

Det går att skapa virtuella Azure-datorer via Azure Portal. Med den här metoden får du ett webbläsarbaserat användargränssnitt för att skapa virtuella datorer och alla relaterade resurser. Den här snabb starten visar hur du använder Azure Portal för att distribuera en virtuell dator (VM) i Azure som kör Windows Server 2019. För att se hur den virtuella datorn fungerar i praktiken ansluter du till den med RDP och installerar IIS-webbservern.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

1. Skriv **virtuella datorer** i sökningen.
1. Under **tjänster** väljer du **virtuella datorer**.
1. På sidan **virtuella datorer** väljer du **Lägg till**. 
1. På fliken **Grundläggande inställningar** går du till **Projektinformation**, kontrollerar att korrekt prenumeration har valts och väljer sedan **Skapa ny** för resursgruppen. Skriv *myResourceGroup* som namn. 

    ![Skärm bild av avsnittet projekt information som visar var du väljer Azure-prenumerationen och resurs gruppen för den virtuella datorn](./media/quick-create-portal/project-details.png)

1. Under **instans information** skriver du *myVM* för namnet på den **virtuella datorn** och väljer *östra USA* för din **region** och väljer sedan *Windows Server 2019 Data Center* för **avbildningen**. Låt de övriga standardvärdena vara som de är.

    ![Skärm bild av avsnittet instans information där du anger ett namn för den virtuella datorn och väljer dess region, bild och storlek](./media/quick-create-portal/instance-details.png)

1. Under **Administratörskonto**, anger du ett användarnamn, som *azureuser*, och ett lösenord. Lösen ordet måste vara minst 12 tecken långt och uppfylla de [definierade komplexitets kraven](faq.md#what-are-the-password-requirements-when-creating-a-vm).

    ![Skärm bild av avsnittet administratörs konto där du anger administratörens användar namn och lösen ord](./media/quick-create-portal/administrator-account.png)

1. Under **regler för inkommande port** väljer du **Tillåt valda portar** och väljer sedan **RDP (3389)** och **http (80)** i list rutan.

    ![Skärm bild av avsnittet regler för inkommande port där du väljer vilka portar som inkommande anslutningar tillåts på](./media/quick-create-portal/inbound-port-rules.png)

1. Låt de återstående standardinställningarna vara och välj sedan knappen **Granska + skapa** längst ned på sidan.

    ![Skärm bild som visar knappen granska och skapa längst ned på sidan](./media/quick-create-portal/review-create.png)


## <a name="connect-to-virtual-machine"></a>Ansluta till den virtuella datorn

Skapa en fjärrskrivbordsanslutning till den virtuella datorn. Dessa instruktioner förklarar hur du ansluter till den virtuella datorn från en Windows-dator. På en Mac-dator behöver du en RDP-klient som denna [Fjärrskrivbordsklient](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) från Mac App Store.

1. Välj knappen **Anslut** på sidan Översikt för den virtuella datorn. 

    ![Skärm bild av översikts sidan för den virtuella datorn som visar platsen för knappen Anslut](./media/quick-create-portal/portal-quick-start-9.png)
    
2. På sidan **Anslut till virtuell dator** ska du behålla standard alternativen för att ansluta via IP-adress, via port 3389 och klicka på **Hämta RDP-fil**.

2. Öppna den hämtade RDP-filen och klicka på **Anslut** när du tillfrågas. 

3. I fönstret **Windows-säkerhet** väljer du **fler alternativ** och sedan **använd ett annat konto**. Skriv användar namnet som **localhost** \\ *användar namn*, ange lösen ordet som du skapade för den virtuella datorn och klicka sedan på **OK**.

4. Du kan få en certifikatvarning under inloggningen. Klicka på **Ja** eller **Fortsätt** för att skapa anslutningen.

## <a name="install-web-server"></a>Installera webbservern

Installera IIS-webbservern för att se hur den virtuella datorn fungerar i praktiken. Öppna en PowerShell-kommandotolk på den virtuella datorn och kör följande kommando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

När kommandot har körts stänger du RDP-anslutningen till den virtuella datorn.


## <a name="view-the-iis-welcome-page"></a>Visa välkomstsidan för IIS

I portalen väljer du den virtuella datorn och i översikten för den virtuella datorn använder du knappen **Klicka för att kopiera** till höger om IP-adressen för att kopiera den och klistra in den i en webbläsare-flik. Standard Välkomst sidan för IIS öppnas och bör se ut så här:

![Skärm bild av standard platsen för IIS i en webbläsare](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när de inte längre behövs. 

Välj resurs gruppen för den virtuella datorn och välj sedan **ta bort**. Bekräfta resurs gruppens namn för att slutföra borttagningen av resurserna.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten distribuerade du en enkel virtuell dator, öppnade en nätverksport för webbtrafik och installerade en enkel webbserver. Om du vill veta mer om virtuella Azure-datorer fortsätter du till självstudien för virtuella Windows-datorer.

> [!div class="nextstepaction"]
> [Självstudier om virtuella Azure Windows-datorer](./tutorial-manage-vm.md)
