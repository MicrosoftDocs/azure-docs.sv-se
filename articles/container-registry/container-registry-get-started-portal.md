---
title: Snabb start – skapa register i portalen
description: Lär dig snabbt att skapa ett privat Azure Container Registry med hjälp av Azure Portal.
ms.topic: quickstart
ms.date: 08/04/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: ff74232edf5c96f091082f8486c44fed6125b0b1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97825834"
---
# <a name="quickstart-create-an-azure-container-registry-using-the-azure-portal"></a>Snabb start: skapa ett Azure Container Registry med hjälp av Azure Portal

Ett Azure Container Registry är ett privat Docker-register i Azure där du kan lagra och hantera privata Docker-behållar avbildningar och relaterade artefakter. I den här snabbstarten skapar du ett containerregister med Azure-portalen. Använd därefter Docker-kommandon för att skicka en containeravbildning till registret, och hämta och kör avbildningen slutligen från registret.

För att logga in i registret för att arbeta med containeravbildningar kräver den här snabbstarten att du kör Azure CLI (version 2.0.55 eller senare rekommenderas). Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

Du måste också ha Docker installerat lokalt. Docker innehåller paket som enkelt kan konfigurera Docker på en [Mac][docker-mac]-, [Windows][docker-windows]- eller [Linux][docker-linux]-dator.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-container-registry"></a>Skapa ett containerregister

Välj **skapa en resurs**  >  **behållare**  >  **container Registry**.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-01.png" alt-text="Navigera till behållar registret i portalen":::

På fliken **grundläggande** anger du värden för **resurs grupp** och **register namn**. Registernamnet måste vara unikt i Azure och innehålla 5–50 alfanumeriska tecken. För den här snabbstarten skapar du en ny resursgrupp på platsen `West US` med namnet `myResourceGroup`, och för **SKU** väljer du ”Basic”.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-03.png" alt-text="Skapa container Registry i portalen":::

Acceptera standardvärden för återstående inställningar. Välj sedan **Granska + skapa**. När du har granskat inställningarna väljer du **skapa**.

I den här snabbstarten skapar du ett *Basic*-register, vilket är ett kostnadsoptimerat alternativ för utvecklare som lär sig om Azure Container Registry. Mer information om tillgängliga tjänst nivåer (SKU: er) finns i [tjänste nivåer för container Registry][container-registry-skus].

När meddelandet **Distribueringen lyckades** visas väljer du containerregistret i portalen. 

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-05.png" alt-text="Översikt över container registret i portalen":::

Anteckna register namnet och värdet för **inloggnings servern**. Du använder dessa värden i följande steg när du push-överför och hämtar bilder med Docker.

## <a name="log-in-to-registry"></a>Logga in till registret

Innan du skickar och hämtar behållar avbildningar måste du logga in på register instansen. [Logga in på Azure CLI][get-started-with-azure-cli] på den lokala datorn och kör sedan kommandot [AZ ACR login][az-acr-login] . Ange bara register namnet när du loggar in med Azure CLI. Använd inte namnet på inloggnings servern, som innehåller ett domänsuffix som `azurecr.io` .

```azurecli
az acr login --name <registry-name>
```

Exempel:

```azurecli
az acr login --name mycontainerregistry
```

Kommandot returnerar `Login Succeeded` när det har slutförts. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Visa lista över containeravbildningar

Om du vill visa en lista över avbildningarna i registret går du till registret i portalen och väljer **databaser**. Välj sedan den  **Hello-världs** databas som du skapade med `docker push` .

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-09.png" alt-text="Lista behållar avbildningar i portalen":::

Genom att välja **Hello-World-** lagringsplatsen visas den `v1` -märkta bilden under **taggar**.

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa dina resurser går du till resursgruppen **myResourceGroup** i portalen. När resurs gruppen har lästs in klickar du på **ta bort resurs** grupp för att ta bort resurs gruppen, behållar registret och behållar avbildningarna som lagras där.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-08.png" alt-text="Ta bort resurs grupp i portalen":::


## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett Azure Container Registry med Azure-portalen, push-överförde en containeravbildning och hämtade och körde avbildningen från registret. Fortsätt till självstudien om Azure Container Registry om du vill titta närmare på ACR.

> [!div class="nextstepaction"]
> [Självstudier för Azure Container Registry][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Azure Container Registry uppgifter – självstudier][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[get-started-with-azure-cli]: /cli/azure/get-started-with-azure-cli
[az-acr-login]: /cli/azure/acr#az-acr-login
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
