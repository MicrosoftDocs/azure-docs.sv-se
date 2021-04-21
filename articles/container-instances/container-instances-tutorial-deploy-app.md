---
title: Självstudie – Distribuera containerapp till containerinstans
description: Självstudie om Azure Container Instances del 3 av 3 – Distribuera ett containerprogram till Azure Container Instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: 2d12b0bc91cf844001b770c8a7e534ad6b1b9b2d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763891"
---
# <a name="tutorial-deploy-a-container-application-to-azure-container-instances"></a>Självstudie: Distribuera ett containerprogram till Azure Container Instances

Det här är den sista självstudien i en serie med tre delar. Tidigare i serien [skapades en behållaravbildning](container-instances-tutorial-prepare-app.md) som sedan [push-överfördes till Azure Container Registry](container-instances-tutorial-prepare-acr.md). I den här artikeln slutför vi serien genom att distribuera behållaren till Azure Container Instances.

I den här kursen får du:

> [!div class="checklist"]
> * Distribuera behållaren från Azure Container Registry till Azure Container Instances
> * Visa det program som körs i webbläsaren
> * Visa containerns loggar

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="deploy-the-container-using-the-azure-cli"></a>Distribuera containern med hjälp av Azure CLI

I det här avsnittet använder du Azure CLI för att distribuera den avbildning som du skapade i den [första självstudiekursen](container-instances-tutorial-prepare-app.md) och flyttade till Azure Container Registry i den [andra självstudiekursen](container-instances-tutorial-prepare-acr.md). Försäkra dig om att du har slutfört de självstudierna innan du går vidare.

### <a name="get-registry-credentials"></a>Hämta autentiseringsuppgifter för registret

När du distribuerar en avbildning som finns i ett privat Azure-containerregister som det som skapades i den andra självstudien [måste](container-instances-tutorial-prepare-acr.md)du ange autentiseringsuppgifter för att komma åt registret. 

En bra metod för många scenarier är att skapa och konfigurera Azure Active Directory tjänstens huvudnamn med *pull-behörigheter* till registret. Se [Autentisera med Azure Container Registry från Azure Container Instances](../container-registry/container-registry-auth-aci.md) exempelskript för att skapa ett huvudnamn för tjänsten med nödvändiga behörigheter. Anteckna ID:t för *tjänstens huvudnamn* och *lösenordet för tjänstens huvudnamn.* Du använder dessa autentiseringsuppgifter för att få åtkomst till registret när du distribuerar containern.

Du behöver också det fullständiga namnet på containerregistrets inloggningsserver (ersätt `<acrName>` med namnet på ditt register):

```azurecli
az acr show --name <acrName> --query loginServer
```

### <a name="deploy-container"></a>Distribuera containern

Nu kan använda kommandot [az container create][az-container-create] för att distribuera behållaren. Ersätt `<acrLoginServer>` med det värde som du fick från föregående kommando. Ersätt `<service-principal-ID>` och `<service-principal-password>` med det ID och det lösenord för tjänstens huvudnamn som du skapade för att komma åt registret. Ersätt `<aciDnsLabel>` med önskat DNS-namn.

```azurecli
az container create --resource-group myResourceGroup --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-login-server <acrLoginServer> --registry-username <service-principal-ID> --registry-password <service-principal-password> --dns-name-label <aciDnsLabel> --ports 80
```

Inom några sekunder bör du få ett första svar från Azure. Värdet `--dns-name-label` måste vara unikt i den Azure-region där du skapar containerinstansen. Ändra värdet i föregående kommandot om du får ett felmeddelande för **DNS-namnsetikett** när du kör kommandot.

### <a name="verify-deployment-progress"></a>Verifiera distributionens fortskridande

Du kan se statusen för distributionen med [az container show][az-container-show]:

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query instanceView.state
```

Upprepa kommandot [az container show][az-container-show] tills status ändras från *Väntar* till *Körs*, vilket bör ta mindre än en minut. När containern *Körs* fortsätter du till nästa steg.

## <a name="view-the-application-and-container-logs"></a>Visa program- och containerloggar

När distributionen är klar kan du visa behållarens fullständiga domännamn (FQDN) med kommandot [az container show][az-container-show]:

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
```

Exempel:
```output
"aci-demo.eastus.azurecontainer.io"
```

Gå till DNS-namnet som visas i webbläsaren om du vill se programmet som körs:

![Hello World-app i webbläsaren][aci-app-browser]

Du kan också visa loggutdata för containern:

```azurecli
az container logs --resource-group myResourceGroup --name aci-tutorial-app
```

Exempel på utdata:

```output
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1&quot; 200 1663 &quot;-&quot; &quot;Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver någon av de resurser som du skapade i den här självstudieserien, kan du köra kommandot [az group delete][az-group-delete] för att ta bort resursgruppen och alla resurser som den innehåller. Det här kommandot tar bort containerregistret som du skapade, den container som körs och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I självstudien slutförde du processen att distribuera din behållare till Azure Container Instances. Följande steg har slutförts:

> [!div class="checklist"]
> * Distribuerade behållaren från Azure Container Registry med hjälp av Azure CLI
> * Visade programmet i webbläsaren
> * Visade containerloggarna

Nu när du har tillägnat dig grunderna kan du gå vidare och lära dig mer om Azure Container Instances, t.ex. hur behållargrupper fungerar:

> [!div class="nextstepaction"]
> [Behållargrupper i Azure Container Instances](container-instances-container-groups.md)

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install-azure-cli
[prepare-app]: ./container-instances-tutorial-prepare-app.md
