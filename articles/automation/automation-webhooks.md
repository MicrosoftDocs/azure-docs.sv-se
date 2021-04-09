---
title: Starta en Azure Automation Runbook från en webhook
description: Den här artikeln beskriver hur du använder en webhook för att starta en Runbook i Azure Automation från ett HTTP-anrop.
services: automation
ms.subservice: process-automation
ms.date: 03/18/2021
ms.topic: conceptual
ms.openlocfilehash: c46a8753c87e981d9e3d6ecdd698bbbe6cba9894
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775790"
---
# <a name="start-a-runbook-from-a-webhook"></a>Starta ett Runbook-flöde från en webhook

Med en webhook kan en extern tjänst starta en viss Runbook i Azure Automation via en enda HTTP-begäran. Externa tjänster omfattar Azure DevOps Services, GitHub, Azure Monitor loggar och anpassade program. En sådan tjänst kan använda en webhook för att starta en Runbook utan att implementera fullständig Azure Automation-API. Du kan jämföra Webhooks med andra metoder för att starta en runbook när du [startar en Runbook i Azure Automation](./start-runbooks.md).

> [!NOTE]
> Det finns inte stöd för att använda en webhook för att starta en python-Runbook.

![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Information om klient krav för TLS 1,2 med Webhooks finns i [TLS 1,2 Enforcement för Azure Automation](automation-managing-data.md#tls-12-enforcement-for-azure-automation).

## <a name="webhook-properties"></a>Webhook-egenskaper

I följande tabell beskrivs de egenskaper som du måste konfigurera för en webhook.

| Egenskap | Beskrivning |
|:--- |:--- |
| Name |Webhookens namn. Du kan ange vilket namn du vill, eftersom det inte visas för klienten. Den används bara för att identifiera runbooken i Azure Automation. Vi rekommenderar att du ger webhooken ett namn som är relaterat till klienten som använder den. |
| URL |Webhookens URL. Detta är den unika adress som en klient anropar med ett HTTP-inlägg för att starta den Runbook som är länkad till webhooken. Den skapas automatiskt när du skapar webhooken. Du kan inte ange en anpassad URL. <br> <br> URL: en innehåller en säkerhetstoken som gör det möjligt för ett system från tredje part att anropa runbooken utan ytterligare autentisering. Därför bör du behandla URL: en som ett lösen ord. Av säkerhets skäl kan du bara Visa webb adressen i Azure Portal när du skapar webhooken. Notera URL: en på en säker plats för framtida användning. |
| Förfallodatum | Utgångs datum för webhooken, efter vilken den inte längre kan användas. Du kan ändra utgångs datumet efter att webhooken har skapats, så länge webhooken inte har upphört att gälla. |
| Enabled | Anger om webhooken är aktive rad som standard när den skapas. Om du ställer in den här egenskapen på inaktive rad kan inte klienten använda webhooken. Du kan ange den här egenskapen när du skapar webhooken eller någon annan gång när den har skapats. |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Parametrar som används när webhooken startar en Runbook

En webhook kan definiera värden för Runbook-parametrar som används när Runbook startas. Webhooken måste innehålla värden för alla obligatoriska Runbook-parametrar och kan innehålla värden för valfria parametrar. Ett parameter värde som har kon figurer ATS för en webhook kan ändras även när webhook har skapats. Flera webhook-länkar till en enda Runbook kan båda använda olika värden för Runbook-parametrar. När en klient startar en Runbook med en webhook kan den inte åsidosätta de parameter värden som definierats i webhooken.

För att ta emot data från klienten stöder runbooken en enda parameter som kallas `WebhookData` . Den här parametern definierar ett objekt som innehåller data som klienten inkluderar i en POST-begäran.

![Egenskaper för WebhookData](media/automation-webhooks/webhook-data-properties.png)

`WebhookData`Parametern har följande egenskaper:

| Egenskap | Beskrivning |
|:--- |:--- |
| `WebhookName` | Webhookens namn. |
| `RequestHeader` | Hash-tabellen som innehåller rubrikerna för inkommande POST-begäran. |
| `RequestBody` | Brödtext i inkommande POST-begäran. Den här texten behåller all dataformatering, till exempel String, JSON, XML eller form-kodad. Runbooken måste skrivas för att fungera med det data format som förväntas. |

Det krävs ingen konfiguration av webhook för att stödja `WebhookData` parametern, och runbooken behöver inte godkänna den. Om runbooken inte definierar parametern ignoreras all information om begäran som skickas från klienten.

> [!NOTE]
> När du anropar en webhook bör klienten alltid lagra alla parameter värden, om anropet Miss lyckas. Om det uppstår ett nätverks avbrott eller anslutnings problem kan programmet inte hämta misslyckade webhook-anrop.

Om du anger ett värde för `WebhookData` när en webhook skapas, åsidosätts det när webhooken startar runbooken med data från klient post förfrågan. Detta inträffar även om programmet inte innehåller några data i begär ande texten. 

Om du startar en Runbook som definierar `WebhookData` en annan metod än en webhook kan du ange ett värde för Runbook- `WebhookData` flödet. Det här värdet ska vara ett objekt med samma [Egenskaper](#webhook-properties) som `WebhookData` parametern så att runbooken kan arbeta med det precis som det fungerar med faktiska objekt som `WebhookData` skickas av en webhook.

Om du till exempel startar följande Runbook från Azure Portal och vill skicka några exempel på webhook-data för testning måste du skicka data i JSON i användar gränssnittet.

![WebhookData-parameter från UI](media/automation-webhooks/WebhookData-parameter-from-UI.png)

I nästa Runbook-exempel definierar vi följande egenskaper för `WebhookData` :

* **WebhookName**: MyWebhook
* **RequestBody**: `*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

Nu skickar vi följande JSON-objekt i användar gränssnittet för- `WebhookData` parametern. Det här exemplet, med vagn returer och rad matnings tecken, matchar formatet som skickas från en webhook.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Starta WebhookData-parametern från användar gränssnittet](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Azure Automation loggar värdena för alla indataparametrar med Runbook-jobbet. Alla indata som tillhandahålls av klienten i webhook-begäran loggas och är tillgängliga för alla som har åtkomst till Automation-jobbet. Av den anledningen bör du vara försiktig med att inkludera känslig information i webhook-anrop.

## <a name="webhook-security"></a>Webhook-säkerhet

Säkerheten för en webhook är beroende av sekretessen för dess URL, som innehåller en säkerhetstoken som gör att webhooken kan anropas. Azure Automation utför ingen autentisering för en begäran så länge den har gjorts till rätt URL. Därför bör dina klienter inte använda Webhooks för Runbooks som utför mycket känsliga åtgärder utan att använda alternativa metoder för att verifiera begäran.

Tänk på följande strategier:

* Du kan inkludera logik i en Runbook för att avgöra om den anropas av en webhook. Ange att runbooken ska kontrol lera `WebhookName` egenskapen för `WebhookData` parametern. Runbooken kan utföra ytterligare verifiering genom att söka efter viss information i `RequestHeader` egenskaperna och `RequestBody` .

* Låt runbooken utföra en validering av ett externt villkor när den får en webhook-begäran. Anta till exempel att en Runbook som anropas av GitHub när som helst är en ny incheckning av en GitHub-lagringsplats. Runbooken kan ansluta till GitHub för att kontrol lera att ett nytt genomförande har skett innan du fortsätter.

* Azure Automation stöder Azure Virtual Network Service-taggar, särskilt [GuestAndHybridManagement](../virtual-network/service-tags-overview.md). Du kan använda service märken för att definiera nätverks åtkomst kontroller för [nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md#security-rules) eller [Azure-brandväggen](../firewall/service-tags.md) och utlösa Webhooks från det virtuella nätverket. Service märken kan användas i stället för vissa IP-adresser när du skapar säkerhets regler. Genom att ange service tag-namnet **GuestAndHybridManagement**  i rätt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för Automation-tjänsten. Den här Service tag-koden har inte stöd för att tillåta mer detaljerad kontroll genom att begränsa IP-intervallen till en speciell region.

## <a name="create-a-webhook"></a>Skapa en webhook

Använd följande procedur för att skapa en ny webhook som är länkad till en Runbook i Azure Portal.

1. På sidan Runbooks i Azure Portal klickar du på den Runbook som webhooken startar för att Visa Runbook-informationen. Se till att fältet Runbook- **status** är inställt på **publicerat**.
2. Klicka på **webhook** överst på sidan för att öppna sidan Lägg till webhook.
3. Klicka på **Skapa ny webhook** för att öppna sidan Skapa webhook.
4. Fyll i fälten **namn** och **förfallo datum** för webhooken och ange om den ska vara aktive rad. Se [webhook-egenskaper](#webhook-properties) för mer information om dessa egenskaper.
5. Klicka på Kopiera-ikonen och tryck på CTRL + C för att kopiera URL: en för webhooken. Registrera den på ett säkert ställe. 

    > [!IMPORTANT]
    > När du skapar webhooken kan du inte hämta URL: en igen. Se till att kopiera och registrera den enligt ovan.

   ![Webhook-URL](media/automation-webhooks/copy-webhook-url.png)

1. Klicka på **parametrar** för att ange värden för Runbook-parametrarna. Om runbooken har obligatoriska parametrar kan du inte skapa webhooken om du inte anger värden.

2. Skapa webhooken genom att klicka på **Skapa**.

## <a name="use-a-webhook"></a>Använda en webhook

Om du vill använda en webhook när den har skapats måste klienten utfärda en HTTP- `POST` begäran med URL: en för webhooken. Syntax:

```http
http://<Webhook Server>/token?=<Token Value>
```

Klienten får en av följande retur koder från `POST` begäran.

| Kod | Text | Beskrivning |
|:--- |:--- |:--- |
| 202 |Har godkänts |Begäran accepterades och Runbook har placerats i kö. |
| 400 |Felaktig begäran |Begäran godtogs inte av någon av följande orsaker: <ul> <li>Webhooken har upphört att gälla.</li> <li>Webhooken är inaktive rad.</li> <li>Token i URL: en är ogiltig.</li>  </ul> |
| 404 |Hittades inte |Begäran godtogs inte av någon av följande orsaker: <ul> <li>Det gick inte att hitta webhooken.</li> <li>Det gick inte att hitta Runbook-flödet.</li> <li>Det gick inte att hitta kontot.</li>  </ul> |
| 500 |Internt Server fel |URL: en var giltig, men ett fel uppstod. Skicka begäran igen. |

Förutsatt att begäran lyckas innehåller webhook-svaret jobb-ID i JSON-format som visas nedan. Den innehåller ett enda jobb-ID, men JSON-formatet möjliggör framtida framtida förbättringar.

```json
{"JobIds":["<JobId>"]}
```

Klienten kan inte avgöra när Runbook-jobbet har slutförts eller om dess slut för ande status från webhooken är slutförd. Den här informationen kan ta reda på den här informationen med jobb-ID: t med en annan mekanism, till exempel [Windows PowerShell](/powershell/module/servicemanagement/azure.service/get-azureautomationjob) eller [Azure Automation-API](/rest/api/automation/job).

### <a name="use-a-webhook-from-an-arm-template"></a>Använda en webhook från en ARM-mall

Automation-webhookar kan också anropas av [Azure Resource Manager arm-mallar (arm)](/azure/azure-resource-manager/templates/overview). ARM-mallen utfärdar en `POST` begäran och får en returkod på samma sätt som andra klienter. Se [använda en webhook](#use-a-webhook).

   > [!NOTE]
   > Av säkerhets skäl returneras URI: n endast första gången en mall distribueras.

Den här exempel mal len skapar en test miljö och returnerar URI: n för webhooken som skapas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "automationAccountName": {
            "type": "String",
            "metadata": {
                "description": "Automation account name"
            }
        },
        "webhookName": {
            "type": "String",
            "metadata": {
                "description": "Webhook Name"
            }
        },
        "runbookName": {
            "type": "String",
            "metadata": {
                "description": "Runbook Name for which webhook will be created"
            }
        },
        "WebhookExpiryTime": {
            "type": "String",
            "metadata": {
                "description": "Webhook Expiry time"
            }
        },
        "_artifactsLocation": {
            "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-automation/",
            "type": "String",
            "metadata": {
                "description": "URI to artifacts location"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2020-01-13-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": {
                    "name": "Free"
                }
            },
            "resources": [
                {
                    "type": "runbooks",
                    "apiVersion": "2018-06-30",
                    "name": "[parameters('runbookName')]",
                    "location": "[resourceGroup().location]",
                    "dependsOn": [
                        "[parameters('automationAccountName')]"
                    ],
                    "properties": {
                        "runbookType": "Python2",
                        "logProgress": "false",
                        "logVerbose": "false",
                        "description": "Sample Runbook",
                        "publishContentLink": {
                            "uri": "[uri(parameters('_artifactsLocation'), 'scripts/AzureAutomationTutorialPython2.py')]",
                            "version": "1.0.0.0"
                        }
                    }
                },
                {
                    "type": "webhooks",
                    "apiVersion": "2018-06-30",
                    "name": "[parameters('webhookName')]",
                    "dependsOn": [
                        "[parameters('automationAccountName')]",
                        "[parameters('runbookName')]"
                    ],
                    "properties": {
                        "isEnabled": true,
                        "expiryTime": "[parameters('WebhookExpiryTime')]",
                        "runbook": {
                            "name": "[parameters('runbookName')]"
                        }
                    }
                }
            ]
        }
    ],
    "outputs": {
        "webhookUri": {
            "type": "String",
            "value": "[reference(parameters('webhookName')).uri]"
        }
    }
}
```

## <a name="renew-a-webhook"></a>Förnya en webhook

När en webhook skapas, har den en giltighets tid på tio år, efter vilken den upphör att gälla automatiskt. När en webhook har gått ut kan du inte återaktivera den. Du kan bara ta bort och sedan återskapa den. 

Du kan utöka en webhook som inte har nått sin förfallo tid. Så här utökar du en webhook:

1. Navigera till den Runbook som innehåller webhooken. 
2. Välj **Webhooks** under **resurser**. 
3. Klicka på den webhook som du vill utöka. 
4. På sidan webhook väljer du ett nytt förfallo datum och tid och klickar på **Spara**.

## <a name="sample-runbook"></a>Exempel-Runbook

Följande exempel-Runbook accepterar webhook-data och startar de virtuella datorer som anges i begär ande texten. Om du vill testa denna Runbook klickar du på **skapa en Runbook** i Automation-kontot under **Runbooks**. Om du inte vet hur du skapar en Runbook, se [skapa en Runbook](automation-quickstart-create-runbook.md).

> [!NOTE]
> För icke-grafiska PowerShell-Runbooks `Add-AzAccount` och `Add-AzureRMAccount` är alias för [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Du kan använda dessa cmdletar, eller så kan du [Uppdatera dina moduler](automation-update-azure-modules.md) i ditt Automation-konto till de senaste versionerna. Du kan behöva uppdatera dina moduler även om du precis har skapat ett nytt Automation-konto.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData
)

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

    # Check header for message to validate request
    if ($WebhookData.RequestHeader.message -eq 'StartedbyContoso')
    {
        Write-Output "Header has required information"}
    else
    {
        Write-Output "Header missing required information";
        exit;
    }

    # Retrieve VMs from Webhook request body
    $vms = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.

    Write-Output "Authenticating to Azure with service principal and certificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    Write-Output "Get connection asset: $ConnectionAssetName"

    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Output "Authenticating to Azure with service principal."
            Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="test-the-sample"></a>Testa exemplet

I följande exempel används Windows PowerShell för att starta en Runbook med en webhook. Alla språk som kan göra en HTTP-begäran kan använda en webhook. Windows PowerShell används här som ett exempel.

Runbooken förväntar sig en lista över virtuella datorer som är formaterade i JSON i bröd texten i begäran. Runbooken validerar även att huvuden innehåller ett definierat meddelande för att verifiera att webhook-anroparen är giltig.

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms
$header = @{ message="StartedbyContoso"}
$response = Invoke-WebRequest -Method Post -Uri $uri -Body $body -Headers $header
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

I följande exempel visas bröd texten i begäran som är tillgänglig för runbooken i `RequestBody` egenskapen för `WebhookData` . Det här värdet är formaterat i JSON för att vara kompatibelt med det format som finns i bröd texten i begäran.

```json
[
    {
        "Name":  "vm01",
        "ResourceGroup":  "myResourceGroup"
    },
    {
        "Name":  "vm02",
        "ResourceGroup":  "myResourceGroup"
    }
]
```

Följande bild visar den begäran som skickas från Windows PowerShell och det resulterande svaret. Jobb-ID: t extraheras från svaret och konverteras till en sträng.

![Webhooks-knapp](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Nästa steg

* Information om hur du utlöser en Runbook från en avisering finns i [använda en avisering för att utlösa en Azure Automation Runbook](automation-create-alert-triggered-runbook.md).