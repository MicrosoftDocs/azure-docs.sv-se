---
title: Så här distribuerar du virtuella datorer på Azure Stack Edge Pro via Azure Portal
description: Lär dig hur du skapar och hanterar virtuella datorer i Azure Stack Edge Pro via Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/02/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro device so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: a48abb34a201928185130fbe8fdf9592e77492d9
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944961"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-the-azure-portal"></a>Distribuera virtuella datorer på Azure Stack Edge Pro GPU-enhet via Azure Portal

Du kan skapa och hantera virtuella datorer på en Azure Stack Edge-enhet med Azure Portal, mallar, Azure PowerShell-cmdletar och via Azure CLI/Python-skript. Den här artikeln beskriver hur du skapar och hanterar en virtuell dator på din Azure Stack Edge-enhet med hjälp av Azure Portal. 

Den här artikeln gäller Azure Stack Edge Pro GPU, Azure Stack Edge Pro R och Azure Stack Edge Mini R-enheter. 

> [!IMPORTANT] 
> Vi rekommenderar att du aktiverar multifaktorautentisering för den användare som hanterar virtuella datorer som distribueras på enheten från molnet.
        
## <a name="vm-deployment-workflow"></a>Arbets flöde för distribution av virtuell dator

Översikten över distributions arbets flödet på hög nivå är följande:

1. Aktivera ett nätverks gränssnitt för beräkning på din Azure Stack Edge-enhet. Detta skapar en virtuell växel på det angivna nätverks gränssnittet.
1. Aktivera moln hantering av virtuella datorer från Azure Portal.
1. Ladda upp en virtuell hård disk till ett Azure Storage konto med Storage Explorer. 
1. Använd den överförda virtuella hård disken för att ladda ned den virtuella hård disken till enheten och skapa en VM-avbildning från den virtuella hård disken. 
1. Använd resurserna som du skapade i föregående steg:
    1. Avbildning av virtuell dator som du har skapat.
    1. VSwitch som är associerad med det nätverks gränssnitt där du har aktiverat beräkning.
    1. Undernät som är associerat med VSwitch.

    Och skapa eller ange följande resurser i rad:
    1. Namn på virtuell dator, Välj en VM-storlek som stöds och inloggnings uppgifter för den virtuella datorn. 
    1. Skapa nya data diskar eller koppla befintliga data diskar.
    1. Konfigurera statisk eller dynamisk IP-adress för den virtuella datorn. Om du tillhandahåller en statisk IP-adress väljer du från en kostnads fri IP-adress i under nätet för det nätverks gränssnitt som är aktiverat för beräkning.

    Använd resurserna ovan för att skapa en virtuell dator.


## <a name="prerequisites"></a>Förutsättningar

Innan du börjar skapa och hantera virtuella datorer på enheten via Azure Portal, se till att:

1. Du har slutfört nätverks inställningarna på din Azure Stack Edge Pro-enhet enligt beskrivningen i [steg 1: konfigurera Azure Stack Edge Pro-enhet](azure-stack-edge-j-series-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device).

    1. Du har aktiverat ett nätverks gränssnitt för beräkning. Nätverks gränssnittets IP-adress används för att skapa en virtuell växel för VM-distributionen. I enhetens lokala användar gränssnitt går du till **Compute**. Välj det nätverks gränssnitt som du ska använda för att skapa en virtuell växel.

        > [!IMPORTANT] 
        > Du kan bara konfigurera en port för beräkning.

    1. Aktivera beräkning i nätverks gränssnittet. Azure Stack Edge Pro skapar och hanterar en virtuell växel som motsvarar det nätverks gränssnittet.

1. Du har åtkomst till en Windows-eller Linux-VHD som du ska använda för att skapa avbildningen av den virtuella datorn för den virtuella dator som du vill skapa.

## <a name="deploy-a-vm"></a>Distribuera en virtuell dator

Följ dessa steg om du vill skapa en virtuell dator på din Azure Stack Edge-enhet.

### <a name="add-a-vm-image"></a>Lägg till en VM-avbildning

1. Ladda upp en virtuell hård disk till ett Azure Storage-konto. Följ stegen i [Ladda upp en virtuell hård disk med Azure Storage Explorer](../devtest-labs/devtest-lab-upload-vhd-using-storage-explorer.md).

1. I Azure Portal går du till Azure Stack Edge-resursen för din Azure Stack Edge-enhet. Gå till **Edge compute > Virtual Machines**.

    ![Lägg till VM-avbildning 1](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-1.png)

1. Välj **Virtual Machines** för att gå till **översikts** sidan. **Aktivera** moln hantering för virtuella datorer.
    ![Lägg till VM-avbildning 2](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-2.png)

1. Det första steget är att lägga till en avbildning av en virtuell dator. Du har redan laddat upp en virtuell hård disk till lagrings kontot i det tidigare steget. Du kommer att använda den virtuella hård disken för att skapa en avbildning av en virtuell dator.

    Välj **Lägg till avbildning** för att ladda ned den virtuella hård disken från lagrings kontot och Lägg till på enheten. Hämtnings processen tar flera minuter beroende på storleken på den virtuella hård disken och den Internet bandbredd som är tillgänglig för nedladdningen. 

    ![Lägg till VM-avbildning 3](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-3.png)

1. Ange följande parametrar i bladet **Lägg till bild** . Välj **Lägg till**.


    |Parameter  |Beskrivning  |
    |---------|---------|
    |Ladda ned från Storage BLOB    |Bläddra till platsen för lagrings-blobben i det lagrings konto där du laddade upp den virtuella hård disken.         |
    |Ladda ned till    | Ställ automatiskt in på den aktuella enheten där du distribuerar den virtuella datorn.        |
    |Spara bild som      | Namnet på den VM-avbildning som du skapar från den virtuella hård disken som du överförde till lagrings kontot.        |
    |OS-typ     |Välj från Windows eller Linux som operativ system för den virtuella hård disk som du ska använda för att skapa den virtuella dator avbildningen.         |
   

    ![Lägg till VM-avbildning 4](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-6.png)

1. Den virtuella hård disken laddas ned och avbildningen av den virtuella datorn skapas. Det tar flera minuter att slutföra avbildningen. Du ser ett meddelande om att den virtuella dator avbildningen har slutförts.

    ![Lägg till VM-avbildning 5](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-8.png)


1. När den virtuella dator avbildningen har skapats läggs den till i listan över avbildningar på bladet **images** .
    ![Lägg till VM-avbildning 6](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-9.png)

    Bladet **distributioner** uppdateras för att ange distributionens status.

    ![Lägg till VM-avbildning 7](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-10.png)

    Den nyligen tillagda bilden visas också på sidan **Översikt** .
    ![Lägg till VM-avbildning 8](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-11.png)


### <a name="add-a-vm"></a>Lägga till en virtuell dator

Följ de här stegen för att skapa en virtuell dator när du har skapat en avbildning av en virtuell dator.

1. På sidan **Översikt** väljer du **Lägg till virtuell dator**.

    ![Lägg till virtuell dator 1](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-1.png)

1. På fliken **grundläggande** skriver du in följande parametrar.


    |Parameter |Beskrivning  |
    |---------|---------|
    |Namn på virtuell dator     |         |
    |Bild     | Välj bland de VM-avbildningar som är tillgängliga på enheten.        |
    |Storlek     | Välj bland de [VM-storlekar som stöds](azure-stack-edge-gpu-virtual-machine-sizes.md).        |
    |Användarnamn     | Använd standard användar namn *azureuser*.        |
    |Autentiseringstyp    | Välj från offentlig SSH-nyckel eller ett användardefinierat lösen ord.       |
    |Lösenord     | Ange ett lösen ord för att logga in på den virtuella datorn. Lösen ordet måste vara minst 12 tecken långt och uppfylla de definierade [komplexitets kraven](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).        |
    |Bekräfta lösenordet    | Ange lösen ordet igen.        |


    ![Lägg till virtuell dator 2](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-basics-1.png)

    Välj **Nästa: diskar**.

1. På fliken **diskar** kommer du att koppla diskar till den virtuella datorn. 
    
    1. Du kan välja att **skapa och ansluta en ny disk** eller **koppla en befintlig disk**.

        ![Lägg till virtuell dator 3](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-1.png)

    1. Välj **skapa och Anslut en ny disk**. På bladet **Skapa ny disk** anger du ett namn för disken och storleken i GIB.

        ![Lägg till virtuell dator 4](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-2.png)

    1.  Upprepa proceduren ovan för att lägga till fler diskar. När diskarna har skapats visas de på fliken **diskar** .

        ![Lägg till VM 5](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-3.png)

        Välj **Nästa: Nätverk**.

1. På fliken **nätverk** kommer du att konfigurera nätverks anslutningen för den virtuella datorn.

    
    |Parameter  |Beskrivning |
    |---------|---------|
    |Virtuellt nätverk    | I list rutan väljer du den virtuella växel som skapats på din Azure Stack Edge-enhet när du har aktiverat Compute i nätverks gränssnittet.    |
    |Undernät     | Det här fältet fylls i automatiskt med det undernät som är associerat med det nätverks gränssnitt som du har aktiverat Compute.         |
    |IP-adress     | Ange en statisk eller dynamisk IP-adress för den virtuella datorn. Den statiska IP-adressen ska vara en tillgänglig, kostnads fri IP-adress från det angivna under nätet.        |

    ![Lägg till VM 6](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-networking-1.png)

    Välj **Nästa: granska + skapa**.

1. På fliken **Granska + skapa** granskar du inställningarna för den virtuella datorn och väljer **skapa**.

    ![Lägg till VM 7](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-review-create-1.png)

1. Den virtuella datorns skapande startar och kan ta upp till 20 minuter. Du kan gå till **distributioner** för att övervaka skapande av virtuella datorer.

    ![Lägg till VM 8](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-deployments-page-1.png)

    
1. När den virtuella datorn har skapats uppdateras **översikts** sidan för att visa den nya virtuella datorn.

    ![Lägg till VM 9](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-overview-page-1.png)

1. Välj den nyligen skapade virtuella datorn för att gå till **virtuella datorer**.

    ![Lägg till VM 10](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-page-1.png)

    Välj den virtuella datorn för att se informationen. 

    ![Lägg till VM 11](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-details-1.png)

## <a name="connect-to-a-vm"></a>Anslut till en virtuell dator

Stegen för att ansluta kan vara olika beroende på om du har skapat ett Windows eller en virtuell Linux-dator. Du kan inte ansluta till de virtuella datorer som distribuerats på enheten via Azure Portal. Du måste utföra följande steg för att ansluta till din virtuella Linux-eller Windows-dator.

### <a name="connect-to-linux-vm"></a>Anslut till virtuell Linux-dator

Följ dessa steg för att ansluta till en virtuell Linux-dator.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-windows-vm"></a>Anslut till virtuell Windows-dator

Följ dessa steg för att ansluta till en virtuell Windows-dator.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

## <a name="next-steps"></a>Nästa steg

Information om hur du administrerar din Azure Stack Edge Pro-enhet finns i[Använd lokalt webb gränssnitt för att administrera en Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md).
