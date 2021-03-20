---
title: Ifyllnad av UserPrincipalName för Azure AD
description: I följande dokument beskrivs hur UserPrincipalName-attributet fylls i.
author: billmath
ms.subservice: hybrid
ms.author: billmath
ms.date: 06/26/2018
ms.topic: conceptual
ms.workload: identity
ms.service: active-directory
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58b4bbcac110398ee4ff132b76ce8c4868ee17f8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91317599"
---
# <a name="azure-ad-userprincipalname-population"></a>Ifyllnad av UserPrincipalName för Azure AD

I den här artikeln beskrivs hur UserPrincipalName-attributet fylls i Azure Active Directory (Azure AD).
Attributvärdet UserPrincipalName är Azure AD-användarnamnet för användar kontona.

## <a name="upn-terminology"></a>UPN-terminologi
Följande terminologi används i den här artikeln:

|Period|Beskrivning|
|-----|-----|
|Första domän|Standard domänen (onmicrosoft.com) i Azure AD-klienten. Till exempel contoso.onmicrosoft.com.|
|Microsoft Online E-mail routing-adress (MOERA)|Azure AD beräknar MOERA från Azure AD-smek namn-attributet och den första Azure AD-domänen som ett &lt; smek namn för &gt;&#64;&lt; inledande domän &gt; .|
|Lokalt smek namn-attribut|Ett attribut i Active Directory, vars värde representerar alias för en användare i en Exchange-organisation.|
|Lokalt e-postattribut|Ett attribut i Active Directory, vars värde representerar e-postadressen för en användare|
|Primär SMTP-adress|Den primära e-postadressen för ett Exchange-mottagar objekt. Till exempel SMTP: User \@ contoso.com.|
|Alternativt inloggnings-ID|Ett lokalt attribut förutom UserPrincipalName, t. ex. e-postattribut, används för inloggning.|

## <a name="what-is-userprincipalname"></a>Vad är UserPrincipalName?
UserPrincipalName är ett attribut som är ett inloggnings namn för Internet som en användare baserat på Internet standard [RFC 822](https://www.ietf.org/rfc/rfc0822.txt). 

### <a name="upn-format"></a>UPN-format
Ett UPN består av ett UPN-prefix (användar kontots namn) och ett UPN-suffix (ett DNS-domännamn). Prefixet är kopplat till suffixet med hjälp av \@ symbolen "". Till exempel "någon \@ example.com". Ett UPN måste vara unikt bland alla säkerhets objekt i en katalog skog. 

## <a name="upn-in-azure-ad"></a>UPN i Azure AD 
UPN används av Azure AD för att tillåta användare att logga in.  Det UPN som en användare kan använda beror på om domänen har verifierats eller inte.  Om domänen har verifierats kommer en användare med detta suffix att kunna logga in på Azure AD.  

Attributet synkroniseras med Azure AD Connect.  Under installationen kan du Visa de domäner som har verifierats och de som inte har det.

   ![Overifierade domäner](./media/plan-connect-userprincipalname/unverifieddomain.png) 

## <a name="alternate-login-id"></a>Alternativt inloggnings-ID
I vissa miljöer kan slutanvändare endast vara medveten om sin e-postadress och inte deras UPN.  Användningen av e-postadress kan bero på en företags princip eller ett lokalt affärs program beroende.

Alternativt inloggnings-ID kan du konfigurera en inloggnings upplevelse där användare kan logga in med ett annat attribut än deras UPN, t. ex. e-post.

Om du vill aktivera alternativt inloggnings-ID med Azure AD behövs inga ytterligare konfigurations steg när du använder Azure AD Connect. Alternativ-ID kan konfigureras i guiden. Se inloggnings konfigurationen i Azure AD för användarna under avsnittet Synkronisera. Under List rutan **användarens huvud namn** väljer du attributet för alternativt inloggnings-ID.

![Skärm bild som visar en lista över användarens huvud namn där du väljer attributet alternativt inloggnings-ID.](./media/plan-connect-userprincipalname/altloginid.png)  

Mer information finns i [Konfigurera alternativt inloggnings-ID](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) och [inloggnings konfiguration för Azure AD](how-to-connect-install-custom.md#azure-ad-sign-in-configuration)

## <a name="non-verified-upn-suffix"></a>UPN-suffix som inte verifierats
Om attributet lokalt UserPrincipalName/alternativt inloggnings-ID inte har verifierats med Azure AD-klienten, anges värdet för Azure AD UserPrincipalName-attributvärdet till MOERA. Azure AD beräknar MOERA från Azure AD-smek namn-attributet och den första Azure AD-domänen som ett &lt; smek namn för &gt;&#64;&lt; inledande domän &gt; .

## <a name="verified-upn-suffix"></a>Verifierat UPN-suffix
Om suffixet för attributet UserPrincipalName/alternativt inloggnings-ID verifieras med Azure AD-klienten, kommer värdet för Azure AD UserPrincipalName att vara detsamma som det lokala UserPrincipalName-attributet/alternativa inloggnings-ID-värdet.

## <a name="azure-ad-mailnickname-attribute-value-calculation"></a>Värde beräkning för Azure AD-smek-attribut
Eftersom värdet för Azure AD UserPrincipalName-attributvärdet kan ställas in på MOERA, är det viktigt att förstå hur värdet för attributet Azure AD-smek namn, som är MOERA-prefixet, beräknas.

När ett användar objekt synkroniseras till en Azure AD-klient för första gången, kontrollerar Azure AD följande objekt i den aktuella ordningen och ställer in värdet för fältet post-smek namn till det första befintliga:

- Lokalt smek namn-attribut
- Prefix för primär SMTP-adress
- Prefix för lokalt e-postattribut
- Prefix för lokalt userPrincipalName-attribut/alternativt inloggnings-ID
- Prefix för sekundär SMTP-adress

När uppdateringarna av ett användar objekt synkroniseras till Azure AD-klienten uppdaterar Azure AD bara värdet för attributet smek namn, om det finns en uppdatering av attributvärdet för det lokala värdeparet.

>[!IMPORTANT]
>Azure AD beräknar om UserPrincipalName-attributvärdet bara om en uppdatering av värdet lokalt UserPrincipalName-attribut/alternativt inloggnings-ID synkroniseras till Azure AD-klienten. 
>
>När Azure AD beräknar om UserPrincipalName-attributet beräknar det även om MOERA. 

## <a name="upn-scenarios"></a>UPN-scenarier
Följande är exempel scenarier för hur UPN beräknas baserat på det aktuella scenariot.

### <a name="scenario-1-non-verified-upn-suffix--initial-synchronization"></a>Scenario 1: icke-verifierat UPN-suffix – första synkronisering

![Scenario1](./media/plan-connect-userprincipalname/example1.png)

Lokalt användar objekt:
- Smek-smek namn: &lt; inte angivet&gt;
- proxyAddresses: { SMTP:us1@contoso.com }
- e us2@contoso.com
- userPrincipalName us3@contoso.com

Synkroniserade användarobjektet med Azure AD-klienten för första gången
- Ange attributet Azure AD-smek namn till primärt SMTP-adressprefix.
- Ange MOERA till  &lt; smek namn &gt;&#64;&lt; inledande domän &gt; .
- Ställ in Azure AD UserPrincipalName-attributet på MOERA.

Användar objekt för Azure AD-klient organisation:
- Smek namn: us1           
- UserPrincipalName us1@contoso.onmicrosoft.com


### <a name="scenario-2-non-verified-upn-suffix--set-on-premises-mailnickname-attribute"></a>Scenario 2: icke-verifierat UPN-suffix – ange lokalt smek namn-attribut

![Scenario2](./media/plan-connect-userprincipalname/example2.png)

Lokalt användar objekt:
- Smek namn: US4
- proxyAddresses: { SMTP:us1@contoso.com }
- e us2@contoso.com
- userPrincipalName us3@contoso.com

Synkronisera uppdatering på lokalt smek namn-attribut till Azure AD-klienten
- Uppdatera attributet Azure AD-smek namn med ett lokalt smek namn-attribut.
- Eftersom det inte finns någon uppdatering av det lokala userPrincipalName-attributet sker ingen ändring i Azure AD UserPrincipalName-attributet.

Användar objekt för Azure AD-klient organisation:
- Smek namn: US4
- UserPrincipalName us1@contoso.onmicrosoft.com

### <a name="scenario-3-non-verified-upn-suffix--update-on-premises-userprincipalname-attribute"></a>Scenario 3: icke-verifierat UPN-suffix – uppdatera lokalt userPrincipalName-attribut

![Scenario3](./media/plan-connect-userprincipalname/example3.png)

Lokalt användar objekt:
- Smek namn: US4
- proxyAddresses: { SMTP:us1@contoso.com }
- e us2@contoso.com
- userPrincipalName us5@contoso.com

Synkronisera uppdatering av lokalt userPrincipalName-attribut till Azure AD-klienten
- Uppdatering på lokala userPrincipalName-attribut utlöser omräkning av MOERA-och Azure AD UserPrincipalName-attribut.
- Ange MOERA till &lt; smek namn &gt;&#64;&lt; inledande domän &gt; .
- Ställ in Azure AD UserPrincipalName-attributet på MOERA.

Användar objekt för Azure AD-klient organisation:
- Smek namn: US4
- UserPrincipalName us4@contoso.onmicrosoft.com

### <a name="scenario-4-non-verified-upn-suffix--update-primary-smtp-address-and-on-premises-mail-attribute"></a>Scenario 4: icke-verifierat UPN-suffix – uppdatera primär SMTP-adress och lokalt e-postattribut

![Scenario4](./media/plan-connect-userprincipalname/example4.png)

Lokalt användar objekt:
- Smek namn: US4
- proxyAddresses: { SMTP:us6@contoso.com }
- e us7@contoso.com
- userPrincipalName us5@contoso.com

Synkronisera uppdatering på lokalt e-postattribut och primär SMTP-adress till Azure AD-klient
- Efter den inledande synkroniseringen av användarobjektet kommer uppdateringar till det lokala e-postattributet och den primära SMTP-adressen inte påverka Azure AD-smek namn eller UserPrincipalName-attributet.

Användar objekt för Azure AD-klient organisation:
- Smek namn: US4
- UserPrincipalName us4@contoso.onmicrosoft.com

### <a name="scenario-5-verified-upn-suffix--update-on-premises-userprincipalname-attribute-suffix"></a>Scenario 5: verifierat UPN-suffix – uppdatera lokalt userPrincipalName domänsuffix

![Scenario5](./media/plan-connect-userprincipalname/example5.png)

Lokalt användar objekt:
- Smek namn: US4
- proxyAddresses: { SMTP:us6@contoso.com }
- e us7@contoso.com
- userPrincipalName us5@verified.contoso.com

Synkronisera uppdatering av lokalt userPrincipalName-attribut till Azure AD-klienten
- Uppdatering på lokalt userPrincipalName-attribut utlöser omräkning av Azure AD UserPrincipalName-attribut.
- Ange Azure AD UserPrincipalName-attribut till lokalt userPrincipalName-attribut eftersom UPN-suffixet verifieras med Azure AD-klienten.

Användar objekt för Azure AD-klient organisation:
- Smek namn: US4     
- UserPrincipalName us5@verified.contoso.com

## <a name="next-steps"></a>Nästa steg
- [Integrerar dina lokala kataloger med Azure Active Directory](whatis-hybrid-identity.md)
- [Anpassad installation av Azure AD Connect](how-to-connect-install-custom.md)