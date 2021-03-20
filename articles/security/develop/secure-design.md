---
title: Utforma säkra program på Microsoft Azure
description: Den här artikeln beskriver metod tips som du bör tänka på under krav och design för ditt webb program projekt.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 743412b7602e5781911cdf190e41a5ee15bfddd4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96487685"
---
# <a name="design-secure-applications-on-azure"></a>Utforma säkra program på Azure
I den här artikeln presenterar vi säkerhets aktiviteter och kontroller som du bör tänka på när du utformar program för molnet. Utbildnings resurser tillsammans med säkerhets frågor och koncept som du bör tänka på under krav och design faser i Microsoft [Security Development Lifecycle (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) omfattas. Målet är att hjälpa dig att definiera aktiviteter och Azure-tjänster som du kan använda för att utforma ett säkrare program.

Följande SDL-faser beskrivs i den här artikeln:

- Utbildning
- Krav
- Design

## <a name="training"></a>Utbildning
Innan du börjar utveckla ditt moln program tar du tid att förstå säkerheten och sekretessen på Azure. Genom att göra det här steget kan du minska antalet sårbarheter och allvarlighets graden för sårbarheter i ditt program. Du är mer beredd att reagera på lämpligt sätt för att åtgärda det ständigt föränderliga hotet.

Använd följande resurser under inlärnings fasen för att bekanta dig med de Azure-tjänster som är tillgängliga för utvecklare och med rekommenderade säkerhets metoder på Azure:

  - [Developer ' s Guide to Azure](https://azure.microsoft.com/campaigns/developer-guide/) visar hur du kommer igång med Azure. Guiden visar vilka tjänster du kan använda för att köra dina program, lagra dina data, införliva intelligens, bygga IoT-appar och distribuera dina lösningar på ett mer effektivt och säkert sätt.

  - [Kom igång-guide för Azure-utvecklare](../../guides/developer/azure-developer-guide.md) ger nödvändig information för utvecklare som vill komma igång med Azure-plattformen för deras utvecklings behov.

  - [SDK: er och verktyg](../../index.yml?pivot=sdkstools) beskriver de verktyg som är tillgängliga i Azure.

  - [Azure DevOps Services](/azure/devops/) innehåller samarbets verktyg för utveckling. Verktygen omfattar högpresterande pipelines, kostnads fria git-databaser, konfigurerbara kanban-kort och omfattande automatiserad och molnbaserad belastnings testning.
    [DevOps Resource Center](/azure/devops/learn/) kombinerar våra resurser för Learning DevOps-metoder, git-, flexibla metoder, hur vi arbetar med DevOps på Microsoft och hur du kan utvärdera ditt eget DevOps-förlopp.

  - De [5 främsta säkerhets objekten som du bör tänka på innan du överför till produktion](/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca) visar hur du skyddar dina webb program på Azure och skyddar dina appar mot de vanligaste och farliga webb program angrepp.

  - [Secure DevOps kit för Azure](https://azsk.azurewebsites.net/index.html) är en samling skript, verktyg, tillägg och automatiseringar som följer de omfattande Azure-prenumerations-och resurs säkerhets behoven hos DevOps team som använder omfattande automatisering. Secure DevOps kit för Azure kan visa hur du smidigt integrerar säkerheten i dina interna DevOps-arbetsflöden. I paketet finns verktyg som SVTs (Security Verification tests), som kan hjälpa utvecklare att skriva säker kod och testa den säkra konfigurationen för sina moln program i kodnings-och tidigt utvecklings faserna.

  - [Rekommenderade säkerhets metoder för Azure-lösningar](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions) ger en samling säkerhets metoder som du kan använda när du utformar, distribuerar och hanterar dina moln lösningar med hjälp av Azure.

## <a name="requirements"></a>Krav
Krav definitions fasen är ett viktigt steg i att definiera vad ditt program är och vad det gör när det släpps. Krav fasen är också en tid att tänka på när de säkerhets kontroller som du kommer att bygga i ditt program. Under den här fasen börjar du också med stegen som du tar i SDL för att säkerställa att du släpper och distribuerar ett säkert program.

### <a name="consider-security-and-privacy-issues"></a>Överväg säkerhets-och sekretess problem
Den här fasen är den bästa tiden för att beakta grundläggande säkerhets-och integritets problem. Att definiera acceptabla säkerhets nivåer och sekretess i början av ett projekt hjälper ett team:

- Förstå risker som rör säkerhets problem.
- Identifiera och åtgärda säkerhets buggar under utvecklingen.
- Tillämpa etablerade nivåer av säkerhet och sekretess i hela projektet.

När du skriver kraven för ditt program bör du tänka på säkerhets kontroller som kan hjälpa dig att skydda dina program och data.

### <a name="ask-security-questions"></a>Ställ säkerhets frågor
Ställ säkerhets frågor som:

  - Innehåller mitt program känsliga data?

  - Samlar mitt program in eller lagrar data som kräver att jag följer bransch standarder och compliance-program, till exempel [råds FFIEC (Federal Financial institution Retesting)](/previous-versions/azure/security/blueprints/ffiec-analytics-overview) eller [betalnings kortet bransch data säkerhets standarder (PCI DSS)](/previous-versions/azure/security/blueprints/pcidss-analytics-overview)?

  - Samlar mitt program in eller innehåller känslig personlig information eller kund information som kan användas, antingen på egen hand eller med annan information, för att identifiera, kontakta eller hitta en enskild person?

  - Samlar mitt program in eller innehåller data som kan användas för att få åtkomst till en persons medicinska, utbildnings-, finans-och anställnings information? Genom att identifiera data känslighet under krav fasen kan du klassificera dina data och identifiera den data skydds metod som du ska använda för ditt program.

  - Var och hur lagras mina data? Överväg hur du övervakar de lagrings tjänster som programmet använder för oväntade förändringar (till exempel långsammare svars tider). Kommer du att kunna påverka loggningen för att samla in mer detaljerade data och analysera ett problem i djupet?

  - Kommer mitt program vara tillgängligt för allmänheten (endast på Internet) eller endast internt? Hur skyddar du de data som kan samlas in från att användas på fel sätt om ditt program är tillgängligt för allmänheten? Om programmet endast är tillgängligt internt kan du fundera över vem i din organisation som ska ha åtkomst till programmet och hur länge de ska ha åtkomst.

  - Förstår du din identitets modell innan du börjar utforma ditt program? Hur ska du bestämma att användarna är de som de säger att de är och vad en användare har behörighet att göra?

  - Utför mitt program känsliga eller viktiga uppgifter (till exempel överföring av pengar, att låsa upp dörrar eller leverera medicin)?
    Överväg hur du ska verifiera att användaren som utför en känslig uppgift har behörighet att utföra uppgiften och hur du kommer att autentisera att personen är den som de säger att de är. Auktorisering (AuthZ) är en åtgärd för att ge ett autentiserat säkerhets objekt behörighet att göra något. Autentisering (authn) är en utmaning för att licensiera en part för legitima autentiseringsuppgifter.

  - Utför mitt program alla riskfyllda program varu aktiviteter, t. ex. att tillåta att användare laddar upp eller laddar ned filer eller andra data? Om ditt program utför riskfyllda aktiviteter bör du fundera över hur programmet kommer att skydda användare från att hantera skadliga filer eller data.

### <a name="review-owasp-top-10"></a>Granska OWASP topp 10
Överväg att granska de [<span class="underline">viktigaste 10 program säkerhets riskerna i OWASP</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).
OWASP 10 främsta tar itu med kritiska säkerhets risker för webb program.
Medvetenheten om dessa säkerhets risker kan hjälpa dig att fatta krav och utforma beslut som minimerar riskerna i ditt program.

Det är viktigt att tänka på säkerhets kontroller för att förhindra överträdelser.
Du vill dock även anta att [en överträdelse](/azure/devops/learn/devops-at-microsoft/security-in-devops) sker. Om du antar en överträdelse kan du svara på viktiga frågor om säkerhet i förväg, så att de inte behöver besvaras i nödfall:

  - Hur kan jag identifiera ett angrepp?

  - Vad ska jag göra om det uppstår ett angrepp eller en överträdelse?

  - Hur ska jag återställa från angreppet som data läckor eller manipulering?

## <a name="design"></a>Design

Design fasen är avgörande för att skapa bästa metoder för design och funktionella specifikationer. Det är också viktigt att utföra riskanalyser som bidrar till att minska säkerhets-och integritets problem i ett projekt.

När du har säkerhets krav på plats och använder säkra design koncept kan du undvika eller minimera affärs möjligheter för ett säkerhets fel. Ett säkerhets fel är en överblick i utformningen av programmet som kan tillåta en användare att utföra skadliga eller oväntade åtgärder när ditt program släpps.

Under Design fasen bör du också tänka på hur du kan använda säkerhet i lager. en nivå av försvar är inte nödvändigt vis tillräckligt. Vad händer om en angripare tidigare har en brand vägg för webbaserade program (WAF)? Du vill ha en annan säkerhets kontroll på plats för att skydda mot angrepp.

Med detta i åtanke diskuterar vi följande säkra design koncept och de säkerhets kontroller som du bör ta itu med när du utformar säkra program:

- Använd ett säkert kodnings bibliotek och ett ramverk för program vara.
- Sök efter sårbara komponenter.
- Använd hot modellering under program design.
- Minska angrepps ytan.
- Anta en identitets princip som primär säkerhetsperimeter.
- Kräv ny autentisering för viktiga transaktioner.
- Använd en nyckel hanterings lösning för att skydda nycklar, autentiseringsuppgifter och andra hemligheter.
- Skydda känsliga data.
- Implementera fel säkert mått.
- Dra nytta av fel-och undantags hantering.
- Använd loggning och aviseringar.

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>Använda ett säkert kodnings bibliotek och ett ramverk för program vara

För utveckling använder du ett säkert kodnings bibliotek och ett ramverk för program vara som har inbyggd säkerhet. Utvecklare kan använda befintliga, beprövade funktioner (kryptering, indata-sanering, kodning av utdata, nycklar och anslutnings strängar och allt annat som skulle anses vara en säkerhets kontroll) i stället för att utveckla säkerhets kontroller från grunden. Detta hjälper till att skydda mot säkerhetsrelaterade design-och implementerings fel.

Se till att du använder den senaste versionen av ramverket och alla säkerhetsfunktioner som är tillgängliga i ramverket. Microsoft erbjuder en omfattande [uppsättning utvecklingsverktyg](https://azure.microsoft.com/product-categories/developer-tools/) för alla utvecklare, som arbetar på vilken plattform eller vilket språk som helst, för att leverera moln program. Du kan koda med valfritt språk genom att välja bland olika [SDK](https://azure.microsoft.com/downloads/): er.
Du kan dra nytta av kompletta, integrerade utvecklings miljöer (IDE: er) och redigerare som har avancerade fel söknings funktioner och inbyggd support för Azure.

Microsoft erbjuder en mängd olika [språk, ramverk och verktyg](../../index.yml?panel=sdkstools-all&pivot=sdkstools) som du kan använda för att utveckla program i Azure. Ett exempel är [Azure för .net och .net Core-utvecklare](/dotnet/azure/). För varje språk och ramverk som vi erbjuder hittar du snabb starter, självstudier och API-referenser som hjälper dig att komma igång snabbt.

Azure erbjuder en mängd olika tjänster som du kan använda för att vara värd för webbplatser och webb program. Med dessa tjänster kan du utveckla på ditt favorit språk, oavsett om det är .NET, .NET Core, Java, ruby, Node.js, PHP eller python.
[Azure App Service Web Apps](../../app-service/overview.md) (Web Apps) är en av dessa tjänster.

Web Apps lägger till kraften i Microsoft Azure till ditt program. Det omfattar säkerhet, belastnings utjämning, automatisk skalning och automatisk hantering. Du kan också dra nytta av DevOps-funktioner i Web Apps, t. ex. paket hantering, mellanlagrings miljöer, anpassade domäner, SSL/TLS-certifikat och kontinuerlig distribution från Azure DevOps, GitHub, Docker Hub och andra källor.

Azure erbjuder andra tjänster som du kan använda som värd för webbplatser och webb program. I de flesta fall är Web Apps det bästa alternativet. För en mikrotjänst arkitektur bör du överväga [Azure Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric).
Om du behöver större kontroll över de virtuella datorer koden körs på kan du använda [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/).
Mer information om hur du väljer mellan dessa Azure-tjänster finns i [jämförelse av Azure App Service, Virtual Machines, Service Fabric och Cloud Services](/azure/architecture/guide/technology-choices/compute-decision-tree).

### <a name="apply-updates-to-components"></a>Tillämpa uppdateringar på komponenter

För att förhindra sårbarheter bör du kontinuerligt inventera både klient sidans och Server sidans komponenter (till exempel ramverk och bibliotek) och deras beroenden för uppdateringar. Nya sårbarheter och uppdaterade program versioner släpps kontinuerligt. Se till att du har en pågående plan för att övervaka, prioritering och tillämpa uppdateringar eller konfigurations ändringar i de bibliotek och komponenter som du använder.

Se sidan [Öppna webb program säkerhets projekt (OWASP)](https://www.owasp.org/index.php/Main_Page) om du vill [använda komponenter med kända sårbarheter](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities) för verktygs förslag. Du kan också prenumerera på e-postaviseringar om säkerhets risker som är relaterade till de komponenter som du använder.

### <a name="use-threat-modeling-during-application-design"></a>Använda hot modellering under program design

Hot modellering är en process för att identifiera potentiella säkerhetshot för din verksamhet och ditt program och sedan se till att lämpliga åtgärder är på plats. SDL anger att team bör delta i Threat Modeling under Design fasen när det är relativt enkelt och kostnads effektivt att lösa potentiella problem. Genom att använda Threat Modeling i Design fasen kan du avsevärt minska den totala utvecklingen av kostnaderna.

För att hjälpa till att under lätta processen för hot modellering utformade vi [SDL Threat Modeling Tool](threat-modeling-tool.md) med icke-säkerhetsexperter i åtanke. Det här verktyget gör det lättare att utforma hot för alla utvecklare genom att tillhandahålla tydlig vägledning om hur du skapar och analyserar hot modeller.

Att modellera program designen och räkna upp [kliv](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) -hot – förfalskning, manipulering, oavvislig het, utlämnande av information, Denial of service och rättighets ökning – över alla förtroende gränser har visat ett effektivt sätt att fånga design fel tidigt. I följande tabell visas en lista över kliv-hoten och ger några exempel på lösningar som använder funktioner som tillhandahålls av Azure. Dessa åtgärder fungerar inte i varje situation.

| Hot | Säkerhets egenskap | Potentiell Azure-plattforms minskning |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Förfalskning               | Autentisering        | [KRÄV HTTPS-anslutningar](/aspnet/core/security/enforcing-ssl?tabs=visual-studio&view=aspnetcore-2.1). |
| Manipulering              | Integritet             | Verifiera SSL/TLS-certifikat. Program som använder SSL/TLS måste fullständigt verifiera X. 509-certifikaten för de entiteter som de ansluter till. Använd Azure Key Vault certifikat för att [Hantera dina x509-certifikat](../../key-vault/general/about-keys-secrets-certificates.md). |
| Avvislighet            | Oavvislighet       | Aktivera [övervakning och diagnostik](/azure/architecture/best-practices/monitoring)i Azure.|
| Avslöjande av information | Konfidentialitet       | Kryptera känsliga data i [vila](../fundamentals/encryption-atrest.md) och [under överföring](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit). |
| Denial of Service      | Tillgänglighet          | Övervaka prestanda mått för potentiella denial of service-villkor. Implementera anslutnings filter. [Azure DDoS Protection](../../ddos-protection/ddos-protection-overview.md#next-steps), kombinerat med bästa praxis för program design, ger skydd mot DDoS-attacker.|
| Höjning av privilegier | Auktorisering         | Använd Azure Active Directory <span class="underline"></span> [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md).|

### <a name="reduce-your-attack-surface"></a>Minska attack ytan

En attack yta är den totala summan av var potentiella sårbarheter kan uppstå. I det här dokumentet fokuserar vi på ett programs attack yta.
Fokus är att skydda ett program från angrepp. Ett enkelt och snabbt sätt att minimera din attack yta är att ta bort oanvända resurser och kod från ditt program. Ju mindre ditt program är, är det mindre angrepps ytan. Ta till exempel bort:

- Kod för funktioner som du ännu inte har släppt.
- Felsöknings support kod.
- Nätverks gränssnitt och protokoll som inte används eller som är föråldrade.
- Virtuella datorer och andra resurser som du inte använder.

Att regelbundet rensa dina resurser och se till att du tar bort oanvänd kod är bra sätt att se till att det finns färre möjligheter för skadliga aktörer att angripa.

Ett mer detaljerat och djupgående sätt att minska risken för angrepp är att slutföra en analys av attack ytan. Med en analys av attack ytan kan du mappa delar av ett system som måste granskas och testas av säkerhets problem.

Syftet med en analys av attack ytan är att förstå risk områdena i ett program, så utvecklare och säkerhetsspecialister är medvetna om vilka delar av programmet som är öppna för angrepp. Sedan kan du hitta olika sätt att minimera den här potentiella, spåra när och hur angrepps ytan ändras, och vad detta innebär från ett risk perspektiv.

Med en analys av attack ytan kan du identifiera:

- Funktioner och delar av systemet du behöver granska och testa för säkerhets risker.
- Högrisk områden i kod som kräver skydd mot djupgående skydd (delar av systemet som du behöver försvara).
- När du ändrar angrepps ytan och behöver uppdatera en hot utvärdering.

Att minska möjligheterna för angripare att utnyttja en potentiell svag punkt eller sårbarhet kräver att du noggrant analyserar programmets övergripande attack yta. Det omfattar också att inaktivera eller begränsa åtkomsten till system tjänster, att tillämpa principen om minsta behörighet och att använda överordnat försvar när så är möjligt.

Vi diskuterar att [utföra en granskning av attack ytan](secure-develop.md#conduct-attack-surface-review) under verifierings fasen för sdl.

> [!NOTE]
> **Vad är skillnaden mellan hot modellering och analys av angrepps ytor?**
Hot modellering är en process där du kan identifiera potentiella säkerhetshot för ditt program och se till att lämpliga åtgärder mot hoten är på plats. Analys av attack ytan identifierar högrisk områden i kod som är öppna för angrepp. Det innefattar att hitta sätt att försvara högrisk områden i ditt program och granska och testa dessa områden innan du distribuerar programmet.

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Anta en identitets princip som primär säkerhetsperimeter

När du utformar moln program är det viktigt att du utökar din säkerhetsperimeters fokus från en nätverks inriktad metod till en identitets inriktad metod. Tidigare var den primära lokala säkerhets omkretsen en organisations nätverk. De flesta lokala säkerhets modeller använder nätverket som den primära säkerhetspivoten. För moln program betjänas du bättre genom att fundera över identiteten som den primära säkerhets omkretsen.

Saker du kan göra för att utveckla en identitets inriktad metod för att utveckla webb program:

- Tvinga Multi-Factor Authentication för användare.
- Använd kraftfulla plattformar för autentisering och auktorisering.
- Använd principen för minsta behörighet.
- Implementera just-in-Time-åtkomst.

#### <a name="enforce-multi-factor-authentication-for-users"></a>Tvinga Multi-Factor Authentication för användare

Använd tvåfaktorautentisering. Tvåfaktorautentisering är den aktuella standarden för autentisering och auktorisering eftersom den undviker de säkerhets svagheter som finns i användar namn och lösen ords typer för autentisering. Åtkomst till Azures hanterings gränssnitt (Azure Portal/fjärr-PowerShell) och till kundinriktade tjänster bör utformas och konfigureras för användning av [azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md).

#### <a name="use-strong-authentication-and-authorization-platforms"></a>Använd kraftfulla plattformar för autentisering och auktorisering

Använd de mekanismer för autentisering och auktorisering som tillhandahålls av plattformen i stället för anpassad kod. Detta beror på att det kan vara lätt att utveckla anpassad autentiseringsnyckel. Kommersiell kod (till exempel från Microsoft) är ofta en omfattande granskning av säkerhet. [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) är Azure-lösningen för identitets-och åtkomst hantering. Dessa Azure AD-verktyg och-tjänster hjälper till med säker utveckling:

- [Microsoft Identity Platform](../../active-directory/develop/index.yml) är en uppsättning komponenter som utvecklare använder för att skapa appar som kan logga in användare på ett säkert sätt. Plattformen hjälper utvecklare som utvecklar appar för en enda klient, verksamhetsspecifika appar och utvecklare som vill utveckla appar för flera klient organisationer. Förutom grundläggande inloggning kan appar som skapats med hjälp av Microsoft Identity Platform anropa Microsoft API: er och anpassade API: er. Microsoft Identity Platform stöder bransch standard protokoll som OAuth 2,0 och OpenID Connect.

- [Azure Active Directory B2C (Azure AD B2C)](../../active-directory-b2c/index.yml) är en tjänst för identitets hantering som du kan använda för att anpassa och styra hur kunder registrerar sig, loggar in och hanterar sina profiler när de använder dina program. Detta omfattar program som har utvecklats för iOS, Android och .NET, bland annat. Azure AD B2C aktiverar de här åtgärderna samtidigt som kundernas identiteter skyddas.

#### <a name="apply-the-principle-of-least-privilege"></a>Tillämpa principen för minsta behörighet

Begreppet [minsta behörighet](https://en.wikipedia.org/wiki/Principle_of_least_privilege) innebär att ge användarna den exakta nivån av åtkomst och kontroll de behöver för att utföra sina jobb och inget mer.

Skulle en program varu utvecklare behöva domän administratörs rättigheter? Skulle en administrativ assistent ha åtkomst till administrativa kontroller på sin personliga dator? Att utvärdera åtkomsten till program varan är ingen annan. Om du använder [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md) för att ge användarna olika förmågor och auktoriteter i ditt program ger du inte alla åtkomst till allt. Genom att begränsa åtkomsten till vad som krävs för varje roll begränsar du risken för ett säkerhets problem som uppstår.

Se till att ditt program har [minst behörighet](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications) över sina åtkomst mönster.

> [!NOTE]
> Reglerna för minsta behörighet måste gälla för program varan och för de personer som skapar program varan. Programutvecklare kan vara enorma risker för IT-säkerheten om de ges för mycket åtkomst. Konsekvenserna kan vara svåra om en utvecklare har skadlig avsikt eller har fått för mycket åtkomst. Vi rekommenderar att reglerna för minsta behörighet tillämpas för utvecklare under hela utvecklings livs cykeln.

#### <a name="implement-just-in-time-access"></a>Implementera just-in-Time-åtkomst

Implementera JIT-åtkomst ( *just-in-Time* ) för att ytterligare minska exponerings tiden för privilegier. Använd [Azure AD Privileged Identity Management](../../active-directory/roles/security-planning.md#stage-3-take-control-of-admin-activity) för att:

- Ge användarna de behörigheter de behöver endast för JIT.
- Tilldela roller för kortare varaktighet med förtroende för att privilegierna återkallas automatiskt.

### <a name="require-re-authentication-for-important-transactions"></a>Kräv ny autentisering för viktiga transaktioner

[Förfalskning av begäran mellan webbplatser](/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1) (även kallat *XSRF* eller *CSRF*) är en attack mot webb värdbaserade appar där en skadlig webbapp påverkar interaktionen mellan en klient webbläsare och en webbapp som litar på webbläsaren. Förfalsknings angrepp mellan webbplatser är möjligt eftersom webbläsare skickar vissa typer av autentiseringstoken automatiskt till varje begäran till en webbplats.
Den här typen av exploatering kallas även för ett *angrepp* eller en *session* som kan utföras på grund av att angreppet utnyttjar användarens tidigare autentiserade session.

Det bästa sättet att skydda mot den här typen av angrepp är att fråga användaren om något som bara användaren kan ange före varje viktig transaktion, till exempel ett köp, konto inaktive ring eller en lösen ords ändring. Du kan be användaren att ange sitt lösen ord på nytt, slutföra en captcha eller skicka en hemlig token som bara användaren skulle ha. Den vanligaste metoden är den hemliga token.

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>Använd en nyckel hanterings lösning för att skydda nycklar, autentiseringsuppgifter och andra hemligheter

Att förlora nycklar och autentiseringsuppgifter är ett vanligt problem. Det enda som är sämre än att förlora nycklar och autentiseringsuppgifter är att en obehörig part får till gång till dem. Angripare kan dra nytta av automatiserade och manuella tekniker för att hitta nycklar och hemligheter som lagras i kod databaser som GitHub. Lägg inte till nycklar och hemligheter i dessa offentliga kod databaser eller på någon annan server.

Lägg alltid till nycklar, certifikat, hemligheter och anslutnings strängar i en nyckel hanterings lösning. Du kan använda en centraliserad lösning där nycklar och hemligheter lagras i HSM: er (Hardware Security modules). Azure ger dig en HSM i molnet med [Azure Key Vault](../../key-vault/general/overview.md).

Key Vault är ett *hemligt Arkiv*: det är en centraliserad moln tjänst för att lagra program hemligheter. Key Vault skyddar dina konfidentiella data genom att spara program hemligheter på en enda, central plats och tillhandahålla säker åtkomst, behörighets kontroll och åtkomst loggning.

Hemligheter lagras i enskilda *valv*. Varje valv har sina egna konfigurations-och säkerhets principer för att kontrol lera åtkomst. Du kommer till dina data via en REST API eller via en klient-SDK som är tillgänglig för de flesta programmeringsspråk.

> [!IMPORTANT]
> Azure Key Vault har utformats för att lagra konfigurations hemligheter för Server program. Det är inte avsett för att lagra data som tillhör App-användare. Det återspeglas i dess prestandaegenskaper, API och kostnadsmodell.
>
> Användar data ska lagras någon annan stans, som i en Azure SQL Database-instans som har transparent datakryptering (TDE) eller i ett lagrings konto som använder Azure Storage tjänst kryptering. Hemligheter som används av ditt program för att komma åt dessa data lager kan behållas i Azure Key Vault.

### <a name="protect-sensitive-data"></a>Skydda känsliga data

Att skydda data är en viktig del av din säkerhets strategi.
Genom att klassificera dina data och identifiera dina data skydds behov kan du utforma din app med data säkerhet i åtanke. Genom att klassificera (kategorisera) lagrade data efter känslighet och inverkan på företaget kan utvecklare avgöra vilka risker som är associerade med data.

Märk alla aktuella data som känsliga när du utformar dina data format. Se till att programmet behandlar aktuella data som känsliga. Dessa metoder kan hjälpa dig att skydda känsliga data:

- Använd kryptering.
- Undvik att avkoda hemligheter som nycklar och lösen ord.
- Se till att åtkomst kontroller och granskning är på plats.

#### <a name="use-encryption"></a>Använd kryptering

Att skydda data bör vara en viktig del av din säkerhets strategi.
Om dina data lagras i en databas eller om de flyttas fram och tillbaka mellan platser, använder du kryptering av [data i vila](../fundamentals/encryption-atrest.md) (i databasen) och kryptering av [data under överföring](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit) (på väg till och från användaren, databasen, ett API eller en tjänst slut punkt). Vi rekommenderar att du alltid använder SSL/TLS-protokoll för att utbyta data. Se till att du använder den senaste versionen av TLS för kryptering (för närvarande är version 1,2).

#### <a name="avoid-hard-coding"></a>Undvik fast kodning

Vissa saker bör aldrig vara hårdkodade i program varan. Några exempel är värdnamn eller IP-adresser, URL: er, e-postadresser, användar namn, lösen ord, lagrings konto nycklar och andra kryptografiska nycklar. Överväg att implementera krav runt vad som kan eller inte kan hårdkoda i din kod, inklusive i kommentars avsnitten i koden.

När du infogar kommentarer i din kod, se till att du inte sparar känslig information. Detta inkluderar din e-postadress, lösen ord, anslutnings strängar, information om ditt program som bara skulle vara känt av någon i din organisation, och allt annat som kan ge en angripare möjlighet att attackera ditt program eller din organisation.

I princip förutsätter vi att allt innehåll i ditt utvecklings projekt är offentlig information när det distribueras. Undvik att inkludera känsliga data av någon typ i projektet.

Tidigare diskuterade vi [Azure Key Vault](../../key-vault/general/overview.md). Du kan använda Key Vault för att lagra hemligheter som nycklar och lösen ord i stället för att hårdkoda dem. När du använder Key Vault i kombination med hanterade identiteter för Azure-resurser, kan Azure-webbappen komma åt hemliga konfigurations värden enkelt och säkert utan att lagra några hemligheter i din käll kontroll eller konfiguration. Mer information finns i [Hantera hemligheter i dina Server appar med Azure Key Vault](/learn/modules/manage-secrets-with-azure-key-vault/).

### <a name="implement-fail-safe-measures"></a>Implementera fel säkert mått

Programmet måste kunna hantera [fel](/dotnet/standard/exceptions/) som uppstår under körningen på ett konsekvent sätt. Programmet bör fånga alla fel och antingen inte säkert eller stängt.

Du bör också se till att fel loggas med tillräcklig användar kontext för att identifiera misstänkt eller skadlig aktivitet. Loggar bör behållas under tillräckligt lång tid för att tillåta fördröjd kriminal tekniska analys. Loggarna bör ha ett format som enkelt kan användas av en logg hanterings lösning. Se till att aviseringar för fel som är relaterade till säkerhet utlöses. Otillräcklig loggning och övervakning gör det möjligt för angripare att ytterligare attackera system och underhålla beständighet.

### <a name="take-advantage-of-error-and-exception-handling"></a>Dra nytta av fel-och undantags hantering

Implementering av korrekt fel och [undantags hantering](/dotnet/standard/exceptions/best-practices-for-exceptions) är en viktig del av en försvars kod. Fel-och undantags hanteringen är avgörande för att göra ett system tillförlitligt och säkert. Misstag i fel hanteringen kan leda till olika typer av säkerhets problem, till exempel läcka information till angripare och hjälpa angripare att förstå mer om din plattform och design.

Se till att:

- Du hanterar undantag på ett centraliserat sätt för att undvika dubbla [try/catch-block](/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions) i koden.

- Alla oväntade beteenden hanteras i programmet.

- Meddelanden som visas för användare läcker inte viktiga data, men ger tillräckligt med information för att förklara problemet.

- Undantag loggas och att de ger tillräckligt med information för att data utredning eller incident hanterings team ska kunna undersöka.

[Azure Logic Apps](../../logic-apps/logic-apps-overview.md) ger en förstklassig upplevelse för att [hantera fel och undantag](../../logic-apps/logic-apps-exception-handling.md) som orsakas av beroende system. Du kan använda Logic Apps för att skapa arbets flöden för att automatisera uppgifter och processer som integrerar appar, data, system och tjänster i företag och organisationer.

### <a name="use-logging-and-alerting"></a>Använd loggning och aviseringar

[Logga](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1) dina säkerhets problem för säkerhets undersökningar och Utlös aviseringar om problem för att se till att människor vet om problem inom rimlig tid. Aktivera granskning och loggning på alla komponenter. Gransknings loggar ska avbilda användar kontext och identifiera alla viktiga händelser.

Kontrol lera att du inte loggar känsliga data som en användare skickar till din webbplats. Exempel på känsliga data är:

- Användarautentiseringsuppgifter
- Person nummer eller annan identifierings information
- Kreditkorts nummer eller annan finansiell information
- Hälso information
- Privata nycklar eller andra data som kan användas för att dekryptera krypterad information
- System-eller program information som kan användas för att på ett effektivare sätt attackera programmet

Kontrol lera att programmet övervakar användar hanterings händelser, till exempel lyckade och misslyckade användar inloggningar, återställning av lösen ord, lösen ords ändringar, konto utelåsning och användar registrering. Loggning för dessa händelser hjälper dig att identifiera och reagera på potentiellt misstänkt beteende. Du kan också samla in drift data, till exempel vem som har åtkomst till programmet.

## <a name="next-steps"></a>Nästa steg
I följande artiklar rekommenderar vi säkerhets kontroller och aktiviteter som kan hjälpa dig att utveckla och distribuera säkra program.

- [Utveckla säkra program](secure-develop.md)
- [Distribuera säkra program](secure-deploy.md)