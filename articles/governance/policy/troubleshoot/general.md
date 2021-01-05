---
title: Felsöka vanliga fel
description: 'Lär dig hur du felsöker problem med att skapa princip definitioner, de olika SDK: n och tillägget för Kubernetes.'
ms.date: 12/01/2020
ms.topic: troubleshooting
ms.openlocfilehash: b88d00575adb571c59b562d25067c4a1716fb50f
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97882984"
---
# <a name="troubleshoot-errors-using-azure-policy"></a>Felsöka fel med hjälp av Azure Policy

Du kan stöta på fel när du skapar princip definitioner, arbetar med SDK eller konfigurerar [Azure policy för Kubernetes](../concepts/policy-for-kubernetes.md) -tillägg. I den här artikeln beskrivs olika allmänna fel som kan uppstå och hur du kan lösa dem.

## <a name="finding-error-details"></a>Hitta fel information

Fel informationens plats beror på vilken åtgärd som orsakar felet.

- När du arbetar med en anpassad princip kan du prova den i Azure Portal för att få en mer beskrivande feedback om schemat eller granska resulterande [efterlevnadsprinciper](../how-to/get-compliance-data.md) för att se hur resurserna utvärderades.
- När du arbetar med olika SDK ger SDK information om varför funktionen misslyckades.
- När du arbetar med tillägget för Kubernetes börjar du med [loggningen](../concepts/policy-for-kubernetes.md#logging) i klustret.

## <a name="general-errors"></a>Allmänna fel

### <a name="scenario-alias-not-found"></a>Scenario: aliaset hittades inte

#### <a name="issue"></a>Problem

Azure Policy använder [alias](../concepts/definition-structure.md#aliases) för att mappa till Azure Resource Manager egenskaper.

#### <a name="cause"></a>Orsak

Ett felaktigt eller obefintligt alias används i en princip definition.

#### <a name="resolution"></a>Lösning

Kontrol lera först att Resource Manager-egenskapen har ett alias. Använd [Azure policy-tillägget för Visual Studio Code](../how-to/extension-for-vscode.md) eller SDK för att slå upp tillgängliga alias. Om alias för en Resource Manager-egenskap inte finns skapar du ett support ärende.

### <a name="scenario-evaluation-details-not-up-to-date"></a>Scenario: utvärderings informationen är inte aktuell

#### <a name="issue"></a>Problem

En resurs har statusen "Ej startad" eller så är kompatibilitetsinformation inte aktuella.

#### <a name="cause"></a>Orsak

En ny princip eller initiativ tilldelning tar cirka 30 minuter att tillämpas. Nya eller uppdaterade resurser inom omfånget för en befintlig tilldelning blir tillgängliga cirka 15 minuter senare. En sökning efter standard kompatibilitet sker var 24: e timme. Mer information finns i [utvärderings utlösare](../how-to/get-compliance-data.md#evaluation-triggers).

#### <a name="resolution"></a>Lösning

Börja med att vänta på rätt tids period för att en utvärdering ska slutföras och efterföljande resultat ska bli tillgängliga i Azure Portal eller SDK. Om du vill starta en ny utvärderings sökning med Azure PowerShell eller REST API, se [utvärderings genomsökning på begäran](../how-to/get-compliance-data.md#on-demand-evaluation-scan).

### <a name="scenario-compliance-not-as-expected"></a>Scenario: kompatibiliteten är inte som förväntat

#### <a name="issue"></a>Problem

En resurs är inte i utvärderings tillstånd, antingen _kompatibel_ eller _inte kompatibel_, som förväntas för resursen.

#### <a name="cause"></a>Orsak

Resursen är inte i rätt omfattning för princip tilldelningen eller så fungerar inte princip definitionen som avsett.

#### <a name="resolution"></a>Lösning

Följ de här stegen för att felsöka princip definitionen:

1. Börja med att vänta på rätt tids period för att en utvärdering ska slutföras och efterföljande resultat ska bli tillgängliga i Azure Portal eller SDK. Om du vill starta en ny utvärderings sökning med Azure PowerShell eller REST API, se [utvärderings genomsökning på begäran](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Kontrol lera att tilldelnings parametrarna och tilldelnings omfånget har angetts korrekt.
1. Kontrollera [principdefinitionsläget](../concepts/definition-structure.md#mode):
   - Mode all för alla resurs typer.
   - Läget Indexerad om princip definitionen söker efter taggar eller plats.
1. Kontrol lera att resursens omfattning inte är [Exkluderad](../concepts/assignment-structure.md#excluded-scopes) eller [undantagen](../concepts/exemption-structure.md).
1. Om kompatibiliteten för en princip tilldelning visar `0/0` resurser, fastställdes inga resurser som är tillämpliga inom tilldelnings omfånget. Kontrol lera både princip definitionen och tilldelnings omfånget.
1. För en icke-kompatibel resurs som förväntades vara kompatibel kontrollerar du [varför det inte](../how-to/determine-non-compliance.md)är kompatibelt. Jämförelsen av definitionen för det utvärderade egenskap svärdet indikerar varför en resurs inte var kompatibel.
   - Om **målvärdet** är fel, ändra princip definitionen.
   - Om det **aktuella värdet** är fel kontrollerar du resurs nytto lasten via `resources.azure.com` .
1. Kontrol lera [fel sökning: tvång inte som förväntat](#scenario-enforcement-not-as-expected) för andra vanliga problem och lösningar.

Om du fortfarande har problem med en duplicerad och anpassad inbyggd princip definition eller anpassad definition, skapar du ett support ärende under redigering av **en princip** för att dirigera problemet korrekt.

### <a name="scenario-enforcement-not-as-expected"></a>Scenario: tvång inte som förväntat

#### <a name="issue"></a>Problem

En resurs som förväntas bli påverkad av Azure Policy är inte och det finns ingen post i [Azure aktivitets loggen](../../../azure-monitor/platform/platform-logs-overview.md).

#### <a name="cause"></a>Orsak

Princip tilldelningen har kon figurer ATS [](../concepts/assignment-structure.md#enforcement-mode) för EnforcementMode _inaktive rad_. Även om tvingande läge är inaktiverat tillämpas inte princip påverkan och det finns ingen post i aktivitets loggen.

#### <a name="resolution"></a>Lösning

Följ de här stegen för att felsöka din princip tilldelnings genomförande:

1. Börja med att vänta på rätt tids period för att en utvärdering ska slutföras och efterföljande resultat ska bli tillgängliga i Azure Portal eller SDK. Om du vill starta en ny utvärderings sökning med Azure PowerShell eller REST API, se [utvärderings genomsökning på begäran](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Kontrol lera att tilldelnings parametrarna och tilldelnings omfånget är rätt inställda och att **enforcementMode** har _Aktiver ATS_.
1. Kontrollera [principdefinitionsläget](../concepts/definition-structure.md#mode):
   - Mode all för alla resurs typer.
   - Läget Indexerad om princip definitionen söker efter taggar eller plats.
1. Kontrol lera att resursens omfattning inte är [Exkluderad](../concepts/assignment-structure.md#excluded-scopes) eller [undantagen](../concepts/exemption-structure.md).
1. Kontrollera att resursnyttolasten matchar principlogiken. Detta kan göras genom att [fånga in en spårning](../../../azure-portal/capture-browser-trace.md) eller granska arm-mallens egenskaper.
1. Kontrol lera [fel sökning: kompatibiliteten är inte lika förväntat](#scenario-compliance-not-as-expected) för andra vanliga problem och lösningar.

Om du fortfarande har problem med en duplicerad och anpassad inbyggd princip definition eller anpassad definition, skapar du ett support ärende under redigering av **en princip** för att dirigera problemet korrekt.

### <a name="scenario-denied-by-azure-policy"></a>Scenario: nekas av Azure Policy

#### <a name="issue"></a>Problem

Skapandet eller uppdateringen av en resurs nekas.

#### <a name="cause"></a>Orsak

En princip tilldelning till omfånget som din nya eller uppdaterade resurs uppfyller villkoren i en princip definition med en [neka](../concepts/effects.md#deny) -påverkan. Resurs möten dessa definitioner hindras från att skapas eller uppdateras.

#### <a name="resolution"></a>Lösning

Fel meddelandet från en princip tilldelning neka inkluderar princip definitionen och princip tilldelnings-ID: n. Om fel informationen i meddelandet saknas är det också tillgängligt i [aktivitets loggen](../../../azure-monitor/platform/activity-log.md#view-the-activity-log). Använd den här informationen om du vill ha mer information för att förstå resurs begränsningarna och justera resurs egenskaperna i din begäran om du vill matcha tillåtna värden.

## <a name="template-errors"></a>Fel i mall

### <a name="scenario-policy-supported-functions-processed-by-template"></a>Scenario: funktioner som stöds av principer som bearbetas av mall

#### <a name="issue"></a>Problem

Azure Policy stöder ett antal mallar för Azure Resource Manager mallar (ARM-mallar) som bara är tillgängliga i en princip definition. Resource Manager bearbetar dessa funktioner som en del av en distribution i stället för som en del av en princip definition.

#### <a name="cause"></a>Orsak

Att använda funktioner som stöds, till exempel `parameter()` eller `resourceGroup()` , resulterar i det behandlade resultatet av funktionen vid distributions tiden i stället för att lämna funktionen för princip definitionen och Azure policy motorn att bearbeta.

#### <a name="resolution"></a>Lösning

Om du vill skicka en funktion genom att vara en del av en princip definition kan du undanta hela strängen med `[` så att egenskapen ser ut som `[[resourceGroup().tags.myTag]` . Escape-tecken gör att Resource Manager hanterar värdet som en sträng när mallen bearbetas. Azure Policy placerar sedan funktionen i princip definitionen så att den kan vara dynamisk som förväntat. Mer information finns [i syntax och uttryck i Azure Resource Manager mallar](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="add-on-for-kubernetes-installation-errors"></a>Tillägg för Kubernetes-installations fel

### <a name="scenario-install-using-helm-chart-fails-on-password"></a>Scenario: det går inte att installera med Helm-diagram på lösen ord

#### <a name="issue"></a>Problem

`helm install azure-policy-addon`Kommandot Miss lyckas med ett av följande meddelanden:

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>Orsak

Det genererade lösen ordet innehåller ett kommatecken ( `,` ) som Helm-diagrammet delar upp.

#### <a name="resolution"></a>Lösning

Undanta kommatecken ( `,` ) i lösen ordet när du kör `helm install azure-policy-addon` med ett omvänt snedstreck ( `\` ).

### <a name="scenario-install-using-helm-chart-fails-as-name-already-exists"></a>Scenario: det går inte att installera med Helm-diagram eftersom namnet redan finns

#### <a name="issue"></a>Problem

`helm install azure-policy-addon`Kommandot Miss lyckas med följande meddelande:

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>Orsak

Helm-diagrammet med namnet `azure-policy-addon` har redan installerats eller är delvis installerat.

#### <a name="resolution"></a>Lösning

Följ anvisningarna för att [ta bort Azure policy för Kubernetes-tillägget](../concepts/policy-for-kubernetes.md#remove-the-add-on)och kör sedan kommandot igen `helm install azure-policy-addon` .

### <a name="scenario-azure-virtual-machine-user-assigned-identities-are-replaced-by-system-assigned-managed-identities"></a>Scenario: användar tilldelningar för virtuella Azure-datorer ersätts av systemtilldelade hanterade identiteter

#### <a name="issue"></a>Problem

När du har tilldelat initiativen för gäst konfigurations principer till gransknings inställningar i datorer, tilldelas inte längre tilldelade hanterade identiteter som har tilldelats datorn. Endast en tilldelad hanterad identitet har tilldelats.

#### <a name="cause"></a>Orsak

De princip definitioner som tidigare användes i DeployIfNotExists-definitioner för gäst konfiguration säkerställer att en tilldelad identitet tilldelas till datorn men även borttagna tilldelade identitets tilldelningar.

#### <a name="resolution"></a>Lösning

Definitionerna som tidigare orsakade det här problemet visas som \[ föråldrade \] och ersätts av princip definitioner som hanterar krav utan att användarens tilldelade hanterade identitet tas bort. En manuell åtgärd krävs. Ta bort eventuella befintliga princip tilldelningar som är markerade som \[ föråldrade \] och ersätt dem med det uppdaterade nödvändiga princip-initiativ och princip definitioner som har samma namn som originalet.

En detaljerad beskrivning finns i följande blogg inlägg:

[Viktig ändring har släppts för gransknings principer för gäst konfiguration](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316)

## <a name="add-on-for-kubernetes-general-errors"></a>Tillägg för Kubernetes allmänna fel

### <a name="scenario-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-due-to-egress-restrictions"></a>Scenario: det går inte att ansluta till Azure Policy tjänstens slut punkt på grund av utgående begränsningar

#### <a name="issue"></a>Problem

Tillägget kan inte komma åt Azure Policy tjänstens slut punkt och returnerar något av följande fel:

- `failed to fetch token, service not reachable`
- `Error getting file "Get https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml: dial tcp 151.101.228.133.443: connect: connection refused`

#### <a name="cause"></a>Orsak

Det här problemet uppstår när ett utgående kluster är låst.

#### <a name="resolution"></a>Lösning

Se till att domänerna och portarna i följande artiklar är öppna:

- [Nödvändiga utgående nätverks regler och FQDN för AKS-kluster](../../../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
- [Installera Azure Policy tillägg för Azure Arc-aktiverade Kubernetes (för hands version)](../concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)

### <a name="scenario-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-due-to-aad-pod-identity-configuration"></a>Scenario: det går inte att ansluta till Azure Policy tjänstens slut punkt på grund av AAD-Pod-Identity Configuration

#### <a name="issue"></a>Problem

Tillägget kan inte komma åt Azure Policy tjänstens slut punkt och returnerar något av följande fel:

- `azure.BearerAuthorizer#WithAuthorization: Failed to refresh the Token for request to https://gov-prod-policy-data.trafficmanager.net/checkDataPolicyCompliance?api-version=2019-01-01-preview: StatusCode=404`
- `adal: Refresh request failed. Status Code = '404'. Response body: getting assigned identities for pod kube-system/azure-policy-8c785548f-r882p in CREATED state failed after 16 attempts, retry duration [5]s, error: <nil>`

#### <a name="cause"></a>Orsak

Det här felet uppstår när _Add-Pod-Identity_ installeras i klustret och _Kube-system_ poddar inte ingår i _AAD-Pod-Identity_.

_AAD-Pod-Identity_ -nodens hanterade identitet (NMI) poddar ändra noderna program varan iptables för att avlyssna anrop till Azure instance metadata-slutpunkten. Den här inställningen innebär att alla begär Anden som görs till metadata-slutpunkten fångas upp av NMI även om Pod inte använder _AAD-Pod-Identity_.
**AzurePodIdentityException** CRD kan konfigureras för att informera _AAD-Pod-identiteten_ att förfrågningar till metadata-slutpunkten från en pod som matchar etiketter som definierats i CRD ska vara proxy utan bearbetning i NMI.

#### <a name="resolution"></a>Lösning

Undanta system poddar med `kubernetes.azure.com/managedby: aks` etikett i _Kube-systemets_ namnrymd i _AAD-Pod-Identity_ genom att konfigurera **AzurePodIdentityException** CRD.

Mer information finns i [inaktivera AAD Pod-identitet för en specifik Pod/applikation](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).

Information om hur du konfigurerar ett undantag finns i det här exemplet:

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

Tillägget kan komma åt Azure Policy tjänstens slut punkt, men ser något av följande fel i tilläggs loggar:

```
The resource provider 'Microsoft.PolicyInsights' is not registered in subscription '{subId}'. See
https://aka.ms/policy-register-subscription for how to register subscriptions.
```

eller

```
policyinsightsdataplane.BaseClient#CheckDataPolicyCompliance: Failure responding to request:
StatusCode=500 -- Original Error: autorest/azure: Service returned an error. Status=500
Code="InternalServerError" Message="Encountered an internal server error."
```

#### <a name="cause"></a>Orsak

`Microsoft.PolicyInsights`Resurs leverantören är inte registrerad och måste vara registrerad för att tillägget ska kunna hämta princip definitioner och returnera efterlevnadsprinciper.

#### <a name="resolution"></a>Lösning

Registrera `Microsoft.PolicyInsights` resurs leverantören i kluster prenumerationen. Anvisningar finns i [Registrera en resurs leverantör](../../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).

### <a name="scenario-the-subscription-is-disabled"></a>Scenario: prenumerationen är inaktive rad

#### <a name="issue"></a>Problem

Tillägget kan komma åt Azure Policy tjänstens slut punkt, men ser följande fel:

```
The subscription '{subId}' has been disabled for azure data-plane policy. Please contact support.
```

#### <a name="cause"></a>Orsak

Det här felet innebär att prenumerationen har bedömts vara problematisk och funktions flaggan `Microsoft.PolicyInsights/DataPlaneBlocked` lades till för att blockera prenumerationen.

#### <a name="resolution"></a>Lösning

Kontakta funktions teamet `azuredg@microsoft.com` för att undersöka och lösa problemet.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

- Få svar från experter via [Microsoft Q&A](/answers/topics/azure-policy.html).
- Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
- Om du behöver mer hjälp kan du skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.
