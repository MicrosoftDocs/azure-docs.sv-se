---
title: Skapa Windows-miljö för virtuella Skriv bords miljö – Azure
description: Så här felsöker och löser du problem med klient-och värd pooler under installationen av en Windows Virtual Desktop-miljö.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 09/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0a5439a9d1fd43154379c1dc1a95a6e98b6e877b
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539635"
---
# <a name="host-pool-creation"></a>Skapa värdpool

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder Windows Virtual Desktop (klassisk) utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/troubleshoot-set-up-issues-2019.md).

Den här artikeln beskriver problem under den första installationen av Windows-klienten för virtuella skriv bord och den relaterade infrastrukturen för anslutningspoolen för värd.

## <a name="provide-feedback"></a>Ge feedback

Besök [Windows-Tech-communityn för Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) för att diskutera Windows Virtual Desktop-tjänsten med produkt teamet och aktiva community-medlemmar.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Hämtar Windows 10 Enterprise-avbildningen för flera sessioner

Om du vill använda Windows 10 Enterprise multi-session-avbildningen går du till Azure Marketplace, väljer **Kom igång**  >  **Microsoft Windows 10** > och [Windows 10 Enterprise multi-session, version 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

## <a name="issues-with-using-the-azure-portal-to-create-host-pools"></a>Problem med att använda Azure Portal för att skapa värdar för pooler

### <a name="error-create-a-free-account-appears-when-accessing-the-service"></a>Fel: "skapa ett kostnads fritt konto" visas vid åtkomst till tjänsten

> [!div class="mx-imgBorder"]
> ![En bild som visar Azure Portal som visar meddelandet "skapa ett kostnads fritt konto"](media/create-new-account.png)

**Orsak**: det finns inga aktiva prenumerationer på det konto som du loggade in på Azure med, eller så har inte kontot behörighet att Visa prenumerationerna.

**Korrigera**: Logga in på prenumerationen där du ska distribuera de virtuella datorerna för sessionens värd (VM) med ett konto som har minst åtkomst på deltagar nivå.

### <a name="error-exceeding-quota-limit"></a>Fel: "överskrider kvot gränsen"

Om din åtgärd går över kvot gränsen kan du göra något av följande:

- Skapa en ny värdbaserad pool med samma parametrar men färre virtuella datorer och virtuella dator kärnor.

- Öppna länken som visas i fältet statusMessage i en webbläsare för att skicka en begäran om att öka kvoten för din Azure-prenumeration för den angivna VM-SKU: n.

### <a name="error-cant-see-user-assignments-in-app-groups"></a>Fel: det går inte att se användar tilldelningar i app-grupper.

Orsak: det här felet uppstår vanligt vis när du har flyttat prenumerationen från 1 Azure Active Directory (AD) klient till en annan. Om dina gamla tilldelningar fortfarande är knutna till den gamla Azure AD-klienten kommer Azure Portal att förlora dem.

KORRIGERA: du måste tilldela om användare till app-grupper.

## <a name="azure-resource-manager-template-errors"></a>Azure Resource Manager mal linne fel

Följ dessa anvisningar för att felsöka misslyckade distributioner av Azure Resource Manager mallar och PowerShell DSC.

1. Granska fel i distributionen med hjälp av [Visa distributions åtgärder med Azure Resource Manager](../azure-resource-manager/templates/deployment-history.md).
2. Om det inte finns några fel i distributionen granskar du fel i aktivitets loggen med [Visa aktivitets loggar för att granska åtgärder på resurser](../azure-resource-manager/management/view-activity-logs.md).
3. När felet har identifierats använder du fel meddelandet och resurserna i [Felsöka vanliga problem med Azure-distribution med Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md) för att åtgärda problemet.
4. Ta bort alla resurser som har skapats under den tidigare distributionen och försök att distribuera mallen igen.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Fel: distributionen misslyckades.... \<hostname> /JoinDomain

> [!div class="mx-imgBorder"]
> ![Det gick inte att distribuera skärm bilden.](media/failure-joindomain.png)

Exempel på RAW-fel:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Orsak 1:** De autentiseringsuppgifter som angavs för att ansluta virtuella datorer till domänen är felaktiga.

**Korrigering 1:** Se felet "felaktiga autentiseringsuppgifter" för virtuella datorer som inte är anslutna till domänen i [VM-konfigurationen för fjärrskrivbordssessioner](troubleshoot-vm-configuration.md).

**Orsak 2:** Domän namnet matchar inte.

**Korrigera 2:** Se [fel: det går inte att matcha domän namnet](troubleshoot-vm-configuration.md#error-domain-name-doesnt-resolve) i [VM-konfigurationen för Session Host](troubleshoot-vm-configuration.md).

**Orsak 3:** Din DNS-konfiguration för virtuellt nätverk (VNET) är inställd på **default**.

Åtgärda detta genom att göra följande:

1. Öppna Azure Portal och gå till fliken **virtuella nätverk** .
2. Hitta ditt VNET och välj **DNS-servrar**.
3. Menyn DNS-servrar bör visas på höger sida av skärmen. På menyn väljer du **anpassad**.
4. Kontrol lera att de DNS-servrar som anges under anpassad matchning av domänkontrollanten eller Active Directorys domänen. Om du inte ser din DNS-server kan du lägga till den genom att ange dess värde i fältet **Lägg till DNS-Server** .

### <a name="error-your-deployment-failedunauthorized"></a>Fel: Distributionen misslyckades …\Unauthorized

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**Orsak:** Den prenumeration du använder är en typ som inte har åtkomst till nödvändiga funktioner i den region där kunden försöker distribuera. Till exempel kan MSDN, kostnads fria eller utbildnings prenumerationer Visa det här felet.

**KORRIGERA:** Ändra din prenumerations typ eller region till en som har åtkomst till de funktioner som krävs.

### <a name="error-vmextensionprovisioningerror"></a>Fel: VMExtensionProvisioningError

> [!div class="mx-imgBorder"]
> ![Skärm bilden av distributionen misslyckades, det gick inte att etablerings status för terminalen.](media/failure-vmextensionprovisioning.png)

**Orsak 1:** Tillfälligt fel i Windows-miljön för virtuella datorer.

**Orsak 2:** Tillfälligt fel med anslutning.

**KORRIGERA:** Bekräfta att Windows Virtual Desktop-miljön är felfri genom att logga in med PowerShell. Slutför VM-registreringen manuellt i [skapa en adresspool med PowerShell](create-host-pools-powershell.md).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Fel: Det angivna administratörsanvändarnamnet är inte tillåtet

> [!div class="mx-imgBorder"]
> ![Skärm bild av distributionen misslyckades i vilken en angiven administratör inte tillåts.](media/failure-username.png)

Exempel på RAW-fel:

```Error
 { …{ "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } … }
```

**Orsak:** Det angivna lösen ordet innehåller förbjudna del strängar (admin, administratör, rot).

**KORRIGERA:** Uppdatera användar namn eller Använd olika användare.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Fel: Den virtuella datorn rapporterade ett fel vid bearbetningen av tillägget

> [!div class="mx-imgBorder"]
> ![Skärm bild av resurs åtgärden slutfördes med etablerings statusen för terminalen i distributionen misslyckades.](media/failure-processing.png)

Exempel på RAW-fel:

```Error
{ … "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'.
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few:
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message:
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] … }
```

**Orsak:** PowerShell DSC-tillägget kunde inte få administratörs åtkomst på den virtuella datorn.

**KORRIGERA:** Bekräfta att användar namnet och lösen ordet har administrativ åtkomst till den virtuella datorn och kör Azure Resource Manager mallen igen.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Fel: DeploymentFailed – PowerShell DSC-konfigurationen ' FirstSessionHost ' slutfördes med fel

> [!div class="mx-imgBorder"]
> ![Skärm bild av distributionen fungerar inte med PowerShell DSC-konfigurationen FirstSessionHost slutfördes med fel.](media/failure-dsc.png)

Exempel på RAW-fel:

```Error
{
    "code": "DeploymentFailed",
   "message": "At least one resource deployment operation failed. Please list
 deployment operations for details. 4 Please see https://aka.ms/arm-debug for usage details.",
 "details": [
         { "code": "Conflict",
         "message": "{\r\n \"status\": \"Failed\",\r\n \"error\": {\r\n \"code\":
         \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource
         operation completed with terminal provisioning state 'Failed'.\",\r\n
         \"details\": [\r\n {\r\n \"code\":
        \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has
              reported a failure when processing extension 'dscextension'.
              Error message: \\\"DSC Configuration 'FirstSessionHost'
              completed with error(s). Following are the first few:
              PowerShell DSC resource MSFT ScriptResource failed to
              execute Set-TargetResource functionality with error message:
              One or more errors occurred. The SendConfigurationApply
              function did not succeed.\\\".\"\r\n }\r\n ]\r\n }\r\n}"  }

```

**Orsak:** PowerShell DSC-tillägget kunde inte få administratörs åtkomst på den virtuella datorn.

**KORRIGERA:** Bekräfta att användar namnet och lösen ordet har administratörs behörighet på den virtuella datorn och kör Azure Resource Manager mallen igen.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Fel: DeploymentFailed – InvalidResourceReference

Exempel på RAW-fel:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/networkInterfaces/erd. Please make sure that
the referenced resource exists, and that both resources are in the same
region.\\\",\\r\\n\\\"details\\\": []\\r\\n }\\r\\n}\"\r\n }\r\n ]\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Orsak:** En del av resurs gruppens namn används för vissa resurser som skapas av mallen. På grund av namnet som matchar befintliga resurser kan mallen välja en befintlig resurs från en annan grupp.

**KORRIGERA:** När du kör Azure Resource Manager-mallen för att distribuera en virtuell dator med en virtuell dator, ska du göra de två första tecknen unika för prenumerationens resurs grupp namn.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Fel: DeploymentFailed – InvalidResourceReference

Exempel på RAW-fel:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/DEMO/providers/Microsoft.Network/networkInterfaces
/EXAMPLE was not found. Please make sure that the referenced resource exists, and that both
resources are in the same region.\\\",\\r\\n \\\"details\\\": []\\r\\n }\\r\\n}\"\r\n
}\r\n ]\r\n }\r\n ]\r\n }\r\n\
```

**Orsak:** Det här felet beror på att det nätverkskort som skapats med Azure Resource Manager-mallen har samma namn som ett annat nätverkskort som redan finns i VNET.

**KORRIGERA:** Använd ett annat prefix för värd.

### <a name="error-deploymentfailed--error-downloading"></a>Fel: DeploymentFailed – fel vid hämtning

Exempel på RAW-fel:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-provision-host-pool-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-prod
/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**Orsak:** Det här felet beror på en statisk väg, brand Väggs regel eller NSG blockerar hämtningen av zip-filen som är kopplad till Azure Resource Manager-mallen.

**KORRIGERA:** Ta bort blockerande statisk väg, brand Väggs regel eller NSG. Du kan också öppna JSON-filen Azure Resource Manager mall i en text redigerare, ta länken till zip-filen och ladda ned resursen till en tillåten plats.

### <a name="error-cant-delete-a-session-host-from-the-host-pool-after-deleting-the-vm"></a>Fel: det går inte att ta bort en sessionsvariabel från värddatorn efter att den virtuella datorn har tagits bort

**Orsak:** Du måste ta bort värd för sessionen innan du tar bort den virtuella datorn.

**KORRIGERA:** Placera sessionen i dränerings läge, logga ut alla användare från värd för sessionen och ta sedan bort värden.

## <a name="next-steps"></a>Nästa steg

- En översikt över fel sökning av virtuella Windows-datorer och eskalerade spår finns i [fel söknings översikt, feedback och support](troubleshoot-set-up-overview.md).
- Information om hur du felsöker problem när du konfigurerar en virtuell dator (VM) i Windows Virtual Desktop finns i [konfiguration av Session Host-dator](troubleshoot-vm-configuration.md).
- Information om hur du felsöker problem som rör agenten för virtuella Windows-datorer eller sessioner finns i [Felsöka vanliga problem med Windows Virtual Desktop agent](troubleshoot-agent.md).
- Information om hur du felsöker problem med klient anslutningar för virtuella Windows-datorer finns i [Windows Virtual Desktop Service Connections](troubleshoot-service-connection.md).
- Information om hur du felsöker problem med fjärr skrivbords klienter finns i [Felsöka fjärr skrivbords klienten](troubleshoot-client.md)
- Information om hur du felsöker problem när du använder PowerShell med Windows Virtual Desktop finns i [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Mer information om tjänsten finns i [Windows Virtual Desktop-miljö](environment-setup.md).
- Information om hur du går igenom en fel söknings kurs finns i [Självstudier: Felsöka distributioner av Resource Manager-mallar](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Mer information om gransknings åtgärder finns i [gransknings åtgärder med Resource Manager](../azure-resource-manager/management/view-activity-logs.md).
- Information om åtgärder för att fastställa felen under distributionen finns i [Visa distributions åtgärder](../azure-resource-manager/templates/deployment-history.md).
