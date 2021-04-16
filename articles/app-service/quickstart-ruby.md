---
title: 'Snabbstart: Skapa en Ruby-app'
description: Kom igång med Azure App Service genom att distribuera din första Ruby-app till en Linux-container i App Service.
keywords: azure app service, linux, oss, ruby, rails
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.topic: quickstart
ms.date: 07/11/2019
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli
ms.openlocfilehash: dc64dfa277e97fe7487ce91a140e73b9ec44e086
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483545"
---
# <a name="create-a-ruby-on-rails-app-in-app-service"></a>Skapa en Ruby on Rails-app i App Service

[Azure App Service på Linux](overview.md#app-service-on-linux) en mycket skalbar och självkorrigering av webbvärdtjänsten med hjälp av Linux-operativsystemet. Den här snabbstartskursen visar hur du distribuerar en Ruby on Rails-app App Service på Linux med hjälp av [Cloud Shell](../cloud-shell/overview.md).

> [!NOTE]
> Ruby-utvecklingsstacken har bara stöd för Ruby on Rails just nu. Om du vill använda en annan plattform, till exempel Sinatra, eller om du vill använda en Ruby-version som inte stöds måste du köra den i en [anpassad container](./quickstart-custom-container.md?pivots=platform-linux%3fpivots%3dplatform-linux).

![Hello-world](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">Installera Ruby 2.6 eller senare</a>
* <a href="https://git-scm.com/" target="_blank">Installera Git</a>

## <a name="download-the-sample"></a>Ladda ned exemplet

Kör följande kommando i ett terminalfönster för att klona databasen för exempelappen till den lokala datorn:

```bash
git clone https://github.com/Azure-Samples/ruby-docs-hello-world
```

## <a name="run-the-application-locally"></a>Köra appen lokalt

Kör programmet lokalt så att du ser hur det ska se ut när du distribuerar det till Azure. Öppna ett terminalfönster, ändra till katalogen `hello-world` och använd kommandot `rails server` för att starta servern.

Första steget är att installera de gems du behöver. Det finns en `Gemfile` som ingår i exemplet, så kör bara följande kommando:

```bash
bundle install
```

När dina gems är installerade använder vi bundler till att starta appen:

```bash
bundle exec rails server
```

Navigera till `http://localhost:3000` i webbläsaren om du vill testa appen lokalt.

![Konfigurerad Hello World](./media/quickstart-ruby/hello-world-updated.png)

[!INCLUDE [Try Cloud Shell](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Skapa en webbapp

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

Bläddra till appen för att se din nyligen skapade webbapp med inbyggd avbildning. Ersätt _&lt; appnamn>_ med namnet på din webbapp.

```bash
http://<app_name>.azurewebsites.net
```

Så här bör din nya webbapp se ut:

![Välkomstsida](./media/quickstart-ruby/splash-page.png)

## <a name="deploy-your-application"></a>Distribuera appen

Kör följande kommandon för att distribuera det lokala programmet till azure-webbappen:

```bash
git remote add azure <Git deployment URL from above>
git push azure main
```

Bekräfta att fjärrdistributionsåtgärderna lyckades. Kommandona producerar utdata som liknar följande:

```bash
remote: Using turbolinks 5.2.0
remote: Using uglifier 4.1.20
remote: Using web-console 3.7.0
remote: Bundle complete! 18 Gemfile dependencies, 78 gems now installed.
remote: Bundled gems are installed into `/tmp/bundle`
remote: Zipping up bundle contents
remote: .......
remote: ~/site/repository
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app-name>.scm.azurewebsites.net/<app-name>.git
   a6e73a2..ae34be9  main -> main
```

När distributionen är klar väntar du ungefär 10 sekunder på att webbappen ska startas om och går sedan till webbappen och verifierar resultatet.

```bash
http://<app-name>.azurewebsites.net
```

![uppdaterad webbapp](./media/quickstart-ruby/hello-world-configured.png)

> [!NOTE]
> När appen startas om kan du se `Error 503 Server unavailable` HTTP-statuskoden i webbläsaren eller på `Hey, Ruby developers!` standardsidan. Det kan ta några minuter för appen att startas om helt.
>

[!INCLUDE [Clean-up section](../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Ruby on Rails med Postgres](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Konfigurera Ruby-appen](configure-language-ruby.md)
