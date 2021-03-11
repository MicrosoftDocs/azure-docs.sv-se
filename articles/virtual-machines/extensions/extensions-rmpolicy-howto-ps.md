---
title: Använd Azure Policy för att begränsa installationen av VM-tillägg (Windows)
description: Använd Azure Policy för att begränsa tillägg för distribution.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 03/23/2018
ms.openlocfilehash: 0587c2af8a90ce362fa6243e9da8a05734f0d8ec
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559757"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>Använd Azure Policy för att begränsa installationen av tillägg på virtuella Windows-datorer

Om du vill förhindra användning eller installation av vissa tillägg på dina virtuella Windows-datorer kan du skapa en Azure Policy-definition med hjälp av PowerShell för att begränsa tillägg för virtuella datorer i en resurs grupp. 

I den här självstudien används Azure PowerShell inom Cloud Shell, som uppdateras kontinuerligt till den senaste versionen. 

 

## <a name="create-a-rules-file"></a>Skapa en regel fil

För att begränsa vilka tillägg som kan installeras måste du ha en [regel](../../governance/policy/concepts/definition-structure.md#policy-rule) för att kunna identifiera tillägget.

Det här exemplet visar hur du nekar tillägg som publicerats av Microsoft. Compute genom att skapa en regel fil i Azure Cloud Shell, men om du arbetar i PowerShell lokalt kan du också skapa en lokal fil och ersätta sökvägen ($home/clouddrive) med sökvägen till den lokala filen på din dator.

I en [Cloud Shell](https://shell.azure.com/powershell)skriver du:

```azurepowershell-interactive
nano $home/clouddrive/rules.json
```

Kopiera och klistra in följande. json i filen.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

När du är färdig trycker du på **CTRL + O** och **anger** sedan för att spara filen. Stäng filen och avsluta genom att trycka på **CTRL + X** .

## <a name="create-a-parameters-file"></a>Skapa en parameter fil

Du behöver också en [parameter](../../governance/policy/concepts/definition-structure.md#parameters) fil som skapar en struktur som du kan använda för att skicka en lista över tillägg som ska blockeras. 

Det här exemplet visar hur du skapar en parameter fil för virtuella datorer i Cloud Shell, men om du arbetar i PowerShell lokalt kan du också skapa en lokal fil och ersätta sökvägen ($home/clouddrive) med sökvägen till den lokala filen på din dator.

I [Cloud Shell](https://shell.azure.com/powershell)skriver du:

```azurepowershell-interactive
nano $home/clouddrive/parameters.json
```

Kopiera och klistra in följande. json i filen.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied.",
            "displayName": "Denied extension"
        }
    }
}
```

När du är färdig trycker du på **CTRL + O** och **anger** sedan för att spara filen. Stäng filen och avsluta genom att trycka på **CTRL + X** .

## <a name="create-the-policy"></a>Skapa principen

En princip definition är ett objekt som används för att lagra den konfiguration som du vill använda. Princip definitionen använder filerna regler och parametrar för att definiera principen. Skapa en princip definition med cmdleten [New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition) .

 Princip reglerna och parametrarna är de filer som du har skapat och lagrats som JSON-filer i Cloud Shell.


```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>Tilldela principen

I det här exemplet tilldelas principen en resurs grupp med hjälp av [New-AzPolicyAssignment](/powershell/module/az.resources/new-azpolicyassignment). Alla virtuella datorer som skapats i resurs gruppen **myResourceGroup** kommer inte att kunna installera VM-agenttjänsten eller anpassade skript tillägg. 

Använd [Get-AzSubscription | Format – tabell-](/powershell/module/az.accounts/get-azsubscription) cmdlet för att hämta ditt prenumerations-ID som ska användas i stället för det som visas i exemplet.

```azurepowershell-interactive
$scope = "/subscriptions/<subscription id>/resourceGroups/myResourceGroup"
$assignment = New-AzPolicyAssignment `
   -Name "not-allowed-vmextension-windows" `
   -Scope $scope `
   -PolicyDefinition $definition `
   -PolicyParameter '{
    "notAllowedExtensions": {
        "value": [
            "VMAccessAgent",
            "CustomScriptExtension"
        ]
    }
}'
$assignment
```

## <a name="test-the-policy"></a>Testa principen

Testa principen genom att försöka använda tillägget för VM-åtkomst. Följande bör inte utföras med meddelandet "Set-AzVMAccessExtension: Resource" myVMAccess "tilläts av principen".

```azurepowershell-interactive
Set-AzVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

I portalen bör ändringen av lösen ordet Miss lyckas med "distribution av mallen misslyckades på grund av princip överträdelse." som meddelande.

## <a name="remove-the-assignment"></a>Ta bort tilldelningen

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>Ta bort principen

```azurepowershell-interactive
Remove-AzPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>Nästa steg
Mer information finns i [Azure Policy](../../governance/policy/overview.md).
