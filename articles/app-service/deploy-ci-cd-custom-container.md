---
title: CI/CD till anpassade Linux-behållare
description: Lär dig hur du konfigurerar kontinuerlig distribution till en anpassad Linux-behållare i Azure App Service. Kontinuerlig distribution stöds för Docker Hub och ACR.
keywords: Azure App Service, Linux, Docker, ACR, oss
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: e879d8370821ea465147e344d0fe95836c843ff4
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97008018"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Kontinuerlig distribution med Web App for Containers

I den här självstudien konfigurerar du kontinuerlig distribution för en anpassad behållar avbildning från hanterade [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) -databaser eller [Docker-hubb](https://hub.docker.com).

## <a name="enable-continuous-deployment-with-acr"></a>Aktivera kontinuerlig distribution med ACR

![Skärm bild av ACR-webhook](./media/deploy-ci-cd-custom-container/ci-cd-acr-02.png)

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj alternativet **App Service** till vänster på sidan.
3. Välj namnet på den app som du vill konfigurera kontinuerlig distribution för.
4. På sidan **behållar inställningar** väljer du **en behållare**
5. Välj **Azure Container Registry**
6. Välj **kontinuerlig distribution > på**
7. Välj **Spara** för att aktivera kontinuerlig distribution.

## <a name="use-the-acr-webhook"></a>Använd ACR-webhooken

När kontinuerlig distribution har Aktiver ATS kan du Visa den nyskapade webhooken på sidan Azure Container Registry Webhooks.

![Skärm bild som visar var du kan visa den nya webhooken på sidan Azure Container Registry Webhooks.](./media/deploy-ci-cd-custom-container/ci-cd-acr-03.png)

Klicka på Webhooks i Container Registry för att visa de aktuella webhookarna.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Aktivera kontinuerlig distribution med Docker Hub (valfritt)

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj alternativet **App Service** till vänster på sidan.
3. Välj namnet på den app som du vill konfigurera kontinuerlig distribution för.
4. På sidan **behållar inställningar** väljer du **en behållare**
5. Välj **Docker-hubb**
6. Välj **kontinuerlig distribution > på**
7. Välj **Spara** för att aktivera kontinuerlig distribution.

![Skärm bild av appens inställning](./media/deploy-ci-cd-custom-container/ci-cd-docker-02.png)

Kopiera webhook-URL: en. Om du vill lägga till en webhook för Docker Hub, följer du <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">Webhooks för Docker Hub</a>.

## <a name="automate-with-cli"></a>Automatisera med CLI

Konfigurera CI/CD med Azure CLI genom att köra config-kommandot [AZ webapp Deployment container](/cli/azure/webapp/deployment/container#az-webapp-deployment-container-config) för att generera webhook-URL: en. URL: en kan användas för att konfigurera DockerHub eller Azure Container Registry.

```azurecli-interactive
az webapp deployment container config --name <app-name> --resource-group <group-name> --enable-cd true
```

## <a name="next-steps"></a>Nästa steg

* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Skapa en .NET Core-webbapp i App Service på Linux](quickstart-dotnetcore.md?pivots=platform-linux)
* [Skapa en ruby-webbapp i App Service på Linux](quickstart-ruby.md)
* [Snabb start: kör en anpassad behållare på App Service](quickstart-custom-container.md?pivots=container-linux)
* [Vanliga frågor och svar om App Service på Linux](faq-app-service-linux.md)
* [Konfigurera anpassade Linux-behållare](configure-custom-container.md)
