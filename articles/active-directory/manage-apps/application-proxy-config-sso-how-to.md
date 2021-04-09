---
title: Förstå enkel inloggning med en lokal app med hjälp av Application Proxy
description: Förstå enkel inloggning med en lokal app med hjälp av Application Proxy.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: kenwith
ms.reviewer: japere, asteen
ms.openlocfilehash: 10b722edbe8d70c92e617c78db3d2fb1d46da3a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99254112"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Konfigurera enkel inloggning till ett Programproxyprogram

Enkel inloggning (SSO) gör att användarna kan komma åt ett program utan att autentisera flera gånger. Den gör det möjligt att utföra en enskild autentisering i molnet, mot Azure Active Directory, och tillåter att tjänsten eller anslutningen imiterar användaren att slutföra eventuella ytterligare autentiseringsbegäranden från programmet.

## <a name="how-to-configure-single-sign-on"></a>Så här konfigurerar du enkel inloggning
Om du vill konfigurera SSO måste du först se till att programmet är konfigurerat för förautentisering genom att Azure Active Directory. För att utföra den här konfigurationen går du till **Azure Active Directory**  - &gt; **företags program**  - &gt; **alla program** program-  - &gt; **- &gt; proxyn**. På den här sidan kan du se fältet "förautentisering" och se till att är inställt på "Azure Active Directory. 

Mer information om metoderna för autentisering finns i steg 4 i [appens publicerings dokument](application-proxy-add-on-premises-application.md).

   ![Metod för förautentisering i Azure Portal](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Konfigurera läge för enkel inloggning för program för programproxy
Konfigurera den speciella typen av enkel inloggning. Inloggnings metoderna klassificeras baserat på vilken typ av autentisering som server dels programmet använder. App proxy-program stöder tre typer av inloggning:

-   **Lösenordsbaserad inloggning**: lösenordsbaserad inloggning kan användas för alla program som använder fälten användar namn och lösen ord för att logga in. Konfigurations steg finns i [Konfigurera enkel inloggning med lösen ord för ett Azure AD Gallery-program](configure-password-single-sign-on-non-gallery-applications.md).

-   **Integrerad Windows-autentisering**: för program som använder integrerad Windows-autentisering (IWA) aktive ras enkel inloggning via Kerberos-begränsad delegering (KCD). Den här metoden ger Application Proxy-anslutningar behörighet i Active Directory för att personifiera användare och för att skicka och ta emot token för deras räkning. Information om hur du konfigurerar KCD finns i den [enskilda Sign-On med KCD-dokumentationen](application-proxy-configure-single-sign-on-with-kcd.md).

-   **Rubrikbaserade inloggningar**: en rubrik baserad inloggning används för att tillhandahålla funktioner för enkel inloggning med HTTP-huvuden. Mer information finns i [sidhuvud-baserad enkel inloggning](application-proxy-configure-single-sign-on-with-headers.md).

-   **SAML enkel inloggning**: med enkel inloggning med SAML, autentiserar Azure AD programmet med hjälp av användarens Azure AD-konto. Azure AD kommunicerar inloggnings informationen till programmet via ett anslutnings protokoll. Med SAML-baserad enkel inloggning kan du mappa användare till specifika program roller baserat på regler som du definierar i dina SAML-anspråk. Information om hur du konfigurerar SAML enkel inloggning finns i [SAML för enkel inloggning med Application Proxy](application-proxy-configure-single-sign-on-on-premises-apps.md).

Vart och ett av dessa alternativ finns i ditt program i "företags program" och öppnar sidan för **enkel inloggning** på den vänstra menyn. Observera att om ditt program skapades på den gamla portalen kanske du inte ser alla dessa alternativ.

På den här sidan kan du också se ytterligare ett Sign-On alternativ: länkad inloggning. Det här alternativet stöds också av Application Proxy. Men det här alternativet lägger inte till enkel inloggning i programmet. Detta program kanske redan har en enkel inloggning implementerad med hjälp av en annan tjänst, till exempel Active Directory Federation Services (AD FS). 

Med det här alternativet kan en administratör skapa en länk till ett program som användarna första landerar vid åtkomst till programmet. Om det till exempel finns ett program som är konfigurerat för att autentisera användare som använder Active Directory Federation Services (AD FS) 2,0 kan en administratör använda alternativet "länkad inloggning" för att skapa en länk till den i Mina appar.

## <a name="next-steps"></a>Nästa steg
- [Lösen ords valv för enkel inloggning med programproxy](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Kerberos-begränsad delegering för enkel inloggning med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)
- [Huvud-baserad autentisering för enkel inloggning med Application Proxy](application-proxy-configure-single-sign-on-with-headers.md) 
- [SAML för enkel inloggning med programproxy](application-proxy-configure-single-sign-on-on-premises-apps.md).
