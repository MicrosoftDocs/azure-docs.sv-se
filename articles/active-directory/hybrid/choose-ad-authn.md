---
title: Autentisering för Azure AD hybrid identitets lösningar
titleSuffix: Active Directory
description: 'Den här guiden hjälper CEOs, CIO: er, ciso, Chief Identity Architects, Enterprise Architects och IT-besluts fattare som ansvarar för att välja en autentiseringsmetod för sin Azure AD hybrid identitets lösning i medel stora och stora organisationer.'
keywords: ''
author: martincoetzer
ms.author: martinco
ms.date: 10/30/2019
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.workload: identity
ms.openlocfilehash: 15d62f40b50617fd1f6e543cb404a0d38361d3bd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94836503"
---
# <a name="choose-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Välj rätt autentiseringsmetod för din Azure Active Directory hybrid identitets lösning

Att välja rätt autentiseringsmetod är det första sättet för organisationer som vill flytta sina appar till molnet. Gör inte det här beslutet lätt, av följande skäl:

1. Det är det första beslutet för en organisation som vill flytta till molnet.

2. Autentiseringsmetoden är en viktig komponent i en organisations närvaro i molnet. Den styr åtkomsten till alla moln data och resurser.

3. Det är grunden för alla andra avancerade funktioner för säkerhet och användar upplevelse i Azure AD.

Identitet är det nya kontroll planet för IT-säkerhet, så autentisering är en organisations åtkomst skydd till den nya moln världen. Organisationer behöver ett identitets kontroll plan som förstärker säkerheten och skyddar sina molnappar mot inkräktare.

> [!NOTE]
> Att ändra autentiseringsmetoden kräver planering, testning och eventuella stillestånds tider. [Mellanlagrad](./how-to-connect-staged-rollout.md) distribution är ett bra sätt att testa användarnas migrering från Federation till molnbaserad autentisering.

### <a name="out-of-scope"></a>Utanför definitions området
Organisationer som inte har något befintligt lokalt katalog utrymme är inte fokus i den här artikeln. Vanligt vis skapar företagen bara identiteter i molnet, vilket inte kräver en hybrid identitets lösning. Enbart moln identiteter finns bara i molnet och är inte kopplade till motsvarande lokala identiteter.

## <a name="authentication-methods"></a>Autentiseringsmetoder
När Azure AD hybrid Identity-lösningen är ditt nya kontroll plan är autentiseringen grunden för moln åtkomst. Att välja rätt autentiseringsmetod är ett viktigt första beslut när du konfigurerar en Azure AD hybrid Identity-lösning. Implementera autentiseringsmetoden som konfigureras med hjälp av Azure AD Connect, som också etablerar användare i molnet.

Om du vill välja en autentiseringsmetod måste du ta hänsyn till tiden, den befintliga infrastrukturen, komplexiteten och kostnaden för att implementera ditt val. Dessa faktorer är olika för alla organisationer och kan ändras med tiden.

>[!VIDEO https://www.youtube.com/embed/YtW2cmVqSEw]

Azure AD stöder följande autentiseringsmetoder för Hybrid identitets lösningar.

### <a name="cloud-authentication"></a>Molnbaserad autentisering
När du väljer den här autentiseringsmetoden hanterar Azure AD användarnas inloggnings processer. Tillsammans med sömlös enkel inloggning (SSO) kan användarna logga in till molnappar utan att behöva ange sina autentiseringsuppgifter på annat sätt. Med molnbaserad autentisering kan du välja mellan två alternativ:

**Synkronisering av lösenordshash för Azure AD**. Det enklaste sättet att aktivera autentisering för lokala katalog objekt i Azure AD. Användare kan använda samma användar namn och lösen ord som de använder lokalt utan att behöva distribuera någon ytterligare infrastruktur. Vissa Premium funktioner i Azure AD, som identitets skydd och [Azure AD Domain Services](../../active-directory-domain-services/tutorial-create-instance.md), kräver synkronisering av lösen ords-hash, oavsett vilken autentiseringsmetod du väljer.

> [!NOTE]
> Lösen ord lagras aldrig i klartext eller krypteras med en omvänd algoritm i Azure AD. Mer information om den faktiska processen för synkronisering av lösen ords-hash finns i [implementera hash-synkronisering av lösen ord med Azure AD Connect Sync](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

**Autentisering med Azure AD-vidarekoppling**. Ger en enkel lösen ords validering för Azure AD Authentication Services med hjälp av en program varu agent som körs på en eller flera lokala servrar. Servrarna verifierar användarna direkt med din lokala Active Directory, vilket garanterar att lösen ords verifieringen inte sker i molnet.

Företag med ett säkerhets krav för att omedelbart framtvinga lokala användar konto tillstånd, lösen ords principer och inloggnings tider kan använda den här autentiseringsmetoden. Mer information om den faktiska processen för direktautentisering finns i [användar inloggning med Azure AD-direktautentisering](../../active-directory/hybrid/how-to-connect-pta.md).

### <a name="federated-authentication"></a>Federerad autentisering
När du väljer den här autentiseringsmetoden tar Azure AD hand om autentiseringsprocessen till ett separat betrott autentiseringsschema, till exempel lokala Active Directory Federation Services (AD FS) (AD FS) för att verifiera användarens lösen ord.

Autentiserings systemet kan ge ytterligare avancerade autentiseringskrav. Exempel är en SmartCard-baserad autentisering eller multifaktorautentisering från tredje part. Mer information finns i [distribuera Active Directory Federation Services (AD FS)](/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

I följande avsnitt får du hjälp att avgöra vilken autentiseringsmetod som passar dig bäst genom att använda ett besluts träd. Det hjälper dig att avgöra om du ska distribuera moln eller federerad autentisering för din Azure AD hybrid Identity-lösning.

## <a name="decision-tree"></a>Beslutsträd

![Besluts träd för Azure AD-autentisering](./media/choose-ad-authn/azure-ad-authn-image1.png)

Information om besluts frågor:

1. Azure AD kan hantera inloggning för användare utan att behöva lita på lokala komponenter för att verifiera lösen ord.
2. Azure AD kan lämna ut användar inloggningen till en betrodd autentiseringsprovider, till exempel Microsofts AD FS.
3. Om du behöver tillämpa Active Directory säkerhets principer på användar nivå, till exempel kontot som har upphört att gälla, inaktiverat konto, lösen ord har upphört att gälla, utelåst konto och inloggnings timmar för varje användar inloggning, kräver Azure AD några lokala komponenter.
4. Inloggnings funktioner som inte stöds internt av Azure AD:
   * Logga in med smartkort eller certifikat.
   * Logga in med lokal MFA-Server.
   * Logga in med hjälp av autentisering från tredje part.
   * Lösning för lokal autentisering med flera platser.
5. Azure AD Identity Protection kräver en hash-synkronisering av lösen ord oavsett vilken inloggnings metod du väljer för att ge *användarna med läcka autentiseringsuppgifter* . Organisationer kan redundansväxla till hash-synkronisering av lösen ord om deras primära inloggnings metod Miss lyckas och den har kon figurer ATS före fel händelsen.

> [!NOTE]
> Azure AD Identity Protection kräver [Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) -licenser.

## <a name="detailed-considerations"></a>Detaljerade överväganden

### <a name="cloud-authentication-password-hash-synchronization"></a>Molnbaserad autentisering: hash-synkronisering av lösen ord

* **Ansträngning**. Hash-synkronisering av lösen ord kräver minst ansträngning för distribution, underhåll och infrastruktur.  Den här nivån av arbete gäller vanligt vis organisationer som bara behöver sina användare för att logga in på Microsoft 365, SaaS-appar och andra Azure AD-baserade resurser. När det är aktiverat är Lösenordssynkronisering en del av den Azure AD Connect Sync-processen och körs var 2: e minut.

* **Användar upplevelse**. Du kan förbättra användarnas inloggnings upplevelser genom att distribuera sömlös SSO med hash-synkronisering av lösen ord. Sömlös SSO eliminerar onödiga meddelanden när användare är inloggade.

* **Avancerade scenarier**. Om organisationer väljer att göra det, är det möjligt att använda insikter från identiteter med Azure AD Identity Protection rapporter med Azure AD Premium P2. Ett exempel är rapporten läckta autentiseringsuppgifter. Windows Hello för företag har [specifika krav när du använder hash-synkronisering av lösen ord](/windows/access-protection/hello-for-business/hello-identity-verification). [Azure AD Domain Services](../../active-directory-domain-services/tutorial-create-instance.md) kräver synkronisering av lösen ords-hash för att etablera användare med sina företags uppgifter i den hanterade domänen.

    Organisationer som kräver Multi-Factor Authentication med lösen ords-hash-synkronisering måste använda anpassade Azure AD-Multi-Factor Authentication eller [anpassade kontroller för villkorlig åtkomst](../../active-directory/conditional-access/controls.md#custom-controls-preview). Dessa organisationer kan inte använda tredjeparts autentiseringsmetoder eller lokala metoder för multifaktorautentisering som förlitar sig på Federation.

> [!NOTE]
> Villkorlig åtkomst för Azure AD kräver [Azure AD Premium P1](https://azure.microsoft.com/pricing/details/active-directory/) -licenser.

* **Affärs kontinuitet**. Användningen av Password hash-synkronisering med molnbaserad autentisering är hög tillgänglig som en moln tjänst som kan skalas till alla Microsoft-datacenter. För att se till att Lösenordssynkronisering inte går nedåt under längre perioder kan du distribuera en andra Azure AD Connect-Server i mellanlagrings läge i en konfiguration för vänte läge.

* **Överväganden**. För närvarande framtvingar inte hash-synkronisering av lösen ord direkt ändringar i lokala konto tillstånd. I det här fallet har en användare åtkomst till molnappar tills användar kontots tillstånd är synkroniserat med Azure AD. Organisationer kanske vill lösa den här begränsningen genom att köra en ny synkroniseringsprocess efter att administratörer har Mass uppdateringar av lokala användar konto tillstånd. Ett exempel är inaktive ring av konton.

> [!NOTE]
> Lösen ordet har upphört att gälla och kontots låst tillstånd synkroniseras inte för närvarande till Azure AD med Azure AD Connect. När du ändrar en användares lösen ord och anger att *användaren måste byta lösen ord vid nästa inloggnings* flagga, synkroniseras inte lösen ordets hash till Azure AD med Azure AD Connect förrän användaren ändrar sitt lösen ord.

Se [implementera synkronisering av lösen ords-hash](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) för distributions steg.

### <a name="cloud-authentication-pass-through-authentication"></a>Molnbaserad autentisering: direktautentisering  

* **Ansträngning**. För direktautentisering måste du ha en eller flera (vi rekommenderar tre) Lightweight-agenter installerade på befintliga servrar. Dessa agenter måste ha åtkomst till din lokala Active Directory Domain Services, inklusive dina lokala AD-domänkontrollanter. De behöver utgående åtkomst till Internet och åtkomst till dina domänkontrollanter. Därför finns det inte stöd för att distribuera agenterna i ett perimeternätverk.

    Direktautentisering kräver obegränsad nätverks åtkomst till domänkontrollanter. All nätverks trafik är krypterad och begränsad till autentiseringsbegäranden. Mer information om den här processen finns i [säkerhets djupet](../../active-directory/hybrid/how-to-connect-pta-security-deep-dive.md) om direkt autentisering.

* **Användar upplevelse**. För att förbättra användarnas inloggnings upplevelser distribuerar du sömlös SSO med direktautentisering. Sömlös SSO eliminerar onödiga meddelanden när användarna loggar in.

* **Avancerade scenarier**. Direktautentisering använder den lokala konto principen vid tidpunkten för inloggningen. Till exempel nekas åtkomst när en lokal användares konto status är inaktive rad, utelåst eller om [lösen ordet upphör att gälla](../../active-directory/hybrid/how-to-connect-pta-faq.md#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication) eller om inloggnings försöket ligger utanför de timmar då användaren får logga in.

    Organisationer som kräver Multi-Factor Authentication med direktautentisering måste använda anpassade kontroller för Azure AD Multi-Factor Authentication (MFA) eller [villkorlig åtkomst](../../active-directory/conditional-access/controls.md#custom-controls-preview). Dessa organisationer kan inte använda en tredjeparts-autentiseringsmetod eller en lokal autentiseringsmetod som förlitar sig på Federation. Avancerade funktioner kräver att hash-synkronisering av lösen ord distribueras oavsett om du väljer direktautentisering eller inte. Ett exempel är en avslöjad identifierings rapport för identitets skydd.

* **Affärs kontinuitet**. Vi rekommenderar att du distribuerar två extra genom strömnings agenter. Dessa tillägg är utöver den första agenten på Azure AD Connect-servern. Den här ytterligare distributionen säkerställer hög tillgänglighet för autentiseringsbegäranden. När du har installerat tre agenter kan en agent fortfarande fungera när en annan agent är nere för underhåll.

    Det finns en annan fördel med att distribuera hash-synkronisering av lösen ord, förutom direktautentisering. Den fungerar som en autentiseringsmetod för säkerhets kopiering när den primära autentiseringsmetoden inte längre är tillgänglig.

* **Överväganden**. Du kan använda Password hash-synkronisering som en autentiseringsmetod för säkerhets kopiering för direktautentisering, när agenterna inte kan verifiera en användares autentiseringsuppgifter på grund av ett betydande lokalt haveri. Växling vid växling till hash-synkronisering sker inte automatiskt och du måste använda Azure AD Connect för att byta inloggnings metod manuellt.

    Andra överväganden vid direktautentisering, inklusive stöd för alternativa ID: n, finns i [vanliga frågor och svar](../../active-directory/hybrid/how-to-connect-pta-faq.md).

Se [implementering av direktautentisering](../../active-directory/hybrid/how-to-connect-pta.md) för distributions steg.

### <a name="federated-authentication"></a>Federerad autentisering

* **Ansträngning**. Ett federerat Authentication-system förlitar sig på ett externt betrott system för att autentisera användare. Vissa företag vill återanvända sina befintliga sammanslagna system investeringar med sin Azure AD hybrid identitets lösning. Underhåll och hantering av det federerade systemet faller utanför kontrollen över Azure AD. Det är upp till organisationen genom att använda det federerade systemet för att se till att det distribueras säkert och kan hantera inläsningen av autentiseringen.

* **Användar upplevelse**. Användarens upplevelse av federerad autentisering beror på implementeringen av funktioner, topologi och konfiguration av Federations gruppen. Vissa organisationer behöver den här flexibiliteten för att anpassa och konfigurera åtkomsten till Federations gruppen så att den passar deras säkerhets krav. Det är till exempel möjligt att konfigurera internt anslutna användare och enheter för att logga in användare automatiskt, utan att fråga dem om autentiseringsuppgifter. Den här konfigurationen fungerar eftersom de redan har loggat in på sina enheter. Om det behövs kan vissa avancerade säkerhetsfunktioner göra användarnas inloggnings process svårare.

* **Avancerade scenarier**. En lösning för federerad autentisering krävs när kunder har ett autentiseringskrav som Azure AD inte stöder internt. Se detaljerad information som hjälper dig att [välja rätt inloggnings alternativ](/archive/blogs/samueld/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365). Tänk på följande vanliga krav:

  * Autentisering som kräver smartkort eller certifikat.
  * Lokala MFA-servrar eller tredjepartsleverantörer som kräver en federerad identitets leverantör.
  * Autentisering med hjälp av lösningar från tredje part. Se [listan över kompatibel Azure AD-Federation](../../active-directory/hybrid/how-to-connect-fed-compatibility.md).
  * Logga in som kräver ett sAMAccountName, t. ex. domän \ användar namn, i stället för UPN (User Principal Name), till exempel user@domain.com .

* **Affärs kontinuitet**. Federerade system kräver vanligt vis en belastningsutjämnad mat ris med servrar, som kallas Server grupp. Den här server gruppen är konfigurerad i en intern topologi för nätverk och perimeter för att säkerställa hög tillgänglighet för autentiseringsbegäranden.

    Distribuera Password hash-synkronisering tillsammans med federerad autentisering som en autentiseringsmetod för säkerhets kopiering när den primära autentiseringsmetoden inte längre är tillgänglig. Ett exempel är när lokala servrar inte är tillgängliga. Vissa stora företags organisationer kräver en Federations lösning för att stödja flera ingångs punkter för Internet som kon figurer ATS med geo-DNS för autentiseringsbegäranden med låg latens.

* **Överväganden**. Federerade system kräver vanligt vis en mer betydande investering i den lokala infrastrukturen. De flesta organisationer väljer det här alternativet om de redan har en lokal Federations investering. Och om det är ett starkt verksamhets krav för att använda en leverantör med en identitets identitet. Federationen är mer komplex för att hantera och felsöka jämfört med Cloud Authentication-lösningar.

För en icke-dirigerbart domän som inte kan verifieras i Azure AD behöver du ytterligare konfiguration för att implementera användar-ID-inloggning. Detta krav kallas alternativt stöd för inloggnings-ID. Mer information finns i [Konfigurera alternativt inloggnings-ID](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) för begränsningar och krav. Om du väljer att använda en Multi-Factor Authentication-provider från tredje part med Federation, se till att providern stöder WS-Trust att tillåta att enheter ansluter till Azure AD.

Se [distribuera Federations Servrar](/windows-server/identity/ad-fs/deployment/deploying-federation-servers) för distributions steg.

> [!NOTE]
> När du distribuerar din Azure AD hybrid Identity-lösning måste du implementera en av de topologier som stöds av Azure AD Connect. Läs mer om konfigurationer som stöds och som inte stöds vid [topologier för Azure AD Connect](../../active-directory/hybrid/plan-connect-topologies.md).

## <a name="architecture-diagrams"></a>Arkitektur diagram

Följande diagram innehåller en översikt över de hög nivå arkitektur komponenter som krävs för varje autentiseringsmetod som du kan använda med din Azure AD hybrid Identity-lösning. De ger en översikt som hjälper dig att jämföra skillnaderna mellan lösningarna.

* Enkelhet till en lösning för hash-synkronisering av lösen ord:

    ![Azure AD hybrid identitet med hash-synkronisering av lösen ord](./media/choose-ad-authn/azure-ad-authn-image2.png)

* Agent krav för direktautentisering, med två agenter för redundans:

    ![Azure AD hybrid identitet med direktautentisering](./media/choose-ad-authn/azure-ad-authn-image3.png)

* Komponenter som krävs för federation i din perimeter och det interna nätverket i din organisation:

    ![Azure AD hybrid identitet med federerad autentisering](./media/choose-ad-authn/azure-ad-authn-image4.png)

## <a name="comparing-methods"></a>Jämföra metoder

|Att tänka på|Password-hash-synkronisering + sömlös SSO|Direktautentisering + sömlös SSO|Federation med AD FS|
|:-----|:-----|:-----|:-----|
|Var sker autentiseringen?|I molnet|I molnet efter ett säkert utbyte av lösen ords verifiering med den lokala Autentiseringstjänsten|Lokalt|
|Vilka är de lokala server kraven utöver etablerings systemet: Azure AD Connect?|Inget|En server för varje ytterligare Authentication agent|Två eller flera AD FS-servrar<br><br>Två eller flera WAP-servrar i perimeternätverket/DMZ-nätverket|
|Vilka är kraven för lokal Internet och nätverk utöver etablerings systemet?|Inget|[Utgående Internet åtkomst](../../active-directory/hybrid/how-to-connect-pta-quick-start.md) från servrar som kör autentiseringsprinciper|[Inkommande Internet åtkomst](/windows-server/identity/ad-fs/overview/ad-fs-requirements) till WAP-servrar i perimeternätverket<br><br>Inkommande nätverks åtkomst till AD FS servrar från WAP-servrar i perimeternätverket<br><br>Utjämning av nätverksbelastning|
|Finns det ett TLS/SSL-certifikat krav?|Inga|Inga|Ja|
|Finns det en lösning för hälso övervakning?|Krävs inte|Agent status som tillhandahålls av [Azure Active Directory administrations Center](../../active-directory/hybrid/tshoot-connect-pass-through-authentication.md)|[Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md)|
|Får användarna enkel inloggning till moln resurser från domänanslutna enheter i företags nätverket?|Ja med [sömlös SSO](../../active-directory/hybrid/how-to-connect-sso.md)|Ja med [sömlös SSO](../../active-directory/hybrid/how-to-connect-sso.md)|Ja|
|Vilka inloggnings typer stöds?|UserPrincipalName + lösen ord<br><br>Windows-Integrated autentisering med [sömlös SSO](../../active-directory/hybrid/how-to-connect-sso.md)<br><br>[Alternativt inloggnings-ID](../../active-directory/hybrid/how-to-connect-install-custom.md)|UserPrincipalName + lösen ord<br><br>Windows-Integrated autentisering med [sömlös SSO](../../active-directory/hybrid/how-to-connect-sso.md)<br><br>[Alternativt inloggnings-ID](../../active-directory/hybrid/how-to-connect-pta-faq.md)|UserPrincipalName + lösen ord<br><br>sAMAccountName + lösen ord<br><br>Windows-Integrated autentisering<br><br>[Autentisering med certifikat och smartkort](/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[Alternativt inloggnings-ID](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|Stöds Windows Hello för företag?|[Nyckel förtroende modell](/windows/security/identity-protection/hello-for-business/hello-identity-verification)|[Nyckel förtroende modell](/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>*Kräver domän funktions nivå för Windows Server 2016*|[Nyckel förtroende modell](/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Certifikat förtroende modell](/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Vad är alternativen för multifaktorautentisering?|[Azure AD MFA](/azure/multi-factor-authentication/)<br><br>[Anpassade kontroller med villkorlig åtkomst *](../../active-directory/conditional-access/controls.md)|[Azure AD MFA](/azure/multi-factor-authentication/)<br><br>[Anpassade kontroller med villkorlig åtkomst *](../../active-directory/conditional-access/controls.md)|[Azure AD MFA](/azure/multi-factor-authentication/)<br><br>[Azure MFA-Server](../../active-directory/authentication/howto-mfaserver-deploy.md)<br><br>[MFA från tredje part](/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)<br><br>[Anpassade kontroller med villkorlig åtkomst *](../../active-directory/conditional-access/controls.md)|
|Vilka användar konto tillstånd stöds?|Inaktiverade konton<br>(upp till 30 minuters fördröjning)|Inaktiverade konton<br><br>Kontot är utelåst<br><br>Kontot har gått ut<br><br>Lösen ordet upphörde<br><br>Inloggnings tider|Inaktiverade konton<br><br>Kontot är utelåst<br><br>Kontot har gått ut<br><br>Lösen ordet upphörde<br><br>Inloggnings tider|
|Vilka är alternativen för villkorlig åtkomst?|[Villkorlig åtkomst för Azure AD med Azure AD Premium](../../active-directory/conditional-access/overview.md)|[Villkorlig åtkomst för Azure AD med Azure AD Premium](../../active-directory/conditional-access/overview.md)|[Villkorlig åtkomst för Azure AD med Azure AD Premium](../../active-directory/conditional-access/overview.md)<br><br>[AD FS anspråks regler](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|Finns det stöd för äldre protokoll?|[Ja](../../active-directory/conditional-access/overview.md)|[Ja](../../active-directory/conditional-access/overview.md)|[Ja](/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|Kan du anpassa logo typen, avbildningen och beskrivningen på inloggnings sidorna?|[Ja, med Azure AD Premium](../../active-directory/fundamentals/customize-branding.md)|[Ja, med Azure AD Premium](../../active-directory/fundamentals/customize-branding.md)|[Ja](../../active-directory/hybrid/how-to-connect-fed-management.md)|
|Vilka avancerade scenarier stöds?|[Smart lösen ords utelåsning](../../active-directory/authentication/howto-password-smart-lockout.md)<br><br>[Läckta autentiseringsuppgifter-rapporter, med Azure AD Premium P2](../identity-protection/overview-identity-protection.md)|[Smart lösen ords utelåsning](../../active-directory/authentication/howto-password-smart-lockout.md)|Autentiserings system med låg latens för Multisite<br><br>[AD FS extra näts utelåsning](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)<br><br>[Integrering med identitets system från tredje part](../../active-directory/hybrid/how-to-connect-fed-compatibility.md)|

> [!NOTE]
> Anpassade kontroller i villkorlig åtkomst för Azure AD stöder inte enhets registrering.

## <a name="recommendations"></a>Rekommendationer
Ditt identitets system ser till att användarnas åtkomst till molnappar och de verksamhetsspecifika appar som du migrerar och gör tillgängliga i molnet. För att få behöriga användare att vara produktiva och dåliga aktörer från din organisations känsliga data kontrollerar autentiseringen åtkomst till appar.

Använd eller aktivera hash-synkronisering av lösen ord för vilken autentiseringsmetod du väljer, av följande skäl:

1. **Hög tillgänglighet och katastrof återställning**. Direktautentisering och Federation förlitar sig på lokal infrastruktur. Vid direktautentisering kräver den lokala lagrings platsen server maskin vara och nätverk som krävs för direktautentisering. För Federation är det lokala utrymmet ännu större. Det kräver att servrar i perimeternätverket för att kunna proxy-autentiseringsbegäranden och interna Federations servrar.

    Du kan undvika enskilda felpunkter genom att distribuera redundanta servrar. Sedan kommer autentiseringsbegäranden alltid att servas om någon komponent Miss lyckas. Både direkt autentisering och Federation förlitar sig även på domänkontrollanter för att svara på autentiseringsbegäranden, vilket också kan fungera. Många av dessa komponenter behöver underhåll för att vara felfria. Avbrott är mer sannolik när underhåll inte planeras och implementeras korrekt. Undvik avbrott med hjälp av hash-synkronisering av lösen ord eftersom Microsoft Azure AD Cloud Authentication Service skalar globalt och alltid är tillgängligt.

2. **Överlevnad för lokalt avbrott**.  Konsekvenserna av ett lokalt avbrott på grund av en cyberhot-attack eller katastrof kan vara betydande, vilket sträcker sig från problem med problem med en Paralyzed organisation som inte kan hantera angreppet. Nyligen var många organisationer offer för angrepp mot skadlig kod, inklusive avsedd utpressnings tro Jan, som gjorde att deras lokala servrar var igång. När Microsoft hjälper kunderna att hantera dessa typer av attacker, ser de två kategorier av organisationer:

   * Organisationer som tidigare har aktiverat hash-synkronisering av lösen ord ovanpå federerade autentisering eller direktautentisering ändrade sin primära autentiseringsmetod till sedan använder synkronisering av lösen ords-hash. De var online igen om några timmar. Genom att använda åtkomst till e-post via Microsoft 365 fungerade de för att lösa problem och få åtkomst till andra molnbaserade arbets belastningar.

   * Organisationer som inte tidigare har aktiverat hash-synkronisering för lösen ord hade till exempel inte betrodda externa konsument-e-postsystem för kommunikation att lösa problem. I dessa fall tog det flera veckor att återställa sin lokala identitets infrastruktur innan användare kunde logga in på molnbaserade appar igen.

3. **Identitets skydd**. Ett av de bästa sätten att skydda användare i molnet är Azure AD Identity Protection med Azure AD Premium P2. Microsoft genomsöker kontinuerligt Internet efter användar-och lösen ords listor som dåliga aktörer säljer och gör tillgängliga på den mörka webben. Azure AD kan använda den här informationen för att kontrol lera om något av användar namn och lösen ord i din organisation har komprometterats. Därför är det viktigt att aktivera synkronisering av lösen ords-hash oavsett vilken autentiseringsmetod du använder, oavsett om den är federerad eller direktautentisering. Läckta autentiseringsuppgifter visas som en rapport. Använd den här informationen för att blockera eller tvinga användare att ändra sina lösen ord när de försöker logga in med läckta lösen ord.

## <a name="conclusion"></a>Slutsats

Den här artikeln beskriver olika autentiseringsalternativ som organisationer kan konfigurera och distribuera för att ge stöd åt molnappar. För att möta olika affärs-, säkerhets-och teknik krav kan organisationer välja mellan Lösenordssynkronisering, direktautentisering och Federation.

Överväg varje autentiseringsmetod. Är arbetet med att distribuera lösningen och användarens upplevelse av inloggnings processen att hantera dina affärs behov? Utvärdera om din organisation behöver de avancerade scenarierna och affärs kontinuitets funktionerna i varje autentiseringsmetod. Utvärdera slutligen överväganden för varje autentiseringsmetod. Hindrar du någon av dem från att implementera ditt val?

## <a name="next-steps"></a>Nästa steg

I dagens värld är Hot tillgängliga dygnet runt, och kommer från var som än är. Implementera rätt autentiseringsmetod så att du kan åtgärda dina säkerhets risker och skydda dina identiteter.

[Kom igång](../fundamentals/active-directory-whatis.md) med Azure AD och distribuera rätt autentiserings lösning för din organisation.

Om du funderar på att migrera från federerade till molnbaserad autentisering, lär du dig mer om [att ändra inloggnings metoden](../../active-directory/hybrid/plan-connect-user-signin.md). För att hjälpa dig att planera och implementera migreringen använder du [de här distributions planerna för projekt](../fundamentals/active-directory-deployment-plans.md) eller funderar på att använda den nya [stegvisa](../../active-directory/hybrid/how-to-connect-staged-rollout.md) distributions funktionen för att migrera federerade användare till att använda molnbaserad autentisering i en stegvis metod.