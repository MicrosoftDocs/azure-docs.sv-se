---
title: Resurs skogens koncept för Azure AD Domain Services | Microsoft Docs
description: Lär dig mer om hur en resurs skog är i Azure Active Directory Domain Services och hur de gynnar din organisation i hybrid miljö med begränsade alternativ för användarautentisering eller säkerhets problem.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 79cf408bcb9060c247b97e6a81204c5a5517d384
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96620009"
---
# <a name="resource-forest-concepts-and-features-for-azure-active-directory-domain-services"></a>Principer och funktioner för resurs skogar för Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) är en inloggnings upplevelse för äldre, lokala, branschspecifika program. Användare, grupper och lösenords-hashar för lokala och moln användare synkroniseras med den hanterade Azure AD DS-domänen. Dessa synkroniserade lösen ords-hashar är vad som ger användare en enda uppsättning autentiseringsuppgifter som de kan använda för den lokala AD DS, Microsoft 365 och Azure Active Directory.

Även om det är säkert och ger ytterligare säkerhets förmåner, kan vissa organisationer inte synkronisera dessa hashar för användar lösen ord till Azure AD eller Azure AD DS. Användare i en organisation kanske inte känner till sitt lösen ord eftersom de bara använder autentisering med smartkort. De här begränsningarna förhindrar att vissa organisationer använder Azure AD DS för att lyfta och byta lokala klassiska program till Azure.

För att lösa dessa behov och begränsningar kan du skapa en hanterad domän som använder en resurs skog. I den här konceptuella artikeln förklaras vad skogar är och hur de litar på andra resurser för att tillhandahålla en säker autentiseringsmetod.

## <a name="what-are-forests"></a>Vad är skogar?

En *skog* är en logisk konstruktion som används av Active Directory Domain Services (AD DS) för att gruppera en eller flera *domäner*. Domänerna lagrar sedan objekt för användare eller grupper och tillhandahåller Authentication Services.

I en Azure AD DS-hanterad domän innehåller skogen bara en domän. Lokala AD DS-skogar innehåller ofta många domäner. I stora organisationer, särskilt efter sammanslagning och förvärv, kan du få flera lokala skogar som var och en innehåller flera domäner.

Som standard skapas en hanterad domän som en *användar* skog. Den här typen av skog synkroniserar alla objekt från Azure AD, inklusive alla användar konton som skapats i en lokal AD DS-miljö. Användar konton kan autentiseras direkt mot den hanterade domänen, t. ex. för att logga in på en domänansluten virtuell dator. En användar skog fungerar när lösen ordets hash-värden kan synkroniseras och användarna inte använder exklusiva inloggnings metoder som smartkort-autentisering.

I en hanterad domän *resurs* skog autentiseras användare över ett enkelriktat skogs *förtroende* från sina lokala AD DS. Med den här metoden synkroniseras inte användar objekt och lösen ordets hash-värden till den hanterade domänen. Användar objekt och autentiseringsuppgifter finns bara i den lokala AD DS. Med den här metoden kan företag hantera resurser och programplattformar i Azure som är beroende av klassisk autentisering, t. ex. LDAP, Kerberos eller NTLM, men eventuella autentiseringsproblem eller problem tas bort.

Resurs skogar ger också möjlighet att lyfta och flytta dina program en komponent i taget. Många äldre lokala program är flera nivåer, ofta med hjälp av en webb server eller klient del och många databasbaserade komponenter. Dessa nivåer gör det svårt att lyfta upp och ned hela programmet till molnet i ett enda steg. Med resurs skogar kan du lyfta ditt program till molnet i stegvisa metoder, vilket gör det enklare att flytta ditt program till Azure.

## <a name="what-are-trusts"></a>Vad är förtroenden?

Organisationer som har mer än en domän behöver ofta användare för att komma åt delade resurser i en annan domän. Åtkomst till dessa delade resurser kräver att användare i en domän autentiseras till en annan domän. För att tillhandahålla dessa funktioner för autentisering och auktorisering mellan klienter och servrar i olika domäner måste det finnas ett *förtroende* mellan de två domänerna.

Med domän förtroenden har autentiseringen för varje domän förtroende för de autentiseringar som kommer från den andra domänen. Förtroenden hjälper till att ge kontrollerad åtkomst till delade resurser i en resurs domän (domänen med *förtroende* ) genom att verifiera att inkommande autentiseringsbegäranden kommer från en betrodd utfärdare (den *betrodda* domänen). Förtroenden fungerar som bryggor som bara tillåter verifierade autentiseringsbegäranden att resa mellan domäner.

Hur ett förtroende passerar autentiseringsbegäranden beror på hur det är konfigurerat. Förtroenden kan konfigureras på något av följande sätt:

* **Enkelriktad** – ger åtkomst från den betrodda domänen till resurser i domänen med förtroende.
* **Dubbelriktat** -ger åtkomst från varje domän till resurser i den andra domänen.

Förtroenden konfigureras också för att hantera ytterligare förtroende relationer på något av följande sätt:

* Icke- **transitivt** – förtroendet finns bara mellan de två betrodda partner domänerna.
* **Transitivt** förtroende utökas automatiskt till alla andra domäner som någon av partnerna litar på.

I vissa fall upprättas förtroende relationer automatiskt när domäner skapas. Andra tider måste du välja en typ av förtroende och uttryckligen upprätta lämpliga relationer. De olika typerna av förtroenden som används och strukturen för dessa förtroende relationer beror på hur AD DS-katalogen är organiserad och om olika versioner av Windows finns i nätverket.

## <a name="trusts-between-two-forests"></a>Förtroenden mellan två skogar

Du kan utöka domän förtroenden inom en enda skog till en annan skog genom att manuellt skapa ett enkelriktat eller dubbelriktat skogs förtroende. Ett skogs förtroende är ett transitivt förtroende som bara finns mellan en skogs rots domän och en andra skogs rots domän.

* Med ett enkelriktat skogs förtroende kan alla användare i en skog lita på alla domäner i den andra skogen.
* Ett dubbelriktat skogs förtroende utgör en transitiv förtroende relation mellan varje domän i båda skogarna.

Förtroendets transitivhet för skogs förtroenden är begränsat till de två skogens partner. Skogs förtroendet utökas inte till ytterligare skogar som är betrodda av någon av partnerna.

![Diagram över skogs förtroende från Azure AD DS till lokal AD DS](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

Du kan skapa olika konfigurationer för domän-och skogs förtroende beroende på organisationens AD DS-struktur. Azure AD DS stöder bara ett enkelriktat skogs förtroende. I den här konfigurationen kan resurser i den hanterade domänen lita på alla domäner i en lokal skog.

## <a name="supporting-technology-for-trusts"></a>Stöd teknik för förtroenden

Förtroenden använder olika tjänster och funktioner, till exempel DNS för att hitta domänkontrollanter i partner skogar. Förtroenden är också beroende av autentiseringsprotokollen NTLM och Kerberos och på Windows-baserade mekanismer för auktorisering och åtkomst kontroll för att tillhandahålla en säker infrastruktur för kommunikation mellan AD DS-domäner och-skogar. Följande tjänster och funktioner hjälper till att stödja lyckade förtroende relationer.

### <a name="dns"></a>DNS

AD DS behöver DNS för plats och namn för domänkontrollant (DC). Följande stöd från DNS tillhandahålls för att AD DS ska fungera korrekt:

* En namn matchnings tjänst som låter nätverks värdar och-tjänster hitta domänkontrollanter.
* En namngivnings struktur som gör det möjligt för ett företag att spegla sin organisations struktur i namnen på dess katalog tjänst domäner.

En DNS-domän namn rymd distribueras vanligt vis som speglar AD DS-domänens namnrymd. Om det finns ett befintligt DNS-namnområde före AD DS-distributionen, är DNS-namnområdet vanligt vis partitionerat för AD DS och en DNS-underdomän och delegering för AD DS-skogs roten skapas. Ytterligare DNS-domännamn läggs sedan till för varje underordnad AD DS-domän.

DNS används också för att stödja platsen för AD DS-domänkontrollanter. DNS-zonerna fylls med DNS-resursposter som gör det möjligt för nätverks värdar och-tjänster att hitta AD DS-domänkontrollanter.

### <a name="applications-and-net-logon"></a>Program och Net Logon

Både program och tjänsten Net Logon är komponenter i modellen Windows-distribuerad säkerhets kanal. Program som är integrerade med Windows Server och AD DS använder autentiseringsprotokoll för att kommunicera med tjänsten Net Logon så att en säker sökväg kan upprättas över vilken autentisering kan utföras.

### <a name="authentication-protocols"></a>Autentiseringsprotokoll

AD DS DCs autentiserar användare och program med något av följande protokoll:

* **Autentiseringsprotokoll för Kerberos version 5**
    * Kerberos version 5-protokollet är det standard autentiseringsprotokoll som används av lokala datorer som kör Windows och som stöder operativ system från tredje part. Det här protokollet anges i RFC 1510 och är fullständigt integrerat med AD DS, SMB (Server Message Block), HTTP och RPC (Remote Procedure Call) samt de klient-och serverprogram som använder dessa protokoll.
    * När Kerberos-protokollet används behöver inte servern kontakta DOMÄNKONTROLLANTen. Klienten får i stället en biljett för en server genom att begära en från en DOMÄNKONTROLLANT i Server konto domänen. Servern verifierar sedan biljetten utan att konsultera någon annan instans.
    * Om en dator som ingår i en transaktion inte stöder Kerberos version 5-protokollet används NTLM-protokollet.

* **NTLM-autentiseringsprotokoll**
    * NTLM-protokollet är ett klassiskt protokoll för nätverksautentisering som används av äldre operativ system. Av kompatibilitetsskäl används den av AD DS-domäner för att bearbeta autentiseringsbegäranden från program som har utformats för tidigare Windows-baserade klienter och servrar och operativ system från tredje part.
    * När NTLM-protokollet används mellan en klient och en server måste servern kontakta en domän verifierings tjänst på en DOMÄNKONTROLLANT för att verifiera klientens autentiseringsuppgifter. Servern autentiserar klienten genom att vidarebefordra klientens autentiseringsuppgifter till en DOMÄNKONTROLLANT i klient konto domänen.
    * När två AD DS-domäner eller-skogar är anslutna med ett förtroende kan autentiseringsbegäranden som görs med hjälp av dessa protokoll dirigeras för att ge åtkomst till resurser i båda skogarna.

## <a name="authorization-and-access-control"></a>Auktorisering och åtkomst kontroll

Auktoriserings-och förtroende tekniker fungerar tillsammans för att tillhandahålla en säker infrastruktur för kommunikation mellan AD DS-domäner eller skogar. Auktorisering avgör vilken åtkomst nivå en användare har till resurser i en domän. Förtroenden underlättar för användare mellan domäner genom att ange en sökväg för autentisering av användare i andra domäner, så att deras begär anden till delade resurser i dessa domäner kan verifieras.

När en autentiseringsbegäran som görs i en betrodd domän verifieras av den betrodda domänen skickas den till mål resursen. Mål resursen avgör sedan om du vill auktorisera den specifika begäran som gjorts av användaren, tjänsten eller datorn i den betrodda domänen baserat på dess konfiguration för åtkomst kontroll.

Förtroenden tillhandahåller den här metoden för att verifiera autentiseringsbegäranden som skickas till en betrodd domän. Åtkomst kontroll metoder på resurs datorn fastställer den slutgiltiga åtkomst nivån som beviljats till begär anden i den betrodda domänen.

## <a name="next-steps"></a>Nästa steg

Mer information om förtroenden finns i [Hur fungerar skogs förtroenden i Azure AD DS?][concepts-trust]

För att komma igång med att skapa en hanterad domän med en resurs skog, se [skapa och konfigurera en Azure AD DS-hanterad domän][tutorial-create-advanced]. Du kan sedan [skapa ett utgående skogs förtroende till en lokal domän][create-forest-trust].

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
