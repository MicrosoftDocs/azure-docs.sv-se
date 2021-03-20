---
title: 'Självstudie: skapa en anpassad princip definition'
description: I den här självstudien får du en anpassad princip definition för Azure Policy att använda anpassade affärs regler på dina Azure-resurser.
ms.date: 10/05/2020
ms.topic: tutorial
ms.openlocfilehash: 817e6f494b024b9a789f39a4101236f64d8fa0cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97882899"
---
# <a name="tutorial-create-a-custom-policy-definition"></a>Självstudie: skapa en anpassad princip definition

Med en anpassad principdefinition kan kunderna definiera egna regler för att använda Azure. De här reglerna framtvingar ofta:

- Säkerhetsmetoder
- Kostnadshantering
- Organisationsspecifika regler (till exempel namngivning eller platser)

Oavsett affärsdrivande faktor för att skapa en anpassad princip är stegen desamma för att definiera en ny anpassad princip.

Innan du skapar en anpassad princip bör du titta bland [principexemplen](../samples/index.md) för att se om det redan finns en princip som passar dina behov.

Metoden för att skapa en anpassad princip följer de här stegen:

> [!div class="checklist"]
> - Identifiera dina affärskrav
> - Mappa varje krav till en Azure-resursegenskap
> - Mappa egenskapen till ett alias
> - Fastställa vilken effekt som ska användas
> - Skapa principdefinitionen

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="identify-requirements"></a>Identifiera krav

Det är viktigt att du förstår syftet med principen innan du skapar principdefinitionen. I den här självstudien använder vi ett vanligt företagssäkerhetskrav som målet för att illustrera stegen som ingår:

- Varje lagringskonto måste vara aktiverat för HTTPS
- Varje lagringskonto måste vara inaktiverat för HTTPS

Dina krav bör tydligt identifiera både resurstillståndet ”to be” och ”not to be”.

Vi har definierat förväntat tillstånd för resursen, men vi har ännu inte definierat vad vi vill ha gjort med icke-kompatibla resurser. Azure Policy stöder ett antal [effekter](../concepts/effects.md). I den här självstudien ska vi definiera affärsbehov som att förhindra skapandet av resurser om de inte är kompatibla med affärsreglerna. För att nå detta mål använder vi effekten för att [neka](../concepts/effects.md#deny). Vi vill också ha alternativet för att inaktivera principen för specifika tilldelningar. Därför använder vi den [inaktiverade](../concepts/effects.md#disabled) effekten och skapar en [parameter](../concepts/definition-structure.md#parameters) i principdefinitionen.

## <a name="determine-resource-properties"></a>Fastställa resursegenskaper

Utifrån affärs kraven är Azure-resursen som ska granskas med Azure Policy ett lagrings konto. Vi vet emellertid inte vilka egenskaper som ska användas i principdefinitionen. Azure Policy utvärderas mot JSON-representationen av resursen så att vi måste förstå de egenskaper som är tillgängliga för den resursen.

Det finns många sätt att avgöra egenskaperna för en Azure-resurs. Vi ska titta på var och en för den här självstudien:

- Azure Policy-tillägg för VS Code
- Azure Resource Manager mallar (ARM-mallar)
  - Exportera befintlig resurs
  - Skapandeupplevelse
  - Snabbstartsmallar (GitHub)
  - Mallreferensdokument
- Azure Resource Explorer

### <a name="view-resources-in-vs-code-extension"></a>Visa resurser i VS Code-tillägg

[Vs Code-tillägget](../how-to/extension-for-vscode.md#search-for-and-view-resources) kan användas för att söka efter resurser i din miljö och se Resource Manager-egenskaperna för varje resurs.

### <a name="arm-templates"></a>ARM-mallar

Det finns flera sätt att titta på på en [arm](../../../azure-resource-manager/templates/template-tutorial-use-template-reference.md) som innehåller den egenskap som du vill hantera.

#### <a name="existing-resource-in-the-portal"></a>Befintlig resurs i portalen

Det enklaste sättet att hitta egenskaper är att titta på en befintlig resurs av samma typ. Resurser som redan har konfigurerats med inställningen som du vill framtvinga innehåller också värdet att jämföra med.
Titta på sidan **Exportera mall** (under **inställningar**) i Azure Portal för den aktuella resursen.

> [!WARNING]
> ARM-mallen som exporteras av Azure Portal kan inte anslutas direkt till `deployment` egenskapen för en arm-mall i en princip definition för [deployIfNotExists](../concepts/effects.md#deployifnotexists) .

:::image type="content" source="../media/create-custom-policy-definition/export-template.png" alt-text="Skärm bild av sidan Exportera mall på en befintlig resurs i Azure Portal." border="false":::

Om du gör det för ett lagringskonto visas en mall som liknar det här exemplet:

```json
...
"resources": [{
    "comments": "Generalized from resource: '/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount'.",
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "name": "[parameters('storageAccounts_mystorageaccount_name')]",
    "apiVersion": "2018-07-01",
    "location": "westus",
    "tags": {
        "ms-resource-usage": "azure-cloud-shell"
    },
    "scale": null,
    "properties": {
        "networkAcls": {
            "bypass": "AzureServices",
            "virtualNetworkRules": [],
            "ipRules": [],
            "defaultAction": "Allow"
        },
        "supportsHttpsTrafficOnly": false,
        "encryption": {
            "services": {
                "file": {
                    "enabled": true
                },
                "blob": {
                    "enabled": true
                }
            },
            "keySource": "Microsoft.Storage"
        }
    },
    "dependsOn": []
}]
...
```

Under **egenskaper** är ett värde med namnet **supportsHttpsTrafficOnly** inställt på **falskt**. Den här egenskapen verkar vara den vi letar efter. Resursens **typ** är **Microsoft.Storage/storageAccounts**. Med typen kan vi begränsa principen till endast resurser av den här typen.

#### <a name="create-a-resource-in-the-portal"></a>Skapa en resurs i portalen

Ett annat sätt via portalen är resursskapandeupplevelsen. När du skapar ett lagringskonto via portalen är ett alternativ under fliken **Avancerat****Säkerhetsöverföring krävs**. Den här egenskapen har alternativen _Inaktiverad_ och _Aktiverad_. Informationsikonen har ytterligare text som bekräftar att det är sannolikt att det här alternativet är egenskapen vi vill ha. Dock visar inte portalen egenskapens namn på den här skärmen.

På fliken **Granska + skapa** finns en länk längst ned på sidan för att **ladda ned en mall för automatisering**. Om du väljer länken öppnas den mall som skapar den resurs som vi har konfigurerat. I det här fallet kan vi se två viktiga uppgifter:

```json
...
"supportsHttpsTrafficOnly": {
    "type": "bool"
}
...
"properties": {
    "accessTier": "[parameters('accessTier')]",
    "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]"
}
...
```

Den här informationen talar om egenskapstypen och bekräftar även att **supportsHttpsTrafficOnly** är egenskapen vi letar efter.

#### <a name="quickstart-templates-on-github"></a>Snabbstartsmallar på GitHub

[Azure snabb starts-mallar](https://github.com/Azure/azure-quickstart-templates) på GitHub har HUNDRATALs arm-mallar som skapats för olika resurser. Dessa mallar kan vara ett bra sätt att hitta resursegenskapen du letar efter. Vissa egenskaper kanske verkar vara det du letar efter, men styr något annat.

#### <a name="resource-reference-docs"></a>Resursreferensdokument

För att verifiera att **supportsHttpsTrafficOnly** är rätt egenskap, kontrollerar du referensen för arm-mallen för [lagrings konto resursen](/azure/templates/microsoft.storage/2018-07-01/storageaccounts) på lagringsprovidern. Egenskapsobjektet har en lista med giltiga parametrar. Om du väljer länken [StorageAccountPropertiesCreateParameters-object](/azure/templates/microsoft.storage/2018-07-01/storageaccounts#storageaccountpropertiescreateparameters-object) visas en tabell över godkända egenskaper. **supportsHttpsTrafficOnly** finns och beskrivningen matchar vad vi är intresserade av för att uppfylla affärskraven.

### <a name="azure-resource-explorer"></a>Azure Resource Explorer

Du kan även utforska dina Azure-resurser via [Azure Resource Explorer](https://resources.azure.com) (förhandsversion). Det här verktyget använder kontexten för din prenumeration, så du måste autentisera till webbplatsen med dina autentiseringsuppgifter för Azure. När autentiseringen är klar kan du söka genom providrar, prenumerationer, resursgrupper och resurser.

Leta upp en lagringskontoresurs och titta på egenskaperna. Vi kan se egenskapen **supportsHttpsTrafficOnly** även här. Om vi väljer fliken **Dokumentation** ser vi att egenskapsbeskrivningen matchar det vi påträffade i referensdokumenten tidigare.

## <a name="find-the-property-alias"></a>Hitta egenskapsalias

Vi har identifierat resursegenskapen, men vi behöver mappa egenskapen till ett [alias](../concepts/definition-structure.md#aliases).

Det finns några olika sätt att avgöra alias för en Azure-resurs. Vi ska titta på var och en för den här självstudien:

- Azure Policy-tillägg för VS Code
- Azure CLI
- Azure PowerShell

### <a name="get-aliases-in-vs-code-extension"></a>Hämta alias i VS Code-tillägg

Tillägget Azure Policy tillägg för VS Code gör det enkelt att söka efter resurser och [identifiera alias](../how-to/extension-for-vscode.md#discover-aliases-for-resource-properties).

> [!NOTE]
> VS Code-tillägget visar endast Resource Manager-läges egenskaper och visar inga egenskaper för [resurs leverantörs läge](../concepts/definition-structure.md#mode) .

### <a name="azure-cli"></a>Azure CLI

I Azure CLI används `az provider`-kommandogruppen för att söka efter resursalias. Vi ska filtrera **Microsoft.Storage**-namnområdet utifrån den information som vi tidigare fått om Azure-resursen.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for type Microsoft.Storage
az provider show --namespace Microsoft.Storage --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

I resultatet ser vi ett alias som stöds av lagringskonton med namnet **supportsHttpsTrafficOnly**. Den här förekomsten av det här aliaset innebär att vi kan skriva principen för att genomdriva våra affärskrav!

### <a name="azure-powershell"></a>Azure PowerShell

I Azure PowerShell används cmdleten `Get-AzPolicyAlias` för att söka efter resursalias. Vi ska filtrera **Microsoft.Storage**-namnområdet utifrån den information som vi tidigare fått om Azure-resursen.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.Storage
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.Storage').Aliases
```

Som i Azure CLI visar resultatet ett alias som stöds av lagringskonton med namnet **supportsHttpsTrafficOnly**.

## <a name="determine-the-effect-to-use"></a>Fastställa vilken effekt som ska användas

Det är nästan lika viktigt att bestämma vad som ska göras med icke-kompatibla resurser som att bestämma vad som ska utvärderas i första hand. Varje möjligt svar på en icke-kompatibel resurs kallas för en [effekt](../concepts/effects.md). Effekten kontrollerar om den icke-kompatibla resursen loggas, blockeras, har bifogade data eller en distribution associerad till sig för att sätta tillbaka resursen i ett kompatibelt tillstånd.

I vårt exempel är neka den påverkan vi vill använda eftersom vi inte vill att icke-kompatibla resurser skapas i vår Azure-miljö. Granskning är ett bra första alternativ för en principeffekt för att avgöra vad effekten av en princip är innan du nekar. Ett sätt att underlätta ändring av effekt per tilldelning är att parameterisera effekten. Se [parametrarna](#parameters) nedan för information om hur.

## <a name="compose-the-definition"></a>Skapa definitionen

Nu har vi egenskapsinformation och alias för det vi planerar att hantera. Därefter ska vi skapa själva principregeln. Om du ännu inte är bekant med principspråket kan du titta på [principdefinitionsstrukturen](../concepts/definition-structure.md) för att se hur du ska strukturera principdefinitionen. Här är en tom mall för hur en principdefinition ska se ut:

```json
{
    "properties": {
        "displayName": "<displayName>",
        "description": "<description>",
        "mode": "<mode>",
        "parameters": {
                <parameters>
        },
        "policyRule": {
            "if": {
                <rule>
            },
            "then": {
                "effect": "<effect>"
            }
        }
    }
}
```

### <a name="metadata"></a>Metadata

De första tre komponenterna är principmetadata. De här komponenterna är enkla att ange värden för eftersom vi vet vad vi skapar regeln för. [Läget](../concepts/definition-structure.md#mode) handlar främst om taggar och resursplats. Eftersom vi inte behöver begränsa utvärdering till resurser som har stöd för taggar använder vi _Alla_ som **läge**.

```json
"displayName": "Deny storage accounts not using only HTTPS",
"description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
"mode": "all",
```

### <a name="parameters"></a>Parametrar

Vi använde inte någon parameter för att ändra utvärderingen, men vi vill använda en parameter för att tillåta ändring av **effekten** för felsökning. Vi ska definiera en **effectType**-parameter och begränsa den till endast **Neka** och **Inaktiverad**. De här två alternativen matchar våra affärskrav. Det slutförda parameterblocket ser ut som i följande exempel:

```json
"parameters": {
    "effectType": {
        "type": "string",
        "defaultValue": "Deny",
        "allowedValues": [
            "Deny",
            "Disabled"
        ],
        "metadata": {
            "displayName": "Effect",
            "description": "Enable or disable the execution of the policy"
        }
    }
},
```

### <a name="policy-rule"></a>Principregel

Skapandet av [principregeln](../concepts/definition-structure.md#policy-rule) är det sista steget i att skapa vår anpassade principdefinition. Vi har identifierat två instruktioner för testning:

- Lagringskontots **typ** är **Microsoft.Storage/storageAccounts**
- Att lagringskontot **supportsHttpsTrafficOnly** inte är **sant**

Eftersom vi behöver båda dessa instruktioner för att vara sanna använder vi den [logiska operatorn](../concepts/definition-structure.md#logical-operators) **allOf** . Vi ska skicka parametern **effectType** för effekten i stället för att göra en statisk deklaration. Vår slutförda regel ser ut som i följande exempel:

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        },
        {
            "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
            "notEquals": "true"
        }
    ]
},
"then": {
    "effect": "[parameters('effectType')]"
}
```

### <a name="completed-definition"></a>Slutförd definition

När alla tre delar av principen har definierats är här vår slutförda definition:

```json
{
    "properties": {
        "displayName": "Deny storage accounts not using only HTTPS",
        "description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
        "mode": "all",
        "parameters": {
            "effectType": {
                "type": "string",
                "defaultValue": "Deny",
                "allowedValues": [
                    "Deny",
                    "Disabled"
                ],
                "metadata": {
                    "displayName": "Effect",
                    "description": "Enable or disable the execution of the policy"
                }
            }
        },
        "policyRule": {
            "if": {
                "allOf": [
                    {
                        "field": "type",
                        "equals": "Microsoft.Storage/storageAccounts"
                    },
                    {
                        "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
                        "notEquals": "true"
                    }
                ]
            },
            "then": {
                "effect": "[parameters('effectType')]"
            }
        }
    }
}
```

Den slutförda definitionen kan användas för att skapa en ny princip. Portalen och varje SDK (Azure CLI, Azure PowerShell och REST-API) accepterar definitionen på olika sätt, så gå igenom kommandona för varje att verifiera korrekt användning. Tilldela den sedan, med den parameteriserade effekten, till lämpliga resurser för att hantera säkerheten för dina lagringskonton.

## <a name="clean-up-resources"></a>Rensa resurser

Om du är klar med att arbeta med resurser i den här självstudien kan du använda följande steg för att ta bort tilldelningar eller definitioner som skapades ovan:

1. Välj **Definitioner** (eller **Tilldelningar** om du ska ta bort en tilldelning) under **Redigering** till vänster på sidan Azure Policy.

1. Sök efter den nya initiativ- eller principdefinition (eller tilldelning) som du vill ta bort.

1. Högerklicka på raden eller välj ellipserna i slutet av definitionen (eller tilldelningen) och välj **Ta bort definition** (eller **Ta bort tilldelning**).

## <a name="review"></a>Genomgång

I den här självstudien har du genomfört följande uppgifter:

> [!div class="checklist"]
> - Identifierade dina affärskrav
> - Mappade varje krav till en Azure-resursegenskap
> - Mappade egenskapen till ett alias
> - Fastställde vilken effekt som skulle användas
> - Skapade principdefinitionen

## <a name="next-steps"></a>Nästa steg

Använd din anpassade principdefinition för att skapa och tilldela en princip:

> [!div class="nextstepaction"]
> [Skapa och tilldela en principdefinition](../how-to/programmatically-create.md#create-and-assign-a-policy-definition)
