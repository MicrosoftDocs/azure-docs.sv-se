---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 410570302eec418f1e4bcb75d6413936a96b5171
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "77471584"
---
Villkorlig åtkomst möjliggör detaljerad åtkomst kontroll för varje program. För att kunna använda villkorlig åtkomst bör du ha Azure AD Premium 1 eller fler licenser som tillämpas på de användare som ska omfattas av reglerna för villkorlig åtkomst.

1. Gå till sidan **företags program – alla program** och klicka på **Azure VPN**.

   - Klicka på **villkorlig åtkomst**.
   - Klicka på **ny princip** för att öppna det **nya** fönstret.
2. I fönstret **nytt** navigerar du till **tilldelningar – > användare och grupper**. På fliken **användare och grupper – >** **Inkludera** :

   - Klicka på **Välj användare och grupper**.
   - Kontrol lera **användare och grupper**.
   - Klicka på **Välj** för att välja en grupp eller en uppsättning användare som ska påverkas av MFA.
   - Klicka på **Klar**.

   ![Tilldelningar](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. I fönstret **nytt** navigerar du till fönstret **åtkomst kontroller – > beviljande** :

   - Klicka på **bevilja åtkomst**.
   - Klicka på **Kräv Multi-Factor Authentication**.
   - Klicka på **Kräv alla markerade kontroller**.
   - Klicka på **Välj**.
   
   ![Bevilja åtkomst – MFA](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. I avsnittet **Aktivera princip** :

   - Välj **på**.
   - Klicka på **Skapa**.

   ![Aktivera princip](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)