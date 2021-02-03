---
title: Marknads plats partner och kund användnings behörighet
description: Få en översikt över att spåra kund användning för Azure Marketplace-lösningar.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: cpercy737
ms.author: camper
ms.date: 11/4/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: 99e1e77a37afbdc1ed54767700574316ed03fae3
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525253"
---
# <a name="commercial-marketplace-partner-and-customer-usage-attribution"></a>Marknads plats partner och kund användnings behörighet

Kund användnings behörighet är en metod för att associera Azure-resurser som körs i kund prenumerationer och distribueras för att köra din lösning, med dig som partner. Genom att bilda dessa associationer i interna Microsoft-system får du bättre insyn i Azure som kör din program vara. När du antar den här spårnings funktionen kan du justera med Microsofts försäljnings team och få kredit för Microsoft partner program.

Du kan skapa kopplingen via Azure Marketplace, snabb starts databasen, privata GitHub-lagringsplatser och 1:1 kund engagemang som skapar varaktig IP (till exempel utveckling av en app).

Kund användnings behörighet stöder tre distributions alternativ:

- Azure Resource Manager mallar: partner kan använda Resource Manager-mallar för att distribuera Azure-tjänster för att köra partnerns program vara. Partner kan skapa en Resource Manager-mall för att definiera infrastrukturen och konfigurationen för deras Azure-lösning. Med en Resource Manager-mall kan du och dina kunder distribuera din lösning under hela livs cykeln. Du kan vara säker på att dina resurser distribueras i ett konsekvent tillstånd.
- Azure Resource Manager API: er kan anropa Resource Manager-API: er direkt för att distribuera en Resource Manager-mall eller generera API-anrop för att direkt etablera Azure-tjänster.
- Terraform: partner kan använda terraform för att distribuera en Resource Manager-mall eller Distribuera Azure-tjänster direkt.

>[!IMPORTANT]
>- Kund användnings behörighet är inte avsedd att spåra arbetet med system integrators, hanterade tjänst leverantörer eller verktyg som utformats för att distribuera och hantera program vara som körs på Azure.
>
>- Kund användnings behörighet är för nya distributioner och stöder inte taggning av befintliga resurser som redan har distribuerats.
>
>- Kund användnings behörighet krävs för [Azure Application](./create-new-azure-apps-offer.md) erbjudanden som publicerats på Azure Marketplace.
>
>- Alla Azure-tjänster är inte kompatibla med kund användnings behörighet. Azure Kubernetes Services (AKS) och VM Scale Sets har kända problem idag som orsakar rapportering av användning.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-guids"></a>Skapa GUID

Ett GUID är ett unikt referens-ID som innehåller 32 hexadecimala siffror. Om du vill skapa GUID för spårning bör du använda en GUID-generator, till exempel via PowerShell.

```powershell
[guid]::NewGuid()
```

Vi rekommenderar att du skapar ett unikt GUID för varje erbjudande och distributions kanal för varje produkt. Du kan välja att använda ett enda GUID för produktens flera distributions kanaler om du inte vill att rapportering ska delas.

Om du distribuerar en produkt med hjälp av en mall och den är tillgänglig på både Azure Marketplace och på GitHub, kan du skapa och registrera två olika GUID:

- Produkt A på Azure Marketplace
- Produkt A på GitHub

Rapportering görs genom Microsoft Partner Network-ID och GUID.

Du kan också spåra användningen till en mer detaljerad nivå genom att registrera ytterligare GUID och ändra GUID mellan planer, där planer är varianter av ett erbjudande.

## <a name="register-guids"></a>Registrera GUID

GUID: er måste vara registrerade i Partner Center för att möjliggöra kund användnings behörighet.

När du har lagt till ett GUID i mallen eller i användar agenten och registrerat GUID i Partner Center, spåras framtida distributioner.

> [!NOTE]
> Om du publicerar ditt [Azure Application](./create-new-azure-apps-offer.md) erbjudande till Azure Marketplace via partner Center registreras alla nya GUID som används i mallen automatiskt i din partner Center profil när mallen laddas upp.  

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard).

1. Registrera dig som en [extern Marketplace-utgivare](https://aka.ms/JoinMarketplace).

   * Partner måste [ha en profil i Partner Center](./partner-center-portal/create-account.md). Du uppmanas att ange erbjudandet i Azure Marketplace eller AppSource.
   * Partner kan registrera flera GUID.
   * Partner kan registrera GUID för mallar och erbjudanden för icke-Marketplace-lösningar.

1. Välj **Inställningar** (kugg hjuls ikonen) i det övre högra hörnet > **konto inställningar**.

1. I **organisations profil**  >  **identifierare** väljer du **Lägg till spårnings-GUID**.

1. I rutan **GUID** anger du ditt spårnings-GUID. Ange bara GUID utan `pid-` prefixet. I rutan **Beskrivning** anger du namnet eller beskrivningen för erbjudandet.

1. Om du vill registrera fler än ett GUID väljer du **Lägg till spårnings-GUID** igen. Fler rutor visas på sidan.

1. Välj **Spara**.

## <a name="use-resource-manager-templates"></a>Använda Resource Manager-mallar
Många partner lösningar distribueras med hjälp av Azure Resource Manager mallar. Om du har en Resource Manager-mall som är tillgänglig i Azure Marketplace, på GitHub eller som en snabb start, kan du ändra mallen så att du kan göra det rakt framåt i kund användnings behörighet.

> [!NOTE]
> Mer information om hur du skapar och publicerar Solution-mallar finns i
> * [Skapa och distribuera din första Resource Manager-mall](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).
>* [Azure Application erbjudande](./create-new-azure-apps-offer.md).
>* Video: [skapa Solution-mallar och hanterade program för Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603).


Om du vill lägga till en globalt unik identifierare (GUID) gör du en enskild ändring av filen med huvud mal len:

1. [Skapa ett GUID](#create-guids) med den föreslagna metoden och [Registrera GUID](#register-guids).

1. Öppna Resource Manager-mallen.

1. Lägg till en ny resurs av typen [Microsoft. Resources/distributioner](/azure/templates/microsoft.resources/deployments) i filen main Template. Resursen måste vara i **mainTemplate.jspå** eller **azuredeploy.jsendast på** fil och inte i några kapslade eller länkade mallar.

1. Ange GUID-värdet efter `pid-` prefixet som namnet på resursen. Om GUID till exempel är eb7927c8-dd66-43e1-b0cf-c346a422063 blir resurs namnet _PID-eb7927c8-dd66-43e1-b0cf-c346a422063_.

1. Kontrol lera om det finns några fel i mallen.

1. Publicera om mallen i lämpliga databaser.

1. [Verifiera att GUID lyckades i mal Lav distributionen](#verify-the-guid-deployment).

### <a name="sample-resource-manager-template-code"></a>Exempel på Resource Manager-mallkod

Om du vill aktivera spårning av resurser för mallen måste du lägga till följande ytterligare resurs under avsnittet resurser. Se till att ändra exempel koden nedan med dina egna indata när du lägger till den i filen för huvud mal len.
Resursen måste läggas till i **mainTemplate.jspå** eller **azuredeploy.jsendast på** fil och inte i några kapslade eller länkade mallar.

```json
// Make sure to modify this sample code with your own inputs where applicable

{ // add this resource to the resources section in the mainTemplate.json (do not add the entire file)
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

## <a name="use-the-resource-manager-apis"></a>Använda Resource Manager-API: er

I vissa fall kanske du föredrar att ringa direkt till Resource Manager REST-API: er för att distribuera Azure-tjänster. [Azure stöder flera SDK](../index.yml?pivot=sdkstools) : er för att aktivera dessa anrop. Du kan använda en av SDK: erna eller anropa REST-API: er direkt för att distribuera resurser.

Om du använder en Resource Manager-mall bör du tagga lösningen genom att följa anvisningarna ovan. Om du inte använder en Resource Manager-mall och gör direkta API-anrop kan du fortfarande tagga distributionen för att associera användningen av Azure-resurser.

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Tagga en distribution med Resource Manager-API: er

Om du vill aktivera kund användnings behörighet ska du inkludera ett GUID i användar agent huvudet i begäran när du utformar dina API-anrop. Lägg till GUID för varje erbjudande eller SKU. Formatera strängen med `pid-` prefixet och inkludera det GUID som genereras av partner. Här är ett exempel på GUID-formatet för infogning i användar agenten:

![Exempel-GUID-format](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!NOTE]
> Strängens format är viktigt. Om `pid-` prefixet inte ingår är det inte möjligt att fråga efter data. Olika SDK: er spårar på olika sätt. Om du vill implementera den här metoden granskar du support-och spårnings metoden för din önskade Azure SDK.

#### <a name="example-the-python-sdk"></a>Exempel: python SDK

För python använder du **config** -attributet. Du kan bara lägga till attributet i en UserAgent. Här är ett exempel:

![Lägg till attributet i en användar agent](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!NOTE]
> Lägg till attributet för varje klient. Det finns ingen global statisk konfiguration. Du kan tagga en klient fabrik för att se till att alla klienter spårar. Mer information finns i detta [klient fabriks exempel på GitHub](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="example-the-net-sdk"></a>Exempel: .NET SDK

För .NET, se till att ange användar agenten. Du kan använda [Microsoft. Azure. Management. Fluent](/dotnet/api/microsoft.azure.management.fluent) -biblioteket för att ange användar agenten med följande kod (exempel i C#):

```csharp

var azure = Microsoft.Azure.Management.Fluent.Azure
    .Configure()
    // Add your pid in the user agent header
    .WithUserAgent("pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", String.Empty) 
    .Authenticate(/* Credentials created via Microsoft.Azure.Management.ResourceManager.Fluent.SdkContext.AzureCredentialsFactory */)
    .WithSubscription("<subscription ID>");
```

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Tagga en distribution med hjälp av Azure PowerShell

Om du distribuerar resurser via Azure PowerShell lägger du till ditt GUID med hjälp av följande metod:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Tagga en distribution med hjälp av Azure CLI

När du använder Azure CLI för att lägga till ditt GUID, anger du **AZURE_HTTP_USER_AGENT** -miljövariabeln. Du kan ange den här variabeln inom omfånget för ett skript. Du kan också ställa in variabeln globalt för Shell-omfång:

```powershell
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```

Mer information finns i [Azure SDK för go](/azure/developer/go/).

## <a name="use-terraform"></a>Använd terraform

Support för terraform är tillgängligt via Azure providers 1.21.0-utgåva: [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019) .  Detta stöd gäller alla partner som distribuerar sin lösning via terraform, och alla resurser som distribueras och mäts av Azure-providern (version 1.21.0 eller senare).

Azure-providern för terraform har lagt till ett nytt valfritt fält med namnet [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) vilket är där du anger det spårnings-GUID som du använder för din lösning. Värdet för det här fältet kan också hämtas från *ARM_PARTNER_ID* -miljövariabeln.

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Partner som vill få sin distribution via terraform som spåras av kund användnings behörighet måste göra följande:

* Skapa ett GUID (GUID ska läggas till för varje erbjudande eller SKU)
* Uppdatera Azure-providern för att ange värdet för *partner_id* till GUID (korrigera inte GUID med "PID-", och Ställ in det på det faktiska GUID)

## <a name="verify-the-guid-deployment"></a>Verifiera GUID-distributionen

När du har ändrat mallen och kört en test distribution använder du följande PowerShell-skript för att hämta de resurser som du har distribuerat och taggat.

Du kan använda skriptet för att kontrol lera att GUID har lagts till i Resource Manager-mallen. Skriptet gäller inte för Resource Manager API eller terraform-distributioner.

Logga in i Azure. Välj den prenumeration med distributionen som du vill verifiera innan du kör skriptet. Kör skriptet i prenumerations kontexten för distributionen.

**GUID** och **resourceGroup** namn för distributionen är obligatoriska parametrar.

Du kan hämta [det ursprungliga skriptet](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1) på GitHub.

```powershell
Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the pid deployment

$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName
$resourceGroupName -Name "pid-$guid").correlationId

# Find all deployments with that correlationId

$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in a deployment by name
# PowerShell doesn't surface outputResources on the deployment
# or correlationId on the deploymentOperation

foreach ($deployment in $deployments){

# Get deploymentOperations by deploymentName
# then the resourceId for any create operation

($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}
```
## <a name="report"></a>Rapport
Rapportering för Azure-användning som spåras via kund användnings behörighet är inte tillgängligt i nuläget för ISV-partner. Att lägga till rapporter i programmet för kommersiella marknads platser i Partner Center för att avse kund användnings behörighet är riktad till den andra halvan av 2021.

## <a name="notify-your-customers"></a>Meddela kunderna

Partner bör informera sina kunder om distributioner som använder sig av kund användnings behörighet. Microsoft rapporterar Azure-användningen som är kopplad till dessa distributioner till partnern. Följande exempel innehåller innehåll som du kan använda för att meddela dina kunder om dessa distributioner. I exemplen ersätter du \<PARTNER> med ditt företags namn. Partner bör se till att meddelandet överensstämmer med sina principer för data sekretess och insamling, inklusive alternativ för kunder som ska undantas från spårning.

### <a name="notification-for-resource-manager-template-deployments"></a>Meddelande för distributioner av Resource Manager-mallar

När du distribuerar den här mallen kan Microsoft identifiera installationen av \<PARTNER> program vara med de Azure-resurser som distribueras. Microsoft kan korrelera de Azure-resurser som används för att stödja program varan. Microsoft samlar in den här informationen för att ge bästa möjliga upplevelse av sina produkter och för att driva verksamheten. Data samlas in och regleras av Microsofts sekretess principer, som du hittar på [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

### <a name="notification-for-sdk-or-api-deployments"></a>Meddelande om SDK-eller API-distributioner

När du distribuerar \<PARTNER> program vara kan Microsoft identifiera installationen av \<PARTNER> program vara med de Azure-resurser som distribueras. Microsoft kan korrelera de Azure-resurser som används för att stödja program varan. Microsoft samlar in den här informationen för att ge bästa möjliga upplevelse av sina produkter och för att driva verksamheten. Data samlas in och regleras av Microsofts sekretess principer, som du hittar på [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

## <a name="get-support"></a>Få support

Lär dig mer om support alternativen i den kommersiella Marketplace [för support för programmet för kommersiella marknads platser i Partner Center](support.md).

### <a name="how-to-submit-a-technical-consultation-request"></a>Så här skickar du en begäran om tekniskt samråd

1. Besök [partner tekniska tjänster](https://aka.ms/TechnicalJourney).
1. Välj moln infrastruktur och hantering, så öppnas en ny sida där du kan se den tekniska resan.
1. Under distributions tjänster klickar du på knappen Skicka en förfrågan
1. Logga in med ditt MSA (MPN-konto) eller ditt AAD-konto (partner instrument panel). baserat på dina inloggnings uppgifter öppnas ett formulär för online-begäran:
    * Slutför/granska kontakt uppgifterna.
    * Information om konsultationen kan vara ifylld eller väljas i list rutan.
    * Ange en rubrik och en beskrivning av problemet (ange så mycket information som möjligt).

1. Klicka på Skicka

Visa steg-för-steg-instruktioner med skärm dum par med [tekniska försäljnings-och distributions tjänster](https://aka.ms/TechConsultInstructions).

### <a name="whats-next"></a>Nästa steg

Du kommer att kontaktas av en teknisk konsult från Microsoft-partner för att skapa ett samtal för att tillgodose dina behov.

## <a name="faq"></a>Vanliga frågor

**Vad är fördelen med att lägga till GUID i mallen?**

Microsoft tillhandahåller partner med en överblick över kund distributioner av lösningar och insikter om deras påverkan på användningen. Både Microsoft och partnern kan använda den här informationen för att driva närmare engagemang mellan sälj team. Både Microsoft och partnern kan använda dessa data för att få en mer enhetlig vy av en enskild partners påverkan på Azure-tillväxten.

**Kan den ändras när ett GUID har lagts till?**

Ja, en kund-eller implementerings partner kan anpassa mallen och kan ändra eller ta bort GUID. Vi rekommenderar att partners proaktivt beskriver rollen för resursen och GUID för sina kunder och partners för att förhindra borttagning eller redigering av GUID. Att ändra GUID påverkar endast nya, inte befintliga, distributioner och resurser.

**Kan jag spåra mallar som har distribuerats från en annan databas än Microsoft, t. ex. GitHub?**

Ja, så länge som GUID finns när mallen distribueras, spåras användningen. Partners måste fortfarande registrera sina GUID.

**Kan kunden även ta emot rapportering?**

Kunder kan spåra användningen av enskilda resurser eller kunddefinierade resurs grupper inom Azure Portal. Kunderna ser inte användning uppdelad med GUID.

**Liknar den här metoden en digital partner av Record (DPOR)?**

Den här nya metoden för att ansluta distribution och användning till en partners lösning är en mekanism för att länka en partner lösning till Azure-användning. DPOR är avsett att koppla en konsult (Systems Integrator) eller hanterings partner (hanterad tjänst leverantör) till en kunds Azure-prenumeration.

**Kan jag använda en privat, anpassad VHD för en lösnings mal len-erbjudande på Azure Marketplace?**

Nej, du kan inte. Avbildningen av den virtuella datorn måste komma från Azure Marketplace, se: [publicerings guide för erbjudanden om virtuella datorer på Azure Marketplace](marketplace-virtual-machines.md).

Du kan skapa ett virtuellt dator erbjudande i Marketplace med din anpassade virtuella hård disk och markera det som privat så att ingen kan se den. Referera till den här virtuella datorn i din lösnings mall.

**Det gick inte att uppdatera *contentVersion* -egenskapen för huvud mal len?**

Detta är troligen ett fel i fallet när mallen distribueras med hjälp av en TemplateLink från en annan mall som förväntar sig äldre contentVersion av någon anledning. Lösningen är att använda egenskapen metadata:

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
