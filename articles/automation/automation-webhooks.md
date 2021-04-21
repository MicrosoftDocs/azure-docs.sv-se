---
title: Starta en Azure Automation runbook från en webhook
description: Den här artikeln beskriver hur du använder en webhook för att starta en runbook Azure Automation från ett HTTP-anrop.
services: automation
ms.subservice: process-automation
ms.date: 03/18/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 338fb56c4af5c24b7b746ffd6508c2fe7d52b131
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830204"
---
# <a name="start-a-runbook-from-a-webhook"></a>Starta ett Runbook-flöde från en webhook

Med en webhook kan en extern tjänst starta en viss runbook i Azure Automation via en enda HTTP-begäran. Externa tjänster är Azure DevOps Services, GitHub, Azure Monitor loggar och anpassade program. En sådan tjänst kan använda en webhook för att starta en runbook utan att implementera det fullständiga Azure Automation-API:et. Du kan jämföra webhooks med andra metoder för att starta en runbook i [Starta en runbook i Azure Automation](./start-runbooks.md).

> [!NOTE]
> Det finns inte stöd för att använda en webhook för att starta en Python-runbook.

![WebhooksÖversikt](media/automation-webhooks/webhook-overview-image.png)

Information om klientkrav för TLS 1.2 med webhooks finns i [TLS 1.2-tillämpning för Azure Automation](automation-managing-data.md#tls-12-enforcement-for-azure-automation).

## <a name="webhook-properties"></a>Webhook-egenskaper

I följande tabell beskrivs de egenskaper som du måste konfigurera för en webhook.

| Egenskap | Beskrivning |
|:--- |:--- |
| Name |Namnet på webhooken. Du kan ange vilket namn du vill eftersom det inte exponeras för klienten. Den används bara för att identifiera runbooken i Azure Automation. Som bästa praxis bör du ge webhooken ett namn som är relaterat till den klient som använder den. |
| URL |Url för webhooken. Det här är den unika adressen som en klient anropar med en HTTP POST för att starta runbooken som är länkad till webhooken. Den genereras automatiskt när du skapar webhooken. Du kan inte ange en anpassad URL. <br> <br> URL:en innehåller en säkerhetstoken som gör att ett tredjepartssystem kan anropa runbooken utan ytterligare autentisering. Därför bör du behandla URL:en som ett lösenord. Av säkerhetsskäl kan du bara visa URL:en i Azure Portal när du skapar webhooken. Anteckna URL:en på en säker plats för framtida användning. |
| Förfallodatum | Förfallodatum för webhooken, efter vilket den inte längre kan användas. Du kan ändra förfallodatumet efter att webhooken har skapats, så länge webhooken inte har upphört att gälla. |
| Enabled | Inställning som anger om webhooken är aktiverad som standard när den skapas. Om du anger den här egenskapen till Inaktiverad kan ingen klient använda webhooken. Du kan ange den här egenskapen när du skapar webhooken eller någon annan gång efter att den har skapats. |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Parametrar som används när webhooken startar en runbook

En webhook kan definiera värden för runbook-parametrar som används när runbook startas. Webhooken måste innehålla värden för alla obligatoriska runbook-parametrar och kan innehålla värden för valfria parametrar. Ett parametervärde som konfigurerats till en webhook kan ändras även efter att webhooken har skapats. Flera webhooks som är länkade till en enda runbook kan använda olika runbook-parametervärden. När en klient startar en runbook med hjälp av en webhook kan den inte åsidosätta de parametervärden som definierats i webhooken.

Runbooken stöder en enda parameter med namnet för att ta emot data från `WebhookData` klienten. Den här parametern definierar ett objekt som innehåller data som klienten inkluderar i en POST-begäran.

![WebhookData-egenskaper](media/automation-webhooks/webhook-data-properties.png)

Parametern `WebhookData` har följande egenskaper:

| Egenskap | Beskrivning |
|:--- |:--- |
| `WebhookName` | Namnet på webhooken. |
| `RequestHeader` | Hash-tabell som innehåller huvudena för den inkommande POST-begäran. |
| `RequestBody` | Brödtext i den inkommande POST-begäran. Den här brödtexten behåller all dataformatering, till exempel sträng, JSON, XML eller formulärkodad. Runbooken måste skrivas för att fungera med det dataformat som förväntas. |

Det krävs ingen konfiguration av webhooken för att stödja `WebhookData` parametern och runbooken krävs inte för att acceptera den. Om runbooken inte definierar parametern ignoreras all information om begäran som skickas från klienten.

> [!NOTE]
> När du anropar en webhook bör klienten alltid lagra alla parametervärden om anropet misslyckas. Om det uppstår ett nätverksavbrott eller anslutningsproblem kan programmet inte hämta misslyckade webhook-anrop.

Om du anger ett värde för när webhook skapas åsidosätts det när webhooken startar runbooken med data från `WebhookData` klientens POST-begäran. Detta inträffar även om programmet inte innehåller några data i begärandetexten. 

Om du startar en runbook som definierar med hjälp av en annan mekanism än en webhook kan du ange ett värde som `WebhookData` `WebhookData` runbooken identifierar. Det här värdet ska vara [](#webhook-properties) ett objekt med samma egenskaper som parametern så att runbooken kan arbeta med den precis som den fungerar med faktiska objekt som skickas av `WebhookData` en `WebhookData` webhook.

Om du till exempel startar följande runbook från Azure Portal och vill skicka några exempelwebhookdata för testning, måste du skicka data i JSON i användargränssnittet.

![WebhookData-parameter från användargränssnittet](media/automation-webhooks/WebhookData-parameter-from-UI.png)

I nästa runbook-exempel ska vi definiera följande egenskaper för `WebhookData` :

* **WebhookName:** MyWebhook
* **RequestBody**: `*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

Nu skickar vi följande JSON-objekt i användargränssnittet för `WebhookData` parametern . Det här exemplet, med vagnreturer och tecken för ny rad, matchar det format som skickas från en webhook.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Starta webhookData-parametern från användargränssnittet](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Azure Automation loggar värdena för alla indataparametrar med runbook-jobbet. Därför loggas alla indata som tillhandahålls av klienten i webhook-begäran och är tillgängliga för alla som har åtkomst till automationsjobbet. Därför bör du vara försiktig med att inkludera känslig information i webhook-anrop.

## <a name="webhook-security"></a>Webhook-säkerhet

Säkerheten för en webhook är beroende av sekretessen för dess URL, som innehåller en säkerhetstoken som gör att webhooken kan anropas. Azure Automation utför ingen autentisering på en begäran så länge den görs till rätt URL. Därför bör klienterna inte använda webhooks för runbooks som utför mycket känsliga åtgärder utan att använda ett alternativt sätt att verifiera begäran.

Tänk på följande strategier:

* Du kan inkludera logik i en runbook för att avgöra om den anropas av en webhook. Be runbooken att kontrollera `WebhookName` parameterns `WebhookData` egenskap. Runbooken kan utföra ytterligare verifiering genom att söka efter viss information i `RequestHeader` egenskaperna `RequestBody` och .

* Be runbooken att utföra viss validering av ett externt villkor när den tar emot en webhook-begäran. Tänk dig till exempel en runbook som anropas av GitHub när det finns en ny commit till en GitHub-lagringsplats. Runbooken kan ansluta till GitHub för att verifiera att en ny genomförande har skett innan du fortsätter.

* Azure Automation har stöd för tjänsttaggar för virtuella Azure-nätverk, [särskilt GuestAndHybridManagement.](../virtual-network/service-tags-overview.md) Du kan använda tjänsttaggar för [](../virtual-network/network-security-groups-overview.md#security-rules) att definiera nätverksåtkomstkontroller i [nätverkssäkerhetsgrupper](../firewall/service-tags.md) eller Azure Firewall och utlösa webhooks inifrån ditt virtuella nätverk. Tjänsttaggar kan användas i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange tjänsttaggnamnet **GuestAndHybridManagement**  i rätt käll- eller målfält för en regel kan du tillåta eller neka trafiken för Automation-tjänsten. Den här tjänsttaggen stöder inte mer detaljerad kontroll genom att begränsa IP-intervall till en specifik region.

## <a name="create-a-webhook"></a>Skapa en webhook

Använd följande procedur för att skapa en ny webhook som är länkad till en runbook i Azure Portal.

1. På sidan Runbooks i Azure Portal klickar du på den runbook som webhooken börjar visa runbook-informationen. Kontrollera att fältet Runbook **Status** är inställt på **Publicerad**.
2. Klicka **på Webhook** överst på sidan för att öppna sidan Lägg till webhook.
3. Klicka **på Skapa ny webhook** för att öppna sidan Skapa webhook.
4. Fyll i fälten **Namn** **och Förfallodatum** för webhooken och ange om den ska aktiveras. Mer information om dessa egenskaper finns i [Webhook-egenskaper.](#webhook-properties)
5. Klicka på kopieringsikonen och tryck på Ctrl+C för att kopiera webhookens URL. Registrera den sedan på en säker plats. 

    > [!IMPORTANT]
    > När du har skapat webhooken kan du inte hämta URL:en igen. Se till att du kopierar och registrerar den som ovan.

   ![Webhook-URL](media/automation-webhooks/copy-webhook-url.png)

1. Klicka **på Parametrar** för att ange värden för runbook-parametrarna. Om runbooken har obligatoriska parametrar kan du inte skapa webhooken om du inte anger värden.

2. Skapa webhooken genom att klicka på **Skapa**.

## <a name="use-a-webhook"></a>Använda en webhook

Om du vill använda en webhook när den har skapats måste klienten utfärda en HTTP-begäran med `POST` URL:en för webhooken. Syntax:

```http
http://<Webhook Server>/token?=<Token Value>
```

Klienten får en av följande returkoder från `POST` begäran.

| Kod | Text | Description |
|:--- |:--- |:--- |
| 202 |Har godkänts |Begäran godkändes och runbooken köade. |
| 400 |Felaktig begäran |Begäran godkändes inte av någon av följande orsaker: <ul> <li>Webhooken har upphört att gälla.</li> <li>Webhooken är inaktiverad.</li> <li>Token i URL:en är ogiltig.</li>  </ul> |
| 404 |Hittades inte |Begäran godkändes inte av någon av följande orsaker: <ul> <li>Webhooken hittades inte.</li> <li>Runbooken hittades inte.</li> <li>Kontot hittades inte.</li>  </ul> |
| 500 |Internt serverfel |URL:en var giltig, men ett fel uppstod. Skicka begäran igen. |

Om begäran lyckas innehåller webhook-svaret jobb-ID:t i JSON-format enligt nedan. Den innehåller ett enda jobb-ID, men JSON-formatet tillåter potentiella framtida förbättringar.

```json
{"JobIds":["<JobId>"]}
```

Klienten kan inte avgöra när runbook-jobbet har slutförts eller dess slutförandestatus från webhooken. Den kan ta reda på den här informationen med hjälp av jobb-ID:t med en [annan mekanism,](/powershell/module/servicemanagement/azure.service/get-azureautomationjob) till exempel Windows PowerShell eller [Azure Automation-API:et](/rest/api/automation/job).

### <a name="use-a-webhook-from-an-arm-template"></a>Använda en webhook från en ARM-mall

Automation-webhooks kan också anropas Azure Resource Manager [ARM-mallar](/azure/azure-resource-manager/templates/overview). ARM-mallen utfärdar en `POST` begäran och tar emot en returkod precis som andra klienter. Se [Använda en webhook.](#use-a-webhook)

   > [!NOTE]
   > Av säkerhetsskäl returneras URI:en bara första gången en mall distribueras.

Den här exempelmallen skapar en testmiljö och returnerar URI:en för den webhook som skapas.

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

När en webhook skapas har den en giltighetsperiod på tio år, varom den automatiskt upphör att gälla. När en webhook har upphört att gälla kan du inte återaktivera den. Du kan bara ta bort och sedan återskapa den. 

Du kan utöka en webhook som inte har nått sin förfallotid. Så här utökar du en webhook:

1. Gå till den runbook som innehåller webhooken. 
2. Välj **Webhooks** under **Resurser**. 
3. Klicka på den webhook som du vill utöka. 
4. På sidan Webhook väljer du ett nytt förfallodatum och en ny tid och klickar på **Spara.**

## <a name="sample-runbook"></a>Exempel på runbook

Följande exempel-runbook accepterar webhook-data och startar de virtuella datorer som anges i begärandetexten. Om du vill testa den här runbooken går du till ditt Automation-konto under **Runbooks** och klickar **på Skapa en runbook.** Om du inte vet hur du skapar en runbook kan du se [Skapa en runbook](automation-quickstart-create-runbook.md).

> [!NOTE]
> För icke-grafiska PowerShell-runbooks `Add-AzAccount` och är alias för `Add-AzureRMAccount` [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Du kan använda dessa cmdlets eller så kan [du uppdatera dina moduler i](automation-update-azure-modules.md) ditt Automation-konto till de senaste versionerna. Du kan behöva uppdatera dina moduler även om du precis har skapat ett nytt Automation-konto.

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

I följande exempel används Windows PowerShell för att starta en runbook med en webhook. Alla språk som kan göra en HTTP-begäran kan använda en webhook. Windows PowerShell används här som exempel.

Runbooken förväntar sig en lista över virtuella datorer formaterade i JSON i brödtexten i begäran. Runbooken verifierar också att huvudena innehåller ett definierat meddelande för att verifiera att webhook-anroparen är giltig.

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

I följande exempel visas brödtexten i begäran som är tillgänglig för runbooken i `RequestBody` egenskapen för `WebhookData` . Det här värdet är formaterat i JSON för att vara kompatibelt med det format som ingår i begärandetexten.

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

Följande bild visar begäran som skickas från Windows PowerShell och det resulterande svaret. Jobb-ID:t extraheras från svaret och konverteras till en sträng.

![Knappen Webhooks](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Nästa steg

* Information om hur du utlöser en runbook från en avisering finns i Använda [en avisering för att Azure Automation en runbook.](automation-create-alert-triggered-runbook.md)
