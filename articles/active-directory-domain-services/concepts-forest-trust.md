---
title: Så här fungerar förtroenden för Azure AD Domain Services | Microsoft Docs
description: Lär dig mer om hur skogs förtroende fungerar med Azure AD Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 5c72ab7d085de558ee95f3c602ccc6be6160b322
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620213"
---
# <a name="how-trust-relationships-work-for-resource-forests-in-azure-active-directory-domain-services"></a>Så här fungerar förtroende relationer för resurs skogar i Azure Active Directory Domain Services

Active Directory Domain Services (AD DS) tillhandahåller säkerhet över flera domäner eller skogar genom förtroende relationer mellan domäner och skogar. Innan autentisering kan ske över förtroenden måste Windows först kontrol lera om domänen begärs av en användare, dator eller tjänst har en förtroende relation med domänen för det begär ande kontot.

För att söka efter den här förtroende relationen beräknar Windows-säkerhetssystemet en förtroende väg mellan domänkontrollanten (DC) för den server som tar emot begäran och DOMÄNKONTROLLANTen i domänen för det begärda kontot.

De åtkomst kontroll metoder som tillhandahålls av AD DS och Windows-distribuerad säkerhets modell ger en miljö för driften av domän-och skogs förtroenden. För att dessa förtroenden ska fungera korrekt måste varje resurs eller dator ha en direkt förtroende väg till en DOMÄNKONTROLLANT i domänen där den finns.

Förtroende vägen implementeras av tjänsten Net Logon med en autentiserad RPC-anslutning (Remote Procedure Call) till den betrodda domän utfärdaren. En säker kanal utökar också andra AD DS-domäner genom förtroende relationer mellan domäner. Den här säkra kanalen används för att hämta och verifiera säkerhets information, inklusive säkerhets identifierare (sid) för användare och grupper.

En översikt över hur förtroenden gäller för Azure AD DS finns i avsnittet om [begrepp och funktioner för resurs skogar][create-forest-trust].

Kom igång med att använda förtroenden i Azure AD DS genom att [skapa en hanterad domän som använder skogs förtroenden][tutorial-create-advanced].

## <a name="trust-relationship-flows"></a>Betrodda Relations flöden

Flödet av skyddade kommunikationer över förtroenden fastställer elastiskheten för ett förtroende. Hur du skapar eller konfigurerar ett förtroende bestämmer hur långt kommunikationen ska utsättas inom eller mellan skogar.

Flödet av kommunikation över förtroenden bestäms av förtroendets riktning. Förtroenden kan vara enkelriktade eller dubbelriktade och kan vara transitiva eller icke-transitiva.

Följande diagram visar att alla domäner i *träd 1* och *träd 2* har transitiva förtroende relationer som standard. Därför kan användare i *träd 1* komma åt resurser i domäner i *träd 2* och användare i *träd 1* kan komma åt resurser i *träd 2*, när rätt behörigheter tilldelas till resursen.

![Diagram över förtroende relationer mellan två skogar](./media/concepts-forest-trust/trust-relationships.png)

### <a name="one-way-and-two-way-trusts"></a>Enkelriktade och dubbelriktade förtroenden

Förtroende relationer ger åtkomst till resurser kan vara antingen envägs eller dubbelriktat.

Ett enkelriktat förtroende är en enkelriktad autentiserings-sökväg som skapas mellan två domäner. I ett enkelriktat förtroende mellan *domän a* och *domän B* kan användare i *domän a* komma åt resurser i *domän B*. Användare i *domän B* kan dock inte komma åt resurser i *domän A*.

Vissa enkelriktade förtroenden kan antingen vara icke-transitiva eller transitiva beroende på vilken typ av förtroende som skapas.

I ett dubbelriktat förtroende har *domän a* förtroende för *domän B* och *domän b* förtroende för *domän a*. Den här konfigurationen innebär att autentiseringsbegäranden kan skickas mellan de två domänerna i båda riktningarna. Vissa tvåvägs relationer kan vara icke-transitiva eller transitiva beroende på vilken typ av förtroende som skapas.

Alla domän förtroenden i en AD DS-skog är dubbelriktade, transitiva förtroenden. När en ny underordnad domän skapas, skapas ett dubbelriktat, transitivt förtroende automatiskt mellan den nya underordnade domänen och den överordnade domänen.

### <a name="transitive-and-non-transitive-trusts"></a>Transitiva och icke-transitiva förtroenden

Transitivitet avgör om ett förtroende kan utökas utanför de två domäner som det har skapats med.

* Ett transitivt förtroende kan användas för att utöka förtroende relationer med andra domäner.
* Ett icke-transitivt förtroende kan användas för att neka förtroende relationer med andra domäner.

Varje gång du skapar en ny domän i en skog skapas automatiskt en dubbelriktad, transitiv förtroende relation mellan den nya domänen och dess överordnade domän. Om underordnade domäner läggs till i den nya domänen, flödar förtroende vägen uppåt genom domänhierarkin som utökar den inledande förtroende vägen som skapas mellan den nya domänen och dess överordnade domän. Transitiva förtroende relationer flödar uppåt genom ett domän träd när det skapas, vilket skapar transitiva förtroenden mellan alla domäner i domän trädet.

Autentiseringsbegäranden följer dessa förtroende vägar, så konton från vilken domän som helst i skogen kan autentiseras av en annan domän i skogen. Med en enda inloggnings process kan konton med rätt behörigheter komma åt resurser i valfri domän i skogen.

## <a name="forest-trusts"></a>Skogsförtroenden

Skogs förtroenden hjälper dig att hantera en segmenterad AD DS-infrastruktur och har stöd för åtkomst till resurser och andra objekt i flera skogar. Skogs förtroenden är användbara för tjänste leverantörer, företag som betjänar fusioner eller förvärv, gemensamma företags extra nät och företag som söker en lösning för administrativ självständighet.

Med skogs förtroenden kan du länka två olika skogar för att bilda en enkelriktad eller dubbelriktad transitiv förtroende relation. Ett skogs förtroende gör att administratörer kan ansluta två AD DS-skogar med en enda förtroende relation för att ge en sömlös autentiserings-och auktoriserings upplevelse i skogarna.

Ett skogs förtroende kan bara skapas mellan en skogs rots domän i en skog och en skogs rots domän i en annan skog. Skogs förtroenden kan bara skapas mellan två skogar och kan inte implicit utökas till en tredje skog. Detta innebär att om ett skogs förtroende skapas mellan *skog 1* och *skog 2* och ett annat skogs förtroende skapas mellan *skog 2* och *skog 3*, har *skog 1* inte ett implicit förtroende med *skog 3*.

I följande diagram visas två separata skogs förtroende relationer mellan tre AD DS-skogar i en enda organisation.

![Diagram över skogs förtroende relationer inom en enskild organisation](./media/concepts-forest-trust/forest-trusts-diagram.png)

Den här exempel konfigurationen ger följande åtkomst:

* Användare i *skog 2* kan komma åt resurser i valfri domän i antingen *skog 1* eller *skog 3*
* Användare i *skog 3* kan komma åt resurser i vilken domän som helst i *skog 2*
* Användare i *skog 1* kan komma åt resurser i vilken domän som helst i *skog 2*

Den här konfigurationen tillåter inte användare i *skog 1* att komma åt resurser i *skog 3* eller vice versa. För att tillåta användare i både *skog 1* och *skog 3* att dela resurser, måste ett dubbelriktat transitivt förtroende skapas mellan de två skogarna.

Om ett enkelriktat skogs förtroende skapas mellan två skogar kan medlemmar i den betrodda skogen använda resurser som finns i skogen med förtroende. Förtroendet fungerar dock bara i en riktning.

Till exempel skapas ett enkelriktat skogs förtroende mellan *skog 1* (den betrodda skogen) och *skog 2* (skogen med förtroende):

* Medlemmar i *skog 1* kan komma åt resurser som finns i *skog 2*.
* Medlemmar i *skog 2* kan inte komma åt resurser som finns i *skog 1* med samma förtroende.

> [!IMPORTANT]
> Azure AD Domain Services resurs skogen stöder endast ett enkelriktat skogs förtroende till lokala Active Directory.

### <a name="forest-trust-requirements"></a>Krav för skogs förtroende

Innan du kan skapa ett skogs förtroende måste du kontrol lera att du har rätt Domain Name System (DNS)-infrastruktur på plats. Skogs förtroenden kan bara skapas när någon av följande DNS-konfigurationer är tillgänglig:

* En enda DNS-rotserver är rot-DNS-servern för båda skogs-DNS-namnområden – rot zonen innehåller delegeringar för var och en av DNS-namnområdena och rot tipsen för alla DNS-servrar inkluderar rot-DNS-servern.
* Om det inte finns någon delad rot-DNS-server och rot-DNS-servrarna i varje skogs DNS-namnrymd använder DNS-vidarebefordrare för varje DNS-namnrymd för att dirigera frågor om namn i det andra namn området.

    > [!IMPORTANT]
    > Azure AD Domain Services resurs skogen måste använda den här DNS-konfigurationen. Värdbaserade DNS-namnrymder än DNS-namnrymden för resurs skogen är inte en funktion i Azure AD Domain Services. Villkorliga vidarebefordrare är rätt konfiguration.

* När det inte finns någon delad rot-DNS-server och rot-DNS-servrarna i varje skogs DNS-namnrymd använder DNS-sekundära zoner i varje DNS-namnrymd för att dirigera frågor om namn i det andra namn området.

Om du vill skapa ett skogs förtroende måste du vara medlem i gruppen domän administratörer (i skogs rots domänen) eller gruppen företags administratörer i Active Directory. Varje förtroende tilldelas ett lösen ord som administratörerna i båda skogarna måste känna till. Medlemmar i företags administratörer i båda skogarna kan skapa förtroenden i båda skogarna samtidigt, och i det här scenariot genereras ett lösen ord som är kryptografiskt slumpmässigt automatiskt och skrivs för båda skogarna.

Det utgående skogs förtroendet för Azure AD Domain Services skapas i Azure Portal. Du skapar inte förtroendet manuellt med den hanterade domänen. Det inkommande skogs förtroendet måste konfigureras av en användare med privilegier som tidigare antecknas i den lokala Active Directory.

## <a name="trust-processes-and-interactions"></a>Förtroende processer och interaktioner

Många transaktioner mellan domäner och mellan skogar är beroende av domän-eller skogs förtroenden för att kunna slutföra olika uppgifter. I det här avsnittet beskrivs de processer och interaktioner som inträffar när resurser nås över förtroenden och referenser för autentisering utvärderas.

### <a name="overview-of-authentication-referral-processing"></a>Översikt över bearbetning av autentiserings referenser

När en begäran om autentisering refereras till en domän, måste domänkontrollanten i domänen avgöra om det finns en förtroende relation med domänen som begäran kommer från. Förtroendets riktning och huruvida förtroendet är transitivt eller icke-transitivt måste också bestämmas innan det autentiserar användaren för att komma åt resurser i domänen. Autentiseringsprocessen som sker mellan betrodda domäner varierar beroende på vilket autentiseringsprotokoll som används. Kerberos V5-och NTLM-protokoll bearbetar referenser för autentisering till en domän på olika sätt

### <a name="kerberos-v5-referral-processing"></a>Kerberos V5 hänvisnings bearbetning

Autentiseringsprotokollet Kerberos V5 är beroende av tjänsten Net Logon på domänkontrollanter för klientautentisering och autentiseringsinformation. Kerberos-protokollet ansluter till en online Key Distribution Center (KDC) och Active Directorys konto Arkiv för sessionsbiljetter.

Kerberos-protokollet använder också förtroenden för skogar som beviljats av skogar (TGS) och för att validera certifikat för Pac (Privileged Attribute Certificate) över en säker kanal. Kerberos-protokollet utför endast autentisering mellan domäner med Kerberos-sfärer för operativ system som inte är Windows-märke, till exempel en MIT Kerberos-sfär och behöver inte samverka med tjänsten Net Logon.

Om klienten använder Kerberos V5 för autentisering begär den en biljett till servern i mål domänen från en domänkontrollant i sin konto domän. Kerberos KDC fungerar som en betrodd mellanhand mellan klienten och servern och innehåller en sessionsnyckel som gör det möjligt för de två parterna att autentisera varandra. Om mål domänen skiljer sig från den aktuella domänen, följer KDC en logisk process för att avgöra om en autentiseringsbegäran kan refereras:

1. Är den aktuella domänen betrodd direkt av domänen på den server som begärs?
    * Om ja, skicka klienten en hänvisning till den begärda domänen.
    * Om nej, gå till nästa steg.

2. Finns det en transitiv förtroende relation mellan den aktuella domänen och nästa domän på förtroende vägen?
    * Om ja, skicka klienten en hänvisning till nästa domän på förtroende Sök vägen.
    * Om nej, skicka ett meddelande om nekad inloggning för klienten.

### <a name="ntlm-referral-processing"></a>Bearbetning av NTLM-referenser

NTLM-autentiseringsprotokollet är beroende av tjänsten Net Logon på domänkontrollanter för klientautentisering och autentiseringsinformation. Det här protokollet autentiserar klienter som inte använder Kerberos-autentisering. NTLM använder förtroenden för att skicka autentiseringsbegäranden mellan domäner.

Om klienten använder NTLM för autentisering, går den inledande begäran om autentisering direkt från klienten till resurs servern i mål domänen. Den här servern skapar en utmaning som klienten svarar på. Servern skickar sedan användarens svar till en domänkontrollant i sin dator konto domän. Den här domänkontrollanten kontrollerar användar kontot mot databasen med säkerhets konton.

Om kontot inte finns i databasen, avgör domänkontrollanten om den utför direktautentisering, vidarebefordrar begäran eller neka begäran med hjälp av följande logik:

1. Har den aktuella domänen en direkt förtroende relation med användarens domän?
    * Om ja, skickar domänkontrollanten autentiseringsuppgifterna för klienten till en domänkontrollant i användarens domän för direktautentisering.
    * Om nej, gå till nästa steg.

2. Har den aktuella domänen en transitiv förtroende relation med användarens domän?
    * Om ja, skickar du autentiseringsbegäran till nästa domän i förtroende Sök vägen. Den här domänkontrollanten upprepar processen genom att kontrol lera användarens autentiseringsuppgifter mot en egen databas för säkerhets konton.
    * Om nej, skicka klienten ett meddelande om nekad inloggning.

### <a name="kerberos-based-processing-of-authentication-requests-over-forest-trusts"></a>Kerberos-baserad bearbetning av autentiseringsbegäranden över skogs förtroenden

När två skogar är anslutna via ett skogs förtroende kan autentiseringsbegäranden som görs med hjälp av Kerberos V5-eller NTLM-protokollen dirigeras mellan skogar för att ge åtkomst till resurser i båda skogarna.

När ett skogs förtroende först upprättas samlar varje skog alla betrodda namn områden i sin partner skog och lagrar informationen i ett [betrott domän objekt](#trusted-domain-object). Betrodda namn områden inkluderar domän träds namn, User Principal Name (UPN) suffix, SPN-suffix (Service Principal Name) och säkerhets-ID: n (SID) som används i den andra skogen. TDO-objekt replikeras till den globala katalogen.

Innan autentiseringsprotokollen kan följa sökvägen till skogs förtroendet, måste tjänstens huvud namn (SPN) för resurs datorn matchas till en plats i den andra skogen. Ett SPN kan vara något av följande namn:

* DNS-namnet för en värd.
* DNS-namnet för en domän.
* Det unika namnet för ett objekt i tjänst anslutnings punkten.

När en arbets station i en skog försöker komma åt data på en resurs dator i en annan skog kontaktar Kerberos-autentiseringsprocessen domänkontrollanten för en tjänst biljett till resurs datorns SPN. När domänkontrollanten frågar den globala katalogen och bestämmer att SPN inte finns i samma skog som domänkontrollanten skickar domänkontrollanten en hänvisning till den överordnade domänen tillbaka till arbets stationen. Vid det här tillfället frågar arbets stationen den överordnade domänen för tjänst biljetten och fortsätter att följa referens kedjan tills den når den domän där resursen finns.

Följande diagram och steg innehåller en detaljerad beskrivning av den Kerberos-autentiseringsprocess som används när datorer som kör Windows försöker få åtkomst till resurser från en dator som finns i en annan skog.

![Diagram över Kerberos-processen över ett skogs förtroende](media/concepts-forest-trust/kerberos-over-forest-trust-process-diagram.png)

1. *Användare1* loggar in på *Arbetsstation1* med autentiseringsuppgifter från *Europe.tailspintoys.com* -domänen. Användaren försöker sedan få åtkomst till en delad resurs på *FileServer1* som finns i *USA.wingtiptoys.com* -skogen.

2. *Arbetsstation1* kontaktar Kerberos KDC på en domänkontrollant i sin domän, *ChildDC1* och begär en tjänst biljett för *FileServer1* SPN.

3. *ChildDC1* hittar inte SPN i sin domän databas och frågar den globala katalogen för att se om några domäner i *tailspintoys.com* -skogen innehåller detta SPN. Eftersom en global katalog är begränsad till sin egen skog, hittas inte SPN.

    Den globala katalogen kontrollerar sedan sin databas för information om alla skogs förtroenden som har upprättats med sin skog. Om den hittas jämförs de namnsuffix som anges i skogs förtroendets betrodda domän objekt (TDO) till suffixet för mål-SPN för att hitta en matchning. När en matchning hittas ger den globala katalogen ett routningsgränssnitt tillbaka till *ChildDC1*.

    Med hjälp av rottips kan begär Anden direkt autentiseras mot mål skogen. Tips används bara när alla traditionella autentiseringsmetoder, till exempel lokal domänkontrollant och global katalog, inte kan hitta ett SPN.

4. *ChildDC1* skickar en hänvisning för dess överordnade domän tillbaka till *Arbetsstation1*.

5. *Arbetsstation1* kontaktar en domänkontrollant i *ForestRootDC1* (dess överordnade domän) för en hänvisning till en domänkontrollant (*ForestRootDC2*) i skogs rots domänen för *wingtiptoys.com* -skogen.

6. *Arbetsstation1* kontaktar *ForestRootDC2* i *wingtiptoys.com* -skogen för en tjänst biljett till den begärda tjänsten.

7. *ForestRootDC2* kontaktar sin globala katalog för att hitta SPN och den globala katalogen hittar en matchning för SPN och skickar tillbaka den till *ForestRootDC2*.

8. *ForestRootDC2* skickar sedan hänvisningen till *USA.wingtiptoys.com* tillbaka till *Arbetsstation1*.

9. *Arbetsstation1* kontaktar KDC på *ChildDC2* och förhandlar om biljetten för *Användare1* för att få åtkomst till *FileServer1*.

10. När *Arbetsstation1* har en tjänst biljett skickar den tjänst biljetten till *FileServer1*, som läser säkerhetsautentiseringsuppgifterna för *Användare1* och skapar en åtkomsttoken enligt detta.

## <a name="trusted-domain-object"></a>Betrott domän objekt

Varje domän eller skogs förtroende inom en organisation representeras av ett betrott domän objekt (TDO) som lagras i *system* behållaren i domänen.

### <a name="tdo-contents"></a>TDO-innehåll

Den information som finns i ett TDO varierar beroende på om ett TDO har skapats av ett domän förtroende eller av ett skogs förtroende.

När ett domän förtroende skapas visas attribut som DNS-domännamnet, domän-SID, förtroende typ, förtroendets transitivitet och motsvarande domän namn i det BETRODDA domän namnet. Skogs förtroende TDOs lagrar ytterligare attribut för att identifiera alla betrodda namn områden från partner skogen. Dessa attribut inkluderar domän träds namn, User Principal Name (UPN) suffix, SPN-suffix (Service Principal Name) och säkerhets-ID: n (SID).

Eftersom förtroenden lagras i Active Directory som TDOs, har alla domäner i en skog kunskap om de förtroende relationer som finns på plats i skogen. När två eller flera skogar sammanställs tillsammans via skogs förtroenden, har skogs rot domänerna i varje skog kunskaper om de förtroende relationer som finns på plats i alla domäner i betrodda skogar.

### <a name="tdo-password-changes"></a>Lösen ords ändringar i TDO

Båda domänerna i en förtroende relation delar ett lösen ord, som lagras i TDO-objektet i Active Directory. Som en del av konto underhålls processen ändrar var 30: e dag som den domän som har förtroende för domänkontrollanten det lösen ord som lagras i filen TDO. Eftersom alla dubbelriktade förtroenden faktiskt är 2 1-vägs förtroenden i motsatt riktning sker processen två gånger för dubbelriktade förtroenden.

Ett förtroende har en betrodd och en betrodd sida. På den betrodda sidan kan en skrivbar domänkontrollant användas för processen. På sidan förtroende utför PDC-emulatorn lösen ords ändringen.

Om du vill ändra ett lösen ord Slutför domän kontrol Lanterna följande process:

1. PDC-emulatorn (primär domänkontrollant) i domänen med förtroende skapar ett nytt lösen ord. En domänkontrollant i den betrodda domänen initierar aldrig lösen ords ändringen. Den initieras alltid av den förlitande domänkontrollantens PDC-emulator.

2. PDC-emulatorn i domänen med förtroende anger fältet *OldPassword* för TDO-objektet till det aktuella *NewPassword* -fältet.

3. PDC-emulatorn i domänen med förtroende anger fältet *NewPassword* för TDO-objektet till det nya lösen ordet. Att behålla en kopia av det tidigare lösen ordet gör det möjligt att återställa till det gamla lösen ordet om domänkontrollanten i den betrodda domänen inte kan ta emot ändringen, eller om ändringen inte replikeras innan en begäran görs som använder det nya förtroende lösen ordet.

4. PDC-emulatorn i domänen med förtroende gör ett fjärran rop till en domänkontrollant i den betrodda domänen som ber det att ange lösen ordet för förtroende kontot till det nya lösen ordet.

5. Domänkontrollanten i den betrodda domänen ändrar förtroende lösen ordet till det nya lösen ordet.

6. På varje sida om förtroendet replikeras uppdateringarna till de andra domän kontrol Lanterna i domänen. I domänen med förtroende utlöser ändringen en brådskande replikering av det betrodda domän-objektet.

Lösen ordet har nu ändrats på båda domän kontrol Lanterna. Normal replikering distribuerar TDO-objekten till de andra domän kontrol Lanterna i domänen. Det är dock möjligt att domänkontrollanten i domänen med förtroende kan ändra lösen ordet utan att en domänkontrollant har uppdaterats i den betrodda domänen. Det här scenariot kan bero på att en säker kanal, som krävs för att bearbeta lösen ords ändringen, inte kunde upprättas. Det är också möjligt att domänkontrollanten i den betrodda domänen kan vara otillgänglig vid en viss tidpunkt under processen och kanske inte får det uppdaterade lösen ordet.

För att hantera situationer där lösen ords ändringen inte har kommunicerats, ändrar domänkontrollanten i domänen med förtroende aldrig det nya lösen ordet om det inte har autentiserats (konfigurerat en säker kanal) med det nya lösen ordet. Detta är orsaken till att både gamla och nya lösen ord behålls i domänen TDO i domänen med förtroende.

En lösen ords ändring slutförs inte förrän autentiseringen med lösen ordet lyckades. Det gamla, lagrade lösen ordet kan användas över den skyddade kanalen tills domänkontrollanten i den betrodda domänen får det nya lösen ordet, vilket aktiverar oavbruten tjänst.

Om autentisering med det nya lösen ordet Miss lyckas eftersom lösen ordet är ogiltigt försöker domänkontrollanten som är betrodd autentisera med det gamla lösen ordet. Om den autentiseras med det gamla lösen ordet återupptas processen för lösen ords ändring inom 15 minuter.

Förtroende lösen ords uppdateringar måste replikeras till domän kontrol Lanterna på båda sidor om förtroende inom 30 dagar. Om lösen ordet för förtroendet ändras efter 30 dagar och en domänkontrollant endast har lösen ordet för N-2, kan det inte använda förtroendet från den betrodda sidan och kan inte skapa en säker kanal på den betrodda sidan.

## <a name="network-ports-used-by-trusts"></a>Nätverks portar som används av förtroenden

Eftersom förtroenden måste distribueras över olika nätverks gränser kan de behöva omfatta en eller flera brand väggar. I så fall kan du antingen tunnel förtroende trafik i en brand vägg eller öppna vissa portar i brand väggen för att tillåta att trafiken passerar.

> [!IMPORTANT]
> Active Directory Domain Services stöder inte begränsning av Active Directory RPC-trafik till vissa portar.

Läs avsnittet **Windows Server 2008 och senare versioner** av Microsoft Support artikeln [så här konfigurerar du en brand vägg för Active Directory domäner och förtroenden](https://support.microsoft.com/help/179442/how-to-configure-a-firewall-for-domains-and-trusts) för att lära dig om de portar som behövs för ett skogs förtroende.

## <a name="supporting-services-and-tools"></a>Stöd tjänster och verktyg

Vissa ytterligare funktioner och hanterings verktyg används för att stödja förtroenden och autentiseringen.

### <a name="net-logon"></a>Net Logon

Tjänsten Net Logon underhåller en säker kanal från en Windows-baserad dator till en DOMÄNKONTROLLANT. Den används också i följande förtroende-relaterade processer:

* Förtroende konfiguration och hantering-Net Logon hjälper till att upprätthålla förtroende lösen ord, samla in förtroende information och verifiera förtroenden genom att interagera med LSA-processen och TDO.

    För skogs förtroenden innehåller förtroende informationen den skogs förtroende information (*FTInfo*) som innehåller en uppsättning namn rymder som en betrodd skog hävdar att de ska hantera, samt kommenteras med ett fält som anger om varje anspråk är betrott av skogen med förtroende.

* Autentisering – tillhandahåller användarautentiseringsuppgifter via en skyddad kanal till en domänkontrollant och returnerar användarens domän-sid och användar rättigheter.

* Plats för domänkontrollant – hjälper till med att söka efter eller hitta domänkontrollanter i en domän eller mellan domäner.

* Direkt verifiering – autentiseringsuppgifter för användare i andra domäner bearbetas av Net Logon. När en domän som är betrodd måste verifiera identiteten för en användare, skickas användarens autentiseringsuppgifter via net logon till den betrodda domänen för verifiering.

* Verifiering av Privilege Attribute Certificate (PAC) – när en server som använder Kerberos-protokollet för autentisering måste verifiera PAC i en tjänst biljett skickas PAC via den säkra kanalen till domänkontrollanten för verifiering.

### <a name="local-security-authority"></a>Lokal säkerhetskontroll

Den lokala säkerhets kontrollen (LSA) är ett skyddat under system som underhåller information om alla aspekter av lokal säkerhet i ett system. LSA är gemensamt känt som lokal säkerhets princip och tillhandahåller olika tjänster för översättning mellan namn och identifierare.

Säkerhets under systemet i LSA tillhandahåller tjänster i både kernelläge och användarläge för att verifiera åtkomst till objekt, kontroll av användar behörigheter och generering av gransknings meddelanden. LSA ansvarar för att kontrol lera giltigheten för alla sessionsbiljetter som presenteras av tjänster i betrodda eller obetrodda domäner.

### <a name="management-tools"></a>Hanteringsverktyg

Administratörer kan använda *Active Directory domäner och förtroenden*, *netdom* och *Nltest* för att visa, skapa, ta bort eller ändra förtroenden.

* *Active Directory domäner och förtroenden* är Microsoft Management Console (MMC) som används för att administrera domän förtroenden, funktions nivåer för domän och skog och User Principal Name suffix.
* Kommando rads verktygen *netdom* och *Nltest* kan användas för att hitta, Visa, skapa och hantera förtroenden. Dessa verktyg kommunicerar direkt med LSA-utfärdaren på en domänkontrollant.

## <a name="next-steps"></a>Nästa steg

Mer information om resurs skogar finns i [Hur fungerar skogs förtroenden i Azure AD DS?][concepts-trust]

För att komma igång med att skapa en hanterad domän med en resurs skog, se [skapa och konfigurera en Azure AD DS-hanterad domän][tutorial-create-advanced]. Du kan sedan [skapa ett utgående skogs förtroende till en lokal domän][create-forest-trust].

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
