---
title: Faktorer som påverkar prestandan för Azure AD Connect
description: Det här dokumentet beskriver hur olika faktorer påverkar Azure AD Connect etablerings motorn. Dessa faktorer hjälper organisationer att planera sin Azure AD Connect-distribution för att kontrol lera att de uppfyller kraven för synkronisering.
services: active-directory
author: billmath
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: hybrid
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2018
ms.reviewer: martincoetzer
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15bcb0f7ca30c343072da396abeac8d08dee03a9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90087017"
---
# <a name="factors-influencing-the-performance-of-azure-ad-connect"></a>Faktorer som påverkar prestandan för Azure AD Connect

Azure AD Connect synkroniserar din Active Directory till Azure AD. Den här servern är en viktig komponent i att flytta dina användar identiteter till molnet. De primära faktorer som påverkar prestandan för en Azure AD Connect är:

| **Design faktor**| **Definition** |
|:-|-|
| Topologi| Distributionen av slut punkter och komponenter som Azure AD Connect måste hantera i nätverket. |
| Skala| Antalet objekt, t. ex. användare, grupper och organisationsenheter, som ska hanteras av Azure AD Connect. |
| Maskinvara| Maskin vara (fysisk eller virtuell) för den Azure AD Connect och beroende prestanda kapaciteten för varje maskin varu komponent, inklusive konfiguration av processor, minne, nätverk och hård disk. |
| Konfiguration| Hur Azure AD Connect bearbetar kataloger och information. |
| Inläsning| Frekvens för objekt ändringar. Belastningarna kan variera mellan en timme, en dag eller en vecka. Beroende på komponent kan du behöva utforma för hög belastning eller genomsnittlig belastning. |

Syftet med det här dokumentet är att beskriva de faktorer som påverkar prestandan för Azure AD Connect etablerings motorn. Stora eller komplexa organisationer (organisationer som håller på att tillhandahålla fler än 100 000 objekt) kan använda rekommendationerna för att optimera sina Azure AD Connect implementering, om de drabbas av eventuella prestanda problem som beskrivs här. De andra komponenterna i Azure AD Connect, till exempel [Azure AD Connect hälsa](how-to-connect-health-agent-install.md) och agenter, omfattas inte här.

> [!IMPORTANT]
> Microsoft stöder inte ändrings-eller drift Azure AD Connect utanför de åtgärder som dokumenteras formellt. Någon av dessa åtgärder kan resultera i ett inkonsekvent eller tillstånd som inte stöds för Azure AD Connect Sync. Därför kan Microsoft inte tillhandahålla teknisk support för sådana distributioner.

## <a name="azure-ad-connect-component-factors"></a>Azure AD Connect komponent faktorer

Följande diagram visar en hög nivå arkitektur för etablerings motorn som ansluter till en enda skog, även om flera skogar stöds. Den här arkitekturen visar hur de olika komponenterna interagerar med varandra.

![Diagram visar hur anslutna kataloger och Azure AD Connect etablerings motorn interagerar, inklusive anslutnings utrymme och metaversum-komponenter i en SQL Database. ](media/plan-connect-performance-factors/AzureADConnentInternal.png)

Etablerings motorn ansluter till varje Active Directory skog och till Azure AD. Processen med att läsa information från varje katalog kallas importera. Export syftar på att uppdatera katalogerna från etablerings motorn. Synkronisering utvärderar reglerna för hur objekten kommer att flöda i etablerings motorn. För en djupare inblick kan du referera till [Azure AD Connect Sync: förstå arkitekturen](./concept-azure-ad-connect-sync-architecture.md).

Azure AD Connect använder följande mellanlagringsplatser, regler och processer för att tillåta synkronisering från Active Directory till Azure AD:

* **Kopplings utrymme (CS)** – objekt från varje ansluten katalog (CD), de faktiska katalogerna mellanlagras här först innan de kan bearbetas av etablerings motorn. Azure AD har sitt eget CS och varje skog som du ansluter till har sin egen CS.
* **Metaversum (MV)** – objekt som behöver synkroniseras skapas här baserat på reglerna för synkronisering. Objekt måste finnas i MV innan de kan fylla objekt och attribut i andra anslutna kataloger. Det finns bara en MV.
* **Regler för synkronisering** – de bestämmer vilka objekt som ska skapas (projiceras) eller ansluts till objekt i MV. Reglerna för synkronisering bestämmer också vilka attributvärden som ska kopieras eller omvandlas till och från katalogerna.
* **Kör profiler** – sammanställer process stegen för att kopiera objekt och deras attributvärden enligt reglerna för synkronisering mellan mellanlagringsområdet och anslutna kataloger.

Det finns olika körnings profiler för att optimera etablerings motorns prestanda. De flesta organisationer använder standard scheman och kör profiler för normala åtgärder, men vissa organisationer kan behöva [ändra schemat](./how-to-connect-sync-feature-scheduler.md) eller utlösa andra körnings profiler för att uppfylla vanliga situationer. Följande körnings profiler är tillgängliga:

### <a name="initial-sync-profile"></a>Inledande profilsynkronisering

Den inledande synkroniseringsklienten är en process för att läsa de anslutna katalogerna, t. ex. en Active Directory skog, för första gången. Den analyserar sedan alla poster i databasen för synkronisering av motor. Den första cykeln kommer att skapa nya objekt i Azure AD och tar extra tid att slutföra om dina Active Directory skogar är stora. Den inledande synkroniseringen innehåller följande steg:

1. Fullständig import för alla anslutningar
2. Fullständig synkronisering för alla anslutningar
3. Exportera på alla kopplingar

### <a name="delta-sync-profile"></a>Ändrings profil för synkronisering

För att optimera synkroniseringsprocessen bearbetar den här körnings profilen ändringarna (skapar, tar bort och uppdaterar) objekt i dina anslutna kataloger, sedan den senaste synkroniseringen. Som standard körs delta-synkronisering var 30: e minut. Organisationer bör sträva efter att hålla tiden det tar under 30 minuter, för att se till att Azure AD är uppdaterad. Om du vill övervaka hälso tillståndet för Azure AD Connect använder du [hälso övervaknings agenten](how-to-connect-health-sync.md) för att se eventuella problem med processen. I Delta-Sync-profilen ingår följande steg:

1. Delta import på alla anslutningar
2. Delta-synkronisering på alla anslutningar
3. Exportera på alla kopplingar

Ett typiskt scenario för att delta i företags organisationer är:

- ~ 1% av objekten har tagits bort
- ~ 1% av objekten skapas
- ~ 5% av objekten har ändrats

Din ändrings takt kan variera beroende på hur ofta din organisation uppdaterar användare i din Active Directory. Till exempel kan högre ändrings takt uppstå i säsongs beroende och minska arbets kraften.

### <a name="full-sync-profile"></a>Fullständig profilsynkronisering

En fullständig synkronisering krävs om du har gjort någon av följande konfigurations ändringar:



- Ökat omfattningen av de objekt eller attribut som ska importeras från de anslutna katalogerna. Till exempel när du lägger till en domän eller ORGANISATIONSENHET i import omfånget.
- Ändringar har gjorts i reglerna för synkronisering. Till exempel när du skapar en ny regel som fyller en användares titel i Azure AD från extension_attribute3 i Active Directory. Den här uppdateringen kräver att etablerings motorn granskar alla befintliga användare igen för att uppdatera sina titlar för att tillämpa ändringen framåt.

Följande åtgärder ingår i en fullständig synkronisering:

1. Fullständig import för alla anslutningar
2. Fullständig/delta-synkronisering på alla anslutningar
3. Exportera på alla kopplingar

> [!NOTE]
> Noggrann planering krävs när du utför Mass uppdateringar av många objekt i Active Directory eller Azure AD. Mass uppdateringar gör att delta-synkroniseringen tar längre tid att importera, eftersom många objekt har ändrats. Långa importer kan inträffa även om Mass uppdateringen inte påverkar synkroniseringsprocessen. Om du till exempel tilldelar licenser till många användare i Azure AD så orsakar det en lång import cykel från Azure AD, men det kommer inte att resultera i några attributändringar i Active Directory.

### <a name="synchronization"></a>Synkronisering

Sync process Runtime har följande prestanda egenskaper:

* Sync är en enkel tråd, vilket innebär att etablerings motorn inte utför någon parallell bearbetning av körnings profiler för anslutna kataloger, objekt eller attribut.
* Import tiden växer linjärt med antalet objekt som synkroniseras. Om till exempel 10 000 objekt tar 10 minuter att importera, tar 20 000-objekt cirka 20 minuter på samma server.
* Export är också linjär.
* Synkroniseringen kommer att växa exponentiellt baserat på antalet objekt med referenser till andra objekt. Grupp medlemskap och kapslade grupper har den främsta prestanda påverkan, eftersom deras medlemmar refererar till användar objekt eller andra grupper. Dessa referenser måste hittas och refereras till faktiska objekt i MV för att synkroniseringen ska kunna slutföras.

### <a name="filtering"></a>Filtrering

Storleken på den Active Directory topologi som du vill importera är den enda faktorn som påverkar prestandan och den övergripande tiden som de interna komponenterna i etablerings motorn tar.

[Filtrering](./how-to-connect-sync-configure-filtering.md) ska användas för att minska objekten till synkroniserat. Det förhindrar att onödiga objekt bearbetas och exporteras till Azure AD. Följande metoder för filtrering är tillgängliga i prioritetsordning:



- **Domänbaserad filtrering** – Använd det här alternativet för att välja vissa domäner som ska synkroniseras till Azure AD. Du måste lägga till och ta bort domäner från Synkroniseringsmotorn när du gör ändringar i den lokala infrastrukturen när du har installerat Azure AD Connect Sync.
- **Organisations enhet (OU)-filtrering** – använder ou för att rikta in sig på specifika objekt i Active Directory domäner för etablering till Azure AD. OU-filtrering är den andra rekommenderade filtrerings mekanismen, eftersom den använder enkla LDAP-frågor för att importera en mindre delmängd objekt från Active Directory.
- **Filtrera attribut per objekt** – använder attributvärden på objekt för att avgöra om ett särskilt objekt i Active Directory har tillhandahållits i Azure AD. Filtrering av attribut är bra för att finjustera filter, när domän-och OU-filtrering inte uppfyller de särskilda filtrerings kraven. Attribut filtrering minskar inte import tiden men kan minska synkroniserings-och export tiderna.
- **Gruppbaserad filtrering** – använder grupp medlemskap för att bestämma om objekt ska tillhandahållas i Azure AD. Gruppbaserad filtrering passar bara för testnings situationer och rekommenderas inte för produktion, på grund av den extra omkostnader som krävs för att kontrol lera grupp medlemskap under synkroniseringsåtgärden.

Många permanenta [från kopplings objekt](concept-azure-ad-connect-sync-architecture.md#relationships-between-staging-objects-and-metaverse-objects) i din Active Directory CS kan orsaka längre synkroniseringsproblem, eftersom etablerings motorn måste utvärdera om varje från kopplings objekt för möjlig anslutning i synkroniseringsprocessen. För att lösa det här problemet bör du överväga någon av följande rekommendationer:



- Placera från kopplings objekt utanför omfånget för import med hjälp av domän-eller OU-filtrering.
- Projicera/koppla objekten till MV och ange [cloudFiltered](how-to-connect-sync-configure-filtering.md#negative-filtering-do-not-sync-these) -attributet lika med sant för att förhindra etablering av dessa objekt i Azure AD CS.

> [!NOTE]
> Användare kan få problem med förvirrande eller program behörigheter, när för många objekt filtreras. I en hybrid Exchange Online-implementering kommer till exempel användare med lokala post lådor att se fler användare i den globala adress listan än användare med post lådor i Exchange Online. I andra fall kan en användare vilja ge åtkomst i en molnbaserad app till en annan användare som inte ingår i omfattningen för den filtrerade uppsättningen objekt.

### <a name="attribute-flows"></a>Attribut flöden

Attribut flöden är processen för att kopiera eller omvandla attributvärden för objekt från en ansluten katalog till en annan ansluten katalog. De har definierats som en del av reglerna för synkronisering. Till exempel när telefonnumret för en användare ändras i Active Directory, kommer telefonnumret i Azure AD att uppdateras. Organisationer kan [ändra attributet flöden](./how-to-connect-sync-change-the-configuration.md) till olika krav för Suite. Vi rekommenderar att du kopierar befintliga attribut flöden innan du ändrar dem.

Enkla omdirigeringar, som att flöda ett attributvärde till ett annat attribut, påverkar inte materialets prestanda. Ett exempel på en omdirigering flödar ett mobiltelefon nummer i Active Directory till arbets telefonnumret i Azure AD.

Omvandling av attributvärden kan ha en prestanda påverkan på synkroniseringsprocessen. Att transformera attributvärden omfattar att ändra, formatera om, sammanfoga eller subtrahera attributens värden.

Organisationer kan förhindra att vissa attribut flödar till Azure AD, men det påverkar inte etablerings motorns prestanda.

> [!NOTE]
> Ta inte bort oönskade attributvärden i dina regler för synkronisering. Du bör hellre inaktivera dem, eftersom borttagna regler återskapas under Azure AD Connect uppgraderingar.

## <a name="azure-ad-connect-dependency-factors"></a>Azure AD Connect beroende faktorer

Prestandan för Azure AD Connect är beroende av prestandan hos de anslutna kataloger som importeras och exporteras till. Till exempel måste storleken på Active Directory importeras eller nätverks fördröjningen till Azure AD-tjänsten. SQL-databasen som etablerings motorn använder påverkar också den övergripande prestandan för den här synkroniseringen.

### <a name="active-directory-factors"></a>Active Directory faktorer

Som tidigare nämnts påverkar antalet objekt som ska importeras prestanda avsevärt. [Maskin vara och krav för Azure AD Connect](how-to-connect-install-prerequisites.md) disponera särskilda maskin varu nivåer baserat på distributionens storlek. Azure AD Connect bara stöd för specifika topologier som beskrivs i [topologier för Azure AD Connect](plan-connect-topologies.md). Det finns inga prestanda optimeringar och rekommendationer för topologier som inte stöds.

Kontrol lera att Azure AD Connect servern uppfyller maskin varu kraven baserat på din Active Directory storlek som du vill importera. Dåliga eller långsamma nätverks anslutningar mellan Azure AD Connect-servern och Active Directory domän kontrol Lanterna kan sakta ned importen.

### <a name="azure-ad-factors"></a>Azure AD-faktorer

Azure AD använder begränsning för att skydda moln tjänsten från DOS-attacker (Denial-of-Service). För närvarande har Azure AD en begränsning på 7 000 skrivningar per 5 minuter (84 000 per timme). Följande åtgärder kan till exempel begränsas:



- Azure AD Connect exportera till Azure AD.
- PowerShell-skript eller program uppdaterar Azure AD direkt även i bakgrunden, till exempel dynamiska grupp medlemskap.
- Användare uppdaterar sina egna identitets poster, till exempel registrering för MFA eller SSPR (självbetjäning för återställning av lösen ord).
- Åtgärder i det grafiska användar gränssnittet.

Planera för distributions-och underhålls aktiviteter för att se till att din Azure AD Connect Sync-cykel inte påverkas av begränsnings gränser. Om du till exempel har en stor anställnings våg där du skapar tusentals användar identiteter kan det leda till uppdateringar av dynamiska grupp medlemskap, licens tilldelningar och registrering av självbetjäning för återställning av lösen ord. Det är bättre att sprida dessa skrivningar över flera timmar eller några dagar.

### <a name="sql-database-factors"></a>SQL Database-faktorer

Storleken på din käll Active Directorys topologi påverkar SQL Database-prestanda. Följ [maskin varu kraven](how-to-connect-install-prerequisites.md) för SQL Server-databasen och Överväg följande rekommendationer:



- Organisationer med fler än 100 000 användare kan minska nätverks fördröjningen genom att placera SQL Database och etablerings motorn på samma server.
- På grund av kraven för både indata och utdata (I/O) av synkroniseringen använder du solid state-hårddiskar (SSD) för etablerings motorns SQL-databas för optimala resultat, om det inte är möjligt, bör du överväga RAID 0-eller RAID 1-konfigurationer.
- Gör inte en fullständig synkronisering förebyggande syfte; Det orsakar onödig omsättning och långsammare svars tider.

## <a name="conclusion"></a>Slutsats

Överväg följande rekommendationer för att optimera prestanda för din Azure AD Connect implementering:



- Använd den [rekommenderade maskin varu konfigurationen](how-to-connect-install-prerequisites.md) baserat på din implementerings storlek för Azure AD Connect-servern.
- När du uppgraderar Azure AD Connect i storskaliga distributioner bör du överväga att använda [metoden flytta migrering](./how-to-upgrade-previous-version.md#swing-migration)för att se till att du har den lägsta stillestånds tiden och bästa tillförlitlighet. 
- Använd SSD för SQL Database för bästa möjliga skriv prestanda.
- Filtrera Active Directory omfattning så att den endast innehåller objekt som måste tillhandahållas i Azure AD med hjälp av domän-, ORGANISATIONSENHETs-eller filtrerings filtrering.
- Om du behöver ändra standardvärdena för attributvärden, kopierar du först regeln och ändrar sedan kopian och inaktiverar den ursprungliga regeln. Kom ihåg att köra en fullständig synkronisering igen.
- Planera tillräckligt med tid för den första fullständiga synkroniseringen av körnings profilen.
- Sträva efter att slutföra den Delta Sync-cykeln på 30 minuter. Om Delta-Sync-profilen inte slutförs inom 30 minuter ändrar du standard frekvensen för synkronisering för att inkludera en fullständig delta-synkronisering.
- Övervaka din [Azure AD Connect Sync-hälsohälsa](how-to-connect-health-agent-install.md) i Azure AD.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).