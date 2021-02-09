---
title: Förstå hur Azure AD-etablering fungerar | Microsoft Docs
description: Förstå hur Azure AD-etablering fungerar
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/04/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.custom: contperf-fy21q2
ms.openlocfilehash: 048adee21d5c2e49ef02f518002a1dc6025c1ecd
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988977"
---
# <a name="how-provisioning-works"></a>Så här fungerar etablering

Automatisk etablering syftar på att skapa användar identiteter och roller i de moln program som användare behöver åtkomst till. Förutom att skapa användar identiteter omfattar automatisk etablering underhåll och borttagning av användar identiteter som status eller roller ändras. Innan du påbörjar en distribution kan du läsa den här artikeln för att lära dig hur Azure AD etablerar fungerar och hur du får konfigurations rekommendationer. 

**Azure AD Provisioning-tjänsten** tillhandahåller användare till SaaS-appar och andra system genom att ansluta till ett system för scim (Cross-Domain Identity Management) 2,0 användar hanterings-API: n som tillhandahålls av program leverantören. Den här SCIM-slutpunkten låter Azure AD program mässigt skapa, uppdatera och ta bort användare. För valda program kan etablerings tjänsten också skapa, uppdatera och ta bort ytterligare ID-relaterade objekt, till exempel grupper och roller. Den kanal som används för etablering mellan Azure AD och programmet krypteras med HTTPS TLS 1,2-kryptering.


![Azure AD Provisioning service- ](./media/how-provisioning-works/provisioning0.PNG)
 *bild 1: Azure AD Provisioning-tjänsten*

![Utgående användar etablering arbets flöde ](./media/how-provisioning-works/provisioning1.PNG)
 *bild 2: "utgående" arbets flöde för användar etablering från Azure AD till populära SaaS-program*

![Inkommande användar etablering arbets flöde ](./media/how-provisioning-works/provisioning2.PNG)
 *bild 3: "inkommande" arbets flöde för användar etablering från populära HCM-program (Human kapital Management) till Azure Active Directory och Windows Server Active Directory*

## <a name="provisioning-using-scim-20"></a>Etablering med SCIM 2,0

Azure AD Provisioning-tjänsten använder [SCIM 2,0-protokollet](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards) för automatisk etablering. Tjänsten ansluter till SCIM-slutpunkten för programmet och använder SCIM för användar objekt och REST-API: er för att automatisera etableringen och inaktive ring av användare och grupper. En SCIM etablerings anslutning tillhandahålls för de flesta program i Azure AD-galleriet. När du skapar appar för Azure AD kan utvecklare använda API: et för SCIM 2,0-användar hantering för att skapa en SCIM-slutpunkt som integrerar Azure AD för etablering. Mer information finns i [Bygg en scim-slutpunkt och konfigurera användar etablering](../app-provisioning/use-scim-to-provision-users-and-groups.md).

Om du vill begära en automatisk Azure AD Provisioning-anslutning för en app som för närvarande inte har en, fyller du i en [Azure Active Directory program förfrågan](https://aka.ms/aadapprequest).

## <a name="authorization"></a>Auktorisering

Autentiseringsuppgifter krävs för att Azure AD ska kunna ansluta till programmets användar hanterings-API. När du konfigurerar automatisk användar etablering för ett program måste du ange giltiga autentiseringsuppgifter. För Galleri program kan du hitta autentiseringstypen och kraven för programmet genom att titta på självstudierna för appen. För program som inte är gallerier kan du läsa [scim](./use-scim-to-provision-users-and-groups.md#authorization-to-provisioning-connectors-in-the-application-gallery) -dokumentationen för att förstå typ och krav för autentiseringsuppgifter. I Azure Portal kan du testa autentiseringsuppgifterna genom att låta Azure AD försöka ansluta till appens etablerings app med de angivna autentiseringsuppgifterna.

## <a name="mapping-attributes"></a>Mappar attribut

När du aktiverar användar etablering för ett SaaS-program från tredje part styr Azure Portal dess attributvärden genom mappningar av attribut. Mappningar bestämmer vilka användarattribut som flödar mellan Azure AD och mål programmet när användar konton är etablerade eller uppdaterade.

Det finns en förkonfigurerad uppsättning attribut och mappningar av attribut mellan Azure AD-användarkonton och varje SaaS-Apps användar objekt. Vissa appar hanterar andra typer av objekt tillsammans med användare, t. ex. grupper.

När du konfigurerar etablering är det viktigt att granska och konfigurera de mappningar och arbets flöden som definierar vilka användar egenskaper (eller grupp) som flödar från Azure AD till programmet. Granska och konfigurera den matchande egenskapen (**matcha objekt med det här attributet**) som används för att unikt identifiera och matcha användare/grupper mellan de två systemen.

Du kan anpassa standardattributen – mappningar efter dina affärs behov. Så du kan ändra eller ta bort befintliga attribut-mappningar eller skapa nya attribut-mappningar. Mer information finns i [Anpassa användar etablering attribut-mappningar för SaaS-program](./customize-application-attributes.md).

När du konfigurerar etablering till ett SaaS-program är en av de typer av mappningar av attribut som du kan ange en uttrycks mappning. För dessa mappningar måste du skriva ett skript liknande uttryck som gör att du kan omvandla dina användares data till format som är mer acceptabla för SaaS-programmet. Mer information finns i [skriva uttryck för mappning av attribut](functions-for-customizing-application-data.md).

## <a name="scoping"></a>Omfångsdefinierande 
### <a name="assignment-based-scoping"></a>Tilldelning baserad omfattning

För utgående etablering från Azure AD till ett SaaS-program är det vanligaste sättet att avgöra [vilka användare som](../manage-apps/assign-user-or-group-access-portal.md) befinner sig i omfång för etablering. Eftersom användar tilldelningar också används för att aktivera enkel inloggning, kan samma metod användas för att hantera både åtkomst och etablering. Tilldelnings omfång gäller inte för inkommande etablerings scenarier som Workday och SuccessFactors.

* **Användargrupp.** Med en Azure AD Premium licens plan kan du använda grupper för att tilldela åtkomst till ett SaaS-program. När etablerings omfånget är inställt på **Synkronisera endast tilldelade användare och grupper**, etablerar eller avetablerar Azure AD Provisioning-tjänsten användare baserat på om de är medlemmar i en grupp som är tilldelad till programmet. Själva gruppobjektet tillhandahålls inte om programmet inte stöder grupp objekt. Kontrol lera att de grupper som har tilldelats ditt program har egenskapen "SecurityEnabled" inställd på "true".

* **Dynamiska grupper.** Azure AD-tjänsten för användar etablering kan läsa och etablera användare i [dynamiska grupper](../enterprise-users/groups-create-rule.md). Tänk på dessa varningar och rekommendationer i åtanke:

  * Dynamiska grupper kan påverka prestanda för etablering från slut punkt till slut punkt från Azure AD till SaaS-program.

  * Hur snabbt en användare i en dynamisk grupp etableras eller avetableras i ett SaaS-program beror på hur snabbt den dynamiska gruppen kan utvärdera medlemskaps ändringar. Information om hur du kontrollerar bearbetnings statusen för en dynamisk grupp finns i [kontrol lera bearbetnings status för en medlemskaps regel](../enterprise-users/groups-create-rule.md).

  * När en användare förlorar medlemskap i den dynamiska gruppen betraktas den som en avetablerings händelse. Tänk på det här scenariot när du skapar regler för dynamiska grupper.

* **Kapslade grupper.** Azure AD-tjänsten för användar etablering kan inte läsa eller etablera användare i kapslade grupper. Tjänsten kan bara läsa och etablera användare som är omedelbara medlemmar i en uttryckligen tilldelad grupp. Den här begränsningen av "gruppbaserade tilldelningar till program" påverkar också enkel inloggning (se [använda en grupp för att hantera åtkomst till SaaS-program](../enterprise-users/groups-saasapps.md)). I stället kan du direkt tilldela eller ändra [omfång i](define-conditional-rules-for-provisioning-user-accounts.md) grupper som innehåller de användare som ska vara etablerade.

### <a name="attribute-based-scoping"></a>Attribut-baserat omfång 

Du kan använda definitions områdes filter för att definiera attributbaserade regler som avgör vilka användare som ska tillhandahållas till ett program. Den här metoden används ofta för inkommande etablering från HCM-program till Azure AD och Active Directory. Definitions områdes filter konfigureras som en del av mappningar av mappar för varje Azure AD-anslutning för användar etablering. Mer information om hur du konfigurerar attributbaserade definitions områdes filter finns i [attribut-baserad program etablering med omfångs filter](define-conditional-rules-for-provisioning-user-accounts.md).

### <a name="b2b-guest-users"></a>B2B-användare (gäst)

Det går att använda Azure AD-tjänsten för användar etablering för att etablera B2B (eller gäst) användare i Azure AD till SaaS-program. För att B2B-användare ska kunna logga in på SaaS-programmet med hjälp av Azure AD, måste dock SaaS-programmet ha sin SAML-baserade enkel inloggnings funktion som kon figurer ATS på ett visst sätt. Mer information om hur du konfigurerar SaaS-program för att stödja inloggningar från B2B-användare finns i [Konfigurera SaaS-appar för B2B-samarbete](../external-identities/configure-saas-apps.md).

Observera att userPrincipalName för en gäst användare ofta lagras som "alias # EXT # @domain.com ". När userPrincipalName inkluderas i dina attributmappning som ett källattribut, tas #EXT # bort från userPrincipalName. Om du kräver att #EXT # ska vara kvar ersätter du userPrincipalName med originalUserPrincipalName som källattribut. 

## <a name="provisioning-cycles-initial-and-incremental"></a>Etablerings cykler: initial och stegvis

När Azure AD är käll systemet använder etablerings tjänsten [use delta-frågan för att spåra ändringar i Microsoft Graph data](/graph/delta-query-overview) för övervakning av användare och grupper. Etablerings tjänsten kör en första cykel mot käll systemet och mål systemet, följt av periodiska stegvisa cykler.

### <a name="initial-cycle"></a>Första cykeln

När etablerings tjänsten startas kommer den första cykeln att:

1. Fråga alla användare och grupper från käll systemet och hämta alla attribut som definierats i [mappningarna för attribut](customize-application-attributes.md).

2. Filtrera de användare och grupper som returneras med hjälp av eventuella konfigurerade [tilldelningar](../manage-apps/assign-user-or-group-access-portal.md) eller [attributbaserade definitions filter](define-conditional-rules-for-provisioning-user-accounts.md).

3. När en användare tilldelas eller omfånget för etablering, frågar tjänsten mål systemet efter en matchande användare med de angivna [matchande attributen](customize-application-attributes.md#understanding-attribute-mapping-properties). Exempel: om namnet på userPrincipal i käll systemet är det matchande attributet och mappar till användar namn i mål systemet, frågar etablerings tjänsten mål systemet efter användar namn som matchar userPrincipal-namnens värden i käll systemet.

4. Om en matchande användare inte hittas i mål systemet, skapas den med hjälp av de attribut som returneras från käll systemet. När användar kontot har skapats identifierar och cachelagrar etablerings tjänsten mål systemets ID för den nya användaren. Detta ID används för att köra alla framtida åtgärder på den användaren.

5. Om det finns en matchande användare uppdateras den med hjälp av de attribut som anges av käll systemet. När användar kontot har matchats identifierar och cachelagrar etablerings tjänsten mål systemets ID för den nya användaren. Detta ID används för att köra alla framtida åtgärder på den användaren.

6. Om attribut mappningarna innehåller "Reference"-attribut gör tjänsten ytterligare uppdateringar i mål systemet för att skapa och länka de refererade objekten. En användare kan till exempel ha ett "Manager"-attribut i mål systemet, som är länkat till en annan användare som skapats i mål systemet.

7. Behåll en vattenstämpel i slutet av den första cykeln, som ger start punkten för de senare stegvisa cyklerna.

Vissa program som ServiceNow, G Suite och Box stöder inte bara etablering av användare, utan även etablering av grupper och medlemmar. I dessa fall, om grupp etablering är aktiverat i [mappningarna](customize-application-attributes.md), synkroniserar etablerings tjänsten användarna och grupperna och synkroniserar sedan grupp medlemskapen senare.

### <a name="incremental-cycles"></a>Stegvisa cykler

Efter den första cykeln kommer alla andra cykler att:

1. Fråga käll systemet efter användare och grupper som har uppdaterats sedan den senaste vattenstämpeln lagrades.

2. Filtrera de användare och grupper som returneras med hjälp av eventuella konfigurerade [tilldelningar](../manage-apps/assign-user-or-group-access-portal.md) eller [attributbaserade definitions filter](define-conditional-rules-for-provisioning-user-accounts.md).

3. När en användare tilldelas eller omfånget för etablering, frågar tjänsten mål systemet efter en matchande användare med de angivna [matchande attributen](customize-application-attributes.md#understanding-attribute-mapping-properties).

4. Om en matchande användare inte hittas i mål systemet, skapas den med hjälp av de attribut som returneras från käll systemet. När användar kontot har skapats identifierar och cachelagrar etablerings tjänsten mål systemets ID för den nya användaren. Detta ID används för att köra alla framtida åtgärder på den användaren.

5. Om det finns en matchande användare uppdateras den med hjälp av de attribut som anges av käll systemet. Om det är ett nyligen tilldelat konto som matchas identifierar och cachelagrar etablerings tjänsten mål systemets ID för den nya användaren. Detta ID används för att köra alla framtida åtgärder på den användaren.

6. Om attribut mappningarna innehåller "Reference"-attribut gör tjänsten ytterligare uppdateringar i mål systemet för att skapa och länka de refererade objekten. En användare kan till exempel ha ett "Manager"-attribut i mål systemet, som är länkat till en annan användare som skapats i mål systemet.

7. Om en användare som tidigare var i omfång för etablering tas bort från omfattningen, inklusive att den inte har tilldelats, inaktiverar tjänsten användaren i mål systemet via en uppdatering.

8. Om en användare som tidigare var i omfång för etablering är inaktive rad eller mjuk raderas i käll systemet, inaktiverar tjänsten användaren i mål systemet via en uppdatering.

9. Om en användare som tidigare var i omfång för etablering är hårt borttagen i käll systemet, tar tjänsten bort användaren i mål systemet. I Azure AD tar användarna bort 30 dagar efter att de har mjuk borttagning.

10. Spara en ny vattenstämpel i slutet av den stegvisa cykeln, som ger start punkten för de senare stegvisa cyklerna.

> [!NOTE]
> Du kan välja att inaktivera åtgärderna **skapa**, **Uppdatera** eller **ta bort** genom att använda kryss rutorna **mål objekt åtgärder** i avsnittet [mappningar](customize-application-attributes.md) . Logiken för att inaktivera en användare under en uppdatering styrs också via en attributmappning från ett fält som "accountEnabled".

Etablerings tjänsten fortsätter att köra stegvisa stegvisa cykler på obestämd tid, med intervall som definierats i [självstudien som är specifik för varje program](../saas-apps/tutorial-list.md). Stegvisa cykler fortsätter tills någon av följande händelser inträffar:

- Tjänsten stoppas manuellt med Azure Portal eller med lämpligt Microsoft Graph API-kommando.
- En ny första cykel utlöses med alternativet **Rensa tillstånd och starta om** i Azure Portal, eller med lämpligt Microsoft Graph API-kommando. Den här åtgärden rensar en lagrad vattenstämpel och gör så att alla käll objekt utvärderas igen.
- En ny första cykel utlöses på grund av en ändring av attributens mappningar eller omfångs filter. Den här åtgärden tar också bort alla lagrade vattenstämplar och gör så att alla käll objekt utvärderas igen.
- Etablerings processen placeras i karantän (se nedan) på grund av en hög fel frekvens och finns kvar i karantän i mer än fyra veckor. I så fall kommer tjänsten att inaktive ras automatiskt.

### <a name="errors-and-retries"></a>Fel och försök

Om ett fel i mål systemet förhindrar att en enskild användare läggs till, uppdateras eller tas bort i mål systemet, görs ett nytt försök vid nästa synkronisering. Om användaren fortsätter att Miss lyckas kommer Återförsöken att ske med minskad frekvens, vilket gradvis skalar tillbaka till bara ett försök per dag. För att lösa felet måste administratörerna kontrol lera [etablerings loggarna](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) för att fastställa rotor saken och vidta lämpliga åtgärder. Vanliga problem kan vara:

- Användare som inte har något attribut ifyllt i käll systemet som krävs i mål systemet
- Användare som har ett attributvärde i käll systemet där det finns en unik begränsning i mål systemet, och samma värde finns i en annan användar post

Lös de här felen genom att justera attributvärdena för den berörda användaren i käll systemet, eller genom att justera mappningarna för attribut så att de inte orsakar konflikter.

### <a name="quarantine"></a>Karantän

Om de flesta eller alla anrop som görs mot mål systemet inte fungerar konsekvent på grund av ett fel (till exempel ogiltiga administratörsautentiseringsuppgifter), försätts etablerings jobbet i "karantän". Det här läget anges i [sammanfattnings rapporten för etablering](./check-status-user-account-provisioning.md) och via e-post om e-postaviseringar har kon figurer ats i Azure Portal.

När den är i karantän minskas frekvensen för stegvisa cykler till en gång per dag.

Etablerings jobbet avslutar karantän när alla fel som är felaktiga har åtgärd ATS och nästa synkronisering startar. Om etablerings jobbet finns kvar i karantän i mer än fyra veckor inaktive ras etablerings jobbet. Läs mer här om karantän status [här](./application-provisioning-quarantine-status.md).

### <a name="how-long-provisioning-takes"></a>Hur lång tid tar etableringen?

Prestanda beror på om ditt etablerings jobb kör en inledande etablerings cykel eller en stegvis cykel. Mer information om hur lång etablering tar och hur du övervakar etablerings tjänstens status finns i [kontrol lera status för användar etablering](application-provisioning-when-will-provisioning-finish-specific-user.md).

### <a name="how-to-tell-if-users-are-being-provisioned-properly"></a>Så här ser du om användarna är korrekt etablerade

Alla åtgärder som körs av användar etablerings tjänsten registreras i Azure AD [-etablerings loggarna (för hands version)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Loggarna omfattar alla Läs-och skriv åtgärder som gjorts för käll-och mål systemen och de användar data som lästs eller skrevs under varje åtgärd. Information om hur du läser etablerings loggar i Azure Portal finns i [rapport guiden för etablering](./check-status-user-account-provisioning.md).

## <a name="de-provisioning"></a>Avetablering
Azure AD Provisioning-tjänsten håller käll-och mål systemen synkroniserade genom att inaktivera etablerings konton när användar åtkomst tas bort.

Etablerings tjänsten stöder både borttagning och inaktive ring (kallas ibland borttagning) av användare. Den exakta definitionen av inaktivera och ta bort beror på appens mål, men vanligt vis anger en inaktive ring att användaren inte kan logga in. En borttagning anger att användaren har tagits bort helt från programmet. För SCIM-program är en inaktive rad en begäran att ställa in egenskapen *Active* på false för en användare. 

**Konfigurera ditt program för att inaktivera en användare**

Kontrol lera att du har markerat kryss rutan för uppdateringar.

Se till att du har mappning för *aktiv* för ditt program. Om du använder ett program från App-galleriet kan mappningen vara något annorlunda. Kontrol lera att du använder standard-/slut för-Box-mappningen för Galleri program.

:::image type="content" source="./media/how-provisioning-works/disable-user.png" alt-text="Inaktivera en användare" lightbox="./media/how-provisioning-works/disable-user.png":::


**Konfigurera ditt program för att ta bort en användare**

Följande scenarier utlöser en inaktivera eller ta bort: 
* En användare är mjuk borttagen i Azure AD (skickas till pappers korgen/AccountEnabled-egenskapen inställt på falskt).
    30 dagar efter att en användare har tagits bort i Azure AD kommer de att tas bort permanent från klienten. I det här läget skickar etablerings tjänsten en begäran om borttagning för att permanent ta bort användaren i programmet. Du kan när som helst under perioden på 30 dagar [manuellt ta bort en användare permanent](../fundamentals/active-directory-users-restore.md), som skickar en begäran om borttagning till programmet.
* En användare tas bort permanent från pappers korgen i Azure AD.
* En användare är inte tilldelad från en app.
* En användare går från inom omfånget till out-of-scope (skickar inte ett definitions områdes filter längre).

:::image type="content" source="./media/how-provisioning-works/delete-user.png" alt-text="Ta bort en användare" lightbox="./media/how-provisioning-works/delete-user.png":::

Som standard tar Azure AD Provisioning-tjänsten bort eller inaktiverar användare som omfattas av omfånget. Om du vill åsidosätta det här standard beteendet kan du ange en flagga som [hoppar över borttagningar utanför omfattning.](skip-out-of-scope-deletions.md)

Om någon av ovanstående fyra händelser inträffar och mål programmet inte stöder mjuka borttagningar, skickar etablerings tjänsten en BORTTAGNINGs förfrågan för att permanent ta bort användaren från appen.

Om du ser ett attribut IsSoftDeleted i dina attributmappning, används det för att fastställa användarens tillstånd och om du vill skicka en uppdateringsbegäran med aktiv = falskt för att ta bort användaren.

**Kända begränsningar**

* Om en användare som tidigare hanterades av etablerings tjänsten inte är tilldelad från en app, eller från en grupp som har tilldelats till en app, kommer vi att skicka en inaktive rings förfrågan. Vid det här läget hanteras inte användaren av tjänsten och vi kommer inte att skicka en begäran om borttagning när de tas bort från katalogen.
* Det finns inte stöd för att konfigurera en användare som är inaktive rad i Azure AD. De måste vara aktiva i Azure AD innan de tillhandahålls.
* När en användare går från mjuk borttagning till aktiv, aktiverar Azure AD Provisioning-tjänsten användaren i mål programmet, men kommer inte att återställa grupp medlemskapen automatiskt. Mål programmet ska behålla grupp medlemskapet för användaren i inaktivt läge. Om mål programmet inte stöder detta kan du starta om etableringen för att uppdatera grupp medlemskapet. 

**Rekommendation**

När du utvecklar ett program stöder du alltid både mjuka borttagningar och hårda borttagningar. Det gör det möjligt för kunder att återställa när en användare av misstag har inaktiverats.


## <a name="next-steps"></a>Nästa steg

[Planera en distribution med automatisk användaretablering](../app-provisioning/plan-auto-user-provisioning.md)

[Konfigurera etablering för en galleriapp](./configure-automatic-user-provisioning-portal.md)

[Skapa en SCIM-slutpunkt och konfigurera etablering när du skapar din egen app](../app-provisioning/use-scim-to-provision-users-and-groups.md)

[Felsök problem med att konfigurera och tillhandahålla användare till ett program](./application-provisioning-config-problem.md).
