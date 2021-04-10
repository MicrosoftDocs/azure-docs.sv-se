---
title: Konfigurera TLS 1,2 på Windows-klienter som har åtkomst till Azure Stack Edge Pro GPU-enhet
description: Beskriver hur du konfigurerar TLS 1,2 på Windows-klienter som har åtkomst till Azure Stack Edge Pro GPU-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 47a6d3bbebdf3b2b14b1c40d7ea8fc93b4d19c6f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565341"
---
# <a name="configure-tls-12-on-windows-clients-accessing-azure-stack-edge-pro-device"></a>Konfigurera TLS 1,2 på Windows-klienter som har åtkomst till Azure Stack Edge Pro-enhet

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Om du använder en Windows-klient för att få åtkomst till din Azure Stack Edge Pro-enhet måste du konfigurera TLS 1,2 på klienten. Den här artikeln innehåller resurser och rikt linjer för att konfigurera TLS 1,2 på din Windows-klient. 

De rikt linjer som anges här baseras på test som utförs på en klient som kör Windows Server 2016.

## <a name="configure-tls-12-for-current-powershell-session"></a>Konfigurera TLS 1,2 för den aktuella PowerShell-sessionen

Utför följande steg för att konfigurera TLS 1,2 på klienten.

1. Kör PowerShell som administratör.
2. Om du vill ställa in TLS 1,2 för den aktuella PowerShell-sessionen skriver du:
  
    ```azurepowershell
    $TLS12Protocol = [System.Net.SecurityProtocolType] 'Ssl3 , Tls12'
    [System.Net.ServicePointManager]::SecurityProtocol = $TLS12Protocol
    ```
## <a name="configure-tls-12-on-client"></a>Konfigurera TLS 1,2 på klienten

Om du vill ställa in systemomfattande TLS 1,2 för din miljö följer du rikt linjerna i dessa dokument:

- [Allmänt – så här aktiverar du TLS 1,2](/windows-server/security/tls/tls-registry-settings#tls-12)
- [Så här aktiverar du TLS 1,2 på klienter](/configmgr/core/plan-design/security/enable-tls-1-2-client)
- [Så här aktiverar du TLS 1,2 på plats servrar och fjärranslutna plats system](/configmgr/core/plan-design/security/enable-tls-1-2-server)
- [Protokoll i TLS/SSL (Schannel SSP)](/windows-server/security/tls/manage-tls#configuring-tls-ecc-curve-order)
- [Chiffersviter](/windows-server/security/tls/tls-registry-settings#tls-12): Konfigurera bara [TLS cipher Suite-ordningen](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order) se till att du visar dina aktuella chiffersviter och lägga som saknas i följande lista:

    - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

    Du kan också lägga till dessa chiffersviter genom att redigera register inställningarna direkt.

    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "Functions"  -PropertyType String -Value ("TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384")
    ```

- Så här ställer du in elliptiska kurvor

    Se till att du visar dina aktuella elliptiska kurvor och lägga som saknas i följande lista:

    - P-256 
    - P-384

    Du kan också lägga till dessa elliptiska kurvor genom att redigera register inställningarna direkt.
    
    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "EccCurves" -PropertyType MultiString -Value @("NistP256", "NistP384")
    ```
    
    - [Ange minsta RSA Key Exchange-storlek till 2048](/windows-server/security/tls/tls-registry-settings#keyexchangealgorithm---client-rsa-key-sizes).



## <a name="next-steps"></a>Nästa steg

[Anslut till Azure Resource Manager](./azure-stack-edge-gpu-connect-resource-manager.md)