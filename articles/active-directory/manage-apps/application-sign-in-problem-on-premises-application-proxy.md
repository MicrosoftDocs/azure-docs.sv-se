---
title: Problem med att logga in till en lokal app med Azure AD App proxy | Microsoft Docs
description: Felsöka vanliga problem som är riktade mot att du inte kan logga in på ett lokalt program som är integrerat med Azure AD med hjälp av Azure-AD-programproxy
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a92ce03b4a8ad241a21b29bbff4e7367d656fab
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99260002"
---
# <a name="problems-signing-in-to-an-on-premises-application-using-the-azure-ad-application-proxy"></a>Problem med att logga in till ett lokalt program med Azure AD-programproxyn

Om du har problem med att logga in i ett lokalt program kan du prova att följa stegen nedan för att lösa problemet.

## <a name="i-can-load-my-application-but-something-on-the-page-looks-broken"></a>Jag kan läsa in mitt program, men något på sidan verkar skadat

Följande dokument kan kanske hjälpa dig att lösa några av de vanligaste problemen i den här kategorin.

  * [Kan jag öppna mitt program, men programmets sida visas inte korrekt](application-proxy-page-appearance-broken-problem.md)
  * [Kan jag öppna mitt program, men det tar för lång tid för programmet att läsas in](application-proxy-page-load-speed-problem.md)
  * [Jag kan öppna mitt program, men länkarna på programsidan fungerar inte](application-proxy-page-links-broken-problem.md)

## <a name="im-having-a-connectivity-problem-my-application"></a>Jag har problem med att ansluta mitt program
  Följande dokument kan kanske hjälpa dig att lösa några av de vanligaste problemen i den här kategorin.
  * [Jag vet inte vilka portar som ska öppnas för mitt program](application-proxy-add-on-premises-application.md)
  * [Jag påträffade ett problem eftersom det inte fanns någon fungerande anslutning i en anslutningsgrupp för mitt program](application-proxy-connectivity-no-working-connector.md)

## <a name="im-having-a-problem-configuring-the-azure-ad-application-proxy-in-the-admin-portal"></a>Jag har problem med att konfigurera Azure AD-programproxyn i administrationsportalen
  Följande dokument kan kanske hjälpa dig att lösa några av de vanligaste problemen i den här kategorin.
  * [Jag har problem med att konfigurera ett proxyprogram](application-proxy-config-how-to.md)
  * [Jag vet inte hur man konfigurerar enkel inloggning till mitt proxyprogram](application-proxy-config-sso-how-to.md)
  * [Jag påträffade ett problem när jag skulle skapa mitt program i administrationsportalen](application-proxy-config-problem.md)

## <a name="im-having-a-problem-setting-up-back-end-authentication-to-my-application"></a>Det har uppstått ett problem vid konfigurering av serverdelsautentisering till mitt program
  Följande dokument kan kanske hjälpa dig att lösa några av de vanligaste problemen i den här kategorin.
  * [Jag vet inte hur man konfigurerar begränsad Kerberos-delegering](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
  * [Jag vet inte hur jag ska konfigurera mina program med PingAccess](./application-proxy-ping-access-publishing-guide.md)

## <a name="im-having-a-problem-when-signing-in-to-my-application"></a>Jag har problem med att logga in till mitt program
  Följande dokument kan kanske hjälpa dig att lösa några av de vanligaste problemen i den här kategorin.
  * [Jag får felmeddelandet "Det går inte att få åtkomst till det här företagsprogrammet"](application-proxy-sign-in-bad-gateway-timeout-error.md)

## <a name="im-having-a-problem-with-the-application-proxy-agent-connector"></a>Det har uppstått ett problem med att anslutningsprogrammet för programproxyagenten
  Följande dokument kan kanske hjälpa dig att lösa några av de vanligaste problemen i den här kategorin.
  * [Jag har problem med att installera Application Proxy agent Connector](application-proxy-connector-installation-problem.md)

## <a name="next-steps"></a>Nästa steg
[Ge säker fjärråtkomst till lokala program](application-proxy.md)