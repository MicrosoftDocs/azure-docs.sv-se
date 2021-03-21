---
title: IIS-autentisering och Azure MFA Server – Azure Active Directory
description: Distribuera IIS-autentisering och Azure Multi-Factor Authentication-server.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c522fb950e2489afce44553d233ac3eddf138730
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96742178"
---
# <a name="configure-azure-multi-factor-authentication-server-for-iis-web-apps"></a>Konfigurera Azure Multi-Factor Authentication Server för IIS-webbappar

Via IIS-autentisering på Azure Multi-Factor Authentication-servern kan du aktivera och konfigurera IIS-autentisering för integrering med Microsoft IIS-webbprogram. Azure MFA Server installerar ett plugin-program som kan filtrera begäranden som görs till IIS-webbservern för att lägga till Azure Multi-Factor Authentication. IIS-plugin-programmet har stöd för formulärbaserad autentisering och integrerad Windows-HTTP-autentisering. Betrodda IP-adresser kan även konfigureras att undanta interna IP-adresser från tvåfaktorsautentisering.

> [!IMPORTANT]
> Från den 1 juli 2019 erbjuder Microsoft inte längre MFA-Server för nya distributioner. Nya kunder som vill kräva Multi-Factor Authentication (MFA) vid inloggnings händelser bör använda molnbaserad Azure AD-Multi-Factor Authentication.
>
> Information om hur du kommer igång med molnbaserad MFA finns i [Självstudier: skydda användar inloggnings händelser med Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Befintliga kunder som aktiverade MFA server före den 1 juli 2019 kan ladda ned den senaste versionen, framtida uppdateringar och generera autentiseringsuppgifter för aktivering som vanligt.
>
> När du använder molnbaserade Azure-Multi-Factor Authentication finns det inget alternativ till det IIS-plugin-program som tillhandahålls av Azure Multi-Factor Authentication-servern (MFA). Använd i stället Webbprogramproxy (WAP) med Active Directory Federation Services (AD FS) (AD FS) eller Azure Active Directory programmets programproxy.

![IIS-autentisering i MFA Server](./media/howto-mfaserver-iis/iis.png)

## <a name="using-form-based-iis-authentication-with-azure-multi-factor-authentication-server"></a>Använda formulärbaserad IIS-autentisering med Azure Multi-Factor Authentication Server

Om du ska skydda en IIS-webbapp som använder formulärbaserad autentisering installerar du Azure Multi-Factor Authentication Server på IIS-webbservern och konfigurerar servern genom att följa stegen nedan:

1. Klicka på ikonen för IIS-autentisering på den vänstra menyn i Azure Multi-Factor Authentication Server.
2. Klicka på fliken **Formulärbaserad**.
3. Klicka på **Lägg till**.
4. Ange inloggnings-URL (t. ex. `https://localhost/contoso/auth/login.aspx` ) i dialog rutan konfigurera Form-Based webbplats automatiskt och klicka på **OK** för att identifiera användar namn, lösen ord och domän variabler automatiskt.
5. Markera rutan om att **Multi-Factor Authentication-användarmatchning krävs** om alla användare har importerats eller ska importeras till servern och om multifaktorautentisering används. Om ett stort antal användare inte har importerats till servern än, eller om de ska undantas från multifaktorautentisering, lämnar du rutan avmarkerad.
6. Om sidvariablerna inte kan identifieras automatiskt klickar du på **Ange manuellt** i dialogrutan Konfigurera formulärbaserad webbplats automatiskt.
7. I dialogrutan Lägg till formulärbaserad webbplats anger du URL:en till inloggningssidan i fältet Skicka URL och anger ett programnamn (valfritt). Programnamnet visas i Azure Multi-Factor Authentication-rapporter och kan visas i autentiseringsmeddelanden i SMS- eller mobilappar.
8. Välj rätt format för begäran. Detta är inställt på **POST eller GET** för de flesta webbprogram.
9. Ange variabeln Användarnamn, Lösenord och Domän (om den visas på inloggningssidan). Du kan behöva gå till inloggningssidan i en webbläsare, högerklicka på sidan och välja **Visa källa** för att hitta namnen på inmatningsrutorna.
10. Markera rutan om att **Multi-Factor Authentication-användarmatchning krävs** om alla användare har importerats eller ska importeras till servern och om multifaktorautentisering används. Om ett stort antal användare inte har importerats till servern än, eller om de ska undantas från multifaktorautentisering, lämnar du rutan avmarkerad.
11. Klicka på **Avancerat** för att granska avancerade inställningar, inklusive:

    - Välj en anpassad nekandeväxlingsfil
    - Cachelagra lyckade autentiseringar på webbplatsen under en period med hjälp av cookies
    - Välj om du vill autentisera primära autentiseringsuppgifter mot en Windows-domän, LDAP-katalogen eller RADIUS-servern.

12. Klicka på **OK** så visas dialogrutan Lägg till formulärbaserad webbplats igen.
13. Klicka på **OK**.
14. När URL- och sidvariablerna har identifierats eller angetts visas webbplatsdata på panelen Formulärbaserad.

## <a name="using-integrated-windows-authentication-with-azure-multi-factor-authentication-server"></a>Använda integrerad Windows-autentisering med Azure Multi-Factor Authentication Server

Om du ska skydda en IIS-webbapp som använder integrerad Windows HTTP-autentisering installerar du Azure MFA Server på IIS-webbservern och konfigurerar servern genom att följa stegen nedan:

1. Klicka på ikonen för IIS-autentisering på den vänstra menyn i Azure Multi-Factor Authentication Server.
2. Klicka på fliken **HTTP**.
3. Klicka på **Lägg till**.
4. I dialog rutan Lägg till grundläggande webb adress anger du URL: en för den webbplats där HTTP-autentiseringen utförs (t `http://localhost/owa` . ex.) och anger ett program namn (valfritt). Programnamnet visas i Azure Multi-Factor Authentication-rapporter och kan visas i autentiseringsmeddelanden i SMS- eller mobilappar.
5. Justera tiderna för Timeout vid inaktivitet och Längsta session om standardvärdet inte räcker.
6. Markera rutan om att **Multi-Factor Authentication-användarmatchning krävs** om alla användare har importerats eller ska importeras till servern och om multifaktorautentisering används. Om ett stort antal användare inte har importerats till servern än, eller om de ska undantas från multifaktorautentisering, lämnar du rutan avmarkerad.
7. Markera kryssrutan för **cachelagring av cookies** om du vill det.
8. Klicka på **OK**.

## <a name="enable-iis-plug-ins-for-azure-multi-factor-authentication-server"></a>Aktivera IIS plugin-program för Azure Multi-Factor Authentication Server

När du har konfigurerat URL:erna och inställningarna för formulärbaserad autentisering eller HTTP-autentisering måste du välja de platser där Azure Multi-Factor Authentication IIS plugin-programmen ska läsas in och aktiveras i IIS. Följ dessa steg:

1. Om du kör på IIS 6 klickar du på fliken **ISAPI** . Välj den webbplats som webb programmet körs under (t. ex. standard webbplats) för att aktivera plugin-programmet för Azure-Multi-Factor Authentication ISAPI-filter för den platsen.
2. Om du kör på IIS 7 eller senare klickar du på fliken **ursprunglig modul** . Välj server, webbplatser eller program för att aktivera IIS-plugin-programmet på önskade nivåer.
3. Klicka på rutan **Aktivera IIS-autentisering** överst på skärmen. Nu skyddar Azure Multi-Factor Authentication det valda IIS-programmet. Kontrollera att användarna har importerats till servern.

## <a name="trusted-ips"></a>Tillförlitliga IP-adresser

Tillförlitliga IP-adresser låter användarna kringgå Azure Multi-Factor Authentication för webbplatsförfrågningar som kommer från specifika IP-adresser eller undernät. Du kanske till exempel vill undanta användare från Azure Multi-Factor Authentication när de loggar in från kontoret. För att göra det anger du kontorets undernät som en tillförlitlig IP-adress. Följ dessa steg om du vill konfigurera tillförlitliga IP-adresser:

1. Klicka på fliken **tillförlitliga IP-adresser** i avsnittet IIS-autentisering.
2. Klicka på **Lägg till**.
3. När dialogrutan för att lägga till tillförlitliga IP-adresser visas väljer du alternativknappen **Enkel IP**, **IP-intervall** eller **Undernät**.
4. Ange IP-adressen, intervallet för IP-adresser eller undernät som ska tillåtas. Om du anger ett undernät väljer du lämplig nätmask och klickar på **OK**.
