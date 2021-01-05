---
title: Information om princip definitions strukturen
description: Beskriver hur princip definitioner används för att upprätta konventioner för Azure-resurser i din organisation.
ms.date: 10/22/2020
ms.topic: conceptual
ms.openlocfilehash: 52adaf9522e4690c4c44a72ed47592f5b1d6471e
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97883256"
---
# <a name="azure-policy-definition-structure"></a>Azure Policy-definitionsstruktur

Azure Policy skapar konventioner för resurser. Princip definitioner [beskriver kraven på resursanvändningen och vilken](#conditions) påverkan som ska vidtas om ett villkor är uppfyllt. Ett villkor jämför ett resurs egenskaps [fält](#fields) eller ett [värde](#value) till ett värde som krävs. Resurs egenskaps fält öppnas med hjälp av [alias](#aliases). När ett resurs egenskaps fält är en matris kan ett särskilt [mat ris Ali Aset](#understanding-the--alias) användas för att välja värden från alla mat ris medlemmar och tillämpa ett villkor för var och en.
Läs mer om [villkor](#conditions).

Genom att definiera konventioner kan du kontrol lera kostnaderna och enklare hantera dina resurser. Du kan till exempel ange att endast vissa typer av virtuella datorer ska tillåtas. Du kan också kräva att resurserna har en viss tagg. Princip tilldelningar ärvs av underordnade resurser. Om en princip tilldelning tillämpas på en resurs grupp, gäller den för alla resurser i resurs gruppen.

_PolicyRule_ -schemat för princip definition finns här:[https://schema.management.azure.com/schemas/2019-09-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-09-01/policyDefinition.json)

Du använder JSON för att skapa en princip definition. Princip definitionen innehåller element för:

- visningsnamn
- description
- mode
- metadata
- parametrar
- princip regel
  - logisk utvärdering
  - effekt

Följande JSON visar till exempel en princip som begränsar var resurserna distribueras:

```json
{
    "properties": {
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "mode": "Indexed",
        "metadata": {
            "version": "1.0.0",
            "category": "Locations"
        },
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                },
                "defaultValue": [ "westus2" ]
            }
        },
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

Azure Policy inbyggda program och mönster finns på [Azure policy exempel](../samples/index.md).

## <a name="display-name-and-description"></a>Visnings namn och beskrivning

Du kan använda **DisplayName** och **Description** för att identifiera princip definitionen och tillhandahålla kontext när den används. **DisplayName** får innehålla högst _128_ tecken och **beskrivningen** får bestå av högst _512_ tecken.

> [!NOTE]
> Under skapandet eller uppdateringen av en princip definition definieras **ID**, **typ** och **namn** av egenskaper som är externa för JSON och är inte nödvändiga i JSON-filen. Hämtning av princip definitionen via SDK returnerar egenskaperna **ID**, **typ** och **namn** som en del av JSON, men var och en är skrivskyddad information som är relaterad till princip definitionen.

## <a name="type"></a>Typ

Det går inte att ange **typ** egenskapen, det finns tre värden som returneras av SDK och visas i portalen:

- `Builtin`: Dessa princip definitioner tillhandahålls och underhålls av Microsoft.
- `Custom`: Alla princip definitioner som skapats av kunder har det här värdet.
- `Static`: Anger en [regel](./regulatory-compliance.md) definition för efterlevnad med Microsoft- **ägarskap**. Kompabilitets resultatet för dessa princip definitioner är resultatet av granskningar från tredje part i Microsoft-infrastrukturen. I Azure Portal visas det här värdet ibland som **Microsoft Managed**. Mer information finns i [delat ansvar i molnet](../../../security/fundamentals/shared-responsibility.md).

## <a name="mode"></a>Läge

**Läge** konfigureras beroende på om principen är riktad mot en Azure Resource Manager-egenskap eller en resurs leverantörs egenskap.

### <a name="resource-manager-modes"></a>Lägen i Resource Manager

**Läget** avgör vilka resurs typer som utvärderas för en princip definition. De lägen som stöds är:

- `all`: utvärdera resurs grupper, prenumerationer och alla resurs typer
- `indexed`: utvärdera endast resurs typer som stöder taggar och platser

Resurs stöder till exempel `Microsoft.Network/routeTables` taggar och plats och utvärderas i båda lägena. Men `Microsoft.Network/routeTables/routes` det går inte att tagga resursen och den utvärderas inte i `Indexed` läget.

Vi rekommenderar att du ställer in **läget** till `all` i de flesta fall. Alla princip definitioner som skapats via portalen använder `all` läget. Om du använder PowerShell eller Azure CLI kan du ange **läges** parametern manuellt. Om princip definitionen inte innehåller ett **läges** värde används standardvärdet `all` i Azure PowerShell och `null` i Azure CLI. Ett `null` läge är detsamma som att använda `indexed` för att ge stöd för bakåtkompatibilitet.

`indexed` ska användas när du skapar principer som tvingar etiketter eller platser. Även om det inte krävs, förhindrar det att resurser som inte stöder taggar och platser visas som icke-kompatibla i resultatet av efterlevnaden. Undantaget är **resurs grupper** och **prenumerationer**. Princip definitioner som tvingar plats eller taggar i en resurs grupp eller prenumeration ska ange **läge** till `all` och särskilt vara mål för- `Microsoft.Resources/subscriptions/resourceGroups` eller- `Microsoft.Resources/subscriptions` typen. Ett exempel finns i [Pattern: Tags-Sample #1](../samples/pattern-tags.md). En lista över resurser som stöder taggar finns i [tagga stöd för Azure-resurser](../../../azure-resource-manager/management/tag-support.md).

### <a name="resource-provider-modes"></a>Resurs leverantörs lägen

Följande resurs leverantörs läge stöds fullt ut:

- `Microsoft.Kubernetes.Data` för hantering av Kubernetes-kluster på eller av Azure. Definitioner som använder detta resurs leverantörs läge använder effekter _granskning_, _neka_ och _inaktive rad_. Användning av [EnforceOPAConstraint](./effects.md#enforceopaconstraint) -effekter är _föråldrad_.

Följande resurs leverantörs lägen stöds för närvarande som en för **hands version**:

- `Microsoft.ContainerService.Data` för hantering av regler för regler för åtkomst kontroll i [Azure Kubernetes-tjänsten](../../../aks/intro-kubernetes.md). Definitioner som använder detta resurs leverantörs läge **måste** använda [EnforceRegoPolicy](./effects.md#enforceregopolicy) -effekter. Det här läget är _föråldrat_.
- `Microsoft.KeyVault.Data` för hantering av valv och certifikat i [Azure Key Vault](../../../key-vault/general/overview.md). Mer information om dessa princip definitioner finns i [integrera Azure Key Vault med Azure policy](../../../key-vault/general/azure-policy.md).

> [!NOTE]
> Resurs leverantörs lägen stöder bara inbyggda princip definitioner och stöder inte [undantag](./exemption-structure.md).

## <a name="metadata"></a>Metadata

Den valfria `metadata` egenskapen innehåller information om princip definitionen. Kunder kan definiera egenskaper och värden som är användbara för deras organisation i `metadata` . Det finns dock några _gemensamma_ egenskaper som används av Azure policy och i inbyggda moduler.

### <a name="common-metadata-properties"></a>Gemensamma egenskaper för metadata

- `version` (sträng): spårar information om versionen av innehållet i en princip definition.
- `category` (sträng): anger under vilken kategori i Azure Portal princip definitionen visas.
- `preview` (boolesk): true eller false flagga för om princip definitionen är för _hands version_.
- `deprecated` (boolesk): true eller false flagga för om princip definitionen har marker ATS som _föråldrad_.

> [!NOTE]
> Azure Policy tjänsten använder `version` , `preview` , och `deprecated` Egenskaper för att förmedla ändrings nivån till en inbyggd princip definition eller initiativ och tillstånd. Formatet `version` är: `{Major}.{Minor}.{Patch}` . Vissa tillstånd, till exempel _föråldrad_ eller för _hands version_, läggs till i `version` egenskapen eller i en annan egenskap som **boolesk**. Mer information om hur Azure Policy inbyggda versioner finns i [inbyggd versions hantering](https://github.com/Azure/azure-policy/blob/master/built-in-policies/README.md).

## <a name="parameters"></a>Parametrar

Med parametrar kan du förenkla princip hanteringen genom att minska antalet princip definitioner. Tänk på parametrar som fälten i ett formulär – `name` ,, `address` `city` , `state` . Dessa parametrar är alltid desamma, men deras värden ändras baserat på de enskilda som fyller i formuläret.
Parametrar fungerar på samma sätt när du skapar principer. Genom att inkludera parametrar i en princip definition kan du återanvända principen för olika scenarier genom att använda olika värden.

> [!NOTE]
> Parametrar kan läggas till i en befintlig och tilldelad definition. Den nya parametern måste innehålla egenskapen **DefaultValue** . Detta förhindrar att befintliga tilldelningar av principen eller initiativet från indirekt görs ogiltiga.

### <a name="parameter-properties"></a>Parameter egenskaper

En parameter har följande egenskaper som används i princip definitionen:

- `name`: Namnet på din parameter. Används av `parameters` distributions funktionen i princip regeln. Mer information finns i [använda ett parameter värde](#using-a-parameter-value).
- `type`: Anger om parametern är en **sträng**, en **matris**, ett **objekt**, ett **booleskt värde** **, ett** **flyttal** eller en **datetime**.
- `metadata`: Definierar under egenskaper som främst används av Azure Portal för att Visa användarvänlig information:
  - `description`: En förklaring av vad parametern används för. Kan användas för att ge exempel på acceptabla värden.
  - `displayName`: Det egna namnet visas i portalen för parametern.
  - `strongType`: (Valfritt) används för att tilldela princip definitionen via portalen. Innehåller en Sammanhangs medveten lista. Mer information finns i [strongType](#strongtype).
  - `assignPermissions`: (Valfritt) Ange som _Sant_ för att Azure Portal skapa roll tilldelningar under princip tilldelning. Den här egenskapen är användbar om du vill tilldela behörigheter utanför tilldelnings omfånget. Det finns en roll tilldelning per roll definition i principen (eller per roll definition i alla principer i initiativet). Parametervärdet måste vara en giltig resurs eller ett giltigt omfång.
- `defaultValue`: (Valfritt) anger värdet för parametern i en tilldelning om inget värde anges.
  Krävs när du uppdaterar en befintlig princip definition som är tilldelad.
- `allowedValues`: (Valfritt) tillhandahåller en matris med värden som parametern accepterar under tilldelningen.

Du kan till exempel definiera en princip definition för att begränsa de platser där resurser kan distribueras. En parameter för den princip definitionen kan vara **allowedLocations**. Den här parametern används av varje tilldelning av princip definitionen för att begränsa de godkända värdena. Användningen av **strongType** ger en bättre upplevelse när du slutför tilldelningen via portalen:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": [ "westus2" ],
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="using-a-parameter-value"></a>Använda ett parameter värde

I princip regeln hänvisar du parametrar till följande `parameters` Function-syntax:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Det här exemplet refererar till den **allowedLocations** -parameter som visades i [parameter egenskaper](#parameter-properties).

### <a name="strongtype"></a>strongType

I `metadata` egenskapen kan du använda **strongType** för att ange en lista med alternativ för flera val i Azure Portal. **strongType** kan vara en _resurs typ_ som stöds eller ett tillåtet värde. Använd [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider)för att avgöra om en _resurs typ_ är giltig för **strongType**. Formatet för en _resurs typ_ **strongType** är `<Resource Provider>/<Resource Type>` . Ett exempel är `Microsoft.Network/virtualNetworks/subnets`.

Vissa _resurs typer_ som inte returneras av **Get-AzResourceProvider** stöds. Dessa typer är:

- `Microsoft.RecoveryServices/vaults/backupPolicies`

_Tillåtna värden_ för icke- **strongType** är:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="definition-location"></a>Definitions plats

När du skapar ett initiativ eller en princip måste du ange definitions platsen. Definitions platsen måste vara en hanterings grupp eller en prenumeration. Den här platsen avgör omfattningen som initiativet eller principen kan tilldelas till. Resurser måste vara direkta medlemmar av eller underordnade inom hierarkin för den definitions plats som ska användas som mål för tilldelningen.

Om definitions platsen är:

- Endast **prenumerations** resurser i den prenumerationen kan tilldelas princip definitionen.
- **Hanterings grupp** – endast resurser inom underordnade hanterings grupper och underordnade prenumerationer kan tilldelas princip definitionen. Om du planerar att tillämpa princip definitionen på flera prenumerationer måste platsen vara en hanterings grupp som innehåller varje prenumeration.

Mer information finns i [förstå omfattning i Azure policy](./scope.md#definition-location).

## <a name="policy-rule"></a>Principregel

Princip regeln består av **IF** och **then** -block. I **IF** -blocket definierar du ett eller flera villkor som anger när principen tillämpas. Du kan använda logiska operatorer för dessa villkor för att exakt definiera scenariot för en princip.

I blocket **then** definierar du den påverkan som inträffar när **IF** -villkoren är uppfyllda.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | modify | append | auditIfNotExists | deployIfNotExists | disabled"
    }
}
```

### <a name="logical-operators"></a>Logiska operatorer

Logiska operatorer som stöds är:

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

Syntaxen **not** inverterar resultatet av villkoret. **AllOf** -syntaxen (liknar logisk **och** åtgärd) kräver att alla villkor är sanna. **AnyOf** -syntaxen (liknar logisk **eller** åtgärd) kräver att ett eller flera villkor är sanna.

Du kan kapsla logiska operatorer. I följande exempel visas en åtgärd som är kapslad i en **allOf** **-åtgärd.**

```json
"if": {
    "allOf": [{
            "not": {
                "field": "tags",
                "containsKey": "application"
            }
        },
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        }
    ]
},
```

### <a name="conditions"></a>Villkor

Ett villkor utvärderar om ett **fält** eller **värde** accessor uppfyller vissa villkor. De villkor som stöds är:

- `"equals": "stringValue"`
- `"notEquals": "stringValue"`
- `"like": "stringValue"`
- `"notLike": "stringValue"`
- `"match": "stringValue"`
- `"matchInsensitively": "stringValue"`
- `"notMatch": "stringValue"`
- `"notMatchInsensitively": "stringValue"`
- `"contains": "stringValue"`
- `"notContains": "stringValue"`
- `"in": ["stringValue1","stringValue2"]`
- `"notIn": ["stringValue1","stringValue2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"less": "dateValue"` | `"less": "stringValue"` | `"less": intValue`
- `"lessOrEquals": "dateValue"` | `"lessOrEquals": "stringValue"` | `"lessOrEquals": intValue`
- `"greater": "dateValue"` | `"greater": "stringValue"` | `"greater": intValue`
- `"greaterOrEquals": "dateValue"` | `"greaterOrEquals": "stringValue"` |
  `"greaterOrEquals": intValue`
- `"exists": "bool"`

För **mindre**, **lessOrEquals**, **större** och **större**, om egenskaps typen inte matchar villkors typen, genereras ett fel. Sträng jämförelser görs med `InvariantCultureIgnoreCase` .

När du använder **gilla** -och **notLike** -villkoren anger du ett jokertecken `*` i värdet.
Värdet får inte ha fler än ett jokertecken `*` .

När du använder **matchnings** -och **notMatch** -villkor, anger `#` du för att matcha en siffra, `?` för en bokstav, för `.` att matcha alla tecken och andra tecken som ska matcha det faktiska tecknet. Även om **match** -och **notMatch** är Skift läges känsliga, är alla andra villkor som utvärderar en _stringValue_ Skift läges känsliga. Skift läges känsliga alternativ är tillgängliga i **matchInsensitively** och **notMatchInsensitively**.

Varje element i matrisen utvärderas individuellt med logiska element **och** mellan element i fältet **\[ \* \] alias för Ali Aset** . Mer information finns i [referera till mat ris resurs egenskaper](../how-to/author-policies-for-arrays.md#referencing-array-resource-properties).

### <a name="fields"></a>Fält

Villkor bildas med hjälp av fält. Ett fält matchar egenskaper i nytto lasten för resurs förfrågan och beskriver resursens tillstånd.

Följande fält stöds:

- `name`
- `fullName`
  - Returnerar resursens fullständiga namn. Det fullständiga namnet på en resurs är resurs namnet anpassningsprefix av eventuella överordnade resurs namn (till exempel "Server/databas").
- `kind`
- `type`
- `location`
  - Använd **Global** för resurser som är plats oberoende.
- `id`
  - Returnerar resurs-ID för den resurs som utvärderas.
  - Exempel: `/subscriptions/06be863d-0996-4d56-be22-384767287aa2/resourceGroups/myRG/providers/Microsoft.KeyVault/vaults/myVault`
- `identity.type`
  - Returnerar typen av [hanterad identitet](../../../active-directory/managed-identities-azure-resources/overview.md) som är aktive rad på resursen.
- `tags`
- `tags['<tagName>']`
  - Den här klammerns syntax stöder taggnamn som har skiljetecken, till exempel bindestreck, punkter eller blank steg.
  - Där **\<tagName\>** är namnet på taggen för att validera villkoret för.
  - Exempel: `tags['Acct.CostCenter']` där **acct. CostCenter** är namnet på taggen.
- `tags['''<tagName>''']`
  - Den här klammerns syntax stöder taggnamn som har apostrofer i den genom att använda dubbla apostrofer.
  - Där **är \<tagName\> namnet** på taggen som används för att validera villkoret för.
  - Exempel: `tags['''My.Apostrophe.Tag''']` där **' My. apostrof. tag '** är namnet på taggen.
- egenskaps Ali Aset – en lista finns i [alias](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]` , och `tags[tag.with.dots]` är fortfarande acceptabla sätt att deklarera ett Tags-fält. De prioriterade uttrycken är dock de som anges ovan.

#### <a name="use-tags-with-parameters"></a>Använda taggar med parametrar

Ett parameter värde kan skickas till ett tagg-fält. Att skicka en parameter till ett taggnamn ökar flexibiliteten i princip definitionen under princip tilldelning.

I följande exempel `concat` används för att skapa ett fält uppslag för taggen som heter värdet för **TagName** -parametern. Om taggen inte finns används **ändrings** funktionen för att lägga till taggen med värdet för samma namngivna tagg uppsättning på den överordnade resurs gruppen granskade resurser med hjälp av `resourcegroup()` funktionen lookup.

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "operations": [{
                "operation": "add",
                "field": "[concat('tags[', parameters('tagName'), ']')]",
                "value": "[resourcegroup().tags[parameters('tagName')]]"
            }],
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ]
        }
    }
}
```

### <a name="value"></a>Värde

Villkor kan även skapas med hjälp av **värde**. **värde** kontrollerar villkor mot [parametrar](#parameters), [mall funktioner som stöds](#policy-functions)eller litteraler. **värdet** kombineras med alla [villkor](#conditions)som stöds.

> [!WARNING]
> Om resultatet av en _mall_ är ett fel, Miss lyckas princip utvärderingen. En misslyckad utvärdering är en implicit **nekande**. Mer information finns i [undvika mall-haverier](#avoiding-template-failures). Använd [enforcementMode](./assignment-structure.md#enforcement-mode) av **DoNotEnforce** för att förhindra påverkan av en misslyckad utvärdering på nya eller uppdaterade resurser vid testning och validering av en ny princip definition.

#### <a name="value-examples"></a>Värde exempel

I den här princip regel exemplet används **värde** för att jämföra resultatet av `resourceGroup()` funktionen och egenskapen returnerat **namn** till ett **like** -villkor `*netrg` . Regeln nekar en resurs som inte är av `Microsoft.Network/*` **typen** i någon resurs grupp vars namn slutar med `*netrg` .

```json
{
    "if": {
        "allOf": [{
                "value": "[resourceGroup().name]",
                "like": "*netrg"
            },
            {
                "field": "type",
                "notLike": "Microsoft.Network/*"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Den här princip regel exemplet använder **värdet** för att kontrol lera om resultatet av flera kapslade funktioner **är lika med** `true` . Regeln nekar en resurs som inte har minst tre taggar.

```json
{
    "mode": "indexed",
    "policyRule": {
        "if": {
            "value": "[less(length(field('tags')), 3)]",
            "equals": "true"
        },
        "then": {
            "effect": "deny"
        }
    }
}
```

#### <a name="avoiding-template-failures"></a>Undvika synkroniseringsfel

Användningen av _Template Functions_ i **Value** tillåter många komplexa kapslade funktioner. Om resultatet av en _mall_ är ett fel, Miss lyckas princip utvärderingen. En misslyckad utvärdering är en implicit **nekande**. Ett exempel på ett **värde** som inte fungerar i vissa scenarier:

```json
{
    "policyRule": {
        "if": {
            "value": "[substring(field('name'), 0, 3)]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

Exempel princip regeln ovan använder [del sträng ()](../../../azure-resource-manager/templates/template-functions-string.md#substring) för att jämföra de tre första tecken **namnen** med **ABC**. Om **namnet** är kortare än tre tecken `substring()` resulterar funktionen i ett fel. Det här felet gör att principen blir en **neka** -påverkan.

Använd i stället funktionen [IF ()](../../../azure-resource-manager/templates/template-functions-logical.md#if) för att kontrol lera om de tre första tecknen i **namn** är lika med **ABC** utan att ett **namn** som är kortare än tre tecken kan orsaka ett fel:

```json
{
    "policyRule": {
        "if": {
            "value": "[if(greaterOrEquals(length(field('name')), 3), substring(field('name'), 0, 3), 'not starting with abc')]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

Med den reviderade princip regeln `if()` kontrollerar **namnet på namnet** innan det försöker hämta ett `substring()` värde med färre än tre tecken. Om **namnet** är för kort returneras värdet "inte börjar med ABC" i stället och jämförs med **ABC**. En resurs med ett kort namn som inte börjar med **ABC** kan fortfarande inte utföra princip regeln, men inte längre orsaka ett fel under utvärderingen.

### <a name="count"></a>Antal

Villkor som räknar hur många medlemmar i en matris i resurs nytto lasten uppfyller ett villkors uttryck kan skapas med hjälp av **Count** -uttryck. Vanliga scenarier kontrollerar om "minst en av", ",", "alla" eller "ingen av" mat ris medlemmarna uppfyller villkoret. **Count** utvärderar varje [ \[ \* \] alias](#understanding-the--alias) mat ris medlem för ett villkors uttryck och summerar de _sanna_ resultaten, som sedan jämförs med uttrycks operatorn. **Count** -uttryck kan läggas till upp till tre gånger i en enda **policyRule** -definition.

Strukturen för **Count** -uttrycket är:

```json
{
    "count": {
        "field": "<[*] alias>",
        "where": {
            /* condition expression */
        }
    },
    "<condition>": "<compare the count of true condition expression array members to this value>"
}
```

Följande egenskaper används med **Count**:

- **Count. Field** (required): innehåller sökvägen till matrisen och måste vara ett mat ris alias. Om matrisen saknas utvärderas uttrycket till _false_ utan att ta hänsyn till villkors uttrycket.
- **Count.** (valfritt): villkors uttrycket för att varje [ \[ \* \] alias](#understanding-the--alias) ska utvärderas individuellt i **fältet Count.** Om den här egenskapen inte anges utvärderas alla mat ris medlemmar med sökvägen för Field till _True_. Alla [villkor](../concepts/definition-structure.md#conditions) kan användas i den här egenskapen.
  [Logiska operatorer](#logical-operators) kan användas i den här egenskapen för att skapa komplexa utvärderings krav.
- **\<condition\>** (obligatoriskt): värdet jämförs med antalet objekt som uppfyllde **antalet. Where** villkors uttryck. Ett numeriskt [villkor](../concepts/definition-structure.md#conditions) ska användas.

Mer information om hur du arbetar med mat ris egenskaper i Azure Policy, inklusive detaljerad förklaring om hur antalet uttryck utvärderas, finns i [referera till mat ris resurs egenskaper](../how-to/author-policies-for-arrays.md#referencing-array-resource-properties).

#### <a name="count-examples"></a>Antal exempel

Exempel 1: kontrol lera om en matris är tom

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

Exempel 2: kontrol lera att endast en mat ris medlem uppfyller villkors uttrycket

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My unique description"
        }
    },
    "equals": 1
}
```

Exempel 3: kontrol lera om minst en mat ris medlem uppfyller villkors uttrycket

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My common description"
        }
    },
    "greaterOrEquals": 1
}
```

Exempel 4: kontrol lera att alla medlemmar i objekt mat ris uppfyller villkors uttrycket

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "description"
        }
    },
    "equals": "[length(field('Microsoft.Network/networkSecurityGroups/securityRules[*]'))]"
}
```

Exempel 5: kontrol lera att minst en mat ris medlem matchar flera egenskaper i villkors uttrycket

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "allOf": [
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].direction",
                    "equals": "Inbound"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].access",
                    "equals": "Allow"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].destinationPortRange",
                    "equals": "3389"
                }
            ]
        }
    },
    "greater": 0
}
```

Exempel 6: `field()` funktionen use inuti `where` villkoren för att få åtkomst till det litterala värdet för den aktuella utvärderade mat ris medlemmen. Det här villkoret kontrollerar att det inte finns några säkerhets regler med ett jämnt numrerat _prioritets_ värde.

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
          "value": "[mod(first(field('Microsoft.Network/networkSecurityGroups/securityRules[*].priority')), 2)]",
          "equals": 0
        }
    },
    "greater": 0
}
```

### <a name="effect"></a>Effekt

Azure Policy stöder följande typer av påverkan:

- **APPEND**: lägger till den definierade fält uppsättningen i begäran
- **Granskning**: genererar en varnings händelse i aktivitets loggen men misslyckade begäran
- **AuditIfNotExists**: genererar en varnings händelse i aktivitets loggen om en relaterad resurs inte finns
- **Neka**: genererar en händelse i aktivitets loggen och Miss lyckas med begäran
- **DeployIfNotExists**: distribuerar en relaterad resurs om den inte redan finns
- **Disabled**: utvärderar inte resurser för efterlevnad för princip regeln
- **Ändra**: lägger till, uppdaterar eller tar bort definierade taggar från en resurs
- **EnforceOPAConstraint** (inaktuell): konfigurerar styrenheten för öppna princip agent med Gatekeeper v3 för självhanterade Kubernetes-kluster på Azure
- **EnforceRegoPolicy** (inaktuell): konfigurerar styrenheten för öppna Policy Agent-inspelare med Gatekeeper v2 i Azure Kubernetes-tjänsten

Fullständig information om varje effekt, utvärderings ordning, egenskaper och exempel finns i [förstå Azure policys effekter](effects.md).

### <a name="policy-functions"></a>Princip funktioner

Alla [funktioner i Resource Manager-mallar](../../../azure-resource-manager/templates/template-functions.md) är tillgängliga för användning i en princip regel, förutom följande funktioner och användardefinierade funktioner:

- copyIndex()
- distribution ()
- lista
- newGuid()
- pickZones()
- providers ()
- referens ()
- resourceId ()
- variabler ()

> [!NOTE]
> Dessa funktioner är fortfarande tillgängliga i den `details.deployment.properties.template` del av mallen som distribueras i en **deployIfNotExists** princip definition.

Följande funktion är tillgänglig för användning i en princip regel, men skiljer sig från användning i en Azure Resource Manager mall (ARM-mall):

- `utcNow()` – Till skillnad från en ARM-mall, kan den här egenskapen användas utanför _DefaultValue_.
  - Returnerar en sträng som har angetts till aktuellt datum och aktuell tid i Universal ISO 8601 DateTime-format `yyyy-MM-ddTHH:mm:ss.fffffffZ` .

Följande funktioner är endast tillgängliga i princip regler:

- `addDays(dateTime, numberOfDaysToAdd)`
  - **datetime**: [required] sträng sträng i Universal ISO 8601 datetime-formatet ' ÅÅÅÅ-MM-ddTHH: mm: SS. FFFFFFFZ'
  - **numberOfDaysToAdd**: [required] heltal-antal dagar som ska läggas till
- `field(fieldName)`
  - **FieldName**: [required] sträng-namnet på det [fält](#fields) som ska hämtas
  - Returnerar värdet för det fältet från den resurs som utvärderas av IF-villkoret.
  - `field` används i första hand med **AuditIfNotExists** och **DeployIfNotExists** för att referera till fält på den resurs som utvärderas. Ett exempel på den här användningen kan visas i [DeployIfNotExists-exemplet](effects.md#deployifnotexists-example).
- `requestContext().apiVersion`
  - Returnerar API-versionen för den begäran som utlöste princip utvärderingen (exempel: `2019-09-01` ).
    Det här värdet är den API-version som användes i begäran om att skicka/korrigera för utvärderingar om att skapa eller uppdatera resurser. Den senaste API-versionen används alltid vid utvärdering av efterlevnad på befintliga resurser.
- `policy()`
  - Returnerar följande information om den princip som utvärderas. Egenskaperna kan nås från det returnerade objektet (exempel: `[policy().assignmentId]` ).
  
  ```json
  {
    "assignmentId": "/subscriptions/ad404ddd-36a5-4ea8-b3e3-681e77487a63/providers/Microsoft.Authorization/policyAssignments/myAssignment",
    "definitionId": "/providers/Microsoft.Authorization/policyDefinitions/34c877ad-507e-4c82-993e-3452a6e0ad3c",
    "setDefinitionId": "/providers/Microsoft.Authorization/policySetDefinitions/42a694ed-f65e-42b2-aa9e-8052e9740a92",
    "definitionReferenceId": "StorageAccountNetworkACLs"
  }
  ```


- `ipRangeContains(range, targetRange)`
    - **intervall**: [required] sträng-sträng som anger ett intervall med IP-adresser.
    - **targetRange**: [required] sträng sträng som anger ett intervall med IP-adresser.

    Returnerar om det angivna IP-adressintervallet innehåller mål-IP-adressintervallet. Tomma intervall eller blandning mellan IP-familjer tillåts inte och resulterar i ett utvärderings problem.

    Format som stöds:
    - Enskild IP-adress (exempel: `10.0.0.0` , `2001:0DB8::3:FFFE` )
    - CIDR-intervall (exempel: `10.0.0.0/24` , `2001:0DB8::/110` )
    - Intervall som definieras av start-och slut-IP-adresser (exempel: `192.168.0.1-192.168.0.9` , `2001:0DB8::-2001:0DB8::3:FFFF` )


#### <a name="policy-function-example"></a>Exempel på princip funktion

Den här princip regel exemplet använder funktionen `resourceGroup` Resource för att hämta egenskapen **Name** , kombinerat med `concat` funktionen array och Object för att skapa ett `like` villkor som tvingar resurs namnet att starta med resurs gruppens namn.

```json
{
    "if": {
        "not": {
            "field": "name",
            "like": "[concat(resourceGroup().name,'*')]"
        }
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="aliases"></a>Alias

Du använder alias för egenskaper för att få åtkomst till vissa egenskaper för en resurs typ. Alias gör att du kan begränsa vilka värden eller villkor som tillåts för en egenskap på en resurs. Varje alias mappar till sökvägar i olika API-versioner för en specifik resurs typ. Under princip utvärderingen hämtar princip motorn egenskaps Sök vägen för API-versionen.

Listan över alias växer alltid. Använd någon av följande metoder för att ta reda på vilka alias som för närvarande stöds av Azure Policy:

- Azure Policy tillägg för Visual Studio Code (rekommenderas)

  Använd [Azure policy-tillägget för Visual Studio Code](../how-to/extension-for-vscode.md) för att visa och identifiera alias för resurs egenskaper.

  :::image type="content" source="../media/extension-for-vscode/extension-hover-shows-property-alias.png" alt-text="Skärm bild av Azure Policy-tillägget för Visual Studio-kod som hovrar på en egenskap för att visa namnen på alias." border="false":::

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Compute -- Microsoft.Compute)
  (Get-AzPolicyAlias -NamespaceMatch 'compute').Aliases
  ```

  > [!NOTE]
  > Om du vill söka efter alias som kan användas med [ändra](./effects.md#modify) -resultatet använder du följande kommando i Azure PowerShell **4.6.0** eller högre:
  >
  > ```azurepowershell-interactive
  > Get-AzPolicyAlias | Select-Object -ExpandProperty 'Aliases' | Where-Object { $_.DefaultMetadata.Attributes -eq 'Modifiable' }
  > ```

- Azure CLI

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Compute -- Microsoft.Compute)
  az provider show --namespace Microsoft.Compute --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REST API/ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2019-10-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Förstå aliaset [*]

Flera av de tillgängliga aliasen har en version som visas som ett normalt namn och en annan som är **\[\*\]** kopplad till den. Till exempel:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

Aliaset "normal" representerar fältet som ett enda värde. Det här fältet är för exakta matchnings scenarier när hela uppsättningen med värden måste vara exakt som definierad, inte mer eller mindre.

**\[\*\]** Alias representerar en samling värden som valts från elementen i en mat ris resurs egenskap. Till exempel:

| Alias | Valda värden |
|:---|:---|
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | Elementen i `ipRules` matrisen. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | Värdena för `action` egenskapen från varje element i `ipRules` matrisen. |

När du använder i ett [fält](#fields) villkor gör mat ris Ali Aset det möjligt att jämföra varje enskilt mat ris element med ett målvärde. När det används med [Count](#count) -uttryck är det möjligt att:

- Kontrol lera storleken på en matris
- Kontrol lera om all\any\none för mat ris element uppfyller ett komplext villkor
- Kontrol lera om exakt ***n*** mat ris element uppfyller ett komplext villkor

Mer information och exempel finns i [referera till mat ris resurs egenskaper](../how-to/author-policies-for-arrays.md#referencing-array-resource-properties).

## <a name="next-steps"></a>Nästa steg

- Se [initiativ definitions strukturen](./initiative-definition-structure.md)
- Granska exempel i [Azure policy exempel](../samples/index.md).
- Granska [Förstå policy-effekter](effects.md).
- Lär dig att [program mässigt skapa principer](../how-to/programmatically-create.md).
- Lär dig hur du [hämtar efterlevnadsprinciper](../how-to/get-compliance-data.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska en hanterings grupp med [organisera dina resurser med Azures hanterings grupper](../../management-groups/overview.md).
