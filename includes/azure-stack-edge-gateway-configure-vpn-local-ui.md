---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: fa65a7354112a2b220686372459b348d45832dd9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96467376"
---
Utför följande steg i det lokala webb gränssnittet på enheten. Det här steget tar ungefär 15 minuter, inklusive uppladdning av VPN-konfigurationsfilen (eller tjänst tag gen filen). 

1. Gå till **konfiguration > VPN**. Välj **Konfigurera**.

    ![Konfigurera lokalt användar gränssnitt 1](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-1.png)

2. På bladet **Konfigurera VPN** :

    - Aktivera **VPN-inställningar**.
    - Ange den **delade VPN-hemligheten**. Det här är den delade nyckel du angav när du skapade resursen för Azure VPN-anslutning.
    - Ange IP-adress för **VPN-gateway** . Det här är IP-adressen för den lokala Azure-Nätverksgatewayen.
    - För **PFS-grupp** väljer du **ingen**. 
    - För **DH-grupp** väljer du **group2**.
    - För **integritets metod för IPSec** väljer du **SHA256**.
    - För **omvandlings konstanter för IPSec-chiffrering** väljer du **GCMAES256**.
    - För **omvandlings konstanter för IPSec-autentisering** väljer du **GCMAES256**.
    - För **krypterings metod för IKE** väljer du **AES256**.
    - Välj **Använd**.

        ![Konfigurera lokalt användar gränssnitt 2](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-2.png)

    Mer information om de kryptografiska algoritmer som stöds finns i [om kryptografiska krav och Azure VPN-gatewayer](../articles/vpn-gateway/vpn-gateway-about-compliance-crypto.md#ipsecike-policy-faq). 

3. Om du vill överföra konfigurations filen för VPN-vägen väljer du **överför**. 

    ![Konfigurera lokalt användar gränssnitt 3](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-3.png)

    - Bläddra till den *JSON* -fil för service märken som du laddade ned i det lokala systemet i föregående steg.
    - Välj region som den Azure-region som är kopplad till enheten, det virtuella nätverket och gatewayer.
    - Välj **Använd**.

        ![Konfigurera lokalt användar gränssnitt 4](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-4.png)
    
    Överföringen tar cirka 7-8 minuter på enheten.

4. Om du vill lägga till användarspecifika vägar konfigurerar du IP-adressintervall så att de endast används med VPN. 

    - Under **IP-adressintervall som ska nås enbart via VPN väljer du** **Konfigurera**.
    - Ange ett giltigt IPv4-intervall och välj **Lägg till**. Upprepa stegen för att lägga till andra intervall.
    - Välj **Använd**.

        ![Konfigurera lokalt användar gränssnitt 5](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-5.png)

