---
title: 'Azure AD Connect: hybrid identitets överväganden för Azure Government molnet'
description: Särskilda överväganden vid distribution av Azure AD Connect med Azure Government molnet.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bbb4298d0d9a9d7589c4a2055b4d55a0b852f951
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97504363"
---
# <a name="hybrid-identity-considerations-for-the-azure-government-cloud"></a>Hybrid identitets överväganden för Azure Government molnet

I den här artikeln beskrivs överväganden för att integrera en hybrid miljö med Microsoft Azure Government molnet. Den här informationen tillhandahålls som referens för administratörer och arkitekter som arbetar med Azure Government molnet.

> [!NOTE]
> För att integrera en Microsoft Active Directory-miljö (antingen lokalt eller värdbaserad i en IaaS som är en del av samma moln instans) med Azure Government molnet måste du uppgradera till den senaste versionen av [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

En fullständig lista över USA myndighets departementets slut punkter finns i [dokumentationen](/office365/enterprise/office-365-u-s-government-dod-endpoints).

## <a name="azure-ad-pass-through-authentication"></a>Direkt autentisering i Azure AD

Följande information beskriver implementeringen av direktautentisering och Azure Government molnet.

### <a name="allow-access-to-urls"></a>Tillåt åtkomst till webbadresser

Innan du distribuerar direktautentisering för direkt autentisering kontrollerar du om det finns en brand vägg mellan dina servrar och Azure AD. Om din brand vägg eller proxy tillåter Domain Name System (DNS) blockerade eller säkra program, lägger du till följande anslutningar.

> [!NOTE]
> Följande rikt linjer gäller även för att installera [Azure AD-programproxy Connector](../manage-apps/what-is-application-proxy.md) för Azure Government miljöer.

|URL |Hur den används|
|-----|-----|
|&#42;. msappproxy.us</br>&#42;.servicebus.usgovcloudapi.net|Agenten använder dessa URL: er för att kommunicera med moln tjänsten Azure AD. |
|`mscrl.microsoft.us:80` </br>`crl.microsoft.us:80` </br>`ocsp.msocsp.us:80` </br>`www.microsoft.us:80`| Agenten använder dessa URL: er för att verifiera certifikat.|
|login.windows.us </br>secure.aadcdn.microsoftonline-p.com </br>&#42;. microsoftonline.us </br>&#42;. microsoftonline-p.us </br>&#42;. msauth.net </br>&#42;. msauthimages.net </br>&#42;. msecnd.net</br>&#42;. msftauth.net </br>&#42;. msftauthimages.net</br>&#42;. phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctldl.windowsupdate.us:80| Agenten använder dessa URL: er under registrerings processen.

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Installera agenten för Azure Government molnet

Följ de här stegen för att installera agenten för Azure Government molnet:

1. I kommando rads terminalen går du till mappen som innehåller den körbara fil som installerar agenten.
1. Kör följande kommandon, som anger att installationen är för Azure Government.

   För direktautentisering:

   ```
   AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
   ```

   För Application Proxy:

   ```
   AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
   ```

## <a name="single-sign-on"></a>Enkel inloggning

### <a name="set-up-your-azure-ad-connect-server"></a>Konfigurera din Azure AD Connect-Server

Om du använder direktautentisering som inloggnings metod krävs ingen ytterligare krav kontroll. Om du använder Password-hash-synkronisering som inloggnings metod och det finns en brand vägg mellan Azure AD Connect och Azure AD, kontrollerar du att:

- Du använder Azure AD Connect version 1.1.644.0 eller senare.
- Om din brand vägg eller proxy tillåter DNS-blockerade eller säkra program, lägger du till anslutningarna till &#42;-URL: er för. msappproxy.us via port 443.

  Om inte, Tillåt åtkomst till Azure datacenter IP-intervall som uppdateras varje vecka. Detta krav gäller endast när du aktiverar funktionen. Det krävs inte för faktiska användar inloggningar.

### <a name="roll-out-seamless-single-sign-on"></a>Distribuera sömlösa enkla Sign-On

Du kan gradvis Distribuera Azure AD sömlöst Sign-On till dina användare med hjälp av följande instruktioner. Du börjar med att lägga till Azure AD-URL: en `https://autologon.microsoft.us` till alla eller valda användares intranät zons inställningar genom att använda Grupprincip i Active Directory.

Du måste också aktivera princip inställningen **Tillåt uppdateringar till statusfältet via skript genom att Grupprincip**.

## <a name="browser-considerations"></a>Webb läsar överväganden

### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (alla plattformar)

I Mozilla Firefox används inte Kerberos-autentisering automatiskt. Varje användare måste manuellt lägga till Azure AD-URL: en i sina Firefox-inställningar genom att följa dessa steg:

1. Kör Firefox och ange **About: config**   i adress fältet. Stäng alla aviseringar som du kan se.
1. Sök efter **nätverket. inställningar för Negotiate-auth. Trusted-URI: er**   . Den här inställningen visar de webbplatser som är betrodda av Firefox för Kerberos-autentisering.
1. Högerklicka på Preference-namnet och välj sedan **ändra**.
1. Ange `https://autologon.microsoft.us` i rutan.
1. Välj **OK**   och öppna sedan webbläsaren igen.

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge baserat på krom (alla plattformar)

Om du har åsidosatt  `AuthNegotiateDelegateAllowlist`   `AuthServerAllowlist`   -eller-princip inställningarna i din miljö kontrollerar du att du lägger till Azure AD-URL: en `https://autologon.microsoft.us` till dem.

### <a name="google-chrome-all-platforms"></a>Google Chrome (alla plattformar)

Om du har åsidosatt  `AuthNegotiateDelegateWhitelist`   `AuthServerWhitelist`   -eller-princip inställningarna i din miljö kontrollerar du att du lägger till Azure AD-URL: en `https://autologon.microsoft.us` till dem.

## <a name="next-steps"></a>Nästa steg

- [Direktautentisering](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
- [Enkel inloggning](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites)
