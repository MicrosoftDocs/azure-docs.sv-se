---
title: Skapa IP-grupper i Azure-brandväggen
description: Med IP-grupper kan du gruppera och hantera IP-adresser för Azure brand Väggs regler.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/23/2020
ms.author: victorh
ms.openlocfilehash: c53b35351108717c7b597e052a66e9902be5ec6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93394534"
---
# <a name="create-ip-groups"></a>Skapa IP-grupper

Med IP-grupper kan du gruppera och hantera IP-adresser för Azure brand Väggs regler. De kan ha en enskild IP-adress, flera IP-adresser eller ett eller flera IP-adressintervall.

## <a name="create-an-ip-group---azure-portal"></a>Skapa en IP-grupp – Azure Portal

Så här skapar du en IP-grupp med hjälp av Azure Portal:

1. På Azure Portal start sida väljer du **skapa en resurs**.
1. I rutan Sök anger du **IP-grupper** och väljer sedan **IP-grupper**.
1. Välj **Skapa**.
1. Välj din prenumeration.
1. Välj en resurs grupp eller skapa en ny.
1. Ange ett unikt namn för IP-gruppen och välj sedan en region.
1. Välj **Nästa: IP-adresser**.
1. Ange en IP-adress, flera IP-adresser eller IP-adressintervall.

   Det finns två sätt att ange IP-adresser:
   - Du kan ange dem manuellt
   - Du kan importera dem från en fil

   Om du vill importera från en fil väljer du **Importera från en fil**. Du kan antingen dra filen till rutan eller välja **Bläddra efter filer**. Om det behövs kan du granska och redigera dina överförda IP-adresser.

   När du anger en IP-adress validerar portalen den för att söka efter överlappande, dubbletter och formateringsfel.

1. När du är färdig väljer du **Granska + skapa**.
1. Välj **Skapa**.

## <a name="create-an-ip-group---azure-powershell"></a>Skapa en IP-grupp – Azure PowerShell

I det här exemplet skapas en IP-grupp med ett adressprefix och en IP-adress med hjälp av Azure PowerShell:

```azurepowershell
$ipGroup = @{
    Name              = 'ipGroup'
    ResourceGroupName = 'ipGroupRG'
    Location          = 'West US'
    IpAddress         = @('10.0.0.0/24', '192.168.1.10') 
}

New-AzIpGroup @ipGroup
```

## <a name="create-an-ip-group---azure-cli"></a>Skapa en IP-grupp – Azure CLI

I det här exemplet skapas en IP-grupp med ett adressprefix och en IP-adress med hjälp av Azure CLI:

```azurecli-interactive
az network ip-group create \
    --name ipGroup \ 
    --resource-group ipGroupRG \
    --location westus \
    --ip-addresses '10.0.0.0/24' '192.168.1.10'
```

## <a name="next-steps"></a>Nästa steg

- [Läs mer om IP-grupper](ip-groups.md)
