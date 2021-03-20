---
title: Referens guide för Azure Active Directory identitets-och åtkomst hanterings åtgärder
description: Den här åtgärds guiden beskriver de kontroller och åtgärder som du bör vidta för att skydda identitets-och åtkomst hanterings åtgärder
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 66bce573be5a31641bdff809b8e9a79b617a703a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92371009"
---
# <a name="azure-active-directory-identity-and-access-management-operations-reference-guide"></a>Referens guide för Azure Active Directory identitets-och åtkomst hanterings åtgärder

Det här avsnittet i [hand boken för Azure AD-åtgärder](active-directory-ops-guide-intro.md) beskriver de kontroller och åtgärder som du bör tänka på för att skydda och hantera livs cykeln för identiteter och deras tilldelningar.

> [!NOTE]
> Dessa rekommendationer är aktuella vid publicerings datumet, men kan ändras med tiden. Organisationer bör kontinuerligt utvärdera sina identitets metoder när Microsofts produkter och tjänster utvecklas med tiden.

## <a name="key-operational-processes"></a>Viktiga operativa processer

### <a name="assign-owners-to-key-tasks"></a>Tilldela ägare till viktiga uppgifter

Hantering av Azure Active Directory kräver kontinuerlig körning av viktiga operativa uppgifter och processer som kanske inte ingår i ett distributions projekt. Det är fortfarande viktigt att du konfigurerar dessa uppgifter för att underhålla din miljö. De viktigaste uppgifterna och deras rekommenderade ägare är:

| Uppgift | Ägare |
| :- | :- |
| Definiera processen för att skapa Azure-prenumerationer | Varierar mellan olika organisationer |
| Bestäm vem som får Enterprise Mobility + Security licenser | IAM-åtgärds team |
| Bestäm vem som får Microsoft 365 licenser | Produktivitets team |
| Bestäm vem som får andra licenser, till exempel Dynamics, Visual Studio Codespaces | Program ägare |
| Tilldela licenser | IAM-åtgärds team |
| Felsöka och åtgärda licens tilldelnings fel | IAM-åtgärds team |
| Etablera identiteter för program i Azure AD | IAM-åtgärds team |

När du granskar listan kanske du måste tilldela en ägare för aktiviteter som saknar ägare eller justera ägarskapet för aktiviteter med ägare som inte är justerade enligt rekommendationerna ovan.

#### <a name="assigning-owners-recommended-reading"></a>Tilldela ägare Rekommenderad läsning

- [Tilldela administratörsroller i Azure Active Directory](../roles/permissions-reference.md)
- [Styrning i Azure](../../governance/index.yml)

## <a name="on-premises-identity-synchronization"></a>Synkronisering av lokala identiteter

### <a name="identify-and-resolve-synchronization-issues"></a>Identifiera och lösa synkroniseringsproblem

Microsoft rekommenderar att du har en god bas linje och förståelse för problemen i din lokala miljö som kan leda till synkroniseringsproblem i molnet. Eftersom automatiserade verktyg, till exempel [IdFix](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) och [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md#why-use-azure-ad-connect-health) kan generera en hög volym av falska positiva identifieringar, rekommenderar vi att du identifierar synkroniseringsfel som har lämnats utan adress i mer än 100 dagar genom att rensa dessa objekt i fel. Långsiktigt olösta synkroniseringsfel kan generera support ärenden. [Fel sökning av fel vid synkronisering](../hybrid/tshoot-connect-sync-errors.md) ger en översikt av olika typer av synkroniseringsfel, några möjliga scenarier som orsakar felen och potentiella sätt att åtgärda felen.

### <a name="azure-ad-connect-sync-configuration"></a>Azure AD Connect Sync-konfiguration

Om du vill aktivera alla hybrid upplevelser, enhets säkerhets position och integrering med Azure AD, krävs det att du synkroniserar användar konton som dina anställda använder för att logga in på sina datorer.

Om du inte synkroniserar skogens användare loggar in, bör du ändra synkroniseringen så att den kommer från rätt skog.

#### <a name="synchronization-scope-and-object-filtering"></a>Omfånget för synkronisering och objekt filtrering

Att ta bort kända buckets objekt som inte krävs för synkronisering har följande drifts fördelar:

- Färre synkroniseringsfel
- Snabbare synkroniseringar
- Mindre "skräp" för att vidarebefordra från lokal plats, till exempel förorening av den globala adress listan för lokala tjänst konton som inte är relevanta i molnet

> [!NOTE]
> Om du har importerat flera objekt som inte exporteras till molnet, bör du filtrera efter OU eller speciella attribut.

Exempel på objekt som ska undantas är:

- Tjänst konton som inte används för moln program
- Grupper som inte är avsedda att användas i moln scenarier som de som används för att bevilja åtkomst till resurser
- Användare eller kontakter som är externa identiteter som är avsedda att representeras med Azure AD B2B-samarbete
- Dator konton där anställda inte är avsedda att komma åt moln program från exempelvis servrar

> [!NOTE]
> Om en enskild mänsklig identitet har flera konton som tillhandahålls från något som en äldre domän migrering, sammanslagning eller förvärv, bör du bara synkronisera kontot som används av användaren på en daglig basis, till exempel vad de använder för att logga in på datorn.

Vi rekommenderar att du uppnår en balans mellan att minska antalet objekt som ska synkroniseras och hur komplexa reglerna är. I allmänhet är en kombination mellan OU/behållar [filtrering](../hybrid/how-to-connect-sync-configure-filtering.md) och en enkel mappning till attributet cloudFiltered en effektiv filtrerings kombination.

> [!IMPORTANT]
> Om du använder grupp filtrering i produktion bör du övergå till en annan filtrerings metod.

#### <a name="sync-failover-or-disaster-recovery"></a>Synkronisera redundans eller haveri beredskap

Azure AD Connect spelar upp en nyckel roll i etablerings processen. Om synkroniseringstjänsten försätts offline av någon anledning, kan ändringar till lokalt inte uppdateras i molnet och kan leda till åtkomst problem för användarna. Därför är det viktigt att definiera en strategi för växling vid fel som gör det möjligt för administratörer att snabbt återuppta synkronisering när Sync-servern är offline. Sådana strategier kan falla i följande kategorier:

- **Distribuera Azure AD Connect servrar i mellanlagrings läge** – låter en administratör "befordra" mellanlagrings servern till produktion med en enkel konfigurations växel.
- **Använd virtualisering** – om Azure AD Connect distribueras på en virtuell dator (VM) kan administratörerna använda sin Virtualization stack för att direktmigrera eller snabbt distribuera om den virtuella datorn och därmed återuppta synkroniseringen.

Om din organisation saknar en katastrof återställning och redundans för synkronisering bör du inte ovilliga Distribuera Azure AD Connect i mellanlagrings läge. På samma sätt bör du, om det uppstår ett matchnings fel mellan produktions-och mellanlagrings konfigurationen, ändra bas linje Azure AD Connect mellanliggande läge för att matcha produktions konfigurationen, inklusive program varu versioner och konfigurationer.

![En skärm bild av konfigurationen för Azure AD Connect mellanlagrings läge](./media/active-directory-ops-guide/active-directory-ops-img1.png)

#### <a name="stay-current"></a>Håll dig uppdaterad

Microsoft uppdaterar Azure AD Connect regelbundet. Håll dig uppdaterad för att dra nytta av prestanda förbättringar, fel korrigeringar och nya funktioner som alla nya versioner erbjuder.

Om din Azure AD Connect version är mer än sex månader bakom bör du uppgradera till den senaste versionen.

#### <a name="source-anchor"></a>Käll fäst punkt

Med **MS-DS-consistencyguid** som [käll ankare](../hybrid/plan-connect-design-concepts.md) kan en enklare migrering av objekt mellan skogar och domäner, vilket är vanligt i AD-domän konsolidering/rensning, sammanslagningar, förvärv och divestitures.

Om du för närvarande använder **ObjectGuid** som käll ankare rekommenderar vi att du växlar till att använda **MS-DS-ConsistencyGuid**.

#### <a name="custom-rules"></a>Anpassade regler

Azure AD Connect anpassade regler ger möjlighet att styra flödet av attribut mellan lokala objekt och moln objekt. Att använda eller använda anpassade regler kan dock medföra följande risker:

- Fel söknings komplexitet
- Försämring av prestanda vid komplexa åtgärder i objekt
- Högre sannolikhet för konfigurations avvikelser mellan produktions servern och mellanlagrings servern
- Ytterligare kostnader för att uppgradera Azure AD Connect om anpassade regler skapas i prioriteten som är större än 100 (används av inbyggda regler)

Om du använder alltför komplexa regler bör du undersöka orsakerna till komplexiteten och hitta möjligheter att förenkla. Om du dessutom har skapat anpassade regler med prioritets värde över 100 bör du åtgärda reglerna så att de inte är i fara eller krockar med standard uppsättningen.

Exempel på felanvända anpassade regler är:

- **Kompensera för felaktiga data i katalogen** – i det här fallet rekommenderar vi att du arbetar med ägarna till AD-teamet och rensar data i katalogen som en reparations uppgift och justerar processerna för att undvika att felaktiga data införs.
- **Engångs reparation av enskilda användare** – det är vanligt att hitta regler som specialfall avviker, vanligt vis på grund av ett problem med en viss användare.
- **Överkomplext "CloudFiltering"** – samtidigt som antalet objekt är bra, finns det en risk för att skapa och överkomplicerat Sync-omfattning med många regler för synkronisering. Om det finns komplex logik för att inkludera/Exkludera objekt utöver ORGANISATIONSENHETs filtreringen, rekommenderas det att hantera den här logiken utanför Sync och märk objekt med ett enkelt "cloudFiltered"-attribut som kan flöda med en enkel Synkroniseringsregel.

#### <a name="azure-ad-connect-configuration-documenter"></a>Azure AD Connect konfigurations dokument

[Azure AD Connect konfigurations dokument](https://github.com/Microsoft/AADConnectConfigDocumenter) är ett verktyg som du kan använda för att skapa dokumentation av en Azure AD Connect-installation för att ge en bättre förståelse för Sync-konfigurationen, öka säkerheten för att komma åt saker och för att veta vad som har ändrats när du använde en ny version eller konfiguration av Azure AD Connect eller lagt till eller uppdaterat anpassade regler för synkronisering. De aktuella funktionerna i verktyget är:

- Dokumentation om den fullständiga konfigurationen av Azure AD Connect Sync.
- Dokumentation av ändringar i konfigurationen av två Azure AD Connect Sync-servrar eller ändringar från en specifik konfigurations bas linje.
- Generering av ett PowerShell-distributions skript för att migrera skillnader i synkroniserings regel eller anpassningar från en server till en annan.

## <a name="assignment-to-apps-and-resources"></a>Tilldelning till appar och resurser

### <a name="group-based-licensing-for-microsoft-cloud-services"></a>Gruppbaserad licensiering för Microsoft Cloud Services

Azure Active Directory effektiviserar hanteringen av licenser via [gruppbaserad licensiering](./active-directory-licensing-whatis-azure-portal.md) för Microsofts moln tjänster. På så sätt tillhandahåller IAM grupp infrastrukturen och delegerad hantering av dessa grupper till rätt team i organisationerna. Det finns flera sätt att konfigurera medlemskapet för grupper i Azure AD, inklusive:

- **Synkronisering från lokala** grupper kan komma från lokala kataloger, vilket kan vara en bra anpassning för organisationer som har upprättat grup hanterings processer som kan utökas för att tilldela licenser i Microsoft 365.

- **Attributbaserade/dynamiska** grupper kan skapas i molnet baserat på ett uttryck baserat på användarattribut, till exempel Department är "Sales". Azure AD upprätthåller medlemmarna i gruppen, så att den överensstämmer med det uttryck som definierats. Med den här typen av grupp för licens tilldelning kan du använda en attribut-baserad licens tilldelning som passar för organisationer som har hög data kvalitet i sin katalog.

- **Delegerad ägarskap** – grupper kan skapas i molnet och kan utses till ägare. På så sätt kan du ge företags ägare, till exempel samarbets team eller BI-team, möjlighet att definiera vem som ska ha åtkomst.

Om du för närvarande använder en manuell process för att tilldela licenser och komponenter till användare, rekommenderar vi att du implementerar gruppbaserad licensiering. Om den aktuella processen inte övervakar licens fel eller vad som är tilldelat till tillgängligt, bör du definiera förbättringar av processen för att lösa licensierings fel och övervaka licens tilldelningen.

En annan aspekt av licens hanteringen är definitionen av tjänste planer (komponenter i licensen) som ska aktive ras baserat på jobb funktioner i organisationen. Att bevilja åtkomst till tjänst planer som inte är nödvändiga kan leda till att användare ser verktyg i Office-portalen att de inte har tränats eller inte bör använda. Den kan köra ytterligare support volymer, onödig etablering och ge din efterlevnad och styrning i fara, till exempel när du konfigurerar OneDrive för företag till individer som kanske inte får dela innehåll.

Använd följande rikt linjer för att definiera tjänst planer för användare:

- Administratörer bör definiera "buntar" av tjänste planer som erbjuds till användare baserat på deras roll, till exempel Collar arbetare och golv arbetare.
- Skapa grupper efter kluster och tilldela licensen till service planen.
- Du kan också definiera ett attribut för att lagra paketen för användare.

> [!IMPORTANT]
> Gruppbaserad licensiering i Azure AD introducerar begreppet användare i ett licensierings fel tillstånd. Om du ser eventuella licens fel bör du omedelbart [identifiera och lösa](../enterprise-users/licensing-groups-resolve-problems.md) eventuella licens tilldelnings problem.

![En skärm bild av en dator skärms beskrivning som genereras automatiskt](./media/active-directory-ops-guide/active-directory-ops-img2.png)

#### <a name="lifecycle-management"></a>Livs cykel hantering

Om du för närvarande använder ett verktyg, till exempel [Microsoft Identity Manager](/microsoft-identity-manager/) eller ett system från tredje part, som förlitar sig på en lokal infrastruktur, rekommenderar vi att du avlastar tilldelningen från det befintliga verktyget, implementerar gruppbaserad licensiering och definierar en grupp livs cykel hantering baserat på [grupper](../enterprise-users/licensing-group-advanced.md#use-group-based-licensing-with-dynamic-groups). På samma sätt bör du, om din befintliga process inte har något konto för nya medarbetare eller anställda som lämnar organisationen, distribuera gruppbaserad licensiering baserat på dynamiska grupper och definiera en livs cykel för grupp medlemskap. Slutligen, om gruppbaserad licensiering distribueras mot lokala grupper som saknar livs cykel hantering, bör du överväga att använda moln grupper för att aktivera funktioner som delegerad ägarskap eller attributbaserade dynamiska medlemskap.

### <a name="assignment-of-apps-with-all-users-group"></a>Tilldelning av appar med gruppen "alla användare"

Resurs ägare kan tro att gruppen **alla användare** endast innehåller **företags anställda** när de faktiskt kan innehålla både **företags anställda** och **gäster**. Därför bör du vara särskilt försiktig när du använder gruppen **alla användare** för program tilldelning och beviljar åtkomst till resurser som SharePoint-innehåll eller program.

> [!IMPORTANT]
> Om gruppen **alla användare** är aktive rad och används för principer för villkorlig åtkomst, app eller resurs tilldelning, se till att [skydda gruppen](../external-identities/use-dynamic-groups.md) om du inte vill att den ska innehålla gäst användare. Dessutom bör du åtgärda licens tilldelningarna genom att skapa och tilldela till grupper som bara innehåller **företags anställda** . Å andra sidan, om du ser att gruppen **alla användare** är aktive rad men inte används för att bevilja åtkomst till resurser, se till att organisationens operativa vägledning är att avsiktligt använda gruppen (som omfattar både **företags anställda** och **gäster**).

### <a name="automated-user-provisioning-to-apps"></a>Automatiserad användar etablering för appar

[Automatiserad användar etablering](../app-provisioning/user-provisioning.md) för program är det bästa sättet att skapa en konsekvent etablering, avetablering och livs cykel för identiteter i flera system.

Om du för närvarande konfigurerar appar på ett ad hoc-sätt eller använder saker som CSV-filer, JIT eller en lokal lösning som inte hanterar livs cykel hantering, rekommenderar vi att du [implementerar program etablering](../app-provisioning/user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) med Azure AD för program som stöds och definierar ett konsekvent mönster för program som ännu inte stöds av Azure AD.

![Azure AD Provisioning-tjänst](./media/active-directory-ops-guide/active-directory-ops-img3.png)

### <a name="azure-ad-connect-delta-sync-cycle-baseline"></a>Bas linje för Azure AD Connect delta-synkronisering

Det är viktigt att förstå mängden ändringar i din organisation och se till att det inte tar för lång tid att ha en förutsägbar synkroniseringstid.

Standard frekvensen för [synkronisering av ändringar](../hybrid/how-to-connect-sync-feature-scheduler.md) är 30 minuter. Om delta-synkroniseringen tar längre tid än 30 minuter, eller om det finns betydande skillnader mellan delta-synkroniseringens prestanda, bör du undersöka och granska de [faktorer som påverkar Azure AD connectens prestanda](../hybrid/plan-connect-performance-factors.md).

#### <a name="azure-ad-connect-troubleshooting-recommended-reading"></a>Azure AD Connect fel sökning av Rekommenderad läsning

- [Förbereda katalog-attribut för synkronisering med Microsoft 365 med hjälp av IdFix-verktyget](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)
- [Azure AD Connect: fel sökning av fel vid synkronisering](../hybrid/tshoot-connect-sync-errors.md)

## <a name="summary"></a>Sammanfattning

Det finns fem aspekter av en säker identitets infrastruktur. Med den här listan kan du snabbt hitta och vidta nödvändiga åtgärder för att skydda och hantera livs cykeln för identiteter och deras rättigheter i din organisation.

- Tilldela ägare till viktiga uppgifter.
- Hitta och lös synkroniseringsproblem.
- Definiera en växlings strategi för haveri beredskap.
- Effektivisera hanteringen av licenser och tilldelning av appar.
- Automatisera användar etablering i appar.

## <a name="next-steps"></a>Nästa steg

Kom igång med [kontrollerna och åtgärderna för verifierings hantering](active-directory-ops-guide-auth.md).