---
title: Information om initiativ definitions strukturen
description: Beskriver hur definitioner av policy initiativ används för att gruppera princip definitioner för distribution till Azure-resurser i din organisation.
ms.date: 08/17/2020
ms.topic: conceptual
ms.openlocfilehash: b151ef4d58998b810e116321de68cbdb2e8d3eff
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544646"
---
# <a name="azure-policy-initiative-definition-structure"></a>Definitions struktur för Azure Policy initiativ

Med initiativ kan du gruppera flera relaterade princip definitioner för att förenkla tilldelningar och hantering eftersom du arbetar med en grupp som ett enda objekt. Du kan till exempel gruppera relaterade definitioner av princip definitioner till ett enda initiativ. I stället för att tilldela varje princip individuellt, tillämpar du initiativet.

Du använder JSON för att skapa en princip initiativ definition. Definitionen av policy initiativ innehåller element för:

- visningsnamn
- beskrivning
- metadata
- parametrar
- princip definitioner
- princip grupper (den här egenskapen ingår i funktionen för för [hands versions kontroll](./regulatory-compliance.md))

I följande exempel visas hur du skapar ett initiativ för att hantera två Taggar: `costCenter` och `productName` . Den använder två inbyggda principer för att tillämpa standard tag gen svärdet.

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "metadata": {
            "version": "1.0.0",
            "category": "Tags"
        },
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                },
                "defaultValue": "DefaultCostCenter"
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                },
                "defaultValue": "DefaultProduct"
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    }
}
```

Azure Policy inbyggda program och mönster finns på [Azure policy exempel](../samples/index.md).

## <a name="metadata"></a>Metadata

Den valfria `metadata` egenskapen innehåller information om definitionen av policy initiativ.
Kunder kan definiera egenskaper och värden som är användbara för deras organisation i `metadata` . Det finns dock några _gemensamma_ egenskaper som används av Azure policy och i inbyggda moduler.

### <a name="common-metadata-properties"></a>Gemensamma egenskaper för metadata

- `version` (sträng): spårar information om versionen av innehållet i en princip initiativs definition.
- `category` (sträng): anger under vilken kategori i Azure Portal princip definitionen visas.

  > [!NOTE]
  > För ett [reglerings](./regulatory-compliance.md) -initiativ `category` måste **lagen efterlevas**.

- `preview` (boolesk): true eller false flagga för om definitionen av policy initiativ är för _hands version_.
- `deprecated` (boolesk): true eller false flagga för om definitionen av policy initiativ har marker ATS som _föråldrad_.

> [!NOTE]
> Azure Policy tjänsten använder `version` , `preview` , och `deprecated` Egenskaper för att förmedla ändrings nivån till en inbyggd princip definition eller initiativ och tillstånd. Formatet `version` är: `{Major}.{Minor}.{Patch}` . Vissa tillstånd, till exempel _föråldrad_ eller för _hands version_, läggs till i `version` egenskapen eller i en annan egenskap som **boolesk**. Mer information om hur Azure Policy inbyggda versioner finns i [inbyggd versions hantering](https://github.com/Azure/azure-policy/blob/master/built-in-policies/README.md).

## <a name="parameters"></a>Parametrar

Med parametrar kan du förenkla princip hanteringen genom att minska antalet princip definitioner. Tänk på parametrar som fälten i ett formulär – `name` ,, `address` `city` , `state` . Dessa parametrar är alltid desamma, men deras värden ändras baserat på de enskilda som fyller i formuläret.
Parametrar fungerar på samma sätt när du skapar princip initiativ. Genom att inkludera parametrar i en princip initiativs definition kan du återanvända den parametern i de principer som ingår.

> [!NOTE]
> När ett initiativ har tilldelats kan parametrarna för Initative-nivå inte ändras. På grund av detta är rekommendationen att ange ett **Standardvärde** när du definierar parametern.

### <a name="parameter-properties"></a>Parameter egenskaper

En parameter har följande egenskaper som används i definitionen av policy initiativ:

- `name`: Namnet på din parameter. Används av `parameters` distributions funktionen i princip regeln. Mer information finns i [använda ett parameter värde](#passing-a-parameter-value-to-a-policy-definition).
- `type`: Anger om parametern är en **sträng**, en **matris**, ett **objekt**, ett **booleskt värde** **, ett** **flyttal**eller en **datetime**.
- `metadata`: Definierar under egenskaper som främst används av Azure Portal för att Visa användarvänlig information:
  - `description`: En förklaring av vad parametern används för. Kan användas för att ge exempel på acceptabla värden.
  - `displayName`: Det egna namnet visas i portalen för parametern.
  - `strongType`: (Valfritt) används för att tilldela princip definitionen via portalen. Innehåller en Sammanhangs medveten lista. Mer information finns i [strongType](#strongtype).
- `defaultValue`: (Valfritt) anger värdet för parametern i en tilldelning om inget värde anges.
- `allowedValues`: (Valfritt) tillhandahåller en matris med värden som parametern accepterar under tilldelningen.

Du kan t. ex. definiera en princip initiativ definition för att begränsa resurs platser i de olika princip definitionerna som ingår. En parameter för denna princip initiativs definition kan vara **allowedLocations**. Parametern är sedan tillgänglig för varje princip definition som ingår och definieras vid tilldelningen av princip initiativet.

```json
"parameters": {
    "init_allowedLocations": {
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

### <a name="passing-a-parameter-value-to-a-policy-definition"></a>Skicka ett parameter värde till en princip definition

Du deklarerar vilka initiativ parametrar du skickar till vilka princip definitioner som ingår i [policyDefinitions](#policy-definitions) -matrisen för initiativ definitionen. Även om parameter namnet kan vara detsamma, fören klar kod läsbarheten med olika namn i initiativen än i princip definitionerna.

Till exempel kan parametern **init_allowedLocations** initiativ som definierats tidigare skickas till flera princip definitioner som ingår och deras parametrar, **sql_locations** och **vm_locations**, så här:

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/0ec8fc28-d5b7-4603-8fec-39044f00a92b",
        "policyDefinitionReferenceId": "allowedLocationsSQL",
        "parameters": {
            "sql_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/aa09bd0f-aa5f-4343-b6ab-a33a6a6304f3",
        "policyDefinitionReferenceId": "allowedLocationsVMs",
        "parameters": {
            "vm_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    }
]
```

Det här exemplet refererar till **init_allowedLocations** parameter som visas i [parameter egenskaper](#parameter-properties).

### <a name="strongtype"></a>strongType

I `metadata` egenskapen kan du använda **strongType** för att ange en lista med alternativ för flera val i Azure Portal. **strongType** kan vara en _resurs typ_ som stöds eller ett tillåtet värde. Använd [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider)för att avgöra om en _resurs typ_ är giltig för **strongType**.

Vissa resurs typer som inte returneras av **Get-AzResourceProvider** stöds. Dessa resurs typer är:

- `Microsoft.RecoveryServices/vaults/backupPolicies`

De värden som inte är av typen resurs typ tillåts för **strongType** :

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="policy-definitions"></a>Principdefinitioner

`policyDefinitions`Delen av initiativ definitionen är en _matris_ där befintliga princip definitioner ingår i initiativet. Som vi nämnt i [att skicka ett parameter värde till en princip definition](#passing-a-parameter-value-to-a-policy-definition)är den här egenskapen där [initiativ parametrar](#parameters) skickas till princip definitionen.

### <a name="policy-definition-properties"></a>Egenskaper för princip definition

Varje _mat ris_ element som representerar en princip definition har följande egenskaper:

- `policyDefinitionId` (sträng): ID: t för den anpassade eller inbyggda princip definitionen som ska ingå.
- `policyDefinitionReferenceId` (sträng): ett kort namn för den inkluderade princip definitionen.
- `parameters`: (Valfritt) namn/värde-par för att skicka en initiativ parameter till den inkluderade princip definitionen som en egenskap i den princip definitionen. Mer information finns i [parametrar](#parameters).
- `groupNames` (sträng mat ris): (valfritt) den grupp som princip definitionen är medlem i. Mer information finns i [princip grupper](#policy-definition-groups).

Här är ett exempel på `policyDefinitions` som har två inkluderade princip definitioner som var och en har genomgått samma initiativ-parameter:

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/0ec8fc28-d5b7-4603-8fec-39044f00a92b",
        "policyDefinitionReferenceId": "allowedLocationsSQL",
        "parameters": {
            "sql_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/aa09bd0f-aa5f-4343-b6ab-a33a6a6304f3",
        "policyDefinitionReferenceId": "allowedLocationsVMs",
        "parameters": {
            "vm_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    }
]
```

## <a name="policy-definitions-groups-preview"></a><a name="policy-definition-groups"></a>Princip definitions grupper (för hands version)

Som en del av Azure Policy [kontroll av regelefterlevnad (för](./regulatory-compliance.md) hands version) kan princip definitioner i en initiativ definition grupperas. Den här informationen definieras i egenskapen `policyDefinitionGroups` _array_ . Dessa grupperingar har ytterligare information, till exempel den **kontroll** och **domän överensstämmelse** som princip definitionen ger täckning mot.
Ytterligare grupperingsinställningar kan hittas i ett **policyMetadata** -objekt som skapats av Microsoft. Mer information finns i [metadata-objekt](#metadata-objects).

### <a name="policy-definition-groups-parameters"></a>Parametrar för princip definitions grupper

Varje _mat ris_ element i `policyDefinitionGroups` måste ha båda följande egenskaper:

- `name` (sträng) \[ krävs \] : **kontrollens**kort namn. Värdet för den här egenskapen används av `groupNames` i `policyDefinitions` .
- `category` (sträng): kontrollens **efterlevnads domän** .
- `displayName` (sträng): det egna namnet på **kontrollen**. Används av portalen.
- `description` (sträng): en beskrivning av vad **kontrollen** gör.
- `additionalMetadataId` (sträng): platsen för [policyMetadata](#metadata-objects) -objektet som innehåller ytterligare information om domänen för **kontroll** och **efterlevnad**.

  > [!NOTE]
  > Kunder kan peka på ett befintligt [policyMetadata](#metadata-objects) -objekt. Dessa objekt är dock _skrivskyddade_ och skapas endast av Microsoft.

Ett exempel på `policyDefinitionGroups` egenskapen från NIST-definitionen för inbyggd initiativ ser ut så här:

```json
"policyDefinitionGroups": [
    {
        "name": "NIST_SP_800-53_R4_AC-1",
        "additionalMetadataId": "/providers/Microsoft.PolicyInsights/policyMetadata/NIST_SP_800-53_R4_AC-1"
    }
]
```

### <a name="metadata-objects"></a>Metadata-objekt

Regler för regelefterlevnad som skapats av Microsoft har ytterligare information om varje kontroll.
Den här informationen är:

- Visas i Azure Portal i Översikt över en **kontroll** på ett reglerings initiativ för regelefterlevnad.
- Tillgängligt via REST API. Se `Microsoft.PolicyInsights` resurs leverantören och policyMetadata- [Åtgärds gruppen](/rest/api/policy-insights/policymetadata/getresource).
- Tillgängligt via Azure CLI. Se kommandot [metadata för AZ-princip](/cli/azure/policy/metadata?view=azure-cli-latest) .

> [!IMPORTANT]
> Metadata-objekt för regelefterlevnad är _skrivskyddade_ och kan inte skapas av kunder.

Metadata för en princip gruppering har följande information i `properties` noden:

- `metadataId`: Det **kontroll-ID** som grupperingen avser.
- `category` (obligatoriskt): den **kompatibilitetstillstånd** som **kontrollen** tillhör.
- `title` (obligatoriskt): det egna namnet på **kontroll-ID: t**.
- `owner` (obligatoriskt): identifierar vem som har ansvar för kontrollen i Azure: _Custom_, _Microsoft_, _Shared_.
- `description`: Ytterligare information om kontrollen.
- `requirements`: Information om ansvaret för implementeringen av kontrollen.
- `additionalContentUrl`: En länk till mer information om kontrollen. Den här egenskapen är vanligt vis en länk till avsnittet i dokumentationen som täcker den här kontrollen i standarden för efterlevnad.

Nedan visas ett exempel på **policyMetadata** -objektet. Detta exempel på metadata hör till _NIST SP 800-53 R4 AC-1-_ kontrollen.

```json
{
  "properties": {
    "metadataId": "NIST SP 800-53 R4 AC-1",
    "category": "Access Control",
    "title": "Access Control Policy and Procedures",
    "owner": "Shared",
    "description": "**The organization:**    \na. Develops, documents, and disseminates to [Assignment: organization-defined personnel or roles]:  \n1. An access control policy that addresses purpose, scope, roles, responsibilities, management commitment, coordination among organizational entities, and compliance; and  \n2. Procedures to facilitate the implementation of the access control policy and associated access controls; and  \n  
\nb. Reviews and updates the current:  \n1. Access control policy [Assignment: organization-defined frequency]; and  \n2. Access control procedures [Assignment: organization-defined frequency].",
    "requirements": "**a.**  The customer is responsible for developing, documenting, and disseminating access control policies and procedures. The customer access control policies and procedures address access to all customer-deployed resources and customer system access (e.g., access to customer-deployed virtual machines, access to customer-built applications).  \n**b.**  The customer is responsible for reviewing and updating access control policies and procedures in accordance with FedRAMP requirements.",
    "additionalContentUrl": "https://nvd.nist.gov/800-53/Rev4/control/AC-1"
  },
  "id": "/providers/Microsoft.PolicyInsights/policyMetadata/NIST_SP_800-53_R4_AC-1",
  "name": "NIST_SP_800-53_R4_AC-1",
  "type": "Microsoft.PolicyInsights/policyMetadata"
}
```

## <a name="next-steps"></a>Nästa steg

- Se [definitions strukturen](./definition-structure.md)
- Granska exempel i [Azure policy exempel](../samples/index.md).
- Granska [Förstå policy-effekter](effects.md).
- Lär dig att [program mässigt skapa principer](../how-to/programmatically-create.md).
- Lär dig hur du [hämtar efterlevnadsprinciper](../how-to/get-compliance-data.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska en hanterings grupp med [organisera dina resurser med Azures hanterings grupper](../../management-groups/overview.md).
