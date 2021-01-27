---
title: Felsöka vanliga fel
description: 'Lär dig hur du felsöker problem med att skapa princip definitioner, de olika SDK: erna och tillägget för Kubernetes.'
ms.date: 01/26/2021
ms.topic: troubleshooting
ms.openlocfilehash: 0a64346188696cc7cc16d832474ec4ee6befdae2
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98917751"
---
# <a name="troubleshoot-errors-with-using-azure-policy"></a>Felsöka fel med hjälp av Azure Policy

När du skapar princip definitioner, arbetar med SDK: er eller konfigurerar [Azure policy för Kubernetes](../concepts/policy-for-kubernetes.md) -tillägg kan du stöta på fel. I den här artikeln beskrivs olika allmänna fel som kan uppstå, och det föreslår olika sätt att lösa dem.

## <a name="find-error-details"></a>Hitta fel information

Fel informationens plats beror på vilken aspekt av Azure Policy du arbetar med.

- Om du arbetar med en anpassad princip går du till Azure Portal för att få en mer beskrivande feedback om schemat eller granskar resulterande [efterlevnadsprinciper](../how-to/get-compliance-data.md) för att se hur resurserna utvärderades.
- Om du arbetar med någon av de olika SDK: erna innehåller SDK information om varför funktionen misslyckades.
- Om du arbetar med tillägget för Kubernetes börjar du med [loggningen](../concepts/policy-for-kubernetes.md#logging) i klustret.

## <a name="general-errors"></a>Allmänna fel

### <a name="scenario-alias-not-found"></a>Scenario: aliaset hittades inte

#### <a name="issue"></a>Problem

Ett felaktigt eller obefintligt alias används i en princip definition. Azure Policy använder [alias](../concepts/definition-structure.md#aliases) för att mappa till Azure Resource Manager egenskaper.

#### <a name="cause"></a>Orsak

Ett felaktigt eller obefintligt alias används i en princip definition.

#### <a name="resolution"></a>Lösning

Kontrol lera först att Resource Manager-egenskapen har ett alias. Om du vill leta upp tillgängliga alias går du till [Azure policy tillägg för Visual Studio Code](../how-to/extension-for-vscode.md) eller SDK.
Om alias för en Resource Manager-egenskap inte finns skapar du ett support ärende.

### <a name="scenario-evaluation-details-arent-up-to-date"></a>Scenario: utvärderings informationen är inte aktuell

#### <a name="issue"></a>Problem

En resurs är i ett tillstånd där det _inte har startats_ eller så är kompatibilitetsinformation inte aktuella.

#### <a name="cause"></a>Orsak

En ny princip eller initiativ tilldelning tar cirka 30 minuter att tillämpas. Nya eller uppdaterade resurser inom omfånget för en befintlig tilldelning blir tillgängliga inom 15 minuter. En sökning efter standard kompatibilitet sker var 24: e timme. Mer information finns i [utvärderings utlösare](../how-to/get-compliance-data.md#evaluation-triggers).

#### <a name="resolution"></a>Lösning

Börja med att vänta en lämplig tids period för att en utvärderings version ska slutföras och att resultatet av efterlevnaden blir tillgängligt i Azure Portal eller SDK. Om du vill starta en ny utvärderings sökning med Azure PowerShell eller REST API, se [utvärderings genomsökning på begäran](../how-to/get-compliance-data.md#on-demand-evaluation-scan).

### <a name="scenario-compliance-isnt-as-expected"></a>Scenario: kompatibiliteten är inte som förväntat

#### <a name="issue"></a>Problem

En resurs är inte i ett _kompatibelt_ eller _inte kompatibelt_ utvärderings tillstånd som förväntas för resursen.

#### <a name="cause"></a>Orsak

Resursen är inte i rätt omfattning för princip tilldelningen, eller så fungerar inte princip definitionen som tänkt.

#### <a name="resolution"></a>Lösning

Så här felsöker du princip definitionen:

1. Vänta i så fall hur lång tid det tar för en utvärdering att slutföra och att efterföljande resultat blir tillgängliga i Azure Portal eller SDK. 

1. Om du vill starta en ny utvärderings sökning med Azure PowerShell eller REST API, se [utvärderings genomsökning på begäran](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Se till att tilldelnings parametrarna och tilldelnings omfånget har angetts korrekt.
1. Kontrollera [principdefinitionsläget](../concepts/definition-structure.md#mode):
   - Läget bör vara `all` för alla resurs typer.
   - Läget bör vara `indexed` om princip definitionen söker efter taggar eller platser.
1. Kontrol lera att resursens omfattning inte är [utesluten](../concepts/assignment-structure.md#excluded-scopes) eller [undantagen](../concepts/exemption-structure.md).
1. Om kompatibiliteten för en princip tilldelning visar `0/0` resurser, fastställdes inga resurser som är tillämpliga inom tilldelnings omfånget. Kontrol lera både princip definitionen och tilldelnings omfånget.
1. En icke-kompatibel resurs som förväntades vara kompatibel finns i [avgöra orsaken till inkompatibiliteten](../how-to/determine-non-compliance.md). Jämförelsen av definitionen för det utvärderade egenskap svärdet indikerar varför en resurs inte var kompatibel.
   - Om **målvärdet** är fel, ändra princip definitionen.
   - Om det **aktuella värdet** är fel kontrollerar du resurs nytto lasten via `resources.azure.com` .
1. Information om andra vanliga problem och lösningar finns i [Felsöka: tvångs åtgärd är inte som förväntat](#scenario-enforcement-not-as-expected).

Om du fortfarande har problem med en duplicerad och anpassad inbyggd princip definition eller anpassad definition, skapar du ett support ärende under redigering av **en princip** för att dirigera problemet korrekt.

### <a name="scenario-enforcement-not-as-expected"></a>Scenario: tvång inte som förväntat

#### <a name="issue"></a>Problem

En resurs som du förväntar dig Azure Policy att agera på är inte igång och det finns ingen post i [Azures aktivitets logg](../../../azure-monitor/platform/platform-logs-overview.md).

#### <a name="cause"></a>Orsak

Princip tilldelningen har kon figurer ATS för en [**enforcementMode**](../concepts/assignment-structure.md#enforcement-mode) -inställning som är _inaktive rad_.
När **enforcementMode** är inaktive rad tillämpas inte princip påverkan och det finns ingen post i aktivitets loggen.

#### <a name="resolution"></a>Lösning

Felsök din princip tilldelnings genomförande genom att göra följande:

1. Vänta i så fall hur lång tid det tar för en utvärdering att slutföra och att efterföljande resultat blir tillgängliga i Azure Portal eller SDK. 

1. Om du vill starta en ny utvärderings sökning med Azure PowerShell eller REST API, se [utvärderings genomsökning på begäran](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Se till att tilldelnings parametrarna och tilldelnings omfånget är rätt inställda och att **enforcementMode** är _aktiverat_.
1. Kontrollera [principdefinitionsläget](../concepts/definition-structure.md#mode):
   - Läget bör vara `all` för alla resurs typer.
   - Läget bör vara `indexed` om princip definitionen söker efter taggar eller platser.
1. Kontrol lera att resursens omfattning inte är [utesluten](../concepts/assignment-structure.md#excluded-scopes) eller [undantagen](../concepts/exemption-structure.md).
1. Kontrol lera att resurs nytto lasten matchar princip logiken. Detta kan göras genom att spara [en spårning för HTTP-arkiv (har)](../../../azure-portal/capture-browser-trace.md) eller granska Azure Resource Manager mallens egenskaper (arm-mall).
1. Andra vanliga problem och lösningar finns i [Felsöka: kompatibiliteten är inte som förväntat](#scenario-compliance-isnt-as-expected).

Om du fortfarande har problem med en duplicerad och anpassad inbyggd princip definition eller anpassad definition, skapar du ett support ärende under redigering av **en princip** för att dirigera problemet korrekt.

### <a name="scenario-denied-by-azure-policy"></a>Scenario: nekas av Azure Policy

#### <a name="issue"></a>Problem

Skapandet eller uppdateringen av en resurs nekas.

#### <a name="cause"></a>Orsak

En princip tilldelning till omfånget för din nya eller uppdaterade resurs uppfyller villkoren i en princip definition med en [neka](../concepts/effects.md#deny) -påverkan. Resurser som uppfyller dessa definitioner förhindras från att skapas eller uppdateras.

#### <a name="resolution"></a>Lösning

Fel meddelandet från en princip tilldelning neka inkluderar princip definitionen och princip tilldelnings-ID: n. Om fel informationen i meddelandet saknas är det också tillgängligt i [aktivitets loggen](../../../azure-monitor/platform/activity-log.md#view-the-activity-log). Använd den här informationen om du vill ha mer information för att förstå resurs begränsningarna och justera resurs egenskaperna i din begäran om du vill matcha tillåtna värden.

## <a name="template-errors"></a>Fel i mall

### <a name="scenario-policy-supported-functions-processed-by-template"></a>Scenario: funktioner som stöds av principer som bearbetas av mall

#### <a name="issue"></a>Problem

Azure Policy stöder ett antal funktioner och funktioner i ARM-mallar som endast är tillgängliga i en princip definition. Resource Manager bearbetar dessa funktioner som en del av en distribution i stället för som en del av en princip definition.

#### <a name="cause"></a>Orsak

Att använda funktioner som stöds, till exempel `parameter()` eller `resourceGroup()` , resulterar i det behandlade resultatet av funktionen vid distributions tiden i stället för att tillåta funktionen för princip definitionen och Azure policy motorn att bearbeta.

#### <a name="resolution"></a>Lösning

Om du vill skicka en funktion som en del av en princip definition kan du undanta hela strängen med `[` så att egenskapen ser ut som `[[resourceGroup().tags.myTag]` . Escape-tecken gör att Resource Manager hanterar värdet som en sträng när den bearbetar mallen. Azure Policy placerar sedan funktionen i princip definitionen, vilket gör att den kan vara dynamisk som förväntat. Mer information finns [i syntax och uttryck i Azure Resource Manager mallar](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="add-on-for-kubernetes-installation-errors"></a>Tillägg för Kubernetes-installations fel

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-of-a-password-error"></a>Scenario: installationen med hjälp av ett Helm-diagram Miss lyckas på grund av ett lösen ords fel

#### <a name="issue"></a>Problem

`helm install azure-policy-addon`Kommandot Miss lyckas och returnerar något av följande fel:

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>Orsak

Det genererade lösen ordet innehåller ett kommatecken ( `,` ), vilket Helm-diagrammet delas på.

#### <a name="resolution"></a>Lösning

När du kör `helm install azure-policy-addon` kan du undanta kommatecken ( `,` ) i lösen ordet med ett omvänt snedstreck ( `\` ).

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-the-name-already-exists"></a>Scenario: installationen med hjälp av ett Helm-diagram Miss lyckas eftersom namnet redan finns

#### <a name="issue"></a>Problem

`helm install azure-policy-addon`Kommandot Miss lyckas och följande fel returneras:

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>Orsak

Helm-diagrammet med namnet `azure-policy-addon` har redan installerats eller är delvis installerat.

#### <a name="resolution"></a>Lösning

Följ anvisningarna för att [ta bort Azure policy för Kubernetes-tillägget](../concepts/policy-for-kubernetes.md#remove-the-add-on)och kör sedan kommandot igen `helm install azure-policy-addon` .

### <a name="scenario-azure-virtual-machine-user-assigned-identities-are-replaced-by-system-assigned-managed-identities"></a>Scenario: användar tilldelningar för virtuella Azure-datorer ersätts av systemtilldelade hanterade identiteter

#### <a name="issue"></a>Problem

När du har tilldelat initiativen för gäst konfigurations principer till gransknings inställningarna i en dator, tilldelas inte längre tilldelade hanterade identiteter som har tilldelats datorn. Endast en tilldelad hanterad identitet har tilldelats.

#### <a name="cause"></a>Orsak

Princip definitionerna som tidigare användes i DeployIfNotExists-definitioner för gäst konfiguration säkerställer att en tilldelad identitet tilldelas till datorn, men de tar också bort tilldelade identitets tilldelningar.

#### <a name="resolution"></a>Lösning

Definitionerna som tidigare orsakade det här problemet visas som _\[ föråldrade \]_ och de ersätts av princip definitioner som hanterar krav utan att användarens tilldelade hanterade identiteter tas bort. En manuell åtgärd krävs. Ta bort eventuella befintliga princip tilldelningar som är markerade som _\[ föråldrade \]_ och ersätt dem med det uppdaterade nödvändiga princip-initiativ och princip definitioner som har samma namn som originalet.

En detaljerad beskrivning finns i blogg inlägget [viktig ändring som har publicerats för gransknings principer för gäst konfiguration](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316).

## <a name="add-on-for-kubernetes-general-errors"></a>Tillägg för Kubernetes allmänna fel

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-egress-restrictions"></a>Scenario: tillägget kan inte komma åt Azure Policy tjänstens slut punkt på grund av utgående begränsningar

#### <a name="issue"></a>Problem

Tillägget kan inte komma åt Azure Policy tjänstens slut punkt och returnerar något av följande fel:

- `failed to fetch token, service not reachable`
- `Error getting file "Get https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml: dial tcp 151.101.228.133.443: connect: connection refused`

#### <a name="cause"></a>Orsak

Det här problemet uppstår när ett utgående kluster är låst.

#### <a name="resolution"></a>Lösning

Se till att de domäner och portar som anges i följande artiklar är öppna:

- [Nödvändiga utgående nätverks regler och fullständigt kvalificerade domän namn (FQDN) för AKS-kluster](../../../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
- [Installera Azure Policy-tillägget för Azure Arc-aktiverade Kubernetes (för hands version)](../concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-the-aad-pod-identity-configuration"></a>Scenario: tillägget kan inte komma åt Azure Policy tjänstens slut punkt på grund av AAD-Pod-Identity-konfigurationen

#### <a name="issue"></a>Problem

Tillägget kan inte komma åt Azure Policy tjänstens slut punkt och returnerar något av följande fel:

- `azure.BearerAuthorizer#WithAuthorization: Failed to refresh the Token for request to https://gov-prod-policy-data.trafficmanager.net/checkDataPolicyCompliance?api-version=2019-01-01-preview: StatusCode=404`
- `adal: Refresh request failed. Status Code = '404'. Response body: getting assigned identities for pod kube-system/azure-policy-8c785548f-r882p in CREATED state failed after 16 attempts, retry duration [5]s, error: <nil>`

#### <a name="cause"></a>Orsak

Felet uppstår när _Add-Pod-Identity_ är installerat i klustret och _Kube-_ poddar inte är exkluderat i _AAD-Pod-Identity_.

_AAD-Pod-Identity_ -nodens hanterade identitet (NMI) poddar ändra noderna program varan iptables för att avlyssna anrop till Azure instance metadata-slutpunkten. Den här inställningen innebär att alla begär Anden som görs till metadata-slutpunkten fångas upp av NMI, även om Pod inte använder _AAD-Pod-Identity_.
_AzurePodIdentityException_ -CUSTOMRESOURCEDEFINITION (CRD) kan konfigureras för att informera _AAD-Pod-identiteten_ att förfrågningar till en slut punkt för metadata som kommer från en pod som matchar etiketterna som definierats i CRD ska vara proxy utan bearbetning i NMI.

#### <a name="resolution"></a>Lösning

Undanta system-poddar som har `kubernetes.azure.com/managedby: aks` etiketten i _Kube-systemets_ namnrymd i _AAD-Pod-Identity_ genom att konfigurera _AzurePodIdentityException_ CRD.

Mer information finns i [inaktivera Azure Active Directory (Azure AD) Pod-identitet för en specifik Pod/tillämpning](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).

Följ det här exemplet om du vill konfigurera ett undantag:

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

### <a name="scenario-the-resource-provider-isnt-registered"></a>Scenario: resurs leverantören har inte registrerats

#### <a name="issue"></a>Problem

Tillägget kan komma åt Azure Policy tjänstens slut punkt, men tilläggs loggarna visar något av följande fel:

- `The resource provider 'Microsoft.PolicyInsights' is not registered in subscription '{subId}'. See
  https://aka.ms/policy-register-subscription for how to register subscriptions.`

- `policyinsightsdataplane.BaseClient#CheckDataPolicyCompliance: Failure responding to request:
  StatusCode=500 -- Original Error: autorest/azure: Service returned an error. Status=500
  Code="InternalServerError" Message="Encountered an internal server error.`

#### <a name="cause"></a>Orsak

Resurs leverantören "Microsoft. PolicyInsights" är inte registrerad. Det måste registreras för att tillägget ska kunna hämta princip definitioner och returnera efterlevnadsprinciper.

#### <a name="resolution"></a>Lösning

Registrera resurs leverantören "Microsoft. PolicyInsights" i kluster prenumerationen. Instruktioner finns i [Registrera en resurs leverantör](../../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).

### <a name="scenario-the-subscription-is-disabled"></a>Scenario: prenumerationen är inaktive rad

#### <a name="issue"></a>Problem

Tillägget kan komma åt Azure Policy tjänstens slut punkt, men följande fel visas:

`The subscription '{subId}' has been disabled for azure data-plane policy. Please contact support.`

#### <a name="cause"></a>Orsak

Det här felet innebär att prenumerationen har bedömts vara problematisk och funktions flaggan `Microsoft.PolicyInsights/DataPlaneBlocked` lades till för att blockera prenumerationen.

#### <a name="resolution"></a>Lösning

[Kontakta funktions teamet](mailto:azuredg@microsoft.com)för att undersöka och lösa problemet.

## <a name="next-steps"></a>Nästa steg

Om problemet inte visas i den här artikeln eller om du inte kan lösa det, kan du få support genom att besöka någon av följande kanaler:

- Få svar från experter via [Microsoft Q&A](/answers/topics/azure-policy.html).
- Anslut till [@AzureSupport](https://twitter.com/azuresupport) . Den här officiella Microsoft Azures resursen på Twitter hjälper till att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt svar, support och experter.
- Om du fortfarande behöver hjälp går du till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och väljer **skicka en support förfrågan**.
