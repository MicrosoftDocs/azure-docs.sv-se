---
title: Konfigurera Ruby-appar – Azure App Service
description: Lär dig att konfigurera en fördefinierad Ruby-container för din app. I artikeln visas de vanligaste konfigurationsåtgärderna.
ms.topic: quickstart
ms.date: 06/18/2020
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 038d62573b491325adc60647debf17fa87e06cfe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92743693"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Konfigurera en Linux Ruby-app för Azure App Service

Artikeln beskriver hur [Azure App Service](overview.md) kör Ruby-appar i en Linux-container och hur du kan anpassa beteendet i App Service när det behövs. Ruby-appar måste distribueras med alla nödvändiga [gems](https://rubygems.org/gems).

I guiden visas viktiga begrepp och instruktioner för Ruby-utvecklare som använder en inbyggd Linux-container i App Service. Om du aldrig har använt Azure App Service bör du följa [snabbstarten för Ruby](quickstart-ruby.md) och [självstudien för Ruby med PostgreSQL](tutorial-ruby-postgres-app.md) först.

## <a name="show-ruby-version"></a>Visa Ruby-version

Om du vill se den aktuella Ruby-versionen kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Om du vill se alla Ruby-versioner som stöds kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

Du kan köra en Ruby-version som inte stöds genom att skapa en egen containeravbildning i stället. Mer information finns i [Använda anpassad Docker-avbildning](tutorial-custom-container.md?pivots=container-linux).

## <a name="set-ruby-version"></a>Ange Ruby-version

Kör följande kommando i [Cloud Shell](https://shell.azure.com) för att ange Ruby-versionen till 2.3:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> Om du ser fel som liknar nedanstående under distributionen:
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> eller
> ```
> rbenv: version `2.3.1' is not installed
> ```
> Det innebär att Ruby-versionen som har konfigurerats i projektet skiljer sig från den version som är installerad i containern som du kör (`2.3.3` i exemplet ovan). I exemplet ovan kontrollerar du både *Gemfile* och *.ruby-version* samt att Ruby-versionen inte har angetts, eller har angetts till den version som är installerad i containern som du kör (`2.3.3` i exemplet ovan).

## <a name="access-environment-variables"></a>Få åtkomst till miljövariabler

I App Service kan du [ange appinställningar](configure-common.md#configure-app-settings) utanför din appkod. Du kommer sedan åt dem med hjälp av standardmönstret [ENV['\<path-name>']](https://ruby-doc.org/core-2.3.3/ENV.html). Om du till exempel vill få åtkomst till en appinställning med namnet `WEBSITE_SITE_NAME` använder du följande kod:

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Anpassa distributionen

När du distribuerar en [Git-lagringsplats](deploy-local-git.md) eller ett [Zip-paket](deploy-zip.md) med produktionsprocesser påslaget, kör distributionsmotorn (Kudu) automatiskt följande steg efter distributionen som standard:

1. Kontrollera om det finns en *Gemfile*.
1. Kör `bundle clean`. 
1. Kör `bundle install --path "vendor/bundle"`.
1. Kör `bundle package` för att paketera gems i mappen leverantör/cache.

### <a name="use---without-flag"></a>Använd flaggan --without

Om du vill köra `bundle install` med flaggan [--without](https://bundler.io/man/bundle-install.1.html) anger du [appinställningen](configure-common.md#configure-app-settings) `BUNDLE_WITHOUT` till en kommaavgränsad lista med grupper. Följande kommando anger exempelvis `development,test`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Om inställningen definieras kör distributionsmotorn `bundle install` med `--without $BUNDLE_WITHOUT`.

### <a name="precompile-assets"></a>Förkompilera tillgångar

Stegen efter distributionen förkompilerar inte några tillgångar som standard. Om du vill aktivera förkompilering av tillgångar anger du [appinställningen](configure-common.md#configure-app-settings) `ASSETS_PRECOMPILE` till `true`. Kommandot `bundle exec rake --trace assets:precompile` kommer att köras i slutet av stegen efter distributionen. Exempel:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

Mer information finns i [Hantera statiska tillgångar](#serve-static-assets).

## <a name="customize-start-up"></a>Anpassa start

Som standard startar Ruby-containern Rails-servern i följande ordning (mer information finns i [startskriptet](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)):

1. Generera ett [secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security)-värde, om det inte redan finns ett. Det här värdet krävs för att appen ska kunna köras i produktionsläge.
1. Ange miljövariabeln `RAILS_ENV` till `production`.
1. Ta bort eventuella *.pid*-filer i katalogen *tmp/pids* som kan ha lämnats kvar av en Rails-server som har körts tidigare.
1. Kontrollera att alla beroenden har installerats. Annars kan du försöka installera gems från den lokala katalogen *leverantör/cache*.
1. Kör `rails server -e $RAILS_ENV`.

Du kan anpassa startprocessen på följande sätt:

- [Hantera statiska tillgångar](#serve-static-assets)
- [Kör i icke-produktionsläge](#run-in-non-production-mode)
- [Ange secret_key_base manuellt](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>Hantera statiska tillgångar

Rails-servern i Ruby-containern körs som standard i produktionsläge och [förutsätter att tillgångarna är förkompilerade och hanteras av webbservern](https://guides.rubyonrails.org/asset_pipeline.html#in-production). Om du vill hantera statiska tillgångar från Rails-servern måste du göra två saker:

- **Förkompilera tillgångarna** - [Förkompilera de statiska tillgångarna lokalt](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation) och distribuera dem manuellt. Eller låta distributionsmotorn hantera den i stället (se [Förkompilera tillgångar](#precompile-assets).
- **Aktivera hantering av statiska filer** – Om du vill hantera statiska tillgångar från Ruby-containern [anger du appinställningen](configure-common.md#configure-app-settings) `RAILS_SERVE_STATIC_FILES` till `true`. Exempel:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Kör i icke-produktionsläge

Rails-servern körs som standard i produktionsläge. Om du exempelvis vill köra i utvecklingsläge kan du ange [appinställningen](configure-common.md#configure-app-settings) `RAILS_ENV` till `development`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

Den här inställningen gör dock att Rails-servern startar i utvecklingsläge, enbart accepterar localhost-begäranden och inte är tillgänglig utanför containern. Om du vill acceptera förfrågningar om fjärrklienter anger du [appinställningen](configure-common.md#configure-app-settings) `APP_COMMAND_LINE` till `rails server -b 0.0.0.0`. Med appinställningen kan du köra ett anpassat kommando i Ruby-containern. Exempel:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secret_key_base-manually"></a><a name="set-secret_key_base-manually"></a> Ange secret_key_base manuellt

Om du vill använda ett eget `secret_key_base`-värde i stället för att låta App Service generera ett åt dig, anger du [appinställningen](configure-common.md#configure-app-settings) `SECRET_KEY_BASE` till det värde som du vill ha. Exempel:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Öppna en SSH-session i webbläsaren

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudier: Rails-app med PostgreSQL](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Vanliga frågor och svar om App Service på Linux](faq-app-service-linux.md)
