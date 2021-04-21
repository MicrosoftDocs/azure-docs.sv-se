---
title: Felsök vanliga distributionsfel
description: Beskriver hur du löser vanliga fel när du distribuerar resurser till Azure med hjälp av Azure Resource Manager.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 01/20/2021
ms.openlocfilehash: 07c197f1b54522b96a3bfa2d6a5ce7b368be3b35
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789185"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Felsöka vanliga fel i Azure-distributioner med Azure Resource Manager

Den här artikeln beskriver några vanliga Azure-distributionsfel och innehåller information för att lösa felen. Om du inte hittar felkoden för ditt distributionsfel läser du [Hitta felkod](#find-error-code).

Om du letar efter information om en felkod och den informationen inte finns i den här artikeln kan du berätta för oss. Längst ned på den här sidan kan du lämna feedback. Feedbacken spåras med GitHub-problem.

## <a name="error-codes"></a>Felkoder

| Felkod | Åtgärd | Mer information |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Följ namngivningsbegränsningar för lagringskonton. | [Matcha lagringskontots namn](error-storage-account-name.md) |
| AccountPropertyCannotBeSet | Kontrollera tillgängliga egenskaper för lagringskontot. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | Klustret eller regionen har inte tillgängliga resurser eller stöder inte den begärda VM-storleken. Försök igen vid ett senare tillfälle eller begär en annan VM-storlek. | [Problem med etablering och allokering för Linux,](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-linux) [problem med etablering och allokering för Windows och](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-windows) Felsökning av [allokeringsfel](/troubleshoot/azure/virtual-machines/allocation-failure)|
| AnotherOperationInProgress | Vänta tills samtidig åtgärd har slutförts. | |
| AuthorizationFailed | Ditt konto eller tjänstens huvudnamn har inte tillräcklig åtkomst för att slutföra distributionen. Kontrollera vilken roll ditt konto tillhör och dess åtkomst för distributionsomfånget.<br><br>Du kan få det här felet när en nödvändig resursprovider inte har registrerats. | [Azure RBAC (rollbaserad åtkomstkontroll)](../../role-based-access-control/role-assignments-portal.md)<br><br>[Lösa registrering](error-register-resource-provider.md) |
| BadRequest | Du skickade distributionsvärden som inte matchar det som förväntas av Resource Manager. Kontrollera det inre statusmeddelandet för att få hjälp med felsökning. | [Mallreferens](/azure/templates/) och [platser som stöds](resource-location.md) |
| Konflikt | Du begär en åtgärd som inte tillåts i resursens aktuella tillstånd. Diskändring tillåts till exempel bara när du skapar en virtuell dator eller när den virtuella datorn har frisits. | |
| DeploymentActiveAndUneditable | Vänta tills samtidig distribution till den här resursgruppen har slutförts. | |
| DeploymentFailedCleanUp | När du distribuerar i fullständigt läge tas alla resurser som inte finns i mallen bort. Du får det här felet när du inte har tillräcklig behörighet för att ta bort alla resurser som inte finns i mallen. Undvik felet genom att ändra distributionsläget till inkrementellt. | [Distributionslägen för Azure Resource Manager](deployment-modes.md) |
| DeploymentNameInvalidCharacters | Distributionsnamnet får bara innehålla bokstav, siffra, "-", "." eller "_". | |
| DeploymentNameLengthLimitExceeded | Distributionsnamnen är begränsade till 64 tecken.  | |
| DeploymentFailed | Felet DeploymentFailed är ett allmänt fel som inte ger den information du behöver för att lösa felet. Titta i felinformationen efter en felkod som innehåller mer information. | [Hitta felkod](#find-error-code) |
| DeploymentQuotaExceeded | Om du når gränsen på 800 distributioner per resursgrupp tar du bort distributioner från historiken som inte längre behövs. | [Lösa fel när distributionsantalet överskrider 800](deployment-quota-exceeded.md) |
| DeploymentJobSizeExceeded | Förenkla mallen för att minska storleken. | [Lösa fel med mallstorlek](error-job-size-exceeded.md) |
| DnsRecordInUse | DNS-postnamnet måste vara unikt. Ange ett annat namn. | |
| ImageNotFound | Kontrollera inställningarna för VM-avbildningar. |  |
| InUseSubnetCannotBeDeleted | Du kan få det här felet när du försöker uppdatera en resurs, och begäran bearbetas genom att ta bort och skapa resursen. Se till att ange alla oförändrade värden. | [Uppdatera resurs](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Hämta åtkomsttoken för lämplig klientorganisation. Du kan bara hämta token från den klientorganisation som ditt konto tillhör. | |
| InvalidContentLink | Du har förmodligen försökt länka till en kapslad mall som inte är tillgänglig. Dubbelkolla den URI som du angav för den kapslade mallen. Om mallen finns i ett lagringskonto kontrollerar du att URI:en är tillgänglig. Du kan behöva skicka en SAS-token. För närvarande kan du inte länka till en mall som finns i ett lagringskonto bakom en [Azure Storage brandvägg.](../../storage/common/storage-network-security.md) Överväg att flytta mallen till en annan lagringsplats, till exempel GitHub. | [Länkade mallar](linked-templates.md) |
| InvalidDeploymentLocation | När du distribuerar på prenumerationsnivå har du angett en annan plats för ett tidigare använt distributionsnamn. | [Distributioner på prenumerationsnivå](deploy-to-subscription.md) |
| InvalidParameter | Ett av de värden som du angav för en resurs matchar inte det förväntade värdet. Det här felet kan uppstå på grund av många olika villkor. Ett lösenord kan till exempel vara otillräckligt eller så kan ett blobnamn vara felaktigt. Felmeddelandet bör ange vilket värde som måste korrigeras. | |
| InvalidRequestContent | Distributionsvärdena innehåller antingen värden som inte känns igen eller så saknas nödvändiga värden. Bekräfta värdena för din resurstyp. | [Mallreferens](/azure/templates/) |
| InvalidRequestFormat | Aktivera felsökningsloggning när du kör distributionen och verifiera innehållet i begäran. | [Felsökningsloggning](#enable-debug-logging) |
| InvalidResourceNamespace | Kontrollera resursnamnområdet som du angav i **egenskapen type.** | [Mallreferens](/azure/templates/) |
| InvalidResourceReference | Resursen finns inte ännu eller refereras felaktigt. Kontrollera om du behöver lägga till ett beroende. Kontrollera att din användning av **referensfunktionen** innehåller de parametrar som krävs för ditt scenario. | [Lösa beroenden](error-not-found.md) |
| InvalidResourceType | Kontrollera resurstypen som du angav i **egenskapen type.** | [Mallreferens](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Registrera din prenumeration med resursprovidern. | [Lösa registrering](error-register-resource-provider.md) |
| InvalidTemplate | Kontrollera om det finns fel i mallsyntaxen. | [Lösa ogiltig mall](error-invalid-template.md) |
| InvalidTemplateCircularDependency | Ta bort onödiga beroenden. | [Lösa cirkelberoenden](error-invalid-template.md#circular-dependency) |
| JobSizeExceeded | Förenkla mallen för att minska storleken. | [Lösa fel med mallstorlek](error-job-size-exceeded.md) |
| LinkedAuthorizationFailed | Kontrollera om ditt konto tillhör samma klientorganisation som den resursgrupp som du distribuerar till. | |
| LinkedInvalidPropertyId | Resurs-ID:t för en resurs matchas inte korrekt. Kontrollera att du anger alla värden som krävs för resurs-ID, inklusive prenumerations-ID, resursgruppsnamn, resurstyp, överordnat resursnamn (om det behövs) och resursnamn. | |
| LocationRequired | Ange en plats för resursen. | [Ange en plats](resource-location.md) |
| FelmatchningResourceSegments | Kontrollera att den kapslade resursen har rätt antal segment i namn och typ. | [Lösa resurssegment](error-invalid-template.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | Kontrollera resursproviderns registreringsstatus och platser som stöds. | [Lösa registrering](error-register-resource-provider.md) |
| MissingSubscriptionRegistration | Registrera din prenumeration med resursprovidern. | [Lösa registrering](error-register-resource-provider.md) |
| NoRegisteredProviderFound | Kontrollera resursproviderns registreringsstatus. | [Lösa registrering](error-register-resource-provider.md) |
| NotFound | Du kanske försöker distribuera en beroende resurs parallellt med en överordnad resurs. Kontrollera om du behöver lägga till ett beroende. | [Lösa beroenden](error-not-found.md) |
| OperationNotAllowed | Distributionen försöker utföra en åtgärd som överskrider kvoten för prenumerationen, resursgruppen eller regionen. Om möjligt ändrar du distributionen så att den håller sig inom kvoterna. I annat fall kan du begära en ändring av dina kvoter. | [Lösa kvoter](error-resource-quota.md) |
| ParentResourceNotFound | Kontrollera att det finns en överordnad resurs innan du skapar de underordnade resurserna. | [Matcha överordnad resurs](error-parent-resource.md) |
| PasswordTooLong | Du kanske har valt ett lösenord med för många tecken eller konverterat ditt lösenordsvärde till en säker sträng innan du anger det som en parameter. Om mallen innehåller en **säker strängparameter** behöver du inte konvertera värdet till en säker sträng. Ange lösenordsvärdet som text. |  |
| PrivateIPAddressInReservedRange | Den angivna IP-adressen innehåller ett adressintervall som krävs av Azure. Ändra IP-adress för att undvika reserverat intervall. | [IP-adresser](../../virtual-network/public-ip-addresses.md) |
| PrivateIPAddressNotInSubnet | Den angivna IP-adressen ligger utanför undernätsintervallet. Ändra IP-adressen så att den hamnar inom undernätsintervallet. | [IP-adresser](../../virtual-network/public-ip-addresses.md) |
| PropertyChangeNotAllowed | Vissa egenskaper kan inte ändras på en distribuerad resurs. När du uppdaterar en resurs begränsar du dina ändringar till tillåtna egenskaper. | [Uppdatera resurs](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| RequestDisallowedByPolicy | Din prenumeration innehåller en resursprincip som förhindrar en åtgärd som du försöker utföra under distributionen. Hitta den princip som blockerar åtgärden. Om möjligt kan du ändra distributionen för att uppfylla begränsningarna i principen. | [Lösa principer](error-policy-requestdisallowedbypolicy.md) |
| ReservedResourceName | Ange ett resursnamn som inte innehåller ett reserverat namn. | [Reserverade resursnamn](error-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Vänta tills borttagningen är klar. | |
| ResourceGroupNotFound | Kontrollera namnet på målresursgruppen för distributionen. Målresursgruppen måste redan finnas i din prenumeration. Kontrollera din prenumerationskontext. | [Azure CLI](/cli/azure/account?#az_account_set) [PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| ResourceNotFound | Distributionen refererar till en resurs som inte kan matchas. Kontrollera att din användning av **referensfunktionen** innehåller de parametrar som krävs för ditt scenario. | [Matcha referenser](error-not-found.md) |
| ResourceQuotaExceeded | Distributionen försöker skapa resurser som överskrider kvoten för prenumerationen, resursgruppen eller regionen. Om möjligt ändrar du infrastrukturen så att den håller sig inom kvoterna. Annars kan du begära en ändring av dina kvoter. | [Lösa kvoter](error-resource-quota.md) |
| SkuNotAvailable | Välj SKU (till exempel VM-storlek) som är tillgänglig för den plats som du har valt. | [Lösa SKU](error-sku-not-available.md) |
| StorageAccountAlreadyExists | Ange ett unikt namn för lagringskontot. | [Matcha lagringskontots namn](error-storage-account-name.md)  |
| StorageAccountAlreadyTaken | Ange ett unikt namn för lagringskontot. | [Matcha lagringskontots namn](error-storage-account-name.md) |
| StorageAccountNotFound | Kontrollera prenumerationen, resursgruppen och namnet på det lagringskonto som du försöker använda. | |
| UndernätNotInSameVnet | En virtuell dator kan bara ha ett virtuellt nätverk. När du distribuerar flera nätverkskort kontrollerar du att de tillhör samma virtuella nätverk. | [Flera nätverkskort](../../virtual-machines/windows/multiple-nics.md) |
| SubscriptionNotFound | Det går inte att komma åt en angiven prenumeration för distribution. Det kan vara så att prenumerations-ID:t är fel, att användaren som distribuerar mallen inte har tillräcklig behörighet att distribuera till prenumerationen eller att prenumerations-ID:t har fel format. När du använder kapslade [distributioner för att distribuera över omfång](./deploy-to-resource-group.md)anger du GUID för prenumerationen. | |
| SubscriptionNotRegistered | När du distribuerar en resurs måste resursprovidern vara registrerad för din prenumeration. När du använder Azure Resource Manager mall för distribution registreras resursprovidern automatiskt i prenumerationen. Ibland slutförs inte den automatiska registreringen i tid. Undvik det tillfälliga felet genom att registrera resursprovidern före distributionen. | [Lösa registrering](error-register-resource-provider.md) |
| TemplateResourceCircularDependency | Ta bort onödiga beroenden. | [Lösa cirkelberoenden](error-invalid-template.md#circular-dependency) |
| TooManyTargetResourceGroups | Minska antalet resursgrupper för en enskild distribution. | [Distribution över flera omfång](./deploy-to-resource-group.md) |

## <a name="find-error-code"></a>Hitta felkod

Det finns två typer av fel som du kan få:

* valideringsfel
* distributionsfel

Valideringsfel uppstår från scenarier som kan fastställas före distributionen. De innehåller syntaxfel i mallen eller försöker distribuera resurser som skulle överskrida prenumerationskvoterna. Distributionsfel uppstår från förhållanden som inträffar under distributionsprocessen. De omfattar försök att få åtkomst till en resurs som distribueras parallellt.

Båda typerna av fel returnerar en felkod som du använder för att felsöka distributionen. Båda typerna av fel visas i [aktivitetsloggen](../management/view-activity-logs.md). Dock visas valideringsfel inte i distributionshistoriken eftersom distributionen aldrig startades.

### <a name="validation-errors"></a>Valideringsfel

När du distribuerar via portalen visas ett valideringsfel när du har skickat in dina värden.

![visa portalverifieringsfel](./media/common-deployment-errors/validation-error.png)

Välj meddelandet för mer information. I följande bild visas felet **InvalidTemplateDeployment** och ett meddelande som anger att en princip har blockerat distributionen.

![visa valideringsinformation](./media/common-deployment-errors/validation-details.png)

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

![meddelandefel](./media/common-deployment-errors/notification.png)

Du ser mer information om distributionen. Välj alternativet för att hitta mer information om felet.

![distributionen misslyckades](./media/common-deployment-errors/deployment-failed.png)

Du ser felmeddelandet och felkoderna. Observera att det finns två felkoder. Den första felkoden (**DeploymentFailed**) är ett allmänt fel som inte ger den information du behöver för att lösa felet. Den andra felkoden (**StorageAccountNotFound**) ger dig den information du behöver.

![felinformation](./media/common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Aktivera felsökningsloggning

Ibland behöver du mer information om begäran och svar för att lära dig vad som gick fel. Under distributionen kan du begära att ytterligare information loggas under en distribution.

### <a name="powershell"></a>PowerShell

I PowerShell anger du **parametern DeploymentDebugLogLevel** till Alla, ResponseContent eller RequestContent.

```powershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

Granska begärandeinnehållet med följande cmdlet:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.request `
| ConvertTo-Json
```

Eller så kan svarsinnehållet med:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.response `
| ConvertTo-Json
```

Den här informationen kan hjälpa dig att avgöra om ett värde i mallen har angetts felaktigt.

### <a name="azure-cli"></a>Azure CLI

Azure CLI stöder för närvarande inte att aktivera felsökningsloggning, men du kan hämta felsökningsloggning.

Granska distributionsåtgärder med följande kommando:

```azurecli
az deployment operation group list \
  --resource-group examplegroup \
  --name exampledeployment
```

Granska begärandeinnehållet med följande kommando:

```azurecli
az deployment operation group list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.request
```

Granska svarsinnehållet med följande kommando:

```azurecli
az deployment operation group list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>Kapslad mall

Om du vill logga felsökningsinformation för en kapslad mall använder du **elementet debugSetting.**

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2020-10-01",
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

## <a name="create-a-troubleshooting-template"></a>Skapa en felsökningsmall

I vissa fall är det enklaste sättet att felsöka mallen att testa delar av den. Du kan skapa en förenklad mall som gör att du kan fokusera på den del som du tror orsakar felet. Anta till exempel att du får ett fel när du refererar till en resurs. I stället för att hantera en hel mall skapar du en mall som returnerar den del som kan orsaka problemet. Det kan hjälpa dig att avgöra om du anger rätt parametrar, använder mallfunktioner korrekt och hämtar den resurs du förväntar dig.

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

Eller anta att du får distributionsfel som du tror är relaterade till felaktigt inställda beroenden. Testa mallen genom att dela upp den i förenklade mallar. Skapa först en mall som endast distribuerar en enskild resurs (till SQL Server). När du är säker på att resursen har definierats korrekt lägger du till en resurs som är beroende av den (till SQL Database). När du har dessa två resurser korrekt definierade lägger du till andra beroende resurser (t.ex. granskningsprinciper). Mellan varje testdistribution tar du bort resursgruppen för att se till att du testar beroendena på rätt sätt.

## <a name="next-steps"></a>Nästa steg

* Om du vill gå igenom en felsökningskurs kan du [gå igenom Självstudie: Felsöka Resource Manager distributioner av mallar](template-tutorial-troubleshoot.md)
* Mer information om granskningsåtgärder finns i [Granskningsåtgärder med Resource Manager](../management/view-activity-logs.md).
* Mer information om åtgärder för att fastställa felen under distributionen finns i [Visa distributionsåtgärder.](deployment-history.md)