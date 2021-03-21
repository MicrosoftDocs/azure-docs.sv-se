---
title: Planera och felsöka ändringar i Azure User Principal Name (UPN)
description: Förstå kända problem och begränsningar vid ändringar i UPN
services: active-directory
ms.service: active-directory
ms.subservice: hybrid
ms.topic: how-to
ms.date: 03/13/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19e40d135316c1c7cd270d2804fff1f487937685
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96858543"
---
# <a name="plan-and-troubleshoot-user-principal-name-changes-in-azure-active-directory"></a>Planera och Felsök ändringar av UPN-namn i Azure Active Directory

Ett UPN (User Principal Name) är ett attribut som är en Internet kommunikations standard för användar konton. Ett UPN består av ett UPN-prefix (användar kontots namn) och ett UPN-suffix (ett DNS-domännamn). Prefixet ansluter till suffixet med hjälp av \@ symbolen "". Till exempel someone@example.com. Ett UPN måste vara unikt bland alla säkerhets objekt i en katalog skog. 

**I den här artikeln förutsätter vi att du använder UPN som användar-ID. IT-adresser planerar för ändringar i UPN och återställer från problem som kan uppstå vid ändringar i UPN.**

> [!NOTE]
> För utvecklare rekommenderar vi att du använder användaren objectID som det oföränderliga ID: t, i stället för UPN eller e-postadresser som deras värden kan ändras.


## <a name="learn-about-upns-and-upn-changes"></a>Läs om UPN-och UPN-ändringar
Inloggnings sidor uppmanas ofta användarna att ange sin e-postadress när det obligatoriska värdet faktiskt är deras UPN. Därför bör du vara noga med att ändra användarens UPN när deras primära e-postadress ändras.

Användarens primära e-postadresser kan ändras av många orsaker:

* omanpassning av företaget

* anställda flyttar till olika företags indelningar 

* sammanslagningar och förvärv

* ändringar av medarbetares namn

### <a name="types-of-upn-changes"></a>Typer av UPN-ändringar

Du kan ändra ett UPN genom att ändra prefix, suffix eller både och.

* **Ändrar prefixet**.

   *  Om till exempel en persons namn har ändrats kan du ändra kontots namn:  
BSimon@contoso.comtillBJohnson@contoso.com

   * Du kan också ändra företags standarden för prefix:  
Bsimon@contoso.comtillBritta.Simon@contoso.com

* **Ändra suffixet**. <br>

    Om till exempel en person har ändrat divisioner kan du ändra deras domän: 

   * Britta.Simon@contoso.com att Britta.Simon@contosolabs.com <br>
     Eller<br>
    * Britta.Simon@corp.contoso.com att Britta.Simon@labs.contoso.com 

Vi rekommenderar att ändra användarens UPN varje gång deras primära e-postadress uppdateras.

Under den inledande synkroniseringen från Active Directory till Azure AD ser du till att användarnas e-postmeddelanden är identiska med sina UPN.

### <a name="upns-in-active-directory"></a>UPN i Active Directory

I Active Directory är standard UPN-suffixet DNS-namnet på den domän där du skapade användar kontot. I de flesta fall är detta det domän namn som du registrerar som företags domän på Internet. Om du skapar användar kontot i contoso.com-domänen är standard-UPN

username@contoso.com

 Du kan dock [lägga till fler UPN-suffix](../fundamentals/add-custom-domain.md) genom att använda Active Directory domäner och förtroenden. 

Till exempel kanske du vill lägga till labs.contoso.com och användarens UPN-och e-postmeddelanden visar att. De blir då

username@labs.contoso.com.

>[!IMPORTANT]
> Om du [ändrar suffixet i Active Directory](../fundamentals/add-custom-domain.md)måste du se till att ett matchande anpassat domän namn har [lagts till och verifierats i Azure AD](../fundamentals/add-custom-domain.md). 

![En skärm bild av verifierade domäner](./media/howto-troubleshoot-upn-changes/custom-domains.png)

### <a name="upns-in-azure-active-directory"></a>UPN i Azure Active Directory

Användarna loggar in på Azure AD med värdet i sitt userPrincipalName-attribut. 

När du använder Azure AD tillsammans med din lokala Active Directory, synkroniseras användar konton med hjälp av tjänsten Azure AD Connect. Som standard använder guiden för Azure AD Connect attributet userPrincipalName från den lokala Active Directory som UPN i Azure AD. Du kan ändra den till ett annat attribut i en anpassad installation.

Det är viktigt att du har en definierad process när du uppdaterar ett UPN (User Principal Name) för en enskild användare eller för hela organisationen. 

Se kända problem och lösningar i det här dokumentet.

När du synkroniserar användar konton från Active Directory till Azure AD kontrollerar du att UPN i Active Directory mappar till verifierade domäner i Azure AD.

![Skärm bild som visar exempel på UPN-mappningar som har mappats till verifierade Azure A D-domäner.](./media/howto-troubleshoot-upn-changes/verified-domains.png)

Om värdet för userPrincipalName-attributet inte motsvarar en verifierad domän i Azure AD, ersätter synkroniseringsprocessen suffixet med värdet default. onmicrosoft.com.


### <a name="roll-out-bulk-upn-changes"></a>Distribuera Mass ändringar i UPN

Följ de [rekommenderade tipsen för en pilot](../fundamentals/active-directory-deployment-plans.md) för Mass ändringar av UPN. Du kan också få en testad återställnings plan för att återställa UPN om du upptäcker problem som inte kan lösas snabbt. När piloten har körts kan du börja rikta in små uppsättningar med användare med olika organisatoriska roller och deras specifika uppsättningar av appar eller enheter.

Genom att gå igenom den första delen av användarna får du en bra uppfattning om vad användarna ska förvänta sig som en del av ändringen. Ta med den här informationen om din användar kommunikation.

Skapa en definierad procedur för att ändra UPN för enskilda användare som en del av normal drift. Vi rekommenderar att du har en testad procedur som innehåller dokumentation om kända problem och lösningar.

Följande avsnitt innehåller information om potentiella kända problem och lösningar när UPN-namn ändras.

## <a name="apps-known-issues-and-workarounds"></a>Kända problem och lösningar för appar

[Program vara som en tjänst (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) och branschspecifika program (LOB) är ofta beroende av UPN för att hitta användare och lagra användar profil information, inklusive roller. Program som använder [just-in-Time-etablering](../app-provisioning/user-provisioning.md) för att skapa en användar profil när användare loggar in på appen för första gången kan påverkas av UPN-ändringar.

**Känt problem**<br>
Om du ändrar en användares UPN kan relationen mellan Azure AD-användaren och användar profilen som skapades i programmet brytas. Om programmet använder  [just vid tids etablering](../app-provisioning/user-provisioning.md)kan det skapa en helt ny användar profil. Detta kräver att program administratören gör manuella ändringar för att åtgärda den här relationen.

**Lösning**<br>
Med [Automatisk användar etablering i Azure AD](../app-provisioning/user-provisioning.md) kan du automatiskt skapa, underhålla och ta bort användar identiteter i moln program som stöds. Konfiguration av automatisk användar etablering i dina program uppdaterar UPN-data automatiskt i programmen. Testa programmen som en del av den Progressive distributionen för att kontrol lera att de inte påverkas av UPN-ändringar.
Om du är utvecklare kan du överväga att [lägga till scim-stöd i programmet](../app-provisioning/use-scim-to-provision-users-and-groups.md) för att aktivera automatisk användar etablering från Azure Active Directory. 

## <a name="managed-devices-known-issues-and-workarounds"></a>Kända problem och lösningar för hanterade enheter

Genom [att ta med dina enheter till Azure AD](../devices/overview.md)kan du maximera användarnas produktivitet genom enkel inloggning (SSO) i molnet och lokala resurser.

### <a name="azure-ad-joined-devices"></a>Azure AD-anslutna enheter

[Azure AD-anslutna](../devices/concept-azure-ad-join.md) enheter är direkt anslutna till Azure AD och gör att användarna kan logga in på enheten med deras organisations identitet.

**Kända problem** <br>
Användare kan stöta på problem med enkel inloggning med program som är beroende av Azure AD för autentisering.

**Lösning** <br>
Problemen som nämns i det här avsnittet har åtgärd ATS i Windows 10 maj 2020 Update (2004).

**Lösning** <br>
Tillåt tillräckligt med tid för att UPN-ändringen ska synkroniseras med Azure AD. När du har kontrollerat att det nya UPN visas på Azure AD-portalen ber du användaren att välja panelen "annan användare" för att logga in med sitt nya UPN. Du kan också kontrol lera genom [PowerShell](/powershell/module/azuread/get-azureaduser). När du har loggat in med sitt nya UPN kan referenser till det gamla UPN fortfarande visas i Windows-inställningen "åtkomst till arbets plats eller skola".

![Skärm bild av verifierade domäner](./media/howto-troubleshoot-upn-changes/other-user.png)


### <a name="hybrid-azure-ad-joined-devices"></a>Hybrid Azure AD-anslutna enheter

[Hybrid Azure AD-anslutna](../devices/concept-azure-ad-join-hybrid.md) enheter är anslutna till Active Directory och Azure AD. Du kan implementera hybrid Azure AD-anslutning om din miljö har en lokal Active Directory plats och du även vill dra nytta av de funktioner som tillhandahålls av Azure AD.

**Kända problem** 

Windows 10 hybrid Azure AD-anslutna enheter upplever förmodligen oväntade omstarter och åtkomst problem.

Om användarna loggar in på Windows innan det nya UPN har synkroniserats till Azure AD, eller om du fortsätter att använda en befintlig Windows-session, kan det uppstå problem med enkel inloggning med program som använder Azure AD för autentisering om villkorlig åtkomst har kon figurer ATS för att framtvinga användning av hybrid anslutna enheter för att få åtkomst till resurser. 

Dessutom visas följande meddelande, och en omstart görs efter en minut. 

"Datorn kommer att startas om automatiskt om en minut. Windows stötte på ett problem och måste startas om. Du bör stänga det här meddelandet nu och spara ditt arbete.

**Lösning** <br>
Problemen som nämns i det här avsnittet har åtgärd ATS i Windows 10 maj 2020 Update (2004).

**Lösning** 

Enheten måste vara frånkopplad från Azure AD och startas om. Efter omstarten kommer enheten automatiskt att ansluta till Azure AD igen och användaren måste logga in med det nya UPN: en genom att välja panelen "annan användare". Om du vill koppla från en enhet från Azure AD kör du följande kommando i en kommando tolk:

**dsregcmd /leave**

Användaren måste registrera för Windows Hello för företag [igen](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-whfb-provision) om den används. Windows 7-och 8,1-enheter påverkas inte av det här problemet när UPN-ändringar har ändrats.


## <a name="microsoft-authenticator-known-issues-and-workarounds"></a>Microsoft Authenticator kända problem och lösningar

Din organisation kan kräva att [Microsoft Authenticator-appen](../user-help/user-help-auth-app-overview.md) används för att logga in och få åtkomst till organisationens program och data. Även om ett användar namn kan visas i appen är kontot inte konfigurerat att fungera som en verifierings metod förrän användaren har slutfört registrerings processen.

[Microsoft Authenticator-appen](../user-help/user-help-auth-app-overview.md) har fyra huvud funktioner:

* Multi-Factor Authentication via ett push-meddelande eller verifierings kod

* Agera som en autentiseringsprovider på iOS-och Android-enheter för att tillhandahålla enkel inloggning för program som använder [Broker-autentisering](../develop/msal-android-single-sign-on.md)

* Enhets registrering (kallas även Workplace Join) till Azure AD, vilket är ett krav för andra funktioner som Intune-appskydd och enhets registrerings/hantering.

* Telefon inloggning, som kräver MFA och enhets registrering.

### <a name="multi-factor-authentication-with-android-devices"></a>Multi-Factor Authentication med Android-enheter

Microsoft Authenticator-appen erbjuder ett alternativ för out-of-band-verifiering. I stället för att placera ett automatiserat telefonsamtal eller SMS till användaren vid inloggningen skickar [Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md) ett meddelande till Microsoft Authenticator-appen på användarens smartphone eller surfplatta. Användaren trycker bara på Godkänn (eller anger en PIN-kod eller bio metrisk och trycker på "autentisera") i appen för att slutföra sin inloggning.

**Kända problem** 

När du ändrar en användares UPN visas fortfarande det gamla UPN på användar kontot och ett meddelande kanske inte tas emot. [Verifierings koderna](../user-help/user-help-auth-app-faq.md) fortsätter att fungera.

**Lösning**

Om ett meddelande tas emot instruerar du användaren att stänga meddelandet, öppna Authenticator-appen, trycka på alternativet "kontrol lera aviseringar" och godkänna MFA-prompten. Därefter kommer det UPN som visas på kontot att uppdateras. Observera att det uppdaterade UPN kan visas som ett nytt konto, detta beror på andra funktioner för autentisering som används. Mer information hittar du i de ytterligare kända problemen i den här artikeln.

### <a name="brokered-authentication"></a>Brokered Authentication

På Android-och iOS-mäklare som Microsoft Authenticator aktivera:

* Enkel inloggning (SSO) – användarna behöver inte logga in på varje program.

* Enhets identifiering – koordinatorn använder enhets certifikatet som skapades på enheten när den anslöts till arbets platsen.

* Verifiering av program identifiering – när ett program anropar koordinatorn, skickas dess omdirigerings-URL och Service Broker verifierar den.

Dessutom gör det möjligt för program att delta i mer avancerade funktioner, till exempel [villkorlig åtkomst](../conditional-access/index.yml), och stöder [Microsoft Intune scenarier](../develop/msal-net-use-brokers-with-xamarin-apps.md).

**Kända problem**<br>
Användaren visas med fler interaktiva autentiserings-prompter på nya program som använder Broker-assisterad inloggning på grund av ett matchnings fel mellan login_hint som skickats av programmet och det UPN som är lagrat i koordinatorn.

**Lösning** <br> Användaren måste ta bort kontot manuellt från Microsoft Authenticator och starta en ny inloggning från ett Service Broker-program. Kontot läggs automatiskt till efter den första autentiseringen.

### <a name="device-registration"></a>Enhetsregistrering

Microsoft Authenticator-appen ansvarar för att registrera enheten i Azure AD. Enhets registrering gör att enheten kan autentisera till Azure AD och är ett krav för följande scenarier:

* Intune App Protection

* Registrering av Intune-enheter

* Telefonin loggning

**Kända problem**<br>
När du ändrar UPN visas ett nytt konto med det nya UPN som visas i Microsoft Authenticator-appen, medan kontot med det gamla UPN fortfarande visas. Dessutom visas det gamla UPN i avsnittet enhets registrering på app-inställningarna. Det finns ingen förändring i den normala funktionen för enhets registrering eller beroende scenarier.

**Lösning** <br> Om du vill ta bort alla referenser till det gamla UPN i Microsoft Authenticator-appen, instruerar du användaren att manuellt ta bort de gamla och nya kontona från Microsoft Authenticator, registrera om för MFA och ansluta enheten igen.

### <a name="phone-sign-in"></a>Telefonin loggning

Med telefonin loggning kan användare logga in på Azure AD utan lösen ord. Om du vill aktivera telefonin loggning måste användaren registrera sig för MFA med hjälp av Authenticator-appen och sedan aktivera telefonin loggning direkt på autentiseraren. Som en del av konfigurationen registreras enheten med Azure AD.

**Kända problem** <br>
Användarna kan inte använda telefonin loggning eftersom de inte får några meddelanden. Om användaren trycker på Sök efter meddelanden får de ett fel meddelande.

**Lösning**<br>
Användaren måste välja den nedrullningsbara menyn för det konto som har Aktiver ATS för telefonin loggning och välja Inaktivera telefonin loggning. Om du vill kan du aktivera telefonin loggning igen.

## <a name="security-key-fido2-known-issues-and-workarounds"></a>Kända problem och lösningar för säkerhets nyckel (FIDO2)

**Kända problem** <br>
När flera användare registreras på samma nyckel, visar inloggnings skärmen en konto val sida där det gamla UPN visas. Inloggningar som använder säkerhets nycklar påverkas inte av UPN-ändringar.  

**Lösning**<br>
Om du vill ta bort referenser till gamla UPN måste användarna [återställa säkerhets nyckeln och registrera den igen](../authentication/howto-authentication-passwordless-security-key.md#known-issues).

## <a name="onedrive-known-issues-and-workarounds"></a>Kända problem och lösningar i OneDrive

OneDrive-användare är kända för att få problem efter ändringar i UPN. Mer information finns i [hur UPN-ändringar påverkar OneDrive-URL och OneDrive-funktioner](/onedrive/upn-changes).

## <a name="next-steps"></a>Nästa steg

Se följande resurser:
* [Azure AD Connect: utforma begrepp](./plan-connect-design-concepts.md)

* [Ifyllnad av UserPrincipalName för Azure AD](./plan-connect-userprincipalname.md)

* [Microsoft Identity Platform ID-token](../develop/id-tokens.md)
