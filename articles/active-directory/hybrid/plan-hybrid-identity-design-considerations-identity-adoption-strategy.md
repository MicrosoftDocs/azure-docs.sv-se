---
title: Implementerings strategi för Hybrid identitet, Azure | Microsoft Docs
description: Med villkorlig åtkomst kontroll kontrollerar Azure AD de specifika villkor som du väljer när du autentiserar användaren och innan du tillåter åtkomst till programmet.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: b92fa5a9-c04c-4692-b495-ff64d023792c
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f52e46ff9cab7d3d150af9fd7b4f1c432bec74b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836197"
---
# <a name="define-a-hybrid-identity-adoption-strategy"></a>Definiera en strategi för införande av hybrid identitet
I den här uppgiften definierar du strategin för att införa Hybrid identiteter för din hybrid identitets lösning som uppfyller de affärs krav som diskuterades i:

* [Fastställa affärs behov](plan-hybrid-identity-design-considerations-business-needs.md)
* [Fastställa krav för katalog synkronisering](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)
* [Fastställa krav för Multi-Factor Authentication](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>Definiera strategi för affärs behov
Den första aktivitets adressen fastställer organisationernas affärs behov.  Detta kan vara mycket brett och omfångs kryp kan uppstå om du inte är försiktig.  I början är det enkelt att komma ihåg att planera för en design som kan hantera och under lätta förändringar i framtiden.  Oavsett om det är en enkel design eller en extremt komplex, Azure Active Directory är Microsoft Identity Platform som stöder Microsoft 365, Microsoft Online Services och molnbaserade program.

## <a name="define-an-integration-strategy"></a>Definiera en integrerings strategi
Microsoft har tre huvud integrerings scenarier som är moln identiteter, synkroniserade identiteter och federerade identiteter.  Du bör planera att införa en av integrerings strategierna.  Den strategi du väljer kan variera och besluten om att välja en kan innehålla, vilken typ av användar upplevelse som du vill tillhandahålla, har en befintlig infrastruktur och det som är mest kostnads effektivt.  

![integrations scenarier](./media/plan-hybrid-identity-design-considerations/integration-scenarios.png)

Scenarierna som definieras i ovanstående figur är:

* **Moln identiteter**: dessa är identiteter som bara finns i molnet.  När det gäller Azure AD skulle de finnas närmare i Azure AD-katalogen.
* **Synkroniserad**: dessa är identiteter som finns lokalt och i molnet.  Med hjälp av Azure AD Connect skapas eller kopplas dessa användare antingen till befintliga Azure AD-konton.  Användarens lösen ords-hash synkroniseras från den lokala miljön till molnet i vad som kallas för en lösen ords-hash.  När du använder Synchronized är det ett villkor att om en användare är inaktive rad i den lokala miljön, kan det ta upp till tre timmar innan konto statusen visas i Azure AD.  Detta beror på tids perioden för synkronisering.
* **Federerad**: dessa identiteter finns både lokalt och i molnet.  Med hjälp av Azure AD Connect skapas eller kopplas dessa användare antingen till befintliga Azure AD-konton.  

> [!NOTE]
> Mer information om synkroniseringsalternativ finns i [integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
> 
> 

Följande tabell hjälper till att fastställa fördelarna och nack delarna med var och en av följande strategier:

| Strategi | Fördelar | Nackdelar |
| --- | --- | --- |
| **Moln identiteter** |Enklare att hantera för små organisationer. <br> Det finns inget att installera lokalt. Ingen ytterligare maskin vara behövs<br>Du kan enkelt inaktivera om användaren lämnar företaget |Användarna måste logga in vid åtkomst till arbets belastningar i molnet <br> Lösen ord kan vara likadana för moln-och lokala identiteter |
| **Synkronisera** |Lokalt lösen ord autentiserar både lokala och molnbaserade kataloger <br>Enklare att hantera för små, medel stora eller stora organisationer <br>Användare kan ha enkel inloggning (SSO) för vissa resurser <br> Microsofts bästa metod för synkronisering <br> Enklare att hantera |Vissa kunder kan vara ovilliga att synkronisera sina kataloger med molnet på grund av ett visst företags principer |
| **Federerade** |Användare kan ha enkel inloggning (SSO) <br>Om en användare avslutas eller lämnas kvar, kan kontot inaktive ras omedelbart och åtkomsten återkallas<br> Har stöd för avancerade scenarier som inte kan utföras med synkroniserade |Fler steg för att konfigurera och konfigurera <br> Högre underhåll <br> Kan kräva ytterligare maskin vara för STS-infrastrukturen <br> Kan kräva ytterligare maskin vara för att installera Federations servern. Ytterligare program vara krävs om AD FS används <br> Kräv omfattande konfiguration för SSO <br> Kritisk felpunkt om Federations servern inte är igång kan användarna inte autentisera |

### <a name="client-experience"></a>Klientupplevelse
Den strategi du använder kommer att diktera användar inloggnings upplevelsen.  I följande tabeller får du information om vad användarna bör förvänta sig att deras inloggnings upplevelse ska vara.  Alla federerade identitets leverantörer stöder inte SSO i alla scenarier.

**Doman-anslutna och privata nätverks program**:

| Program | Synkroniserad identitet | Federerade identiteter |
| --- | --- | --- |
| Webbläsare |Formulärbaserad autentisering |enkel inloggning, ibland krävs för att ange organisations-ID |
| Outlook |Fråga efter autentiseringsuppgifter |Fråga efter autentiseringsuppgifter |
| Skype för företag (Lync) |Fråga efter autentiseringsuppgifter |enkel inloggning för Lync, uppmaning om autentiseringsuppgifter för Exchange |
| OneDrive för företag |Fråga efter autentiseringsuppgifter |enkel inloggning |
| Office Pro Plus-prenumeration |Fråga efter autentiseringsuppgifter |enkel inloggning |

**Externa eller ej betrodda källor**:

| Program | Synkroniserad identitet | Federerade identiteter |
| --- | --- | --- |
| Webbläsare |Formulärbaserad autentisering |Formulärbaserad autentisering |
| Outlook, Skype för företag (Lync), OneDrive för företag, Office-prenumeration |Fråga efter autentiseringsuppgifter |Fråga efter autentiseringsuppgifter |
| Exchange ActiveSync |Fråga efter autentiseringsuppgifter |enkel inloggning för Lync, uppmaning om autentiseringsuppgifter för Exchange |
| Mobilappar |Fråga efter autentiseringsuppgifter |Fråga efter autentiseringsuppgifter |

Om du har bestämt från uppgift 1 som du har en IdP från tredje part eller om du ska använda en för att tillhandahålla Federation med Azure AD måste du vara medveten om följande funktioner som stöds:

* En SAML 2,0-provider som är kompatibel med SP-Lite profilen kan stödja autentisering till Azure AD och associerade program
* Stöder passiv autentisering, vilket underlättar autentisering till OWA, SPO osv.
* Exchange Online-klienter kan stödjas via en utökad klient profil i SAML 2,0 (ECP)

Du måste också vara medveten om vilka funktioner som inte är tillgängliga:

* Utan WS-Trust/Federation-support bryts alla andra aktiva klienter
  * Det innebär att ingen Lync-klient, OneDrive-klient, Office-prenumeration, Office Mobile före Office 2016
* Över gång av Office till passiv autentisering gör det möjligt för dem att stödja ren SAML 2,0-IDP: er, men stöd kommer fortfarande att finnas på klient-för-klient-basis

> [!NOTE]
> För den senast uppdaterade listan läser du artikeln [Azure AD Federation Compatibility List](how-to-connect-fed-compatibility.md).
> 
> 

## <a name="define-synchronization-strategy"></a>Definiera strategi för synkronisering
I den här uppgiften definierar du de verktyg som ska användas för att synkronisera organisationens lokala data till molnet och vilken topologi du ska använda.  Eftersom de flesta organisationer använder Active Directory, finns det mer information om hur du använder Azure AD Connect för att hantera frågorna ovan.  För miljöer som inte har Active Directory finns det information om hur du kan planera denna strategi med hjälp av FIM 2010 R2 eller MIM 2016.  Framtida versioner av Azure AD Connect kommer dock att ha stöd för LDAP-kataloger, så beroende på din tids linje kan den här informationen hjälpa dig.

### <a name="synchronization-tools"></a>Verktyg för synkronisering
Under åren har flera verktyg för synkronisering funnits och använts för olika scenarier.  För närvarande Azure AD Connect är det verktyg du väljer för alla scenarier som stöds.  AAD Sync och DirSync är också fortfarande kvar och kan även finnas i din miljö nu. 

> [!NOTE]
> Den senaste informationen om vilka funktioner som stöds av varje verktyg finns i [jämförelse artikel för katalog integrerings verktyg](plan-hybrid-identity-design-considerations-tools-comparison.md) .  
> 
> 

### <a name="supported-topologies"></a>Topologier som stöds
När du definierar en strategi för synkronisering måste topologin som används fastställas. Beroende på den information som identifierades i steg 2 kan du bestämma vilken topologi som är den som ska användas. Den enda skogen är en enda Azure AD-topologi som är vanligast och består av en enda Active Directory skog och en enda instans av Azure AD.  Detta kommer att användas i en majoritet av scenarierna och är den förväntade topologin när du använder Azure AD Connect Express installation som visas i bilden nedan.

![](./media/plan-hybrid-identity-design-considerations/single-forest.png)Scenarier för topologier med en enda skog som stöds är det vanligt för stora och till och med små organisationer att ha flera skogar, som du ser i bild 5.

> [!NOTE]
> Mer information om de olika lokala och Azure AD-topologierna med Azure AD Connect Sync finns i artikel [topologier för Azure AD Connect](plan-connect-topologies.md).
> 
> 

![topologi för flera skogar](./media/plan-hybrid-identity-design-considerations/multi-forest.png) 

Scenario med flera skogar

I så fall bör du överväga att använda en enda Azure AD-topologi för flera skogar om följande objekt är sanna:

* Användare har endast 1 identitet i alla skogar – i avsnittet identifiera användare nedan beskrivs detta i mer detalj.
* Användaren autentiseras mot den skog där deras identitet finns
* UPN och käll ankare (oföränderliga ID) kommer från den här skogen
* Alla skogar är tillgängliga via Azure AD Connect – det innebär att den inte behöver vara domänansluten och kan placeras i en DMZ om detta underlättar detta.
* Användare har bara en post låda
* Den skog som är värd för en användares post låda har den bästa data kvaliteten för attribut som visas i den globala adress listan i Exchange (GAL)
* Om det inte finns någon post låda för användaren kan en skog användas för att bidra med dessa värden
* Om du har en länkad post låda finns det också ett annat konto i en annan skog som används för att logga in.

> [!NOTE]
> Objekt som finns i både lokalt och i molnet är "anslutna" via en unik identifierare. I kontexten för katalog synkronisering kallas den här unika identifieraren SourceAnchor. I samband med enkel inloggning kallas detta för ImmutableId. [Utforma begrepp för Azure AD Connect](plan-connect-design-concepts.md#sourceanchor) för mer information om användningen av SourceAnchor.
> 
> 

Om ovanstående inte är sant och du har fler än ett aktivt konto eller fler än en post låda, kommer Azure AD Connect att välja ett och ignorera det andra.  Om du har länkade post lådor men inga andra konton, exporteras inte dessa konton till Azure AD och den användaren är inte medlem i någon grupp.  Detta skiljer sig från hur det tidigare hade DirSync och är avsiktligt för att bättre stödja dessa scenarier med flera skogar. Ett scenario med flera skogar visas i bilden nedan.

![flera Azure AD-klienter](./media/plan-hybrid-identity-design-considerations/multiforest-multipleAzureAD.png) 

**Flera skogar med flera olika Azure AD-scenarier**

Vi rekommenderar att du bara har en enda katalog i Azure AD för en organisation, men den stöds en 1:1-relation hålls mellan en Azure AD Connect Sync-Server och en Azure AD-katalog.  För varje instans av Azure AD behöver du en installation av Azure AD Connect.  Dessutom är Azure AD efter design isolerad och användare i en instans av Azure AD kommer inte att kunna se användare i en annan instans.

Det är möjligt och stöds för att ansluta en lokal instans av Active Directory till flera Azure AD-kataloger som visas i bilden nedan:

![filtrering av enstaka skogar](./media/plan-hybrid-identity-design-considerations/single-forest-flitering.png) 

**Filtrerings scenario med en skog**

För att göra detta måste följande vara sant:

* Azure AD Connect Sync-servrar måste konfigureras för filtrering så att de båda har en ömsesidigt exklusiv uppsättning objekt.  Detta sker, till exempel genom att varje server omfångs till en viss domän eller ORGANISATIONSENHET.
* En DNS-domän kan bara registreras i en enda Azure AD-katalog, så att UPN-användare i den lokala AD måste använda separata namn områden
* Användare i en instans av Azure AD kommer bara att kunna se användare från sin instans.  De kommer inte att kunna se användare i de andra instanserna
* Endast en av Azure AD-katalogerna kan aktivera Exchange hybrid med lokal AD
* Ömsesidiga exklusivitet gäller även för Skriv återställning.  Detta gör att vissa skriv-och bakgrunds funktioner inte stöds med den här topologin eftersom dessa förutsätter en enda lokal konfiguration.  Du måste bland annat:
  * Skriv åtgärd för grupp med standard konfiguration
  * Tillbakaskrivning av enhet

Följande stöds inte och bör inte väljas som en implementering:

* Det går inte att ha flera Azure AD Connect Sync-servrar som ansluter till samma Azure AD-katalog även om de har kon figurer ATS för att synkronisera ömsesidigt uteslutande objekt uppsättning
* Det går inte att synkronisera samma användare med flera Azure AD-kataloger. 
* Det går inte heller att göra en konfigurations ändring för att användare i en Azure AD ska visas som kontakter i en annan Azure AD-katalog. 
* Det går inte heller att ändra Azure AD Connect Sync för att ansluta till flera Azure AD-kataloger.
* Azure AD-kataloger är design isolerade. Det finns inte stöd för att ändra konfigurationen för Azure AD Connect Sync för att läsa data från en annan Azure AD-katalog i ett försök att bygga en gemensam och enhetlig GAL mellan katalogerna. Det går inte heller att exportera användare som kontakter till en annan lokal AD med hjälp av Azure AD Connect Sync.

> [!NOTE]
> Om din organisation begränsar datorerna i nätverket från att ansluta till Internet, visar den här artikeln de slut punkter (FQDN, IPv4 och IPv6-adress intervall) som du bör ta med i listan över utgående listor och zonen Tillförlitliga platser i Internet Explorer på klient datorer så att datorerna kan använda Microsoft 365. Mer information finns i [Office 365-URL: er och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US).
> 
> 

## <a name="define-multi-factor-authentication-strategy"></a>Definiera Multi-Factor Authentication-strategi
I den här uppgiften ska du definiera strategin för Multi-Factor Authentication som ska användas.  Azure AD Multi-Factor Authentication levereras i två olika versioner.  En är en molnbaserad och den andra finns lokalt baserat på Azure MFA-servern.  Utifrån utvärderingen som du gjorde ovan kan du avgöra vilken lösning som är rätt för din strategi.  Använd tabellen nedan för att avgöra vilket design alternativ som bäst uppfyller företagets säkerhets krav:

Design alternativ för flera faktorer:

| Till gång för att skydda | MFA i molnet | MFA lokalt |
| --- | --- | --- |
| Microsoft-appar |ja |ja |
| Saas-appar i appgalleriet |ja |ja |
| IIS-program publicerade via Azure AD App Proxy |ja |ja |
| IIS-program som inte publicerats via Azure AD App proxy |nej |ja |
| Fjärråtkomst som VPN, RDG |nej |ja |

Även om du kan ha löst i en lösning för din strategi, behöver du fortfarande använda utvärderingen ovan på var användarna finns.  Detta kan orsaka att lösningen ändras.  Använd tabellen nedan för att hjälpa dig att fastställa följande:

| Användarplats | Önskat design alternativ |
| --- | --- |
| Azure Active Directory |Multi-FactorAuthentication i molnet |
| Azure AD och lokalt AD med federation med AD FS |Båda |
| Azure AD och lokalt AD med Azure AD Connect ingen Lösenordssynkronisering |Båda |
| Azure AD och lokalt med hjälp av Azure AD Connect med Lösenordssynkronisering |Båda |
| Lokal AD |Multi-Factor Authentication Server |

> [!NOTE]
> Du bör också se till att design alternativet Multi-Factor Authentication som du har valt stöder de funktioner som krävs för din design.  Mer information finns [i välja Multi-Factor Security-lösningen för dig](../authentication/concept-mfa-howitworks.md).
> 

## <a name="multi-factor-auth-provider"></a>Multi-Factor Auth-provider
Multi-Factor Authentication är tillgängligt som standard för globala administratörer som har en Azure Active Directory-klient. Men om du vill utöka Multi-Factor Authentication till alla dina användare och/eller om du vill att dina globala administratörer ska kunna dra nytta av funktioner, till exempel hanterings portalen, anpassade hälsningar och rapporter, måste du köpa och konfigurera Multi-Factor Authentication leverantören.

> [!NOTE]
> Du bör också se till att design alternativet Multi-Factor Authentication som du har valt stöder de funktioner som krävs för din design. 
> 
> 

## <a name="next-steps"></a>Nästa steg
[Fastställa krav för data skydd](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Se även
[Översikt över design överväganden](plan-hybrid-identity-design-considerations-overview.md)

