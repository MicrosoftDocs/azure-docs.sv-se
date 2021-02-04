---
title: Sluta använda hanterad identitet VM-tillägg – Azure AD
description: Steg-för-steg-instruktioner för att sluta använda VM-tillägget och börja använda Azure-Instance Metadata Service (IMDS) för autentisering.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/03/2020
ms.author: barclayn
ROBOTS: NOINDEX
ms.openlocfilehash: dca5f9ed2911ae3042fb9871f849212ec18b1b58
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539391"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>Så här slutar du använda tillägget för hanterade identiteter för virtuella datorer och börjar använda Azure-Instance Metadata Service

## <a name="virtual-machine-extension-for-managed-identities"></a>Tillägg för virtuell dator för hanterade identiteter

Tillägget för den virtuella datorn för hanterade identiteter används för att begära token för en hanterad identitet i den virtuella datorn. Arbets flödet består av följande steg:

1. Först anropar arbets belastningen i resursen den lokala slut punkten `http://localhost/oauth2/token` för att begära en åtkomsttoken.
2. Tillägget för den virtuella datorn använder sedan autentiseringsuppgifterna för den hanterade identiteten för att begära en åtkomsttoken från Azure AD. 
3. Åtkomsttoken returneras till anroparen och kan användas för att autentisera till tjänster som stöder Azure AD-autentisering, t. ex. Azure Key Vault eller Azure Storage.

På grund av flera begränsningar som beskrivs i nästa avsnitt, har Managed Identity VM-tillägget förfallit till förmån för att använda motsvarande slut punkt i Azure-Instance Metadata Service (IMDS)

### <a name="provision-the-extension"></a>Etablera tillägget 

När du konfigurerar en virtuell dator eller skalnings uppsättning för virtuell dator som har en hanterad identitet kan du välja att etablera hanterade identiteter för VM-tillägget för Azure-resurser med hjälp av `-Type` parametern på cmdleten [set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) . Du kan skicka antingen `ManagedIdentityExtensionForWindows` eller `ManagedIdentityExtensionForLinux` , beroende på typen av virtuell dator och ge den namnet med hjälp av `-Name` parametern. `-Settings`Parametern anger den port som används av OAuth-token för hämtning av token:

```azurepowershell-interactive
$settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

Du kan också använda mallen för Azure Resource Manager distribution för att etablera VM-tillägget genom att lägga till följande JSON till `resources` avsnittet i mallen (Använd `ManagedIdentityExtensionForLinux` för namn-och typ element för Linux-versionen).

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
    "apiVersion": "2018-06-01",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.ManagedIdentity",
        "type": "ManagedIdentityExtensionForWindows",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "port": 50342
        }
    }
}
```
    
    
Om du arbetar med skalnings uppsättningar för virtuella datorer kan du även etablera hanterade identiteter för Azure-resursers skalnings uppsättnings tillägg för virtuella datorer med cmdleten [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) . Du kan skicka antingen `ManagedIdentityExtensionForWindows` eller `ManagedIdentityExtensionForLinux` , beroende på typ av skalnings uppsättning för virtuell dator och ge den namnet med hjälp av `-Name` parametern. `-Settings`Parametern anger den port som används av OAuth-token för hämtning av token:

   ```azurepowershell-interactive
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
Om du vill etablera tillägget för skalnings uppsättning för virtuell dator med mallen för Azure Resource Manager distribution lägger du till följande JSON till `extensionpProfile` -avsnittet i mallen (Använd `ManagedIdentityExtensionForLinux` för namn-och typ element för Linux-versionen).

```json
"extensionProfile": {
    "extensions": [
        {
            "name": "ManagedIdentityWindowsExtension",
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
                },
                "protectedSettings": {}
            }
        }
```

Etableringen av tillägget för den virtuella datorn kan Miss lyckas på grund av misslyckade DNS-sökningar. Om detta händer startar du om den virtuella datorn och försöker igen. 

### <a name="remove-the-extension"></a>Ta bort tillägget 
Om du vill ta bort tillägget använder du `-n ManagedIdentityExtensionForWindows` eller `-n ManagedIdentityExtensionForLinux` växlar (beroende på typ av virtuell dator) med [AZ VM Extension Delete](/cli/azure/vm/)eller [AZ VMSS Extension Delete](/cli/azure/vmss) för Virtual Machine Scale Sets med Azure CLI eller `Remove-AzVMExtension` för PowerShell:

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```azurepowershell-interactive
Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
```

### <a name="acquire-a-token-using-the-virtual-machine-extension"></a>Hämta en token med hjälp av tillägget för virtuell dator

Följande är en exempel förfrågan som använder hanterade identiteter för VM-tillägget för virtuella Azure-resurser:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Element | Beskrivning |
| ------- | ----------- |
| `GET` | HTTP-verbet som anger att du vill hämta data från slut punkten. I det här fallet en OAuth-åtkomsttoken. | 
| `http://localhost:50342/oauth2/token` | Hanterade identiteter för Azure-resursers slut punkt, där 50342 är standard porten och kan konfigureras. |
| `resource` | En frågesträngparametern som anger URI för app-ID för mål resursen. Den visas också i `aud` (Audience)-anspråket för Utfärdad token. Det här exemplet begär en token för att få åtkomst till Azure Resource Manager, som har en app-ID-URI för `https://management.azure.com/` . |
| `Metadata` | Ett huvud fält för HTTP-begäran som krävs av hanterade identiteter för Azure-resurser som ett skydd mot Server sidans förfalsknings attack (SSRF). Värdet måste anges till "true", i gemener.|
| `object_id` | Valfritt En frågesträngparametern, som anger object_id för den hanterade identitet som du vill ha token för. Krävs om den virtuella datorn har flera användare tilldelade hanterade identiteter.|
| `client_id` | Valfritt En frågesträngparametern, som anger client_id för den hanterade identitet som du vill ha token för. Krävs om den virtuella datorn har flera användare tilldelade hanterade identiteter.|


Exempel svar:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Element | Beskrivning |
| ------- | ----------- |
| `access_token` | Den begärda åtkomsttoken. När du anropar en skyddad REST API, bäddas token in i `Authorization` fältet begär ande huvud som en "Bearer"-token, vilket gör att API: et kan autentisera anroparen. | 
| `refresh_token` | Används inte av hanterade identiteter för Azure-resurser. |
| `expires_in` | Antalet sekunder som åtkomst-token fortsätter att vara giltig, innan det går ut, från tid för utfärdande. Tiden för utfärdande kan hittas i token- `iat` anspråk. |
| `expires_on` | TimeSpan när åtkomsttoken upphör att gälla. Datumet visas som antalet sekunder från "1970-01-01T0:0: 0Z UTC" (motsvarar token: s `exp` anspråk). |
| `not_before` | TimeSpan när åtkomsttoken börjar gälla och kan godkännas. Datumet visas som antalet sekunder från "1970-01-01T0:0: 0Z UTC" (motsvarar token: s `nbf` anspråk). |
| `resource` | Resursen som åtkomsttoken begärdes för, som matchar `resource` frågesträngparametern för begäran. |
| `token_type` | Typen av token, som är en "Bearer"-åtkomsttoken, vilket innebär att resursen kan ge till gång till denna token. |


### <a name="troubleshoot-the-virtual-machine-extension"></a>Felsöka tillägget för virtuell dator 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>Starta om tillägget för den virtuella datorn efter ett haveri

Om tillägget stoppas i Windows och vissa versioner av Linux kan följande cmdlet användas för att starta om den manuellt:

```azurepowershell-interactive
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Plats: 
- Tilläggs namn och typ för Windows är: `ManagedIdentityExtensionForWindows`
- Tilläggs namn och-typ för Linux är: `ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>"Automation-skript" Miss lyckas vid försök att exportera schema för hanterade identiteter för Azure-resursers tillägg

När hanterade identiteter för Azure-resurser är aktiverade på en virtuell dator visas följande fel vid försök att använda funktionen "Automation-skript" för den virtuella datorn eller resurs gruppen:

![Hanterade identiteter för Azure-resurser Automation skript export fel](./media/howto-migrate-vm-extension/automation-script-export-error.png)

Hanterade identiteter för virtuella dator tillägg i Azure-resurser stöder för närvarande inte möjligheten att exportera sitt schema till en resurs grupps mall. Det innebär att den genererade mallen inte visar konfigurations parametrar för att aktivera hanterade identiteter för Azure-resurser på resursen. Dessa avsnitt kan läggas till manuellt genom att följa exemplen i [Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med hjälp av mallar](qs-configure-template-windows-vm.md).

När schema export funktionen blir tillgänglig för hanterade identiteter för Azure-resursers tillägg för virtuella datorer (planerat för utfasning i januari 2019) visas den i [Exportera resurs grupper som innehåller VM-tillägg](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

## <a name="limitations-of-the-virtual-machine-extension"></a>Begränsningar för tillägget för virtuell dator 

Det finns flera större begränsningar för att använda tillägget för virtuella datorer. 

 * Den mest allvarliga begränsningen är att de autentiseringsuppgifter som används för att begära token lagras på den virtuella datorn. En angripare som lyckas med att skada den virtuella datorn kan stjäla autentiseringsuppgifterna. 
 * Dessutom stöds inte tillägget för virtuella datorer fortfarande av flera Linux-distributioner, med en enorm utvecklings kostnad för att ändra, bygga och testa tillägget på var och en av dessa distributioner. För närvarande stöds endast följande Linux-distributioner: 
    * CoreOS Stable
    * CentOS 7,1 
    * Red Hat 7,2 
    * Ubuntu 15,04 
    * Ubuntu 16.04
 * Det är en prestanda som påverkar distributionen av virtuella datorer med hanterade identiteter, eftersom tillägget för virtuell dator också måste tillhandahållas. 
 * Slutligen stöder tillägget för virtuella datorer bara att ha 32 användare tilldelade hanterade identiteter per virtuell dator. 

## <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

[Azure instance metadata service (IMDS)](../../virtual-machines/windows/instance-metadata-service.md) är en REST-slutpunkt som ger information om att köra virtuella dator instanser som kan användas för att hantera och konfigurera dina virtuella datorer. Slut punkten är tillgänglig för en välkänd icke-flyttbar IP-adress ( `169.254.169.254` ) som bara kan nås från den virtuella datorn.

Det finns flera fördelar med att använda Azure-IMDS för att begära token. 

1. Tjänsten är extern för den virtuella datorn, och därför finns autentiseringsuppgifterna som används av hanterade identiteter inte längre på den virtuella datorn. De är i stället värdbaserade och skyddade på värd datorn för den virtuella Azure-datorn.   
2. Alla Windows-och Linux-operativsystem som stöds på Azure-IaaS kan använda hanterade identiteter.
3. Distributionen är snabbare och enklare eftersom det virtuella dator tillägget inte längre behöver tillhandahållas.
4. Med IMDS-slutpunkten kan du tilldela upp till 1000 användare tilldelade hanterade identiteter till en enda virtuell dator.
5. Det finns ingen betydande ändring av de begär Anden som använder IMDS i stället för de som använder tillägget för virtuella datorer, och därför är det ganska enkelt att Porta över befintliga distributioner som för närvarande använder tillägget för virtuell dator.

Av dessa skäl är Azure IMDS-tjänsten det facto-sätt som krävs för att begära token, när tillägget för den virtuella datorn är föråldrat. 


## <a name="next-steps"></a>Nästa steg

* [Använda hanterade identiteter för Azure-resurser på en virtuell Azure-dator för att få en åtkomsttoken](how-to-use-vm-token.md)
* [Azure Instance Metadata Service](../../virtual-machines/windows/instance-metadata-service.md)
