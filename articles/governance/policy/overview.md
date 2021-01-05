---
title: Översikt över Azure Policy
description: Azure Policy är en tjänst i Azure som används för att skapa, tilldela och hantera principdefinitioner i Azure-miljön.
ms.date: 10/05/2020
ms.topic: overview
ms.openlocfilehash: 96fd29b5e3d24bc3e678461a95005e004a8a3a80
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803960"
---
# <a name="what-is-azure-policy"></a>Vad är Azure Policy?

Azure Policy hjälper till att framtvinga organisationsstandarder och utvärdera efterlevnad i stor skala. Med hjälp av instrument panelen för efterlevnad får du en sammanställd vy som utvärderar miljöns övergripande tillstånd, med möjlighet att öka detalj nivån till per-resurs-granularitet per princip. Det hjälper också till att se till att dina resurser efterlevs genom Mass reparation av befintliga resurser och automatisk reparation av nya resurser.

Vanliga användnings fall för Azure Policy inkluderar implementering av styrning för resurs konsekvens, regelefterlevnad, säkerhet, kostnad och hantering. Princip definitioner för dessa vanliga användnings fall är redan tillgängliga i din Azure-miljö som inbyggda moduler för att hjälpa dig att komma igång.

Alla Azure Policy data och objekt krypteras i vila. Mer information finns i [Azure Data Encryption i vila](../../security/fundamentals/encryption-atrest.md).

## <a name="overview"></a>Översikt

Azure Policy utvärderar resurser i Azure genom att jämföra egenskaperna för dessa resurser med affärs regler. Dessa affärs regler, som beskrivs i [JSON-format](./concepts/definition-structure.md), kallas för [princip definitioner](#policy-definition). För att förenkla hanteringen kan flera affärs Regler grupperas tillsammans för att bilda ett [princip initiativ](#initiative-definition) (kallas ibland för en _policySet_). När dina affärs regler har skapats [tilldelas](#assignments) princip definitionen eller initiativet alla resurser som stöds av Azure, till exempel [hanterings grupper](../management-groups/overview.md), prenumerationer, [resurs grupper](../../azure-resource-manager/management/overview.md#resource-groups)eller enskilda resurser. Tilldelningen gäller för alla resurser inom [Resource Manager-omfånget](../../azure-resource-manager/management/overview.md#understand-scope) för tilldelningen. Under omfattningar kan undantas vid behov. Mer information finns [i omfattning i Azure policy](./concepts/scope.md).

Azure Policy använder ett [JSON-format](./concepts/definition-structure.md) för att forma logiken som utvärderingen använder för att avgöra om en resurs är kompatibel eller inte. Definitioner inkluderar metadata och princip regeln. Den definierade regeln kan använda funktioner, parametrar, logiska operatorer, villkor och egenskaps- [alias](./concepts/definition-structure.md#aliases) för att matcha exakt det scenario du önskar. Princip regeln avgör vilka resurser i tilldelnings omfånget som ska utvärderas.

### <a name="understand-evaluation-outcomes"></a>Förstå utvärderings resultat

Resurserna utvärderas vid bestämda tidpunkter under resurs livs cykeln, livs cykeln för princip tilldelning och för regelbunden utvärdering av efterlevnad. Följande är de tider eller händelser som gör att en resurs utvärderas:

- En resurs skapas, uppdateras eller tas bort i ett omfång med en princip tilldelning.
- En princip eller ett initiativ har nyligen tilldelats ett omfång.
- En princip eller ett initiativ som redan har tilldelats ett omfång uppdateras.
- Under utvärderings cykeln för standard kompatibilitet, som inträffar en gång var 24: e timme.

Detaljerad information om när och hur princip utvärderingen sker finns i [utvärderings utlösare](./how-to/get-compliance-data.md#evaluation-triggers).

### <a name="control-the-response-to-an-evaluation"></a>Kontrol lera svaret på en utvärdering

Affärs regler för hantering av icke-kompatibla resurser varierar kraftigt mellan organisationer. Exempel på hur en organisation vill att plattformen ska svara på en icke-kompatibel resurs är:

- Neka resurs ändringen
- Logga ändringen i resursen
- Ändra resursen innan ändringen
- Ändra resursen efter ändringen
- Distribuera relaterade kompatibla resurser

Azure Policy gör vart och ett av dessa affärs svar möjligt genom tillämpning av [effekter](./concepts/effects.md). Effekter anges i **princip regel** delen i [princip definitionen](./concepts/definition-structure.md).

### <a name="remediate-non-compliant-resources"></a>Åtgärda icke-kompatibla resurser

Även om dessa effekter främst påverkar en resurs när resursen skapas eller uppdateras, kan Azure Policy också hantera befintliga icke-kompatibla resurser utan att behöva ändra den resursen. Mer information om hur du gör befintliga resurser kompatibla finns i [Reparera resurser](./how-to/remediate-resources.md).

### <a name="video-overview"></a>Videoöversikt

Följande översikt över Azure Policy är från Build 2018. För nedladdning av bilder eller video besöker du sidan om att [styra Azure-miljön via Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) på Channel 9.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="getting-started"></a>Komma igång

### <a name="azure-policy-and-azure-rbac"></a>Azure Policy och Azure RBAC

Det finns några viktiga skillnader mellan Azure Policy och rollbaserad åtkomst kontroll i Azure (Azure RBAC). Azure Policy utvärderar tillstånd genom att undersöka egenskaper för resurser som representeras i Resource Manager och egenskaper för vissa resurs leverantörer. Azure Policy begränsar inte åtgärder (kallas även _åtgärder_). Azure Policy ser till att resurs statusen är kompatibel med dina affärs regler utan att behöva oroa sig för vem som gjorde ändringen eller vem som har behörighet att göra en ändring.

Azure RBAC fokuserar på att hantera användar [åtgärder](../../role-based-access-control/resource-provider-operations.md) i olika omfång. Om kontrollen av en åtgärd krävs är Azure RBAC rätt verktyg för att använda. Även om en enskild person har åtkomst för att utföra en åtgärd, eller om resultatet är en icke-kompatibel resurs, blockerar Azure Policy fortfarande åtgärden Skapa eller uppdatera.

Kombinationen av Azure RBAC och Azure Policy ger fullständig omfattnings kontroll i Azure.

### <a name="azure-rbac-permissions-in-azure-policy"></a>Azure RBAC-behörigheter i Azure Policy

Azure Policy har flera behörigheter, som kallas åtgärder, i två olika resursprovidrar:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft. PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Många inbyggda roller beviljar behörighet till Azure Policy-resurser. Rollen som **deltagar resurs princip** omfattar de flesta Azure policy åtgärder. **Ägare** har fullständiga behörigheter. Både **deltagare** och **läsare** har åtkomst till alla _Läs_ Azure policy-åtgärder. **Deltagare** kan utlösa resurs reparation, men kan inte _skapa_ definitioner eller tilldelningar. **Administratör för användar åtkomst** krävs för att ge den hanterade identiteten på **deployIfNotExists** eller **ändra** tilldelningar som krävs.

Om ingen av de inbyggda rollerna har de behörigheter som krävs skapar du en [anpassad roll](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> Den hanterade identiteten för en **deployIfNotExists** eller **ändra** princip tilldelning måste ha tillräcklig behörighet för att skapa eller uppdatera mål-resurser. Mer information finns i [Konfigurera princip definitioner för reparation](./how-to/remediate-resources.md#configure-policy-definition).

### <a name="resources-covered-by-azure-policy"></a>Resurser som omfattas av Azure Policy

Azure Policy utvärderar alla resurser i Azure och Arc-aktiverade resurser. För vissa resurs leverantörer, till exempel [gäst konfiguration](./concepts/guest-configuration.md), [Azure Kubernetes service](../../aks/intro-kubernetes.md)och [Azure Key Vault](../../key-vault/general/overview.md), finns det en djupare integrering för hantering av inställningar och objekt. Mer information finns i [resurs leverantörs lägen](./concepts/definition-structure.md).

### <a name="recommendations-for-managing-policies"></a>Rekommendationer för principhantering

Här följer några råd och tips att tänka på:

- Börja med en spårningsseffekt i stället för en nekandeeffekt för att spåra den påverkan principdefinitionen får på resurserna i miljön. Om du redan har skript på plats för att automatiskt skala dina program kan dessa automatiserade uppgifter eventuellt blockeras om du anger en nekandeeffekt.

- Ta hänsyn till på organisationens hierarkier när du skapar definitioner och tilldelningar. Vi rekommenderar att du skapar definitioner på högre nivåer, som på hanteringsgrupps- eller prenumerationsnivå. Skapa sedan tilldelningen på nästa underordnade nivå. Om du skapar en definition för en hanteringsgrupp, kan tilldelningen begränsas till en prenumeration eller resursgrupp inom den hanteringsgruppen.

- Vi rekommenderar att du skapar och tilldelar initiativdefinitioner även för en enskild principdefinition.
  Om du som exempel har principdefinitionen _policyDefA_, så skapar du den under initiativdefinitionen _initiativeDefC_. Om du skapar en annan principdefinition senare för _policyDefB_ med mål som liknar dem för _policyDefA_, kan du lägga till den under _initiativeDefC_ och spåra dem tillsammans.

- När du har skapat en initiativ tilldelning blir de princip definitioner som läggs till i initiativet också en del av initiativets tilldelningar.

- När en initiativtilldelning utvärderas, utvärderas även alla principer inom initiativet.
  Om du behöver utvärdera en princip individuellt är det bättre att inte inkludera den i ett initiativ.

## <a name="azure-policy-objects"></a>Azure Policy objekt

### <a name="policy-definition"></a>Principdefinition

Resan med att skapa och implementera en princip i Azure Policy börjar med skapandet av en principdefinition. Varje principdefinition har villkor för när den ska tillämpas. Och den har en definierad effekt som träder ikraft om villkoren är uppfyllda.

Vi erbjuder flera inbyggda principer som är tillgängliga för dig som standard i Azure Policy. Exempel:

- **Tillåtna lagrings konto SKU: er** (neka): avgör om ett lagrings konto som distribueras är inom en uppsättning SKU-storlekar. Effekten är att neka alla lagringskonton som inte överensstämmer med uppsättningen definierade SKU-storlekar.
- **Tillåten resurs typ** (neka): definierar de resurs typer som du kan distribuera. Effekten är att neka alla resurser som inte finns på den definierade listan.
- **Tillåtna platser** (neka): begränsar de tillgängliga platserna för nya resurser. Effekten används för att genomdriva kraven på geo-efterlevnad.
- **Tillåtna virtuella dator-SKU** : er (neka): anger en uppsättning SKU: er för virtuella datorer som du kan distribuera.
- **Lägg till en tagg till resurser** (ändra): använder en obligatorisk tagg och dess standardvärde om den inte anges i distributions förfrågan.
- **Ej tillåtna resurs typer** (neka): förhindrar att en lista över resurs typer distribueras.

För att implementera dessa principdefinitioner (både inbyggda och anpassade definitioner) måste du tilldela dem. Du kan tilldela de här principerna via Azure-portalen, PowerShell eller Azure CLI.

Principutvärdering sker med flera olika åtgärder, till exempel tilldelning av principer eller principuppdateringar. En fullständig lista finns i [Principutvärderingsutlösare](./how-to/get-compliance-data.md#evaluation-triggers).

Läs mer om principdefinitionernas strukturer i artikeln, [struktur för principdefinitioner](./concepts/definition-structure.md).

Principparametrar underlättar hanteringen av principer genom att minska antalet principdefinitioner du måste skapa. Du kan definiera parametrar när du skapar en principdefinition så att den blir mer allmän. Sedan kan du återanvända den principdefinitionen för olika scenarier. Det gör du genom att ange olika värden när du tilldelar principdefinitionen. Till exempel ange du en uppsättning platser för en prenumeration.

Parametrar definieras när du skapar en principdefinition. När en parameter definieras tilldelas den ett namn och eventuellt ett bestämt värde. Du kan till exempel definiera en parameter för en princip med titeln _plats_. Sedan kan du ge den olika värden som _EastUS_ eller _WestUS_ när du tilldelar en princip.

Mer information om principparametrar finns i [Struktur för definitioner – parametrar](./concepts/definition-structure.md#parameters).

### <a name="initiative-definition"></a>Initiativdefinition

En initiativdefinition är en samling principdefinitioner som är skräddarsydda för att uppnå ett enda övergripande mål. Initiativdefinitioner gör det enklare att hantera och tilldela principdefinitioner genom att de grupperar en uppsättning principer som ett enda objekt. Du kan till exempel skapa ett initiativ med titeln **Aktivera övervakning i Azure Security Center**, med målet att övervaka alla tillgängliga säkerhetsrekommendationer i Azure Security Center.

> [!NOTE]
> SDK, till exempel Azure CLI och Azure PowerShell, använder egenskaper och parametrar som heter **PolicySet** för att referera till initiativ.

Under det här initiativet skulle du ha principdefinitioner som dessa:

- **Övervaka okrypterade SQL-databaser i Security Center** – för att övervaka okrypterade SQL-databaser och -servrar.
- **Övervaka OS-säkerhetsproblem i Security Center** – för att övervaka servrar som inte uppfyller grundkonfigurationen.
- **Övervaka saknad Endpoint Protection i Security Center** – för att övervaka servrar utan en installerad Endpoint Protection-agent.

Precis som principparametrar underlättar initiativparametrar initiativhanteringen genom att minska redundansen. Initiativparametrar är parametrar som används av principdefinitionerna inom ett initiativ.

Ta till exempel scenariot där du har en initiativdefinition, **initiativeC**, med principdefinitionerna **policyA** och **policyB** som vardera förväntar sig olika typer av parametrar:

| Princip | Parameternamn |Parametertyp  |Anteckning |
|---|---|---|---|
| principA | allowedLocations | matris  |Den här parametern förväntar sig en lista med strängar för ett värde eftersom parametertypen har definierats som en matris |
| principB | allowedSingleLocation |sträng |Den här parametern förväntar sig ett ord som värde eftersom parametertypen har definierats som en sträng |

I det här scenariot, när du definierar initiativparametrar för **initiativC**, har du tre alternativ:

- Använd parametrarna för principdefinitionerna i det här initiativet. I det här exemplet blir _allowedLocations_ och _allowedSingleLocation_ initiativparametrar för **initiativC**.
- Ange värden för parametrarna för principdefinitionerna i den här initiativdefinitionen. I det här exemplet kan du ange en lista över **platser som ska** användas för att ställa in parameter- **allowedLocations** och **principb** parameter – **allowedSingleLocation**. Du kan också ange värden när du tilldelar det här initiativet.
- Ange en lista med alternativ _värden_ som kan användas när du tilldelar det här initiativet. När du tilldelar det här initiativet kan ärvda parametrarna från principdefinitionerna inom initiativet endast ha värden från den här listan.

När du skapar värdealternativ i en initiativdefinition kan du inte ange ett annat värde under initiativtilldelningen eftersom det inte ingår i listan.

Om du vill veta mer om strukturerna för initiativ definitioner granskar du [initiativ definitions strukturen](./concepts/initiative-definition-structure.md).

### <a name="assignments"></a>Tilldelningar

En tilldelning är en princip definition eller ett initiativ som har tilldelats för att äga rum inom ett angivet omfång. Det här omfånget kan vara ett intervall från en [hanterings grupp](../management-groups/overview.md) till en enskild resurs. Termen _definitions område_ syftar på alla resurser, resurs grupper, prenumerationer eller hanterings grupper som definitionen är tilldelad till. Tilldelningar ärvs av alla underordnade resurser. Den här designen innebär att en definition som tillämpas på en resurs grupp också tillämpas på resurser i den resurs gruppen. Du kan dock undanta ett under omfång från tilldelningen.

I prenumerations omfattningen kan du till exempel tilldela en definition som förhindrar skapandet av nätverks resurser. Du kan undanta en resursgrupp i prenumerationen som är avsedd för nätverksinfrastruktur. Du beviljar därefter åtkomst till den här nätverksresursgruppen för användare som du litar på för att skapa nätverksresurser.

I ett annat exempel kanske du vill tilldela en resurs typ Tillåt List definition på hanterings grupps nivå. Sedan tilldelar du en mer tillåtande princip (vilket tillåter fler resurs typer) i en underordnad hanterings grupp eller till och med direkt på prenumerationer. Det här exemplet skulle dock inte fungera eftersom Azure Policy är ett explicit Deny system. I stället måste du undanta den underordnade hanterings gruppen eller prenumerationen från tilldelningen på hanterings grupps nivå. Tilldela sedan den mer tillåtna definitionen för den underordnade hanterings gruppen eller prenumerations nivån. Om en tilldelning resulterar i att en resurs får åtkomst, är det enda sättet att tillåta resursen att ändra den nekande tilldelningen.

Mer information om hur du ställer in tilldelningar via portalen finns i [skapa en princip tilldelning för att identifiera icke-kompatibla resurser i Azure-miljön](./assign-policy-portal.md). Steg för [PowerShell](./assign-policy-powershell.md) och [Azure CLI](./assign-policy-azurecli.md) är också tillgängliga. Information om tilldelnings strukturen finns i avsnittet [tilldelningar](./concepts/assignment-structure.md).

## <a name="maximum-count-of-azure-policy-objects"></a>Maximalt antal Azure Policy objekt

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över Azure Policy och några av de centrala begreppen föreslår vi följande som nästa steg:

- [Granska princip definitions strukturen](./concepts/definition-structure.md).
- [Tilldela en princip definition med hjälp av portalen](./assign-policy-portal.md).
