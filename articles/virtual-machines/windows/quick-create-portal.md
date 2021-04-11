---
title: Snabb start – skapa en virtuell Windows-dator i Azure Portal
description: I den här snabbstarten lär du dig hur du skapar en virtuell Windows-dator med hjälp av Azure Portal
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 03/15/2021
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 0ba28d003f359af12de6242c6d2444fb8adab0d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103562763"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-the-azure-portal"></a>Snabbstart: Skapa en virtuell Windows-dator i Azure Portal

Det går att skapa virtuella Azure-datorer via Azure Portal. Med den här metoden får du ett webbläsarbaserat användargränssnitt för att skapa virtuella datorer och alla relaterade resurser. Den här snabb starten visar hur du använder Azure Portal för att distribuera en virtuell dator (VM) i Azure som kör Windows Server 2019. För att se hur den virtuella datorn fungerar i praktiken ansluter du till den med RDP och installerar IIS-webbservern.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

1. Skriv **virtuella datorer** i sökningen.
1. Under **tjänster** väljer du **virtuella datorer**.
1. På sidan **virtuella datorer** väljer du **Lägg till** sedan **virtuell dator**. 
1. På fliken **Grundläggande inställningar** går du till **Projektinformation**, kontrollerar att korrekt prenumeration har valts och väljer sedan **Skapa ny** för resursgruppen. Skriv *myResourceGroup* som namn. 

    ![Skärm bild av avsnittet projekt information som visar var du väljer Azure-prenumerationen och resurs gruppen för den virtuella datorn](./media/quick-create-portal/project-details.png)

1. Under **Instansinformation** skriver du *myVM* för **Namn på virtuell dator** och väljer *USA, östra* som **Region**. Välj *Windows Server 2019 Data Center* för **avbildningen** och *Standard_DS1_v2* **storleken**. Låt de övriga standardvärdena vara som de är.

    ![Skärm bild av avsnittet instans information där du anger ett namn för den virtuella datorn och väljer dess region, bild och storlek](./media/quick-create-portal/instance-details.png)

1. Under **Administratörskonto**, anger du ett användarnamn, som *azureuser*, och ett lösenord. Lösen ordet måste vara minst 12 tecken långt och uppfylla de [definierade komplexitets kraven](faq.md#what-are-the-password-requirements-when-creating-a-vm).

    ![Skärm bild av avsnittet administratörs konto där du anger administratörens användar namn och lösen ord](./media/quick-create-portal/administrator-account.png)

1. Under **regler för inkommande port** väljer du **Tillåt valda portar** och väljer sedan **RDP (3389)** och **http (80)** i list rutan.

    ![Skärm bild av avsnittet regler för inkommande port där du väljer vilka portar som inkommande anslutningar tillåts på](./media/quick-create-portal/inbound-port-rules.png)

1. Låt de återstående standardinställningarna vara och välj sedan knappen **Granska + skapa** längst ned på sidan.

    ![Skärm bild som visar knappen granska och skapa längst ned på sidan](./media/quick-create-portal/review-create.png)

1. När valideringen har körts väljer du knappen **skapa** längst ned på sidan.

1. När distributionen är klar väljer **du gå till resurs**.

    ![Skärm bild som visar nästa steg för att gå till resursen](./media/quick-create-portal/next-steps.png)

## <a name="connect-to-virtual-machine"></a>Ansluta till den virtuella datorn

Skapa en fjärrskrivbordsanslutning till den virtuella datorn. Dessa instruktioner förklarar hur du ansluter till den virtuella datorn från en Windows-dator. På en Mac-dator behöver du en RDP-klient som denna [Fjärrskrivbordsklient](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) från Mac App Store.

1. På sidan Översikt för den virtuella datorn väljer du knappen **Anslut** och sedan **RDP**. 

    ![Skärm bild av översikts sidan för den virtuella datorn som visar platsen för knappen Anslut](./media/quick-create-portal/portal-quick-start-9.png)
    
2. På sidan **Anslut med RDP** ska du behålla standard alternativen för att ansluta via IP-adress, via port 3389 och klicka på **Ladda ned RDP-fil**.

2. Öppna den hämtade RDP-filen och klicka på **Anslut** när du tillfrågas. 

3. I fönstret **Windows-säkerhet** väljer du **fler alternativ** och sedan **använd ett annat konto**. Skriv användar namnet som **localhost** \\ *användar namn*, ange det lösen ord som du skapade för den virtuella datorn och klicka sedan på **OK**.

4. Du kan få en certifikatvarning under inloggningen. Klicka på **Ja** eller **Fortsätt** för att skapa anslutningen.

## <a name="install-web-server"></a>Installera webbservern

Installera IIS-webbservern för att se hur den virtuella datorn fungerar i praktiken. Öppna en PowerShell-kommandotolk på den virtuella datorn och kör följande kommando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

När kommandot har körts stänger du RDP-anslutningen till den virtuella datorn.


## <a name="view-the-iis-welcome-page"></a>Visa välkomstsidan för IIS

I portalen väljer du den virtuella datorn och i översikten över den virtuella datorn, hovrar du över IP-adressen för att visa **Kopiera till Urklipp**. Kopiera IP-adressen och klistra in den i en webbläsare-flik. Standard Välkomst sidan för IIS öppnas och bör se ut så här:

![Skärm bild av standard platsen för IIS i en webbläsare](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när de inte längre behövs. 

Gå till resurs gruppen för den virtuella datorn och välj sedan **ta bort resurs grupp**. Bekräfta resurs gruppens namn för att slutföra borttagningen av resurserna.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du distribuerat en enkel virtuell dator, öppnat en nätverks port för webb trafik och installerat en grundläggande webb server. Om du vill veta mer om virtuella Azure-datorer fortsätter du till självstudien för virtuella Windows-datorer.

> [!div class="nextstepaction"]
> [Självstudier om virtuella Azure Windows-datorer](./tutorial-manage-vm.md)
