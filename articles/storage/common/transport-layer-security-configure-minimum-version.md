---
title: Framtvinga minst en nödvändig version av Transport Layer Security (TLS) för inkommande begär Anden
titleSuffix: Azure Storage
description: Konfigurera ett lagrings konto för att kräva en lägsta version av Transport Layer Security (TLS) för klienter som begär förfrågningar mot Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: e5ab583330b46b8f53223500076aa04780e6deac
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108729"
---
# <a name="enforce-a-minimum-required-version-of-transport-layer-security-tls-for-requests-to-a-storage-account"></a>Framtvinga en minsta version av Transport Layer Security (TLS) som krävs för begär anden till ett lagrings konto

Kommunikation mellan ett klient program och ett Azure Storage-konto krypteras med hjälp av Transport Layer Security (TLS). TLS är ett standardiserat kryptografiskt protokoll som garanterar integritet och data integritet mellan klienter och tjänster via Internet. Mer information om TLS finns i [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security).

Azure Storage stöder för närvarande tre versioner av TLS-protokollet: 1,0, 1,1 och 1,2. Azure Storage använder TLS 1,2 på offentliga HTTPS-slutpunkter, men TLS 1,0 och TLS 1,1 stöds fortfarande för bakåtkompatibilitet.

Som standard tillåter Azure Storage-konton att klienter skickar och tar emot data med den äldsta versionen av TLS, TLS 1,0 och senare. Om du vill använda strängare säkerhets åtgärder kan du konfigurera ditt lagrings konto för att kräva att klienter skickar och tar emot data med en nyare version av TLS. Om ett lagrings konto kräver en lägsta-version av TLS, kommer alla begär Anden som görs med en äldre version att Miss sen.

Den här artikeln beskriver hur du använder ett ramverk för att dra (identifierings-och gransknings styrning) för att kontinuerligt hantera säker TLS för dina lagrings konton.

Information om hur du anger en viss version av TLS när du skickar en begäran från ett klient program finns i [konfigurera Transport Layer Security (TLS) för ett klient program](transport-layer-security-configure-client-version.md).

## <a name="detect-the-tls-version-used-by-client-applications"></a>Identifiera den TLS-version som används av klient program

När du tillämpar en lägsta TLS-version för ditt lagrings konto riskerar du att avvisa begär Anden från klienter som skickar data med en äldre version av TLS. För att förstå hur konfigurering av den lägsta TLS-versionen kan påverka klient program, rekommenderar Microsoft att du aktiverar loggning för ditt Azure Storage-konto och analyserar loggarna efter ett tidsintervall för att identifiera vilka versioner av TLS-klientprogrammet som används.

Om du vill logga förfrågningar till ditt Azure Storage-konto och fastställa vilken TLS-version som används av klienten, kan du använda Azure Storage inloggning Azure Monitor (för hands version). Mer information finns i [övervaka Azure Storage](../blobs/monitor-blob-storage.md).

Azure Storage loggning i Azure Monitor har stöd för att använda logg frågor för att analysera loggdata. Om du vill söka i loggar kan du använda en Azure Log Analytics-arbetsyta. Mer information om logg frågor finns i [Självstudier: komma igång med Log Analytics frågor](../../azure-monitor/log-query/log-analytics-tutorial.md).

Om du vill logga Azure Storage data med Azure Monitor och analysera dem med Azure Log Analytics måste du först skapa en diagnostisk inställning som anger vilka typer av begär Anden och för vilka lagrings tjänster du vill logga data. Azure Storage loggar i Azure Monitor finns i offentlig för hands version och är tillgänglig för för hands testning i alla offentliga moln regioner. Den här för hands versionen aktiverar loggar för blobbar (inklusive Azure Data Lake Storage Gen2), filer, köer och tabeller. Följ dessa steg om du vill skapa en diagnostisk inställning i Azure Portal:

1. Skapa en ny Log Analytics-arbetsyta i prenumerationen som innehåller ditt Azure Storage-konto. När du har konfigurerat loggning för ditt lagrings konto är loggarna tillgängliga i Log Analytics arbets ytan. Mer information finns i [skapa en Log Analytics arbets yta i Azure Portal](../../azure-monitor/learn/quick-create-workspace.md).
1. Navigera till ditt lagringskonto i Azure-portalen.
1. I avsnittet övervakning väljer du **diagnostikinställningar (för hands version)**.
1. Välj den Azure Storage tjänst som du vill logga förfrågningar för. Välj till exempel **BLOB** för att logga förfrågningar till Blob Storage.
1. Välj **Lägg till diagnostikinställning**.
1. Ange ett namn för den diagnostiska inställningen.
1. Under **kategori information** i avsnittet **logg** väljer du vilka typer av begär Anden som ska loggas. Du kan logga läsnings-, skriv-och borttagnings begär Anden. Om du till exempel väljer **StorageRead** och **StorageWrite** loggas Läs-och skriv förfrågningar till den valda tjänsten.
1. Under **mål information** väljer **du skicka till Log Analytics**. Välj din prenumeration och Log Analytics arbets ytan som du skapade tidigare, som du ser i följande bild.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/create-diagnostic-setting-logs.png" alt-text="Skärm bild som visar hur du skapar en diagnostisk inställning för loggnings begär Anden":::

Efter att du har skapat den diagnostiska inställningen loggas begär anden till lagrings kontot sedan i enlighet med den inställningen. Mer information finns i [skapa diagnostisk inställning för insamling av resurs loggar och mått i Azure](../../azure-monitor/platform/diagnostic-settings.md).

En referens för fält som är tillgängliga i Azure Storage loggar i Azure Monitor finns i [resurs loggar (för hands version)](../blobs/monitor-blob-storage-reference.md#resource-logs-preview).

### <a name="query-logged-requests-by-tls-version"></a>Fråga loggade förfrågningar per TLS-version

Azure Storage loggar i Azure Monitor inkluderar den TLS-version som används för att skicka en begäran till ett lagrings konto. Använd egenskapen **TlsVersion** för att kontrol lera TLS-versionen av en loggad begäran.

För att avgöra hur många begär Anden som gjorts mot Blob Storage med olika versioner av TLS under de senaste sju dagarna, öppnar du Log Analytics arbets ytan. Klistra sedan in följande fråga i en ny logg fråga och kör den. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>"
| summarize count() by TlsVersion
```

Resultaten visar antalet begär Anden som gjorts med varje version av TLS:

:::image type="content" source="media/transport-layer-security-configure-minimum-version/log-analytics-query-version.png" alt-text="Skärm bild som visar resultatet av Log Analytics-frågan för att returnera TLS-version":::

### <a name="query-logged-requests-by-caller-ip-address-and-user-agent-header"></a>Fråga loggade begär Anden efter uppringande IP-adress och användar agent huvud

Azure Storage loggar i Azure Monitor inkluderar även anropare IP-adress och användar agent huvud som hjälper dig att utvärdera vilka klient program som har åtkomst till lagrings kontot. Du kan analysera dessa värden för att bestämma om klient program måste uppdateras för att använda en nyare version av TLS eller om det är acceptabelt att klienten inte skickas med den lägsta TLS-versionen.

För att avgöra vilka klienter som har gjort begär Anden med en version av TLS som är äldre än TLS 1,2 under de senaste sju dagarna klistrar du in följande fråga i en ny logg fråga och kör den. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>" and TlsVersion != "TLS 1.2"
| project TlsVersion, CallerIpAddress, UserAgentHeader
```

## <a name="remediate-security-risks-with-a-minimum-version-of-tls"></a>Åtgärda säkerhets risker med en lägsta version av TLS

När du är säker på att trafik från klienter som använder äldre versioner av TLS är minimal, eller om det är acceptabelt att misslyckade begär Anden som görs med en äldre version av TLS, kan du börja verkställa en lägsta TLS-version på ditt lagrings konto. Att kräva att klienter använder en lägsta version av TLS för att göra förfrågningar mot ett lagrings konto är en del av en strategi för att minimera säkerhets riskerna för dina data.

> [!IMPORTANT]
> Om du använder en tjänst som ansluter till Azure Storage, se till att tjänsten använder rätt version av TLS för att skicka begär anden till Azure Storage innan du anger den lägsta version som krävs för ett lagrings konto.

### <a name="configure-the-minimum-tls-version-for-a-storage-account"></a>Konfigurera den lägsta TLS-versionen för ett lagrings konto

Om du vill konfigurera den lägsta TLS-versionen för ett lagrings konto anger du **MinimumTlsVersion** -versionen för kontot. Den här egenskapen är tillgänglig för alla lagrings konton som skapas med Azure Resource Manager distributions modell. Mer information om distributions modellen för Azure Resource Manager finns i [Översikt över lagrings konto](storage-account-overview.md).

Egenskapen **MinimumTlsVersion** har inte angetts som standard och returnerar inte något värde förrän du uttryckligen anger det.  Om egenskap svärdet är **Null** tillåter lagrings kontot begär Anden som skickas med TLS version 1,0 eller senare.

# <a name="portal"></a>[Portal](#tab/portal)

När du skapar ett lagrings konto med Azure Portal är den minsta TLS-versionen inställd på 1,2 som standard.

Följ dessa steg om du vill konfigurera den lägsta TLS-versionen för ett befintligt lagrings konto med Azure Portal:

1. Navigera till ditt lagringskonto i Azure-portalen.
1. Välj **konfigurations** inställningen.
1. Under **lägsta TLS-version** använder du List rutan för att välja den lägsta version av TLS som krävs för att komma åt data i det här lagrings kontot, som du ser i följande bild.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png" alt-text="Skärm bild som visar hur du konfigurerar den lägsta versionen av TLS i Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill konfigurera den lägsta TLS-versionen för ett lagrings konto med PowerShell installerar du [Azure PowerShell version 4.4.0](https://www.powershellgallery.com/packages/Az/4.4.0) eller senare. Konfigurera sedan egenskapen **MinimumTLSVersion** för ett nytt eller befintligt lagrings konto. Giltiga värden för **MinimumTlsVersion** är `TLS1_0` , `TLS1_1` och `TLS1_2` .

I följande exempel skapas ett lagrings konto som anger **MinimumTLSVersion** till TLS 1,1, uppdaterar kontot och anger **MinimumTLSVersion** till TLS 1,2. I exemplet hämtas även egenskap svärdet i varje fall. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account with MinimumTlsVersion set to TLS 1.1.
New-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -Location $location `
    -SkuName Standard_GRS `
    -MinimumTlsVersion TLS1_1

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion

# Update the MinimumTlsVersion version for the storage account to TLS 1.2.
Set-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -MinimumTlsVersion TLS1_2

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill konfigurera den lägsta TLS-versionen för ett lagrings konto med Azure CLI installerar du Azure CLI version 2.9.0 eller senare. Mer information finns i [Installera Azure CLI](/cli/azure/install-azure-cli). Konfigurera sedan egenskapen **minimumTlsVersion** för ett nytt eller befintligt lagrings konto. Giltiga värden för **minimumTlsVersion** är `TLS1_0` , `TLS1_1` och `TLS1_2` .

I följande exempel skapas ett lagrings konto och **minimumTLSVersion** anges till TLS 1,1. Sedan uppdateras kontot och egenskapen **minimumTLSVersion** anges till TLS 1,2. I exemplet hämtas även egenskap svärdet i varje fall. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --kind StorageV2 \
    --location <location> \
    --min-tls-version TLS1_1

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv

az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --min-tls-version TLS1_2

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv
```

# <a name="template"></a>[Mall](#tab/template)

Om du vill konfigurera den lägsta TLS-versionen för ett lagrings konto med en mall skapar du en mall med egenskapen **MinimumTLSVersion** inställd på `TLS1_0` , `TLS1_1` eller `TLS1_2` . Följande steg beskriver hur du skapar en mall i Azure Portal.

1. I Azure Portal väljer du **skapa en resurs**.
1. I **Sök på Marketplace** skriver du **mall distribution** och trycker sedan på **RETUR**.
1. Välj **malldistribution (distribuera med anpassade mallar) (för hands version)**, Välj **skapa** och välj sedan **skapa en egen mall i redigeraren**.
1. I redigeraren för mallar klistrar du in följande JSON för att skapa ett nytt konto och anger den lägsta TLS-versionen till TLS 1,2. Kom ihåg att ersätta plats hållarna inom vinkelparenteser med dina egna värden.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {
            "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'tls')]"
        },
        "resources": [
            {
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "<location>",
            "properties": {
                "minimumTlsVersion": "TLS1_2"
            },
            "dependsOn": [],
            "sku": {
              "name": "Standard_GRS"
            },
            "kind": "StorageV2",
            "tags": {}
            }
        ]
    }
    ```

1. Spara mallen.
1. Ange parametern resurs grupp och välj sedan knappen **Granska + skapa** för att distribuera mallen och skapa ett lagrings konto med egenskapen **MinimumTLSVersion** konfigurerad.

---

> [!NOTE]
> När du har uppdaterat den lägsta TLS-versionen för lagrings kontot kan det ta upp till 30 sekunder innan ändringen har spridits helt.

För att konfigurera den lägsta TLS-versionen krävs version 2019-04-01 eller senare av Azure Storage Resource Provider. Mer information finns i [Azure Storage Resource Provider REST API](/rest/api/storagerp/).

### <a name="check-the-minimum-required-tls-version-for-multiple-accounts"></a>Kontrol lera den minsta TLS-version som krävs för flera konton

Om du vill kontrol lera den minsta TLS-version som krävs för en uppsättning lagrings konton med optimala prestanda kan du använda Azure Resource Graph Explorer i Azure Portal. Mer information om hur du använder resurs diagram Utforskaren finns i [snabb start: kör din första resurs diagram fråga med Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md).

Om du kör följande fråga i resursens diagram Utforskaren returneras en lista över lagrings konton och den lägsta TLS-versionen för varje konto visas:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend minimumTlsVersion = parse_json(properties).minimumTlsVersion
| project subscriptionId, resourceGroup, name, minimumTlsVersion
```

### <a name="test-the-minimum-tls-version-from-a-client"></a>Testa den lägsta TLS-versionen från en klient

Om du vill testa att den minsta obligatoriska TLS-versionen för ett lagrings konto tillåter att anrop görs med en äldre version kan du konfigurera en klient så att den använder en äldre version av TLS. Mer information om hur du konfigurerar en klient för att använda en specifik version av TLS finns i [konfigurera Transport Layer Security (TLS) för ett klient program](transport-layer-security-configure-client-version.md).

När en klient ansluter till ett lagrings konto med hjälp av en TLS-version som inte uppfyller den lägsta TLS-version som kon figurer ATS för kontot, returnerar Azure Storage felkod 400 fel (felaktig begäran) och ett meddelande som anger att den TLS-version som användes inte tillåts för att begära förfrågningar mot det här lagrings kontot.

## <a name="use-azure-policy-to-audit-for-compliance"></a>Använda Azure Policy för att granska kompatibilitet

Om du har ett stort antal lagrings konton kanske du vill utföra en granskning för att se till att alla konton är konfigurerade för den lägsta version av TLS som din organisation behöver. Om du vill granska en uppsättning lagrings konton för deras kompatibilitet använder du Azure Policy. Azure Policy är en tjänst som du kan använda för att skapa, tilldela och hantera principer som tillämpar regler på Azure-resurser. Azure Policy hjälper dig att hålla resurserna kompatibla med företagets standarder och service nivå avtal. Mer information finns i [Översikt över Azure policy](../../governance/policy/overview.md).

### <a name="create-a-policy-with-an-audit-effect"></a>Skapa en princip med en gransknings funktion

Azure Policy stöder effekter som avgör vad som händer när en princip regel utvärderas mot en resurs. Gransknings funktionen skapar en varning när en resurs inte är kompatibel, men den stoppar inte begäran. Mer information om effekter finns i [förstå Azure policys effekter](../../governance/policy/concepts/effects.md).

Följ dessa steg om du vill skapa en princip med en gransknings funktion för den lägsta TLS-versionen med Azure Portal:

1. I Azure Portal navigerar du till tjänsten Azure Policy.
1. Under avsnittet **redigering** väljer du **definitioner**.
1. Välj **Lägg till princip definition** för att skapa en ny princip definition.
1. I fältet **definitions plats** väljer du knappen **mer** för att ange var gransknings princip resursen finns.
1. Ange ett namn för principen. Du kan också ange en beskrivning och kategori.
1. Under **princip regel** lägger du till följande princip definition i **policyRule** -avsnittet.

    ```json
    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "field":"Microsoft.Storage/storageAccounts/minimumTlsVersion",
              "equals": "TLS1_2"
            }
          }
        ]
      },
      "then": {
        "effect": "audit"
      }
    }
    ```

1. Spara principen.

### <a name="assign-the-policy"></a>Tilldela principen

Tilldela sedan principen till en resurs. Principens omfattning motsvarar resursen och eventuella resurser under den. Mer information om princip tilldelning finns i [Azure policy tilldelnings struktur](../../governance/policy/concepts/assignment-structure.md).

Följ dessa steg om du vill tilldela principen till Azure Portal:

1. I Azure Portal navigerar du till tjänsten Azure Policy.
1. Under avsnittet **redigering** väljer du **tilldelningar**.
1. Välj **tilldela princip** om du vill skapa en ny princip tilldelning.
1. I fältet **omfattning** väljer du omfånget för princip tilldelningen.
1. I fältet **princip definition** väljer du knappen **mer** och väljer sedan den princip som du definierade i föregående avsnitt i listan.
1. Ange ett namn för princip tilldelningen. Beskrivningen är valfri.
1. Aktivera **tvingande princip** uppsättning till *aktive rad*. Den här inställningen har ingen inverkan på gransknings principen.
1. Välj **Granska + skapa** för att skapa tilldelningen.

### <a name="view-compliance-report"></a>Visa Kompatibilitetsrapport

När du har tilldelat principen kan du Visa rapporten efterlevnad. Kompatibilitetsrapport för en gransknings princip innehåller information om vilka lagrings konton som inte är kompatibla med principen. Mer information finns i [Hämta efterlevnadsprincip](../../governance/policy/how-to/get-compliance-data.md)för information.

Det kan ta flera minuter för rapporten att bli tillgänglig när princip tilldelningen har skapats.

Följ dessa steg om du vill visa Kompatibilitetsrapport i Azure Portal:

1. I Azure Portal navigerar du till tjänsten Azure Policy.
1. Välj **efterlevnad**.
1. Filtrera resultaten för namnet på princip tilldelningen som du skapade i föregående steg. Rapporten visar hur många resurser som inte är kompatibla med principen.
1. Du kan öka detalj nivån i rapporten för ytterligare information, inklusive en lista över lagrings konton som inte är kompatibla.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/compliance-report-policy-portal.png" alt-text="Skärm bild som visar Kompatibilitetsrapport för en gransknings princip för lägsta TLS-version":::

## <a name="use-azure-policy-to-enforce-the-minimum-tls-version"></a>Använd Azure Policy för att framtvinga den lägsta TLS-versionen

Azure Policy stöder moln styrning genom att se till att Azure-resurserna följer krav och standarder. Om du vill framtvinga en minimi krav på TLS-version för lagrings kontona i din organisation kan du skapa en princip som förhindrar att ett nytt lagrings konto skapas som anger det lägsta TLS-kravet till en äldre version av TLS än den som styrs av principen. Den här principen förhindrar även konfigurations ändringar till ett befintligt konto om den minsta inställningen för TLS-versionen för kontot inte är kompatibel med principen.

Den tvingande principen använder funktionen neka för att förhindra en begäran som skulle skapa eller ändra ett lagrings konto så att den lägsta TLS-versionen inte längre följer organisationens standarder. Mer information om effekter finns i [förstå Azure policys effekter](../../governance/policy/concepts/effects.md).

Om du vill skapa en princip med en neka-påverkan för en lägsta TLS-version som är mindre än TLS 1,2 följer du samma steg som beskrivs i [använda Azure policy för att granska kompatibilitet](#use-azure-policy-to-audit-for-compliance), men ange följande JSON i avsnittet **policyRule** i princip definitionen:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field":"Microsoft.Storage/storageAccounts/minimumTlsVersion",
          "equals": "TLS1_2"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

När du har skapat principen med neka-resultatet och tilldelar den till ett omfång kan en användare inte skapa ett lagrings konto med en lägsta TLS-version som är äldre än 1,2. Du kan inte heller göra några konfigurations ändringar i ett befintligt lagrings konto som för närvarande kräver en lägsta TLS-version som är äldre än 1,2. Om du försöker göra det uppstår ett fel. Den minsta TLS-version som krävs för lagrings kontot måste anges till 1,2 för att det ska gå att skapa eller konfigurera kontot.

Följande bild visar felet som uppstår om du försöker skapa ett lagrings konto med den lägsta TLS-versionen inställd på TLS 1,0 (standard för ett nytt konto) när en princip med en neka-inverkan kräver att den lägsta TLS-versionen anges till TLS 1,2.

:::image type="content" source="media/transport-layer-security-configure-minimum-version/deny-policy-error.png" alt-text="Skärm bild som visar felet som inträffar när du skapar ett lagrings konto som strider mot principen":::

## <a name="permissions-necessary-to-require-a-minimum-version-of-tls"></a>Behörigheter som krävs för att kräva en lägsta version av TLS

Om du vill ange egenskapen **MinimumTlsVersion** för lagrings kontot måste en användare ha behörighet att skapa och hantera lagrings konton. Azure-rollbaserad åtkomst kontroll (Azure RBAC) roller som tillhandahåller dessa behörigheter innefattar åtgärden **Microsoft. Storage/storageAccounts/Write** eller **Microsoft. Storage/storageAccounts/ \** _. Inbyggda roller med den här åtgärden är:

- Rollen Azure Resource Manager [ägare](../../role-based-access-control/built-in-roles.md#owner)
- Rollen Azure Resource Manager [Contributor](../../role-based-access-control/built-in-roles.md#contributor)
- Rollen [lagrings konto deltagare](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Dessa roller ger inte åtkomst till data i ett lagrings konto via Azure Active Directory (Azure AD). De inkluderar dock * Microsoft. Storage/storageAccounts/listnycklar/Action * *, som ger åtkomst till kontots åtkomst nycklar. Med den här behörigheten kan en användare använda kontots åtkomst nycklar för att komma åt alla data i ett lagrings konto.

Roll tilldelningar måste begränsas till lagrings kontots nivå eller högre för att en användare ska kunna kräva en lägsta TLS-version för lagrings kontot. Mer information om roll omfattning finns i [förstå omfattning för Azure RBAC](../../role-based-access-control/scope-overview.md).

Var noga med att begränsa tilldelningen av rollerna till de som kräver möjlighet att skapa ett lagrings konto eller uppdatera dess egenskaper. Använd principen för minsta behörighet för att se till att användarna har minst de behörigheter som de behöver för att utföra sina uppgifter. Mer information om hur du hanterar åtkomst med Azure RBAC finns i [metod tips för Azure RBAC](../../role-based-access-control/best-practices.md).

> [!NOTE]
> Administratören för rollen administratör för den klassiska prenumerationen och Co-Administrator innehåller motsvarigheten till Azure Resource Manager [Owner](../../role-based-access-control/built-in-roles.md#owner) -rollen. **Ägar** rollen innehåller alla åtgärder, så en användare med någon av dessa administrativa roller kan också skapa och hantera lagrings konton. Mer information finns i [klassiska prenumerationer på administratörs roller, Azure-roller och Azure AD-administratörer](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

## <a name="network-considerations"></a>Nätverksöverväganden

När en klient skickar en begäran till ett lagrings konto upprättar klienten en anslutning med den offentliga slut punkten för lagrings kontot först innan begär Anden bearbetas. Inställningen för minsta TLS-version kontrol leras när anslutningen har upprättats. Om begäran använder en tidigare version av TLS än den som anges i inställningen fortsätter anslutningen att lyckas, men begäran kommer att Miss lyckas. Mer information om offentliga slut punkter för Azure Storage finns i [resurs-URI-syntax](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#resource-uri-syntax).

## <a name="next-steps"></a>Nästa steg

- [Konfigurera Transport Layer Security (TLS) för ett klient program](transport-layer-security-configure-client-version.md)
- [Säkerhets rekommendationer för Blob Storage](../blobs/security-recommendations.md)
