---
title: Snabb start – skapa register – PowerShell
description: Lär dig snabbt att skapa ett privat Docker-register i Azure Container Registry med PowerShell
ms.topic: quickstart
ms.date: 01/22/2019
ms.custom: seodec18, mvc, devx-track-azurepowershell
ms.openlocfilehash: 91d4209ccf558bf7c8038d8a753ec038428bc484
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96020030"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>Snabb start: skapa ett privat behållar register med hjälp av Azure PowerShell

Azure Container Registry är en hanterad Docker-behållarregistertjänst för att bygga, lagra och hantera Docker-behållaravbildningar. I denna snabbstart kommer du att lära dig hur du skapar ett Azure-containerregister med hjälp av PowerShell. Använd därefter Docker-kommandon för att skicka en containeravbildning till registret, och hämta och kör avbildningen slutligen från registret.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Den här snabbstarten kräver Azure PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att avgöra installerad version. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul).

Du måste också ha Docker installerat lokalt. Docker innehåller paket för [macOS][docker-mac]-, [Windows][docker-windows]- och [Linux][docker-linux]-system.

Eftersom Azure Cloud Shell inte innehåller alla nödvändiga Docker-komponenter (`dockerd`-daemon), kan du inte använda Cloud Shell för denna snabbstart.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot [Connect-AzAccount][Connect-AzAccount] och följ anvisningarna på skärmen.

```powershell
Connect-AzAccount
```

## <a name="create-resource-group"></a>Skapa resursgrupp

När du är autentiserad med Azure skapar du en resursgrupp med [New-AzResourceGroup][New-AzResourceGroup]. En resursgrupp är en logisk container där du kan distribuera och hantera dina Azure-resurser.

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Skapa containerregister

Skapa sedan ett containerregister i din nya resursgrupp med kommandot [New-AzContainerRegistry][New-AzContainerRegistry].

Registernamnet måste vara unikt i Azure och innehålla 5–50 alfanumeriska tecken. I följande exempel skapas ett register med namnet ”myContainerRegistry007”. Ersätt *myContainerRegistry007* i följande kommando innan du kör det för att skapa registernyckeln:

```powershell
$registry = New-AzContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

I den här snabbstarten skapar du ett *Basic*-register, vilket är ett kostnadsoptimerat alternativ för utvecklare som lär sig om Azure Container Registry. Mer information om tillgängliga tjänst nivåer finns i [tjänste nivåer för container Registry][container-registry-skus].

## <a name="log-in-to-registry"></a>Logga in till registret

Innan du skickar och hämtar containeravbildningar måste du logga in i ditt register. I produktionsscenarier bör du använda en individuell identitet eller tjänstens huvudnamn för åtkomst till containerregistret, men för att snabbstarten inte ska bli för lång ska du aktivera administratörsanvändaren i registret med kommandot [Get-AzContainerRegistryCredential][Get-AzContainerRegistryCredential]:

```powershell
$creds = Get-AzContainerRegistryCredential -Registry $registry
```

Kör därefter [docker-inloggning][docker-login] för att logga in:

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

Kommandot returnerar `Login Succeeded` när det har slutförts.

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Rensa resurser

När du har arbetat klart med de resurser som du skapade i den här snabbstarten använder du kommandot [Remove-AzResourceGroup][Remove-AzResourceGroup] för att ta bort resursgruppen, containerregistret och de containeravbildningar som lagras där:

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett Azure Container Registry med Azure PowerShell, push-överförde en containeravbildning och hämtade och körde avbildningen från registret. Fortsätt till självstudien om Azure Container Registry om du vill titta närmare på ACR.

> [!div class="nextstepaction"]
> [Självstudier för Azure Container Registry][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Azure Container Registry uppgifter – självstudier][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Get-AzContainerRegistryCredential]: /powershell/module/az.containerregistry/get-azcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzContainerRegistry]: /powershell/module/az.containerregistry/New-AzContainerRegistry
[New-AzResourceGroup]: /powershell/module/az.resources/new-azresourcegroup
[Remove-AzResourceGroup]: /powershell/module/az.resources/remove-azresourcegroup
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
