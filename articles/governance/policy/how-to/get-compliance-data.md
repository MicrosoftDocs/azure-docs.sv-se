---
title: Hämta information om efterlevnadsprinciper
description: Azure Policy utvärderingar och effekter avgör efterlevnad. Lär dig hur du hämtar information om kompatibiliteten för dina Azure-resurser.
ms.date: 10/05/2020
ms.topic: how-to
ms.openlocfilehash: 3c1c128b414444c6004f32f3f3173548f81a82e1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100577112"
---
# <a name="get-compliance-data-of-azure-resources"></a>Hämta efterlevnads data för Azure-resurser

En av de största fördelarna med Azure Policy är insikter och kontrollerar att den ger över resurser i en prenumeration eller [hanterings grupp](../../management-groups/overview.md) av prenumerationer. Den här kontrollen kan utnyttjas på många olika sätt, t. ex. förhindra att resurser skapas på fel plats, framtvinga gemensam och konsekvent användning av taggar, eller granska befintliga resurser för lämpliga konfigurationer och inställningar. I samtliga fall genereras data av Azure Policy så att du kan förstå miljöns kompatibilitetstillstånd.

Det finns flera sätt att komma åt kompatibilitetsinformation som genereras av din princip och initiativ tilldelningar:

- Använda [Azure Portal](#portal)
- Via [kommando rads](#command-line) skript

Innan du tittar på metoderna för att rapportera om efterlevnad ska vi titta på när kompatibilitetsinformation uppdateras och frekvensen och händelserna som utlöser en utvärderings cykel.

> [!WARNING]
> Om kompatibilitetstillstånd rapporteras som **ej registrerat**, verifierar du att **Microsoft. PolicyInsights** Resource Provider är registrerad och att användaren har rätt behörighet för Azure-rollbaserad åtkomst kontroll (Azure RBAC) enligt beskrivningen i [azure RBAC-behörigheter i Azure policy](../overview.md#azure-rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Utvärderings utlösare

Resultatet av en slutförd utvärderings cykel är tillgängligt i `Microsoft.PolicyInsights` resurs leverantören genom `PolicyStates` och `PolicyEvents` åtgärder. Mer information om åtgärder för Azure Policy insikter REST API finns i [Azure policy insikter](/rest/api/policy-insights/).

Utvärderingar av tilldelade principer och initiativ sker som resultatet av olika händelser:

- En princip eller ett initiativ har nyligen tilldelats ett omfång. Det tar cirka 30 minuter för tilldelningen att tillämpas på det definierade omfånget. När den har tillämpats börjar utvärderings cykeln för resurser inom det omfånget mot den nyligen tilldelade principen eller initiativet, och beroende på vilka effekter som används av principen eller initiativet markeras resurserna som kompatibla, icke-kompatibla eller undantagna. En stor princip eller ett initiativ som utvärderas mot en stor omfattning av resurser kan ta tid. Därför finns det ingen fördefinierad förväntad utvärdering av när utvärderings cykeln har slutförts. När den är klar finns uppdaterade efterlevnadsprinciper i portalen och SDK: erna.

- En princip eller ett initiativ som redan har tilldelats ett omfång uppdateras. Utvärderings cykeln och tids inställningen för det här scenariot är desamma som för en ny tilldelning i ett omfång.

- En resurs distribueras till eller uppdateras inom ett omfång med en tilldelning via Azure Resource Manager, REST API eller en SDK som stöds. I det här scenariot blir Effect-händelsen (Lägg till, granska, neka, distribuera) och kompatibel status information för den enskilda resursen tillgänglig i portalen och SDK: er som är cirka 15 minuter senare. Den här händelsen orsakar ingen utvärdering av andra resurser.

- Ett [princip undantag](../concepts/exemption-structure.md) skapas, uppdateras eller tas bort. I det här scenariot utvärderas motsvarande tilldelning för den definierade undantags omfattningen.

- Utvärderings cykel för standard kompatibilitet. En gång var 24: e timme utvärderas tilldelningarna automatiskt. En stor princip eller initiativ för många resurser kan ta tid, så det finns ingen fördefinierad förväntad utvärdering av när utvärderings cykeln har slutförts. När den är klar finns uppdaterade efterlevnadsprinciper i portalen och SDK: erna.

- Resurs leverantören för [gäst konfigurationen](../concepts/guest-configuration.md) har uppdaterats med information om efterlevnad av en hanterad resurs.

- Genomsökning på begäran

### <a name="on-demand-evaluation-scan"></a>Utvärderingsgenomsökning på begäran

En utvärderings sökning för en prenumeration eller en resurs grupp kan startas med Azure CLI, Azure PowerShell, ett anrop till REST API eller med hjälp av GitHub- [åtgärden för att söka efter Azure policy efterlevnad](https://github.com/marketplace/actions/azure-policy-compliance-scan).
Den här inläsningen är en asynkron process.

#### <a name="on-demand-evaluation-scan---github-action"></a>Utvärderings genomsökning på begäran – GitHub åtgärd

Använd [åtgärden Azure policy kompatibilitetskontroll](https://github.com/marketplace/actions/azure-policy-compliance-scan) för att utlösa en utvärderings version på begäran från ditt [GitHub-arbetsflöde](https://docs.github.com/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) på en eller flera resurser, resurs grupper eller prenumerationer och Gate arbets flödet baserat på resursernas kompatibilitetstillstånd. Du kan också konfigurera arbets flödet så att det körs vid en schemalagd tidpunkt så att du får den senaste kompatibilitetsstatus vid en lämplig tidpunkt. Dessutom kan den här GitHub-åtgärden generera en rapport om kompatibilitetstillstånd för genomsökta resurser för vidare analys eller för arkivering.

I följande exempel körs en kompatibilitetskontroll för en prenumeration. 

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

Mer information och exempel på arbets flöde finns i [GitHub-åtgärden för Azure policy kompatibilitets ökning lagrings platsen](https://github.com/Azure/policy-compliance-scan).

#### <a name="on-demand-evaluation-scan---azure-cli"></a>Utvärderings genomsökning på begäran – Azure CLI

Kompatibilitetskontroll påbörjas med [AZ princip tillstånd trigger-scanning](/cli/azure/policy/state#az_policy_state_trigger_scan) kommandot.

Som standard `az policy state trigger-scan` startar en utvärdering för alla resurser i den aktuella prenumerationen. Om du vill starta en utvärdering för en speciell resurs grupp använder du parametern **resurs grupp** . I följande exempel startas en kompatibilitetskontroll i den aktuella prenumerationen för resurs gruppen _MyRG_ :

```azurecli-interactive
az policy state trigger-scan --resource-group "MyRG"
```

Du kan välja att inte vänta på att den asynkrona processen ska slutföras innan du fortsätter med parametern **no-wait** .

#### <a name="on-demand-evaluation-scan---azure-powershell"></a>Utvärderings genomsökning på begäran – Azure PowerShell

Genomsökningen efter kompatibiliteten startas med cmdleten [Start-AzPolicyComplianceScan](/powershell/module/az.policyinsights/start-azpolicycompliancescan) .

Som standard `Start-AzPolicyComplianceScan` startar en utvärdering för alla resurser i den aktuella prenumerationen. Om du vill starta en utvärdering för en speciell resurs grupp använder du parametern **ResourceGroupName** . I följande exempel startas en kompatibilitetskontroll i den aktuella prenumerationen för resurs gruppen _MyRG_ :

```azurepowershell-interactive
Start-AzPolicyComplianceScan -ResourceGroupName 'MyRG'
```

Du kan använda PowerShell för att vänta tills det asynkrona anropet har slutförts innan du ger resultatet utdata eller som körs i bakgrunden som ett [jobb](/powershell/module/microsoft.powershell.core/about/about_jobs). Om du vill använda ett PowerShell-jobb för att köra Kompatibilitetskontroll i bakgrunden använder du parametern **AsJob** och anger värdet till ett objekt, t. ex `$job` . i det här exemplet:

```azurepowershell-interactive
$job = Start-AzPolicyComplianceScan -AsJob
```

Du kan kontrol lera jobbets status genom att kontrol lera `$job` objektet. Jobbet är av typen `Microsoft.Azure.Commands.Common.AzureLongRunningJob` . Använd `Get-Member` på `$job` objektet för att se tillgängliga egenskaper och metoder.

När kompatibilitetskontroll körs, kontrollerar du att `$job` objektet ger utdata, till exempel följande:

```azurepowershell-interactive
$job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
2      Long Running O… AzureLongRunni… Running       True            localhost            Start-AzPolicyCompliance…
```

När kompatibilitetskontroll har slutförts ändras egenskapen **State** till _slutförd_.

#### <a name="on-demand-evaluation-scan---rest"></a>Utvärderings genomsökning på begäran – REST

Som en asynkron process väntar REST-slutpunkten för att starta genomsökningen inte förrän genomsökningen är klar för att svara. I stället tillhandahåller den en URI för att fråga om status för den begärda utvärderingen.

I varje REST API-URI finns det variabler som används och som du måste ersätta med egna värden:

- `{YourRG}` -Ersätt med namnet på din resurs grupp
- `{subscriptionId}` – Ersätt med ditt prenumerations-ID

Genomsökningen stöder utvärdering av resurser i en prenumeration eller i en resurs grupp. Starta en sökning efter omfattning med ett REST API **post** -kommando med följande URI-strukturer:

- Prenumeration

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

- Resursgrupp

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

Anropet returnerar status **202** . Som ingår i svars huvudet är en **plats** egenskap med följande format:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2019-10-01
```

`{ResourceContainerGUID}` skapas statiskt för det begärda omfånget. Om ett omfång redan kör en genomsökning på begäran startas inte en ny sökning. I stället ges den nya begäran samma `{ResourceContainerGUID}` **plats** -URI för status. Ett REST API **Get** -kommando till **platsen** URI returnerar en **202 som godkänts** medan utvärderingen pågår. När utvärderings genomsökningen har slutförts returneras statusen **200 OK** . Texten i en slutförd genomsökning är ett JSON-svar med statusen:

```json
{
    "status": "Succeeded"
}
```

#### <a name="on-demand-evaluation-scan---visual-studio-code"></a>Utvärderings genomsökning på begäran – Visual Studio Code

Azure Policy-tillägget för Visual Studio Code kan köra en utvärderings sökning för en speciell resurs. Den här genomsökningen är en synkron process, till skillnad från metoderna Azure PowerShell och REST.
Mer information och anvisningar finns i [utvärderingen på begäran med vs Code-tillägget](./extension-for-vscode.md#on-demand-evaluation-scan).

## <a name="how-compliance-works"></a>Hur efterlevnad fungerar

I en tilldelning är en resurs **icke-kompatibel** om den inte följer princip-eller initiativ reglerna och inte är _undantagen_. Följande tabell visar hur olika princip effekter fungerar med villkors utvärderingen för det resulterande kompatibilitetstillstånd:

| Resurstillstånd | Effekt | Principutvärdering | Kompatibilitetsstatus |
| --- | --- | --- | --- |
| Ny eller uppdaterad? | Granska, ändra, AuditIfNotExist | Sant | Icke-kompatibel |
| Ny eller uppdaterad? | Granska, ändra, AuditIfNotExist | Falskt | Kompatibel |
| Finns | Neka, granska, lägga till, ändra, DeployIfNotExist, AuditIfNotExist | Sant | Icke-kompatibel |
| Finns | Neka, granska, lägga till, ändra, DeployIfNotExist, AuditIfNotExist | Falskt | Kompatibel |

> [!NOTE]
> DeployIfNotExist-och AuditIfNotExist-effekterna kräver att IF-instruktionen är TRUE och förekomstens villkor är falskt för att vara icke-kompatibel. När det är TRUE utlöser IF-villkoret utvärdering av villkoret Finns för de relaterade resurserna.

Anta till exempel att du har en resurs grupp – ContsoRG med vissa lagrings konton (markerade i rött) som exponeras för offentliga nätverk.

:::image type="complex" source="../media/getting-compliance-data/resource-group01.png" alt-text="Diagram över lagrings konton som exponeras för offentliga nätverk i resurs gruppen contoso R G." border="false":::
   Diagram över bilder för fem lagrings konton i resurs gruppen contoso R G.  Lagrings konton en och tre är blå, medan lagrings konton två, fyra och fem är röda.
:::image-end:::

I det här exemplet måste du vara försiktig säkerhets risker. Nu när du har skapat en princip tilldelning utvärderas den för alla inkluderade och icke-undantagna lagrings konton i resurs gruppen conto sorg. Den granskar de tre icke-kompatibla lagrings kontona, vilket innebär att deras tillstånd ändras till **icke-kompatibel.**

:::image type="complex" source="../media/getting-compliance-data/resource-group03.png" alt-text="Diagram över kompatibilitet för lagrings konto i resurs gruppen contoso R G." border="false":::
   Diagram över bilder för fem lagrings konton i resurs gruppen contoso R G. Lagrings konton en och tre har nu gröna bockar under dem, medan lagrings konton två, fyra och fem nu har röda varnings tecken under dem.
:::image-end:::

Utöver **kompatibla** och **icke-kompatibla** har principer och resurser fyra andra tillstånd:

- **Undantag**: resursen omfattas av en tilldelning, men har ett [definierat undantag](../concepts/exemption-structure.md).
- **Konflikt**: det finns två eller flera princip definitioner med motstridiga regler. Två definitioner lägger till exempel till samma tagg med olika värden.
- **Inte startat**: utvärderings cykeln har inte startat för principen eller resursen.
- **Inte registrerad**: den Azure policy Resource providern har inte registrerats eller så har det inloggade kontot inte behörighet att läsa efterlevnadsprinciper.

Azure Policy använder fälten **typ**, **namn** eller **typ** i definitionen för att avgöra om en resurs är en matchning. När resursen matchar, betraktas den som tillämplig och har statusen antingen **kompatibel**, **icke-kompatibel** eller **undantagen**. Om antingen **typ**, **namn** eller **typ** är den enda egenskapen i definitionen anses alla inkluderade och icke-undantagna resurser vara tillämpliga och utvärderas.

Procent andelen för efterlevnad bestäms genom att de  resurser som **uppfyller** resurserna divideras med de _totala resurserna_. _Totalt antal resurser_ definieras som summan av de **kompatibla**, **icke-kompatibla**, **undantagna** och **motstridiga** resurserna. De övergripande kompatibilitets numren är summan av distinkta resurser som är **kompatibla** eller **undantagna** dividerade med summan av alla distinkta resurser. I bilden nedan finns det 20 distinkta resurser som är tillämpliga och endast en är **icke-kompatibel**.
Den övergripande resursens kompatibilitet är 95% (19 av 20).

:::image type="content" source="../media/getting-compliance-data/simple-compliance.png" alt-text="Skärm bild av information om efterlevnadsprincip från sidan efterlevnad." border="false":::

> [!NOTE]
> Regelefterlevnad som följer Azure Policy är en förhands gransknings funktion. Egenskaperna för efterlevnad från SDK och sidor i portalen är olika för aktiverade initiativ. Mer [information finns i regelefterlevnad](../concepts/regulatory-compliance.md)

## <a name="portal"></a>Portalen

Azure Portal demonstrerar en grafisk upplevelse av visualisering och förståelse av status för miljön. På **princip** sidan innehåller **översikts** alternativet information om tillgängliga omfång för efterlevnad av både principer och initiativ. Tillsammans med kompatibilitetstillstånd och antalet per tilldelning innehåller det ett diagram som visar efterlevnad under de senaste sju dagarna. Sidan **efterlevnad** innehåller ungefär samma information (förutom diagrammet), men innehåller ytterligare alternativ för filtrering och sortering.

:::image type="content" source="../media/getting-compliance-data/compliance-page.png" alt-text="Skärm bild av sidan efterlevnad, filtrerings alternativ och information." border="false":::

Eftersom en princip eller ett initiativ kan tilldelas till olika omfattningar, innehåller tabellen omfattningen för varje tilldelning och den typ av definition som har tilldelats. Antalet icke-kompatibla resurser och icke-kompatibla principer för varje tilldelning anges också. Om du väljer en princip eller ett initiativ i tabellen visas en djupare titt på kompatibiliteten för den specifika tilldelningen.

:::image type="content" source="../media/getting-compliance-data/compliance-details.png" alt-text="Skärm bild av sidan information om efterlevnad, inklusive antal och resurs kraven." border="false":::

I listan över resurser på fliken **kompatibilitet** visas utvärderings status för befintliga resurser för den aktuella tilldelningen. Fliken är som standard **icke-kompatibel**, men kan filtreras.
Händelser (tillägg, granskning, neka, distribuera, ändra) som utlöses av begäran om att skapa en resurs visas på fliken **händelser** .

> [!NOTE]
> För en AKS Engine-princip är resursen som visas resurs gruppen.

:::image type="content" source="../media/getting-compliance-data/compliance-events.png" alt-text="Skärm bild av fliken händelser på sidan information om efterlevnad." border="false":::

<a name="component-compliance"></a> För resurser i [resurs leverantörs läge](../concepts/definition-structure.md#resource-provider-modes) går du till fliken **Resource Compliance (Resource Compliance** ) och markerar resursen eller högerklickar på raden och väljer **Visa kompatibilitetsinformation** öppnar komponenten Kompatibilitetsrapport. På den här sidan finns också flikar för att se de principer som har tilldelats den här resursen, händelser, komponent händelser och ändrings historik.

:::image type="content" source="../media/getting-compliance-data/compliance-components.png" alt-text="Skärm bild av fliken efterlevnad för komponenter och efterlevnad för en resurs leverantörs läge tilldelning." border="false":::

Tillbaka på sidan Resource Compliance (resurser) högerklickar du på den rad i händelsen som du vill samla in mer information om och väljer **Visa aktivitets loggar**. Sidan aktivitets logg öppnas och filtreras i förväg till sökningen som visar information om tilldelningen och händelserna. Aktivitets loggen ger ytterligare kontext och information om dessa händelser.

:::image type="content" source="../media/getting-compliance-data/compliance-activitylog.png" alt-text="Skärm bild av aktivitets loggen för Azure Policy aktiviteter och utvärderingar." border="false":::

### <a name="understand-non-compliance"></a>Förstå bristande efterlevnad

När en resurs bedöms vara **icke-kompatibel** finns det många möjliga orsaker. Om du vill ta reda på orsaken till att en resurs är **icke-kompatibel** eller om du vill ha en ändrings ansvarig, kontrollerar du att det [inte är kompatibelt](./determine-non-compliance.md)

## <a name="command-line"></a>Kommandorad

Samma information som är tillgänglig i portalen kan hämtas med REST API (inklusive med [ARMClient](https://github.com/projectkudu/ARMClient)), Azure PowerShell och Azure CLI. Fullständig information om REST API finns i [Azure policy Insights](/rest/api/policy-insights/) -referensen. REST API referens sidor har en grön "Try"-knapp för varje åtgärd som gör att du kan testa den direkt i webbläsaren.

Använd ARMClient eller ett liknande verktyg för att hantera autentisering till Azure för REST API exempel.

### <a name="summarize-results"></a>Sammanfatta resultat

Med REST API kan Sammanfattning utföras av behållare, definition eller tilldelning. Här är ett exempel på en sammanfattning på prenumerations nivån med Azure Policy Insight- [Sammanfattning för prenumeration](/rest/api/policy-insights/policystates/summarizeforsubscription):

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2019-10-01
```

Utdata sammanfattar prenumerationen. I exemplet nedan är den summerade kompatibiliteten under **Value. Results. nonCompliantResources** och **Value. Results. nonCompliantPolicies**. Den här begäran innehåller ytterligare information, inklusive varje tilldelning som innehåller de icke-kompatibla talen och definitions informationen för varje tilldelning. Varje princip objekt i hierarkin innehåller en **queryResultsUri** som kan användas för att få ytterligare information på den nivån.

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

I exemplet ovan tillhandahåller **Value. policyAssignments. policyDefinitions. Results. queryResultsUri** en exempel-URI för alla icke-kompatibla resurser för en speciell princip definition. Om du tittar på **$filter** värde är ComplianceState lika med (EQ) till "icke-kompatibel", PolicyAssignmentId har angetts för princip definitionen och sedan själva PolicyDefinitionId. Orsaken till att inkludera PolicyAssignmentId i filtret beror på att PolicyDefinitionId kan finnas i flera principer eller initiativ tilldelningar med olika omfång. Genom att ange både PolicyAssignmentId och PolicyDefinitionId kan vi komma i de resultat vi söker. Tidigare användes för PolicyStates vi **senaste**, vilket automatiskt ställer in ett **från** -och **till** -tidsfönster för de senaste 24 timmarna.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Exempel svaret nedan har trimmats till en enskild icke-kompatibel resurs för det kortfattat. Det detaljerade svaret har flera delar av data om resursen, principen eller initiativet och tilldelningen. Observera att du även kan se vilka tilldelnings parametrar som skickades till princip definitionen.

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

När en resurs skapas eller uppdateras genereras ett utvärderings resultat för principen. Resultat kallas _princip händelser_. Använd följande URI för att visa de senaste princip händelser som är associerade med prenumerationen.

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

Mer information om hur du frågar princip händelser finns i artikeln referens för [Azure policy händelser](/rest/api/policy-insights/policyevents) .

### <a name="azure-cli"></a>Azure CLI

Kommando gruppen för [Azure CLI](/cli/azure/what-is-azure-cli) för Azure policy omfattar de flesta åtgärder som är tillgängliga i REST eller Azure PowerShell. En fullständig lista över tillgängliga kommandon finns i [Översikt över Azure CLI-Azure policy](/cli/azure/policy).

Exempel: hämtar status sammanfattning för den översta tilldelade principen med det högsta antalet icke-kompatibla resurser.

```azurecli-interactive
az policy state summarize --top 1
```

Den övre delen av svaret ser ut som i det här exemplet:

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

Exempel: hämtar tillstånds posten för den senaste utvärderade resursen (Standardvärdet är efter tidsstämpel i fallande ordning).

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

Exempel: hämtar information om alla icke-kompatibla virtuella nätverks resurser.

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

Exempel: Hämta händelser relaterade till icke-kompatibla virtuella nätverks resurser som inträffat efter ett visst datum.

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

Azure PowerShell-modulen för Azure Policy finns på PowerShell-galleriet som [AZ. PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights). Med PowerShellGet kan du installera modulen med `Install-Module -Name Az.PolicyInsights` (kontrol lera att du har de senaste [Azure PowerShell](/powershell/azure/install-az-ps) installerade):

```azurepowershell-interactive
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name Az.PolicyInsights

# Import the downloaded module
Import-Module Az.PolicyInsights

# Login with Connect-AzAccount if not using Cloud Shell
Connect-AzAccount
```

Modulen har följande cmdletar:

- `Get-AzPolicyStateSummary`
- `Get-AzPolicyState`
- `Get-AzPolicyEvent`
- `Get-AzPolicyRemediation`
- `Remove-AzPolicyRemediation`
- `Start-AzPolicyRemediation`
- `Stop-AzPolicyRemediation`

Exempel: hämtar status sammanfattning för den översta tilldelade principen med det högsta antalet icke-kompatibla resurser.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Exempel: hämtar tillstånds posten för den senaste utvärderade resursen (Standardvärdet är efter tidsstämpel i fallande ordning).

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

Exempel: hämtar information om alla icke-kompatibla virtuella nätverks resurser.

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

Exempel: Hämta händelser relaterade till icke-kompatibla virtuella nätverks resurser som inträffat efter ett visst datum, konvertera till ett CSV-objekt och exportera till en fil.

```azurepowershell-interactive
$policyEvents = Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2020-09-19'
$policyEvents | ConvertTo-Csv | Out-File 'C:\temp\policyEvents.csv'
```

Utdata från `$policyEvents` objektet ser ut ungefär så här:

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

Fältet **PrincipalOid** kan användas för att hämta en speciell användare med cmdleten Azure PowerShell `Get-AzADUser` . Ersätt **{principalOid}** med svaret som du fick från föregående exempel.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Azure Monitor-loggar

Om du har en [Log Analytics-arbetsyta](../../../azure-monitor/logs/log-query-overview.md) med `AzureActivity` från [Aktivitetslogganalys-lösningen](../../../azure-monitor/essentials/activity-log.md) som är kopplad till din prenumeration kan du också Visa inkompatibla resultat från utvärderingen av nya och uppdaterade resurser med hjälp av enkla Kusto-frågor och `AzureActivity` tabellen. Med information i Azure Monitor loggar kan aviseringar konfigureras för att se om de inte uppfyller kraven.

:::image type="content" source="../media/getting-compliance-data/compliance-loganalytics.png" alt-text="Skärm bild av Azure Monitor loggar som visar Azure Policy åtgärder i tabellen AzureActivity." border="false":::

## <a name="next-steps"></a>Nästa steg

- Granska exempel i [Azure policy exempel](../samples/index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).
- Lär dig att [program mässigt skapa principer](programmatically-create.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](remediate-resources.md).
- Granska en hanterings grupp med [organisera dina resurser med Azures hanterings grupper](../../management-groups/overview.md).
