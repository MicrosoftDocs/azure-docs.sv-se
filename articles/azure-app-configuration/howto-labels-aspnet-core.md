---
title: Använd konfiguration per miljö
titleSuffix: Azure App Configuration
description: Använd etiketter för att tillhandahålla konfigurations värden per miljö.
ms.service: azure-app-configuration
author: AlexandraKemperMS
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 3/12/2020
ms.author: alkemper
ms.openlocfilehash: 84286df063994f3def15079cb9b190550d5bd977
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96929624"
---
# <a name="use-labels-to-enable-configurations-for-different-environments"></a>Använd etiketter för att aktivera konfigurationer för olika miljöer

Många program måste använda olika konfigurationer för olika miljöer. Anta att ett program har ett konfigurations värde som definierar den anslutnings sträng som ska användas för backend-databasen. Programutvecklarna använder en annan databas än den som används i produktionen. Databas anslutnings strängen som används av programmet måste ändras när programmet flyttas från utveckling till produktion.

I Azure App konfiguration kan du använda *Etiketter* för att definiera olika värden för samma nyckel. Du kan till exempel definiera en enskild nyckel med olika värden för utveckling och produktion. Du kan ange vilken etikett som ska läsas in när du ansluter till app-konfigurationen.

För att demonstrera den här funktionen ändrar du webbappen som skapats i [snabb start: skapa en ASP.net Core-app med Azure App konfiguration](./quickstart-aspnet-core-app.md) för att använda olika konfigurations inställningar för utveckling jämfört med produktion. Slutför snabb starten innan du fortsätter.

## <a name="specify-a-label-when-adding-a-configuration-value"></a>Ange en etikett när du lägger till ett konfigurations värde

I Azure Portal går du till **konfigurations Utforskaren** och hittar *TestApp: Settings: FontColor* -nyckeln som du skapade i snabb starten. Välj snabb menyn och välj sedan **Lägg till värde**.

> [!div class="mx-imgBorder"]
> ![Meny alternativet Lägg till värde](media/labels-add-value.png)

På skärmen **Lägg till värde** anger du **värdet** **röd** och en **utvecklings** **etikett** . Lämna **innehålls typen** tom. Välj **Använd**.

## <a name="load-configuration-values-with-a-specified-label"></a>Läs in konfigurations värden med en angiven etikett

Som standard läser Azure App-konfiguration bara konfigurations värden utan etikett. Om du har definierat etiketter för dina konfigurations värden vill du ange vilka etiketter som ska användas när du ansluter till app-konfigurationen.

I föregående avsnitt skapade du ett annat konfigurations värde för utvecklings miljön. Du kan använda `HostingEnvironment.EnvironmentName` variabeln för att dynamiskt avgöra vilken miljö appen för närvarande körs i. Mer information finns [i använda flera miljöer i ASP.net Core](/aspnet/core/fundamentals/environments).

Läs in konfigurations värden med etiketten som motsvarar den aktuella miljön genom att skicka miljö namnet till- `Select` metoden:

```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options
                        .Connect(Environment.GetEnvironmentVariable("AppConfigConnectionString"))
                        // Load configuration values with no label
                        .Select(KeyFilter.Any, LabelFilter.Null)
                        // Override with any configuration values specific to current hosting env
                        .Select(KeyFilter.Any, hostingContext.HostingEnvironment.EnvironmentName)
                );
            })
            .UseStartup<Startup>());
```

> [!IMPORTANT]
> Föregående kodfragment läser in anslutnings strängen för app-konfigurationen från en miljö variabel med namnet `AppConfigConnectionString` . Se till att miljövariabeln är korrekt inställd.

`Select`Metoden anropas två gånger. Första gången laddar den konfigurations värden utan etikett. Sedan läser den in konfigurations värden med etiketten som motsvarar den aktuella miljön. Dessa miljörelaterade värden åsidosätter motsvarande värden utan etikett. Du behöver inte definiera miljöspecificerade värden för varje nyckel. Om en nyckel inte har ett värde med en etikett som motsvarar den aktuella miljön, används värdet utan etikett.

## <a name="test-in-different-environments"></a>Testa i olika miljöer

Öppna `launchSettings.json` filen under `Properties` katalogen. Hitta `config` posten under `profiles` . I `environmentVariables` avsnittet anger du `ASPNETCORE_ENVIRONMENT` variabeln till `Production` .

Skapa och kör ditt program med de nya värdena som angetts.

```dotnetcli
dotnet build
dotnet run
```

Använd en webbläsare för att gå till `http://localhost:5000` . Du ser att tecken färgen är svart.

![Webb program som körs med produktions konfigurationen](media/labels-website-prod.png)

Uppdatera `launchSettings.json` för att ange `ASPNETCORE_ENVIRONMENT` variabeln till `Development` . Kör `dotnet run` igen. 

Nu ser du att tecken färgen är röd. Detta beror på att programmet nu använder värdet för `TestApp:Settings:FontColor` som har `Development` etiketten. Alla andra konfigurations värden förblir samma som deras produktions värden.

![Webb program som kör med utvecklings konfiguration](media/labels-website-dev.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfiguration i ASP.NET Core](/aspnet/core/fundamentals/configuration/)
