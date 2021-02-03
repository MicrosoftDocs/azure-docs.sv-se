---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/15/2021
ms.author: alkohli
ms.openlocfilehash: 8f3031669723cd61715c12a42f99869ac0eaf3bc
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99500011"
---
Innan du kan distribuera virtuella datorer på din Azure Stack Edge-enhet måste du konfigurera klienten så att den ansluter till enheten via Azure Resource Manager över Azure PowerShell. Detaljerade anvisningar finns i [ansluta till Azure Resource Manager på din Azure Stack Edge-enhet](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md).

Kontrol lera att följande steg kan användas för att komma åt enheten från klienten. (Du har gjort den här konfigurationen när du ansluter till Azure Resource Manager. Nu är det bara att verifiera att konfigurationen lyckades.) 

1. Verifiera att Azure Resource Manager kommunikation fungerar. Skriv:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. Anropa lokala enhets-API: er för autentisering. Skriv: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Ange användar namnet *EdgeARMuser* och lösen ordet för att ansluta via Azure Resource Manager.

1. Om du har konfigurerat **Compute** för Kubernetes kan du hoppa över det här steget. Fortsätt till att kontrol lera att du har aktiverat ett nätverks gränssnitt för beräkning. I ditt lokala användar gränssnitt går du till **beräknings** inställningar. Välj det nätverks gränssnitt som du vill använda för att skapa en virtuell växel. De virtuella datorer som du skapar kommer att kopplas till en virtuell växel som är kopplad till den här porten och det associerade nätverket. Se till att välja ett nätverk som matchar IP-adressen som du vill använda för den virtuella datorn.  

    ![Skärm bild som visar hur du aktiverar beräknings inställningar.](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

    Aktivera beräkning i nätverks gränssnittet. Azure Stack Edge skapar och hanterar en virtuell växel som motsvarar det nätverks gränssnittet. Ange inte vissa IP-adresser för Kubernetes just nu. Det kan ta flera minuter att aktivera beräkning.

    > [!NOTE]
    > Om du skapar GPU-virtuella datorer väljer du ett nätverks gränssnitt som är anslutet till Internet. På så sätt kan du installera ett GPU-tillägg på enheten.


