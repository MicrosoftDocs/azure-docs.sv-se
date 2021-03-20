---
title: Översikt över Azure Blueprint
description: Förstå hur tjänsten Azure-ritningar ger dig möjlighet att skapa, definiera och distribuera artefakter i din Azure-miljö.
ms.date: 01/27/2021
ms.topic: overview
ms.openlocfilehash: f4ba77f5fcb376bf600d94997b0d6ba569f04f82
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919350"
---
# <a name="what-is-azure-blueprints"></a>Vad är Azure Blueprint?

Precis som en skiss tillåter en tekniker eller arkitekt att skissa designparametrarna för ett projekt kan Azure Blueprint göra det möjligt för molnarkitekter och centrala IT-grupper att definiera en upprepningsbar uppsättning med Azure-resurser som implementerar och tillämpar en organisations standarder, mönster och krav. Azure-ritningar gör det möjligt för utvecklings grupper att snabbt bygga och sätta igång nya miljöer med förtroende som de skapar inom organisationens kompatibilitet med en uppsättning inbyggda komponenter, till exempel nätverk, för att påskynda utvecklingen och leveransen.

Skisser är en deklarativ metod för att dirigera distribution av flera resursmallar och andra artefakter som:

- Rolltilldelningar
- Principtilldelningar
- Azure Resource Manager mallar (ARM-mallar)
- Resursgrupper

Azure Blueprints-tjänsten backas av det globalt distribuerade [Azure Cosmos DB](../../cosmos-db/introduction.md). Skissobjekt replikeras till flera Azure-regioner. Den här replikeringen ger låg latens, hög tillgänglighet och konsekvent åtkomst till dina skiss objekt, oavsett vilken region Azure-ritningar distribuerar dina resurser till.

## <a name="how-its-different-from-arm-templates"></a>Så här skiljer den sig från ARM-mallar

Tjänsten är utformad för att hjälpa till med att _konfigurera miljön_. Den här konfigurationen består ofta av en uppsättning resurs grupper, principer, roll tilldelningar och distributioner av ARM-mallar. En skiss är ett paket för att ta med var och en av dessa _artefakt_ typer och låter dig skapa och version av paketet, inklusive genom en pipeline för kontinuerlig integrering och kontinuerlig leverans (CI/CD). Slutligen tilldelas varje skiss till en prenumeration i en enda åtgärd som kan granskas och spåras.

Nästan allt som du vill inkludera för distribution i Azure-ritningar kan utföras med en ARM-mall. En ARM-mall är dock ett dokument som inte finns internt i Azure – varje lagras antingen lokalt eller i käll kontrollen. Mallen används för distribution av en eller flera Azure-resurser, men när resurserna har distribuerats finns det ingen aktiv anslutning eller relation till mallen.

Med Azure-ritningar bevaras relationen mellan skiss definitionen (vad _ska_ distribueras) och skiss tilldelningen (vad _som distribuerades_ ). Den här anslutningen har stöd för förbättrad spårning och granskning av distributioner. Azure-ritningar kan också uppgradera flera prenumerationer samtidigt som styrs av samma skiss.

Du behöver inte välja mellan en ARM-mall och en skiss. Varje skiss kan bestå av noll eller fler _artefakter_ för ARM-mallar. Det här stödet innebär att tidigare ansträngningar för att utveckla och underhålla ett bibliotek med ARM-mallar kan återanvändas i Azure-ritningar.

## <a name="how-its-different-from-azure-policy"></a>Skillnaden mot Azure Policy

En skiss är ett paket eller en container där du kan skapa fokusspecifika uppsättningar standarder, mönster och krav relaterade till implementeringen av Azure-molntjänster, säkerhet och design som kan återanvändas för att säkerställa konsekvens och efterlevnad.

En [princip](../policy/overview.md) är en standard för att tillåta och explicit neka system som fokuserar på resursegenskaper under distributionen och för redan befintliga resurser. Den stöder molnstyrning genom att bekräfta att resurser i en prenumeration uppfyller krav och standarder.

Om du tar med en princip i en skiss kan du skapa rätt mönster eller design när du utvärderar skissen. Principinkluderingen ser till att endast godkända eller förväntade ändringar kan göras i miljön för att skydda fortlöpande efterlevnad med skissens intention.

En princip kan inkluderas som en av många _artefakter_ i en skiss definition. Med skisser kan du även använda parametrar med principer och initiativ.

## <a name="blueprint-definition"></a>Definition av skisser

En skiss består av _artefakter_. Azure-ritningar stöder för närvarande följande resurser som artefakter:

|Resurs  | Hierarkialternativ| Beskrivning  |
|---------|---------|---------|
|Resursgrupper | Prenumeration | Skapa en ny resursgrupp för användning av andra artefakter i skissen.  Dessa resurs grupper för plats hållare gör det möjligt att organisera resurser exakt som du vill att de ska vara strukturerade och ger en omfattnings gräns för inkluderade principer och roll tilldelnings artefakter och ARM-mallar. |
|ARM-mall | Prenumeration, resursgrupp | Mallar, inklusive kapslade och länkade mallar, används för att skapa komplexa miljöer. Exempelmiljöer: en SharePoint-servergrupp, Azure Automation State Configuration eller en Log Analytics-arbetsyta. |
|Principtilldelning | Prenumeration, resursgrupp | Tillåter tilldelning av en princip eller ett initiativ till den prenumeration som skissen är tilldelad till. Principen eller initiativet måste vara inom omfånget för skissdefinitionens plats. Om principen eller initiativet har parametrar kan de parametrarna tilldelas vid skapandet av skissen eller under skisstilldelningen. |
|Rolltilldelning | Prenumeration, resursgrupp | Lägg till en befintlig användare eller grupp till en inbyggd roll för att se till att rätt personer alltid har rätt åtkomst till dina resurser. Rolltilldelningar kan definieras för hela prenumerationen eller kapslade till en specifik resursgrupp som ingår i skissen. |

### <a name="blueprint-definition-locations"></a>Skissdefinitionsplatser

När du skapar en skissdefinition ska du definiera var skissen har sparats. Skisser kan sparas till en [hanteringsgrupp](../management-groups/overview.md) som eller en prenumeration som du har **deltagaråtkomst** till. Om platsen är en hanteringsgrupp kan skissen tilldelas till valfri underordnad prenumeration för den hanteringsgruppen.

### <a name="blueprint-parameters"></a>Skissparametrar

Ritningar kan skicka parametrar till antingen en princip/ett initiativ eller en ARM-mall. När en _artefakt_ läggs till i en skiss kan författaren besluta sig för att tillhandahålla ett angivet värde för varje skisstilldelning eller tillåta att varje skisstilldelning anger ett värde för tilldelningstiden. Tack vare den här flexibiliteten får du möjlighet att definiera ett förinställt värde för alla användare av skissen eller göra det möjligt för beslutet att göras vid tidpunkten för tilldelningen.

> [!NOTE]
> En skiss kan ha sina egna parametrar, men de kan för närvarande bara skapas om en skiss genereras från REST API:et i stället för via portalen.

Mer information finns [Skissparametrar](./concepts/parameters.md).

### <a name="blueprint-publishing"></a>Publicering av skiss

När en skiss först skapas betraktas den som att den är i **utkastläge**. När den är redo att tilldelas måste den vara **publicerad**. Publicering kräver att du definierar en **versionssträng** (bokstäver, siffror och bindestreck med en maximal längd på 20 tecken) tillsammans med valfria **Ändra anteckningar**. **Versionen** särskiljer den från framtida ändringar i samma skiss och gör att varje version ska tilldelas. Den här versionshanteringen innebär också att olika **versioner** i samma skiss kan tilldelas till samma prenumeration. När ytterligare ändringar görs i skissen finns den **publicerade** 
 **versionen** fortfarande, precis som de **opublicerade ändringarna**. När ändringarna har slutförts är den uppdaterade skissen **publicerad** med en ny och unik **version** och kan nu också tilldelas.

## <a name="blueprint-assignment"></a>Skisstilldelning

Varje **publicerad** **version** av en skiss kan tilldelas (med en maximal namn längd på 90 tecken) till en befintlig hanterings grupp eller prenumeration. I portalen får skissen som standard den **version** som **publicerades** senast. Om det finns artefakt parametrar eller skiss parametrar definieras parametrarna under tilldelnings processen.

> [!NOTE]
> Att tilldela en skiss definition till en hanterings grupp innebär att tilldelnings objekt finns i hanterings gruppen. Distributionen av artefakter är fortfarande riktad mot en prenumeration. Om du vill utföra en tilldelning av hanterings grupper måste [skapa eller uppdatera REST API](/rest/api/blueprints/assignments/createorupdate) användas och begär ande texten måste innehålla ett värde för `properties.scope` att definiera mål prenumerationen.

## <a name="permissions-in-azure-blueprints"></a>Behörigheter i Azure Blueprint

Om du vill använda ritningar måste du beviljas behörigheter via [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md). Om du vill läsa eller Visa en skiss i Azure Portal måste ditt konto ha Läs behörighet till omfånget där skiss definitionen finns.

Om du vill skapa skisser måste ditt konto ha följande behörigheter:

- `Microsoft.Blueprint/blueprints/write` – Skapa en skissdefinition
- `Microsoft.Blueprint/blueprints/artifacts/write` – Skapa artefakter på en skissdefinition
- `Microsoft.Blueprint/blueprints/versions/write` – Publicera en skiss

Om du vill ta bort skisser måste ditt konto ha följande behörigheter:

- `Microsoft.Blueprint/blueprints/delete`
- `Microsoft.Blueprint/blueprints/artifacts/delete`
- `Microsoft.Blueprint/blueprints/versions/delete`

> [!NOTE]
> Behörigheter för skissdefinition måste beviljas eller ärvas i det omfång för hanteringsgrupp eller prenumeration där den sparas.

Om du vill tilldela eller ta bort tilldelningar av skisser måste ditt konto ha följande behörigheter:

- `Microsoft.Blueprint/blueprintAssignments/write` – Tilldela en skiss
- `Microsoft.Blueprint/blueprintAssignments/delete` – Ta bort tilldelning av en skiss

> [!NOTE]
> Tilldelningar för skissen skapas på en prenumeration. Behörigheterna för tilldelning och för att ta bort tilldelning av behörigheter måste beviljas för eller ärvas till en prenumerationsomfattning.

Följande inbyggda roller är tillgängliga:

|Azure-roll | Beskrivning |
|-|-|
|[Ägare](../../role-based-access-control/built-in-roles.md#owner) | Förutom andra behörigheter innehåller alla Azure Blueprint-relaterade behörigheter. |
|[Deltagare](../../role-based-access-control/built-in-roles.md#contributor) | Förutom andra behörigheter kan skapa och ta bort skiss definitioner, men har inte behörighet för skiss tilldelning. |
|[Skiss deltagare](../../role-based-access-control/built-in-roles.md#blueprint-contributor) | Kan hantera skiss definitioner, men tilldela dem inte. |
|[Skiss operator](../../role-based-access-control/built-in-roles.md#blueprint-operator) | Kan tilldela befintliga publicerade ritningar, men kan inte skapa nya skiss definitioner. Skiss tilldelning fungerar bara om tilldelningen görs med en tilldelad hanterad identitet. |

Om dessa inbyggda roller inte motsvarar dina behov kan du skapa en [anpassad roll](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> Om du använder en systemtilldelad hanterad identitet, kräver tjänstens huvud namn för Azure-ritningar **ägar** rollen för den tilldelade prenumerationen för att distributionen ska kunna aktive ras. Om du använder portalen är den här rollen automatiskt beviljad och återkallad för distributionen. Om du använder REST API måste den här rollen måste beviljas manuellt, men återkallas fortfarande automatiskt när distributionen är klar. Om du använder en användardefinierad hanterad identitet behöver endast användaren som skapar skiss tilldelningen `Microsoft.Blueprint/blueprintAssignments/write` behörigheten, som ingår i de inbyggda rollerna **ägare** och **skiss-operator** .

## <a name="naming-limits"></a>Namngivnings gränser

Följande begränsningar finns för vissa fält:

|Objekt|Fält|Tillåtna tecken|Max. Längd|
|-|-|-|-|
|Skiss|Name|bokstäver, siffror, bindestreck och punkter|48|
|Skiss|Version|bokstäver, siffror, bindestreck och punkter|20|
|Skisstilldelning|Name|bokstäver, siffror, bindestreck och punkter|90|
|Skiss artefakt|Name|bokstäver, siffror, bindestreck och punkter|48|

## <a name="video-overview"></a>Videoöversikt

Följande översikt över Azure Blueprints kommer från Azure Fridays. En nedladdningsbar video finns på [Azure Fridays - An overview of Azure Blueprints](https://channel9.msdn.com/Shows/Azure-Friday/An-overview-of-Azure-Blueprints) (Azure Fridays – En översikt över Azure Blueprints) på Channel 9.

> [!VIDEO https://www.youtube.com/embed/cQ9D-d6KkMY]

## <a name="next-steps"></a>Nästa steg

- [Skapa en skiss – Portal](./create-blueprint-portal.md).
- [Skapa en skiss – PowerShell](./create-blueprint-powershell.md).
- [Skapa en skiss-REST API](./create-blueprint-rest-api.md).