---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/15/2021
ms.author: alkohli
ms.openlocfilehash: f166413507afb9aff814eaddaade099d2e34ae68
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554626"
---
Innan du kan distribuera virtuella datorer på din Azure Stack Edge-enhet måste du konfigurera klienten så att den ansluter till enheten via Azure Resource Manager över Azure PowerShell. Detaljerade anvisningar finns i [Anslut till Azure Resource Manager på Azure Stack Edge-enhet](../articles/databox-online/azure-stack-edge-gpu-connect-resource-manager.md).

Se till att du kan använda följande steg för att komma åt enheten från klienten. Du har redan slutfört den här konfigurationen när du anslöt till Azure Resource Manager och nu verifierar du att konfigurationen lyckades. 

1. Kontrol lera att Azure Resource Manager kommunikation fungerar genom att köra följande kommando:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. Om du vill anropa de lokala enhets-API: erna för autentisering anger du: 

    `login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d`

    Om du vill ansluta via Azure Resource Manager anger du användar namnet *EdgeArmUser* och lösen ordet.

1. Om du har konfigurerat Compute för Kubernetes kan du hoppa över det här steget. Annars kontrollerar du att du har aktiverat ett nätverks gränssnitt för beräkning genom att göra följande: 

   a. I ditt lokala användar gränssnitt går du till **beräknings** inställningar.  
   b. Välj det nätverks gränssnitt som du vill använda för att skapa en virtuell växel. De virtuella datorer som du skapar kommer att kopplas till en virtuell växel som är kopplad till den här porten och det associerade nätverket. Se till att välja ett nätverk som matchar IP-adressen som du ska använda för den virtuella datorn.  

    ![Skärm bild av fönstret för nätverks inställningar för beräknings konfiguration.](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

   c. Under **Aktivera för beräkning** i nätverks gränssnittet väljer du **Ja**. Azure Stack Edge skapar och hanterar en virtuell växel som motsvarar det nätverks gränssnittet. Ange inte vissa IP-adresser för Kubernetes just nu. Det kan ta flera minuter att aktivera beräkning.

    > [!NOTE]
    > Om du skapar GPU-virtuella datorer väljer du ett nätverks gränssnitt som är anslutet till Internet. På så sätt kan du installera ett GPU-tillägg på enheten.