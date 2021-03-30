---
title: Självstudie – push-uppdatering till geo-replikerat register
description: Push-överför en uppdaterad Docker-avbildning till ditt geo-replikerade Azure Container Registry och se sedan ändringarna automatiskt distribuerade till Web Apps som körs i flera regioner. Del tre av en serie i tre delar.
ms.topic: tutorial
ms.date: 04/30/2018
ms.custom: seodec18, mvc
ms.openlocfilehash: 9222ac31e067cba6a0ffa71143c90f906ba6ff7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "74454701"
---
# <a name="tutorial-push-an-updated-container-image-to-a-geo-replicated-container-registry-for-regional-web-app-deployments"></a>Självstudie: push-överför en uppdaterad behållar avbildning till ett geo-replikerat behållar register för regionala webbappar-distributioner

Det här är del tre i en serie självstudier i tre delar. I den [föregående kursen](container-registry-tutorial-deploy-app.md) konfigurerades geo-replikering för två olika regionala Web App-distributioner. I den här självstudiekursen modifierar du först programmet, skapar sedan en ny containeravbildning och push-överför den till ditt geo-replikerade register. Slutligen visar du ändringen, som distribuerats automatiskt med Azure Container Registry-webhookar i båda Web App-instanserna.

Den här självstudiekursen är den avslutande delen i serien:

> [!div class="checklist"]
> * Göra ändringar i webbprogrammets HTML
> * Skapa och tagga Docker-avbildningen
> * Push-överför ändringen till Azure Container Registry
> * Visa den uppdaterade appen i två olika regioner

Om du ännu inte har konfigurerat de två regionala distributionerna av *Web App for Containers*, så gå tillbaka till den föregående självstudiekursen i serien, [Distribuera webbapp från Azure Container Registry](container-registry-tutorial-deploy-app.md).

## <a name="modify-the-web-application"></a>Göra ändringar i webbprogrammet

I det här steget gör du ändringar i webbprogrammet så att det blir mycket synligare när du har push-överfört den uppdaterade behållaravbildningen till Azure Container Registry.

Sök efter `AcrHelloworld/Views/Home/Index.cshtml`-filen i den programkälla som du har [klonat från GitHub](container-registry-tutorial-prepare-registry.md#get-application-code) i en tidigare självstudiekurs och öppna den i valfritt redigeringsprogram. Lägg till följande rad under den befintliga `<h1>`-raden:

```html
<h1>MODIFIED</h1>
```

Den ändrade `Index.cshtml` bör se ut ungefär så här:

```html
@{
    ViewData["Title"] = "Azure Container Registry :: Geo-replication";
}
<style>
    body {
        background-image: url('images/azure-regions.png');
        background-size: cover;
    }
    .footer {
        position: fixed;
        bottom: 0px;
        width: 100%;
    }
</style>

<h1 style="text-align:center;color:blue">Hello World from:  @ViewData["REGION"]</h1>
<h1>MODIFIED</h1>
<div class="footer">
    <ul>
        <li>Registry URL: @ViewData["REGISTRYURL"]</li>
        <li>Registry IP: @ViewData["REGISTRYIP"]</li>
        <li>Registry Region: @ViewData["REGION"]</li>
    </ul>
</div>
```

## <a name="rebuild-the-image"></a>Återskapa avbildningen

Nu när du har uppdaterat webbprogrammet återskapa dess containeravbildning. Som tidigare, använd det fullständiga avbildningsnamnet, inklusive inloggningsserverns fullständigt kvalificerade domännamn (FQDN) för taggen:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

## <a name="push-image-to-azure-container-registry"></a>Push-överför avbildningen till Azure Container Registry

Push-överför sedan den uppdaterade containeravbildningen *acr-helloworld* till ditt geo-replikerade register. Här utför du ett enda `docker push`-kommando för att distribuera den uppdaterade avbildningen till registerreplikerna i båda regionerna *USA, västra* och *USA, östra*.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

Ditt `docker push`-resultat bör likna följande:

```console
$ docker push uniqueregistryname.azurecr.io/acr-helloworld:v1
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
5b9454e91555: Pushed
d6803756744a: Layer already exists
b7b1f3a15779: Layer already exists
a89567dff12d: Layer already exists
59c7b561ff56: Layer already exists
9a2f9413d9e4: Layer already exists
a75caa09eb1f: Layer already exists
v1: digest: sha256:4c3f2211569346fbe2d1006c18cbea2a4a9dcc1eb3a078608cef70d3a186ec7a size: 1792
```

## <a name="view-the-webhook-logs"></a>Visa webhook-loggarna

Du kan se hur Azure Container Registry-webhookarna utlöses när avbildningen replikeras.

Om du vill se de regionala webhookar som skapades när du distribuerade behållaren till *Web App for Containers* i en tidigare självstudiekurs, så gå till ditt behållarregister i Azure Portal och välj sedan **Webhooks** under **TJÄNSTER**.

![Container Registry-webhookar i Azure Portal][tutorial-portal-01]

Markera varje webhook om du vill se historiken för dess anrop och svar. Du bör se en rad för **push**-åtgärden i loggarna för båda webhookarna. Här visar loggen för webhooken i regionen *USA, västra* den **push**-åtgärd som utlöstes av `docker push` i det föregående steget:

![Container Registry-webhooksloggen i Azure Portal (USA, västra)][tutorial-portal-02]

## <a name="view-the-updated-web-app"></a>Visa den uppdaterade webbappen

Webhookarna meddelar Web Apps att en ny avbildning har push-överförts till registret, vilket automatiskt distribuerar den uppdaterade containern till de två regionala webbapparna.

Verifiera att programmet har uppdaterats i båda distributionerna genom att gå till båda de regionala Web App-distributionerna i webbläsaren. Som en påminnelse finns URL:en för den distribuerade webbappen högst upp till höger på varje App Service-översiktsflik.

![App Service-översikt i Azure Portal][tutorial-portal-03]

Om du vill se det uppdaterade programmet, så välj länken i App Service-översikten. Här är en exempelvy av appen som körs i *USA, västra*:

![Webbläsarvy över en ändrad webbapp som körs i regionen USA, västra][deployed-app-westus-modified]

Verifiera att den uppdaterade containeravbildningen också distribueras till distributionen *USA, östra* genom att visa den i webbläsaren.

![Webbläsarvy över en ändrad webbapp som körs i regionen USA, östra][deployed-app-eastus-modified]

Med en enda `docker push`, har du automatiskt uppdaterat webbprogrammet som körs i båda de regionala Web App-distributionerna. Och Azure Container Registry hanterat behållaravbildningar från de databaser som ligger närmast varje distribution.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen har du uppdaterat och push-överfört en ny version av webbprogramscontainern till ditt geo-replikerade register. Webhooks i Azure Container Registry meddelade Web Apps for Containers om uppdateringen, vilken utlöste en lokal hämtning från de närmsta registerreplikerna.

### <a name="acr-build-automated-image-build-and-patch"></a>ACR Build: Automatisk skapande av avbildning och korrigering

Förutom geo-replikering är ACR Build en annan funktion i Azure Container Registry som kan hjälpa dig att optimera din distributionspipeline för behållare. Börja med ACR Build-översikten om du vill få en uppfattning om dess funktioner:

[Automatisera korrigering av operativsystem och ramverk med ACR Build](container-registry-tasks-overview.md)

<!-- IMAGES -->
[deployed-app-eastus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-eastus-modified.png
[deployed-app-westus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-westus-modified.png
[local-container-01]: ./media/container-registry-tutorial-deploy-update/local-container-01.png
[tutorial-portal-01]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-01.png
[tutorial-portal-02]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-02.png
[tutorial-portal-03]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-03.png