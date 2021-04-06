---
title: Planera molnet HR Application för att Azure Active Directory användar etablering
description: Den här artikeln beskriver distributions processen för att integrera moln HR-system, till exempel Workday och SuccessFactors, med Azure Active Directory. Genom att integrera Azure AD med ditt moln HR-system resulterar det i ett komplett hanterings system för identitets livs cykeln.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: kenwith
ms.reviewer: arvindha, celested
ms.openlocfilehash: 9c896d4cccf898b8818b4c363c5bc891a8734ca5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99256719"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Planera molnet HR Application för att Azure Active Directory användar etablering

Tidigare har IT-personalen förlitat sig på manuella metoder för att skapa, uppdatera och ta bort medarbetare. De har använt metoder som att ladda upp CSV-filer eller anpassade skript för att synkronisera medarbetar data. Dessa etablerings processer är fel känsliga, oskyddade och svåra att hantera.

Om du vill hantera livs cykeln för identiteter för anställda, leverantörer eller eventualtillgångar, [Azure Active Directory (Azure AD) användar etablerings tjänsten](../app-provisioning/user-provisioning.md) erbjuder integrering med MOLNbaserade HR-program (personal). Exempel på program är Workday eller SuccessFactors.

Azure AD använder denna integrering för att aktivera följande moln-arbets flöden för HR (app):

- **Etablera användare för att Active Directory:** Etablera valda uppsättningar av användare från en Cloud HR-app till en eller flera Active Directory domäner.
- **Tillhandahåll enbart moln användare till Azure AD:** I scenarier där Active Directory inte används etablerar du användare direkt från Cloud HR-appen till Azure AD.
- **Skriv tillbaka till Cloud HR-appen:** Skriv e-postadresserna och attributen för användar namn från Azure AD tillbaka till Cloud HR-appen.

> [!NOTE]
> I den här distributions planen lär du dig hur du distribuerar dina Cloud HR app-arbetsflöden med användar etablering i Azure AD. Information om hur du distribuerar automatisk användar etablering för SaaS-appar (program vara som en tjänst) finns i [Planera en automatisk distribution av användar etablering](./plan-auto-user-provisioning.md).

## <a name="enabled-hr-scenarios"></a>Aktiverade HR-scenarier

Azure AD-tjänsten för användar etablering möjliggör automatisering av följande scenarier för HR-baserade identitets livs cykel hantering:

- **Ny anställds anställning:** När en ny medarbetare läggs till i Cloud HR-appen skapas ett användar konto automatiskt i Active Directory och Azure AD med alternativet att skriva tillbaka attributen e-postadress och användar namn till Cloud HR-appen.
- **Uppdateringar av anställdas attribut och profiler:** När en anställds post, till exempel namn, titel eller chef uppdateras i Cloud HR-appen, uppdateras deras användar konto automatiskt i Active Directory och Azure AD.
- **Anställdas uppsägningar:** När en medarbetare avslutas i Cloud HR-appen inaktive ras deras användar konto automatiskt i Active Directory och Azure AD.
- **Anställdas återställningar:** När en medarbetare återställs i Cloud HR-appen kan deras gamla konto automatiskt återaktiveras eller etableras om till Active Directory och Azure AD.

## <a name="who-is-this-integration-best-suited-for"></a>Vem är den här integreringen bäst lämpad för?

Cloud HR app-integrering med Azure AD-användar etablering passar utmärkt för organisationer som:

- Vill ha en förbyggd, molnbaserad lösning för användar etablering i molnet.
- Kräv direkt användar etablering från Cloud HR-appen till Active Directory eller Azure AD.
- Kräv att användare ska tillhandahållas genom att använda data som hämtats från Cloud HR-appen.
- Kräv anslutning till, flytta och lämna användare för att synkroniseras till en eller flera Active Directory skogar, domäner och organisationsenheter enbart baserat på ändrings information som identifieras i Cloud HR-appen.
- Använd Microsoft 365 för e-post.

## <a name="learn"></a>Learn

Användar etablering skapar en grund för pågående identitets styrning. Det förbättrar kvaliteten på affärs processer som förlitar sig på auktoritativa identitets data.

### <a name="terms"></a>Termer

I den här artikeln används följande villkor:

- **Käll system**: databasen med användare som Azure AD etablerar från. Ett exempel är en Cloud HR-app, till exempel Workday eller SuccessFactors.
- **Mål system**: databasen med användare som Azure AD tillhandahåller. Exempel är Active Directory, Azure AD, Microsoft 365 eller andra SaaS-appar.
- **Kopplingar-process för flyttal**: en term som används för nya anställningar, överföringar och uppsägningar med hjälp av en Cloud HR-app som ett system med poster. Processen slutförs när tjänsten har angett de attribut som krävs för mål systemet.

### <a name="key-benefits"></a>Viktiga fördelar

Den här funktionen för HR-drivna IT-etablering ger följande betydande affärs förmåner:

- **Öka produktiviteten:** Nu kan du automatisera tilldelningen av användar konton och Microsoft 365 licenser och ge åtkomst till nyckel grupper. Automatiserade tilldelningar ger nya anställda omedelbar till gång till sina jobb verktyg och ökar produktiviteten.
- **Hantera risk:** Du kan öka säkerheten genom att automatisera ändringar baserat på medarbetarnas status eller grupp medlemskap med data som flödar in från Cloud HR-appen. Automatisering av ändringar säkerställer att användar identiteter och åtkomst till viktiga appar uppdateras automatiskt när användare övergår till eller lämnar organisationen.
- **Hantera efterlevnad och styrning:** Azure AD stöder interna gransknings loggar för användar etablerings begär Anden som utförs av appar av både käll-och mål system. Med granskning kan du spåra vem som har åtkomst till apparna från en enda skärm.
- **Hantera kostnad:** Automatisk etablering minskar kostnaderna genom att undvika ineffektivitet och mänskligt fel som är kopplat till manuell etablering. Det minskar behovet av anpassade användar etablerings lösningar som skapats med tiden genom att använda äldre och inaktuella plattformar.

### <a name="licensing"></a>Licensiering

Om du vill konfigurera Cloud HR-appen till Azure AD-integrering av användar etablering måste du ha en giltig [Azure AD Premium-licens](https://azure.microsoft.com/pricing/details/active-directory/) och en licens för Cloud HR-appen, till exempel Workday eller SuccessFactors.

Du måste också ha en giltig Azure AD Premium P1 eller högre prenumerations licens för varje användare som kommer att skickas från Cloud HR-appen och tillhandahålls till antingen Active Directory eller Azure AD. Ett felaktigt antal licenser som ägs i Cloud HR-appen kan leda till fel vid användar etablering.

### <a name="prerequisites"></a>Förutsättningar

- Azure AD [hybrid Identity-administratör](../roles/permissions-reference.md#hybrid-identity-administrator)  för att konfigurera Azure AD Connect etablerings agenten.
- Azure AD- [programadministratörs](../roles/permissions-reference.md#application-administrator) roll för att konfigurera etablerings appen i Azure Portal
- En test-och produktions instans av Cloud HR-appen.
- Administratörs behörighet i Cloud HR-appen för att skapa en system integrations användare och göra ändringar för att testa medarbetar data i test syfte.
- För att användar etablering ska kunna Active Directory krävs en server som kör Windows Server 2012 eller senare med .NET 4.7.1 + runtime för att vara värd för Azure AD Connect etablerings agenten
- [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) för synkronisering av användare mellan Active Directory och Azure AD.

### <a name="training-resources"></a>Utbildnings resurser

| **Resurser** | **Länk och beskrivning** |
|:-|:-|
| Video | [Vad är användar etablering i Active Azure-katalogen?](https://youtu.be/_ZjARPpI6NI) |
| | [Så här distribuerar du användar etablering i Active Azure-katalogen](https://youtu.be/pKzyts6kfrw) |
| Självstudier | [Lista över självstudier om hur du integrerar SaaS-appar med Azure AD](../saas-apps/tutorial-list.md) |
| | [Självstudie: Konfigurera arbets dag för automatisk användar etablering](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |
| Vanliga frågor | [Automatiserad användar etablering](../app-provisioning/user-provisioning.md#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [Etablering från Workday till Azure AD](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>Lösningsarkitektur

I följande exempel beskrivs den kompletta lösningen för användar etablerings lösningen för vanliga hybrid miljöer och inkluderar:

- **Auktoritativt HR-dataflöde från Cloud HR-appen till Active Directory.** I det här flödet initieras processen för HR-händelsen (kopplingar – flyttal-Leavers) i Cloud HR App-klienten. Azure AD Provisioning-tjänsten och Azure AD Connect etablerings agenten etablerar användar data från Cloud HR App-klienten i Active Directory. Beroende på händelsen kan det leda till att du skapar, uppdaterar, aktiverar och inaktiverar åtgärder i Active Directory.
- **Synkronisera med Azure AD och skriv tillbaka e-post och användar namn från lokala Active Directory till Cloud HR-appen.** När kontona har uppdaterats i Active Directory synkroniseras de med Azure AD via Azure AD Connect. Attributen e-postadress och användar namn kan skrivas tillbaka till Cloud HR App-klienten.

![Arbets flödes diagram](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>Beskrivning av arbets flöde

Följande viktiga steg visas i diagrammet:  

1. **HR team** utför transaktionerna i Cloud HR App-klienten.
2. **Azure AD Provisioning-tjänsten** kör de schemalagda cyklerna från Cloud HR App-klienten och identifierar ändringar som behöver bearbetas för synkronisering med Active Directory.
3. **Azure AD Provisioning-tjänsten** anropar Azure AD Connect etablerings agenten med en nytto last för begäran som innehåller Active Directory konto skapa, uppdatera, aktivera och inaktivera åtgärder.
4. **Azure AD Connect etablerings agenten** använder ett tjänst konto för att hantera Active Directory konto data.
5. **Azure AD Connect** kör delta- [synkronisering](../hybrid/how-to-connect-sync-whatis.md) för att hämta uppdateringar i Active Directory.
6. **Active Directory** uppdateringar synkroniseras med Azure AD.
7. **Azure AD Provisioning-tjänsten** skriver tillbaka e-postattributet och användar namnet från Azure AD till Cloud HR App-klienten.

## <a name="plan-the-deployment-project"></a>Planera distributions projektet

Överväg organisationens behov medan du fastställer strategin för den här distributionen i din miljö.

### <a name="engage-the-right-stakeholders"></a>Engagera rätt intressenter

När teknik projekt inte fungerar gör de vanligt vis det på grund av felaktiga förväntningar på påverkan, resultat och ansvars områden. För att undvika dessa fall GRO par bör [du se till att du är engagerande rätt intressenter](../fundamentals/active-directory-deployment-plans.md). Se också till att från intressenter roller i projektet är väl förstå. Dokumentera intressenterna och deras ingångs-och Accountabilities.

Ta med en representant från den HR-organisation som kan tillhandahålla indata på befintliga arbets processer och arbets uppgifter för arbets flöde samt jobb data bearbetnings krav.

### <a name="plan-communications"></a>Planera kommunikation

Kommunikationen är nödvändig för att en ny tjänst ska lyckas. Kommunicera proaktivt med dina användare om när och hur deras upplevelse kommer att ändras. Ta reda på hur du kan få support om det uppstår problem.

### <a name="plan-a-pilot"></a>Planera en pilot

Att integrera HR-affärsprocesser och identitets arbets flöden från moln HR-appen till mål systemen kräver en stor mängd data verifiering, data omvandling, rengöring av data och slut punkt till slut punkt innan du kan distribuera lösningen till produktion.

Kör den inledande konfigurationen i en [pilot miljö](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) innan du skalar den till alla användare i produktionen.

## <a name="select-cloud-hr-provisioning-connector-apps"></a>Välj Cloud HR Provisioning Connector-appar

För att under lätta Azure AD etablering av arbets flöden mellan Cloud HR-appen och Active Directory kan du lägga till flera etablerings anslutnings program från Azure AD App-galleriet:

- **Cloud HR-appen för att Active Directory användar etablering**: den här etablerings anslutnings appen underlättar användar konto etablering från Cloud HR-appen till en enda Active Directory domän. Om du har flera domäner kan du lägga till en instans av den här appen från Azure AD App-galleriet för varje Active Directory domän som du behöver etablera till.
- **Cloud HR-appen till Azure AD-användar etablering**: när Azure AD Connect är det verktyg som ska användas för att synkronisera Active Directory användare till Azure AD, kan den här etablerings anslutnings appen användas för att under lätta etableringen av enbart moln användare från molnet HR till en enda Azure AD-klient.
- **Cloud HR app-tillbakaskrivning**: den här etablerings anslutnings appen gör det möjligt att skriva tillbaka användarens e-postadresser från Azure AD till Cloud HR-appen.

Följande bild visar till exempel de Workday Connector-appar som är tillgängliga i Azure AD App-galleriet.

![Azure Active Directory portalens app-Galleri](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flow-chart"></a>Diagram över besluts flöde

Använd följande besluts flödes diagram för att identifiera vilka Cloud HR-etablerings appar som är relevanta för ditt scenario.

![Diagram över besluts flöde](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img3.png)

## <a name="design-the-azure-ad-connect-provisioning-agent-deployment-topology"></a>Utforma topologi för distribution av Azure AD Connect-etablerings agent

Etableringen av integreringen mellan Cloud HR-appen och Active Directory kräver fyra komponenter:

- Cloud HR app-klient
- Provisioning Connector-appen
- Azure AD Connect etablerings agent
- Active Directory-domän

Den Azure AD Connect etablerings agentens distributions topologi beror på antalet Cloud HR app-klienter och Active Directory underordnade domäner som du planerar att integrera. Om du har flera Active Directory domäner beror det på om Active Directorys domänerna är sammanhängande eller [åtskilda](/windows-server/identity/ad-ds/plan/disjoint-namespace).

Välj ett av distributions scenarier baserat på ditt beslut:

- En enda molnbaserad app-klient för HR-> rikta in sig på en eller flera Active Directory underordnade domäner i en betrodd skog
- En enda molnbaserad app-klient för HR-> rikta in sig på flera underordnade domäner i en åtskild Active Directory skog

### <a name="single-cloud-hr-app-tenant---target-single-or-multiple-active-directory-child-domains-in-a-trusted-forest"></a>En enda molnbaserad app-klient för HR-> rikta in sig på en eller flera Active Directory underordnade domäner i en betrodd skog

Vi rekommenderar följande produktions konfiguration:

|Krav|Rekommendation|
|:-|:-|
|Antal Azure AD Connect etablerings agenter som ska distribueras|Två (för hög tillgänglighet och redundans)
|Antal etablerings anslutnings program som ska konfigureras|En app per underordnad domän|
|Server värd för Azure AD Connect etablerings agent|Windows 2012 R2 + med detaljerad information för Active Directory domänkontrollanter</br>Kan samverka med Azure AD Connect tjänst|

![Flöda till lokala agenter](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-active-directory-forest"></a>En enda molnbaserad app-klient för HR-> rikta in sig på flera underordnade domäner i en åtskild Active Directory skog

I det här scenariot ingår etablering av användare från Cloud HR-appen till domäner i åtskilda Active Directory skogar.

Vi rekommenderar följande produktions konfiguration:

|Krav|Rekommendation|
|:-|:-|
|Antal Azure AD Connect etablerings agenter som ska distribueras lokalt|Två per åtskild Active Directory-skog|
|Antal etablerings anslutnings program som ska konfigureras|En app per underordnad domän|
|Server värd för Azure AD Connect etablerings agent|Windows 2012 R2 + med detaljerad information för Active Directory domänkontrollanter</br>Kan samverka med Azure AD Connect tjänst|

![Ett enda moln HR-program klient som är åtskild Active Directory skog](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Azure AD Connect krav för etablerings agenten

Cloud HR-appen för att Active Directory användar etablerings lösningen kräver att du distribuerar en eller flera Azure AD Connect etablerings agenter på servrar som kör Windows 2012 R2 eller senare. Servrarna måste ha minst 4 GB RAM-minne och .NET 4.7.1 + Runtime. Se till att värd servern har nätverks åtkomst till mål Active Directorys domänen.

För att förbereda den lokala miljön registrerar konfigurations guiden för Azure AD Connect etablerings agent agenten med din Azure AD-klient, [öppnar portar](../manage-apps/application-proxy-add-on-premises-application.md#open-ports), [tillåter åtkomst till URL: er](../manage-apps/application-proxy-add-on-premises-application.md#allow-access-to-urls)och stöder [konfiguration av utgående https-proxy](../saas-apps/workday-inbound-tutorial.md#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).

Etablerings agenten använder ett tjänst konto för att kommunicera med Active Directory-domäner. Innan du installerar agenten skapar du ett tjänst konto i Active Directory användare och datorer som uppfyller följande krav:

- Ett lösen ord som inte upphör att gälla
- Delegerad kontroll behörighet att läsa, skapa, ta bort och hantera användar konton

Du kan välja domänkontrollanter som ska hantera etablerings begär Anden. Om du har flera geografiskt distribuerade domänkontrollanter installerar du etablerings agenten på samma plats som de önskade domän kontrol Lanterna. Den här placeringen förbättrar tillförlitligheten och prestandan hos lösningen från slut punkt till slut punkt.

För hög tillgänglighet kan du distribuera mer än en Azure AD Connect etablerings agent. Registrera agenten för att hantera samma uppsättning lokala Active Directory domäner.

## <a name="plan-scoping-filters-and-attribute-mapping"></a>Planera omfångs filter och attributmappning

När du aktiverar etablering från Cloud HR-appen till Active Directory eller Azure AD, styr Azure Portal attributvärdena genom mappning av attribut.

### <a name="define-scoping-filters"></a>Definiera omfångs filter

Använd [omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) för att definiera de attributbaserade regler som avgör vilka användare som ska tillhandahållas från Cloud HR-appen till Active Directory eller Azure AD.

När du initierar anslutnings processen samlar du in följande krav:

- Används Cloud HR-appen för att ta fram både anställda och eventualtillgångar?
- Planerar du att använda Cloud HR-appen till användar etablering i Azure AD för att hantera både anställda och eventualtillgångar?
- Planerar du att bara distribuera Cloud HR-appen till Azure AD-användar etablering för en delmängd av molnet för HR-appar? Ett exempel kan endast vara anställda.

Beroende på dina behov, när du konfigurerar attribut mappningar, kan du ange fältet **käll objekt omfång** för att välja vilka uppsättningar med användare i Cloud HR-appen som ska tillhöra omfånget för etablering till Active Directory. Mer information finns i själv studie kursen om Cloud HR-appen för vanliga omfångs filter.

### <a name="determine-matching-attributes"></a>Bestämma matchande attribut

Med hjälp av etableringen får du möjlighet att matcha befintliga konton mellan käll-och mål systemet. När du integrerar Cloud HR-appen med Azure AD Provisioning-tjänsten kan du [konfigurera mappning av mappar](../app-provisioning/configure-automatic-user-provisioning-portal.md#mappings) för att avgöra vilka användar data som ska flöda från Cloud HR-appen till Active Directory eller Azure AD.

När du initierar anslutnings processen samlar du in följande krav:

- Vad är det unika ID: t i den här moln-HR-appen som används för att identifiera varje användare?
- Hur hanterar du återställningar från ett livs cykel perspektiv för identiteter? Behåller återställningen sina gamla anställnings-ID?
- Bearbetar du framtida anställda och skapar Active Directory konton för dem i förväg?
- Hur hanterar man medarbetare till konverteringen av en identitets livs cykel eller på annat sätt från ett livs cykel perspektiv för identiteter?
- Behåller de konverterade användarna sina gamla Active Directory-konton eller gör de nya?

Beroende på dina krav stöder Azure AD mappning av direkt attribut-till-attribut genom att tillhandahålla konstanta värden eller [skriva uttryck för mappning av attribut](../app-provisioning/functions-for-customizing-application-data.md). Den här flexibiliteten ger dig den bästa kontrollen över vad som är ifyllt i det riktade app-attributet. Du kan använda [Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md) och Graph Explorer för att exportera mappningar och schemat för användar etablerings attribut till en JSON-fil och importera tillbaka dem till Azure AD.

Som standard används attributet i Cloud HR-appen som representerar det unika medarbetar-ID: t som matchande attribut *mappat till det unika attributet i Active Directory.* I app-scenariot för arbets dagar mappas exempelvis attributet **WorkerID** för **workday** till attributet Active Directory **Anställningsnr** .

Du kan ange flera matchande attribut och tilldela matchande prioritet. De utvärderas utifrån matchnings prioritet. Så fort en matchning hittas utvärderas inga ytterligare matchande attribut.

Du kan också [Anpassa mappningar för standardattribut](../app-provisioning/customize-application-attributes.md#understanding-attribute-mapping-types), till exempel ändra eller ta bort befintliga mappningar för attribut. Du kan också skapa nya mappningar för attribut enligt dina affärs behov. Mer information finns i själv studie kursen om Cloud HR-appen (till exempel [Workday](../saas-apps/workday-inbound-tutorial.md#managing-your-configuration)) för en lista över anpassade attribut att mappa.

### <a name="determine-user-account-status"></a>Ange status för användar konto

Som standard mappar Provisioning Connector-appen användar profilens status till användar kontots status i Active Directory eller Azure AD för att avgöra om användar kontot ska aktive ras eller inaktive ras.

Samla in följande krav när du startar Joiners-Leavers processen.

| Process | Krav |
| - | - |
| **Kopplingar** | Hur hanterar du återställningar från ett livs cykel perspektiv för identiteter? Behåller återställningen sina gamla anställnings-ID? |
| | Bearbetar du framtida anställda och skapar Active Directory konton för dem i förväg? Skapas de här kontona i ett aktiverat eller inaktiverat tillstånd? |
| | Hur hanterar man medarbetare till konverteringen av en identitets livs cykel eller på annat sätt från ett livs cykel perspektiv för identiteter? |
| | Har konverterade användare försätter sina gamla Active Directory-konton eller får de nya? |
| **Lämnare** | Hanteras uppsägningar på olika sätt för anställda och eventualtillgångar i Active Directory? |
| | Vilka effektiva datum beaktas vid bearbetning av användar terminering? |
| | Hur påverkas befintliga Active Directory-konton av medarbetar-och jobb versioner? |
| | Hur bearbetar du åtgärden återkalla i Active Directory? Återställnings åtgärder måste hanteras om framtida daterad anställda skapas i Active Directory som en del av anslutnings processen. |

Beroende på dina behov kan du anpassa mappnings logiken med hjälp av [Azure AD-uttryck](../app-provisioning/functions-for-customizing-application-data.md) så att Active Directory-kontot aktive ras eller inaktive ras baserat på en kombination av data punkter.

### <a name="map-cloud-hr-app-to-active-directory-user-attributes"></a>Kartlägg Cloud HR-appen för att Active Directory användarattribut

Varje Cloud HR-app levereras med Cloud HR-appen som standard för att Active Directory mappningar.

Samla in följande krav när du initierar processen Joinr-Movers-process.

| Process | Krav |
| - | - |
| **Kopplingar** | Är processen för att skapa Active Directorys konto manuell, automatiserad eller delvis automatiserad? |
| | Planerar du att sprida anpassade attribut från Cloud HR-appen till Active Directory? |
| **Flytter** | Vilka attribut vill du bearbeta när en flyttnings åtgärd sker i Cloud HR-appen? |
| | Utför du några speciella attributändringar vid tidpunkten för användar uppdateringar? Om ja, ange information. |
| **Lämnare** | Hanteras uppsägningar på olika sätt för anställda och eventualtillgångar i Active Directory? |
| | Vilka effektiva datum beaktas vid bearbetning av användar terminering? |
| | Hur påverkas befintliga Active Directory-konton av medarbetare och jobb konverteringar? |

Beroende på dina krav kan du ändra mappningarna så att de passar dina integrations mål. Mer information finns i själv studie kursen om Cloud HR-appar (till exempel [Workday](../saas-apps/workday-inbound-tutorial.md#part-4-configure-attribute-mappings)) för en lista över anpassade attribut att mappa.

### <a name="generate-a-unique-attribute-value"></a>Generera ett unikt attributvärde

När du startar anslutnings processen kan du behöva generera unika attributvärden när du anger attribut som CN, samAccountName och UPN, som har unika begränsningar.

Azure AD Function- [SelectUniqueValues](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) utvärderar varje regel och kontrollerar sedan värdet som genereras för unikhet i mål systemet. Ett exempel finns i [generera unikt värde för userPrincipalName-attributet (UPN)](../app-provisioning/functions-for-customizing-application-data.md#generate-unique-value-for-userprincipalname-upn-attribute).

> [!NOTE]
> Den här funktionen stöds för närvarande endast för arbets dagar som Active Directory användar etablering. Den kan inte användas med andra etablerings program.

### <a name="configure-active-directory-ou-container-assignment"></a>Konfigurera Active Directory OU Container tilldelning

Det är ett vanligt krav att placera Active Directory användar konton i behållare baserat på affär senheter, platser och avdelningar. När du initierar en flyttnings process, och om det finns en ändring av organisations administratören, kan du behöva flytta användaren från en ORGANISATIONSENHET till en annan i Active Directory.

Använd funktionen [switch ()](../app-provisioning/functions-for-customizing-application-data.md#switch) för att konfigurera affärs logiken för OU-tilldelningen och mappa den till Active Directory attributet **parentDistinguishedName**.

Om du till exempel vill skapa användare i OU baserat på **kommunen** HR kan du använda följande uttryck:

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

Med det här uttrycket, om Orts värdet är Borås, Austin, Seattle eller London, kommer användar kontot att skapas i motsvarande ORGANISATIONSENHET. Om det inte finns någon matchning, skapas kontot i standard ORGANISATIONSENHETen.

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>Planera för lösen ords leverans för nya användar konton

När du startar anslutnings processen måste du ange och leverera ett tillfälligt lösen ord för nya användar konton. Med Azure AD-användar etablering för Azure AD kan du distribuera funktionen för återställning av lösen ord för Azure AD-SSPR ( [självbetjäning](../authentication/tutorial-enable-sspr.md) ) för användaren dag ett.

SSPR är ett enkelt sätt för IT-administratörer att göra det möjligt för användare att återställa sina lösen ord eller låsa upp sina konton. Du kan etablera attributet **Mobile Number** från Cloud HR-appen för att Active Directory och synkronisera det med Azure AD. När attributet **Mobile Number** är i Azure AD kan du aktivera SSPR för användarens konto. Sedan på dag ett kan den nya användaren använda det registrerade och verifierade mobiltelefon numret för autentisering.

## <a name="plan-for-initial-cycle"></a>Planera för första cykeln

När Azure AD Provisioning-tjänsten körs för första gången utför den en [första cykel](../app-provisioning/how-provisioning-works.md#initial-cycle) mot Cloud HR-appen för att skapa en ögonblicks bild av alla användar objekt i Cloud HR-appen. Den tid det tar för inledande cykler är direkt beroende av hur många användare som finns i käll systemet. Den första cykeln för vissa Cloud HR app-klienter med över 100 000 användare kan ta lång tid.

**För stora Cloud HR app-klienter (>30 000-användare)** kör du den första cykeln i stegvisa steg. Starta bara de stegvisa uppdateringarna när du har kontrollerat att rätt attribut är inställda i Active Directory för olika användar etablerings scenarier. Följ ordningen här.

1. Kör bara den första cykeln för en begränsad uppsättning användare genom att ange [omfångs filtret](#plan-scoping-filters-and-attribute-mapping).
2. Verifiera Active Directory konto etablering och attributvärden som angetts för de användare som valts för första körningen. Om resultatet uppfyller dina förväntningar expanderar du omfångs filtret till progressivt innehåller fler användare och kontrollerar resultatet för den andra körningen.

När du är nöjd med resultatet från den första cykeln för test användare startar du de [stegvisa uppdateringarna](../app-provisioning/how-provisioning-works.md#incremental-cycles).

## <a name="plan-testing-and-security"></a>Planera testning och säkerhet

I varje steg i distributionen från inledande pilot genom att aktivera användar etablering, se till att du testar att resultatet är som förväntat och granskar etablerings cyklerna.

### <a name="plan-testing"></a>Planera testning

När du har konfigurerat Cloud HR-appen till användar etablering i Azure AD kan du köra test fall för att kontrol lera om den här lösningen uppfyller organisationens krav.

|Scenarier|Förväntat resultat|
|:-|:-|
|Ny medarbetare anställs i Cloud HR-appen.| -Användar kontot tillhandahålls i Active Directory.</br>– Användaren kan logga in på Active Directory-domän-appar och utföra önskade åtgärder.</br>-Om Azure AD Connect Sync konfigureras skapas även användar kontot i Azure AD.
|Användaren har avbrutits i Cloud HR-appen.|-Användar kontot har inaktiverats i Active Directory.</br>– Användaren kan inte logga in på alla företags program som skyddas av Active Directory.
|Den användar ansvariga organisationen uppdateras i Cloud HR-appen.|Baserat på mappningen av attributen flyttas användar kontot från en ORGANISATIONSENHET till en annan i Active Directory.|
|HR uppdaterar användarens chef i Cloud HR-appen.|Fältet chef i Active Directory uppdateras för att återspegla den nya chefens namn.|
|HR återanställer en medarbetare till en ny roll.|Beteendet beror på hur Cloud HR-appen är konfigurerad att generera anställnings-ID:</br>– Om det gamla medarbetar-ID: t återanvänds för en återställning, aktiverar anslutningen det befintliga Active Directory-kontot för användaren.</br>– Om återställningen får ett nytt anställnings-ID skapar kopplingen ett nytt Active Directory konto för användaren.|
|HR konverterar medarbetaren till en kontrakts arbetare eller vice versa.|Ett nytt Active Directory-konto skapas för den nya personen och det gamla kontot inaktive ras på konverteringens giltighets datum.|

Använd de tidigare resultaten för att avgöra hur du ska överföra den automatiska användar etablerings implementeringen till produktion baserat på dina etablerade tids linjer.

> [!TIP]
> Använd metoder som data minskning och data skrubbning när du uppdaterar test miljön med produktions data för att ta bort eller maskera känsliga personliga data för att följa sekretess-och säkerhets standarder. 

### <a name="plan-security"></a>Planera säkerhet

Det är vanligt att en säkerhets granskning krävs som en del av distributionen av en ny tjänst. Om en säkerhets granskning krävs eller inte har utförts, se de många Azure AD [White-dokument](https://www.microsoft.com/download/details.aspx?id=36391) som ger en översikt över identiteten som en tjänst.

### <a name="plan-rollback"></a>Planera återställning

Den molnbaserade användar etablerings implementeringen för HR kanske inte fungerar som du vill i produktions miljön. I så fall kan följande återställnings steg hjälpa dig att återställa till ett tidigare fungerande tillstånd.

1. Granska [sammanfattnings rapporten för etablering](../app-provisioning/check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal) och [etablerings loggar](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) för att fastställa vilka felaktiga åtgärder som utförts på berörda användare eller grupper. Mer information om etablerings översikts rapporten och loggarna finns i [Hantera Cloud HR app User-etablering](#manage-your-configuration).
2. Det senaste fungerande läget för de användare eller grupper som påverkas kan bestämmas genom etableringen av gransknings loggar eller genom att granska mål systemen (Azure AD eller Active Directory).
3. Arbeta med appens ägare för att uppdatera de användare eller grupper som påverkas direkt i appen med hjälp av senast fungerande tillstånds värden.

## <a name="deploy-the-cloud-hr-app"></a>Distribuera Cloud HR-appen

Välj den Cloud HR-app som passar dina lösnings krav.

**Workday**: om du vill importera profiler från arbets dagar till Active Directory och Azure AD, se [självstudie: Konfigurera arbets dag för automatisk användar etablering](../saas-apps/workday-inbound-tutorial.md#planning-your-deployment). Du kan också skriva tillbaka e-postadressen, användar namnet och telefonnumret till arbets dagen.

**SAP-SuccessFactors**: om du vill importera Worker-profiler från SuccessFactors till Active Directory och Azure AD, se [självstudie: konfigurera SAP SuccessFactors för automatisk användar etablering](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md). Du kan också skriva tillbaka e-postadressen och användar namnet till SuccessFactors.

## <a name="manage-your-configuration"></a>Hantera konfigurationen

Azure AD kan ge ytterligare insikter om din organisations användar etablering och drifts hälsa genom gransknings loggar och-rapporter.

### <a name="gain-insights-from-reports-and-logs"></a>Få insikter från rapporter och loggar

Efter en lyckad [första cykel](../app-provisioning/how-provisioning-works.md#initial-cycle)fortsätter Azure AD Provisioning-tjänsten att köra stegvisa säkerhets uppdateringar på obestämd tid, med intervall som definierats i självstudierna som är särskilda för varje app, tills någon av följande händelser inträffar:

- Tjänsten stoppas manuellt. En ny första cykel utlöses med hjälp av [Azure Portal](https://portal.azure.com/) eller lämpligt [Microsoft Graph API](/graph/api/resources/synchronization-overview) -kommando.
- En ny första cykel utlöses på grund av en ändring av attributens mappningar eller omfångs filter.
- Etablerings processen går i karantän på grund av en hög fel frekvens. Den finns kvar i karantän i mer än fyra veckor, då den inaktive ras automatiskt.

Om du vill granska dessa händelser och alla andra aktiviteter som utförs av etablerings tjänsten, [lär du dig att granska loggar och hämta rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md).

#### <a name="azure-monitor-logs"></a>Azure Monitor-loggar

Alla aktiviteter som utförs av etablerings tjänsten registreras i gransknings loggarna för Azure AD. Du kan skicka Azure AD audit-loggar till Azure Monitor loggar för vidare analys. Med Azure Monitor loggar (även kallat Log Analytics arbets yta) kan du fråga efter händelser, analysera trender och utföra korrelation mellan olika data källor. Titta på den här [videon](https://youtu.be/MP5IaCTwkQg) och lär dig fördelarna med att använda Azure Monitor loggar för Azure AD-loggar i praktiska användar scenarier.

Installera [Log Analytics-vyer för Azure AD-aktivitets loggar](../reports-monitoring/howto-install-use-log-analytics-views.md) för att få åtkomst till [färdiga rapporter](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views) kring etablering av händelser i din miljö.

Mer information finns i [analysera Azure AD-aktivitets loggarna med dina Azure Monitor loggar](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md).

### <a name="manage-personal-data"></a>Hantera personliga data

Azure AD Connect etablerings agenten som är installerad på Windows Server skapar loggar i Windows-händelseloggen som kan innehålla personliga data beroende på din Cloud HR-app för att Active Directory mappningar av attribut. Om du vill följa användarens sekretess krav ställer du in en schemalagd Windows-aktivitet för att rensa händelse loggen och se till att inga data behålls efter 48 timmar.

Azure AD Provisioning-tjänsten genererar inte rapporter, utför analyser eller ger insikter mer än 30 dagar eftersom tjänsten inte lagrar, bearbetar eller behåller några data längre än 30 dagar.

### <a name="troubleshoot"></a>Felsöka

Information om hur du felsöker problem som kan uppstå under etableringen finns i följande artiklar:

- [Problem med att konfigurera användar etablering i ett Azure AD Gallery-program](application-provisioning-config-problem.md)
- [Synkronisera ett attribut från din lokala Active Directory till Azure AD för etablering till ett program](user-provisioning-sync-attributes-for-mapping.md)
- [Problem med att spara administratörsautentiseringsuppgifter när du konfigurerar användar etablering till ett Azure Active Directory Galleri program](./user-provisioning.md)
- [Inga användare tillhandahålls till ett Azure AD Gallery-program](application-provisioning-config-problem-no-users-provisioned.md)
- [Fel uppsättning användare tillhandahålls till ett Azure AD Gallery-program](../manage-apps/add-application-portal-assign-users.md)
- [Konfigurera Windows Loggboken för agent fel sökning](../saas-apps/workday-inbound-tutorial.md#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [Konfigurera Azure Portal gransknings loggar för fel sökning av tjänst](../saas-apps/workday-inbound-tutorial.md#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [Förstå loggar för skapande åtgärder i AD-användarkontot](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-ad-user-account-create-operations)
- [Förstå loggar för uppdaterings åtgärder i Manager](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-manager-update-operations)
- [Lösa vanliga fel som har påträffats](../saas-apps/workday-inbound-tutorial.md#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>Nästa steg

- [Skriver uttryck för mappningar av attribut](functions-for-customizing-application-data.md)
- [Översikt över Azure AD Synchronization API](/graph/api/resources/synchronization-overview)
- [Hoppa över borttagning av användar konton som ingår i omfånget](skip-out-of-scope-deletions.md)
- [Azure AD Connect etablerings agent: versions historik](provisioning-agent-release-version-history.md)