---
title: Azure App återhämtning och haveri beredskap
description: Lean-implementera återhämtning och haveri beredskap med Azure App-konfiguration.
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 7910e2092259081aade799fc662052e5a1375e25
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96930491"
---
# <a name="resiliency-and-disaster-recovery"></a>Återhämtning och haveriberedskap

Azure App konfiguration är för närvarande en regional tjänst. Varje konfigurations Arkiv skapas i en viss Azure-region. Ett områdes omfattande avbrott påverkar alla butiker i den regionen. App-konfigurationen erbjuder inte automatisk redundans till en annan region. Den här artikeln innehåller allmän vägledning om hur du kan använda flera konfigurations lager i Azure-regioner för att öka geo-återhämtningen för ditt program.

## <a name="high-availability-architecture"></a>Arkitektur med hög tillgänglighet

Om du vill utnyttja redundans mellan regioner måste du skapa flera konfigurations lager för appar i olika regioner. Med den här installationen har ditt program minst en extra konfigurations lagring som går tillbaka om den primära lagringen blir otillgänglig. Följande diagram illustrerar topologin mellan ditt program och dess primära och sekundära konfigurations lager:

![Geo-redundanta lager](./media/geo-redundant-app-configuration-stores.png)

Ditt program läser in konfigurationen från både den primära och den sekundära lagrings plats parallellt. Detta ökar risken för att hämta konfigurations data. Du är ansvarig för att hålla data i båda butikerna synkroniserade. I följande avsnitt förklaras hur du kan bygga geo-återhämtning i ditt program.

## <a name="failover-between-configuration-stores"></a>Redundans mellan konfigurations lager

Tekniskt sett är ditt program inte att köra en redundansväxling. Det görs ett försök att hämta samma uppsättning konfigurations data från två program konfigurations lager samtidigt. Ordna koden så att den läses in från den sekundära butiken först och sedan till det primära lagret. Den här metoden säkerställer att konfigurations data i det primära lagret prioriteras när de är tillgängliga. Följande kodfragment visar hur du kan implementera den här ordningen i .NET Core:

#### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                  .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
        })
        .UseStartup<Startup>();
    
```

#### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                    .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
            })
            .UseStartup<Startup>());
```
---

Observera att `optional` parametern har överförts till `AddAzureAppConfiguration` funktionen. När `true` den här parametern har angetts till förhindrar det att programmet inte kan fortsätta om funktionen inte kan läsa in konfigurations data.

## <a name="synchronization-between-configuration-stores"></a>Synkronisering mellan konfigurations lager

Det är viktigt att den geo-redundanta konfigurationen lagrar alla har samma uppsättning data. Det finns två sätt att åstadkomma detta:

### <a name="backup-manually-using-the-export-function"></a>Säkerhetskopiera manuellt med funktionen Exportera

Du kan använda funktionen **Exportera** i appen konfiguration för att kopiera data från den primära butiken till den sekundära på begäran. Den här funktionen är tillgänglig via både Azure Portal och CLI.

Från Azure Portal kan du skicka en ändring till ett annat konfigurations Arkiv genom att följa dessa steg.

1. Gå till fliken **Importera/exportera** och välj **Exportera**  >  **konfigurations**  >  **mål** för app  >  **Välj en resurs**.

1. På det nya bladet som öppnas anger du prenumerationen, resurs gruppen och resurs namnet för din sekundära lagring och väljer sedan **Använd**.

1. Användar gränssnittet har uppdaterats så att du kan välja vilka konfigurations data som du vill exportera till ditt sekundära arkiv. Du kan lämna standardvärdet för tid och ange både **etikett** och **etikett** till samma värde. Välj **Använd**. Upprepa detta för alla etiketter i ditt primära lager.

1. Upprepa föregående steg när konfigurationen ändras.

Export processen kan också uppnås med hjälp av Azure CLI. Följande kommando visar hur du exporterar alla konfigurationer från den primära lagrings platsen till den sekundära:

```azurecli
    az appconfig kv export --destination appconfig --name {PrimaryStore} --dest-name {SecondaryStore} --label * --preserve-labels -y
```

### <a name="backup-automatically-using-azure-functions"></a>Säkerhetskopiera automatiskt med hjälp av Azure Functions

Säkerhets kopierings processen kan automatiseras med hjälp av Azure Functions. Den använder integrationen med Azure Event Grid i app-konfigurationen. När konfigurationen är aktive rad kommer app-konfigurationen att publicera händelser till Event Grid för alla ändringar som görs i nyckel värden i ett konfigurations lager. Därför kan en Azure Functions-app Lyssna på dessa händelser och säkerhetskopiera data i enlighet med detta. Mer information finns i självstudien om [hur du säkerhetskopierar konfigurations Arkiv för appar automatiskt](./howto-backup-config-store.md).

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du kan förbättra ditt program för att uppnå geo-återhämtning under körningen av app-konfigurationen. Du kan också bädda in konfigurations data från App-konfiguration vid bygge eller distributions tid. Mer information finns i [integrera med en CI/CD-pipeline](./integrate-ci-cd-pipeline.md).
