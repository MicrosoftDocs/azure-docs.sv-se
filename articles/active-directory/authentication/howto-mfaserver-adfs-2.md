---
title: Använd Azure MFA server med AD FS 2,0-Azure Active Directory
description: Det här är sidan om Azure Multi-Factor Authentication som beskriver hur du kommer igång med Azure MFA och AD FS 2.0.
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
ms.openlocfilehash: d144716dbc5636451405f1124d4c3949f585d6b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96742654"
---
# <a name="configure-azure-multi-factor-authentication-server-to-work-with-ad-fs-20"></a>Konfigurera Azure Multi-Factor Authentication Server så att den fungerar med AD FS 2.0

Den här artikeln är för organisationer som federerade med Azure Active Directory och som vill skydda resurser som finns lokalt eller i molnet. Skydda dina resurser genom att använda Azure Multi-Factor Authentication-servern och konfigurera den för att fungera med AD FS så att tvåstegsverifiering utlöses för slutpunkter med högt värde.

Den här dokumentationen beskriver hur du använder Azure Multi-Factor Authentication Server med AD FS 2.0. Mer information om AD FS finns i [Skydda molnresurser och lokala resurser med Azure Multi-Factor Authentication Server med Windows Server 2012 R2 AD FS](howto-mfaserver-adfs-2012.md).

> [!IMPORTANT]
> Från den 1 juli 2019 erbjuder Microsoft inte längre MFA-Server för nya distributioner. Nya kunder som vill kräva Multi-Factor Authentication (MFA) vid inloggnings händelser bör använda molnbaserad Azure AD-Multi-Factor Authentication.
>
> Information om hur du kommer igång med molnbaserad MFA finns i [Självstudier: skydda användar inloggnings händelser med Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Om du använder molnbaserad MFA, se [skydda moln resurser med Azure AD Multi-Factor Authentication och AD FS](howto-mfa-adfs.md).
>
> Befintliga kunder som aktiverade MFA server före den 1 juli 2019 kan ladda ned den senaste versionen, framtida uppdateringar och generera autentiseringsuppgifter för aktivering som vanligt.

## <a name="secure-ad-fs-20-with-a-proxy"></a>Skydda AD FS 2.0 med en proxy

Om du vill skydda AD FS 2.0 med en proxy installerar du Azure Multi-Factor Authentication Server på ADFS-proxyservern.

### <a name="configure-iis-authentication"></a>Konfigurera IIS-autentisering

1. Klicka på ikonen för **IIS-autentisering** på den vänstra menyn i Azure Multi-Factor Authentication Server.
2. Klicka på fliken **Formulärbaserad**.
3. Klicka på **Lägg till**.

   ![IIS-autentiseringsläge för MFA Server](./media/howto-mfaserver-adfs-2/setup1.png)

4. Ange inloggnings-URL (t. ex. `https://sso.contoso.com/adfs/ls` ) i dialog rutan konfigurera Form-Based webbplats automatiskt och klicka på **OK** för att identifiera användar namn, lösen ord och domän variabler automatiskt.
5. Markera rutan **Kräv Azure Multi-Factor Authentication-användarmatchning** om alla användare har importerats eller ska importeras till servern och använda tvåstegsverifiering. Om ett stort antal användare inte har importerats till servern än, eller om de ska undantas från tvåstegsverifiering, lämnar du rutan avmarkerad.
6. Om sidvariablerna inte kan identifieras automatiskt klickar du på knappen **Ange manuellt** i dialogrutan Konfigurera formulärbaserad webbplats automatiskt.
7. I dialog rutan Lägg till Form-Based webbplats anger du URL: en till sidan för AD FS inloggning i fältet överförings-URL (som `https://sso.contoso.com/adfs/ls` ) och anger ett program namn (valfritt). Programnamnet visas i Azure Multi-Factor Authentication-rapporter och kan visas i autentiseringsmeddelanden i SMS- eller mobilappar.
8. Ange formatet för begäran till **POST eller GET**.
9. Ange användarnamnsvariabeln (ctl00$ContentPlaceHolder1$UsernameTextBox) och lösenordsvariabeln (ctl00$ContentPlaceHolder1$PasswordTextBox). Om en textruta för domänen visas på den formulärbaserade inloggningssidan anger du även domänvariabeln. Du kan behöva gå till inloggningssidan i en webbläsare, högerklicka på sidan och välja **Visa källa** för att hitta namnen på inmatningsrutorna på inloggningssidan.
10. Markera rutan **Kräv Azure Multi-Factor Authentication-användarmatchning** om alla användare har importerats eller ska importeras till servern och använda tvåstegsverifiering. Om ett stort antal användare inte har importerats till servern än, eller om de ska undantas från tvåstegsverifiering, lämnar du rutan avmarkerad.

    ![Lägg till formulärbaserad webbplats i MFA Server](./media/howto-mfaserver-adfs-2/manual.png)

11. Klicka på **Avancerat...** och granska de avancerade inställningarna. Bland de inställningar som du kan konfigurera finns följande:

    - Välj en anpassad nekandeväxlingsfil
    - Cachelagra lyckade autentiseringar till webbplatsen med hjälp av cookies
    - Välj hur du vill autentisera primära autentiseringsuppgifter

12. Eftersom AD FS-proxyservern troligen inte ska anslutas till domänen kan du använda LDAP för att ansluta till domänkontrollanten för användarimport och förautentisering. Klicka på fliken **Primär autentisering** i dialogrutan Avancerad formulärbaserad webbplats och välj **LDAP-bindning** för autentiseringstypen Förautentisering.
13. När du är klar klickar du på **OK** så visas dialogrutan Lägg till formulärbaserad webbplats igen.
14. Stäng dialogrutan genom att klicka på **OK**.
15. När URL- och sidvariablerna har identifierats eller angetts visas webbplatsdata på panelen Formulärbaserad.
16. Klicka på fliken **ursprunglig modul** och välj servern, webbplatsen som AD FS proxyn körs under (t. ex. "standard webbplats") eller AD FS proxy-programmet (t. ex. "LS" under "ADFS") för att aktivera IIS-plugin-programmet på önskad nivå.
17. Klicka på rutan **Aktivera IIS-autentisering** överst på skärmen.

Nu är IIS-autentisering aktiverat.

### <a name="configure-directory-integration"></a>Konfigurera katalogintegrering

Du har aktiverat IIS-autentisering, men för att kunna utföra förautentisering till din Active Directory (AD) via LDAP måste du konfigurera LDAP-anslutningen till domänkontrollanten.

1. Det gör du genom att klicka på ikonen **Katalogintegrering**.
2. På fliken Inställningar väljer du alternativ knappen **Använd angiven LDAP-konfiguration** .

   ![Konfigurera LDAP-inställningar för vissa LDAP-inställningar](./media/howto-mfaserver-adfs-2/ldap1.png)

3. Klicka på **Redigera**.
4. I dialogrutan Redigera LDAP-konfiguration fyller du i fälten med den information som krävs för att ansluta till AD-domänkontrollanten. Fältbeskrivningar finns inkluderade i hjälpfilen för Azure Multi-Factor Authentication-server.
5. Testa LDAP-anslutningen genom att klicka på knappen **testa** .

   ![Testa LDAP-konfiguration i MFA Server](./media/howto-mfaserver-adfs-2/ldap2.png)

6. Klicka på **OK** om LDAP-anslutningstestet lyckas.

### <a name="configure-company-settings"></a>Konfigurera företagsinställningar

1. Klicka sedan på ikonen **företags inställningar** och välj fliken **matcha användar namn** .
2. Välj alternativ knappen **Använd LDAP-unik identifierare för matchning av användar namn** .
3. Om användarna anger sina användar namn i formatet "domän \ användar namn", måste servern kunna ta bort domänen från användar namnet när LDAP-frågan skapas. Detta kan göras via en registerinställning.
4. Öppna Registereditorn och gå till HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/Positive Networks/PhoneFactor på en 64-bitarsserver. Om du använder en 32-bitars server tar du bort "Wow6432Node" från sökvägen. Skapa en DWORD-registernyckeln med namnet "UsernameCxz_stripPrefixDomain" och ange värdet till 1. Nu skyddas AD FS-proxyn av Azure Multi-Factor Authentication.

Se till att användarna har importerats från Active Directory till servern. Se [avsnittet betrodda IP](#trusted-ips) -adresser om du vill tillåta interna IP-adresser så att tvåstegsverifiering inte krävs vid inloggning till webbplatsen från dessa platser.

![Registereditorn för att konfigurera företags inställningar](./media/howto-mfaserver-adfs-2/reg.png)

## <a name="ad-fs-20-direct-without-a-proxy"></a>AD FS 2.0 direkt utan någon proxy

Du kan skydda AD FS när AD FS-proxy inte används. Installera Azure Multi-Factor Authentication Server på AD FS-servern och konfigurera servern genom att följa stegen nedan:

1. Klicka på ikonen för **IIS-autentisering** på den vänstra menyn i Azure Multi-Factor Authentication Server.
2. Klicka på fliken **HTTP**.
3. Klicka på **Lägg till**.
4. I dialog rutan Lägg till grundläggande webb adress anger du URL: en för den AD FS webbplats där HTTP-autentiseringen utförs (t `https://sso.domain.com/adfs/ls/auth/integrated` . ex.) i fältet bas-URL. Ange sedan ett programnamn (valfritt). Programnamnet visas i Azure Multi-Factor Authentication-rapporter och kan visas i autentiseringsmeddelanden i SMS- eller mobilappar.
5. Om du vill justerar du värdena för timeout vid inaktivitet och längsta sessionstid.
6. Markera rutan **Kräv Azure Multi-Factor Authentication-användarmatchning** om alla användare har importerats eller ska importeras till servern och använda tvåstegsverifiering. Om ett stort antal användare inte har importerats till servern än, eller om de ska undantas från tvåstegsverifiering, lämnar du rutan avmarkerad.
7. Markera kryssrutan för cachelagring av cookies om du vill.

   ![AD FS 2.0 direkt utan någon proxy](./media/howto-mfaserver-adfs-2/noproxy.png)

8. Klicka på **OK**.
9. Klicka på fliken **ursprunglig modul** och välj servern, webbplatsen (t. ex. "standard webbplats") eller AD FS program (t. ex. "LS" under "ADFS") för att aktivera IIS-plugin-programmet på önskad nivå.
10. Klicka på rutan **Aktivera IIS-autentisering** överst på skärmen.

Nu skyddas AD FS av Azure Multi-Factor Authentication.

Se till att användarna har importerats från Active Directory till servern. Se avsnittet betrodda IP-adresser om du vill tillåta interna IP-adresser så att tvåstegsverifiering inte krävs vid inloggning till webbplatsen från dessa platser.

## <a name="trusted-ips"></a>Tillförlitliga IP-adresser

Tillförlitliga IP-adresser låter användarna kringgå Azure Multi-Factor Authentication för webbplatsförfrågningar som kommer från specifika IP-adresser eller undernät. Du vill kanske till exempel undanta användare från tvåstegsverifiering när de loggar in från kontoret. För att göra det anger du kontorets undernät som en tillförlitlig IP-adress.

### <a name="to-configure-trusted-ips"></a>Så här konfigurerar du tillförlitliga IP-adresser

1. Klicka på fliken **tillförlitliga IP-adresser** i avsnittet IIS-autentisering.
2. Klicka på **Lägg …** Redigera.
3. När dialogrutan Lägg till tillförlitlig IP-adress visas väljer du en av alternativknapparna **Enkel IP**, **IP-intervall** eller **Undernät**.
4. Ange IP-adressen, intervallet för IP-adresser eller undernät som ska tillåtas. Om du anger ett undernät väljer du lämplig nätmask och klickar på **OK**.

![Konfigurera betrodda IP-adresser till MFA Server](./media/howto-mfaserver-adfs-2/trusted.png)
