---
title: Felsök vanliga distributionsfel
description: Beskriver hur du löser vanliga fel när du distribuerar resurser till Azure med hjälp av Azure Resource Manager.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 01/20/2021
ms.openlocfilehash: bb701baf164cfd6ba1dd5c670a406343e83c5c70
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98622819"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Felsöka vanliga fel i Azure-distributioner med Azure Resource Manager

Den här artikeln beskriver några vanliga problem med Azure-distribution och innehåller information om hur du löser felen. Om du inte hittar felkoden för ditt distributionsfel läser du [Hitta felkod](#find-error-code).

Om du letar efter information om en felkod och informationen inte finns i den här artikeln kan du berätta för oss. Längst ned på den här sidan kan du lämna feedback. Feedbacken spåras med GitHub-problem.

## <a name="error-codes"></a>Felkoder

| Felkod | Åtgärd | Mer information |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Följ namngivnings begränsningar för lagrings konton. | [Matcha lagrings konto namn](error-storage-account-name.md) |
| AccountPropertyCannotBeSet | Kontrol lera egenskaperna för tillgängligt lagrings konto. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | Det finns inga tillgängliga resurser för klustret eller regionen eller så stöds inte den begärda virtuella dator storleken. Försök igen vid ett senare tillfälle eller begär en annan VM-storlek. | [Etablerings-och tilldelnings problem för Linux](../../virtual-machines/troubleshooting/troubleshoot-deployment-new-vm-linux.md), [etablering och tilldelning av Windows](../../virtual-machines/troubleshooting/troubleshoot-deployment-new-vm-windows.md) och [fel sökning av allokeringsfel](../../virtual-machines/troubleshooting/allocation-failure.md)|
| AnotherOperationInProgress | Vänta tills den samtidiga åtgärden har slutförts. | |
| AuthorizationFailed | Ditt konto eller tjänstens huvud namn har inte tillräcklig åtkomst för att slutföra distributionen. Kontrol lera vilken roll ditt konto tillhör och dess åtkomst till distributions omfånget.<br><br>Du kan få det här felet när en resurs leverantör som krävs inte är registrerad. | [Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)<br><br>[Lös registrering](error-register-resource-provider.md) |
| BadRequest | Du skickade distributions värden som inte matchar det som förväntas av Resource Manager. Kontrol lera meddelandet om inre status för att få hjälp med fel sökning. | [Referera till mallar](/azure/templates/) och [platser som stöds](resource-location.md) |
| Konflikt | Du begär en åtgärd som inte är tillåten i resursens aktuella tillstånd. Till exempel tillåts disk storleks ändring bara när du skapar en virtuell dator eller när den virtuella datorn frigörs. | |
| DeploymentActiveAndUneditable | Vänta tills en samtidig distribution till den här resurs gruppen har slutförts. | |
| DeploymentFailedCleanUp | När du distribuerar i fullständigt läge tas alla resurser som inte finns i mallen bort. Du får det här felet när du inte har tillräcklig behörighet för att ta bort alla resurser som inte finns i mallen. Undvik felet genom att ändra distributions läget till stegvist. | [Distributionslägen för Azure Resource Manager](deployment-modes.md) |
| DeploymentNameInvalidCharacters | Distributions namnet får bara innehålla bokstäver, siffror, "-", "." eller "_". | |
| DeploymentNameLengthLimitExceeded | Distributions namnen är begränsade till 64 tecken.  | |
| DeploymentFailed | DeploymentFailed-felet är ett allmänt fel som inte innehåller den information du behöver för att lösa problemet. Se fel informationen för en felkod som innehåller mer information. | [Hitta felkod](#find-error-code) |
| DeploymentQuotaExceeded | Om du når gränsen på 800-distributioner per resurs grupp tar du bort distributioner från den historik som inte längre behövs. | [Lös fel när antalet distributioner överskrider 800](deployment-quota-exceeded.md) |
| DeploymentJobSizeExceeded | Förenkla din mall för att minska storleken. | [Lös fel i mal Lav Tor lek](error-job-size-exceeded.md) |
| DnsRecordInUse | DNS-postens namn måste vara unikt. Ange ett annat namn. | |
| ImageNotFound | Kontrol lera inställningarna för VM-avbildningar. |  |
| InUseSubnetCannotBeDeleted | Du kan få det här felet när du försöker uppdatera en resurs, och begäran bearbetas genom att ta bort och skapa resursen. Se till att du anger alla värden som inte har ändrats. | [Uppdatera resurs](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Hämta åtkomsttoken för lämplig klient organisation. Du kan bara hämta token från den klient som ditt konto tillhör. | |
| InvalidContentLink | Du har troligen försökt länka till en kapslad mall som inte är tillgänglig. Dubbelt kontrol lera den URI du angav för den kapslade mallen. Om mallen finns i ett lagrings konto kontrollerar du att URI: n är tillgänglig. Du kan behöva skicka en SAS-token. För närvarande kan du inte länka till en mall som finns i ett lagrings konto bakom en [Azure Storage-brandvägg](../../storage/common/storage-network-security.md). Överväg att flytta mallen till en annan lagrings plats, t. ex. GitHub. | [Länkade mallar](linked-templates.md) |
| InvalidDeploymentLocation | När du distribuerar på prenumerations nivå har du angett en annan plats för ett tidigare använt distributions namn. | [Distributioner av prenumerations nivå](deploy-to-subscription.md) |
| InvalidParameter | Ett av de värden som du angav för en resurs stämmer inte överens med det förväntade värdet. Det här felet kan bero på flera olika villkor. Ett lösen ord kan till exempel vara otillräckligt eller också kan ett BLOB-namn vara felaktigt. Fel meddelandet ska ange vilket värde som behöver korrigeras. | |
| InvalidRequestContent | Distributions värden innehåller antingen värden som inte känns igen eller värden som krävs saknas. Bekräfta värdena för resurs typen. | [Mallreferens](/azure/templates/) |
| InvalidRequestFormat | Aktivera fel söknings loggning när du kör distributionen och kontrol lera innehållet i begäran. | [Fel söknings loggning](#enable-debug-logging) |
| InvalidResourceNamespace | Kontrol lera resurs namn området du angav i egenskapen **Type** . | [Mallreferens](/azure/templates/) |
| InvalidResourceReference | Resursen finns inte ännu eller så refereras den felaktigt. Kontrol lera om du behöver lägga till ett beroende. Kontrol lera att din användning av **referens** funktionen innehåller de nödvändiga parametrarna för ditt scenario. | [Matcha beroenden](error-not-found.md) |
| InvalidResourceType | Kontrol lera den resurs typ som du angav i egenskapen **Type** . | [Mallreferens](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Registrera din prenumeration med resurs leverantören. | [Lös registrering](error-register-resource-provider.md) |
| InvalidTemplate | Kontrol lera din malls syntax för fel. | [Lös Ogiltig mall](error-invalid-template.md) |
| InvalidTemplateCircularDependency | Ta bort onödiga beroenden. | [Lös cirkulära beroenden](error-invalid-template.md#circular-dependency) |
| JobSizeExceeded | Förenkla din mall för att minska storleken. | [Lös fel i mal Lav Tor lek](error-job-size-exceeded.md) |
| LinkedAuthorizationFailed | Kontrol lera om ditt konto tillhör samma klient organisation som den resurs grupp som du distribuerar till. | |
| LinkedInvalidPropertyId | Resurs-ID: t för en resurs löses inte korrekt. Kontrol lera att du anger alla obligatoriska värden för resurs-ID, inklusive prenumerations-ID, resurs gruppens namn, resurs typ, överordnat resurs namn (vid behov) och resurs namn. | |
| LocationRequired | Ange en plats för resursen. | [Ange en plats](resource-location.md) |
| MismatchingResourceSegments | Kontrol lera att den kapslade resursen har rätt antal segment i namn och typ. | [Lösa resurs segment](error-invalid-template.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | Kontrol lera registrerings status för resurs leverantör och platser som stöds. | [Lös registrering](error-register-resource-provider.md) |
| MissingSubscriptionRegistration | Registrera din prenumeration med resurs leverantören. | [Lös registrering](error-register-resource-provider.md) |
| NoRegisteredProviderFound | Kontrol lera registrerings status för resurs leverantör. | [Lös registrering](error-register-resource-provider.md) |
| NotFound | Du kanske försöker att distribuera en beroende resurs parallellt med en överordnad resurs. Kontrol lera om du behöver lägga till ett beroende. | [Matcha beroenden](error-not-found.md) |
| OperationNotAllowed | Distributionen försöker utföra en åtgärd som överskrider kvoten för prenumerationen, resurs gruppen eller regionen. Om möjligt kan du ändra distributionen så att den ligger inom kvoterna. Annars kan du överväga att begära en ändring av dina kvoter. | [Lösa kvoter](error-resource-quota.md) |
| ParentResourceNotFound | Kontrol lera att det finns en överordnad resurs innan du skapar underordnade resurser. | [Matcha överordnad resurs](error-parent-resource.md) |
| PasswordTooLong | Du kanske har valt ett lösen ord med för många tecken eller konverterat ditt lösen ord till en säker sträng innan du skickar det som en parameter. Om mallen innehåller en **säker sträng** -parameter behöver du inte konvertera värdet till en säker sträng. Ange lösen ordets värde som text. |  |
| PrivateIPAddressInReservedRange | Den angivna IP-adressen innehåller ett adress intervall som krävs av Azure. Ändra IP-adress för att undvika reserverat intervall. | [IP-adresser](../../virtual-network/public-ip-addresses.md) |
| PrivateIPAddressNotInSubnet | Den angivna IP-adressen ligger utanför under nätets intervall. Ändra IP-adressen så att den hamnar inom under nätets intervall. | [IP-adresser](../../virtual-network/public-ip-addresses.md) |
| PropertyChangeNotAllowed | Vissa egenskaper kan inte ändras i en distribuerad resurs. Begränsa ändringarna till tillåtna egenskaper när du uppdaterar en resurs. | [Uppdatera resurs](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | Din prenumeration innehåller en resurs princip som förhindrar en åtgärd som du försöker utföra under distributionen. Hitta principen som blockerar åtgärden. Om möjligt kan du ändra distributionen för att uppfylla begränsningarna från principen. | [Lös principer](error-policy-requestdisallowedbypolicy.md) |
| ReservedResourceName | Ange ett resurs namn som inte innehåller ett reserverat namn. | [Reserverade resurs namn](error-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Vänta tills borttagningen har slutförts. | |
| ResourceGroupNotFound | Kontrol lera namnet på mål resurs gruppen för distributionen. Mål resurs gruppen måste redan finnas i din prenumeration. Kontrol lera prenumerations kontexten. | [Azure CLI](/cli/azure/account?#az-account-set) [PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| ResourceNotFound | Distributionen refererar till en resurs som inte kan lösas. Kontrol lera att din användning av funktionen **Reference** innehåller de parametrar som krävs för ditt scenario. | [Lösa referenser](error-not-found.md) |
| ResourceQuotaExceeded | Distributionen försöker skapa resurser som överskrider kvoten för prenumerationen, resurs gruppen eller regionen. Om möjligt kan du ändra infrastrukturen för att hålla dig inom kvoterna. Annars kan du överväga att begära en ändring av dina kvoter. | [Lösa kvoter](error-resource-quota.md) |
| SkuNotAvailable | Välj SKU (till exempel VM-storlek) som är tillgänglig för den plats som du har valt. | [Lös SKU](error-sku-not-available.md) |
| StorageAccountAlreadyExists | Ange ett unikt namn för lagringskontot. | [Matcha lagrings konto namn](error-storage-account-name.md)  |
| StorageAccountAlreadyTaken | Ange ett unikt namn för lagringskontot. | [Matcha lagrings konto namn](error-storage-account-name.md) |
| StorageAccountNotFound | Kontrol lera prenumerationen, resurs gruppen och namnet på det lagrings konto som du försöker använda. | |
| SubnetsNotInSameVnet | En virtuell dator kan bara ha ett virtuellt nätverk. När du distribuerar flera nätverkskort ser du till att de tillhör samma virtuella nätverk. | [Flera nätverkskort](../../virtual-machines/windows/multiple-nics.md) |
| SubscriptionNotFound | Det går inte att komma åt en angiven prenumeration för distribution. Det kan bero på att prenumerations-ID: t är felaktigt, att användaren som distribuerar mallen inte har tillräcklig behörighet för att distribuera till prenumerationen, eller att prenumerations-ID: t har fel format. När du använder kapslade distributioner för att [distribuera mellan omfattningar](./deploy-to-resource-group.md)anger du GUID för prenumerationen. | |
| SubscriptionNotRegistered | När du distribuerar en resurs måste resurs leverantören vara registrerad för din prenumeration. När du använder en Azure Resource Manager mall för distribution registreras resurs leverantören automatiskt i prenumerationen. Ibland slutförs inte den automatiska registreringen i tid. För att undvika det här tillfälliga felet registrerar du resurs leverantören före distributionen. | [Lös registrering](error-register-resource-provider.md) |
| TemplateResourceCircularDependency | Ta bort onödiga beroenden. | [Lös cirkulära beroenden](error-invalid-template.md#circular-dependency) |
| TooManyTargetResourceGroups | Minska antalet resurs grupper för en enskild distribution. | [Distribution över omfång](./deploy-to-resource-group.md) |

## <a name="find-error-code"></a>Hitta felkod

Det finns två typer av fel som du kan få:

* valideringsfel
* distributionsfel

Valideringsfel uppstår från scenarier som kan fastställas före distributionen. De innehåller syntaxfel i mallen eller försöker distribuera resurser som skulle överskrida prenumerationskvoterna. Distributionsfel uppstår från förhållanden som inträffar under distributionsprocessen. De omfattar försök att få åtkomst till en resurs som distribueras parallellt.

Båda typerna av fel returnerar en felkod som du använder för att felsöka distributionen. Båda typerna av fel visas i [aktivitetsloggen](../management/view-activity-logs.md). Dock visas valideringsfel inte i distributionshistoriken eftersom distributionen aldrig startades.

### <a name="validation-errors"></a>Verifierings fel

När du distribuerar via portalen visas ett valideringsfel när du har skickat in dina värden.

![Visa Portal validerings fel](./media/common-deployment-errors/validation-error.png)

Välj meddelandet för mer information. I följande bild visas ett **InvalidTemplateDeployment** -fel och ett meddelande som anger en princip blockerad distribution.

![Visa verifierings information](./media/common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>Distributionsfel

När åtgärden godkänns men inte fungerar under distributionen visas ett distributionsfel.

Om du vill visa felkoder och meddelanden för distribution med PowerShell kan du använda:

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).Properties.statusMessage
```

Om du vill visa felkoder och meddelanden för distribution med Azure CLI kan du använda:

```azurecli-interactive
az deployment operation group list --name exampledeployment -g examplegroup --query "[*].properties.statusMessage"
```

Markera meddelandet på portalen.

![meddelande fel](./media/common-deployment-errors/notification.png)

Du får mer information om distributionen. Välj alternativet för att hitta mer information om felet.

![distributionen misslyckades](./media/common-deployment-errors/deployment-failed.png)

Du ser felmeddelandet och felkoderna. Observera att det finns två felkoder. Den första felkoden (**DeploymentFailed**) är ett allmänt fel som inte ger den information du behöver för att lösa felet. Den andra felkoden (**StorageAccountNotFound**) ger dig den information du behöver.

![fel information](./media/common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Aktivera fel söknings loggning

Ibland behöver du mer information om begäran och svar för att lära dig vad som gått fel. Under distributionen kan du begära att ytterligare information loggas under en distribution.

### <a name="powershell"></a>PowerShell

I PowerShell anger du parametern **DeploymentDebugLogLevel** till alla, ResponseContent eller RequestContent.

```powershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

Granska innehållet i begäran med följande cmdlet:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.request `
| ConvertTo-Json
```

Eller, svars innehållet med:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.response `
| ConvertTo-Json
```

Den här informationen kan hjälpa dig att avgöra om ett värde i mallen har angetts felaktigt.

### <a name="azure-cli"></a>Azure CLI

För närvarande stöder inte Azure CLI aktivering av fel söknings loggning, men du kan hämta fel söknings loggning.

Granska distributions åtgärderna med följande kommando:

```azurecli
az deployment operation group list \
  --resource-group examplegroup \
  --name exampledeployment
```

Granska innehållet i begäran med följande kommando:

```azurecli
az deployment operation group list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.request
```

Granska svars innehållet med följande kommando:

```azurecli
az deployment operation group list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>Kapslad mall

Om du vill logga felsöknings information för en kapslad mall använder du **debugSetting** -elementet.

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2016-09-01",
  "name": "nestedTemplate",
  "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "{template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "debugSetting": {
       "detailLevel": "requestContent, responseContent"
    }
  }
}
```

## <a name="create-a-troubleshooting-template"></a>Skapa en mall för fel sökning

I vissa fall är det enklaste sättet att felsöka din mall att testa delar av den. Du kan skapa en förenklad mall som hjälper dig att fokusera på den del som du tror är orsaken till felet. Anta till exempel att du får ett fel när du refererar till en resurs. I stället för att hantera en hel mall skapar du en mall som returnerar den del som kan orsaka ditt problem. Det kan hjälpa dig att avgöra om du skickar rätt parametrar, använder mallarna på rätt sätt och hur du får den resurs som du förväntar dig.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  "storageName": {
    "type": "string"
  },
  "storageResourceGroup": {
    "type": "string"
  }
  },
  "variables": {},
  "resources": [],
  "outputs": {
  "exampleOutput": {
    "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
    "type" : "object"
  }
  }
}
```

Eller, anta att du får distributions fel som du tror är relaterade till felaktigt inställda beroenden. Testa mallen genom att dela upp den i förenklade mallar. Börja med att skapa en mall som endast distribuerar en enskild resurs (som en SQL Server). När du är säker på att resursen är korrekt definierad lägger du till en resurs som är beroende av den (t. ex. en SQL Database). När de här två resurserna är korrekt definierade lägger du till andra beroende resurser (t. ex. gransknings principer). I mellan varje test distribution tar du bort resurs gruppen för att kontrol lera att du testar beroendena på ett korrekt sätt.

## <a name="next-steps"></a>Nästa steg

* Information om hur du går igenom en fel söknings kurs finns i [Självstudier: Felsöka Resource Manager-mallar distributioner](template-tutorial-troubleshoot.md)
* Mer information om gransknings åtgärder finns i [gransknings åtgärder med Resource Manager](../management/view-activity-logs.md).
* Information om åtgärder för att fastställa felen under distributionen finns i [Visa distributions åtgärder](deployment-history.md).