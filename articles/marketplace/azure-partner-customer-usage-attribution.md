---
title: Azures kund användnings behörighet
description: Få en översikt över att spåra kund användning för Azure-program på den kommersiella marknaden och andra distributions bara IP-adresser som har utvecklats av partner.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: cpercy737
ms.author: camper
ms.date: 03/22/2021
ms.custom: devx-track-terraform
ms.openlocfilehash: 53edd3ec9a8d30d0c25f994db4a8b6f0199c2169
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558439"
---
# <a name="azure-customer-usage-attribution"></a>Azures kund användnings behörighet

Kund användnings tilldelningen kopplar användning från Azure-resurser i kund prenumerationer som skapas när du distribuerar din IP-adress med dig som partner. Genom att bilda dessa associationer i interna Microsoft-system får du bättre insyn i Azure som kör din program vara. För [Azure Application erbjudanden i den kommersiella marknads platsen](#commercial-marketplace-azure-apps)hjälper den här spårnings funktionen dig att anpassa sig till Microsofts försäljnings team och få kredit för Microsoft partner program.

Kund användnings behörighet stöder tre distributions alternativ:

1. Azure Resource Manager mallar (de vanliga underfästningarna av Azure Apps, som också kallas för "lösningsfiler" eller "hanterade appar"): partner skapar Resource Manager-mallar för att definiera infrastrukturen och konfigurationen för deras Azure-lösningar. Med en Resource Manager-mall kan dina kunder distribuera lösningens resurser i ett konsekvent och repeterbart tillstånd.
1. Azure Resource Manager API: er kan anropa Resource Manager-API: er för att distribuera en Resource Manager-mall eller etablera Azure-tjänster direkt.
1. Terraform: partner kan använda terraform för att distribuera en Resource Manager-mall eller Distribuera Azure-tjänster direkt.

Det finns sekundära användnings fall för kund användnings behörighet utanför den kommersiella Marketplace som beskrivs [längre fram i den här artikeln](#other-use-cases).

>[!IMPORTANT]
>- Kund användnings behörighet är inte avsedd att spåra arbetet med system integrators, hanterade tjänst leverantörer eller verktyg som främst utformats för att distribuera och hantera Azure-resurser.
>- Kund användnings behörighet är för nya distributioner och stöder inte spårning av resurser som redan har distribuerats.
>- Alla Azure-tjänster är inte kompatibla med kund användnings behörighet. Azure Kubernetes Services (AKS), VM Scale Sets och Azure Batch har kända problem som orsakar rapportering av användning.

## <a name="commercial-marketplace-azure-apps"></a>Azure-appar på kommersiella platser

Spårning av Azure-användning från Azure-appar som publiceras på den kommersiella marknaden är i stort sett automatisk. När du laddar upp en Resource Manager-mall som en del av den [tekniska konfigurationen av din Marketplace-plan för Azure-appen](./create-new-azure-apps-offer-solution.md#define-the-technical-configuration), lägger Partner Center till ett SPÅRNINGS-ID som kan läsas av Azure Resource Manager.

Om du använder Azure Resource Manager-API: er måste du lägga till ditt spårnings-ID enligt [instruktionerna nedan](#use-resource-manager-apis) för att skicka det till Azure Resource Manager när din kod distribuerar resurser. Detta ID visas i Partner Center på din Plans tekniska konfigurations sida. 

> [!NOTE]
> För befintliga Azure-appar började migreringen en gång i mars 2021 för att uppdatera spårnings-ID: n i varje Plans tekniska konfiguration. Användning från tidigare distributioner av dessa erbjudanden fortsätter att följas i Microsoft system.
>
>När du uppdaterar dina erbjudanden behöver du inte längre lägga till resurs typen **Microsoft. Resources/distributions** resurs i filen main Template.

## <a name="other-use-cases"></a>Andra användnings fall 

Du kan använda kund användnings behörighet för att spåra Azures användning av lösningar som inte är tillgängliga på den kommersiella marknads platsen. Dessa lösningar finns vanligt vis i snabb starts databasen, privata GitHub-lagringsplatser eller kommer från 1:1 kund engagemang som skapar varaktig IP (till exempel en distributions bar och skalbar app).

Det krävs flera manuella steg:

1. Skapa ett eller flera GUID som ska användas som spårnings-ID.
1. Registrera dessa GUID i Partner Center.
1. Lägg till dina registrerade GUID i Azure-appen och/eller användar agent strängarna.

### <a name="create-guids"></a>Skapa GUID

Till skillnad från de spårnings-ID: n som partner Center skapar för Azure-appar på den kommersiella marknaden, kräver annan användning av CUA att du skapar ett GUID som du kan använda som ditt spårnings-ID. Ett GUID är ett unikt referens-ID som innehåller 32 hexadecimala siffror. Om du vill skapa GUID för spårning bör du använda en GUID-generator, till exempel via PowerShell:

```powershell
[guid]::NewGuid()
```

Du bör skapa ett unikt GUID för varje produkt-och distributions kanal. Du kan använda ett enda GUID för en produkts flera distributions kanaler om du inte vill att rapportering ska delas. Rapporteringen sker genom Microsoft Partner Network-ID och GUID.

### <a name="register-guids"></a>Registrera GUID

GUID måste sedan vara registrerat i Partner Center så att de kan kopplas till dig som en partner:

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard).

1. Registrera dig som en [extern Marketplace-utgivare](https://aka.ms/JoinMarketplace).

1. Välj **Inställningar** (kugg hjuls ikonen) i det övre högra hörnet och sedan **konto inställningar**.

1. Välj **organisations profil**  >  **identifierare**  >  **Lägg till spårnings-GUID**.

1. I rutan **GUID** anger du ditt spårnings-GUID. Ange bara GUID utan `pid-` prefixet. I rutan **Beskrivning** anger du lösningens namn eller beskrivning.

1. Om du vill registrera fler än ett GUID väljer du **Lägg till spårnings-GUID** igen. Fler rutor visas på sidan.

1. Välj **Spara**.

### <a name="add-a-guid-to-a-resource-manager-template"></a>Lägg till ett GUID i en Resource Manager-mall

Om du vill lägga till ditt registrerade GUID i en Resource Manager-mall gör du en ändring av filen med huvud mal len:

1. Öppna Resource Manager-mallen.

1. Lägg till en ny resurs av typen [Microsoft. Resources/distributioner](/azure/templates/microsoft.resources/deployments) i filen main Template. Resursen måste vara i **mainTemplate.jspå** eller **azuredeploy.jsendast på** fil, inte i några kapslade eller länkade mallar.

1. Ange GUID-värdet efter `pid-` prefixet som namnet på resursen. Om GUID till exempel är eb7927c8-dd66-43e1-b0cf-c346a422063 blir resurs namnet **PID-eb7927c8-dd66-43e1-b0cf-c346a422063**. Exempel:
 
```json
{ // add this resource to the resources section in the mainTemplate.json
    "apiVersion": "2020-06-01",
    "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your generated GUID here
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "template": {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "resources": []
        }
    }
} // remove all comments from the file when complete
```
4. Kontrol lera om det finns fel i mallen.

1. Publicera om mallen i lämpliga databaser.

1. [Verifiera att GUID lyckades i mal Lav distributionen](#verify-deployments-tracked-with-a-guid).

> [!TIP]
> Mer information om hur du skapar och publicerar Resource Manager-mallar finns i: [skapa och distribuera din första Resource Manager-mall](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

### <a name="verify-deployments-tracked-with-a-guid"></a>Verifiera distributioner som spåras med ett GUID

När du har ändrat mallen och kört en test distribution använder du följande PowerShell-skript för att hämta de resurser som du har distribuerat och taggat.

Du kan använda skriptet för att kontrol lera att GUID har lagts till i Resource Manager-mallen. Skriptet gäller inte för Resource Manager API eller terraform-distributioner.

Logga in i Azure. Välj prenumerationen med den distribution du vill verifiera innan du kör skriptet. Kör skriptet i prenumerations kontexten för distributionen.

**GUID** (nedan kallat "deploymentName") och **resourceGroupName** namn för distributionen är obligatoriska parametrar.

Du kan hämta [det ursprungliga skriptet](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1) på GitHub.

```powershell
Param(
    [string][Parameter(Mandatory=$true)]$deploymentName, # the full name of the deployment, e.g. pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the named deployment
$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name "$deploymentName").correlationId

# Find all deployments with that correlationId
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in all deployments with that correlationId as PowerShell doesn't surface outputResources on the deployment or correlationId on the deploymentOperation

foreach ($deployment in $deployments){
    # Get deploymentOperations by deploymentName
    # then the resourceIds for each resource
    ($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.targetResource -notlike "*Microsoft.Resources/deployments*"}).TargetResource
}
```

### <a name="notify-your-customers"></a>Meddela kunderna

Partner bör informera sina kunder om distributioner som använder sig av kund användnings behörighet. Microsoft rapporterar Azure-användningen som är kopplad till dessa distributioner till partnern. Följande exempel innehåller innehåll som du kan använda för att meddela dina kunder om dessa distributioner. I exemplen ersätter du \<PARTNER> med ditt företags namn. Partner bör se till att meddelandet överensstämmer med sina principer för data sekretess och insamling, inklusive alternativ för kunder som ska undantas från spårning.

#### <a name="notification-for-resource-manager-template-deployments"></a>Meddelande för distributioner av Resource Manager-mallar

När du distribuerar den här mallen kan Microsoft identifiera installationen av \<PARTNER> program vara med de distribuerade Azure-resurserna. Microsoft kan korrelera dessa resurser som används för att stödja program varan. Microsoft samlar in den här informationen för att ge bästa möjliga upplevelse av sina produkter och för att driva verksamheten. Data samlas in och regleras av Microsofts sekretess principer, som finns på [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

#### <a name="notification-for-sdk-or-api-deployments"></a>Meddelande om SDK-eller API-distributioner

När du distribuerar \<PARTNER> program vara kan Microsoft identifiera installationen av \<PARTNER> program vara med de distribuerade Azure-resurserna. Microsoft kan korrelera dessa resurser som används för att stödja program varan. Microsoft samlar in den här informationen för att ge bästa möjliga upplevelse av sina produkter och för att driva verksamheten. Data samlas in och regleras av Microsofts sekretess principer, som finns på [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

## <a name="use-resource-manager-apis"></a>Använd API: er för Resource Manager

I vissa fall kan du ringa direkt till Resource Manager REST-API: er för att distribuera Azure-tjänster. [Azure stöder flera SDK](../index.yml?pivot=sdkstools) : er för att aktivera dessa anrop. Du kan använda en av SDK: erna eller anropa REST-API: erna direkt för att distribuera resurser.

Om du vill aktivera kund användnings behörighet, när du utformar dina API-anrop, inkluderar du ditt spårnings-ID i användar agentens huvud i begäran. Formatera strängen med `pid-` prefixet. Exempel:

```xml
//Commercial Marketplace Azure app
pid-contoso-myoffer-partnercenter //copy the tracking ID exactly as it appears in Partner Center

//Other use cases
pid-b6addd8f-5ff4-4fc0-a2b5-0ec7861106c4 //enter your GUID after "pid-"
```
> [!IMPORTANT]
> Om du använder Resource Manager-API: er med en Azure-app på den kommersiella Marketplace använder du det spårnings-ID som finns i Partner Center. Använd inte ett GUID.

Olika SDK: er interagerar med Resource Manager-API: er annorlunda och kräver vissa skillnader i koden. Exemplen nedan visar den icke-kommersiella Marketplace-metoden med ett GUID och beskriver en mängd olika populära Azure-SDK: er.

#### <a name="example-python-sdk"></a>Exempel: python SDK

För python använder du **config** -attributet. Du kan bara lägga till attributet i en UserAgent. Exempel:

```python
client = azure.mgmt.servicebus.ServiceBusManagementClient(**parameters)
client.config.add_user_agent("pid-b6addd8f-5ff4-4fc0-a2b5-0ec7861106c4")
```
> [!IMPORTANT]
> Lägg till attributet för varje klient. Det finns ingen global statisk konfiguration. Du kan tagga en klient fabrik för att se till att varje klient spårar. Mer information finns i detta [klient fabriks exempel på GitHub](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="example-net-sdk"></a>Exempel: .NET SDK

För .NET, se till att ange användar agenten. Använd [Microsoft. Azure. Management. Fluent](/dotnet/api/microsoft.azure.management.fluent) -biblioteket för att ange användar agenten med följande kod (exempel i C#):

```csharp
var azure = Microsoft.Azure.Management.Fluent.Azure
    .Configure()
    // Add your pid in the user agent header
    .WithUserAgent("pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", String.Empty) 
    .Authenticate(/* Credentials created via Microsoft.Azure.Management.ResourceManager.Fluent.SdkContext.AzureCredentialsFactory */)
    .WithSubscription("<subscription ID>");
```

#### <a name="example-azure-powershell"></a>Exempel: Azure PowerShell

Om du distribuerar resurser via Azure PowerShell lägger du till ditt GUID med hjälp av den här metoden:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="example-azure-cli"></a>Exempel: Azure CLI

När du använder Azure CLI för att lägga till ditt GUID, anger du **AZURE_HTTP_USER_AGENT** -miljövariabeln inom omfånget för ett skript. Du kan också ställa in variabeln globalt för Shell-omfång:

```powershell
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```

Mer information finns i [Azure SDK för go](/azure/developer/go/).

## <a name="use-terraform"></a>Använd terraform

Support för terraform är tillgängligt via Azure providers 1.21.0-utgåva: [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019) . Detta gäller alla partner som distribuerar sin lösning via terraform och alla resurser som distribueras och mäts av Azure-providern (version 1.21.0 eller senare).

Azure Provider för terraform har lagt till ett nytt valfritt fält med namnet [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) för att ange det spårnings-GUID som används för din lösning. Värdet för det här fältet kan också hämtas från *ARM_PARTNER_ID* -miljövariabeln.

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Ange värdet för *partner_id* till ett registrerat GUID. Använd inte GUID-prefixet med "PID-", och ange det bara som faktiskt GUID.

> [!IMPORTANT]
> Om du använder terraform med en Azure-app på den kommersiella Marketplace använder du hela det spårnings-ID som finns i Partner Center. Använd inte ett GUID.

## <a name="get-support"></a>Få support

Lär dig mer om support alternativen i den kommersiella Marketplace [för support för programmet för kommersiella marknads platser i Partner Center](support.md).

### <a name="how-to-submit-a-technical-consultation-request"></a>Så här skickar du en begäran om tekniskt samråd

1. Besök [partner tekniska tjänster](https://aka.ms/TechnicalJourney).
1. Välj **moln infrastruktur och hantering** för att se den tekniska resan.
1. Välj **Deployment Services**  >  **skicka en begäran**.
1. Logga in med ditt MSA (MPN-konto) eller ditt AAD-konto (partner instrument panel).
1. Slutför/granska kontakt informationen i formuläret som öppnas. Information om konsultationen kan vara ifylld eller också kan du ha List alternativ.
1. Ange en rubrik och en detaljerad beskrivning av problemet.
1. Välj **Skicka**.

Visa steg-för-steg-instruktioner med skärm dum par med [tekniska försäljnings-och distributions tjänster](https://aka.ms/TechConsultInstructions).

Du kommer att kontaktas av en teknisk konsult från Microsoft-partner för att skapa ett samtal för att tillgodose dina behov.

## <a name="report"></a>Rapport
Rapportering för Azure-användning som spåras via kund användnings behörighet är inte tillgängligt i nuläget för ISV-partner. Att lägga till rapporter i programmet för kommersiella marknads platser i Partner Center för att avse kund användnings behörighet är riktad till den andra halvan av 2021.

## <a name="faq"></a>Vanliga frågor

#### <a name="after-a-tracking-id-is-added-can-it-be-changed"></a>Kan den ändras när du har lagt till ett spårnings-ID?

Spårnings-ID: n för Azure apps på den kommersiella marknaden hanteras automatiskt av Partner Center. En kund kan dock hämta en mall och ändra eller ta bort spårnings-ID: t. Partner bör proaktivt beskriva rollen för spårnings-ID: n till sina kunder för att förhindra borttagning eller redigering. Att ändra spårnings-ID påverkar endast nya distributioner och resurser, inte befintliga.

#### <a name="can-i-track-templates-deployed-from-a-non-microsoft-repository-like-github"></a>Kan jag spåra mallar som har distribuerats från en annan databas än Microsoft, t. ex. GitHub?

Ja, så länge spårnings-ID: t finns när mallen distribueras, spåras användningen. Om du vill behålla kopplingen mellan dig som utgivare och mallen som distribuerats från en annan databas än Microsoft, laddar du först ned en kopia av din publicerade mall (som innehåller spårnings-ID) från ditt erbjudandes kommersiella Marketplace-lista i Azure Portal. Publicera den versionen på GitHub eller en annan lagrings plats som inte kommer från Microsoft.

Om din mall inte finns med på den kommersiella Marketplace och innehåller ett registrerat GUID, se till att GUID finns i den version som du publicerar på GitHub eller en annan lagrings plats som inte kommer från Microsoft.

#### <a name="does-the-customer-receive-reporting-as-well"></a>Kan kunden även ta emot rapportering?

Nej. Kunder kan spåra användningen av alla resurser eller resurs grupper inom Azure Portal. Kunderna ser inte användning uppdelad efter CUA spårnings-ID.

#### <a name="is-customer-usage-attribution-similar-to-the-digital-partner-of-record-dpor-or-partner-admin-link-pal"></a>Liknar kund användnings behörigheten för den digitala DPOR-partnern () eller partner admin Link (PAL)?

Kund användnings behörighet är en mekanism för att associera Azure-användning med en partners repeterbara, distributions bara IP-som utgör associationen vid tidpunkten för distributionen. DPOR och PAL är avsedda att associera en konsult-partner (Systems Integrator) eller hanterings tjänst (Managed Service Provider) med en kunds relevanta Azure-data för den tid då partnern arbetar med kunden.