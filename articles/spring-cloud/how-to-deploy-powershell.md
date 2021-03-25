---
title: Distribuera Azure våren Cloud med Azure PowerShell
description: Distribuera Azure våren Cloud med Azure PowerShell
author: bmitchell287
ms.author: brendm
ms.topic: conceptual
ms.service: spring-cloud
ms.devlang: azurepowershell
ms.date: 11/16/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3cb320a37818084f2fbcad22a3cc992655b19c3d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878301"
---
# <a name="deploy-azure-spring-cloud-with-azure-powershell"></a>Distribuera Azure Spring Cloud med Azure PowerShell

Den här artikeln beskriver hur du kan skapa en instans av Azure våren Cloud med PowerShell-modulen [AZ. SpringCloud](/powershell/module/Az.SpringCloud) .

## <a name="requirements"></a>Krav

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Även om **AZ. SpringCloud** PowerShell-modulen är i för hands version måste du installera den separat med hjälp av `Install-Module` cmdleten. När modulen blir allmänt tillgänglig kommer den att ingå i framtida versioner av Az PowerShell-modulen och vara tillgänglig som standard i Azure Cloud Shell.

  ```azurepowershell-interactive
  Install-Module -Name Az.SpringCloud
  ```

* Om du har flera Azure-prenumerationer väljer du lämplig prenumeration där resurserna ska faktureras. Välj en speciell prenumeration med cmdleten [set-AzContext](/powershell/module/az.accounts/set-azcontext) .

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [Azure-resurs grupp](../azure-resource-manager/management/overview.md) med cmdlet: en [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp.

I följande exempel skapas en resurs grupp med det angivna namnet och på den angivna platsen.

```azurepowershell-interactive
New-AzResourceGroup -Name <resource group name> -Location eastus
```

## <a name="provision-a-new-instance-of-azure-spring-cloud"></a>Etablera en ny instans av Azure våren Cloud

Om du vill skapa en ny instans av Azure våren Cloud använder du cmdleten [New-AzSpringCloud](/powershell/module/az.springcloud/new-azspringcloud) . I följande exempel skapas en moln tjänst för Azure våren med det angivna namnet i den tidigare skapade resurs gruppen.

```azurepowershell-interactive
New-AzSpringCloud -ResourceGroupName <resource group name> -name <service instance name> -Location eastus
```

## <a name="create-a-new-azure-spring-cloud-app"></a>Skapa en ny Azure våren Cloud-App

Om du vill skapa en ny app använder du cmdleten [New-AzSpringCloudApp](/powershell/module/az.springcloud/new-azspringcloudapp) . I följande exempel skapas en Azure våren Cloud-App med namnet `gateway` .

```azurepowershell-interactive
New-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="create-a-new-azure-spring-cloud-deployment"></a>Skapa en ny Azure våren Cloud-distribution

Om du vill skapa en ny distribution använder du cmdleten [New-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/new-azspringcloudappdeployment) . I följande exempel skapas en Azure våren Cloud-distribution med namnet `default` för `gateway` appen.

```azurepowershell-interactive
New-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -Name <service instance name> -AppName gateway -DeploymentName default
```

## <a name="get-an-azure-spring-cloud-service"></a>Skaffa en moln tjänst för Azure våren

För att få en moln tjänst för Azure våren och dess egenskaper använder du cmdleten [Get-AzSpringCloud](/powershell/module/az.springcloud/get-azspringcloud) . I följande exempel hämtas information om den angivna moln tjänsten Azure våren.

```azurepowershell-interactive
Get-AzSpringCloud -ResourceGroupName <resource group name> -ServiceName <service instance name>
```

## <a name="get-an-azure-spring-cloud-app"></a>Skaffa en Azure våren Cloud-App

Om du vill hämta en Azure våren Cloud-App och dess egenskaper använder du cmdleten [Get-AzSpringCloudApp](/powershell/module/az.springcloud/get-azspringcloudapp) . I följande exempel hämtas information om `gateway` våren Cloud-appen.

```azurepowershell-interactive
Get-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="get-an-azure-spring-cloud-deployment"></a>Skaffa en Azure våren Cloud-distribution

För att få en Azure våren Cloud-distribution och dess egenskaper använder du cmdleten [Get-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/get-azspringcloudappdeployment) . I följande exempel hämtas information om `default` vår moln distribution.

```azurepowershell-interactive
Get-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

## <a name="clean-up-resources"></a>Rensa resurser

Om resurserna som skapas i den här artikeln inte behövs kan du ta bort dem genom att köra följande exempel.

### <a name="delete-an-azure-spring-cloud-deployment"></a>Ta bort en Azure våren Cloud-distribution

Om du vill ta bort en Azure våren Cloud-distribution använder du cmdleten [Remove-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/remove-azspringcloudappdeployment) . I följande exempel tar vi bort en Azure våren Cloud App-distribution med namnet `default` för den angivna tjänsten och appen.

```azurepowershell-interactive
Remove-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

### <a name="delete-an-azure-spring-cloud-app"></a>Ta bort en Azure våren Cloud-App

Om du vill ta bort en våren Cloud-app använder du cmdleten [Remove-AzSpringCloudApp](/powershell/module/Az.SpringCloud/remove-azspringcloudapp) . I följande exempel tas `gateway` appen bort i den angivna tjänsten och resurs gruppen.

```azurepowershell
Remove-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

### <a name="delete-an-azure-spring-cloud-service"></a>Ta bort en Azure våren Cloud-tjänst

Om du vill ta bort en Azure våren Cloud-tjänst använder du cmdleten [Remove-AzSpringCloud](/powershell/module/Az.SpringCloud/remove-azspringcloud) . I följande exempel tar vi bort den angivna moln tjänsten Azure våren.

```azurepowershell
Remove-AzSpringCloud -ResourceGroupName <resource group name> -ServiceName <service instance name>
```

### <a name="delete-the-resource-group"></a>Ta bort resursgruppen

> [!CAUTION]
> I följande exempel tas den angivna resurs gruppen och alla resurser som ingår i den bort.
> Om resurser utanför omfånget för den här artikeln finns i den angivna resurs gruppen kommer de också att tas bort.

```azurepowershell-interactive
Remove-AzResourceGroup -Name <resource group name>
```

## <a name="next-steps"></a>Nästa steg

[Azure våren Cloud Developer-resurser](spring-cloud-resources.md).
