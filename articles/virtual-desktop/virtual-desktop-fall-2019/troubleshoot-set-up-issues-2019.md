---
title: Windows Virtual Desktop (klassisk) skapande av klient värd pool – Azure
description: Så här felsöker och löser du problem med klient-och värd pooler under installationen av en Windows Virtual Desktop (klassisk) klient miljö.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 12be4611f0583c6da630b9e5a0e4b5a82ebfa650
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444283"
---
# <a name="tenant-and-host-pool-creation-in-windows-virtual-desktop-classic"></a>Skapande av innehavare och värddator i Windows Virtual Desktop (klassisk)

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer (klassisk), vilket inte stöder Azure Resource Manager virtuella Skriv bords objekt i Windows. Om du försöker hantera Azure Resource Manager virtuella Windows Desktop-objekt, se [den här artikeln](../troubleshoot-set-up-issues.md).

Den här artikeln beskriver problem under den första installationen av Windows-klienten för virtuella skriv bord och den relaterade infrastrukturen för anslutningspoolen för värd.

## <a name="provide-feedback"></a>Ge feedback

Besök [Windows-Tech-communityn för Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) för att diskutera Windows Virtual Desktop-tjänsten med produkt teamet och aktiva community-medlemmar.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Hämtar Windows 10 Enterprise-avbildningen för flera sessioner

Om du vill använda Windows 10 Enterprise-avbildningen av flera sessioner går du till Azure Marketplace, väljer **Kom igång**  >  **Microsoft Windows 10** > och [Windows 10 Enterprise för virtuella skriv bord, version 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

> [!div class="mx-imgBorder"]
> ![En skärm bild av att välja Windows 10 Enterprise för virtuella skriv bord, version 1809.](../media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant&quot;></a>Skapar Windows Virtual Desktop-klient

I det här avsnittet beskrivs möjliga problem när du skapar Windows-klienten för virtuella skriv bord.

### <a name=&quot;error-aadsts650052-the-app-needs-access-to-a-service&quot;></a>Fel: AADSTS650052 appen behöver åtkomst till en tjänst.

Exempel på RAW-fel:

```Error
AADSTS650052 Message The app needs access to a service(\"{name}\") that your organization
\"{organization}\" has not subscribed to or enabled. Contact your IT Admin to review the
configuration of your service subscriptions.650052 Message The app needs access to a service
(\"{name}\") that your organization \"{organization}\" has not subscribed to or enabled.
Contact your IT Admin to review the configuration of your service subscriptions.
```

**Orsak:** Medgivande beviljas inte till virtuellt Windows-skrivbord i Azure Active Directory-instansen.

**KORRIGERA:** [Följ den här guiden](./tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop) för att bevilja medgivande.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Fel: Användaren har inte behörighet att fråga hanteringstjänsten

> [!div class="mx-imgBorder"]
> ![Skärm bild av PowerShell-fönstret där en användare inte har behörighet att fråga hanterings tjänsten.](../media/UserNotAuthorizedNewTenant.png)

Exempel på RAW-fel:

```Error
   New-RdsTenant : User isn't authorized to query the management service.
   ActivityId: ad604c3a-85c6-4b41-9b81-5138162e5559
   Powershell commands to diagnose the failure:
   Get-RdsDiagnosticActivities -ActivityId ad604c3a-85c6-4b41-9b81-5138162e5559
   At line:1 char:1
   + New-RdsTenant -Name "testDesktopTenant" -AadTenantId "01234567-89ab-c ...
   + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
       + CategoryInfo          : FromStdErr: (Microsoft.RDInf...nt.NewRdsTenant:NewRdsTenant) [New-RdsTenant], RdsPowerSh
      ellException
       + FullyQualifiedErrorId : UnauthorizedAccess,Microsoft.RDInfra.RDPowershell.Tenant.NewRdsTenant
```

**Orsak:** Användaren som är inloggad har inte tilldelats rollen TenantCreator i sina Azure Active Directory.

**KORRIGERA:** Följ instruktionerna i [tilldela program rollen TenantCreator till en användare i din Azure Active Directory-klient](tenant-setup-azure-active-directory.md#assign-the-tenantcreator-application-role). När du har följt anvisningarna får du en användare som tilldelats rollen TenantCreator.

> [!div class="mx-imgBorder"]
> ![Skärm bild av TenantCreator-rollen har tilldelats.](../media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Skapar virtuella Windows-datorer för fjärrskrivbordssessioner

Virtuella datorers VM-datorer kan skapas på flera sätt, men det virtuella Windows-teamet stöder bara VM-etablerings problem som rör [Azure Marketplace](https://azuremarketplace.microsoft.com/) -erbjudandet. Mer information finns i [problem med att använda Windows Virtual Desktop – etablera en Host pool Azure Marketplace-erbjudande](#issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering).

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Problem med att använda Windows Virtual Desktop – etablera en Host pool Azure Marketplace-erbjudande

Den virtuella Windows-datorn – etablera en mall för värd pool är tillgänglig från Azure Marketplace.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Fel: när du använder länken från GitHub visas meddelandet "skapa ett kostnads fritt konto"

> [!div class="mx-imgBorder"]
> ![Skärm bild för att skapa ett kostnads fritt konto.](../media/be615904ace9832754f0669de28abd94.png)

**Orsak 1:** Det finns inga aktiva prenumerationer i kontot som används för att logga in på Azure, eller så har det konto som används inte behörighet att Visa prenumerationerna.

**Korrigering 1:** Logga in med ett konto som har deltagar åtkomst (minst) till den prenumeration där sessionens värddatorer för virtuella datorer ska distribueras.

**Orsak 2:** Den prenumeration som används är en del av en Microsoft Cloud tjänst leverantörs klient (CSP).

**Korrigera 2:** Gå till GitHub-platsen för att **skapa och etablera nya Windows-värdar för virtuella skriv bord** och följ dessa anvisningar:

1. Högerklicka på **distribuera till Azure** och välj **Kopiera länk adress**.
2. Öppna **anteckningar** och klistra in länken.
3. Före #-symbolen infogar du namnet på CSP-slutpunktens klient organisation.
4. Öppna den nya länken i en webbläsare så att den Azure Portal läser in mallen.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

### <a name="error-you-receive-template-deployment-is-not-valid-error"></a>Fel: fel meddelandet "mall distributionen är inte giltig"

> [!div class="mx-imgBorder"]
> ![Skärm bild av "mall distribution... är inte giltigt "fel](../media/troubleshooting-marketplace-validation-error-generic.png)

Innan du vidtar särskilda åtgärder måste du kontrol lera aktivitets loggen för att se det detaljerade felet för den misslyckade distributions verifieringen.

Så här visar du felet i aktivitets loggen:

1. Avsluta det aktuella distributions erbjudandet för Azure Marketplace.
2. I det övre Sök fältet söker du efter och väljer **aktivitets logg**.
3. Hitta en aktivitet med namnet **validate Deployment** som har statusen **misslyckad** och välj aktiviteten.

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av enskild * * verifiera distribution * * aktivitet med en * * misslyckad * *-status](../media/troubleshooting-marketplace-validation-error-activity-summary.png)

4. Välj JSON och bläddra sedan ned längst ned på skärmen tills du ser fältet "statusMessage".

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av misslyckad aktivitet, med en röd ruta runt statusMessage-egenskapen för JSON-texten.](../media/troubleshooting-marketplace-validation-error-json-boxed.png)

Om din åtgärds mal len går över kvot gränsen kan du göra något av följande för att åtgärda problemet:

 - Kör Azure Marketplace med de parametrar som du använde första gången, men den här gången använder färre virtuella datorer och virtuella dator kärnor.
 - Öppna länken som visas i fältet **statusMessage** i en webbläsare för att skicka en begäran om att öka kvoten för din Azure-prenumeration för den angivna VM-SKU: n.

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Azure Resource Manager mall och DSC-fel (Desired State Configuration)

Följ dessa anvisningar för att felsöka misslyckade distributioner av Azure Resource Manager mallar och PowerShell DSC.

1. Granska fel i distributionen med hjälp av [Visa distributions åtgärder med Azure Resource Manager](../../azure-resource-manager/templates/deployment-history.md).
2. Om det inte finns några fel i distributionen granskar du fel i aktivitets loggen med [Visa aktivitets loggar för att granska åtgärder på resurser](../../azure-resource-manager/management/view-activity-logs.md).
3. När felet har identifierats använder du fel meddelandet och resurserna i [Felsöka vanliga problem med Azure-distribution med Azure Resource Manager](../../azure-resource-manager/templates/common-deployment-errors.md) för att åtgärda problemet.
4. Ta bort alla resurser som har skapats under den tidigare distributionen och försök att distribuera mallen igen.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Fel: distributionen misslyckades.... \<hostname> /JoinDomain

> [!div class="mx-imgBorder"]
> ![Det gick inte att distribuera skärm bilden.](../media/e72df4d5c05d390620e07f0d7328d50f.png)

Exempel på RAW-fel:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Orsak 1:** De autentiseringsuppgifter som angavs för att ansluta virtuella datorer till domänen är felaktiga.

**Korrigering 1:** Se felet "felaktiga autentiseringsuppgifter" för virtuella datorer som inte är anslutna till domänen i [VM-konfigurationen för fjärrskrivbordssessioner](troubleshoot-vm-configuration-2019.md).

**Orsak 2:** Domän namnet matchar inte.

**Korrigera 2:** Se [fel: det går inte att matcha domän namnet](troubleshoot-vm-configuration-2019.md#error-domain-name-doesnt-resolve) i [VM-konfigurationen för Session Host](troubleshoot-vm-configuration-2019.md).

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
> ![Skärm bilden av distributionen misslyckades, det gick inte att etablerings status för terminalen.](../media/7aaf15615309c18a984673be73ac969a.png)

**Orsak 1:** Tillfälligt fel i Windows-miljön för virtuella datorer.

**Orsak 2:** Tillfälligt fel med anslutning.

**KORRIGERA:** Bekräfta att Windows Virtual Desktop-miljön är felfri genom att logga in med PowerShell. Slutför VM-registreringen manuellt i [skapa en adresspool med PowerShell](create-host-pools-powershell-2019.md).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Fel: Det angivna administratörsanvändarnamnet är inte tillåtet

> [!div class="mx-imgBorder"]
> ![Skärm bild av distributionen misslyckades i vilken en angiven administratör inte tillåts.](../media/f2b3d3700e9517463ef88fa41875bac9.png)

Exempel på RAW-fel:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**Orsak:** Det angivna lösen ordet innehåller förbjudna del strängar (admin, administratör, rot).

**KORRIGERA:** Uppdatera användar namn eller Använd olika användare.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Fel: Den virtuella datorn rapporterade ett fel vid bearbetningen av tillägget

> [!div class="mx-imgBorder"]
> ![Skärm bild av resurs åtgärden slutfördes med etablerings statusen för terminalen i distributionen misslyckades.](../media/49c4a1836a55d91cd65125cf227f411f.png)

Exempel på RAW-fel:

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-provision-host-pool-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
 { "provisioningOperation": "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T21:43:05.1416423Z",
 "duration": "PT7M56.8150879S", "trackingId": "43c4f71f-557c-4abd-80c3-01f545375455", "statusCode": "Conflict",
 "statusMessage": { "status": "Failed", "error": { "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'.
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few:
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message:
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] } }, "targetResource":
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.
 Compute/virtualMachines/desktop-1/extensions/dscextension",
 "resourceType": "Microsoft.Compute/virtualMachines/extensions", "resourceName": "desktop-1/dscextension" } }}
```

**Orsak:** PowerShell DSC-tillägget kunde inte få administratörs åtkomst på den virtuella datorn.

**KORRIGERA:** Bekräfta att användar namnet och lösen ordet har administrativ åtkomst till den virtuella datorn och kör Azure Resource Manager mallen igen.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Fel: DeploymentFailed – PowerShell DSC-konfigurationen ' FirstSessionHost ' slutfördes med fel

> [!div class="mx-imgBorder"]
> ![Skärm bild av distributionen fungerar inte med PowerShell DSC-konfigurationen FirstSessionHost slutfördes med fel.](../media/64870370bcbe1286906f34cf0a8646ab.png)

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

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Fel: Användaren har inte behörighet att fråga hanteringstjänsten

Exempel på RAW-fel:

```Error
"response": { "content": { "startTime": "2019-04-01T17:45:33.3454563+00:00", "endTime": "2019-04-01T17:48:52.4392099+00:00",
"status": "Failed", "error": { "code": "VMExtensionProvisioningError", "message": "VM has reported a failure when processing
extension 'dscextension'. Error message: \"DSC Configuration 'FirstSessionHost' completed with error(s).
Following are the first few: PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource
 functionality with error message: User is not authorized to query the management service.
\nActivityId: 1b4f2b37-59e9-411e-9d95-4f7ccd481233\nPowershell commands to diagnose the failure:
\nGet-RdsDiagnosticActivities -ActivityId 1b4f2b37-59e9-411e-9d95-4f7ccd481233\n
The SendConfigurationApply function did not succeed.\"." }, "name": "2c3272ec-d25b-47e5-8d70-a7493e9dc473" } } }}
```

**Orsak:** Den angivna Windows-innehavaradministratör för virtuella skriv bord har ingen giltig roll tilldelning.

**KORRIGERA:** Användaren som skapade den virtuella Windows-klienten för fjärr skrivbord måste logga in på Windows Virtual Desktop PowerShell och tilldela det försök användaren att tilldela en roll tilldelning. Om du kör parametrarna för GitHub Azure Resource Manager-mallen följer du de här anvisningarna med PowerShell-kommandon:

```PowerShell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName "RDS Contributor" -SignInName <UPN>
```

### <a name="error-user-requires-azure-ad-multi-factor-authentication-mfa"></a>Fel: användaren måste använda Azure AD Multi-Factor Authentication (MFA)

> [!div class="mx-imgBorder"]
> ![Skärm bild av distributionen misslyckades på grund av brist på Multi-Factor Authentication (MFA)](../media/MFARequiredError.png)

Exempel på RAW-fel:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**Orsak:** Den angivna Windows-innehavaradministratör för virtuella skriv bord kräver Azure AD Multi-Factor Authentication (MFA) för att logga in.

**KORRIGERA:** Skapa ett huvud namn för tjänsten och tilldela det en roll för din Windows-klient för virtuella skriv bord genom att följa stegen i [Självstudier: skapa tjänstens huvud namn och roll tilldelningar med PowerShell](create-service-principal-role-powershell.md). När du har verifierat att du kan logga in på Windows Virtual Desktop med tjänstens huvud namn, kör du om Azure Marketplace-erbjudandet eller GitHub Azure Resource Manager-mallen, beroende på vilken metod du använder. Följ anvisningarna nedan för att ange rätt parametrar för din metod.

Om du använder Azure Marketplace-erbjudandet anger du värden för följande parametrar för att autentisera till Windows Virtual Desktop på rätt sätt:

- RDS-ägare för Windows Virtual Desktop-klient: tjänstens huvud namn
- Program-ID: program-ID för det nya tjänst objekt som du skapade
- Lösen ord/bekräfta lösen ord: lösen ords hemligheten som du skapade för tjänstens huvud namn
- Azure AD-klient-ID: Azure AD-klient-ID för tjänstens huvud namn som du skapade

Om du kör GitHub-mallen för Azure Resource Manager anger du värden för följande parametrar för att autentisera till Windows Virtual Desktop korrekt:

- Klient administratörs User Principal Name (UPN) eller program-ID: program identifieringen för det nya tjänst objekt som du har skapat
- Administratörs lösen ord för klient organisation: lösen ords hemligheten som du skapade för tjänstens huvud namn
- IsServicePrincipal: **Sant**
- AadTenantId: Azure AD-klient-ID för tjänstens huvud namn som du skapade

### <a name="error-vmsubnet-not-available-when-configuring-virtual-networks"></a>Fel: vmSubnet är inte tillgängligt vid konfigurering av virtuella nätverk

**Orsak:** I Windows-mallen för virtuella Skriv bords platser visar användar gränssnittet bara undernät som har minst så många IP-adresser som är tillgängliga som det totala antalet virtuella datorer som anges i mallen. Det faktiska antalet tillgängliga IP-adresser i under nätet behöver bara vara lika med antalet nya virtuella datorer som distribueras, men det kan inte beräknas av det aktuella användar gränssnittet.

**KORRIGERA:** Du kan ange ett undernät med minst så många IP-adresser som är tillgängliga som antalet virtuella datorer som läggs till genom att inte använda Marketplace-ANVÄNDARGRÄNSSNITTET, detta kan göras genom att ange under nätets namn i parametern "**existingSubnetName**" när du [distribuerar om en befintlig distribution](expand-existing-host-pool-2019.md#redeploy-from-azure) eller [distribuerar med hjälp av den underliggande arm-mallen från GitHub](create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool).

## <a name="next-steps"></a>Nästa steg

- En översikt över fel sökning av virtuella Windows-datorer och eskalerade spår finns i [fel söknings översikt, feedback och support](troubleshoot-set-up-overview-2019.md).
- Information om hur du felsöker problem när du konfigurerar en virtuell dator (VM) i Windows Virtual Desktop finns i [konfiguration av Session Host-dator](troubleshoot-vm-configuration-2019.md).
- Information om hur du felsöker problem med klient anslutningar för virtuella Windows-datorer finns i [Windows Virtual Desktop Service Connections](troubleshoot-service-connection-2019.md).
- Information om hur du felsöker problem med fjärr skrivbords klienter finns i [Felsöka fjärr skrivbords klienten](../troubleshoot-client.md)
- Information om hur du felsöker problem när du använder PowerShell med Windows Virtual Desktop finns i [Windows Virtual Desktop PowerShell](troubleshoot-powershell-2019.md).
- Mer information om tjänsten finns i [Windows Virtual Desktop-miljö](environment-setup-2019.md).
- Information om hur du går igenom en fel söknings kurs finns i [Självstudier: Felsöka distributioner av Resource Manager-mallar](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Mer information om gransknings åtgärder finns i [gransknings åtgärder med Resource Manager](../../azure-resource-manager/management/view-activity-logs.md).
- Information om åtgärder för att fastställa felen under distributionen finns i [Visa distributions åtgärder](../../azure-resource-manager/templates/deployment-history.md).