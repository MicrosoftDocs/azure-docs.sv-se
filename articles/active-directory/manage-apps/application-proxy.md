---
title: Fjärråtkomst till lokala appar – Azure AD-programproxy
description: Azure Active Directorys Application Proxy ger säker fjärråtkomst till lokala webb program. Efter en enkel inloggning till Azure AD kan användare komma åt både molnet och lokala program via en extern URL eller en intern applikations Portal. Till exempel kan programproxy tillhandahålla fjärråtkomst och enkel inloggning till fjärr skrivbord, SharePoint, teams, Tableau, Qlik och LOB-program (Line of Business).
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 40472dc2446133ec23d1d62343a93d2d69fc90cd
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99260154"
---
# <a name="remote-access-to-on-premises-applications-through-azure-ad-application-proxy"></a>Fjärråtkomst till lokala program via Azure AD-programproxy

Azure Active Directorys Application Proxy ger säker fjärråtkomst till lokala webb program. Efter en enkel inloggning till Azure AD kan användare komma åt både molnet och lokala program via en extern URL eller en intern applikations Portal. Till exempel kan programproxy tillhandahålla fjärråtkomst och enkel inloggning till fjärr skrivbord, SharePoint, teams, Tableau, Qlik och LOB-program (Line of Business).

Azure AD-programproxy är:

- **Enkelt att använda**. Användare kan komma åt dina lokala program på samma sätt som de har åtkomst till Microsoft 365 och andra SaaS-appar som är integrerade med Azure AD. Du behöver inte ändra eller uppdatera dina program för att fungera med programproxy.

- **Säker**. Lokala program kan använda Azures verifierings kontroller och säkerhets analys. Lokala program kan till exempel använda villkorlig åtkomst och tvåstegsverifiering. Application Proxy kräver inte att du öppnar inkommande anslutningar via brand väggen.

- **Kostnads effektivt**. Lokala lösningar kräver vanligt vis att du konfigurerar och underhåller demilitariserad-zoner (DMZs), Edge-servrar eller andra komplexa infrastrukturer. Programproxyn körs i molnet, vilket gör det enkelt att använda. Om du vill använda Application Proxy behöver du inte ändra nätverks infrastrukturen eller installera ytterligare enheter i din lokala miljö.

## <a name="what-is-application-proxy"></a>Vad är Application Proxy?
Application Proxy är en funktion i Azure AD som gör det möjligt för användare att komma åt lokala webb program från en fjärran sluten klient. Programproxyn innehåller både tjänsten Application Proxy som körs i molnet och Application Proxy Connector som körs på en lokal server. Azure AD, Application Proxy-tjänsten och Application Proxy-anslutningsprogrammet fungerar tillsammans för att på ett säkert sätt skicka användar inloggnings-token från Azure AD till webb programmet.

Application Proxy fungerar med:

* Webb program som använder [integrerad Windows-autentisering](application-proxy-configure-single-sign-on-with-kcd.md) för autentisering
* Webb program som använder form-baserad eller [rubrik baserad](./application-proxy-configure-single-sign-on-with-headers.md) åtkomst
* Webb-API: er som du vill exponera för rika program på olika enheter
* Program som finns bakom en [fjärr skrivbords-Gateway](application-proxy-integrate-with-remote-desktop-services.md)
* Rika klient program som är integrerade med Microsoft Authentication Library (MSAL)

Application Proxy stöder enkel inloggning. Mer information om metoder som stöds finns i [välja en metod för enkel inloggning](sso-options.md#choosing-a-single-sign-on-method).

Application Proxy rekommenderas för att ge fjärran vändare åtkomst till interna resurser. Programproxyn ersätter behovet av en VPN-eller omvänd proxy. Den är inte avsedd för interna användare i företags nätverket.  Dessa användare som inte nödvändigt vis använder Application Proxy kan orsaka oväntade och oönskade prestanda problem.

## <a name="how-application-proxy-works"></a>Så här fungerar tillämpningsproxy

Följande diagram visar hur Azure AD och Application Proxy arbetar tillsammans för att tillhandahålla enkel inloggning till lokala program.

![Diagram över AzureAD-programproxy](./media/application-proxy/azureappproxxy.png)

1. När användaren har åtkomst till programmet via en slut punkt dirigeras användaren till inloggnings sidan för Azure AD.
2. Efter en lyckad inloggning skickar Azure AD en token till användarens klient enhet.
3. Klienten skickar token till Application Proxy-tjänsten som hämtar User Principal Name (UPN) och säkerhets huvud namn (SPN) från token. Application Proxy skickar sedan begäran till Application Proxy Connector.
4. Om du har konfigurerat enkel inloggning utför anslutningen all ytterligare autentisering som krävs för användarens räkning.
5. Anslutningen skickar begäran till det lokala programmet.
6. Svaret skickas via tjänsten anslutnings-och programproxy till användaren.

> [!NOTE]
> Precis som i de flesta Azure AD hybrid-agenter kräver inte Application Proxy Connector att du öppnar inkommande anslutningar via brand väggen. Användar trafiken i steg 3 upphör med Application Proxy-tjänsten (i Azure AD). Application Proxy-anslutningen (lokalt) är ansvarig för resten av kommunikationen.
>


| Komponent | Beskrivning |
| --------- | ----------- |
| Slutpunkt  | Slut punkten är en URL eller en [slut användar Portal](end-user-experiences.md). Användare kan nå program utanför nätverket genom att ha åtkomst till en extern URL. Användare i nätverket kan komma åt programmet via en URL eller en slut användar Portal. När användarna går till någon av dessa slut punkter, autentiseras de i Azure AD och dirigeras sedan via anslutningen till det lokala programmet.|
| Azure AD | Azure AD utför autentiseringen med hjälp av klient katalogen som lagras i molnet. |
| Application Proxy-tjänst | Tjänsten Application Proxy körs i molnet som en del av Azure AD. Den skickar inloggnings-token från användaren till Application Proxy Connector. Programproxyn vidarebefordrar eventuella tillgängliga huvuden på begäran och anger huvudena enligt dess protokoll till klientens IP-adress. Om den inkommande begäran till proxyn redan har den rubriken, läggs klientens IP-adress till i slutet av den kommaavgränsade listan som är värdet för rubriken.|
| Application Proxy Connector | Anslutningen är en Lightweight-agent som körs på en Windows-Server i nätverket. Anslutningen hanterar kommunikationen mellan Application Proxy-tjänsten i molnet och det lokala programmet. Anslutningen använder bara utgående anslutningar, så du behöver inte öppna några inkommande portar eller publicera något i DMZ. Anslutningarna är tillstånds lösa och hämtar information från molnet om det behövs. Mer information om anslutningar, t. ex. hur de kan belastningsutjämna och autentiseras, finns i [förstå Azure AD-programproxy-kopplingar](application-proxy-connectors.md).|
| Active Directory (AD) | Active Directory körs lokalt för att utföra autentisering för domän konton. När enkel inloggning har kon figurer ATS kommunicerar anslutningen med AD för att utföra ytterligare autentisering krävs.
| Lokalt program | Slutligen kan användaren komma åt ett lokalt program.

## <a name="next-steps"></a>Nästa steg
Information om hur du börjar använda Application Proxy finns i [Självstudier: Lägg till ett lokalt program för fjärråtkomst via programproxy](application-proxy-add-on-premises-application.md).