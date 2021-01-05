---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/21/2020
ms.author: alkohli
ms.openlocfilehash: f2443765ecc9116193cefbc729ced25fa5657e59
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763444"
---
Innan du kan distribuera virtuella datorer på din Azure Stack Edge-enhet måste du konfigurera klienten så att den ansluter till enheten via Azure Resource Manager över Azure PowerShell. Detaljerade anvisningar finns i [ansluta till Azure Resource Manager på din Azure Stack Edge-enhet](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md).


Kontrol lera att följande steg kan användas för att komma åt enheten från klienten (du har gjort den här konfigurationen när du ansluter till Azure Resource Manager, verifierar du bara att konfigurationen lyckades.): 

1. Verifiera att Azure Resource Manager kommunikation fungerar. Ange:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. Anropa lokala enhets-API: er för autentisering. Ange: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Ange användar namnet *EdgeARMuser* och lösen ordet för att ansluta via Azure Resource Manager.

1. Om du har konfigurerat **Compute** för Kubernetes kan du hoppa över det här steget. Fortsätt till att kontrol lera att du har aktiverat ett nätverks gränssnitt för beräkning. I lokalt användar gränssnitt går du till **beräknings** inställningar. Välj det nätverks gränssnitt som du ska använda för att skapa en virtuell växel. De virtuella datorer som du skapar kommer att kopplas till en virtuell växel som är kopplad till den här porten och det associerade nätverket. Se till att välja ett nätverk som matchar IP-adressen som du vill använda för den virtuella datorn.  

    ![Aktivera beräknings inställningar 1](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

    Aktivera beräkning i nätverks gränssnittet. Azure Stack Edge skapar och hanterar en virtuell växel som motsvarar det nätverks gränssnittet. Ange inte vissa IP-adresser för Kubernetes just nu. Det kan ta flera minuter att aktivera beräkning.

    > [!NOTE]
    > Om du skapar GPU-virtuella datorer väljer du ett nätverks gränssnitt som är anslutet till Internet. På så sätt kan du installera GPU-tillägg på enheten.


1. Aktivera VM-rollen från Azure Portal. Det här steget skapar en unik prenumeration på enheten som används för att skapa virtuella datorer via lokala API: er för enheten. 

    1. Om du vill aktivera den virtuella dator rollen går du till Azure Stack Edge-resursen för Azure Stack gräns enheten i Azure Portal. Gå till **Edge compute > Virtual Machines**.

        ![Lägg till VM-avbildning 1](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-1.png)

    1. Välj **Virtual Machines** för att gå till **översikts** sidan. **Aktivera** moln hantering för virtuella datorer.
        ![Lägg till VM-avbildning 2](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-2.png)