---
title: 'Snabb start: skapa en Ruby-app'
description: Kom igång med Azure App Service genom att distribuera din första Ruby-app till en Linux-behållare i App Service.
keywords: azure app service, linux, oss, ruby, rails
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.topic: quickstart
ms.date: 07/11/2019
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: 45c87f690c24e989a797cbd82147b9125e447e79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97561791"
---
# <a name="create-a-ruby-on-rails-app-in-app-service"></a>Skapa en Ruby on räler-app i App Service

[Azure App Service på Linux](overview.md#app-service-on-linux) är en mycket skalbar och automatisk korrigering av webb värd tjänst med hjälp av Linux-operativsystemet. Den här snabb starts guiden visar hur du distribuerar en Ruby on-App-app till App Service i Linux med hjälp av [Cloud Shell](../cloud-shell/overview.md).

> [!NOTE]
> Ruby-utvecklingsstacken har bara stöd för Ruby on Rails just nu. Om du vill använda en annan plattform, till exempel Sinatra, eller om du vill använda en ruby-version som inte stöds, måste du [köra den i en anpassad behållare](./quickstart-custom-container.md?pivots=platform-linux%3fpivots%3dplatform-linux).

![Hello-world](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">Installera ruby 2,6 eller senare</a>
* <a href="https://git-scm.com/" target="_blank">Installera Git</a>

## <a name="download-the-sample"></a>Ladda ned exemplet

Kör följande kommando i ett terminalfönster för att klona databasen för exempelappen till den lokala datorn:

```bash
git clone https://github.com/Azure-Samples/ruby-docs-hello-world
```

## <a name="run-the-application-locally"></a>Köra appen lokalt

Kör programmet lokalt så att du ser hur det ska se ut när du distribuerar det till Azure. Öppna ett terminalfönster, ändra till katalogen `hello-world` och använd kommandot `rails server` för att starta servern.

Första steget är att installera de gems du behöver. Det finns ett `Gemfile` inkluderat i exemplet, så kör bara följande kommando:

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

Bläddra till appen för att se din nya webbapp med inbyggd avbildning. Ersätt _&lt; app Name>_ med namnet på din webbapp.

```bash
http://<app_name>.azurewebsites.net
```

Så här bör din nya webbapp se ut:

![Välkomstsida](./media/quickstart-ruby/splash-page.png)

## <a name="deploy-your-application"></a>Distribuera appen

Kör följande kommandon för att distribuera det lokala programmet till din Azure-webbapp:

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

När distributionen är klar väntar du ungefär 10 sekunder på att webbappen ska startas om och navigera sedan till webbappen och verifiera resultaten.

```bash
http://<app-name>.azurewebsites.net
```

![uppdaterad webbapp](./media/quickstart-ruby/hello-world-configured.png)

> [!NOTE]
> När appen startas om kan du se HTTP-statuskoden `Error 503 Server unavailable` i webbläsaren eller på `Hey, Ruby developers!` standard sidan. Det kan ta några minuter innan appen har startats om helt.
>

[!INCLUDE [Clean-up section](../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Ruby on-räler med postgres](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Konfigurera Ruby-app](configure-language-ruby.md)