---
title: Använd Azure Policy för att begränsa installationen av VM-tillägg (Linux)
description: Använd Azure Policy för att begränsa distributioner av VM-tillägg.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 03/23/2018
ms.openlocfilehash: 582f690c00ff8bdd06e4d2867724aa482bcacaf3
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559978"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-linux-vms"></a>Använd Azure Policy för att begränsa installationen av tillägg på virtuella Linux-datorer

Om du vill förhindra användning eller installation av vissa tillägg på virtuella Linux-datorer kan du skapa en Azure Policy-definition med hjälp av CLI för att begränsa tillägg för virtuella datorer i en resurs grupp. 

I den här självstudien används CLI i Azure Cloud Shell, som uppdateras kontinuerligt till den senaste versionen. Om du vill köra Azure CLI lokalt måste du installera version 2.0.26 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="create-a-rules-file"></a>Skapa en regel fil

För att begränsa vilka tillägg som kan installeras måste du ha en [regel](../../governance/policy/concepts/definition-structure.md#policy-rule) för att kunna identifiera tillägget.

Det här exemplet visar hur du nekar installation av tillägg som publicerats av "Microsoft. OSTCExtensions" genom att skapa en regel fil i Azure Cloud Shell, men om du arbetar i CLI lokalt kan du också skapa en lokal fil och ersätta sökvägen (~/clouddrive) med sökvägen till den lokala filen på din dator.

I en [bash Cloud Shell](https://shell.azure.com/bash)skriver du:

```bash
vim ~/clouddrive/azurepolicy.rules.json
```

Kopiera och klistra in följande. json i filen.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.OSTCExtensions/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/publisher",
                "equals": "Microsoft.OSTCExtensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

När du är färdig trycker du på **ESC** -tangenten och skriver sedan **: Wq** för att spara och stänga filen.


## <a name="create-a-parameters-file"></a>Skapa en parameter fil

Du behöver också en [parameter](../../governance/policy/concepts/definition-structure.md#parameters) fil som skapar en struktur som du kan använda för att skicka en lista över tillägg som ska blockeras. 

Det här exemplet visar hur du skapar en parameter fil för virtuella Linux-datorer i Cloud Shell, men om du arbetar i CLI lokalt kan du också skapa en lokal fil och ersätta sökvägen (~/clouddrive) med sökvägen till den lokala filen på din dator.

I [bash Cloud Shell](https://shell.azure.com/bash)skriver du:

```bash
vim ~/clouddrive/azurepolicy.parameters.json
```

Kopiera och klistra in följande. json i filen.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied. Example: CustomScriptForLinux, VMAccessForLinux etc.",
            "displayName": "Denied extension"
        }
    }
}
```

När du är färdig trycker du på **ESC** -tangenten och skriver sedan **: Wq** för att spara och stänga filen.

## <a name="create-the-policy"></a>Skapa principen

En princip definition är ett objekt som används för att lagra den konfiguration som du vill använda. Princip definitionen använder filerna regler och parametrar för att definiera principen. Skapa princip definitionen med [AZ-princip definition Create](/cli/azure/role/assignment).

I det här exemplet är reglerna och parametrarna de filer som du har skapat och lagrat som JSON-filer i Cloud Shell.

```azurecli-interactive
az policy definition create \
   --name 'not-allowed-vmextension-linux' \
   --display-name 'Block VM Extensions' \
   --description 'This policy governs which VM extensions that are blocked.' \
   --rules '~/clouddrive/azurepolicy.rules.json' \
   --params '~/clouddrive/azurepolicy.parameters.json' \
   --mode All
```


## <a name="assign-the-policy"></a>Tilldela principen

I det här exemplet tilldelas principen en resurs grupp med [AZ policy Assignment Create](/cli/azure/policy/assignment). Alla virtuella datorer som skapats i resurs gruppen **myResourceGroup** kommer inte att kunna installera Linux VM-åtkomst eller anpassade skript tillägg för Linux. Resurs gruppen måste finnas innan du kan tilldela principen.

Använd [AZ Account List](/cli/azure/account) för att hämta ditt PRENUMERATIONS-ID i stället för det som visas i exemplet.


```azurecli-interactive
az policy assignment create \
   --name 'not-allowed-vmextension-linux' \
   --scope /subscriptions/<subscription Id>/resourceGroups/myResourceGroup \
   --policy "not-allowed-vmextension-linux" \
   --params '{
        "notAllowedExtensions": {
            "value": [
                "VMAccessForLinux",
                "CustomScriptForLinux"
            ]
        }
    }'
```

## <a name="test-the-policy"></a>Testa principen

Testa principen genom att skapa en ny virtuell dator och försöka lägga till en ny användare.


```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Försök att skapa en ny användare med namnet **myNewUser** med hjälp av tillägget för VM-åtkomst.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --password 'mynewuserpwd123!'
```



## <a name="remove-the-assignment"></a>Ta bort tilldelningen

```azurecli-interactive
az policy assignment delete --name 'not-allowed-vmextension-linux' --resource-group myResourceGroup
```
## <a name="remove-the-policy"></a>Ta bort principen

```azurecli-interactive
az policy definition delete --name 'not-allowed-vmextension-linux'
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Azure Policy](../../governance/policy/overview.md).
