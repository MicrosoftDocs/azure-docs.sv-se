---
title: Återanvänd mallar över moln
description: Utveckla Azure Resource Manager mallar som fungerar konsekvent för olika moln miljöer. Skapa nya eller uppdatera befintliga mallar för Azure Stack.
author: marcvaneijk
ms.topic: conceptual
ms.date: 12/09/2018
ms.author: mavane
ms.custom: seodec18
ms.openlocfilehash: 9355482c26cabb96fc6292bab5d542f36aec6a8c
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509764"
---
# <a name="develop-arm-templates-for-cloud-consistency"></a>Utveckla ARM-mallar för moln konsekvens

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

En viktig fördel med Azure är konsekvens. Utvecklings investeringar för en plats kan återanvändas i en annan. En mall för Azure Resource Manager (ARM) gör distributionen konsekvent och upprepnings bar över miljöer, inklusive globala Azure-, Azure-suveräna moln och Azure Stack. Om du vill återanvända mallar över moln måste du dock fundera över vilka molnbaserade beroenden som beskrivs i den här hand boken.

Microsoft erbjuder intelligenta, företags färdiga moln tjänster på många platser, inklusive:

* Den globala Azure-plattformen stöds av ett växande nätverk av Microsoft-hanterade data Center i regioner runtom i världen.
* Isolerade suveräna moln som Azure Germany, Azure Government och Azure Kina 21Vianet. Suveräna moln ger en konsekvent plattform med de flesta av de fantastiska funktioner som globala Azure-kunder har åtkomst till.
* Azure Stack en hybrid moln plattform som gör det möjligt för dig att leverera Azure-tjänster från organisationens data Center. Företag kan konfigurera Azure Stack i sina egna data Center, eller använda Azure-tjänster från tjänst leverantörer, som kör Azure Stack i sina anläggningar (kallas ibland värdbaserade regioner).

I kärnan i alla dessa moln är Azure Resource Manager ett API som gör det möjligt att kommunicera med Azure-plattformen med en mängd olika användar gränssnitt. Detta API ger dig kraftfulla funktioner för infrastruktur som kod. Alla typer av resurser som är tillgängliga på Azures moln plattform kan distribueras och konfigureras med Azure Resource Manager. Med en enda mall kan du distribuera och konfigurera hela programmet till ett slut tillstånd för drift.

![Azure-miljöer](./media/templates-cloud-consistency/environments.png)

Konsekvensen av Global Azure, de suveräna molnen, de värdbaserade molnen och ett moln i ditt data Center hjälper dig att få nytta av Azure Resource Manager. Du kan återanvända dina utvecklings investeringar i molnet när du konfigurerar mall-baserad resurs distribution och konfiguration.

Men även om globala, suveräna, värdbaserade och hybrid moln tillhandahåller konsekventa tjänster, är alla moln inte identiska. Därför kan du skapa en mall med beroenden för funktioner som endast är tillgängliga i ett särskilt moln.

Resten av den här hand boken beskriver de områden som du bör tänka på när du planerar att utveckla nya eller uppdatera befintliga ARM-mallar för Azure Stack. I allmänhet bör din check lista innehålla följande objekt:

* Kontrol lera att funktionerna, slut punkterna, tjänsterna och andra resurser i mallen är tillgängliga på mål distributions platserna.
* Lagra kapslade mallar och konfigurations artefakter på tillgängliga platser, vilket säkerställer åtkomst över moln.
* Använd dynamiska referenser i stället för hårda-kodning av länkar och element.
* Se till att mallparametrar som du använder fungerar i mål molnen.
* Kontrol lera att resurs specifika egenskaper är tillgängliga för mål molnen.

En introduktion till ARM-mallar finns [malldistribution](overview.md).

## <a name="ensure-template-functions-work"></a>Se till att mallens funktioner fungerar

Den grundläggande syntaxen för en ARM-mall är JSON. Mallar använder en supermängd av JSON och utökar syntaxen med uttryck och funktioner. Mallens språk processor uppdateras ofta för att stödja ytterligare mallar. En detaljerad förklaring av tillgängliga mallar finns i [funktioner i arm-mallar](template-functions.md).

Nya mall funktioner som introduceras för Azure Resource Manager är inte omedelbart tillgängliga i de suveräna molnen eller Azure Stack. För att en mall ska kunna distribueras måste alla funktioner som refereras i mallen vara tillgängliga i mål molnet.

Azure Resource Manager funktioner kommer alltid att lanseras till Global Azure. Du kan använda följande PowerShell-skript för att kontrol lera om nyligen introducerade mal funktioner också är tillgängliga i Azure Stack:

1. Gör en klon av GitHub-lagringsplatsen: [https://github.com/marcvaneijk/arm-template-functions](https://github.com/marcvaneijk/arm-template-functions) .

1. När du har en lokal klon av lagrings platsen ansluter du till målets Azure Resource Manager med PowerShell.

1. Importera psm1-modulen och kör cmdleten test-AzureRmTemplateFunctions:

   ```powershell
   # Import the module
   Import-module <path to local clone>\AzTemplateFunctions.psm1

   # Execute the Test-AzureRmTemplateFunctions cmdlet
   Test-AzureRmTemplateFunctions -path <path to local clone>
   ```

Skriptet distribuerar flera, minimerade mallar, som endast innehåller unika mall-funktioner. Utdata från skriptet rapporterar de mall funktioner som stöds och inte är tillgängliga.

## <a name="working-with-linked-artifacts"></a>Arbeta med länkade artefakter

En mall kan innehålla referenser till länkade artefakter och innehålla en distributions resurs som länkar till en annan mall. De länkade mallarna (kallas även kapslad mall) hämtas av Resource Manager vid körning. En mall kan även innehålla referenser till artefakter för tillägg för virtuella datorer (VM). Dessa artefakter hämtas av VM-tillägget som körs i den virtuella datorn för konfiguration av VM-tillägget under mallen distribution.

I följande avsnitt beskrivs överväganden för moln konsekvens när du utvecklar mallar som innehåller artefakter som är externa i den huvudsakliga distributions mal len.

### <a name="use-nested-templates-across-regions"></a>Använda kapslade mallar i regioner

Mallar kan delas upp i små, återanvändbara mallar som var och en har ett specifikt syfte och som kan återanvändas i distributions scenarier. Om du vill utföra en distribution anger du en enda mall som kallas huvud-eller huvud mal len. Den anger vilka resurser som ska distribueras, till exempel virtuella nätverk, virtuella datorer och webbappar. Huvud mal len kan också innehålla en länk till en annan mall, vilket innebär att du kan kapsla mallar. På samma sätt kan en kapslad mall innehålla länkar till andra mallar. Du kan kapsla upp till fem nivåer djup.

Följande kod visar hur templateLink-parametern refererar till en kapslad mall:

```json
"resources": [
  {
     "type": "Microsoft.Resources/deployments",
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "properties": {
       "mode": "incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/vNet.json",
          "contentVersion":"1.0.0.0"
       }
     }
  }
]
```

Azure Resource Manager utvärderar huvud mal len vid körning och hämtar och utvärderar varje kapslad mall. När alla kapslade mallar har hämtats, förenklas mallen och ytterligare bearbetning initieras.

### <a name="make-linked-templates-accessible-across-clouds"></a>Gör länkade mallar tillgängliga över moln

Överväg var och hur du lagrar eventuella länkade mallar som du använder. Vid körning hämtas Azure Resource Manager, och därför krävs direkt åtkomst till – alla länkade mallar. En vanlig metod är att använda GitHub för att lagra de kapslade mallarna. En GitHub-lagringsplats kan innehålla filer som är tillgängliga offentligt via en URL. Även om den här tekniken fungerar bra för det offentliga molnet och de suveräna molnen kan en Azure Stack miljö finnas i ett företags nätverk eller på en frånkopplad fjärrplats, utan någon utgående Internet åtkomst. I dessa fall skulle Azure Resource Manager inte kunna hämta de kapslade mallarna.

En bättre praxis för distributioner mellan moln är att lagra de länkade mallarna på en plats som är tillgänglig för mål molnet. Vi rekommenderar att alla distributions artefakter upprätthålls i och distribueras från en pipeline för kontinuerlig integrering/kontinuerlig utveckling (CI/CD). Du kan också lagra kapslade mallar i en Blob Storage-behållare där Azure Resource Manager kan hämta dem.

Eftersom Blob Storage på varje moln använder ett annat fullständigt kvalificerat domän namn (FQDN), konfigurerar du mallen med platsen för de länkade mallarna med två parametrar. Parametrar kan acceptera användarindata vid distributions tiden. Mallar har vanligt vis skapats och delats av flera personer, så det är en bra idé att använda ett standard namn för dessa parametrar. Namn konventioner gör det lättare att använda mallar i regioner, moln och författare.

I följande kod `_artifactsLocation` används för att peka på en enda plats, som innehåller alla distributions relaterade artefakter. Observera att ett standardvärde har angetts. Vid distributions tillfället används standardvärdet om inget indatavärde har angetts för `_artifactsLocation` . `_artifactsLocationSasToken`Används som ininformation för `sasToken` . Standardvärdet ska vara en tom sträng för scenarier där `_artifactsLocation` inte är säkrad, till exempel en offentlig GitHub-lagringsplats.

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-custom-script-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

I hela mallen genereras länkar genom att kombinera bas-URI: n (från `_artifactsLocation` parametern) med en artefakt relativ sökväg och `_artifactsLocationSasToken` . Följande kod visar hur du anger länken till den kapslade mallen med hjälp av funktionen URI-mall:

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2019-10-01",
    "name": "shared",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "[uri(parameters('_artifactsLocation'), concat('nested/vnet.json', parameters('_artifactsLocationSasToken')))]",
        "contentVersion": "1.0.0.0"
      }
    }
  }
]
```

Med den här metoden används standardvärdet för `_artifactsLocation` parametern. Om de länkade mallarna måste hämtas från en annan plats, kan parameter ingången användas vid distributions tiden för att åsidosätta standardvärdet – ingen ändring i själva mallen behövs.

### <a name="use-_artifactslocation-instead-of-hardcoding-links"></a>Använd _artifactsLocation i stället för hårdkoda-länkar

Förutom att används för kapslade mallar används URL: en i `_artifactsLocation` parametern som bas för alla relaterade artefakter för en distributionsmall. Vissa VM-tillägg innehåller en länk till ett skript som lagras utanför mallen. För dessa tillägg bör du inte hårdkoda länkarna. Till exempel kan det anpassade skriptet och PowerShell DSC-tillägg länka till ett externt skript på GitHub som visas:

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
    ]
  }
}
```

Hårdkoda länkar till skriptet gör det möjligt att mallen inte kan distribueras till en annan plats. Under konfigurationen av VM-resursen, initierar den virtuella dator agenten som körs i den virtuella datorn en nedladdning av alla skript som är länkade i VM-tillägget och lagrar sedan skripten på den virtuella datorns lokala disk. Den här metoden fungerar som de länkar för kapslade mallar som beskrivs tidigare i avsnittet "använda kapslade mallar över flera regioner".

Resource Manager hämtar kapslade mallar vid körning. För VM-tillägg utförs hämtning av externa artefakter av VM-agenten. Förutom den olika initieraren av artefakt hämtningen är lösningen i definitionen samma. Använd parametern _artifactsLocation med standardvärdet för bas Sök vägen där alla artefakter lagras (inklusive skript för virtuella dator tillägg) och `_artifactsLocationSasToken` parametern för indata för sasToken.

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

För att skapa en artefakts absoluta URI är den bästa metoden att använda funktionen URI-mall, i stället för funktionen concat Template. Genom att ersätta hårdkodad-länkar till skripten i VM-tillägget med funktionen URI-mall, är den här funktionen i mallen konfigurerad för moln konsekvens.

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "[uri(parameters('_artifactsLocation'), concat('scripts/configure-music-app.ps1', parameters('_artifactsLocationSasToken')))]"
    ]
  }
}
```

Med den här metoden kan alla distributions artefakter, inklusive konfigurations skript, lagras på samma plats med själva mallen. Om du vill ändra platsen för alla länkar behöver du bara ange en annan bas-URL för ArtifactsLocation- _parametrarna_.

## <a name="factor-in-differing-regional-capabilities"></a>Faktor i olika regionala funktioner

Med den Agile-utveckling och det kontinuerliga flödet av uppdateringar och nya tjänster som introducerats i Azure [kan regioner variera](https://azure.microsoft.com/regions/services/) i tillgänglighet för tjänster eller uppdateringar. Efter rigorös intern testning lanseras nya tjänster eller uppdateringar av befintliga tjänster vanligt vis till en liten grupp av kunder som deltar i ett verifierings program. Efter en lyckad kund validering görs tjänsterna eller uppdateringarna tillgängliga i en delmängd av Azure-regioner, sedan lanseras de till fler regioner, som är klara för de suveräna molnen och eventuellt gjorts tillgängliga för Azure Stack kunder.

Om du vet att Azure-regioner och moln kan skilja sig åt i de tillgängliga tjänsterna kan du fatta några proaktiva beslut om dina mallar. En bra plats för att börja är genom att undersöka tillgängliga resurs leverantörer för ett moln. En resurs leverantör visar en uppsättning av resurser och åtgärder som är tillgängliga för en Azure-tjänst.

En mall distribuerar och konfigurerar resurser. En resurs typ tillhandahålls av en resurs leverantör. Till exempel tillhandahåller Compute Resource Provider (Microsoft. Compute) flera resurs typer, till exempel virtualMachines och availabilitySets. Varje resurs leverantör tillhandahåller ett API som Azure Resource Manager definieras av ett gemensamt kontrakt, vilket möjliggör en enhetlig och enhetlig redigerings upplevelse i alla resurs leverantörer. Men en resurs leverantör som är tillgänglig i Global Azure kanske inte är tillgänglig i ett suverän moln eller i en Azure Stack region.

![Resursproviders](./media/templates-cloud-consistency/resource-providers.png)

Kör följande skript i kommando rads gränssnittet för Azure ([CLI](/cli/azure/install-azure-cli)) för att kontrol lera vilka resurs leverantörer som är tillgängliga i ett specifikt moln:

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Du kan också använda följande PowerShell-cmdlet för att se tillgängliga resurs leverantörer:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

### <a name="verify-the-version-of-all-resource-types"></a>Verifiera versionen av alla resurs typer

En egenskaps uppsättning är gemensam för alla resurs typer, men varje resurs har också egna specifika egenskaper. Nya funktioner och relaterade egenskaper läggs till i befintliga resurs typer vid gånger via en ny API-version. En resurs i en mall har sin egen API-version-egenskap – `apiVersion` . Den här versionen säkerställer att en befintlig resurs konfiguration i en mall inte påverkas av ändringar på plattformen.

Nya API-versioner som introduceras i befintliga resurs typer i Global Azure kanske inte omedelbart är tillgängliga i alla regioner, suveräna moln eller Azure Stack. Om du vill visa en lista över tillgängliga resurs leverantörer, resurs typer och API-versioner för ett moln kan du använda Resursläsaren i Azure Portal. Sök efter Resursläsaren på menyn alla tjänster. Expandera noden providers i Resursläsaren för att returnera alla tillgängliga resurs-providers, resurs typer och API-versioner i molnet.

Om du vill visa en lista över tillgängliga API-versioner för alla resurs typer i ett angivet moln i Azure CLI kör du följande skript:

```azurecli-interactive
az provider list --query "[].{namespace:namespace, resourceType:resourceType[]}"
```

Du kan också använda följande PowerShell-cmdlet:

```azurepowershell-interactive
Get-AzureRmResourceProvider | select-object ProviderNamespace -ExpandProperty ResourceTypes | ft ProviderNamespace, ResourceTypeName, ApiVersions
```

### <a name="refer-to-resource-locations-with-a-parameter"></a>Referera till resurs platser med en parameter

En mall distribueras alltid till en resurs grupp som finns i en region. Förutom själva distributionen har varje resurs i en mall också en plats egenskap som du använder för att ange den region som ska distribueras i. För att utveckla din mall för moln konsekvens, behöver du ett dynamiskt sätt att referera till resurs platser, eftersom varje Azure Stack kan innehålla unika plats namn. Resurserna distribueras vanligt vis i samma region som resurs gruppen, men för att ge stöd för scenarier som program tillgänglighet över flera regioner kan det vara praktiskt att sprida resurser över flera regioner.

Även om du kan hårdkoda regions namnen när du anger resurs egenskaper i en mall garanterar den här metoden inte att mallen kan distribueras till andra Azure Stack miljöer, eftersom det regions namn som troligen inte finns där.

Om du vill hantera olika regioner lägger du till en indataparameter till mallen med ett standardvärde. Standardvärdet kommer att användas om inget värde anges under distributionen.

Funktionen Template `[resourceGroup()]` returnerar ett objekt som innehåller följande nyckel/värde-par:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

Genom att referera till plats nyckeln för objektet i defaultValue för Indataparametern Azure Resource Manager kommer vid körning att ersätta `[resourceGroup().location]` funktionen mall med namnet på den resurs grupp där mallen distribueras.

```json
"parameters": {
  "location": {
    "type": "string",
    "metadata": {
      "description": "Location the resources will be deployed to."
    },
    "defaultValue": "[resourceGroup().location]"
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2015-06-15",
    "name": "storageaccount1",
    "location": "[parameters('location')]",
    ...
```

Med den här funktionen mall kan du distribuera din mall till alla moln utan att ens känna till region namnen i förväg. Dessutom kan en plats för en speciell resurs i mallen skilja sig från resurs gruppens plats. I det här fallet kan du konfigurera den med hjälp av ytterligare indataparametrar för den aktuella resursen, medan de andra resurserna i samma mall fortfarande använder den ursprungliga Indataporten för platsen.

### <a name="track-versions-using-api-profiles"></a>Spåra versioner med hjälp av API-profiler

Det kan vara mycket svårt att hålla koll på alla tillgängliga resurs leverantörer och relaterade API-versioner som finns i Azure Stack. Vid tidpunkten för skrivning är den senaste API-versionen för **Microsoft. Compute/availabilitySets** i Azure `2018-04-01` , medan den tillgängliga API-versionen som är gemensam för Azure och Azure Stack är `2016-03-30` . Den vanliga API-versionen för **Microsoft. Storage/storageAccounts** som delas mellan alla Azure-och Azure Stack platser är `2016-01-01` , medan den senaste API-versionen i Azure är `2018-02-01` .

Av den anledningen introducerade Resource Manager konceptet med API-profiler för mallar. Utan API-profiler konfigureras varje resurs i en mall med ett- `apiVersion` element som beskriver API-versionen för den aktuella resursen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "metadata": {
          "description": "Location the resources will be deployed to."
      },
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "name": "mystorageaccount",
      "location": "[parameters('location')]",
      "properties": {
        "accountType": "Standard_LRS"
      }
    },
    {
      "type": "Microsoft.Compute/availabilitySets",
      "apiVersion": "2016-03-30",
      "name": "myavailabilityset",
      "location": "[parameters('location')]",
      "properties": {
        "platformFaultDomainCount": 2,
        "platformUpdateDomainCount": 2
      }
    }
  ],
  "outputs": {}
}
```

En API-profil-version fungerar som ett alias för en enskild API-version per resurs typ som är gemensam för Azure och Azure Stack. I stället för att ange en API-version för varje resurs i en mall anger du bara API-profilen i ett nytt rot element med namnet `apiProfile` och utelämnar `apiVersion` elementet för de enskilda resurserna.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "mystorageaccount",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "myavailabilityset",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

API-profilen ser till att API-versionerna är tillgängliga på alla platser, så du behöver inte manuellt verifiera de apiVersions som är tillgängliga på en bestämd plats. För att se till att de API-versioner som din API-profil refererar till finns i en Azure Stacks miljö, måste Azure Stack operatörerna hålla lösningen uppdaterad baserat på principen för support. Om ett system är mer än sex månader inaktuellt anses det vara inkompatibelt och miljön måste uppdateras.

API-profilen är inte ett obligatoriskt element i en mall. Även om du lägger till elementet används det bara för resurser som inget `apiVersion` har angetts för. Det här elementet möjliggör gradvisa ändringar men kräver inte några ändringar i befintliga mallar.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2016-01-01",
            "name": "mystorageaccount",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "myavailabilityset",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

## <a name="check-endpoint-references"></a>Kontrol lera slut punkts referenser

Resurser kan ha referenser till andra tjänster på plattformen. Till exempel kan en offentlig IP-adress tilldelas ett offentligt DNS-namn. Det offentliga molnet, de suveräna molnen och Azure Stack lösningar har sina egna distinkta slut punkts namn rymder. I de flesta fall kräver en resurs bara ett prefix som inmatat i mallen. Under körningen läggs slut punkt svärdet till i Azure Resource Manager. Vissa slut punkts värden måste anges explicit i mallen.

> [!NOTE]
> För att utveckla mallar för moln konsekvens, hårdkoda inte slut punkts namn rymder.

Följande två exempel är vanliga slut punkts namn rymder som måste anges explicit när du skapar en resurs:

* Lagrings konton (BLOB, kö, tabell och fil)
* Anslutnings strängar för databaser och Azure cache för Redis

Slut punkts namn rymder kan också användas i utdata från en mall som information för användaren när distributionen är klar. Följande är vanliga exempel:

* Lagrings konton (BLOB, kö, tabell och fil)
* Anslutnings strängar (MySql, SQLServer, SQLAzure, Custom, NotificationHub, Service Bus, EventHub, ApiHub, DocDb, RedisCache, PostgreSQL)
* Traffic Manager
* domainNameLabel för en offentlig IP-adress
* Molntjänster

Undvik i allmänhet hårdkodad-slutpunkter i en mall. Det bästa sättet är att använda funktionen Reference Template för att hämta slut punkterna dynamiskt. Slut punkten vanligaste hårdkodad är till exempel slut punkts namn området för lagrings konton. Varje lagrings konto har ett unikt fullständigt domän namn som skapas genom att namnet på lagrings kontot med slut punktens namn område sammanfogas. Ett Blob Storage-konto med namnet mystorageaccount1 resulterar i olika FQDN beroende på molnet:

* **mystorageaccount1.blob.Core.Windows.net** när de skapas i det globala Azure-molnet.
* **mystorageaccount1.blob.Core.chinacloudapi.cn** när de skapas i Azure Kina 21Vianet-molnet.

Följande Template-funktion hämtar slut punkts namn området från Storage Resource providern:

```json
"diskUri":"[concat(reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))).primaryEndpoints.blob, 'container/myosdisk.vhd')]"
```

Genom att ersätta hårdkodad-värdet för lagrings konto slut punkten med `reference` funktionen mall kan du använda samma mall för att distribuera till olika miljöer utan att göra några ändringar i slut punkts referensen.

### <a name="refer-to-existing-resources-by-unique-id"></a>Referera till befintliga resurser efter unikt ID

Du kan också referera till en befintlig resurs från samma eller en annan resurs grupp, inom samma prenumeration eller en annan prenumeration, inom samma klient organisation i samma moln. Om du vill hämta resurs egenskaperna måste du använda den unika identifieraren för själva resursen. `resourceId`Funktionen Template hämtar det unika ID: t för en resurs, till exempel SQL Server som följande kod visar:

```json
"outputs": {
  "resourceId":{
    "type": "string",
    "value": "[resourceId('otherResourceGroup', 'Microsoft.Sql/servers', parameters('serverName'))]"
  }
}
```

Du kan sedan använda `resourceId` funktionen inuti funktionen `reference` mall för att hämta egenskaperna för en databas. Returvärdet innehåller den `fullyQualifiedDomainName` egenskap som innehåller det fullständiga slut punkt svärdet. Det här värdet hämtas vid körning och ger det moln miljön-angivna slut punkts namn området. Om du vill definiera anslutnings strängen utan hårdkoda för slut punkts namn området kan du referera till egenskapen för returvärdet direkt i anslutnings strängen som visas:

```json
"[concat('Server=tcp:', reference(resourceId('sql', 'Microsoft.Sql/servers', parameters('test')), '2015-05-01-preview').fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('database'),';User ID=', parameters('username'), ';Password=', parameters('pass'), ';Encrypt=True;')]"
```

## <a name="consider-resource-properties"></a>Ta hänsyn till resurs egenskaper

Specifika resurser i Azure Stack miljöer har unika egenskaper som du måste ta hänsyn till i mallen.

### <a name="ensure-vm-images-are-available"></a>Se till att VM-avbildningar är tillgängliga

Azure innehåller en omfattande uppsättning VM-avbildningar. De här avbildningarna skapas och förbereds för distribution av Microsoft och partner. Bilderna utgör grunden för virtuella datorer på plattformen. En molnbaserad mall bör dock bara referera till tillgängliga parametrar – i synnerhet utgivare, erbjudande och SKU för de VM-avbildningar som är tillgängliga för de globala Azure-, Azure-suveräna molnen eller en Azure Stack lösning.

Om du vill hämta en lista över tillgängliga VM-avbildningar på en plats kör du följande Azure CLI-kommando:

```azurecli-interactive
az vm image list -all
```

Du kan hämta samma lista med Azure PowerShell cmdlet [Get-AzureRmVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) och ange den plats som du vill använda med `-Location` parametern. Ett exempel:

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "West Europe" | Get-AzureRmVMImageOffer | Get-AzureRmVMImageSku | Get-AzureRmVMImage
```

Det här kommandot tar några minuter att returnera alla tillgängliga bilder i regionen Europa, västra i det globala Azure-molnet.

Om du har gjort dessa VM-avbildningar tillgängliga för Azure Stack, används all tillgänglig lagring. För att kunna hantera även den minsta skalnings enheten kan du Azure Stack välja de avbildningar som du vill lägga till i en miljö.

I följande kod exempel visas en konsekvent metod för att referera till parametrarna utgivare, erbjudande och SKU i ARM-mallarna:

```json
"storageProfile": {
    "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2016-Datacenter",
    "version": "latest"
    }
}
```

### <a name="check-local-vm-sizes"></a>Kontrol lera lokala VM-storlekar

För att utveckla din mall för moln konsekvens måste du se till att den virtuella dator storleken som du vill använda är tillgänglig i alla mål miljöer. VM-storlekar är en gruppering av prestanda egenskaper och funktioner. Vissa storlekar på virtuella datorer är beroende av den maskin vara som den virtuella datorn körs på. Om du till exempel vill distribuera en GPU-optimerad virtuell dator måste maskin varan som kör hypervisor-program varan vara GPU: er.

När Microsoft presenterar en ny storlek på den virtuella datorn som har vissa maskin varu beroenden görs VM-storleken vanligt vis tillgänglig i en liten delmängd av regionerna i Azure-molnet. Senare görs det tillgängligt för andra regioner och moln. För att se till att den virtuella dator storleken finns i varje moln som du distribuerar till kan du hämta tillgängliga storlekar med följande Azure CLI-kommando:

```azurecli-interactive
az vm list-sizes --location "West Europe"
```

För Azure PowerShell använder du:

```azurepowershell-interactive
Get-AzureRmVMSize -Location "West Europe"
```

En fullständig lista över tillgängliga tjänster finns i [produkt tillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?cdn=disable).

### <a name="check-use-of-azure-managed-disks-in-azure-stack"></a>Kontrol lera användningen av Azure-Managed Disks i Azure Stack

Managed disks hanterar lagringen för en Azure-klient. I stället för att explicit skapa ett lagrings konto och ange URI: n för en virtuell hård disk (VHD) kan du använda hanterade diskar för att implicit utföra dessa åtgärder när du distribuerar en virtuell dator. Hanterade diskar förbättrar tillgängligheten genom att placera alla diskar från virtuella datorer i samma tillgänglighets uppsättning i olika lagrings enheter. Dessutom kan befintliga virtuella hård diskar konverteras från standard till Premium Storage med betydligt mindre stillestånds tid.

Även om Managed disks finns i översikten för Azure Stack, stöds de inte för närvarande. Innan de är det kan du utveckla moln-konsekventa mallar för Azure Stack genom att uttryckligen ange virtuella hård diskar med hjälp av `vhd` elementet i mallen för den virtuella dator resursen som visas:

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "name": "osdisk",
    "vhd": {
      "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
    },
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

Om du däremot vill ange en hanterad disk konfiguration i en mall tar du bort `vhd` elementet från disk konfigurationen.

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

Samma ändringar använder även [data diskar](../../virtual-machines/using-managed-disks-template-deployments.md).

### <a name="verify-that-vm-extensions-are-available-in-azure-stack"></a>Kontrol lera att VM-tillägg är tillgängliga i Azure Stack

Ett annat övervägande för moln konsekvens är användningen av [virtuella dator tillägg](../../virtual-machines/extensions/features-windows.md) för att konfigurera resurserna inuti en virtuell dator. Alla VM-tillägg är inte tillgängliga i Azure Stack. En mall kan ange de resurser som är reserverade för VM-tillägget och skapa beroenden och villkor inom mallen.

Om du till exempel vill konfigurera en virtuell dator som kör Microsoft SQL Server, kan det virtuella dator tillägget Konfigurera SQL Server som en del av mall distributionen. Överväg vad som händer om distributions mal len också innehåller en program server konfigurerad för att skapa en databas på den virtuella datorn som kör SQL Server. Förutom att även använda ett VM-tillägg för program servrarna kan du konfigurera beroendet av program servern så att den lyckade återlämningen av SQL Server VM tilläggs resursen. Den här metoden säkerställer att den virtuella datorn som kör SQL Server har kon figurer ATS och är tillgänglig när program servern instrueras att skapa databasen.

Med hjälp av den här mallens deklarativ metod kan du definiera slut tillstånd för resurserna och deras beroenden, medan plattformen tar hand om den logik som krävs för beroenden.

#### <a name="check-that-vm-extensions-are-available"></a>Kontrol lera att VM-tillägg är tillgängliga

Det finns många typer av VM-tillägg. När du utvecklar mall för moln konsekvens ska du bara använda de tillägg som är tillgängliga i alla regioner som mal len mål.

Om du vill hämta en lista över de VM-tillägg som är tillgängliga för en speciell region (i det här exemplet `myLocation` ) kör du följande Azure CLI-kommando:

```azurecli-interactive
az vm extension image list --location myLocation
```

Du kan också köra cmdleten Azure PowerShell [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) och använda `-Location` för att ange platsen för den virtuella dator avbildningen. Ett exempel:

```azurepowershell-interactive
Get-AzureRmVmImagePublisher -Location myLocation | Get-AzureRmVMExtensionImageType | Get-AzureRmVMExtensionImage | Select Type, Version
```

#### <a name="ensure-that-versions-are-available"></a>Kontrol lera att det finns tillgängliga versioner

Eftersom VM-tillägg är resurser från första parts Resource Manager har de sina egna API-versioner. Som följande kod visar är typen av VM-tillägg en kapslad resurs i Microsoft. Compute Resource-providern.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "apiVersion": "2015-06-15",
    "name": "myExtension",
    "location": "[parameters('location')]",
    ...
```

API-versionen av resursen för VM-tillägg måste finnas på alla platser som du planerar att använda som mål för din mall. Plats beroendet fungerar som resurs leverantörens API-versions tillgänglighet som beskrivs tidigare i avsnittet "kontrol lera versionen av alla resurs typer".

Om du vill hämta en lista över tillgängliga API-versioner för VM-tillägget använder du cmdleten [Get-AzureRmResourceProvider](/powershell/module/az.resources/get-azresourceprovider) med **Microsoft. Compute** Resource-providern som visas:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachines/extensions"}
```

Du kan också använda VM-tillägg i skalnings uppsättningar för virtuella datorer. Samma plats villkor gäller. Om du vill utveckla din mall för moln konsekvens kontrollerar du att API-versionerna är tillgängliga på alla platser som du planerar att distribuera till. Om du vill hämta API-versionerna av resursen för VM-tillägg för skalnings uppsättningar använder du samma cmdlet som förut, men anger resurs typen virtuell dator skalnings uppsättningar som visas:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachineScaleSets/extensions"}
```

Varje enskilt tillägg är också en version. Den här versionen visas i `typeHandlerVersion` egenskapen för VM-tillägget. Kontrol lera att versionen som anges i `typeHandlerVersion` elementet i mallens VM-tillägg är tillgängliga på de platser där du planerar att distribuera mallen. Följande kod anger till exempel version 1,7:

```json
{
    "type": "extensions",
    "apiVersion": "2016-03-30",
    "name": "MyCustomScriptExtension",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
    ],
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        ...
```

Om du vill hämta en lista över tillgängliga versioner för ett angivet VM-tillägg använder du cmdleten [Get-AzureRmVMExtensionImage](/powershell/module/az.compute/get-azvmextensionimage) . I följande exempel hämtas tillgängliga versioner för PowerShell DSC (Desired State Configuration) VM-tillägg från min **plats**:

```azurepowershell-interactive
Get-AzureRmVMExtensionImage -Location myLocation -PublisherName Microsoft.PowerShell -Type DSC | FT
```

Använd kommandot [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) för att hämta en lista över utgivare. Använd kommandot [Get-AzureRmVMExtensionImageType](/powershell/module/az.compute/get-azvmextensionimagetype) commend för att begära typ.

## <a name="tips-for-testing-and-automation"></a>Tips för testning och automatisering

Det är en utmaning att hålla reda på alla relaterade inställningar, funktioner och begränsningar när du redigerar en mall. Det vanliga tillvägagångs sättet är att utveckla och testa mallar mot ett enda moln innan andra platser är riktade. Men det tidigare testet utförs i redigerings processen, men det är mindre fel sökning och kod som skriver om utvecklings teamet. Distributioner som inte fungerar på grund av plats beroenden kan vara tids krävande att felsöka. Därför rekommenderar vi automatisk testning så tidigt som möjligt i redigerings cykeln. I slut ändan behöver du mindre utvecklings tid och färre resurser och dina moln enhetliga artefakter blir ännu mer värdefulla.

Följande bild visar ett typiskt exempel på en utvecklings process för ett team som använder en Integrated Development Environment (IDE). På olika steg i tids linjen körs olika test typer. Här arbetar två utvecklare med samma lösning, men det här scenariot gäller även för en enda utvecklare eller ett stort team. Varje utvecklare skapar vanligt vis en lokal kopia av en central lagrings plats, vilket gör att var och en kan arbeta med den lokala kopian utan att påverka de andra som kanske arbetar på samma filer.

![Arbetsflöde](./media/templates-cloud-consistency/workflow.png)

Tänk på följande tips för testning och automatisering:

* Använder test verktyg. Visual Studio Code och Visual Studio innehåller t. ex. IntelliSense och andra funktioner som kan hjälpa dig att validera dina mallar.
* Förbättra kod kvaliteten under utvecklingen på den lokala IDE-enheten genom att utföra statisk kod analys med enhets test och integrations test.
* För en ännu bättre upplevelse under den första utvecklingen bör enhets test och integrations test bara varna när ett problem upptäcks och fortsätter med testerna. På så sätt kan du identifiera de problem som ska åtgärdas och prioritera ordningen på ändringarna, även kallade test driven distribution (TDD).
* Tänk på att vissa tester kan utföras utan att vara anslutna till Azure Resource Manager. Andra, som testning av mall distribution, kräver Resource Manager för att utföra vissa åtgärder som inte kan utföras offline.
* Att testa en distributions mall mot verifierings-API: t är inte lika med en faktisk distribution. Även om du distribuerar en mall från en lokal fil hämtas alla referenser till kapslade mallar i mallen av Resource Manager direkt och artefakter som refereras till av VM-tillägg hämtas av den virtuella dator agenten som körs i den distribuerade virtuella datorn.

## <a name="next-steps"></a>Nästa steg

* [Överväganden för Azure Resource Manager mallar](/azure-stack/user/azure-stack-develop-templates)
* [Metod tips för ARM-mallar](template-syntax.md)
