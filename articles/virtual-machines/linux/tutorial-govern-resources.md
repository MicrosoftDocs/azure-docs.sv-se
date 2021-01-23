---
title: Självstudie – hantera virtuella datorer med CLI
description: I den här självstudien får du lära dig hur du använder Azure CLI för att hantera virtuella Azure-datorer genom att använda Azure RBAC, principer, lås och taggar.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tfitzmac
manager: gwallace
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.topic: tutorial
ms.date: 09/30/2019
ms.author: tomfitz
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 695bf57e120889207151209702c16d456da79385
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736775"
---
# <a name="tutorial-learn-about-linux-virtual-machine-management-with-azure-cli"></a>Självstudie: Lär dig mer om hantering av virtuella Linux-datorer med Azure CLI

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- I den här självstudien krävs version 2.0.30 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="understand-scope"></a>Förstå omfång

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

I den här självstudiekursen tillämpar du alla hanteringsinställningar på en resursgrupp, så att du enkelt kan ta bort inställningarna när du är klar.

Nu ska vi skapa resursgruppen.

```azurecli-interactive
az group create --name myResourceGroup --location "East US"
```

Resursgruppen är tom för närvarande.

## <a name="azure-role-based-access-control"></a>Rollbaserad Azure-åtkomstkontroll

Du vill kontrollera att användarna i din organisation har rätt åtkomstnivå till dessa resurser. Du vill inte bevilja obegränsad åtkomst till användare, men du måste också försäkra dig om att de kan utföra sitt arbete. Med [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md) kan du hantera vilka användare som har behörighet att utföra vissa åtgärder i ett omfång.

För att kunna skapa och ta bort rolltilldelningar måste användare ha `Microsoft.Authorization/roleAssignments/*`-åtkomst. Den här åtkomsten beviljas via rollerna Ägare eller Administratör för användaråtkomst.

För hanteringen av VM-lösningar finns det tre resursspecifika roller som beviljar åtkomst på lämplig nivå:

* [Virtuell datordeltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Nätverksdeltagare](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Lagringskontodeltagare](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

I stället för att tilldela roller till enskilda användare är det ofta lättare att använda en Azure Active Directory-grupp som har användare som behöver utföra liknande åtgärder. Därefter tilldelar du gruppen lämplig roll. För den här artikeln använder du en befintlig grupp för den virtuella datorn eller använder portalen för att [skapa en Azure Active Directory-grupp](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

När du har skapat en ny grupp eller hittar en befintlig använder du kommandot [az role assignment create](/cli/azure/policy/assignment#az_policy_assignment_create) för att tilldela den nya Azure Active Directory-gruppen till rollen Virtuell datordeltagare för resursgruppen.

```azurecli-interactive
adgroupId=$(az ad group show --group <your-group-name> --query objectId --output tsv)

az role assignment create --assignee-object-id $adgroupId --role "Virtual Machine Contributor" --resource-group myResourceGroup
```

Om du får ett felmeddelande om att **huvudnamn \<guid> inte finns i katalogen** har den nya gruppen inte spridits i hela Azure Active Directory. Prova att köra kommandot igen.

Normalt upprepar du processen för *Nätverksdeltagare* och *Lagringskontodeltagare* för att se till att hanteringen av alla distribuerade resurser tilldelas till användare. Du kan hoppa över dessa steg i den här artikeln.

## <a name="azure-policy"></a>Azure Policy

[Azure Policy](../../governance/policy/overview.md) hjälper dig se till att alla resurser i prenumerationen uppfyller företagets standarder. Din prenumeration har redan flera principdefinitioner. Om du vill visa de tillgängliga principdefinitionerna använder du kommandot [az policy definition list](/cli/azure/policy/definition#az_policy_definition_list):

```azurecli-interactive
az policy definition list --query "[].[displayName, policyType, name]" --output table
```

De befintliga principdefinitionerna visas. Principtypen är antingen **BuiltIn** eller **Custom**. Titta igenom definitionerna och leta efter sådana som beskriver ett tillstånd som du vill tilldela. I den här artikeln tilldelar du principer som:

* Begränsar platserna för alla resurser.
* Begränsa SKU:erna för virtuella datorer.
* Granskar virtuella datorer som inte använder hanterade diskar.

I exemplet nedan hämtar du tre principdefinitioner baserat på visningsnamnet. Du använder kommandot [az policy assignment create](/cli/azure/policy/assignment#az_policy_assignment_create) för att tilldela dessa definitioner till resursgruppen. För vissa principer anger du parametervärden för att definiera de tillåtna värdena.

```azurecli-interactive
# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
locationDefinition=$(az policy definition list --query "[?displayName=='Allowed locations'].name | [0]" --output tsv)
skuDefinition=$(az policy definition list --query "[?displayName=='Allowed virtual machine SKUs'].name | [0]" --output tsv)
auditDefinition=$(az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks'].name | [0]" --output tsv)

# Assign policy for allowed locations
az policy assignment create --name "Set permitted locations" \
  --resource-group myResourceGroup \
  --policy $locationDefinition \
  --params '{ 
      "listOfAllowedLocations": {
        "value": [
          "eastus", 
          "eastus2"
        ]
      }
    }'

# Assign policy for allowed SKUs
az policy assignment create --name "Set permitted VM SKUs" \
  --resource-group myResourceGroup \
  --policy $skuDefinition \
  --params '{ 
      "listOfAllowedSKUs": {
        "value": [
          "Standard_DS1_v2", 
          "Standard_E2s_v2"
        ]
      }
    }'

# Assign policy for auditing unmanaged disks
az policy assignment create --name "Audit unmanaged disks" \
  --resource-group myResourceGroup \
  --policy $auditDefinition
```

Föregående exempel förutsätter att du redan känner till parametrarna för en princip. Om du vill visa parametrarna använder du:

```azurecli-interactive
az policy definition show --name $locationDefinition --query parameters
```

## <a name="deploy-the-virtual-machine"></a>Distribuera den virtuella datorn

Nu när du har tilldelat roller och principer är det dags att distribuera lösningen. Standardstorleken är Standard_DS1_v2, som är en av dina tillåtna SKU:er. Kommandot skapar SSH-nycklar om de inte finns på standardplatsen.

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

När distributionen är klar kan du lägga till fler hanteringsinställningar för lösningen.

## <a name="lock-resources"></a>Lås resurser

[Resurslås](../../azure-resource-manager/management/lock-resources.md) förhindrar att användare i din organisation tar bort eller ändrar viktiga resurser av misstag. Till skillnad från rollbaserad åtkomstkontroll tillämpar resurslås en begränsning för alla användare och roller. Du kan ange låsnivån till *CanNotDelete* eller *ReadOnly*.

För att kunna skapa eller ta bort hanteringslås måste du ha åtkomst till `Microsoft.Authorization/locks/*`-åtgärder. Av de inbyggda rollerna har endast **Ägare** och **Administratör för användaråtkomst** åtkomst till dessa åtgärder.

Om du vill låsa den virtuella datorn och nätverkssäkerhetsgruppen använder du kommandot [az lock create](/cli/azure/resource/lock#az_resource_lock_create):

```azurecli-interactive
# Add CanNotDelete lock to the VM
az lock create --name LockVM \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVM \
  --resource-type Microsoft.Compute/virtualMachines

# Add CanNotDelete lock to the network security group
az lock create --name LockNSG \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVMNSG \
  --resource-type Microsoft.Network/networkSecurityGroups
```

Om du vill testa låsen provar du att köra följande kommando:

```azurecli-interactive 
az group delete --name myResourceGroup
```

Ett felmeddelande visas som anger att borttagningsåtgärden inte kan slutföras på grund av ett lås. Resursgruppen kan bara tas bort om du tar bort låsen. Det steget beskrivs i [Rensa resurser](#clean-up-resources).

## <a name="tag-resources"></a>Tagga resurser

Du kan använda [taggar](../../azure-resource-manager/management/tag-resources.md) till dina Azure-resurser för att logiskt organisera dem efter kategorier. Varje tagg består av ett namn och ett värde. Du kan till exempel använda namnet ”Miljö” och värdet ”Produktion” för alla resurser i produktionsmiljön.

[!INCLUDE [Resource Manager governance tags CLI](../../../includes/resource-manager-governance-tags-cli.md)]

Om du vill lägga till taggar till en virtuell dator använder du kommandot [az resource tag](/cli/azure/resource#az_resource_list). En resurs eventuella befintliga taggar bevaras inte.

```azurecli-interactive
az resource tag -n myVM \
  -g myResourceGroup \
  --tags Dept=IT Environment=Test Project=Documentation \
  --resource-type "Microsoft.Compute/virtualMachines"
```

### <a name="find-resources-by-tag"></a>Hitta resurser efter tagg

Om du vill söka efter resurser med ett taggnamn och taggvärde använder du kommandot [az resource list](/cli/azure/resource#az_resource_list):

```azurecli-interactive
az resource list --tag Environment=Test --query [].name
```

Du kan använda de returnerade värdena för olika hanteringsuppgifter, t.ex. för att stoppa alla virtuella datorer med ett visst taggvärde.

```azurecli-interactive
az vm stop --ids $(az resource list --tag Environment=Test --query "[?type=='Microsoft.Compute/virtualMachines'].id" --output tsv)
```

### <a name="view-costs-by-tag-values"></a>Visa kostnader efter taggvärden

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Den låsta nätverkssäkerhetsgruppen kan inte tas bort förrän låset tagits bort. Om du vill ta bort lås hämtar du låsens ID:n och anger dem i kommandot [az lock delete](/cli/azure/resource/lock#az_resource_lock_delete):

```azurecli-interactive
vmlock=$(az lock show --name LockVM \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Compute/virtualMachines \
  --resource-name myVM --output tsv --query id)
nsglock=$(az lock show --name LockNSG \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Network/networkSecurityGroups \
  --resource-name myVMNSG --output tsv --query id)
az lock delete --ids $vmlock $nsglock
```

När de inte längre behövs kan du använda kommandot [AZ Group Delete](/cli/azure/group#az_group_delete) för att ta bort resurs gruppen, den virtuella datorn och alla relaterade resurser. Avsluta SSH-sessionen till den virtuella datorn och ta sedan bort resurserna enligt följande:

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Nästa steg

I självstudien skapade du en anpassad VM-avbildning. Du har lärt dig att:

> [!div class="checklist"]
> * Tilldela användare en roll
> * Tillämpa principer som verkställer standarder
> * Skydda viktiga resurser med lås
> * Tagga resurser för fakturering och hantering

Gå vidare till nästa självstudie för att lära dig att identifiera ändringar och hantera paket uppdateringar på en virtuell dator.

> [!div class="nextstepaction"]
> [Hantera virtuella datorer](tutorial-config-management.md)
