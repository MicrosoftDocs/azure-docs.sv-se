---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/15/2021
ms.author: alkohli
ms.openlocfilehash: 56fc24966fa60c3a5e91f92b57332ae2f6a525ff
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98256451"
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


