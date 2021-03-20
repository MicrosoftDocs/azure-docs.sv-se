---
title: Integrera NPS med VPN Gateway RADIUS-autentisering för MFA
description: Beskriver integrering av Azure Gateway RADIUS-autentisering med NPS-server för Multi-Factor Authentication.
services: vpn-gateway
documentationcenter: na
author: ahmadnyasin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/16/2019
ms.author: genli
ms.openlocfilehash: 208e99f61694f5a81a98dbc649e2a6035f57891b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018281"
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>Integrera Azure VPN gateway RADIUS-autentisering med NPS-server för Multi-Factor Authentication 

Artikeln beskriver hur du integrerar nätverks princip Server (NPS) med Azure VPN gateway RADIUS-autentisering för att leverera Multi-Factor Authentication (MFA) för punkt-till-plats-VPN-anslutningar. 

## <a name="prerequisite"></a>Förutsättning

Om du vill aktivera MFA måste användarna vara i Azure Active Directory (Azure AD), som måste synkroniseras från antingen den lokala miljön eller moln miljön. Dessutom måste användaren redan ha slutfört den automatiska registreringen av MFA-processen.  Mer information finns i [Konfigurera mitt konto för](../active-directory/user-help/multi-factor-authentication-end-user-first-time.md) tvåstegsverifiering

## <a name="detailed-steps"></a>Detaljerade steg

### <a name="step-1-create-a-virtual-network-gateway"></a>Steg 1: skapa en virtuell nätverksgateway

1. Logga in på [Azure portal](https://portal.azure.com).
2. I det virtuella nätverk som ska vara värd för den virtuella Nätverksgatewayen väljer du **undernät** och väljer sedan **Gateway-undernät** för att skapa ett undernät. 

    ![Bilden om hur du lägger till gateway-undernät](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. Skapa en virtuell nätverksgateway genom att ange följande inställningar:

    - **Gatewaytyp**: välj **VPN**.
    - **VPN-typ**: Välj **Route-based**.
    - **SKU**: Välj en SKU-typ baserat på dina krav.
    - **Virtuellt nätverk**: Välj det virtuella nätverk där du skapade Gateway-undernätet.

        ![Avbildningen om inställningar för virtuell nätverksgateway](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-ad-mfa"></a>Steg 2 Konfigurera NPS för Azure AD MFA

1. [Installera NPS-tillägget för Azure AD MFA](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension)på NPS-servern.
2. Öppna NPS-konsolen, högerklicka på **RADIUS-klienter** och välj sedan **ny**. Skapa RADIUS-klienten genom att ange följande inställningar:

    - **Eget namn**: Ange ett namn.
    - **Adress (IP eller DNS)**: Ange det Gateway-undernät som du skapade i steg 1.
    - **Delad hemlighet**: Ange en hemlig nyckel och kom ihåg att den används för senare användning.

      ![Avbildningen om RADIUS-klientinställningar](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  På fliken **Avancerat** ställer du in leverantörs namnet på **RADIUS-standard** och kontrollerar att kryss rutan **ytterligare alternativ** inte är markerad.

    ![Avbildningen om avancerade inställningar för RADIUS-klient](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. Gå till **principer**  >  **nätverks principer**, dubbelklicka på **anslutningar till Microsoft Routning och fjärråtkomst server** princip, Välj **bevilja åtkomst** och klicka sedan på **OK**.

### <a name="step-3-configure-the-virtual-network-gateway"></a>Steg 3 Konfigurera den virtuella Nätverksgatewayen

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Öppna den virtuella nätverksgateway som du har skapat. Kontrol lera att Gateway-typen är inställd på **VPN** och att VPN-typen är **Route-baserad**.
3. Klicka på **peka på plats konfiguration**  >  **Konfigurera nu** och ange sedan följande inställningar:

    - Adresspool: Ange det Gateway **-** undernät som du skapade i steg 1.
    - **Autentiseringstyp**: Välj **RADIUS-autentisering**.
    - **Server-IP-adress**: Ange IP-adressen för NPS-servern.

      ![Bilden om peka på plats inställningar](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>Nästa steg

- [Azure AD Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md)
- [Integrera din befintliga NPS-infrastruktur med Azure AD Multi-Factor Authentication](../active-directory/authentication/howto-mfa-nps-extension.md)