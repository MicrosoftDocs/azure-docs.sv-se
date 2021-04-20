---
title: Felsöka vanliga fel
description: Lär dig hur du felsöker problem med att skapa principdefinitioner, de olika SDK:erna och tillägget för Kubernetes.
ms.date: 04/19/2021
ms.topic: troubleshooting
ms.openlocfilehash: c4feae11c6d8d78a43bae9882405e292a18e90bd
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725070"
---
# <a name="troubleshoot-errors-with-using-azure-policy"></a>Felsöka fel med hjälp av Azure Policy

När du skapar principdefinitioner, arbetar med -SDK:er eller ställer in Azure Policy för [Kubernetes-tillägg](../concepts/policy-for-kubernetes.md) kan du få fel. Den här artikeln beskriver olika allmänna fel som kan uppstå och föreslår olika sätt att lösa dem.

## <a name="find-error-details"></a>Hitta felinformation

Platsen för felinformationen beror på vilken aspekt av Azure Policy du arbetar med.

- Om du arbetar med en anpassad princip går du till Azure Portal för att få [](../how-to/get-compliance-data.md) linting-feedback om schemat eller granskar resulterande efterlevnadsdata för att se hur resurserna utvärderades.
- Om du arbetar med någon av de olika SDK:erna ger SDK information om varför funktionen misslyckades.
- Om du arbetar med tillägget för Kubernetes börjar du med [loggning](../concepts/policy-for-kubernetes.md#logging) i klustret.

## <a name="general-errors"></a>Allmänna fel

### <a name="scenario-alias-not-found"></a>Scenario: Alias hittades inte

#### <a name="issue"></a>Problem

Ett felaktigt eller inexistent alias används i en principdefinition. Azure Policy använder [alias för](../concepts/definition-structure.md#aliases) att mappa till Azure Resource Manager egenskaper.

#### <a name="cause"></a>Orsak

Ett felaktigt eller inexistent alias används i en principdefinition.

#### <a name="resolution"></a>Lösning

Kontrollera först att Resource Manager har ett alias. Om du vill söka efter tillgängliga alias går du [till Azure Policy för Visual Studio Code](../how-to/extension-for-vscode.md) eller SDK.
Om aliaset för en Resource Manager-egenskap inte finns skapar du en supportbiljett.

### <a name="scenario-evaluation-details-arent-up-to-date"></a>Scenario: Utvärderingsinformationen är inte uppdaterad

#### <a name="issue"></a>Problem

En resurs har _statusen Inte startad_ eller så är kompatibilitetsinformationen inte aktuell.

#### <a name="cause"></a>Orsak

Det tar cirka 30 minuter att tillämpa en ny princip- eller initiativtilldelning. Nya eller uppdaterade resurser inom omfånget för en befintlig tilldelning blir tillgängliga inom cirka 15 minuter. En standardgenomsökning av efterlevnad sker var 24:e timme. Mer information finns i [utvärderingsutlösare.](../how-to/get-compliance-data.md#evaluation-triggers)

#### <a name="resolution"></a>Lösning

Vänta först en lämplig tid tills utvärderingen har avslutats och kompatibilitetsresultaten blir tillgängliga i Azure Portal eller SDK. Information om hur du startar en ny utvärderingsgenomsökning med Azure PowerShell eller REST API finns [i Utvärderingsgenomsökning på begäran.](../how-to/get-compliance-data.md#on-demand-evaluation-scan)

### <a name="scenario-compliance-isnt-as-expected"></a>Scenario: Efterlevnad är inte som förväntat

#### <a name="issue"></a>Problem

En resurs är inte i utvärderingstillståndet Kompatibel eller Ej kompatibel som förväntas för resursen.  

#### <a name="cause"></a>Orsak

Resursen finns inte i rätt omfång för principtilldelningen, eller så fungerar principdefinitionen inte som den ska.

#### <a name="resolution"></a>Lösning

Så här felsöker du principdefinitionen:

1. Vänta först lämplig tid tills utvärderingen har avslutats och kompatibilitetsresultaten blir tillgängliga i Azure Portal sdk. 

1. Information om hur du startar en ny utvärderingsgenomsökning med Azure PowerShell eller REST API finns [i Utvärderingsgenomsökning på begäran.](../how-to/get-compliance-data.md#on-demand-evaluation-scan)
1. Kontrollera att tilldelningsparametrarna och tilldelningsomfånget är korrekt inställda.
1. Kontrollera [principdefinitionsläget](../concepts/definition-structure.md#mode):
   - Läget ska vara för `all` alla resurstyper.
   - Läget ska vara om `indexed` principdefinitionen söker efter taggar eller plats.
1. Kontrollera att omfånget för resursen inte [undantas](../concepts/assignment-structure.md#excluded-scopes) eller [undantas.](../concepts/exemption-structure.md)
1. Om efterlevnad för en principtilldelning `0/0` visar resurser, kunde inga resurser fastställas vara tillämpliga inom tilldelningsomfånget. Kontrollera både principdefinitionen och tilldelningsomfånget.
1. För en icke-kompatibel resurs som förväntades vara kompatibel, se [fastställa orsakerna till inkompatibilitet](../how-to/determine-non-compliance.md). Jämförelsen av definitionen med det utvärderade egenskapsvärdet anger varför en resurs var inkompatibla.
   - Om **målvärdet är** fel ändrar du principdefinitionen.
   - Om det **aktuella värdet** är fel verifierar du resursnyttolasten via `resources.azure.com` .
1. Information om andra vanliga problem och lösningar finns i [Felsöka: Framtvingande fungerar inte som förväntat.](#scenario-enforcement-not-as-expected)

Om du fortfarande har problem med din duplicerade och anpassade inbyggda principdefinition eller anpassade definition skapar du en supportbiljett **under** Redigera en princip för att dirigera problemet korrekt.

### <a name="scenario-enforcement-not-as-expected"></a>Scenario: Tillämpningen är inte som förväntat

#### <a name="issue"></a>Problem

En resurs som du Azure Policy att agera på åtgärdas inte och det finns ingen post i [Azure-aktivitetsloggen](../../../azure-monitor/essentials/platform-logs-overview.md).

#### <a name="cause"></a>Orsak

Principtilldelningen har konfigurerats för en [**enforcementMode-inställning**](../concepts/assignment-structure.md#enforcement-mode) som är _Inaktiverad._
När **enforcementMode** är inaktiverat tillämpas inte principeffekten och det finns ingen post i aktivitetsloggen.

#### <a name="resolution"></a>Lösning

Felsök principtilldelningens tillämpning genom att göra följande:

1. Vänta först lämplig tid tills en utvärdering har avslutats och kompatibilitetsresultaten blir tillgängliga i Azure Portal eller SDK. 

1. Om du vill starta en ny utvärderingsgenomsökning med Azure PowerShell eller REST API, se [Utvärderingsgenomsökning på begäran.](../how-to/get-compliance-data.md#on-demand-evaluation-scan)
1. Kontrollera att tilldelningsparametrarna och tilldelningsomfånget är korrekt inställda och att **enforcementMode** är _Aktiverat._
1. Kontrollera [principdefinitionsläget](../concepts/definition-structure.md#mode):
   - Läget ska vara för `all` alla resurstyper.
   - Läget ska vara om `indexed` principdefinitionen söker efter taggar eller plats.
1. Se till att resursens omfång inte [undantas](../concepts/assignment-structure.md#excluded-scopes) eller [undantas.](../concepts/exemption-structure.md)
1. Kontrollera att resursnyttolasten matchar principlogiken. Detta kan göras genom att [samla in en HTTP-arkivspårning (HAR)](../../../azure-portal/capture-browser-trace.md) eller granska Azure Resource Manager (ARM-mall) egenskaper.
1. Information om andra vanliga problem och lösningar finns [i Felsöka: Efterlevnad är inte som förväntat.](#scenario-compliance-isnt-as-expected)

Om du fortfarande har problem med din duplicerade och anpassade inbyggda principdefinition eller anpassade definition skapar du en supportbiljett **under** Redigera en princip för att dirigera problemet korrekt.

### <a name="scenario-denied-by-azure-policy"></a>Scenario: Nekas av Azure Policy

#### <a name="issue"></a>Problem

Skapande eller uppdatering av en resurs nekas.

#### <a name="cause"></a>Orsak

En principtilldelning till omfånget för din nya eller uppdaterade resurs uppfyller kriterierna för en principdefinition med [en Neka-effekt.](../concepts/effects.md#deny) Resurser som uppfyller dessa definitioner förhindras från att skapas eller uppdateras.

#### <a name="resolution"></a>Lösning

Felmeddelandet från en principtilldelning som nekar innehåller principdefinitionen och principtilldelnings-ID:erna. Om felinformationen i meddelandet missas är den också tillgänglig i [aktivitetsloggen](../../../azure-monitor/essentials/activity-log.md#view-the-activity-log). Använd den här informationen för att få mer information för att förstå resursbegränsningarna och justera resursegenskaperna i din begäran så att de matchar tillåtna värden.

### <a name="scenario-definition-targets-multiple-resource-types"></a>Scenario: Definitionen riktar in sig på flera resurstyper

#### <a name="issue"></a>Problem

En principdefinition som innehåller flera resurstyper misslyckas vid verifiering eller uppdatering med följande fel:

```error
The policy definition '{0}' targets multiple resource types, but the policy rule is authored in a way that makes the policy not applicable to the target resource types '{1}'.
```

#### <a name="cause"></a>Orsak

Principdefinitionsregeln har ett eller flera villkor som inte utvärderas av målresurstyperna.

#### <a name="resolution"></a>Lösning

Om ett alias används kontrollerar du att aliaset endast utvärderas mot den resurstyp som det tillhör genom att lägga till ett typvillkor före det. Ett alternativ är att dela upp principdefinitionen i flera definitioner för att undvika att rikta in flera resurstyper.

## <a name="template-errors"></a>Mallfel

### <a name="scenario-policy-supported-functions-processed-by-template"></a>Scenario: Funktioner som stöds av principen bearbetas av mallen

#### <a name="issue"></a>Problem

Azure Policy stöder ett antal ARM-mallfunktioner och funktioner som endast är tillgängliga i en principdefinition. Resource Manager dessa funktioner som en del av en distribution i stället för som en del av en principdefinition.

#### <a name="cause"></a>Orsak

Om du använder funktioner som stöds, till exempel eller , resulterar det bearbetade resultatet av funktionen vid distributionen i stället för att funktionen för `parameter()` `resourceGroup()` principdefinitionen och Azure Policy-motorn kan bearbetas.

#### <a name="resolution"></a>Lösning

Om du vill skicka en funktion genom som en del av en principdefinition, så undvik hela strängen `[` med så att egenskapen ser ut som `[[resourceGroup().tags.myTag]` . Escape-tecknet gör Resource Manager att behandla värdet som en sträng när mallen bearbetar den. Azure Policy placerar sedan funktionen i principdefinitionen, vilket gör att den kan vara dynamisk som förväntat. Mer information finns i [Syntax och uttryck i Azure Resource Manager mallar](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="add-on-for-kubernetes-installation-errors"></a>Tillägg för Kubernetes-installationsfel

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-of-a-password-error"></a>Scenario: Installationen med hjälp av ett Helm-diagram misslyckas på grund av ett lösenordsfel

#### <a name="issue"></a>Problem

Kommandot `helm install azure-policy-addon` misslyckas och returnerar något av följande fel:

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>Orsak

Det genererade lösenordet innehåller ett kommatecken ( `,` ), som Helm-diagrammet delar upp sig på.

#### <a name="resolution"></a>Lösning

När du kör `helm install azure-policy-addon` kan du escape-använda kommatecken ( `,` ) i lösenordsvärdet med ett omsnedstreck ( `\` ).

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-the-name-already-exists"></a>Scenario: Installationen med hjälp av ett Helm-diagram misslyckas eftersom namnet redan finns

#### <a name="issue"></a>Problem

Kommandot `helm install azure-policy-addon` misslyckas och returnerar följande fel:

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>Orsak

Helm-diagrammet med namnet har `azure-policy-addon` redan installerats eller delvis installerats.

#### <a name="resolution"></a>Lösning

Följ instruktionerna för [att ta Azure Policy för Kubernetes-tillägget](../concepts/policy-for-kubernetes.md#remove-the-add-on)och kör sedan kommandot `helm install azure-policy-addon` igen.

### <a name="scenario-azure-virtual-machine-user-assigned-identities-are-replaced-by-system-assigned-managed-identities"></a>Scenario: Användar tilldelade identiteter för virtuella Azure-datorer ersätts med system tilldelade hanterade identiteter

#### <a name="issue"></a>Problem

När du har tilldelat gästkonfigurationsprincipinitiativ för att granska inställningar på en dator, tilldelas inte längre de användar tilldelade hanterade identiteterna som har tilldelats datorn. Endast en system tilldelad hanterad identitet tilldelas.

#### <a name="cause"></a>Orsak

Principdefinitionerna som tidigare användes i gästkonfigurationens DeployIfNotExists-definitioner säkerställde att en system tilldelad identitet har tilldelats till datorn, men de tog även bort de användartilldelningar som tilldelats användaren.

#### <a name="resolution"></a>Lösning

Definitionerna som tidigare orsakade _\[ \]_ det här problemet visas som inaktuella och ersätts av principdefinitioner som hanterar krav utan att ta bort användar tilldelade hanterade identiteter. Det krävs ett manuellt steg. Ta bort alla befintliga principtilldelningar som har markerats _\[ \]_ som inaktuella och ersätt dem med det uppdaterade nödvändiga principinitiativ och principdefinitioner som har samma namn som originalet.

En detaljerad berättelse finns i blogginlägget Important [change released for Guest Configuration audit policies](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316).

## <a name="add-on-for-kubernetes-general-errors"></a>Tillägg för allmänna Kubernetes-fel

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-egress-restrictions"></a>Scenario: Tillägget kan inte nå slutpunkten för Azure Policy på grund av begränsningar för utgående trafik

#### <a name="issue"></a>Problem

Tillägget kan inte nå slutpunkten för Azure Policy och returnerar något av följande fel:

- `failed to fetch token, service not reachable`
- `Error getting file "Get https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml: dial tcp 151.101.228.133.443: connect: connection refused`

#### <a name="cause"></a>Orsak

Det här problemet uppstår när ett utgående kluster är låst.

#### <a name="resolution"></a>Lösning

Kontrollera att de domäner och portar som anges i följande artiklar är öppna:

- [Obligatoriska regler för utgående nätverk och fullständigt kvalificerade domännamn (FQDN) för AKS-kluster](../../../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
- [Installera Azure Policy för kubernetes Azure Arc (förhandsversion)](../concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-the-aad-pod-identity-configuration"></a>Scenario: Tillägget kan inte nå slutpunkten för Azure Policy på grund av konfigurationen av aad-pod-identity

#### <a name="issue"></a>Problem

Tillägget kan inte nå slutpunkten för Azure Policy och returnerar något av följande fel:

- `azure.BearerAuthorizer#WithAuthorization: Failed to refresh the Token for request to https://gov-prod-policy-data.trafficmanager.net/checkDataPolicyCompliance?api-version=2019-01-01-preview: StatusCode=404`
- `adal: Refresh request failed. Status Code = '404'. Response body: getting assigned identities for pod kube-system/azure-policy-8c785548f-r882p in CREATED state failed after 16 attempts, retry duration [5]s, error: <nil>`

#### <a name="cause"></a>Orsak

Det här felet uppstår _när add-pod-identity_ installeras i klustret och _kube-system-poddarna_ inte exkluderas i _aad-pod-identity_.

Komponenten _aad-pod-identity_ Hanterad identitet för noder (NMI) ändrar nodernas iptables för att fånga upp anrop till azure-instansens metadataslutpunkt. Den här konfigurationen innebär att alla förfrågningar som görs till metadataslutpunkten fångas upp av NMI, även om podden inte använder _aad-pod-identity_.
_AzurePodIdentityException_ CustomResourceDefinition (CRD) kan konfigureras för att informera _aad-pod-identity_ om att alla begäranden till en metadataslutpunkt som kommer från en podd som matchar etiketterna som definierats i CRD ska ske med proxy utan någon bearbetning i NMI.

#### <a name="resolution"></a>Lösning

Undanta de systempoddar som har etiketten i `kubernetes.azure.com/managedby: aks` _namnområdet kube-system_ i _aad-pod-identity_ genom att konfigurera _CRD:en AzurePodIdentityException._

Mer information finns i [Inaktivera identiteten Azure Active Directory podd (Azure AD) för en specifik pod/program](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).

Om du vill konfigurera ett undantag följer du det här exemplet:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: mic-exception
  namespace: default
spec:
  podLabels:
    app: mic
    component: mic
---
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: aks-addon-exception
  namespace: kube-system
spec:
  podLabels:
    kubernetes.azure.com/managedby: aks
```

### <a name="scenario-the-resource-provider-isnt-registered"></a>Scenario: Resursprovidern är inte registrerad

#### <a name="issue"></a>Problem

Tillägget kan nå slutpunkten för Azure Policy-tjänsten, men tilläggsloggarna visar något av följande fel:

- `The resource provider 'Microsoft.PolicyInsights' is not registered in subscription '{subId}'. See
  https://aka.ms/policy-register-subscription for how to register subscriptions.`

- `policyinsightsdataplane.BaseClient#CheckDataPolicyCompliance: Failure responding to request:
  StatusCode=500 -- Original Error: autorest/azure: Service returned an error. Status=500
  Code="InternalServerError" Message="Encountered an internal server error.`

#### <a name="cause"></a>Orsak

Resursprovidern "Microsoft.PolicyInsights" är inte registrerad. Det måste vara registrerat för tillägget för att hämta principdefinitioner och returnera efterlevnadsdata.

#### <a name="resolution"></a>Lösning

Registrera resursprovidern Microsoft.PolicyInsights i klusterprenumerationen. Anvisningar finns i Registrera [en resursprovider.](../../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)

### <a name="scenario-the-subscription-is-disabled"></a>Scenario: Prenumerationen är inaktiverad

#### <a name="issue"></a>Problem

Tillägget kan nå slutpunkten Azure Policy tjänst, men följande fel visas:

`The subscription '{subId}' has been disabled for azure data-plane policy. Please contact support.`

#### <a name="cause"></a>Orsak

Det här felet innebär att prenumerationen har fastställts vara problematisk och att funktionsflaggan `Microsoft.PolicyInsights/DataPlaneBlocked` har lagts till för att blockera prenumerationen.

#### <a name="resolution"></a>Lösning

Kontakta funktionsteamet för att undersöka [och lösa problemet.](mailto:azuredg@microsoft.com)

## <a name="next-steps"></a>Nästa steg

Om problemet inte finns med i den här artikeln eller om du inte kan lösa det kan du få support genom att besöka någon av följande kanaler:

- Få svar från experter via [Microsoft Q&A](/answers/topics/azure-policy.html).
- Anslut med [@AzureSupport](https://twitter.com/azuresupport) . Den här Microsoft Azure resursen på Twitter hjälper till att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt svar, support och experter.
- Om du fortfarande behöver hjälp går du till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och väljer **Skicka en supportbegäran.**
