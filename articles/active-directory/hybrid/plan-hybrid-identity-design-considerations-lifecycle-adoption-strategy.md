---
title: Hybrid Identity design – livs cykel implementering strategi Azure | Microsoft Docs
description: Hjälper dig att definiera uppgifter för Hybrid identitets hantering enligt de alternativ som är tillgängliga för varje livs cykel fas.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 420b6046-bd9b-4fce-83b0-72625878ae71
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf68406d4b0806e1d533e0bb8669a01939387989
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997757"
---
# <a name="determine-hybrid-identity-lifecycle-adoption-strategy"></a>Fastställ livs cykel för införande av hybrid identitet
I den här uppgiften definierar du identitets hanterings strategin för din hybrid identitets lösning som uppfyller de affärs krav som du definierade i [fastställa uppgifter för Hybrid identitets hantering](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md).

Om du vill definiera uppgifter för Hybrid identitets hantering enligt den slut punkt till slut punkt som anges tidigare i det här steget måste du överväga vilka alternativ som är tillgängliga för varje livs cykel fas.

## <a name="access-management-and-provisioning"></a>Åtkomst hantering och etablering
Med en bra konto åtkomst hanterings lösning kan din organisation spåra exakt vem som har åtkomst till den information som finns i organisationen.

Åtkomst kontroll är en kritisk funktion för ett centraliserat etablerings system med en punkt. Utöver att skydda känslig information visar åtkomst kontroller befintliga konton som har ej godkända auktoriseringar eller som inte längre behövs. För att kontrol lera gamla konton länkar etablerings systemet samman konto information med auktoritativ information om de användare som äger kontona. Information om auktoritativ användar identitet underhålls vanligt vis i databaserna och katalogerna för personal.

Konton i avancerade IT-företag innehåller hundratals parametrar som definierar myndigheterna, och dessa uppgifter kan styras av ditt etablerings system. Nya användare kan identifieras med de data som du anger från den auktoritativa källan. Funktionen för godkännande av åtkomst begär Anden initierar de processer som godkänner (eller avvisar) resurs etablering för dem.

| Fas för livs cykel hantering | Lokalt | Moln | Hybrid |
| --- | --- | --- | --- |
| Konto hantering och etablering |Med hjälp av serverrollen Active Directory® Domain Services (AD DS) kan du skapa en skalbar, säker och hanterbar infrastruktur för användare och resurshantering och erbjuda stöd för katalogaktiverade applikationer som Microsoft® Exchange Server. <br><br> [Du kan etablera grupper i AD DS via en identitets hanterare](/previous-versions/mim/ff686261(v=ws.10)) <br>[Du kan etablera användare i AD DS](/previous-versions/mim/ff686263(v=ws.10)) <br><br> Administratörer kan använda åtkomst kontroll för att hantera användar åtkomst till delade resurser av säkerhets synpunkt. I Active Directory administreras åtkomst kontrollen på objekt nivå genom att ange olika åtkomst nivåer eller behörigheter till objekt, till exempel fullständig behörighet, skriva, läsa eller ingen åtkomst. Åtkomst kontroll i Active Directory definierar hur olika användare kan använda Active Directory objekt. Som standard är behörigheter för objekt i Active Directory inställda på den säkraste inställningen. |Du måste skapa ett konto för varje användare som ska ha åtkomst till en moln tjänst från Microsoft. Du kan också ändra användar konton eller ta bort dem när de inte längre behövs. Som standard har användare inte administratörs behörighet, men du kan välja att tilldela dem. <br><br> I Azure Active Directory är en av de viktigaste funktionerna möjligheten att hantera åtkomst till resurser. Dessa resurser kan vara en del av katalogen, som i fallet med behörigheter för att hantera objekt via roller i katalogen, eller resurser som är externa för katalogen såsom SaaS-program, Azure-tjänster och SharePoint-webbplatser eller lokala resurser. <br><br> I mitten av Azure Active Directorys åtkomst hanterings lösning är säkerhets gruppen. Resursägaren (eller administratören för katalogen) kan tilldela en grupp för att ge en viss åtkomsträtt till resurser som ägaren äger. Medlemmarna i gruppen får åtkomst och resurs ägaren kan delegera rätten att hantera medlemmarnas lista över en grupp till någon annan – till exempel en avdelnings chef eller en supportavdelningen-administratör<br> <br> Avsnittet hantera grupper i Azure AD innehåller mer information om hur du hanterar åtkomst via grupper. |Utöka Active Directory identiteter i molnet genom synkronisering och Federation |

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll
Rollbaserad åtkomst kontroll i Azure (Azure RBAC) använder roller och etablerings principer för att utvärdera, testa och tillämpa affärs processer och regler för att bevilja åtkomst till användare. Nyckel administratörer skapar etablerings principer och tilldelar användare till roller och definierar uppsättningar av rättigheter till resurser för dessa roller. Azure RBAC utökar identitets hanterings lösningen så att den använder programvarubaserade processer och minskar användar handskrivna åtgärder i etablerings processen.
Med Azure RBAC kan företaget begränsa antalet åtgärder som en enskild person kan göra när de har åtkomst till Azure Portal. Genom att använda Azure RBAC för att kontrol lera åtkomsten till portalen, har IT-administratörerna ombuds åtkomst med hjälp av följande metoder för åtkomst hantering:

* **Gruppbaserad roll tilldelning**: du kan tilldela åtkomst till Azure AD-grupper som kan synkroniseras från din lokala Active Directory. På så sätt kan du utnyttja de befintliga investeringar som din organisation har gjort i verktyg och processer för att hantera grupper. Du kan också använda funktionen delegerad grupp hantering i Azure AD Premium.
* Använd **inbyggda roller i Azure**: du kan använda tre roller – ägare, deltagare och läsare för att säkerställa att användare och grupper har behörighet att bara utföra de uppgifter som de behöver för att utföra sina jobb.
* **Detaljerad åtkomst till resurser**: du kan tilldela roller till användare och grupper för en viss prenumeration, resurs grupp eller en enskild Azure-resurs, till exempel en webbplats eller databas. På så sätt kan du se till att användarna har åtkomst till alla resurser som de behöver och ingen åtkomst till resurser som de inte behöver hantera.

## <a name="provisioning-and-other-customization-options"></a>Etablering och andra anpassnings alternativ
Ditt team kan använda affärs planer och-krav för att avgöra hur mycket du ska anpassa identitets lösningen. Ett stort företag kan till exempel kräva en stegvis sammanslagnings plan för arbets flöden och anpassade kort som baseras på en tids linje för stegvis etablering av program som används ofta i olika geografiska områden. En annan anpassnings plan kan tillhandahålla att två eller flera program tillhandahålls i hela organisationen efter testet. Interaktionen mellan användare och program kan anpassas och procedurer för etablering av resurser kan ändras för att anpassa automatisk etablering.

Du kan avetablera för att ta bort en tjänst eller komponent. Om du exempelvis avetablerar ett konto innebär det att kontot tas bort från en resurs.

Hybrid modellen för etablerings resurser kombinerar begär ande och rollbaserade metoder, som båda stöds av Azure AD. För en delmängd av anställda eller hanterade system kan ett företag vilja automatisera åtkomst med rollbaserad tilldelning. Ett företag kan också hantera alla andra åtkomst begär Anden eller undantag via en förfrågnings beroende modell. Vissa företag kan börja med manuell tilldelning och utveckla sig mot en hybrid modell, med avsikt att använda en helt rollbaserad distribution vid ett senare tillfälle.

Andra företag kan upptäcka att det är opraktiskt för affärs skäl att uppnå fullständig rollbaserad etablering och rikta en hybrid strategi som ett önskat mål. Även andra företag kan vara uppfyllt med endast begärd etablering och inte vill investera ytterligare arbete för att definiera och hantera rollbaserade, automatiserade etablerings principer.

## <a name="license-management"></a>Licenshantering
Med gruppbaserad licens hantering i Azure AD kan administratörer tilldela användare till en säkerhets grupp och Azure AD tilldelar automatiskt licenser till alla medlemmar i gruppen. Om en användare senare läggs till i eller tas bort från gruppen, tilldelas eller tas en licens automatiskt bort efter behov.

Du kan använda grupper som du synkroniserar från lokala AD eller hantera i Azure AD. Genom att para ihop detta med Azure AD Premium Self-Service Group Management kan du enkelt delegera licens tilldelning till rätt besluts fattare. Du kan vara säker på att problem som licens konflikter och saknade plats data automatiskt sorteras ut.

## <a name="self-regulating-user-administration"></a>Själv reglerande användar administration
När din organisation börjar etablera resurser i alla interna organisationer, implementerar du den själv reglerande användar administrationen. Du kan utnyttja fördelarna och fördelarna med att tillhandahålla användare över organisationens gränser. I den här miljön avspeglas en ändring i en användares status automatiskt i åtkomst rättigheter för organisationens gränser och geografiska områden. Du kan minska etablerings kostnaderna och effektivisera åtkomst-och godkännande processerna. Implementeringen inser hela potentialen med att implementera rollbaserad åtkomst kontroll för slut punkt till slut punkts åtkomst hantering i din organisation. Du kan minska administrativa kostnader genom att automatisera procedurer för styrning av användar etablering. Du kan förbättra säkerheten genom att automatisera säkerhets principens tillämpning och effektivisera och centralisera hantering av användar livs cykeln och resurs etablering för stora användar populationer.

> [!NOTE]
> Mer information finns i Konfigurera Azure AD för självbetjänings program åtkomst hantering
> 
> 

Licensbaserade (berättigade) Azure AD-tjänster fungerar genom att aktivera en prenumeration i din Azure AD-katalog/tjänst klient. När prenumerationen är aktiv kan tjänst funktionerna hanteras av katalog-/tjänst administratörer och används av licensierade användare. 

## <a name="integration-with-other-3rd-party-providers"></a>Integrering med andra leverantörer från tredje part

Azure Active Directory ger enkel inloggning och utökad åtkomst till program åtkomst till tusentals SaaS-program och lokala webb program. Mer information finns i [integrera program med Azure Active Directory](../develop/quickstart-register-app.md)

## <a name="define-synchronization-management"></a>Definiera hantering av synkronisering
Om du integrerar dina lokala kataloger med Azure AD kan du hjälpa dina användare att bli mer produktiva genom att tillhandahålla en gemensam identitet för åtkomst både till molnet och lokala resurser. Med den här integrationen kan användare och organisationer dra nytta av följande:

* Organisationer kan ge användare en gemensam hybrid identitet i lokala eller molnbaserade tjänster som utnyttjar Windows Server Active Directory och sedan ansluter till Azure Active Directory.
* Administratörer kan ge villkorlig åtkomst baserat på program resurs, enhets-och användar identitet, nätverks plats och Multi-Factor Authentication.
* Användare kan använda sin gemensamma identitet via konton i Azure AD till Microsoft 365, Intune, SaaS-appar och tredjepartsprogram.
* Utvecklare kan bygga program som utnyttjar den gemensamma identitets modellen, integrera program i Active Directory lokalt eller i Azure för molnbaserade program

Följande figur innehåller ett exempel på en övergripande vy över processen för synkronisering av identiteter.

![Synkronisera](./media/plan-hybrid-identity-design-considerations/identitysync.png)

Identitetssynkronisering process

Granska följande tabell för att jämföra alternativen för synkronisering:

| Alternativ för hantering av synkronisering | Fördelar | Nackdelar |
| --- | --- | --- |
| Sync-baserad (via DirSync eller AADConnect) |Användare och grupper synkroniserade lokalt och i molnet <br>  **Princip kontroll**: konto principer kan ställas in via Active Directory, vilket ger administratören möjlighet att hantera lösen ords principer, arbets stationer, begränsningar, låsnings kontroller och mycket mer, utan att behöva utföra ytterligare aktiviteter i molnet.  <br>  **Åtkomst kontroll**: kan begränsa åtkomsten till moln tjänsten så att tjänsterna kan nås via företags miljön, via Online servrar eller både och. <br>  Färre support samtal: om användare har färre lösen ord för att komma ihåg är det mindre troligt att de glömmer bort dem. <br>  Säkerhet: användar identiteter och information skyddas på grund av att alla servrar och tjänster som används i enkel inloggning är hanterade och kontrollerade lokalt. <br>  Stöd för stark autentisering: du kan använda stark autentisering (kallas även tvåfaktorautentisering) med moln tjänsten. Om du använder stark autentisering måste du dock använda enkel inloggning. | |
| Federation-baserad (via AD FS) |Aktiverat av säkerhetstokentjänst (STS). När du konfigurerar en STS för att tillhandahålla enkel inloggning med en moln tjänst från Microsoft, kommer du att skapa ett federerat förtroende mellan din lokala STS och den federerade domän som du har angett i Azure AD-klienten. <br> Låter slutanvändare använda samma uppsättning autentiseringsuppgifter för att få åtkomst till flera resurser <br>slutanvändare behöver inte ha flera uppsättningar med autentiseringsuppgifter. Användarna måste ännu ange sina autentiseringsuppgifter för var och en av de deltagande resurserna., B2B-och B2C-scenarier stöds. |Kräver specialiserad personal för distribution och underhåll av dedikerade lokala AD FS-servrar. Det finns begränsningar för användningen av stark autentisering om du planerar att använda AD FS för din STS. Mer information finns i [Konfigurera avancerade alternativ för AD FS 2,0](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh237448(v=ws.10)). |

> [!NOTE]
> Mer information finns i [integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
> 
> 

## <a name="see-also"></a>Se även
[Översikt över design överväganden](plan-hybrid-identity-design-considerations-overview.md)