---
title: Förstå hur effekter fungerar
description: Azure Policy definitioner har olika effekter som avgör hur efterlevnaden hanteras och rapporteras.
ms.date: 08/17/2020
ms.topic: conceptual
ms.openlocfilehash: 0cfa8215d828de6d5426c3883ca1968e7a7cb542
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544731"
---
# <a name="understand-azure-policy-effects"></a>Förstå Azure Policys effekter

Varje principdefinition i Azure Policy har en enda effekt. Den här inställningen avgör vad som händer när princip regeln utvärderas för matchning. Effekterna fungerar annorlunda om de är för en ny resurs, en uppdaterad resurs eller en befintlig resurs.

Dessa effekter stöds för närvarande i en princip definition:

- [Append](#append) (Lägg till)
- [Audit](#audit) (Granska)
- [AuditIfNotExists](#auditifnotexists)
- [Neka](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Disabled](#disabled) (Inaktiverat)
- [Modify](#modify) (Ändra)

Följande effekter är _föråldrade_:

- [EnforceOPAConstraint](#enforceopaconstraint)
- [EnforceRegoPolicy](#enforceregopolicy)

> [!IMPORTANT]
> I stället för **EnforceOPAConstraint** -eller **EnforceRegoPolicy** -effekterna använder du _granskning_ och _neka_ med resurs leverantörs läge `Microsoft.Kubernetes.Data` . De inbyggda princip definitionerna har uppdaterats. När befintliga princip tilldelningar för dessa inbyggda princip definitioner ändras, måste parametern _Effect_ ändras till ett värde i den uppdaterade _allowedValues_ -listan.

## <a name="order-of-evaluation"></a>Utvärderings ordning

Begär Anden om att skapa eller uppdatera en resurs utvärderas av Azure Policy först. Azure Policy skapar en lista med alla tilldelningar som gäller för resursen och utvärderar sedan resursen mot varje definition. För ett [Resource Manager-läge](./definition-structure.md#resource-manager-modes)bearbetar Azure policy flera av effekterna innan du överlämnar begäran till lämplig resurs leverantör. Den här ordningen förhindrar onödig bearbetning av en resurs leverantör när en resurs inte uppfyller de design kontroller som är utformade för Azure Policy. Med ett [resurs leverantörs läge](./definition-structure.md#resource-provider-modes)hanterar resurs leverantören utvärderingen och resultatet och rapporterar tillbaka resultatet till Azure policy.

- **Inaktiverat** kontrol leras först för att avgöra om princip regeln ska utvärderas.
- **Lägg till** och **ändra** utvärderas sedan. Eftersom antingen kan ändra begäran kan en ändring som gjorts förhindra att en granskning eller nekas från att utlösas. Dessa effekter är bara tillgängliga i Resource Manager-läge.
- **Deny** utvärderas sedan. Genom att utvärdera neka före granskning, förhindras dubbel loggning av en oönskad resurs.
- **Granskningen** utvärderas sist.

När resurs leverantören returnerar en lyckad kod i ett Resource Manager-läge, utvärderas **AuditIfNotExists** och **DeployIfNotExists** för att avgöra om det krävs ytterligare loggning eller åtgärd av efterlevnad.

## <a name="append"></a>Lägg till

Lägg till används för att lägga till ytterligare fält till den begärda resursen under skapandet eller uppdateringen. Ett vanligt exempel är att ange tillåtna IP-adresser för en lagrings resurs.

> [!IMPORTANT]
> Append är avsett för användning med egenskaper som inte är taggar. När Lägg till kan lägga till taggar till en resurs under en Create-eller Update-begäran, rekommenderar vi att du använder taggen [ändra](#modify) i stället.

### <a name="append-evaluation"></a>Lägg till utvärdering

Lägg till utvärderas innan begäran bearbetas av en resurs leverantör under skapandet eller uppdateringen av en resurs. Lägg till fält till resursen **när villkors** villkoret för princip regeln är uppfyllt. Om Lägg till-resultatet skulle åsidosätta ett värde i den ursprungliga begäran med ett annat värde, fungerar det som en nekande-inverkan och avvisar begäran. Om du vill lägga till ett nytt värde i en befintlig matris använder du **\[\*\]** versionen av aliaset.

När en princip definition med hjälp av Lägg till-effekter körs som en del av en utvärderings cykel, gör den inte några ändringar i resurser som redan finns. I stället markeras alla resurser som uppfyller **IF** -villkoret som icke-kompatibel.

### <a name="append-properties"></a>Lägg till egenskaper

En Lägg till-funktion har endast en **informations** mat ris, vilket krävs. Som **information** är en matris kan den ta antingen ett enda **fält/värde-** par eller multipler. Se [definitions strukturen](definition-structure.md#fields) för listan över godkända fält.

### <a name="append-examples"></a>Lägg till exempel

Exempel 1: ett **fält/värde** -par med ett icke- **\[\*\]** [alias](definition-structure.md#aliases) med ett mat ris **värde** som anger IP-regler för ett lagrings konto. När ett icke- **\[\*\]** alias är en matris, lägger effekterna till **värdet** som hela matrisen. Om matrisen redan finns inträffar en Deny-händelse från konflikten.

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

Exempel 2: ett **fält/värde** -par med ett **\[\*\]** [alias](definition-structure.md#aliases) med ett mat ris **värde** som anger IP-regler för ett lagrings konto. Genom att använda **\[\*\]** aliaset lägger du till **värdet** i en befintlig matris som kan användas. Om matrisen ännu inte finns skapas den.

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

Granskning används för att skapa en varnings händelse i aktivitets loggen när en icke-kompatibel resurs utvärderas, men begäran stoppas inte.

### <a name="audit-evaluation"></a>Gransknings utvärdering

Audit är den senaste effekterna som kontrol leras av Azure Policy när en resurs skapas eller uppdateras. För ett Resource Manager-läge skickar Azure Policy resursen till resurs leverantören. Granskningen fungerar likadant för en resurs förfrågan och en utvärderings cykel. Azure Policy lägger till en `Microsoft.Authorization/policies/audit/action` åtgärd i aktivitets loggen och markerar resursen som icke-kompatibel.

### <a name="audit-properties"></a>Gransknings egenskaper

För ett Resource Manager-läge har gransknings effekterna inga ytterligare egenskaper att använda i **then** -villkoret för princip definitionen.

För ett resurs leverantörs läge av `Microsoft.Kubernetes.Data` , har gransknings effekterna följande ytterligare egenskaper för **information**.

- **constraintTemplate** (krävs)
  - Begränsnings mal len CustomResourceDefinition (CRD) som definierar nya begränsningar. Mallen definierar Rego Logic, begränsnings schema och de villkors parametrar som skickas via **värden** från Azure policy.
- **begränsning** (obligatoriskt)
  - CRD-implementeringen av begränsnings mal len. Använder parametrar som skickas via **värden** som `{{ .Values.<valuename> }}` . I exempel 2 nedan är dessa värden `{{ .Values.excludedNamespaces }}` och `{{ .Values.allowedContainerImagesRegex }}` .
- **värden** (valfritt)
  - Definierar alla parametrar och värden som ska skickas till begränsningen. Varje värde måste finnas i CRD för begränsnings mal len.

### <a name="audit-example"></a>Gransknings exempel

Exempel 1: använda gransknings effekterna för Resource Manager-lägen.

```json
"then": {
    "effect": "audit"
}
```

Exempel 2: använda gransknings effekterna för ett resurs leverantörs läge i `Microsoft.Kubernetes.Data` . Mer information i **Detaljer** definierar den constraint-mall och CRD som ska användas i Kubernetes för att begränsa de tillåtna behållar avbildningarna.

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

AuditIfNotExists möjliggör granskning av resurser som är _relaterade_ till den resurs som matchar **IF** -villkoret, men som inte har de egenskaper som anges i **informationen** om **then** -villkoret.

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists-utvärdering

AuditIfNotExists körs efter att en resurs leverantör har hanterat en begäran om att skapa eller uppdatera resurs och returnerat en status kod som lyckats. Granskningen sker om det inte finns några relaterade resurser eller om resurserna som definieras av **ExistenceCondition** inte utvärderas till true. Azure Policy lägger till en `Microsoft.Authorization/policies/audit/action` åtgärd i aktivitets loggen på samma sätt som gransknings resultatet. När den utlöses är den resurs som uppfyllde **IF** -villkoret den resurs som har marker ATS som icke-kompatibel.

### <a name="auditifnotexists-properties"></a>Egenskaper för AuditIfNotExists

Egenskapen **information** för AuditIfNotExists-effekterna har alla under egenskaper som definierar de relaterade resurserna som ska matchas.

- **Typ** (krävs)
  - Anger vilken typ av relaterad resurs som ska matchas.
  - Om **information. Type** är en resurs typ under **IF** -villkor-resursen, frågar principen efter resurser av den här **typen** inom omfånget för den utvärderade resursen. I annat fall är princip frågorna inom samma resurs grupp som den utvärderade resursen.
- **Namn** (valfritt)
  - Anger det exakta namnet på resursen som ska matchas och gör att principen hämtar en specifik resurs i stället för alla resurser av den angivna typen.
  - När villkors värden för **IF. Field. Type** och **then. details. Type** match, blir **namnet** _obligatoriskt_ och måste vara `[field('name')]` . En [gransknings](#audit) funktion bör dock beaktas i stället.
- **ResourceGroupName** (valfritt)
  - Tillåter matchning av den relaterade resursen att komma från en annan resurs grupp.
  - Gäller inte om **typen** är en resurs som skulle ligga under villkors resursen **IF** .
  - Standard är resurs gruppen **om** villkors resursen.
- **ExistenceScope** (valfritt)
  - Tillåtna värden är _prenumerations_ -och _ResourceGroup_.
  - Anger omfånget för var den relaterade resursen ska hämtas för matchning från.
  - Gäller inte om **typen** är en resurs som skulle ligga under villkors resursen **IF** .
  - För _ResourceGroup_begränsas till resurs gruppen **om** villkors resursen eller resurs gruppen som anges i **ResourceGroupName**.
  - För _prenumerationen_frågar hela prenumerationen för den relaterade resursen.
  - Standardvärdet är _ResourceGroup_.
- **ExistenceCondition** (valfritt)
  - Om inget annat anges uppfyller en relaterad resurs av **typen** effekterna och utlöser inte granskningen.
  - Använder samma språk som princip regeln för **IF** -villkoret, men utvärderas mot varje relaterad resurs individuellt.
  - Om en matchande relaterad resurs utvärderas som sant är resultatet uppfyllt och utlöser inte granskningen.
  - Kan använda [Field ()] för att kontrol lera likvärdighet med värden i **IF** -villkoret.
  - Kan till exempel användas för att kontrol lera att den överordnade resursen (i **IF** -villkoret) finns på samma resurs plats som den matchande relaterade resursen.

### <a name="auditifnotexists-example"></a>AuditIfNotExists-exempel

Exempel: utvärderar Virtual Machines för att avgöra om tillägget för program mot skadlig kod finns, granskas när de saknas.

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

Neka används för att förhindra en resurs förfrågan som inte matchar definierade standarder via en princip definition och som Miss lyckas med begäran.

### <a name="deny-evaluation"></a>Neka utvärdering

När du skapar eller uppdaterar en matchad resurs i ett Resource Manager-läge förhindrar neka-begäran innan den skickas till resurs leverantören. Begäran returneras som en `403 (Forbidden)` . I portalen kan den förbjudna visas som status för den distribution som förhindrades av princip tilldelningen. För ett resurs leverantörs läge hanterar resurs leverantören utvärderingen av resursen.

Vid utvärdering av befintliga resurser markeras resurser som matchar en definition för neka-principer som icke-kompatibla.

### <a name="deny-properties"></a>Egenskaper för neka

För ett Resource Manager-läge har neka-effekterna inga ytterligare egenskaper för användning i **then** -villkoret för princip definitionen.

För ett resurs leverantörs läge av `Microsoft.Kubernetes.Data` , har neka-påverkan följande ytterligare egenskaper för **information**.

- **constraintTemplate** (krävs)
  - Begränsnings mal len CustomResourceDefinition (CRD) som definierar nya begränsningar. Mallen definierar Rego Logic, begränsnings schema och de villkors parametrar som skickas via **värden** från Azure policy.
- **begränsning** (obligatoriskt)
  - CRD-implementeringen av begränsnings mal len. Använder parametrar som skickas via **värden** som `{{ .Values.<valuename> }}` . I exempel 2 nedan är dessa värden `{{ .Values.excludedNamespaces }}` och `{{ .Values.allowedContainerImagesRegex }}` .
- **värden** (valfritt)
  - Definierar alla parametrar och värden som ska skickas till begränsningen. Varje värde måste finnas i CRD för begränsnings mal len.

### <a name="deny-example"></a>Neka exempel

Exempel 1: använda neka-påverkan för Resource Manager-lägen.

```json
"then": {
    "effect": "deny"
}
```

Exempel 2: Använd neka-funktionen för ett resurs leverantörs läge i `Microsoft.Kubernetes.Data` . Mer information i **Detaljer** definierar den constraint-mall och CRD som ska användas i Kubernetes för att begränsa de tillåtna behållar avbildningarna.

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

På samma sätt som med AuditIfNotExists kör en DeployIfNotExists princip definition en mall distribution när villkoret är uppfyllt.

> [!NOTE]
> [Kapslade mallar](../../../azure-resource-manager/templates/linked-templates.md#nested-template) stöds med **deployIfNotExists**, men [länkade mallar](../../../azure-resource-manager/templates/linked-templates.md#linked-template) stöds inte för närvarande.

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists-utvärdering

DeployIfNotExists körs ungefär 15 minuter efter att en resurs leverantör har hanterat en Create-eller Update-resurs-begäran och returnerat en status kod som lyckats. En mall distribution sker om det inte finns några relaterade resurser eller om resurserna som definieras av **ExistenceCondition** inte utvärderas till true.
Distributionens varaktighet beror på hur komplexa resurser som ingår i mallen.

Under en utvärderings cykel markeras princip definitioner med en DeployIfNotExists-påverkan som matchar resurserna som icke-kompatibel, men ingen åtgärd utförs på den resursen. Befintliga icke-kompatibla resurser kan åtgärdas med en [reparations uppgift](../how-to/remediate-resources.md).

### <a name="deployifnotexists-properties"></a>Egenskaper för DeployIfNotExists

Egenskapen **information** för DeployIfNotExists-effekterna har alla under egenskaper som definierar de relaterade resurserna som ska matchas och mallen som ska köras.

- **Typ** (krävs)
  - Anger vilken typ av relaterad resurs som ska matchas.
  - Startar genom att försöka hämta en resurs under villkors resursen **IF** , och sedan frågar i samma resurs grupp som villkors resursen **om** .
- **Namn** (valfritt)
  - Anger det exakta namnet på resursen som ska matchas och gör att principen hämtar en specifik resurs i stället för alla resurser av den angivna typen.
  - När villkors värden för **IF. Field. Type** och **then. details. Type** match, blir **namnet** _obligatoriskt_ och måste vara `[field('name')]` .
- **ResourceGroupName** (valfritt)
  - Tillåter matchning av den relaterade resursen att komma från en annan resurs grupp.
  - Gäller inte om **typen** är en resurs som skulle ligga under villkors resursen **IF** .
  - Standard är resurs gruppen **om** villkors resursen.
  - Om en mall distribution körs, distribueras den i resurs gruppen för det här värdet.
- **ExistenceScope** (valfritt)
  - Tillåtna värden är _prenumerations_ -och _ResourceGroup_.
  - Anger omfånget för var den relaterade resursen ska hämtas för matchning från.
  - Gäller inte om **typen** är en resurs som skulle ligga under villkors resursen **IF** .
  - För _ResourceGroup_begränsas till resurs gruppen **om** villkors resursen eller resurs gruppen som anges i **ResourceGroupName**.
  - För _prenumerationen_frågar hela prenumerationen för den relaterade resursen.
  - Standardvärdet är _ResourceGroup_.
- **ExistenceCondition** (valfritt)
  - Om inget annat anges uppfyller en relaterad resurs av **typen** effekterna och utlöser inte distributionen.
  - Använder samma språk som princip regeln för **IF** -villkoret, men utvärderas mot varje relaterad resurs individuellt.
  - Om en matchande relaterad resurs utvärderas som sant är resultatet uppfyllt och utlöser inte distributionen.
  - Kan använda [Field ()] för att kontrol lera likvärdighet med värden i **IF** -villkoret.
  - Kan till exempel användas för att kontrol lera att den överordnade resursen (i **IF** -villkoret) finns på samma resurs plats som den matchande relaterade resursen.
- **roleDefinitionIds** (krävs)
  - Den här egenskapen måste innehålla en matris med strängar som matchar rollbaserad åtkomst kontroll roll-ID som är tillgängligt för prenumerationen. Mer information finns i [reparation-Konfigurera princip definition](../how-to/remediate-resources.md#configure-policy-definition).
- **DeploymentScope** (valfritt)
  - Tillåtna värden är _prenumerations_ -och _ResourceGroup_.
  - Anger vilken typ av distribution som ska utlösas. _Prenumerationen_ anger en [distribution på prenumerations nivå](../../../azure-resource-manager/templates/deploy-to-subscription.md), _ResourceGroup_ anger en distribution till en resurs grupp.
  - En _plats_ egenskap måste anges i _distributionen_ när du använder distributioner på prenumerations nivå.
  - Standardvärdet är _ResourceGroup_.
- **Distribution** (krävs)
  - Den här egenskapen ska innehålla den fullständiga mal Lav distributionen som den skulle skickas till `Microsoft.Resources/deployments` API: et för placering. Mer information finns i [distributioner REST API](/rest/api/resources/deployments).

  > [!NOTE]
  > Alla funktioner i **distributions** egenskapen utvärderas som komponenter i mallen, inte principen. Undantaget är **parameter** egenskapen som skickar värden från principen till mallen. **Värdet** i det här avsnittet under ett Mallnamn används för att utföra det här värdet genom att skicka (se _fullDbName_ i DeployIfNotExists-exemplet).

### <a name="deployifnotexists-example"></a>DeployIfNotExists-exempel

Exempel: utvärderar SQL Server databaser för att avgöra om transparentDataEncryption har Aktiver ATS. Annars körs en distribution som ska aktive ras.

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

Den här inställningen är användbar för att testa situationer eller när princip definitionen har parameterstyrda påverkan. Den här flexibiliteten gör det möjligt att inaktivera en enskild tilldelning i stället för att inaktivera alla tilldelningar för principen.

Ett alternativ till den inaktiverade inställningen är **enforcementMode**, som anges för princip tilldelningen.
När **enforcementMode** är _inaktive rad_utvärderas resurserna fortfarande. Loggning, till exempel aktivitets loggar och princip påverkan inträffar inte. Mer information finns i [princip tilldelning – tvingande läge](./assignment-structure.md#enforcement-mode).

## <a name="enforceopaconstraint"></a>EnforceOPAConstraint

Den här inställningen används med ett princip definitions _läge_ för `Microsoft.Kubernetes.Data` . Den används för att skicka Gatekeeper v3-regler för åtkomst kontroll som definierats med [OPA constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) till att [Öppna princip agent](https://www.openpolicyagent.org/) (OPA) till Kubernetes-kluster i Azure.

> [!NOTE]
> [Azure policy för Kubernetes](./policy-for-kubernetes.md) finns i för hands version och stöder bara Linux-nodkonfigurationer och inbyggda princip definitioner. Inbyggda princip definitioner finns i kategorin **Kubernetes** . De begränsade för hands versions princip definitionerna med **EnforceOPAConstraint** -effekter och den relaterade **Kubernetes-tjänst** kategorin är _inaktuella_. Använd i stället effekterna _granskning_ och _neka_ med resurs leverantörs läge `Microsoft.Kubernetes.Data` .

### <a name="enforceopaconstraint-evaluation"></a>EnforceOPAConstraint-utvärdering

Den öppna princip agentens åtkomst kontroll utvärderar alla nya begär anden i klustret i real tid.
Var 15: e minut slutförs en fullständig genomsökning av klustret och resultaten rapporteras till Azure Policy.

### <a name="enforceopaconstraint-properties"></a>Egenskaper för EnforceOPAConstraint

Egenskapen **information** för EnforceOPAConstraint-effekter har de subegenskaper som beskriver Gatekeeper-åtkomstkontroll för åtkomst kontroll.

- **constraintTemplate** (krävs)
  - Begränsnings mal len CustomResourceDefinition (CRD) som definierar nya begränsningar. Mallen definierar Rego Logic, begränsnings schema och de villkors parametrar som skickas via **värden** från Azure policy.
- **begränsning** (obligatoriskt)
  - CRD-implementeringen av begränsnings mal len. Använder parametrar som skickas via **värden** som `{{ .Values.<valuename> }}` . I exemplet nedan är dessa värden `{{ .Values.cpuLimit }}` och `{{ .Values.memoryLimit }}` .
- **värden** (valfritt)
  - Definierar alla parametrar och värden som ska skickas till begränsningen. Varje värde måste finnas i CRD för begränsnings mal len.

### <a name="enforceopaconstraint-example"></a>EnforceOPAConstraint-exempel

Exempel: Gatekeeper v3-åtkomstkontroll för att ange behållarens processor gränser och minnes resurs gränser i Kubernetes.

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

Den här inställningen används med ett princip definitions _läge_ för `Microsoft.ContainerService.Data` . Den används för att skicka Gatekeeper v2-regler för åtkomst kontroll som definierats med [Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) för att [Öppna princip agent](https://www.openpolicyagent.org/) (OPA) på [Azure Kubernetes-tjänsten](../../../aks/intro-kubernetes.md).

> [!NOTE]
> [Azure policy för Kubernetes](./policy-for-kubernetes.md) finns i för hands version och stöder bara Linux-nodkonfigurationer och inbyggda princip definitioner. Inbyggda princip definitioner finns i kategorin **Kubernetes** . De begränsade för hands versions princip definitionerna med **EnforceRegoPolicy** -effekter och den relaterade **Kubernetes-tjänst** kategorin är _inaktuella_. Använd i stället effekterna _granskning_ och _neka_ med resurs leverantörs läge `Microsoft.Kubernetes.Data` .

### <a name="enforceregopolicy-evaluation"></a>EnforceRegoPolicy-utvärdering

Den öppna princip agentens åtkomst kontroll utvärderar alla nya begär anden i klustret i real tid.
Var 15: e minut slutförs en fullständig genomsökning av klustret och resultaten rapporteras till Azure Policy.

### <a name="enforceregopolicy-properties"></a>Egenskaper för EnforceRegoPolicy

Egenskapen **information** för EnforceRegoPolicy-funktionen har de subegenskaper som beskriver åtkomst kontroll regeln för gatekeeper v2.

- **policyId** (krävs)
  - Ett unikt namn som skickas som en parameter till Rego-åtkomstkontroll.
- **princip** (krävs)
  - Anger URI för Rego-åtkomstkontroll.
- **policyParameters** (valfritt)
  - Definierar alla parametrar och värden som ska skickas till Rego-principen.

### <a name="enforceregopolicy-example"></a>EnforceRegoPolicy-exempel

Exempel: Gatekeeper v2-åtkomstkontroll för att endast tillåta de angivna behållar avbildningarna i AKS.

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

Ändra används för att lägga till, uppdatera eller ta bort taggar på en resurs under skapandet eller uppdateringen. Ett vanligt exempel är att uppdatera taggar på resurser som costCenter. En ändra princip ska alltid ha `mode` angetts till _indexerad_ om inte mål resursen är en resurs grupp. Befintliga icke-kompatibla resurser kan åtgärdas med en [reparations uppgift](../how-to/remediate-resources.md). En enda ändra-regel kan ha valfritt antal åtgärder.

> [!IMPORTANT]
> Ändra är för närvarande endast för användning med-taggar. Om du hanterar Taggar rekommenderar vi att du använder ändra i stället för Lägg till som ändra och ger ytterligare åtgärds typer och möjlighet att åtgärda befintliga resurser. Tillägg rekommenderas dock om du inte kan skapa en hanterad identitet.

### <a name="modify-evaluation"></a>Ändra utvärdering

Ändra utvärderar innan begäran bearbetas av en resurs leverantör under skapandet eller uppdateringen av en resurs. Ändra taggar för tillägg eller uppdateringar på en resurs när **villkors** villkoret för princip regeln är uppfyllt.

När en princip definition med hjälp av ändra-effekter körs som en del av en utvärderings cykel, gör den inte några ändringar i resurser som redan finns. I stället markeras alla resurser som uppfyller **IF** -villkoret som icke-kompatibel.

### <a name="modify-properties"></a>Ändra egenskaper

Egenskapen **information** för funktionen ändra har alla under egenskaper som definierar de behörigheter som krävs för reparation och de **åtgärder** som används för att lägga till, uppdatera eller ta bort taggattribut.

- **roleDefinitionIds** (krävs)
  - Den här egenskapen måste innehålla en matris med strängar som matchar rollbaserad åtkomst kontroll roll-ID som är tillgängligt för prenumerationen. Mer information finns i [reparation-Konfigurera princip definition](../how-to/remediate-resources.md#configure-policy-definition).
  - Den roll som definieras måste innehålla alla åtgärder som beviljas rollen [deltagare](../../../role-based-access-control/built-in-roles.md#contributor) .
- **conflictEffect** (valfritt)
  - Fastställer vilken princip definition "WINS" i händelse av att mer än en princip definition ändrar samma egenskap.
    - För nya eller uppdaterade resurser prioriteras princip definitionen med _neka_ . Princip definitioner med _granskning_ hoppa över alla **åtgärder**. Om mer än en princip definition har _neka_nekas begäran som en konflikt. Om alla princip definitioner har _granskning_bearbetas ingen av **åtgärderna** i de motstridiga princip definitionerna.
    - För befintliga resurser, om mer än en princip definition har _neka_, är kompatibilitetsstatus en _konflikt_. Om en eller färre princip definitioner har _neka_, returnerar varje tilldelning en kompatibilitetsstatus som _inte är kompatibel_.
  - Tillgängliga värden: _audit_, _Deny_, _Disabled_.
  - Standardvärdet är _Deny_.
- **åtgärder** (krävs)
  - En matris med alla märknings åtgärder som ska utföras för matchande resurser.
  - Egenskaper:
    - **åtgärd** (krävs)
      - Definierar vilken åtgärd som ska vidtas för en matchande resurs. Alternativen är: _addOrReplace_, _Add_, _Remove_. _Lägg till_ fungerar ungefär som [i Lägg till-resultatet.](#append)
    - **fält** (obligatoriskt)
      - Taggen för att lägga till, ersätta eller ta bort. Taggnamn måste följa samma namngivnings konvention för andra [fält](./definition-structure.md#fields).
    - **värde** (valfritt)
      - Värdet som taggen ska ställas in på.
      - Den här egenskapen krävs om **åtgärden** är _addOrReplace_ eller _Add_.

### <a name="modify-operations"></a>Ändra åtgärder

Med egenskapen för **drifts** egenskaper kan du ändra flera taggar på olika sätt från en enda princip definition. Varje åtgärd består av egenskaperna **åtgärd**, **fält**och **värde** . Åtgärden avgör vad reparations uppgiften gör till taggarna, fältet avgör vilken tagg som ändras och värdet definierar den nya inställningen för taggen. Exemplet nedan gör följande tagg ändringar:

- Ställer in `environment` taggen på "test", även om den redan finns med ett annat värde.
- Tar bort taggen `TempResource` .
- Ställer in `Dept` taggen till den princip parameter _DeptName_ som kon figurer ATS för princip tilldelningen.

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

Egenskapen **operation** har följande alternativ:

|Åtgärd |Description |
|-|-|
|addOrReplace |Lägger till den definierade taggen och värdet i resursen, även om taggen redan finns med ett annat värde. |
|Lägg till |Lägger till den definierade taggen och värdet i resursen. |
|Ta bort |Tar bort den definierade taggen från resursen. |

### <a name="modify-examples"></a>Ändra exempel

Exempel 1: Lägg till `environment` taggen och ersätt befintliga `environment` taggar med "test":

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

Exempel 2: ta bort `env` taggen och Lägg till `environment` taggen eller ersätt befintliga `environment` taggar med ett parameter värde:

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

## <a name="layering-policy-definitions"></a>Skikt princip definitioner

En resurs kan påverkas av flera tilldelningar. Tilldelningarna kan finnas i samma omfång eller i olika omfång. Vart och ett av dessa tilldelningar är också troligt att en annan inverkan har definierats. Villkoret och påverkan för varje princip utvärderas oberoende av varandra. Exempel:

- Princip 1
  - Begränsar resursens plats till "väst"
  - Tilldelad till prenumeration A
  - Neka inverkan
- Princip 2
  - Begränsar resursens plats till "öster"
  - Tilldelad till resurs grupp B i prenumeration A
  - Gransknings funktion
  
Den här installationen skulle resultera i följande resultat:

- Alla resurser som redan finns i resurs grupp B i "öst" är kompatibla med princip 2 och icke-kompatibel till princip 1
- Alla resurser som redan finns i resurs grupp B som inte är östasiatiska är inte kompatibla med princip 2 och icke-kompatibla till princip 1 om inte i "väst"
- Alla nya resurser i prenumerationen som inte finns i "väst" nekas av princip 1
- Alla nya resurser i prenumeration A och resurs grupp B i "väst" skapas och är inkompatibla för princip 2

Om både princip 1 och princip 2 hade påverkan från neka ändras situationen till:

- Alla resurser som redan finns i resurs gruppen B som inte är östasiatiska är inte kompatibla med princip 2
- En resurs som redan finns i resurs grupp B som inte är "väst" är inte kompatibel med princip 1
- Alla nya resurser i prenumerationen som inte finns i "väst" nekas av princip 1
- Alla nya resurser i resurs grupp B för prenumeration A nekas

Varje tilldelning utvärderas individuellt. Det finns därför ingen möjlighet för en resurs att gå igenom ett mellanrum från olikheter i omfattningen. Netto resultatet av definitioner av skikt principer anses vara **ackumulerat mest restriktivt**. Exempel: om både princip 1 och 2 hade en neka-påverkan blockeras en resurs av de överlappande och motstridiga princip definitionerna. Om du fortfarande behöver resursen som ska skapas i mål omfånget, granskar du undantagen för varje tilldelning för att verifiera att rätt princip tilldelningar påverkar rätt omfång.

## <a name="next-steps"></a>Nästa steg

- Granska exempel i [Azure policy exempel](../samples/index.md).
- Granska [Azure Policy-definitionsstrukturen](definition-structure.md).
- Lär dig att [program mässigt skapa principer](../how-to/programmatically-create.md).
- Lär dig hur du [hämtar efterlevnadsprinciper](../how-to/get-compliance-data.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska en hanterings grupp med [organisera dina resurser med Azures hanterings grupper](../../management-groups/overview.md).
