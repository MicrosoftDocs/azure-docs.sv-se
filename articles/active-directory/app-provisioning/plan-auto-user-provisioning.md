---
title: Planera en distribution av automatisk användar etablering för Azure Active Directory
description: Vägledning för att planera och köra automatisk användar etablering
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 12/31/2020
ms.author: kenwith
ms.reviewer: arvindha, celested
ms.openlocfilehash: 9f2b5bf1195682b200b5840459194506e29bcdc6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101645096"
---
# <a name="plan-an-automatic-user-provisioning-deployment"></a>Planera en distribution med automatisk användaretablering

Många organisationer förlitar sig på SaaS-program (program vara som en tjänst) som ServiceNow, Zscaler och slack för slutanvändarens produktivitet. Den tidigare IT-personalen har förlitat sig på manuella etablerings metoder, till exempel att ladda upp CSV-filer eller använda anpassade skript för att hantera användar identiteter på ett säkert sätt i varje SaaS-program. De här processerna är fel känsliga, oskyddade och svåra att hantera.

Azure Active Directory (Azure AD) automatisk användar etablering fören klar processen genom att på ett säkert sätt automatisera skapandet, underhåll och borttagning av användar identiteter i SaaS program baserat på affärs regler. Med den här automationen kan du effektivt skala dina identitets hanterings system både i molnet och i hybrid miljöer när du utökar deras beroende av molnbaserade lösningar.

Se [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md) för att bättre förstå funktionerna.

## <a name="learn"></a>Learn

Användar etablering skapar en grund för pågående identitets styrning och förbättrar kvaliteten på affärs processer som förlitar sig på auktoritativa identitets data.

### <a name="key-benefits"></a>Viktiga fördelar

De främsta fördelarna med att aktivera automatisk användar etablering är:

* **Ökad produktivitet**. Du kan hantera användar identiteter i SaaS-program med ett enda hanterings gränssnitt för användar etablering. Det här gränssnittet har en enda uppsättning etablerings principer.

* **Hantera risker**. Du kan öka säkerheten genom att automatisera ändringar baserat på medarbetarnas status eller grupp medlemskap som definierar roller och/eller åtkomst.

* **Hantera efterlevnad och styrning**. Azure AD har stöd för interna gransknings loggar för varje användar etablerings förfrågan. Begär Anden körs både i käll-och mål systemen. På så sätt kan du spåra vem som har åtkomst till program från en enda skärm.

* **Minska kostnaderna**. Automatisk användar etablering minskar kostnaderna genom att undvika ineffektivitet och mänskligt fel som är kopplat till manuell etablering. Det minskar behovet av anpassade användar etablerings lösningar, skript och gransknings loggar.

### <a name="licensing"></a>Licensiering

Azure AD tillhandahåller självbetjänings integrering av alla program med hjälp av mallar som finns på menyn i program galleriet. En fullständig lista över licens krav finns på [sidan om Azure AD-licensiering](https://azure.microsoft.com/pricing/details/active-directory/).

#### <a name="application-licensing"></a>Program licensiering

Du behöver rätt licenser för de program som du vill etablera automatiskt. Diskutera med program ägare om de användare som har tilldelats programmet har rätt licenser för sina program roller. Om Azure AD hanterar automatisk etablering baserat på roller måste rollerna som tilldelats i Azure AD anpassas till program licenser. Felaktiga licenser som ägs i programmet kan leda till fel vid etablering/uppdatering av en användare.

### <a name="terms"></a>Termer

I den här artikeln används följande villkor:

* CRUD-åtgärder som vidtas för användar konton: skapa, läsa, uppdatera och ta bort.

* Enkel inloggning (SSO) – möjligheten för en användare att logga in en gång och få åtkomst till alla SSO-aktiverade program. I kontexten för användar etablering är SSO ett resultat av användare som har ett enda konto för åtkomst till alla system som använder automatisk användar etablering.

* Käll system – databasen med användare som Azure AD tillhandahåller från. Azure AD är käll systemet för de flesta pre-integrerade etablerings anslutningar. Det finns dock vissa undantag för moln program som SAP, Workday och AWS. Se till exempel [användar etablering från Workday till AD](../saas-apps/workday-inbound-tutorial.md).

* Mål system – databasen med användare som Azure AD tillhandahåller. Mål systemet är vanligt vis ett SaaS-program, till exempel ServiceNow, Zscaler och slack. Mål systemet kan också vara ett lokalt system, till exempel AD.

* [System för scim (Cross-Domain Identity Management)](https://aka.ms/scimoverview) – en öppen standard som möjliggör automatisering av användar etablering. SCIM kommunicerar användar identitets data mellan identitets leverantörer som Microsoft och tjänst leverantörer som Salesforce eller andra SaaS-appar som kräver information om användar identitet.

### <a name="training-resources"></a>Utbildnings resurser

| Resurser| Länk och beskrivning |
| - | - |
| Webbseminarier på begäran| [Hantera dina företags program med Azure AD](https://info.microsoft.com/CO-AZUREPLAT-WBNR-FY18-03Mar-06-ManageYourEnterpriseApplicationsOption1-MCW0004438_02OnDemandRegistration-ForminBody.html)<br>Lär dig hur Azure AD kan hjälpa dig att få SSO till dina företags SaaS-program och bästa praxis för att kontrol lera åtkomst. |
| Videoklipp| [Vad är användar etablering i Active Azure-katalogen?](https://youtu.be/_ZjARPpI6NI) <br> [Hur distribuerar jag användar etablering i Active Azure-katalogen?](https://youtu.be/pKzyts6kfrw) <br> [Integrera Salesforce med Azure AD: så här automatiserar du användar etablering](https://azure.microsoft.com/resources/videos/integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning/) |
| Onlinekurser| SkillUp online:  [hantera identiteter](https://skillup.online/courses/course-v1:Microsoft+AZ-100.5+2018_T3/about) <br> Lär dig hur du integrerar Azure AD med många SaaS-program och skyddar användarnas åtkomst till dessa program. |
| Böcker| [Modern autentisering med Azure Active Directory för webb program (referens för utvecklare) 1: a utgåvan](https://www.amazon.com/Authentication-Directory-Applications-Developer-Reference/dp/0735696942/ref=sr_1_fkmr0_1?keywords=Azure+multifactor+authentication&qid=1550168894&s=gateway&sr=8-1-fkmr0).  <br> Det här är en auktoritativ och djupgående guide för att skapa Active Directory-autentiseringsbegäranden för de här nya miljöerna. |
| Självstudier| Se [listan med självstudier om hur du integrerar SaaS-appar med Azure AD](../saas-apps/tutorial-list.md). |
| Vanliga frågor| [Vanliga frågor](../app-provisioning/user-provisioning.md) om automatisk användar etablering |

### <a name="solution-architectures"></a>Lösningsarkitekturer

Azure AD Provisioning-tjänsten etablerar användare för att SaaS appar och andra system genom att ansluta till användar hanterings-API-slutpunkter som tillhandahålls av varje program leverantör. Dessa API-slutpunkter för användar hantering tillåter Azure AD att program mässigt skapa, uppdatera och ta bort användare.

#### <a name="automatic-user-provisioning-for-hybrid-enterprises"></a>Automatisk användar etablering för Hybrid företag

I det här exemplet skapas användare och grupper i en HR-databas som är ansluten till en lokal katalog. Azure AD Provisioning-tjänsten hanterar automatisk användar etablering för mål SaaS-program.

 ![användar etablering](./media/plan-auto-user-provisioning/hybridprovisioning.png)

**Beskrivning av arbets flöde:**

1. Användare/grupper skapas i ett lokalt HR-program/-system, till exempel SAP. 

1. **Azure AD Connect agent** kör schemalagda synkroniseringar av identiteter (användare och grupper) från den lokala AD till Azure AD.

1. **Azure AD Provisioning-tjänsten** startar en [första cykel](../app-provisioning/user-provisioning.md) mot käll systemet och mål systemet. 

1. **Azure AD Provisioning-tjänsten** skickar frågor till käll systemet för alla användare och grupper som har ändrats sedan den första cykeln, och skickar ändringar i [stegvisa cykler](../app-provisioning/user-provisioning.md).

#### <a name="automatic-user-provisioning-for-cloud-only-enterprises"></a>Automatisk användar etablering för enbart moln bolag

I det här exemplet sker skapande av användare i Azure AD och Azure AD Provisioning-tjänsten hanterar automatisk användar etablering till mål-(SaaS)-program.

![Diagram som visar processen för att skapa användare/grupper från ett lokalt H R-program via Azure A D-etablerings tjänsten till målets a S-program.](./media/plan-auto-user-provisioning/cloudprovisioning.png)

**Beskrivning av arbets flöde:**

1. Användare/grupper skapas i Azure AD.

1. **Azure AD Provisioning-tjänsten** startar en [första cykel](../app-provisioning/user-provisioning.md) mot käll systemet och mål systemet. 

1. **Azure AD Provisioning-tjänsten** skickar frågor till käll systemet för användare och grupper som har uppdaterats sedan den första cykeln och utför eventuella [stegvisa cykler](../app-provisioning/user-provisioning.md).

#### <a name="automatic-user-provisioning-for-cloud-hr-applications"></a>Automatisk användar etablering för moln HR-program 

I det här exemplet skapas användare och grupper i ett moln HR-program som arbets dag och SuccessFactors. Azure AD Provisioning-tjänsten och Azure AD Connect etablerings agenten etablerar användar data från Cloud HR App-klienten i AD. När kontona har uppdaterats i AD, synkroniseras de med Azure AD via Azure AD Connect, och attributen e-postadresser och användar namn kan skrivas tillbaka till Cloud HR App-klienten.

![Bild 2](./media/plan-auto-user-provisioning/workdayprovisioning.png)

1.  **HR team** utför transaktionerna i Cloud HR App-klienten.
2.  **Azure AD Provisioning-tjänsten** kör de schemalagda cyklerna från Cloud HR App-klienten och identifierar ändringar som behöver bearbetas för synkronisering med AD.
3.  **Azure AD Provisioning-tjänsten** anropar Azure AD Connect etablerings agenten med en nytto last för begäran som innehåller AD-konto skapa/uppdatera/aktivera/inaktivera åtgärder.
4.  **Azure AD Connect etablerings agenten** använder ett tjänst konto för att hantera AD-konto data.
5.  **Azure AD Connect** kör delta-synkronisering för att hämta uppdateringar i AD.
6.  **AD** -uppdateringar synkroniseras med Azure AD. 
7.  **Azure AD Provisioning-tjänsten** tillbakaskrivningar e-postattribut och användar namn från Azure AD till Cloud HR App-klienten.

## <a name="plan-the-deployment-project"></a>Planera distributions projektet

Överväg organisationens behov av att fastställa strategin för att distribuera användar etablering i din miljö.

### <a name="engage-the-right-stakeholders"></a>Engagera rätt intressenter

När teknik projekt kraschar är det vanligt vis på grund av en felaktig matchning av konsekvenserna, resultat och ansvars områden. För att undvika dessa fall GRO par bör [du se till att du engagerar rätt intressenter](../fundamentals/active-directory-deployment-plans.md) och att från intressenter-rollerna i projektet är väl förstå genom att dokumentera intressenterna och deras ingångs-och Accountabilities i projektet.

### <a name="plan-communications"></a>Planera kommunikation

Kommunikationen är nödvändig för att en ny tjänst ska lyckas. Kommunicera proaktivt med dina användare hur deras upplevelse kommer att ändras, när den kommer att ändras och hur du får support om de drabbas av problem.

### <a name="plan-a-pilot"></a>Planera en pilot

Vi rekommenderar att den inledande konfigurationen av automatisk användar etablering är i en test miljö med en liten del av användarna innan de skalas till alla användare i produktionen. Se [metod tips](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) för att köra en pilot.

#### <a name="best-practices-for-a-pilot"></a>Metod tips för en pilot  

Med en pilot kan du testa med en liten grupp innan du distribuerar en funktion för alla. Se till att varje användnings fall i din organisation testas grundligt under testningen.

I din första Wave, rikta in dig på den, användbarhet och andra lämpliga användare som kan testa och ge feedback. Använd den här feedbacken för att ytterligare utveckla kommunikation och anvisningar som du skickar till användarna och ger insikter om vilka typer av problem som support Personalen kan se.

Utöka distributionen till större grupper av användare genom att öka omfånget för de grupper som är riktade till varandra. Detta kan göras via [dynamiskt grupp medlemskap](../enterprise-users/groups-dynamic-membership.md)eller genom att manuellt lägga till användare till mål grupperna.

## <a name="plan-application-connections-and-administration"></a>Planera program anslutningar och administration

Använd Azure AD-portalen för att visa och hantera alla program som har stöd för etablering. Se [hitta dina appar i portalen](../app-provisioning/configure-automatic-user-provisioning-portal.md).

### <a name="determine-the-type-of-connector-to-use"></a>Bestäm vilken typ av anslutning som ska användas

De faktiska stegen som krävs för att aktivera och konfigurera automatisk etablering varierar beroende på programmet. Om det program som du vill etablera automatiskt är listat i [Azure AD SaaS app-galleriet](../saas-apps/tutorial-list.md), bör du välja den [app-specifika integrerings guiden](../saas-apps/tutorial-list.md) för att konfigurera den förintegrerade anslutningen för användar etablering.

Annars följer du stegen nedan:

1. [Skapa en begäran](../develop/v2-howto-app-gallery-listing.md) för en för hands integrerad användar etablerings anslutning. Vårt team kommer att samar beta med dig och programutvecklaren för att publicera ditt program på vår plattform om det stöder SCIM.

1. Använd [BYOA scim](../app-provisioning/use-scim-to-provision-users-and-groups.md) Generic User Provisioning-stöd för appen. Detta är ett krav för att Azure AD ska kunna etablera användare till appen utan en förintegrerad etablerings anslutning.

1. Om programmet kan använda BYOA SCIM-anslutningen kan du läsa [själv studie kursen BYOA scim integration](../app-provisioning/use-scim-to-provision-users-and-groups.md) för att konfigurera BYOA scim-anslutaren för programmet.

Mer information finns i [vilka program och system kan jag använda med automatisk användar etablering i Azure AD?](../app-provisioning/user-provisioning.md)

### <a name="collect-information-to-authorize-application-access"></a>Samla in information för att auktorisera program åtkomst

Att konfigurera automatisk användar etablering är en process per program. För varje program måste [du ange administratörsautentiseringsuppgifter för att ansluta](../app-provisioning/configure-automatic-user-provisioning-portal.md) till mål datorns slut punkt för användar hantering.

Bilden nedan visar en version av de autentiseringsuppgifter som krävs för administratören:

![Etablerings skärmen för att hantera inställningar för användar konto etablering](./media/plan-auto-user-provisioning/userprovisioning-admincredentials.png)

Vissa program kräver administratörs användar namn och lösen ord, men andra kan kräva en Bearer-token.

## <a name="plan-user-and-group-provisioning"></a>Planera användar-och grupp etablering

Om du aktiverar användar etablering för företags program kontrollerar [Azure Portal](https://portal.azure.com/) dess attributvärden genom mappning av attribut.

### <a name="determine-operations-for-each-saas-app"></a>Fastställa åtgärder för varje SaaS-app

Varje program kan ha unika användar-eller Gruppattribut som måste mappas till attributen i din Azure AD. Programmet kan ha endast en delmängd av CRUD-åtgärder tillgängliga.

Dokumentera följande information för varje program:

* CRUD etablerings åtgärder som ska utföras på användaren och eller grupp objekt för mål systemen. Till exempel kanske varje företags ägare av SaaS-appen inte vill ha några möjliga åtgärder.

* Attribut som är tillgängliga i käll systemet

* Attribut som är tillgängliga i mål systemet

* Mappning av attribut mellan system.

### <a name="choose-which-users-and-groups-to-provision"></a>Välj vilka användare och grupper som ska etableras

Innan du implementerar automatisk användar etablering måste du bestämma vilka användare och grupper som ska vara etablerade i programmet.

* Använd [omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) för att definiera attributbaserade regler som avgör vilka användare som ska tillhandahållas till ett program.

* Använd sedan [användar-och grupp tilldelningar](../manage-apps/assign-user-or-group-access-portal.md) efter behov för ytterligare filtrering.

### <a name="define-user-and-group-attribute-mapping"></a>Definiera mappning av användar-och Gruppattribut

Om du vill implementera automatisk användar etablering måste du definiera de användar-och Gruppattribut som behövs för programmet. Det finns en förkonfigurerad uppsättning attribut och [attribut mappningar](../app-provisioning/configure-automatic-user-provisioning-portal.md) mellan Azure AD-användarkonton och varje SaaS-programs användar objekt. Alla SaaS-appar kan inte aktivera Gruppattribut.

Azure AD stöder direkt mappning mellan attribut och attribut, vilket ger konstanta värden eller [skriver uttryck för mappningar av attribut](../app-provisioning/functions-for-customizing-application-data.md). Den här flexibiliteten ger dig noggrann kontroll över vad som kommer att vara ifyllt i mål systemets attribut. Du kan använda [Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md) och Graph Explorer för att exportera mappningar och schemat för användar etablerings attribut till en JSON-fil och importera tillbaka dem till Azure AD.

Mer information finns i [Anpassa användar etablering Attribute-Mappings för SaaS-program i Azure Active Directory](../app-provisioning/customize-application-attributes.md).

### <a name="special-considerations-for-user-provisioning"></a>Särskilda överväganden för användar etablering

Tänk på följande för att minska problem efter distributionen:

* Se till att attributen som används för att mappa användare/grupp-objekt mellan käll-och mål program är elastiska. De bör inte göra det möjligt för användare/grupper att tillhandahållas felaktigt om attributen ändras (till exempel när en användare flyttas till en annan del av företaget).

* Program kan ha särskilda begränsningar och/eller krav som måste uppfyllas för att användar etableringen ska fungera korrekt. Slack trunkerar till exempel värdena för vissa attribut. Information om [automatiska användar etablerings guider](../saas-apps/tutorial-list.md) som är specifika för varje program.

* Bekräfta schema konsekvens mellan käll-och mål systemen. Vanliga problem inkluderar attribut som UPN eller e-post som inte matchar. Till exempel är UPN i Azure AD inställt som *john_smith@contoso.com* och i appen *jsmith@contoso.com* . Mer information finns i [schema referens för användare och grupper](../app-provisioning/use-scim-to-provision-users-and-groups.md).

## <a name="plan-testing-and-security"></a>Planera testning och säkerhet

I varje steg i distributionen ser du till att du testar att resultatet är som förväntat och granskar etablerings cyklerna.

### <a name="plan-testing"></a>Planera testning

När du har konfigurerat automatisk användar etablering för programmet kan du köra test ärenden för att kontrol lera att lösningen uppfyller organisationens krav.

| Scenarier| Förväntat resultat |
| - | - |
| Användaren läggs till i en grupp som tilldelats mål systemet | Användarobjektet är etablerad i mål systemet. <br>Användaren kan logga in på mål systemet och utföra önskade åtgärder. |
| Användaren har tagits bort från en grupp som är tilldelad till mål systemet | Användar objekt har avetablerats i mål systemet.<br>Användaren kan inte logga in på mål systemet. |
| Användar information uppdateras i Azure AD med valfri metod | Uppdaterade användarattribut visas i mål systemet efter en stegvis cykel |
| Användaren är utanför omfånget | Användar objekt har inaktiverats eller tagits bort. <br>Obs! det här beteendet åsidosätts för [etablering av arbets dagar](skip-out-of-scope-deletions.md). |

### <a name="plan-security"></a>Planera säkerhet

Det är vanligt att en säkerhets granskning krävs som en del av en distribution. Om du behöver en säkerhets granskning kan du läsa mer i de många Azure AD- [Whitepapers](https://www.microsoft.com/download/details.aspx?id=36391) som innehåller en översikt över identiteten som en tjänst.

### <a name="plan-rollback"></a>Planera återställning

Om den automatiska etablerings implementeringen av användaren inte fungerar som du vill i produktions miljön kan följande återställnings steg hjälpa dig att återställa till ett tidigare fungerande tillstånd:

1. Granska [sammanfattnings rapporten för etablering](../app-provisioning/check-status-user-account-provisioning.md) och [etablerings loggar](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) för att fastställa vilka fel åtgärder som har inträffat för berörda användare och/eller grupper.

1. Använd etableringen av gransknings loggar för att fastställa senast fungerande tillstånd för de användare och/eller grupper som påverkas. Granska även käll systemen (Azure AD eller AD).

1. Arbeta med program ägaren för att uppdatera användare och/eller grupper som påverkas direkt i programmet med de senaste fungerande tillstånds värdena.

## <a name="deploy-automatic-user-provisioning-service"></a>Distribuera automatisk etablerings tjänst för användare

Välj de steg som passar dina lösnings krav.

### <a name="prepare-for-the-initial-cycle"></a>Förbered dig för den första cykeln

När Azure AD Provisioning-tjänsten körs för första gången skapar den första cykeln mot käll system-och mål systemen en ögonblicks bild av alla användar objekt för varje mål system.

När du aktiverar automatisk etablering för ett program kan den första cykeln ta var som helst från 20 minuter till flera timmar. Varaktigheten beror på storleken på Azure AD-katalogen och antalet användare i omfånget för etablering.

Etablerings tjänsten lagrar tillstånd för båda systemen efter den första cykeln, vilket förbättrar prestandan för efterföljande stegvisa cykler.

### <a name="configure-automatic-user-provisioning"></a>Konfigurera automatisk användaretablering

Använd [Azure Portal](https://portal.azure.com/) för att hantera automatisk etablering av användar konton och avetablering för program som stöder det. Följ stegen i [Hur gör jag för att konfigurera automatisk etablering för ett program?](../app-provisioning/user-provisioning.md)

Azure AD-tjänsten för användar etablering kan också konfigureras och hanteras med hjälp av [Microsoft Graph-API: et](/graph/api/resources/synchronization-overview).

## <a name="manage-automatic-user-provisioning"></a>Hantera automatisk användar etablering

Nu när du har distribuerat måste du hantera lösningen.

### <a name="monitor-user-provisioning-operation-health"></a>Övervaka hälsa för användar etablerings åtgärd

Efter en lyckad [första cykel](../app-provisioning/user-provisioning.md)körs stegvisa uppdateringar i Azure AD Provisioning-tjänsten, med intervall som är specifika för varje program, tills någon av följande händelser inträffar:

* Tjänsten stoppas manuellt och en ny första cykel utlöses med hjälp av [Azure Portal](https://portal.azure.com/)eller med lämpligt [Microsoft Graph API](/graph/api/resources/synchronization-overview) -kommando.

* En ny första cykel utlöses av en ändring av attributens mappningar eller omfångs filter.

* Etablerings processen försätts i karantän på grund av en hög fel frekvens och hålls kvar i karantän i mer än fyra veckor när den inaktive ras automatiskt.

Information om hur du granskar dessa händelser och alla andra aktiviteter som utförs av etablerings tjänsten finns i Azure AD- [etablerings loggar](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context).

För att förstå hur länge etablerings cyklerna tar och övervakar förloppet för etablerings jobbet kan du [kontrol lera status för användar etablering](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).

### <a name="gain-insights-from-reports"></a>Få insikter från rapporter

Azure AD kan ge [ytterligare insikter](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om din organisations användar etablering och drifts hälsa genom gransknings loggar och-rapporter.

Administratörer bör kontrol lera sammanfattnings rapporten för etablering för att övervaka etablerings jobbets operativa tillstånd. Alla aktiviteter som utförs av etablerings tjänsten registreras i gransknings loggarna för Azure AD. Se [självstudie: rapportering om automatisk etablering av användar konto](../app-provisioning/check-status-user-account-provisioning.md).

Vi rekommenderar att du förbrukar ägande rätt till och använder dessa rapporter på en takt som uppfyller organisationens krav. Azure AD behåller de flesta gransknings data i 30 dagar.

### <a name="troubleshoot"></a>Felsöka

Se följande länkar för att felsöka eventuella problem som kan uppstå under etableringen:

* [Problem med att konfigurera användar etablering i ett Azure AD Gallery-program](../app-provisioning/application-provisioning-config-problem.md)

* [Synkronisera ett attribut från din lokala Active Directory till Azure AD för etablering till ett program](../app-provisioning/user-provisioning-sync-attributes-for-mapping.md)

* [Problem med att spara administratörsautentiseringsuppgifter när du konfigurerar användar etablering till ett Azure Active Directory Galleri program](./user-provisioning.md)

* [Inga användare tillhandahålls till ett Azure AD Gallery-program](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

* [Fel uppsättning användare tillhandahålls till ett Azure AD Gallery-program](../manage-apps/add-application-portal-assign-users.md)

### <a name="helpful-documentation"></a>Användbar dokumentation

* [Skriver uttryck för mappningar av attribut](../app-provisioning/functions-for-customizing-application-data.md)

* [Översikt över Azure AD Synchronization API](/graph/api/resources/synchronization-overview)

* [Hoppa över borttagning av användar konton som ingår i omfånget](skip-out-of-scope-deletions.md)

* [Azure AD Connect etablerings agent: versions historik](provisioning-agent-release-version-history.md)

#### <a name="resources"></a>Resurser

* [Ge produktfeedback](https://feedback.azure.com/forums/169401-azure-active-directory)

* [Håll dig uppdaterad om vad som är nytt med Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

* [Microsoft Q&ett Azure AD-forum](/answers/topics/azure-active-directory.html)

## <a name="next-steps"></a>Nästa steg
* [Konfigurera automatisk användar etablering](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [Exportera eller importera etablerings konfigurationen genom att använda Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md)

* [Skriva uttryck för mappningar av attribut i Azure Active Directory](../app-provisioning/functions-for-customizing-application-data.md)