---
title: Förstå hur effekter fungerar
description: Azure Policy har olika effekter som avgör hur efterlevnad hanteras och rapporteras.
ms.date: 04/19/2021
ms.topic: conceptual
ms.openlocfilehash: e0d6eb5fb37ecf1b13edd945de52398b1e12f192
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861561"
---
# <a name="understand-azure-policy-effects"></a>Förstå Azure Policy effekter

Varje principdefinition i Azure Policy har en enda effekt. Den effekten avgör vad som händer när principregeln utvärderas för att matcha. Effekterna beter sig annorlunda om de gäller för en ny resurs, en uppdaterad resurs eller en befintlig resurs.

Dessa effekter stöds för närvarande i en principdefinition:

- [Append](#append) (Lägg till)
- [Audit](#audit) (Granska)
- [AuditIfNotExists](#auditifnotexists)
- [Neka](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Disabled](#disabled) (Inaktiverat)
- [Modify](#modify) (Ändra)

Följande effekter _är inaktuella:_

- [EnforceOPAConstraint](#enforceopaconstraint)
- [EnforceRegoPolicy](#enforceregopolicy)

> [!IMPORTANT]
> I stället för effekterna **EnforceOPAConstraint** eller **EnforceRegoPolicy** använder du _granska_ och _neka med_ resursproviderläget `Microsoft.Kubernetes.Data` . De inbyggda principdefinitionerna har uppdaterats. När befintliga principtilldelningar av dessa inbyggda principdefinitioner ändras måste  effektparametern ändras till ett värde i den uppdaterade listan _allowedValues._

## <a name="order-of-evaluation"></a>Utvärderingsordning

Begäranden om att skapa eller uppdatera en resurs utvärderas av Azure Policy först. Azure Policy skapar en lista över alla tilldelningar som gäller för resursen och utvärderar sedan resursen mot varje definition. För ett [Resource Manager läge](./definition-structure.md#resource-manager-modes)Azure Policy flera av effekterna innan du lämnar över begäran till lämplig resursprovider. Den här ordningen förhindrar onödig bearbetning av en resursprovider när en resurs inte uppfyller de utformade styrningskontrollerna för Azure Policy. I läget [Resursprovider](./definition-structure.md#resource-provider-modes)hanterar resursprovidern utvärderingen och resultatet och rapporterar resultatet tillbaka till Azure Policy.

- **Inaktiverad** kontrolleras först för att avgöra om principregeln ska utvärderas.
- **Append** **och Modify** utvärderas sedan. Eftersom antingen kan ändra begäran kan en ändring som görs förhindra att en gransknings- eller neka-effekt utlöses. Dessa effekter är endast tillgängliga med ett Resource Manager läge.
- **Neka** utvärderas sedan. Genom att utvärdera neka före granskning förhindras dubbel loggning av en oönskad resurs.
- **Granskningen** utvärderas sist.

När resursprovidern returnerar en lyckad kod på en Resource Manager-lägesbegäran utvärderar **AuditIfNotExists** och **DeployIfNotExists** för att avgöra om ytterligare efterlevnadsloggning eller åtgärd krävs.

Begäranden som endast `PATCH` ändrar relaterade fält begränsar dessutom `tags` principutvärderingen till principer som innehåller villkor som inspekterar `tags` relaterade fält.

## <a name="append"></a>Lägg till

Lägg till används för att lägga till ytterligare fält i den begärda resursen under skapande eller uppdatering. Ett vanligt exempel är att ange tillåtna IP-adresser för en lagringsresurs.

> [!IMPORTANT]
> Append är avsedd att användas med egenskaper som inte är taggade. Lägg till kan lägga till taggar till en resurs under en begäran om att skapa eller uppdatera, men vi rekommenderar att du använder [effekten Ändra](#modify) för taggar i stället.

### <a name="append-evaluation"></a>Tilläggsutvärdering

Tilläggsvärde utvärderas innan begäran bearbetas av en resursprovider när en resurs skapas eller uppdateras. Lägg till lägger till fält i resursen när **if-villkoret** för principregeln uppfylls. Om tilläggseffekten skulle åsidosätta ett värde i den ursprungliga begäran med ett annat värde, fungerar den som en neka-effekt och avvisar begäran. Om du vill lägga till ett nytt värde i en befintlig matris använder **\[\*\]** du aliasversionen.

När en principdefinition som använder tilläggseffekten körs som en del av en utvärderingscykel görs inga ändringar i resurser som redan finns. I stället markerar den alla resurser som uppfyller **if-villkoret** som icke-kompatibla.

### <a name="append-properties"></a>Lägg till egenskaper

En tilläggseffekt har bara en **informationsmatris,** vilket krävs. Eftersom **information** är en matris kan det ta antingen ett enskilt **fält/värde-par** eller multiplar. Se [definitionsstrukturen för](definition-structure.md#fields) listan över godkända fält.

### <a name="append-examples"></a>Exempel på tillägg

Exempel 1: Ett **fält/värde-par** som använder ett icke-alias med ett **\[\*\]** 
 [matrisvärde](definition-structure.md#aliases) för att ange IP-regler för ett lagringskonto.  När det **\[\*\]** icke-aliaset är en matris lägger effekten till **värdet** som hela matrisen. Om matrisen redan finns inträffar en neka-händelse från konflikten.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

Exempel 2: Ett **fält/värde-par med** ett alias med ett **\[\*\]** [matrisvärde](definition-structure.md#aliases) **för att** ange IP-regler för ett lagringskonto. Med hjälp **\[\*\]** av aliaset lägger effekten till värdet **i en** eventuellt befintlig matris. Om matrisen inte finns ännu skapas den.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]",
        "value": {
            "value": "40.40.40.40",
            "action": "Allow"
        }
    }]
}
```

## <a name="audit"></a>Granska

Granskning används för att skapa en varningshändelse i aktivitetsloggen vid utvärdering av en icke-kompatibel resurs, men den stoppar inte begäran.

### <a name="audit-evaluation"></a>Granskningsutvärdering

Granskning är den sista effekten som kontrolleras Azure Policy när en resurs skapas eller uppdateras. För ett Resource Manager läge skickar Azure Policy sedan resursen till resursprovidern. Granskning fungerar på samma sätt för en resursbegäran och en utvärderingscykel. För nya och uppdaterade resurser Azure Policy lägger till en åtgärd i `Microsoft.Authorization/policies/audit/action` aktivitetsloggen och markerar resursen som icke-kompatibel.

### <a name="audit-properties"></a>Granskningsegenskaper

För ett Resource Manager-läge har inte granskningseffekten några ytterligare egenskaper för användning i **sedan-villkoret** för principdefinitionen.

För resursproviderläget `Microsoft.Kubernetes.Data` har granskningseffekten följande ytterligare underegenskaper med **information**.

- **constraintTemplate** (krävs)
  - Mallen Constraint CustomResourceDefinition (CRD) som definierar nya begränsningar. Mallen definierar Rego-logiken, schemat för begränsning och de begränsningsparametrar som skickas **via** värden från Azure Policy.
- **constraint** (obligatoriskt)
  - CRD-implementeringen av begränsningsmallen. Använder parametrar som skickas via **värden** som `{{ .Values.<valuename> }}` . I exempel 2 nedan är dessa värden `{{ .Values.excludedNamespaces }}` och `{{ .Values.allowedContainerImagesRegex }}` .
- **värden** (valfritt)
  - Definierar alla parametrar och värden som ska överföras till begränsningen. Varje värde måste finnas i CRD-mallen Begränsning.

### <a name="audit-example"></a>Granskningsexempel

Exempel 1: Använda granskningseffekten för Resource Manager lägena.

```json
"then": {
    "effect": "audit"
}
```

Exempel 2: Använda granskningseffekten för resursproviderläget `Microsoft.Kubernetes.Data` . Den ytterligare informationen i detalj **definierar** den begränsningsmall och CRD som ska användas i Kubernetes för att begränsa de tillåtna containeravbildningarna.

```json
"then": {
    "effect": "audit",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/constraint.yaml",
        "values": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]",
            "excludedNamespaces": "[parameters('excludedNamespaces')]"
        }
    }
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists möjliggör granskning  av resurser relaterade till den resurs som matchar **if-villkoret,** men inte har de egenskaper som anges i informationen om **villkoret.** 

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists-utvärdering

AuditIfNotExists körs när en resursprovider har hanterat en begäran om att skapa eller uppdatera en resurs och har returnerat en statuskod för lyckad körning. Granskningen inträffar om det inte finns några relaterade resurser eller om resurserna som definieras av **ExistenceCondition** inte utvärderas till true. För nya och uppdaterade resurser Azure Policy lägger till en åtgärd i aktivitetsloggen `Microsoft.Authorization/policies/audit/action` och markerar resursen som icke-kompatibel. När den utlöses är den resurs som uppfyllt **if-villkoret** den resurs som markerats som icke-kompatibel.

### <a name="auditifnotexists-properties"></a>Egenskaper för AuditIfNotExists

Informationsegenskapen för AuditIfNotExists-effekterna har alla underegenskaper som definierar de relaterade resurserna som ska matchas. 

- **Typ** (krävs)
  - Anger vilken typ av relaterad resurs som ska matchas.
  - Om **details.type** är en resurstyp under **if-villkorsresursen** frågar  principen efter resurser av den här typen inom omfånget för den utvärderade resursen. Annars kan principfrågor inom samma resursgrupp som den utvärderade resursen.
- **Namn** (valfritt)
  - Anger det exakta namnet på resursen som ska matchas och gör att principen hämtar en specifik resurs i stället för alla resurser av den angivna typen.
  - När villkorsvärdena **för if.field.type** och **then.details.type** matchar, blir **Name** obligatoriskt och måste vara , eller för en underordnad  `[field('name')]` `[field('fullName')]` resurs.
    En [granskningseffekt](#audit) bör dock övervägas i stället.
- **ResourceGroupName** (valfritt)
  - Gör att matchningen av den relaterade resursen kan komma från en annan resursgrupp.
  - Gäller inte om typen **är** en resurs som finns under **if-villkorsresursen.**
  - Standardvärdet  är if-villkorsresursens resursgrupp.
- **ExistenceScope** (valfritt)
  - Tillåtna värden _är Subscription_ och _ResourceGroup._
  - Anger omfånget för var den relaterade resursen ska hämtas att matcha från.
  - Gäller inte om typen **är** en resurs som finns under **if-villkorsresursen.**
  - För _ResourceGroup_ skulle begränsa till **if-villkorsresursens** resursgrupp eller resursgruppen som anges i **ResourceGroupName**.
  - För _Prenumeration_ frågar hela prenumerationen efter den relaterade resursen.
  - Standardvärdet är _ResourceGroup_.
- **ExistenceCondition** (valfritt)
  - Om inget anges uppfyller alla relaterade **resurser** av typen effekten och utlöser inte granskningen.
  - Använder samma språk som principregeln för **if-villkoret,** men utvärderas mot varje relaterad resurs individuellt.
  - Om en matchande relaterad resurs utvärderas som sann uppfylls effekten och utlöser inte granskningen.
  - Kan använda [field()] för att kontrollera likvärdighet med värden i **if-villkoret.**
  - Kan till exempel användas för att verifiera att  den överordnade resursen (i if-villkoret) finns på samma resursplats som den matchande relaterade resursen.

### <a name="auditifnotexists-example"></a>AuditIfNotExists-exempel

Exempel: Utvärderar Virtual Machines för att avgöra om tillägget mot skadlig programvara finns och granskar när det saknas.

```json
{
    "if": {
        "field": "type",
        "equals": "Microsoft.Compute/virtualMachines"
    },
    "then": {
        "effect": "auditIfNotExists",
        "details": {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "existenceCondition": {
                "allOf": [{
                        "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                        "equals": "Microsoft.Azure.Security"
                    },
                    {
                        "field": "Microsoft.Compute/virtualMachines/extensions/type",
                        "equals": "IaaSAntimalware"
                    }
                ]
            }
        }
    }
}
```

## <a name="deny"></a>Neka

Neka används för att förhindra en resursbegäran som inte matchar definierade standarder via en principdefinition och som misslyckas med begäran.

### <a name="deny-evaluation"></a>Neka utvärdering

När du skapar eller uppdaterar en matchande resurs i Resource Manager läge förhindrar nekandet att begäran skickas till resursprovidern. Begäran returneras som `403 (Forbidden)` en . I portalen kan förbjuden visas som en status för distributionen som förhindrades av principtilldelningen. För ett resursproviderläge hanterar resursprovidern utvärderingen av resursen.

Under utvärderingen av befintliga resurser markeras resurser som matchar en principdefinition som nekar som icke-kompatibla.

### <a name="deny-properties"></a>Neka egenskaper

För ett Resource Manager-läge har neka-effekten inga ytterligare egenskaper för användning i **sedan-villkoret** för principdefinitionen.

För resursproviderläget `Microsoft.Kubernetes.Data` har neka-effekten följande ytterligare underegenskaper med **information**.

- **constraintTemplate** (krävs)
  - Mallen Constraint CustomResourceDefinition (CRD) som definierar nya begränsningar. Mallen definierar Rego-logiken, schemat för begränsning och de begränsningsparametrar som skickas **via** värden från Azure Policy.
- **constraint** (krävs)
  - CRD-implementeringen av begränsningsmallen. Använder parametrar som skickas via **värden** som `{{ .Values.<valuename> }}` . I exempel 2 nedan är dessa värden `{{ .Values.excludedNamespaces }}` och `{{ .Values.allowedContainerImagesRegex }}` .
- **värden** (valfritt)
  - Definierar alla parametrar och värden som ska överföras till begränsningen. Varje värde måste finnas i CRD-mallen Begränsning.

### <a name="deny-example"></a>Neka exempel

Exempel 1: Använd neka-effekten för Resource Manager lägena.

```json
"then": {
    "effect": "deny"
}
```

Exempel 2: Användning av neka-effekten för resursproviderläget `Microsoft.Kubernetes.Data` . Den ytterligare informationen i detalj **definierar** den begränsningsmall och CRD som ska användas i Kubernetes för att begränsa de tillåtna containeravbildningarna.

```json
"then": {
    "effect": "deny",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/constraint.yaml",
        "values": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]",
            "excludedNamespaces": "[parameters('excludedNamespaces')]"
        }
    }
}
```

## <a name="deployifnotexists"></a>DeployIfNotExists

Precis som AuditIfNotExists kör en DeployIfNotExists-principdefinition en malldistribution när villkoret uppfylls.

> [!NOTE]
> [Kapslade](../../../azure-resource-manager/templates/linked-templates.md#nested-template) mallar stöds med **deployIfNotExists**, men [länkade mallar](../../../azure-resource-manager/templates/linked-templates.md#linked-template) stöds inte för närvarande.

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists-utvärdering

DeployIfNotExists körs cirka 15 minuter efter att en resursprovider har hanterat en skapa eller uppdatera prenumeration eller resursbegäran och har returnerat en statuskod för lyckad körning. En malldistribution sker om det inte finns några relaterade resurser eller om resurserna som definierats av **ExistenceCondition** inte utvärderas till true. Distributionens varaktighet beror på komplexiteten för de resurser som ingår i mallen.

Under en utvärderingscykel markeras principdefinitioner med en DeployIfNotExists-effekt som matchar resurser som icke-kompatibla, men ingen åtgärd vidtas på den resursen. Befintliga icke-kompatibla resurser kan åtgärdas med en [reparationsuppgift.](../how-to/remediate-resources.md)

### <a name="deployifnotexists-properties"></a>DeployIfNotExists-egenskaper

Informationsegenskapen för effekten DeployIfNotExists har alla underegenskaper som definierar de relaterade resurserna som ska matchas och malldistributionen som ska köras. 

- **Typ** (krävs)
  - Anger vilken typ av relaterad resurs som ska matchas.
  - Börjar med att försöka hämta  en resurs under if-villkorsresursen och frågar sedan i samma resursgrupp som if-villkorsresursen. 
- **Namn** (valfritt)
  - Anger det exakta namnet på resursen som ska matchas och gör att principen hämtar en specifik resurs i stället för alla resurser av den angivna typen.
  - När villkorsvärdena **för if.field.type** och **then.details.type** matchar, blir **Name** obligatoriskt och måste vara , eller för en underordnad  `[field('name')]` `[field('fullName')]` resurs.
- **ResourceGroupName** (valfritt)
  - Gör att matchningen av den relaterade resursen kommer från en annan resursgrupp.
  - Gäller inte om typen **är** en resurs som skulle vara under if-villkorsresursen. 
  - Standardvärdet  är if-villkorsresursens resursgrupp.
  - Om en malldistribution körs distribueras den i resursgruppen för det här värdet.
- **ExistenceScope** (valfritt)
  - Tillåtna värden _är Prenumeration_ och _ResourceGroup._
  - Anger omfånget för varifrån den relaterade resursen ska hämtas.
  - Gäller inte om typen **är** en resurs som skulle vara under **if-villkorsresursen.**
  - För _ResourceGroup_ skulle begränsa till **if-villkorets** resursgrupp eller resursgruppen som anges i **ResourceGroupName**.
  - För _Prenumeration_ frågar hela prenumerationen efter den relaterade resursen.
  - Standardvärdet är _ResourceGroup_.
- **ExistenceCondition** (valfritt)
  - Om inget anges uppfyller alla relaterade **resurser** av typen effekten och utlöser inte distributionen.
  - Använder samma språk som principregeln för **if-villkoret,** men utvärderas mot varje relaterad resurs individuellt.
  - Om en matchande relaterad resurs utvärderas till sant uppfylls effekten och utlöser inte distributionen.
  - Kan använda [field()] för att kontrollera likvärdighet med värden i **if-villkoret.**
  - Kan till exempel användas för att verifiera att  den överordnade resursen (i if-villkoret) finns på samma resursplats som den matchande relaterade resursen.
- **roleDefinitionIds** (krävs)
  - Den här egenskapen måste innehålla en matris med strängar som matchar rollbaserat roll-ID för åtkomstkontroll som är tillgängligt för prenumerationen. Mer information finns i [Reparation – konfigurera principdefinition.](../how-to/remediate-resources.md#configure-policy-definition)
- **DeploymentScope** (valfritt)
  - Tillåtna värden _är Subscription_ och _ResourceGroup._
  - Anger vilken typ av distribution som ska utlösas. _Prenumeration_ anger en [distribution på prenumerationsnivå](../../../azure-resource-manager/templates/deploy-to-subscription.md), _ResourceGroup anger_ en distribution till en resursgrupp.
  - En  platsegenskap måste anges i _Distribution när_ du använder distributioner på prenumerationsnivå.
  - Standardvärdet är _ResourceGroup_.
- **Distribution** (krävs)
  - Den här egenskapen bör innehålla den fullständiga malldistributionen eftersom den skulle skickas till `Microsoft.Resources/deployments` PUT-API:et. Mer information finns i [Distributioner REST API](/rest/api/resources/deployments).

  > [!NOTE]
  > Alla funktioner i **egenskapen** Distribution utvärderas som komponenter i mallen, inte principen. Undantaget är **parameteregenskapen** som skickar värden från principen till mallen. Värdet **i** det här avsnittet under ett mallparameternamn används för att skicka det här värdet (se _fullDbName_ i exemplet DeployIfNotExists).

### <a name="deployifnotexists-example"></a>DeployIfNotExists-exempel

Exempel: Utvärderar SQL Server för att avgöra om transparentDataEncryption är aktiverat. Annars körs en distribution som ska aktiveras.

```json
"if": {
    "field": "type",
    "equals": "Microsoft.Sql/servers/databases"
},
"then": {
    "effect": "DeployIfNotExists",
    "details": {
        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
        "name": "current",
        "roleDefinitionIds": [
            "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
            "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
        ],
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "fullDbName": {
                            "type": "string"
                        }
                    },
                    "resources": [{
                        "name": "[concat(parameters('fullDbName'), '/current')]",
                        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
                        "apiVersion": "2014-04-01",
                        "properties": {
                            "status": "Enabled"
                        }
                    }]
                },
                "parameters": {
                    "fullDbName": {
                        "value": "[field('fullName')]"
                    }
                }
            }
        }
    }
}
```

## <a name="disabled"></a>Inaktiverad

Den här effekten är användbar för att testa situationer eller när principdefinitionen har parametriserat effekten. Den här flexibiliteten gör det möjligt att inaktivera en enskild tilldelning i stället för att inaktivera alla tilldelningar för principen.

Ett alternativ till den inaktiverade effekten **är enforcementMode**, som anges för principtilldelningen.
När **enforcementMode** _är Inaktiverat_ utvärderas fortfarande resurser. Loggning, till exempel aktivitetsloggar, och principeffekten inträffar inte. Mer information finns i [principtilldelning – tvingande läge.](./assignment-structure.md#enforcement-mode)

## <a name="enforceopaconstraint"></a>EnforceOPAConstraint

Den här effekten används med _principdefinitionsläget_ `Microsoft.Kubernetes.Data` . Den används för att skicka Gatekeeper v3-regler för antagningskontroll [som definierats med OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) till Open Policy [Agent](https://www.openpolicyagent.org/) (OPA) till Kubernetes-kluster i Azure.

> [!IMPORTANT]
> De begränsade principdefinitionerna för **förhandsversionen med EnforceOPAConstraint-effekten** och den relaterade **Kubernetes** _Service-kategorin är inaktuella._ Använd i stället effekterna granska _och_ _neka med_ resursproviderläget `Microsoft.Kubernetes.Data` .

### <a name="enforceopaconstraint-evaluation"></a>EnforceOPAConstraint-utvärdering

Antagningskontrollanten för Open Policy Agent utvärderar alla nya förfrågningar i klustret i realtid.
Var 15:e minut slutförs en fullständig genomsökning av klustret och resultatet rapporteras till Azure Policy.

### <a name="enforceopaconstraint-properties"></a>EnforceOPAConstraint-egenskaper

Informationsegenskapen för EnforceOPAConstraint-effekten har underegenskaperna som beskriver regeln för gatekeeper v3-antagningskontroll. 

- **constraintTemplate** (krävs)
  - Mallen Constraint CustomResourceDefinition (CRD) som definierar nya begränsningar. Mallen definierar Rego-logiken, schemat för begränsning och de begränsningsparametrar som skickas **via** värden från Azure Policy.
- **constraint** (krävs)
  - CRD-implementeringen av begränsningsmallen. Använder parametrar som skickas via **värden** som `{{ .Values.<valuename> }}` . I exemplet nedan är dessa värden `{{ .Values.cpuLimit }}` och `{{ .Values.memoryLimit }}` .
- **värden** (valfritt)
  - Definierar alla parametrar och värden som ska överföras till begränsningen. Varje värde måste finnas i CRD-mallen Begränsning.

### <a name="enforceopaconstraint-example"></a>EnforceOPAConstraint-exempel

Exempel: Gatekeeper v3-antagningskontrollregel för att ange gränser för containerns CPU- och minnesresurser i Kubernetes.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "in": [
                "Microsoft.ContainerService/managedClusters",
                "AKS Engine"
            ]
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "enforceOPAConstraint",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/constraint.yaml",
        "values": {
            "cpuLimit": "[parameters('cpuLimit')]",
            "memoryLimit": "[parameters('memoryLimit')]"
        }
    }
}
```

## <a name="enforceregopolicy"></a>EnforceRegoPolicy

Den här effekten används med _principdefinitionsläget_ `Microsoft.ContainerService.Data` . Den används för att skicka gatekeeper v2-regler för antagningskontroll som definierats med [Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) [för att öppna principagenten](https://www.openpolicyagent.org/) (OPA) [Azure Kubernetes Service](../../../aks/intro-kubernetes.md).

> [!IMPORTANT]
> De begränsade principdefinitionerna för **förhandsversionen med EnforceRegoPolicy-effekten** och den relaterade **Kubernetes** _Service-kategorin är inaktuella._ Använd i stället effekterna granska _och_ _neka med_ resursproviderläget `Microsoft.Kubernetes.Data` .

### <a name="enforceregopolicy-evaluation"></a>EnforceRegoPolicy-utvärdering

Antagningskontrollanten för Open Policy Agent utvärderar alla nya förfrågningar i klustret i realtid.
Var 15:e minut slutförs en fullständig genomsökning av klustret och resultatet rapporteras till Azure Policy.

### <a name="enforceregopolicy-properties"></a>EnforceRegoPolicy-egenskaper

Informationsegenskapen för EnforceRegoPolicy-effekten har underegenskaperna som beskriver regeln för gatekeeper v2-antagningskontroll. 

- **policyId** (krävs)
  - Ett unikt namn som skickas som en parameter till regeln för Rego-antagningskontroll.
- **princip** (krävs)
  - Anger URI för regeln för Rego-antagningskontroll.
- **policyParameters** (valfritt)
  - Definierar alla parametrar och värden som ska överföras till rego-principen.

### <a name="enforceregopolicy-example"></a>EnforceRegoPolicy-exempel

Exempel: Gatekeeper v2-antagningskontrollregel för att endast tillåta de angivna containeravbildningarna i AKS.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.ContainerService/managedClusters"
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "EnforceRegoPolicy",
    "details": {
        "policyId": "ContainerAllowedImages",
        "policy": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/KubernetesService/container-allowed-images/limited-preview/gatekeeperpolicy.rego",
        "policyParameters": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]"
        }
    }
}
```

## <a name="modify"></a>Ändra

Ändra används för att lägga till, uppdatera eller ta bort egenskaper eller taggar för en prenumeration eller resurs under skapande eller uppdatering. Ett vanligt exempel är att uppdatera taggar för resurser som costCenter. Befintliga icke-kompatibla resurser kan åtgärdas med en [reparationsuppgift.](../how-to/remediate-resources.md) En enda ändringsregel kan ha val av åtgärder.

Följande åtgärder stöds av Ändra:

- Lägga till, ersätta eller ta bort resurstaggar. För taggar ska en Ändra princip ha `mode` angetts till _Indexerad_ om inte målresursen är en resursgrupp.
- Lägg till eller ersätt värdet för hanterad identitetstyp ( `identity.type` ) för virtuella datorer och VM-skalningsuppsättningar.
- Lägg till eller ersätt värdena för vissa alias (förhandsversion).
  - Använda `Get-AzPolicyAlias | Select-Object -ExpandProperty 'Aliases' | Where-Object { $_.DefaultMetadata.Attributes -eq 'Modifiable' }`
    i Azure PowerShell **4.6.0** eller senare för att hämta en lista över alias som kan användas med Ändra.

> [!IMPORTANT]
> Om du hanterar taggar rekommenderar vi att du använder Ändra i stället för Lägg till eftersom Ändra ger ytterligare åtgärdstyper och möjlighet att åtgärda befintliga resurser. Lägg till rekommenderas dock om du inte kan skapa en hanterad identitet eller om Ändra ännu inte stöder aliaset för resursegenskapen.

### <a name="modify-evaluation"></a>Ändra utvärdering

Ändra utvärderas innan begäran bearbetas av en resursprovider när en resurs skapas eller uppdateras. Ändringsåtgärder tillämpas på begärandeinnehållet när **if-villkoret** för principregeln uppfylls. Varje ändringsåtgärd kan ange ett villkor som avgör när det tillämpas. Åtgärder med villkor som utvärderas som _false_ hoppas över.

När ett alias anges utförs följande ytterligare kontroller för att säkerställa att åtgärden Ändra inte ändrar begärandeinnehållet på ett sätt som gör att resursprovidern avvisar det:

- Egenskapen som aliaset mappar till markeras som "Ändringsbar" i begärans API-version.
- Tokentypen i åtgärden Ändra matchar den förväntade tokentypen för egenskapen i begärans API-version.

Om någon av dessa kontroller misslyckas faller principutvärderingen tillbaka till den angivna **conflictEffect**.

> [!IMPORTANT]
> Det har rekommenderats att Ändra definitioner som innehåller   alias använder effekten av granskningskonflikten för att undvika misslyckade begäranden med HJÄLP av API-versioner där den mappade egenskapen inte är "Ändringsbar". Om samma alias fungerar på olika sätt mellan API-versioner kan åtgärder för villkorsstyrd ändring användas för att fastställa vilken ändringsåtgärd som används för varje API-version.

När en principdefinition som använder effekten Ändra körs som en del av en utvärderingscykel gör den inte ändringar i resurser som redan finns. I stället markerar den alla resurser som uppfyller **if-villkoret** som icke-kompatibla.

### <a name="modify-properties"></a>Ändra egenskaper

Informationsegenskapen för effekten Ändra har alla underegenskaper som definierar de  behörigheter som krävs för reparation och de åtgärder som används för att lägga till, uppdatera eller ta bort taggvärden. 

- **roleDefinitionIds** (krävs)
  - Den här egenskapen måste innehålla en matris med strängar som matchar rollbaserad åtkomstkontrollroll-ID som kan nås av prenumerationen. Mer information finns i [Reparation – konfigurera principdefinitionen](../how-to/remediate-resources.md#configure-policy-definition).
  - Den definierade rollen måste innehålla alla åtgärder som beviljas till [deltagarrollen.](../../../role-based-access-control/built-in-roles.md#contributor)
- **conflictEffect** (valfritt)
  - Avgör vilken principdefinition som "vinner" om mer än en principdefinition ändrar samma egenskap eller när åtgärden Ändra inte fungerar på det angivna aliaset.
    - För nya eller uppdaterade resurser har _principdefinitionen med nekande_ företräde. Principdefinitioner med _granskning hoppar_ över alla **åtgärder**. Om mer än en principdefinition _har nekat_ nekas begäran som en konflikt. Om alla principdefinitioner _har granskning_ bearbetas ingen **av** åtgärderna i de motstridiga principdefinitionerna.
    - Om fler än en principdefinition har nekat _för befintliga_ resurser är kompatibilitetsstatusen _Konflikt_. Om en eller flera principdefinitioner har _nekat_ returnerar varje tilldelning kompatibilitetsstatusen _Icke-kompatibel._
  - Tillgängliga värden: _audit_, _deny_, _disabled_.
  - Standardvärdet _nekar_.
- **åtgärder** (krävs)
  - En matris med alla taggåtgärder som ska utföras på matchande resurser.
  - Egenskaper:
    - **åtgärd** (krävs)
      - Definierar vilken åtgärd som ska vidtas på en matchande resurs. Alternativen är: _addOrReplace,_ _Add_, _Remove_. _Lägg_ till fungerar ungefär som [tilläggseffekten.](#append)
    - **fält** (krävs)
      - Taggen för att lägga till, ersätta eller ta bort. Taggnamn måste följa samma namngivningskonvention för andra [fält.](./definition-structure.md#fields)
    - **värde** (valfritt)
      - Värdet som taggen ska anges till.
      - Den här egenskapen krävs om **åtgärden är** _addOrReplace eller_ Add . 
    - **villkor** (valfritt)
      - En sträng som innehåller Azure Policy ett språkuttryck [med principfunktioner](./definition-structure.md#policy-functions) som utvärderas till _sant_ eller _falskt_.
      - Stöder inte följande principfunktioner: `field()` , `resourceGroup()` , `subscription()` .

### <a name="modify-operations"></a>Ändra åtgärder

**Matrisen** för operationsegenskap gör det möjligt att ändra flera taggar på olika sätt från en enda principdefinition. Varje åtgärd består av egenskaperna **åtgärd,** **fält** **och** värde. Åtgärden avgör vad reparationsaktiviteten gör med taggarna, fältet avgör vilken tagg som ändras och värdet definierar den nya inställningen för den taggen. I exemplet nedan ändras följande tagg:

- Anger `environment` taggen till "Test", även om den redan finns med ett annat värde.
- Tar bort taggen `TempResource` .
- Anger `Dept` taggen till principparametern _DeptName_ som konfigurerats för principtilldelningen.

```json
"details": {
    ...
    "operations": [
        {
            "operation": "addOrReplace",
            "field": "tags['environment']",
            "value": "Test"
        },
        {
            "operation": "Remove",
            "field": "tags['TempResource']",
        },
        {
            "operation": "addOrReplace",
            "field": "tags['Dept']",
            "value": "[parameters('DeptName')]"
        }
    ]
}
```

Åtgärdsegenskapen har följande alternativ: 

|Åtgärd |Description |
|-|-|
|addOrReplace |Lägger till den definierade egenskapen eller taggen och värdet i resursen, även om egenskapen eller taggen redan finns med ett annat värde. |
|Lägg till |Lägger till den definierade egenskapen eller taggen och värdet i resursen. |
|Ta bort |Tar bort den definierade egenskapen eller taggen från resursen. |

### <a name="modify-examples"></a>Ändra exempel

Exempel 1: Lägg till `environment` taggen och ersätt befintliga `environment` taggar med "Test":

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "Test"
            }
        ]
    }
}
```

Exempel 2: Ta bort `env` taggen och lägg till `environment` taggen eller ersätt befintliga `environment` taggar med ett parametriserat värde:

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "conflictEffect": "deny",
        "operations": [
            {
                "operation": "Remove",
                "field": "tags['env']"
            },
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "[parameters('tagValue')]"
            }
        ]
    }
}
```

Exempel 3: Se till att ett lagringskonto inte tillåter offentlig blobåtkomst. Åtgärden Ändra tillämpas endast vid utvärdering av begäranden med EN API-version som är större än eller lika med "2019-04-01":

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/microsoft.authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab"
        ],
        "conflictEffect": "audit",
        "operations": [
            {
                "condition": "[greaterOrEquals(requestContext().apiVersion, '2019-04-01')]",
                "operation": "addOrReplace",
                "field": "Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
                "value": false
            }
        ]
    }
}
```

## <a name="layering-policy-definitions"></a>Principdefinitioner för skiktning

En resurs kan påverkas av flera tilldelningar. Dessa tilldelningar kan finnas i samma omfång eller i olika omfång. Var och en av dessa tilldelningar har troligen också en annan effekt definierad. Villkoret och effekten för varje princip utvärderas oberoende av varandra. Exempel:

- Princip 1
  - Begränsar resursplatsen till "westus"
  - Tilldelad till prenumeration A
  - Neka-effekt
- Princip 2
  - Begränsar resursplatsen till "eastus"
  - Tilldelad till resursgrupp B i prenumeration A
  - Granskningseffekt
  
Den här konfigurationen skulle resultera i följande resultat:

- Alla resurser som redan finns i resursgrupp B i "eastus" är kompatibla med princip 2 och icke-kompatibla med princip 1
- Alla resurser som redan finns i resursgrupp B som inte finns i "eastus" är icke-kompatibla med princip 2 och icke-kompatibla med princip 1 om de inte är i "westus"
- Alla nya resurser i prenumeration A som inte finns i "westus" nekas av princip 1
- Alla nya resurser i prenumeration A och resursgrupp B i "westus" skapas och är icke-kompatibla med princip 2

Om både princip 1 och princip 2 hade effekt av neka ändras situationen till:

- Alla resurser som redan finns i resursgrupp B som inte finns i "eastus" är icke-kompatibla med princip 2
- Alla resurser som redan finns i resursgrupp B som inte är i "westus" är icke-kompatibla med princip 1
- Alla nya resurser i prenumeration A som inte är i "westus" nekas av princip 1
- Alla nya resurser i resursgrupp B i prenumeration A nekas

Varje tilldelning utvärderas individuellt. Därför finns det ingen möjlighet för en resurs att gå igenom en lucka från skillnader i omfång. Nettoresultatet av principdefinitioner för skiktning anses vara kumulativt **mest restriktiva**. Om både princip 1 och 2 till exempel hade en neka-effekt skulle en resurs blockeras av de överlappande och motstridiga principdefinitionerna. Om du fortfarande behöver att resursen ska skapas i målomfånget granskar du undantagen för varje tilldelning för att verifiera att rätt principtilldelningar påverkar rätt omfång.

## <a name="next-steps"></a>Nästa steg

- Granska exempel på [Azure Policy exempel](../samples/index.md).
- Granska [Azure Policy-definitionsstrukturen](definition-structure.md).
- Förstå hur du [skapar principer programmatiskt.](../how-to/programmatically-create.md)
- Lär dig hur du [hämtar efterlevnadsdata.](../how-to/get-compliance-data.md)
- Lär dig hur [du åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska vad en hanteringsgrupp är med [Organisera dina resurser med Azure-hanteringsgrupper.](../../management-groups/overview.md)
