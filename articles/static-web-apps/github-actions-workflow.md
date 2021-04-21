---
title: GitHub Actions arbetsflöden för Azure Static Web Apps
description: Lär dig hur du använder GitHub-lagringsplatsen för att konfigurera kontinuerlig distribution till Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: cshoe
ms.openlocfilehash: b20a1670c13a272ed48088567a205d854ac99179
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791255"
---
# <a name="github-actions-workflows-for-azure-static-web-apps-preview"></a>GitHub Actions arbetsflöden för Azure Static Web Apps förhandsversion

När du skapar en Azure Static Web Apps resurs genererar Azure ett GitHub Actions arbetsflöde för att styra appens kontinuerliga distribution. Arbetsflödet drivs av en YAML-fil. Den här artikeln beskriver strukturen och alternativen för arbetsflödesfilen.

Distributioner initieras av [utlösare](#triggers), som kör [jobb](#jobs) som definieras av enskilda [steg](#steps).

> [!NOTE]
> Azure Static Web Apps har även stöd för Azure DevOps. Se [Publicera med Azure DevOps för](publish-devops.md) information om hur du ställer in en pipeline.

## <a name="file-location"></a>Filplats

När du länkar gitHub-lagringsplatsen till Azure Static Web Apps läggs en arbetsflödesfil till på lagringsplatsen.

Följ de här stegen för att visa den genererade arbetsflödesfilen.

1. Öppna appens lagringsplats på GitHub.
1. Klicka _på mappen_ på fliken `.github/workflows` Kod.
1. Klicka på filen med ett namn som ser ut så `azure-static-web-apps-<RANDOM_NAME>.yml` här: .

YAML-filen på din lagringsplats liknar följande exempel:

```yml
name: Azure Static Web Apps CI/CD

on:
  push:
    branches:
      - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main

jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
          repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
          action: 'upload'
          ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
          app_location: '/' # App source code path
          api_location: 'api' # Api source code path - optional
          output_location: 'dist' # Built app content directory - optional
          ###### End of Repository/Build Configurations ######

  close_pull_request_job:
    if: github.event_name == 'pull_request' && github.event.action == 'closed'
    runs-on: ubuntu-latest
    name: Close Pull Request Job
    steps:
      - name: Close Pull Request
        id: closepullrequest
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
          action: 'close'
```

## <a name="triggers"></a>Utlösare

En GitHub Actions [en utlösare](https://help.github.com/actions/reference/events-that-trigger-workflows) meddelar ett GitHub Actions arbetsflöde för att köra ett jobb baserat på händelseutlösare. Utlösare visas med hjälp av `on` egenskapen i arbetsflödesfilen.

```yml
on:
  push:
    branches:
      - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main
```

Via inställningar som är associerade med egenskapen kan du definiera vilka grenar som utlöser ett jobb och ange utlösare som `on` utlöses för olika pull-begärande tillstånd.

I det här exemplet startas ett arbetsflöde när _huvudgrenen_ ändras. Ändringar som startar arbetsflödet inkluderar push-push-förfrågningar och öppning av pull-begäranden mot den valda grenen.

## <a name="jobs"></a>Jobb

Varje händelseutlösare kräver en händelsehanterare. [Jobb](https://help.github.com/actions/reference/workflow-syntax-for-github-actions#jobs) definierar vad som händer när en händelse utlöses.

Det finns Static Web Apps jobb i arbetsflödesfilen.

| Name                     | Beskrivning                                                                                                    |
| ------------------------ | -------------------------------------------------------------------------------------------------------------- |
| `build_and_deploy_job`   | Körs när du push-genomför eller öppnar en pull-begäran mot grenen som anges i `on` egenskapen .          |
| `close_pull_request_job` | Körs ENDAST när du stänger en pull-begäran, vilket tar bort mellanlagringsmiljön som skapats från pull-begäranden. |

## <a name="steps"></a>Steg

Steg är sekventiella uppgifter för ett jobb. Ett steg utför åtgärder som att installera beroenden, köra tester och distribuera programmet till produktion.

En arbetsflödesfil definierar följande steg.

| Jobb                      | Steg                                                                                                                              |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------- |
| `build_and_deploy_job`   | <ol><li>Checkar ut lagringsplatsen i åtgärdsmiljön.<li>Skapar och distribuerar lagringsplatsen till Azure Static Web Apps.</ol> |
| `close_pull_request_job` | <ol><li>Meddelar Azure Static Web Apps att en pull-begäran har stängts.</ol>                                                        |

## <a name="build-and-deploy"></a>Skapa och distribuera

Steget med namnet `Build and Deploy` skapar och distribuerar till din Azure Static Web Apps instans. Under avsnittet `with` kan du anpassa följande värden för distributionen.

```yml
with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
  repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
  action: 'upload'
  ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
  app_location: '/' # App source code path
  api_location: 'api' # Api source code path - optional
  output_location: 'dist' # Built app content directory - optional
  ###### End of Repository/Build Configurations ######
```

[!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

Värdena `repo_token` , och anges åt dig genom att Azure Static Web Apps ska inte ändras `action` `azure_static_web_apps_api_token` manuellt.

## <a name="custom-build-commands"></a>Anpassade byggkommandon

Du kan ha en mer exakt kontroll över vilka kommandon som körs under en distribution. Följande kommandon kan definieras under avsnittet för ett `with` jobb.

Distributionen anropar alltid `npm install` före ett anpassat kommando.

| Kommando             | Beskrivning                                                                                                                                                                                                                                                                                                                                                                                |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `app_build_command` | Definierar ett anpassat kommando som ska köras under distributionen av programmet för statiskt innehåll.<br><br>Om du till exempel vill konfigurera en produktionsbygge för ett Angular-program skapar du ett npm-skript med namnet för att köra `build-prod` och ange som det anpassade `ng build --prod` `npm run build-prod` kommandot. Om det lämnas tomt försöker arbetsflödet köra `npm run build` kommandona `npm run build:azure` eller . |
| `api_build_command` | Definierar ett anpassat kommando som ska köras under distributionen av Azure Functions API-programmet.                                                                                                                                                                                                                                                                                                  |

## <a name="skip-app-build"></a>Hoppa över appbygge

Om du behöver fullständig kontroll över hur ditt frontend-program har skapats kan du lägga till anpassade byggsteg i arbetsflödet. Sedan kan du konfigurera Static Web Apps för att kringgå den automatiska byggprocessen och bara distribuera appen som skapats i ett tidigare steg.

Om du vill hoppa över att skapa `skip_app_build` appen anger du till och till platsen för mappen som ska `true` `app_location` distribueras.

```yml
with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
  repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
  action: 'upload'
  ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
  app_location: 'dist' # Application build output generated by a previous step
  api_location: 'api' # Api source code path - optional
  output_location: '' # Leave this empty
  skip_app_build: true
  ###### End of Repository/Build Configurations ######
```

| Egenskap         | Beskrivning                                                 |
| ---------------- | ----------------------------------------------------------- |
| `skip_app_build` | Ange värdet till för `true` att hoppa över att skapa frontend-appen. |

> [!NOTE]
> Du kan bara hoppa över bygget för frontend-appen. Om din app har ett API kommer den fortfarande att byggas av Static Web Apps GitHub Action.

## <a name="route-file-location"></a>Routes-filens plats

Du kan anpassa arbetsflödet för att leta efter [routes.jspå](routes.md) i valfri mapp på din lagringsplats. Följande egenskap kan definieras under avsnittet för ett `with` jobb.

| Egenskap          | Beskrivning                                                                                                                                 |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| `routes_location` | Definierar den katalogplats där _routes.jspå_ filen hittas. Den här platsen är relativ till lagringsplatsens rot. |

Det är särskilt viktigt _att duroutes.js_ platsen för filen om byggsteget för frontend-ramverket inte flyttar den här filen till som `output_location` standard.

> [!IMPORTANT]
> Funktioner som _definierats iroutes.jspå_ filen är nu inaktuella. Se konfigurationsfilen Azure Static Web Apps [för](./configuration.md) information om _staticwebapp.config.jspå_.

## <a name="environment-variables"></a>Miljövariabler

Du kan ange miljövariabler för ditt `env` bygge via avsnittet i ett jobbs konfiguration.

```yaml
jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }}
          action: 'upload'
          ###### Repository/Build Configurations
          app_location: '/'
          api_location: 'api'
          output_location: 'public'
          ###### End of Repository/Build Configurations ######
        env: # Add environment variables here
          HUGO_VERSION: 0.58.0
```

## <a name="monorepo-support"></a>Monorepo-stöd

Ett monorepo är en lagringsplats som innehåller kod för mer än ett program. Som standard spårar en Static Web Apps-arbetsflödesfil alla filer i en lagringsplats, men du kan justera den för att rikta in dig på en enda app. För monorepos har därför varje statisk app sin egen konfigurationsfil som finns sida vid sida i _lagringsplatsens mapp .github/workflows._

```files
├── .github
│   └── workflows
│       ├── azure-static-web-apps-purple-pond.yml
│       └── azure-static-web-apps-yellow-shoe.yml
│
├── app1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── app2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
├── api1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── api2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
└── README.md
```

Om du vill rikta en arbetsflödesfil till en enda app anger du sökvägar i `push` avsnitten och `pull_request` .

I följande exempel visas hur du lägger till en nod i - och -avsnitten i en fil med namnet `paths` `push` `pull_request` _azure-static-web-apps-purple-mapp.yml_.

```yml
on:
  push:
    branches:
      - main
    paths:
      - app1/**
      - api1/**
      - .github/workflows/azure-static-web-apps-purple-pond.yml
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main
    paths:
      - app1/**
      - api1/**
      - .github/workflows/azure-static-web-apps-purple-pond.yml
```

I det här fallet utlöser endast ändringar som görs i följande filer en ny version:

- Filer i _mappen app1_
- Filer i _mappen api1_
- Ändringar i appens _azure-static-web-apps-purple-steg.yml-arbetsflödesfil_

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Granska pull-begäranden i förproduktionsmiljöer](review-publish-pull-requests.md)
