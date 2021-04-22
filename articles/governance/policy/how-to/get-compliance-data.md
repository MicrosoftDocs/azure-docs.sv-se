---
title: Hämta data om principefterlevnad
description: Azure Policy utvärderingar och effekter avgör efterlevnaden. Lär dig hur du hämtar efterlevnadsinformation för dina Azure-resurser.
ms.date: 04/19/2021
ms.topic: how-to
ms.openlocfilehash: e1a9a7fcbbcbd7f490b2f665b40c7ed922ec61ee
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864603"
---
# <a name="get-compliance-data-of-azure-resources"></a>Hämta efterlevnadsdata för Azure-resurser

En av de största fördelarna med Azure Policy är den insikt och de kontroller som den ger över resurser i en prenumeration eller [hanteringsgrupp](../../management-groups/overview.md) med prenumerationer. Den här kontrollen kan användas på många olika sätt, till exempel för att förhindra att resurser skapas på fel plats, framtvinga gemensam och konsekvent tagganvändning eller granska befintliga resurser för lämpliga konfigurationer och inställningar. I samtliga fall genereras data av Azure Policy så att du kan förstå kompatibilitetstillståndet för din miljö.

Det finns flera sätt att komma åt efterlevnadsinformationen som genereras av dina princip- och initiativtilldelningar:

- Använda [Azure Portal](#portal)
- Via [kommandoradsskript](#command-line)

Innan vi tittar på metoderna för att rapportera om efterlevnad ska vi titta på när efterlevnadsinformationen uppdateras och den frekvens och de händelser som utlöser en utvärderingscykel.

> [!WARNING]
> Om kompatibilitetstillståndet rapporteras som Inte registrerad kontrollerar du att resursprovidern **Microsoft.PolicyInsights** har registrerats och att användaren har rätt behörigheter för rollbaserad åtkomst (Azure RBAC) enligt beskrivningen i [Azure RBAC-behörigheter i Azure Policy](../overview.md#azure-rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Utvärderingsutlösare

Resultatet av en slutförd utvärderingscykel är tillgängliga i `Microsoft.PolicyInsights` resursprovidern via `PolicyStates` - och `PolicyEvents` -åtgärder. Mer information om åtgärderna för Azure Policy Insights REST API finns i [Azure Policy Insights](/rest/api/policy/).

Utvärderingar av tilldelade principer och initiativ sker som ett resultat av olika händelser:

- En princip eller ett initiativ har nyligen tilldelats till ett omfång. Det tar cirka 30 minuter för tilldelningen att tillämpas på det definierade omfånget. När utvärderingscykeln har tillämpats börjar utvärderingscykeln för resurser inom det omfånget mot den nyligen tilldelade principen eller initiativet, och beroende på de effekter som används av principen eller initiativet markeras resurserna som kompatibla, icke-kompatibla eller undantagna. En stor princip eller ett stort initiativ som utvärderas mot ett stort resursomfång kan ta tid. Därför finns det ingen fördefinierad förväntan på när utvärderingscykeln har slutförts. När det är klart är uppdaterade kompatibilitetsresultat tillgängliga i portalen och IKS.

- En princip eller ett initiativ som redan har tilldelats till ett omfång uppdateras. Utvärderingscykeln och tidpunkten för det här scenariot är samma som för en ny tilldelning till ett omfång.

- En resurs distribueras till eller uppdateras inom ett omfång med en tilldelning via Azure Resource Manager, REST API eller ett SDK som stöds. I det här scenariot blir information om effekthändelsen (lägga till, granska, neka, distribuera) och kompatibel status för den enskilda resursen tillgänglig i portalen och SDK:er cirka 15 minuter senare. Den här händelsen orsakar ingen utvärdering av andra resurser.

- En prenumeration (resurstyp `Microsoft.Resource/subscriptions` ) skapas eller flyttas i en hanteringsgruppshierarki med en tilldelad principdefinition som riktar in sig på prenumerationens resurstyp. [](../../management-groups/overview.md) Utvärdering av de prenumerationseffekter som stöds (granskning, auditIfNotExist, deployIfNotExists, ändra), loggning och eventuella reparationsåtgärder tar cirka 30 minuter.

- Ett [principundantag](../concepts/exemption-structure.md) skapas, uppdateras eller tas bort. I det här scenariot utvärderas motsvarande tilldelning för det definierade undantagsomfånget.

- Utvärderingscykel för standardefterlevnad. Var 24:e timme omvärderas tilldelningarna automatiskt. En stor princip eller ett initiativ med många resurser kan ta tid, så det finns ingen fördefinierad förväntan på när utvärderingscykeln har slutförts. När det är klart är uppdaterade kompatibilitetsresultat tillgängliga i portalen och IKS.

- [Resursprovidern](../concepts/guest-configuration.md) för gästkonfiguration uppdateras med efterlevnadsinformation av en hanterad resurs.

- Genomsökning på begäran

### <a name="on-demand-evaluation-scan"></a>Utvärderingsgenomsökning på begäran

En utvärderingsgenomsökning för en prenumeration eller en resursgrupp kan startas med Azure CLI, Azure PowerShell, ett anrop till REST API eller med hjälp [av GitHub-åtgärden Azure Policy Compliance Scan](https://github.com/marketplace/actions/azure-policy-compliance-scan).
Den här genomsökningen är en asynkron process.

#### <a name="on-demand-evaluation-scan---github-action"></a>Utvärderingsgenomsökning på begäran – GitHub Action

Använd [åtgärden Azure Policy Compliance Scan](https://github.com/marketplace/actions/azure-policy-compliance-scan) för att utlösa en utvärderingsgenomsökning på begäran från [ditt GitHub-arbetsflöde](https://docs.github.com/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) på en eller flera resurser, resursgrupper eller prenumerationer och porta arbetsflödet baserat på kompatibilitetstillståndet för resurser. Du kan också konfigurera arbetsflödet så att det körs vid en schemalagd tidpunkt så att du får den senaste kompatibilitetsstatusen vid en lämplig tidpunkt. Den här GitHub-åtgärden kan också generera en rapport om kompatibilitetstillståndet för skannade resurser för ytterligare analys eller arkivering.

I följande exempel körs en kompatibilitetssökning för en prenumeration. 

```yaml
on:
  schedule:    
    - cron:  '0 8 * * *'  # runs every morning 8am
jobs:
  assess-policy-compliance:    
    runs-on: ubuntu-latest
    steps:         
    - name: Login to Azure
      uses: azure/login@v1
      with:
        creds: ${{secrets.AZURE_CREDENTIALS}} 

    
    - name: Check for resource compliance
      uses: azure/policy-compliance-scan@v0
      with:
        scopes: |
          /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

Mer information och arbetsflödesexempel finns i [GitHub Action for Azure Policy Compliance Scan repo](https://github.com/Azure/policy-compliance-scan).

#### <a name="on-demand-evaluation-scan---azure-cli"></a>Utvärderingsgenomsökning på begäran – Azure CLI

Kompatibilitetsgenomsökningen startas med kommandot [az policy state trigger-scan.](/cli/azure/policy/state#az_policy_state_trigger_scan)

Som standard startar `az policy state trigger-scan` en utvärdering för alla resurser i den aktuella prenumerationen. Om du vill starta en utvärdering av en specifik resursgrupp använder du **parametern resource-group.** I följande exempel startas en kompatibilitetssökning i den aktuella prenumerationen för _resursgruppen MyRG:_

```azurecli-interactive
az policy state trigger-scan --resource-group "MyRG"
```

Du kan välja att inte vänta tills den asynkrona processen har slutförts innan du fortsätter med **parametern no-wait.**

#### <a name="on-demand-evaluation-scan---azure-powershell"></a>Utvärderingsgenomsökning på begäran – Azure PowerShell

Kompatibilitetsgenomsökningen startas med cmdleten [Start-AzPolicyComplianceScan.](/powershell/module/az.policyinsights/start-azpolicycompliancescan)

Som standard startar `Start-AzPolicyComplianceScan` en utvärdering för alla resurser i den aktuella prenumerationen. Om du vill starta en utvärdering av en specifik resursgrupp använder du **parametern ResourceGroupName.** I följande exempel startas en kompatibilitetssökning i den aktuella prenumerationen för _resursgruppen MyRG:_

```azurepowershell-interactive
Start-AzPolicyComplianceScan -ResourceGroupName 'MyRG'
```

Du kan ha PowerShell-väntan på att det asynkrona anropet ska slutföras innan du ger resultatutdata eller att det körs i bakgrunden som ett [jobb](/powershell/module/microsoft.powershell.core/about/about_jobs). Om du vill använda ett PowerShell-jobb för att köra kompatibilitetsgenomsökningen i bakgrunden använder du **parametern AsJob** och anger värdet till ett -objekt, till exempel `$job` i det här exemplet:

```azurepowershell-interactive
$job = Start-AzPolicyComplianceScan -AsJob
```

Du kan kontrollera jobbets status genom att kontrollera `$job` objektet. Jobbet är av typen `Microsoft.Azure.Commands.Common.AzureLongRunningJob` . Använd `Get-Member` på `$job` -objektet för att se tillgängliga egenskaper och metoder.

När kompatibilitetsgenomsökningen körs ger kontroll `$job` av objektet resultat som dessa:

```azurepowershell-interactive
$job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
2      Long Running O… AzureLongRunni… Running       True            localhost            Start-AzPolicyCompliance…
```

När kompatibilitetsgenomsökningen är klar ändras **egenskapen Tillstånd** till _Slutförd._

#### <a name="on-demand-evaluation-scan---rest"></a>Utvärderingsgenomsökning på begäran – REST

Som en asynkron process väntar REST-slutpunkten för att starta genomsökningen inte tills genomsökningen är klar för att svara. I stället innehåller den en URI för att fråga efter status för den begärda utvärderingen.

I varje REST API-URI finns det variabler som används och som du måste ersätta med egna värden:

- `{YourRG}` – Ersätt med namnet på din resursgrupp
- `{subscriptionId}` – Ersätt med ditt prenumerations-ID

Genomsökningen stöder utvärdering av resurser i en prenumeration eller i en resursgrupp. Starta en genomsökning efter omfång med REST API **POST-kommando** med hjälp av följande URI-strukturer:

- Prenumeration

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

- Resursgrupp

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

Anropet returnerar **statusen 202 Godkänt.** I svarshuvudet ingår en **Location-egenskap** med följande format:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2019-10-01
```

`{ResourceContainerGUID}` genereras statiskt för det begärda omfånget. Om ett omfång redan kör en genomsökning på begäran startas inte en ny genomsökning. I stället får den nya begäran samma `{ResourceContainerGUID}` **plats-URI** för status. Ett REST API **GET-kommando** till **plats-URI:en** returnerar ett **202 Accepted (202-godkänt)** medan utvärderingen pågår. När utvärderingsgenomsökningen har slutförts returneras **statusen 200 OK.** Brödtexten i en slutförd genomsökning är ett JSON-svar med statusen :

```json
{
    "status": "Succeeded"
}
```

#### <a name="on-demand-evaluation-scan---visual-studio-code"></a>Utvärderingsgenomsökning på begäran – Visual Studio Code

Tillägget Azure Policy för Visual Studio kod kan köra en utvärderingsgenomsökning för en specifik resurs. Den här genomsökningen är en synkron process, till skillnad från Azure PowerShell och REST-metoderna.
Mer information och steg finns [i Utvärdering på begäran med VS Code-tillägget](./extension-for-vscode.md#on-demand-evaluation-scan).

## <a name="how-compliance-works"></a>Så här fungerar efterlevnad

I en tilldelning är en resurs **icke-kompatibel om** den inte följer princip- eller initiativregler och inte är _undantagen_. I följande tabell visas hur olika principeffekter fungerar med villkorsutvärderingen för det resulterande kompatibilitetstillståndet:

| Resurstillstånd | Effekt | Principutvärdering | Kompatibilitetsstatus |
| --- | --- | --- | --- |
| Ny eller uppdaterad? | Granska, ändra, AuditIfNotExist | Sant | Icke-kompatibel |
| Ny eller uppdaterad? | Granska, ändra, AuditIfNotExist | Falskt | Kompatibel |
| Finns | Deny, Audit, Append, Modify, DeployIfNotExist, AuditIfNotExist | Sant | Icke-kompatibel |
| Finns | Deny, Audit, Append, Modify, DeployIfNotExist, AuditIfNotExist | Falskt | Kompatibel |

> [!NOTE]
> Effekterna DeployIfNotExist och AuditIfNotExist kräver att IF-instruktionen är TRUE och att villkoret är FALSE för att vara icke-kompatibelt. När det är TRUE utlöser IF-villkoret utvärdering av villkoret Finns för de relaterade resurserna.

Anta till exempel att du har en resursgrupp – ContsoRG, med vissa lagringskonton (markerade i rött) som exponeras för offentliga nätverk.

:::image type="complex" source="../media/getting-compliance-data/resource-group01.png" alt-text="Diagram över lagringskonton som exponeras för offentliga nätverk i resursgruppen Contoso R G." border="false":::
   Diagram som visar bilder för fem lagringskonton i resursgruppen Contoso R G.  Lagringskonton ett och tre är blå, medan lagringskontona två, fyra och fem är röda.
:::image-end:::

I det här exemplet måste du vara försiktig med säkerhetsrisker. Nu när du har skapat en principtilldelning utvärderas den för alla inkluderade och undantagna lagringskonton i resursgruppen ContosoRG. Den granskar de tre icke-kompatibla lagringskontona och ändrar därför deras tillstånd till **Icke-kompatibla.**

:::image type="complex" source="../media/getting-compliance-data/resource-group03.png" alt-text="Diagram över efterlevnad för lagringskonto i resursgruppen Contoso R G." border="false":::
   Diagram som visar bilder för fem lagringskonton i resursgruppen Contoso R G. Lagringskontona ett och tre har nu gröna bockmarkeringar under sig, medan lagringskontona två, fyra och fem nu har röda varningstecken under sig.
:::image-end:::

Förutom **kompatibla** och **icke-kompatibla** har principer och resurser fyra andra tillstånd:

- **Undantag:** Resursen ingår i en tilldelning, men har ett definierat [undantag.](../concepts/exemption-structure.md)
- **Motstridiga:** Det finns två eller flera principdefinitioner med motstridiga regler. Två definitioner lägger till exempel till samma tagg med olika värden.
- **Inte startad:** Utvärderingscykeln har inte startats för principen eller resursen.
- **Inte registrerad:** Azure Policy resursprovidern har inte registrerats eller kontot som är inloggat har inte behörighet att läsa efterlevnadsdata.

Azure Policy använder fälten **type**, **name** eller **kind** i definitionen för att avgöra om en resurs matchar. När resursen matchar anses den vara tillämplig och har statusen **Kompatibel,** **Icke-kompatibel** eller **Undantag.** Om någon **av** dessa  **typer** av typer är , namn eller typ är den enda egenskapen i definitionen, anses alla inkluderade och icke-undantagna resurser vara tillämpliga och utvärderas.

Kompatibilitetsprocenten bestäms genom att **dela upp kompatibla** och **undantagna** resurser med _totalt antal resurser_. _Totalt antal_ resurser definieras som summan av resurserna  **Kompatibel,** **Icke-kompatibel,** Undanta och **Motstridiga.** De övergripande kompatibilitetsnumren är summan av distinkta resurser som är **kompatibla** eller **undantagna** dividerat med summan av alla distinkta resurser. I bilden nedan finns det 20 distinkta resurser som är tillämpliga och endast en är **Icke-kompatibel.**
Den övergripande resursefterlevnaden är 95 % (19 av 20).

:::image type="content" source="../media/getting-compliance-data/simple-compliance.png" alt-text="Skärmbild av information om principefterlevnad på sidan Efterlevnad." border="false":::

> [!NOTE]
> Regelefterlevnad i Azure Policy är en förhandsgranskningsfunktion. Efterlevnadsegenskaper från SDK och sidor i portalen är olika för aktiverade initiativ. Mer information finns i [Regelefterlevnad](../concepts/regulatory-compliance.md)

## <a name="portal"></a>Portalen

I Azure Portal visas en grafisk upplevelse av att visualisera och förstå kompatibilitetstillståndet i din miljö. På sidan **Princip** innehåller alternativet **Översikt** information om tillgängliga omfång för efterlevnad för både principer och initiativ. Tillsammans med kompatibilitetstillstånd och antal per tilldelning innehåller den ett diagram som visar efterlevnad under de senaste sju dagarna. Sidan **Efterlevnad** innehåller mycket av samma information (förutom diagrammet), men innehåller ytterligare filtrerings- och sorteringsalternativ.

:::image type="content" source="../media/getting-compliance-data/compliance-page.png" alt-text="Skärmbild av sidan Efterlevnad, filtreringsalternativ och information." border="false":::

Eftersom en princip eller ett initiativ kan tilldelas till olika omfång innehåller tabellen omfånget för varje tilldelning och den typ av definition som tilldelades. Antalet icke-kompatibla resurser och icke-kompatibla principer för varje tilldelning tillhandahålls också. Om du väljer en princip eller ett initiativ i tabellen får du en mer ingående titt på kompatibiliteten för den specifika tilldelningen.

:::image type="content" source="../media/getting-compliance-data/compliance-details.png" alt-text="Skärmbild av sidan Efterlevnadsinformation, inklusive information om antal och resursefterlevnad." border="false":::

Listan över resurser på fliken **Resursefterlevnad** visar utvärderingsstatus för befintliga resurser för den aktuella tilldelningen. Fliken är som standard **Icke-kompatibel,** men kan filtreras.
Händelser (tillägg, granskning, neka, distribuera, ändra) som utlöses av begäran om att skapa en resurs visas under **fliken** Händelser.

> [!NOTE]
> För en AKS-motorprincip är den resurs som visas resursgruppen.

:::image type="content" source="../media/getting-compliance-data/compliance-events.png" alt-text="Skärmbild av fliken Händelser på sidan Efterlevnadsinformation." border="false":::

<a name="component-compliance"></a>För [resurser i resursproviderläget](../concepts/definition-structure.md#resource-provider-modes) går du till fliken Resursefterlevnad,  väljer resursen eller högerklickar på raden och väljer Visa efterlevnadsinformation öppnar komponentefterlevnadsinformationen.  Den här sidan innehåller också flikar för att se de principer som har tilldelats till den här resursen, händelser, komponenthändelser och ändringshistorik.

:::image type="content" source="../media/getting-compliance-data/compliance-components.png" alt-text="Skärmbild av fliken Komponentefterlevnad och kompatibilitetsinformation för tilldelning av resursproviderläge." border="false":::

På sidan resursefterlevnad högerklickar du på raden för den händelse som du vill samla in mer information om och väljer **Visa aktivitetsloggar.** Aktivitetsloggsidan öppnas och filtreras i förväg till sökningen som visar information om tilldelningen och händelserna. Aktivitetsloggen innehåller ytterligare kontext och information om dessa händelser.

:::image type="content" source="../media/getting-compliance-data/compliance-activitylog.png" alt-text="Skärmbild av aktivitetsloggen för Azure Policy aktiviteter och utvärderingar." border="false":::

### <a name="understand-non-compliance"></a>Förstå bristande efterlevnad

När en resurs anses vara **icke-kompatibel finns** det många möjliga orsaker. Om du vill ta reda på orsaken till att en resurs inte är **kompatibel eller** för att hitta den ansvariga ändringen kan du gå till [Fastställa bristande efterlevnad.](./determine-non-compliance.md)

## <a name="command-line"></a>Kommandorad

Samma information som är tillgänglig i portalen kan hämtas med REST API (inklusive [med ARMClient),](https://github.com/projectkudu/ARMClient)Azure PowerShell och Azure CLI. Fullständig information om REST API finns i [Azure Policy](/rest/api/policy/) referens. På REST API-referenssidorna finns en grön "Prova"-knapp för varje åtgärd som gör att du kan prova direkt i webbläsaren.

Använd ARMClient eller ett liknande verktyg för att hantera autentisering till Azure för REST API exempel.

### <a name="summarize-results"></a>Sammanfatta resultat

Med REST API kan sammanfattningen utföras av container, definition eller tilldelning. Här är ett exempel på sammanfattning på prenumerationsnivå med hjälp Azure Policy Insight Sammanfattning [för prenumeration:](/rest/api/policy/policystates/summarizeforsubscription)

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2019-10-01
```

Utdata sammanfattar prenumerationen. I exempelutdata nedan är den sammanfattade kompatibiliteten under **value.results.nonCompliantResources** och **value.results.nonCompliantPolicies**. Denna begäran innehåller ytterligare information, inklusive varje tilldelning som utgör de icke-kompatibla talen och definitionsinformationen för varje tilldelning. Varje principobjekt i hierarkin tillhandahåller en **queryResultsUri** som kan användas för att hämta ytterligare information på den nivån.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
        "results": {
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant'",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>Fråga efter resurser

I exemplet ovan tillhandahåller **value.policyAssignments.policyDefinitions.results.queryResultsUri** en exempel-URI för alla icke-kompatibla resurser för en specifik principdefinition. Om vi **tittar $filter** värdet är ComplianceState lika med (eq) med "NonCompliant", PolicyAssignmentId har angetts för principdefinitionen och sedan själva PolicyDefinitionId. Anledningen till att inkludera PolicyAssignmentId i filtret är att PolicyDefinitionId kan finnas i flera princip- eller initiativtilldelningar med olika omfång. Genom att ange både PolicyAssignmentId och PolicyDefinitionId kan vi vara explicita i de resultat vi letar efter. Tidigare använde vi senaste för PolicyStates,  som automatiskt anger ett från- och till-tid-fönster för de senaste 24 timmarna. 

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Exempelsvaret nedan har trimmats till en enda icke-kompatibel resurs av utrymmesklistlig natur. Det detaljerade svaret innehåller flera data om resursen, principen eller initiativet och tilldelningen. Observera att du även kan se vilka tilldelningsparametrar som skickades till principdefinitionen.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 15,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "timestamp": "2018-05-19T04:41:09Z",
        "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Compute/virtualMachines/linux",
        "policyAssignmentId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Authorization/policyAssignments/37ce239ae4304622914f0c77",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "effectiveParameters": "",
        "ComplianceState": "NonCompliant",
        "subscriptionId": "{subscriptionId}",
        "resourceType": "/Microsoft.Compute/virtualMachines",
        "resourceLocation": "westus2",
        "resourceGroup": "RG-Tags",
        "resourceTags": "tbd",
        "policyAssignmentName": "37ce239ae4304622914f0c77",
        "policyAssignmentOwner": "tbd",
        "policyAssignmentParameters": "{\"tagName\":{\"value\":\"costCenter\"},\"tagValue\":{\"value\":\"Contoso-Test\"}}",
        "policyAssignmentScope": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags",
        "policyDefinitionName": "1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "policyDefinitionAction": "deny",
        "policyDefinitionCategory": "tbd",
        "policySetDefinitionId": "",
        "policySetDefinitionName": "",
        "policySetDefinitionOwner": "",
        "policySetDefinitionCategory": "",
        "policySetDefinitionParameters": "",
        "managementGroupIds": "",
        "policyDefinitionReferenceId": ""
    }]
}
```

### <a name="view-events"></a>Visa händelser

När en resurs skapas eller uppdateras genereras ett principutvärderingsresultat. Resultatet kallas _principhändelser_. Använd följande URI för att visa de senaste principhändelserna som är associerade med prenumerationen.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2019-10-01
```

Ditt resultat liknar följande exempel:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 16
    }]
}
```

Mer information om hur du frågar efter principhändelser finns i [referensartikeln Azure Policy händelser.](/rest/api/policy/policyevents)

### <a name="azure-cli"></a>Azure CLI

Azure [CLI-kommandogruppen](/cli/azure/what-is-azure-cli) för Azure Policy täcker de flesta åtgärder som är tillgängliga i REST eller Azure PowerShell. En fullständig lista över tillgängliga kommandon finns i [Azure CLI – Azure Policy Översikt.](/cli/azure/policy)

Exempel: Hämta tillståndssammanfattningen för den högsta tilldelade principen med det högsta antalet icke-kompatibla resurser.

```azurecli-interactive
az policy state summarize --top 1
```

Den översta delen av svaret ser ut som i det här exemplet:

```json
{
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
    "odataid": null,
    "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
            "policyDefinitions": [{
                "effect": "audit",
                "policyDefinitionGroupNames": [
                    ""
                ],
                "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
                "policyDefinitionReferenceId": "",
                "results": {
                    "nonCompliantPolicies": null,
                    "nonCompliantResources": 398,
                    "policyDetails": [{
                        "complianceState": "noncompliant",
                        "count": 1
                    }],
                    "policyGroupDetails": [{
                        "complianceState": "noncompliant",
                        "count": 1
                    }],
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2020-07-14 14:01:22Z&$to=2020-07-15 14:01:22Z and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8' and PolicyDefinitionId eq '/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a'",
                    "resourceDetails": [{
                            "complianceState": "noncompliant",
                            "count": 398
                        },
                        {
                            "complianceState": "compliant",
                            "count": 4
                        }
                    ]
                }
            }],
    ...
```

Exempel: Hämta tillståndsposten för den senast utvärderade resursen (standardvärdet är tidsstämpeln i fallande ordning).

```azurecli-interactive
az policy state list --top 1
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "Compliant",
    "effectiveParameters": "",
    "isCompliant": true,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/securitycenterbuiltin",
    "policyAssignmentName": "SecurityCenterBuiltIn",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "auditifnotexists",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/aa633080-8b72-40c4-a2d7-d00c03e80bed",
    "policyDefinitionName": "aa633080-8b72-40c4-a2d7-d00c03e80bed",
    "policyDefinitionReferenceId": "identityenablemfaforownerpermissionsmonitoring",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "security center",
    "policySetDefinitionId": "/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8",
    "policySetDefinitionName": "1f3afdf9-d0c9-4c3d-847f-89da613e70a8",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "",
    "resourceId": "/subscriptions/{subscriptionId}",
    "resourceLocation": "",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Resources/subscriptions",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.903433+00:00"
  }
]
```

Exempel: Hämta information för alla icke-kompatibla virtuella nätverksresurser.

```azurecli-interactive
az policy state list --filter "ResourceType eq 'Microsoft.Network/virtualNetworks'"
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "NonCompliant",
    "effectiveParameters": "",
    "isCompliant": false,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
    "policyAssignmentName": "e0704696df5e4c3c81c873e8",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "audit",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionName": "2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionReferenceId": "",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "",
    "policySetDefinitionId": "",
    "policySetDefinitionName": "",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "RG-Tags",
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Microsoft.Network/virtualNetworks/RG-Tags-vnet",
    "resourceLocation": "westus2",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Network/virtualNetworks",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.901911+00:00"
  }
]
```

Exempel: Hämta händelser relaterade till icke-kompatibla virtuella nätverksresurser som inträffat efter ett visst datum.

```azurecli-interactive
az policy state list --filter "ResourceType eq 'Microsoft.Network/virtualNetworks'" --from '2020-07-14T00:00:00Z'
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "NonCompliant",
    "effectiveParameters": "",
    "isCompliant": false,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
    "policyAssignmentName": "e0704696df5e4c3c81c873e8",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "audit",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionName": "2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionReferenceId": "",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "",
    "policySetDefinitionId": "",
    "policySetDefinitionName": "",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "RG-Tags",
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Microsoft.Network/virtualNetworks/RG-Tags-vnet",
    "resourceLocation": "westus2",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Network/virtualNetworks",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.901911+00:00"
  }
]
```

### <a name="azure-powershell"></a>Azure PowerShell

Modulen Azure PowerShell för Azure Policy är tillgänglig på PowerShell-galleriet [som Az.PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights). Med PowerShellGet kan du installera modulen med hjälp av `Install-Module -Name Az.PolicyInsights` (kontrollera att du har den senaste [Azure PowerShell](/powershell/azure/install-az-ps) installerat):

```azurepowershell-interactive
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name Az.PolicyInsights

# Import the downloaded module
Import-Module Az.PolicyInsights

# Login with Connect-AzAccount if not using Cloud Shell
Connect-AzAccount
```

Modulen har följande cmdlets:

- `Get-AzPolicyStateSummary`
- `Get-AzPolicyState`
- `Get-AzPolicyEvent`
- `Get-AzPolicyRemediation`
- `Remove-AzPolicyRemediation`
- `Start-AzPolicyRemediation`
- `Stop-AzPolicyRemediation`

Exempel: Hämta tillståndssammanfattningen för den högsta tilldelade principen med det högsta antalet icke-kompatibla resurser.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Exempel: Hämta tillståndsposten för den senast utvärderade resursen (standardvärdet är tidsstämpeln i fallande ordning).

```azurepowershell-interactive
PS> Get-AzPolicyState -Top 1

Timestamp                  : 5/22/2018 3:47:34 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/networkInterfaces/linux316
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/networkInterfaces
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Exempel: Hämta information för alla icke-kompatibla virtuella nätverksresurser.

```azurepowershell-interactive
PS> Get-AzPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

Timestamp                  : 5/22/2018 4:02:20 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Exempel: Hämta händelser relaterade till icke-kompatibla virtuella nätverksresurser som inträffat efter ett visst datum, konvertera till ett CSV-objekt och exportera till en fil.

```azurepowershell-interactive
$policyEvents = Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2020-09-19'
$policyEvents | ConvertTo-Csv | Out-File 'C:\temp\policyEvents.csv'
```

Objektets utdata `$policyEvents` ser ut som följande utdata:

```output
Timestamp                  : 9/19/2020 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : eastus
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
TenantId                   : {tenantId}
PrincipalOid               : {principalOid}
```

Fältet **PrincipalOid** kan användas för att hämta en specifik användare med Azure PowerShell cmdleten `Get-AzADUser` . Ersätt **{principalOid}** med svaret du fick från föregående exempel.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Azure Monitor-loggar

Om du har en [Log Analytics-arbetsyta](../../../azure-monitor/logs/log-query-overview.md) med från Aktivitetslogganalys-lösningen som är kopplad till din prenumeration kan du även visa icke-efterlevnadsresultat från utvärderingen av nya och uppdaterade resurser med hjälp av enkla `AzureActivity` Kusto-frågor och [](../../../azure-monitor/essentials/activity-log.md) `AzureActivity` tabellen. Med information Azure Monitor loggar kan aviseringar konfigureras för att se om de inte följer efterlevnadsvillkoren.

:::image type="content" source="../media/getting-compliance-data/compliance-loganalytics.png" alt-text="Skärmbild av Azure Monitor som visar Azure Policy åtgärder i tabellen AzureActivity." border="false":::

## <a name="next-steps"></a>Nästa steg

- Granska exempel på [Azure Policy exempel](../samples/index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).
- Förstå hur du [programmässigt skapar principer.](programmatically-create.md)
- Lär dig hur [du åtgärdar icke-kompatibla resurser](remediate-resources.md).
- Granska vad en hanteringsgrupp är med [Organisera dina resurser med Azure-hanteringsgrupper.](../../management-groups/overview.md)
