---
title: 'VPN Gateway: Felsöka VPN-klienten – Azure AD-autentisering'
description: Felsöka VPN Gateway P2S Azure AD Authentication-klienter
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 56a8514fc2531ba0b18925427814e5bfef7d64bf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84988110"
---
# <a name="troubleshoot-an-azure-ad-authentication-vpn-client"></a>Felsöka en Azure AD Authentication VPN-klient

Den här artikeln hjälper dig att felsöka en VPN-klient för att ansluta till ett virtuellt nätverk med hjälp av punkt-till-plats-VPN och Azure Active Directory autentisering.

## <a name="view-status-log"></a><a name="status"></a>Visa status logg

Visa status loggen för fel meddelanden.

![loggar](./media/troubleshoot-ad-vpn-client/1.png)

1. Klicka på ikonen pilar i det nedre högra hörnet i klient fönstret för att visa **status loggarna**.
2. Kontrol lera loggarna för fel som kan tyda på problemet.
3. Fel meddelanden visas i rött.

## <a name="clear-sign-in-information"></a><a name="clear"></a>Rensa inloggnings information

Rensa inloggnings informationen.

![logga in](./media/troubleshoot-ad-vpn-client/2.png)

1. Välj... bredvid den profil som du vill felsöka. Välj **Konfigurera-> rensa sparat konto**.
2. Välj **Spara**.
3. Prova att ansluta.
4. Om anslutningen fortfarande Miss lyckas kan du fortsätta till nästa avsnitt.

## <a name="run-diagnostics"></a><a name="diagnostics"></a>Kör diagnostik

Kör diagnostik på VPN-klienten.

![diagnostik](./media/troubleshoot-ad-vpn-client/3.png)

1. Klicka på **...** bredvid den profil som du vill köra diagnostik på. Välj **diagnostisera-> kör diagnostik**.
2. Klienten kommer att köra en serie tester och visa resultatet av testet

   * Internet åtkomst – kontrollerar om klienten har Internet anslutning
   * Klientautentiseringsuppgifter – kontrol lera om det går att få åtkomst till Azure Active Directory-autentiseringens slut punkt
   * Servern kan matchas – kontaktar DNS-servern för att matcha IP-adressen för den konfigurerade VPN-servern
   * Servern kan inte uppnås – kontrollerar om VPN-servern svarar eller inte
3. Om något av testerna inte fungerar kontaktar du nätverks administratören för att lösa problemet.
4. I nästa avsnitt visas hur du samlar in loggarna, om det behövs.

## <a name="collect-client-log-files"></a><a name="logfiles"></a>Samla in klient logg filer

Samla in VPN-klientens loggfiler. Loggfilerna kan skickas till support/administratör via en metod som du väljer. Till exempel e-post.

1. Klicka på "..." bredvid den profil som du vill köra diagnostik på. Välj **diagnostisera-> Visa logg katalog**.

   ![Visa loggar](./media/troubleshoot-ad-vpn-client/4.png)
2. Utforskaren öppnas i den mapp som innehåller loggfilerna.

   ![Visa fil](./media/troubleshoot-ad-vpn-client/5.png)

## <a name="next-steps"></a>Nästa steg

Mer information finns i [skapa en Azure Active Directory-klient för P2s öppna VPN-anslutningar som använder Azure AD-autentisering](openvpn-azure-ad-tenant.md).