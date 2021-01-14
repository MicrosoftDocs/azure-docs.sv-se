---
title: Använd funktions filter för att aktivera villkorliga funktions flaggor
titleSuffix: Azure App Configuration
description: Lär dig hur du använder funktions filter för att aktivera villkorliga funktions flaggor
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
author: AlexandraKemperMS
ms.author: alkemper
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 13857580d434736fbf0b12f52d39537ca864a497
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98203154"
---
# <a name="use-feature-filters-to-enable-conditional-feature-flags"></a>Använd funktions filter för att aktivera villkorliga funktions flaggor

Med funktions flaggor kan du aktivera eller inaktivera funktioner i ditt program. En enkel funktions flagga är antingen på eller av. Programmet fungerar alltid på samma sätt. Du kan till exempel distribuera en ny funktion bakom en funktions flagga. När funktions flaggan är aktive rad ser alla användare den nya funktionen. Om du inaktiverar funktions flaggan döljs den nya funktionen.

En _villkorlig funktions flagga_ gör det möjligt att aktivera eller inaktivera funktions flagga dynamiskt. Programmet kan beter sig på olika sätt beroende på funktions flagg kriterier. Anta att du vill visa din nya funktion till en liten delmängd av användarna först. Med en villkorlig funktions flagga kan du aktivera funktions flaggan för vissa användare medan du inaktiverar den för andra. _Funktions filter_ bestämmer funktions flaggans status varje gången den utvärderas.

`Microsoft.FeatureManagement`Biblioteket innehåller tre funktions filter:

- `PercentageFilter` aktiverar funktions flagga baserat på en procents ATS.
- `TimeWindowFilter` aktiverar funktions flagga under en angiven tids period.
- `TargetingFilter` aktiverar funktions flagga för angivna användare och grupper.

Du kan också skapa ett eget funktions filter som implementerar [gränssnittet Microsoft. FeatureManagement. IFeatureFilter](/dotnet/api/microsoft.featuremanagement.ifeaturefilter).

## <a name="registering-a-feature-filter"></a>Registrera ett funktions filter

Du registrerar ett funktions filter genom att anropa `AddFeatureFilter` -metoden och ange namnet på funktions filtret. Till exempel registreras följande kod `PercentageFilter` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddFeatureManagement().AddFeatureFilter<PercentageFilter>();
}
```

## <a name="configuring-a-feature-filter-in-azure-app-configuration"></a>Konfigurera ett funktions filter i Azure App konfiguration

Vissa funktions filter har ytterligare inställningar. Aktiverar till exempel `PercentageFilter` en funktion som baseras på en procent andel. Den har en inställning som definierar procent andelen som ska användas.

Du kan konfigurera de här inställningarna för funktions flaggor som definierats i Azure App-konfigurationen. Följ till exempel de här stegen för `PercentageFilter` att aktivera funktions flagga för 50% av begär anden till en webbapp:

1. Följ anvisningarna i [snabb start: Lägg till funktions flaggor i en ASP.net Core app](./quickstart-feature-flag-aspnet-core.md) för att skapa en webbapp med en funktions flagga.

1. Gå till konfigurations arkivet i Azure Portal och klicka på **funktions hanteraren**.

1. Klicka på snabb menyn för den *beta* funktions flagga som du skapade i snabb starten. Klicka på **Redigera**.

    > [!div class="mx-imgBorder"]
    > ![Redigera beta funktions flagga](./media/edit-beta-feature-flag.png)

1. På sidan **Redigera** väljer du knappen **Aktivera funktions flagga** om den inte redan är markerad. Klicka sedan på knappen **Använd funktions filter** och välj **anpassad**. 

1. I fältet **nyckel** anger du *Microsoft. procent*.

    > [!div class="mx-imgBorder"]
    > ![Lägg till funktions filter](./media/feature-flag-add-filter.png)

1. Klicka på snabb menyn bredvid funktions filter nyckeln. Klicka på **Redigera filter parametrar**.

    > [!div class="mx-imgBorder"]
    > ![Redigera funktions filter parametrar](./media/feature-flags-edit-filter-parameters.png)

1. Ange ett **namn** på ett *värde* och ett **värde** på 50. Fältet **värde** anger procent andelen begär Anden som du vill aktivera funktions filtret för.

    > [!div class="mx-imgBorder"]
    > ![Ange funktions filter parametrar](./media/feature-flag-set-filter-parameters.png)

1. Klicka på **Använd** för att återgå till skärmen **Redigera funktions flagga** . Klicka sedan på **tillämpa** igen för att spara inställningarna för funktions flagga.

1. Funktions flaggans **tillstånd** visas nu som *villkor*. Det här läget anger att funktions flaggan kommer att aktive ras eller inaktive ras för varje begäran, baserat på de kriterier som tillämpas av funktions filtret.

    > [!div class="mx-imgBorder"]
    > ![Flagga för villkorlig funktion](./media/feature-flag-filter-enabled.png)

## <a name="feature-filters-in-action"></a>Funktions filter i åtgärd

Om du vill se effekterna av den här funktions flaggan startar du programmet och klickar på **Uppdatera** i webbläsaren flera gånger. Du ser att *beta* -objektet visas i verktygsfältet om 50% av tiden. Den är dold resten av tiden, eftersom `PercentageFilter` inaktiverar *beta* funktionen för en delmängd begär Anden. Följande video visar hur det fungerar i praktiken.

> [!div class="mx-imgBorder"]
> ![TargetingFilter i praktiken](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Aktivera stegvis distribution av funktioner för mål grupper](./howto-targetingfilter-aspnet-core.md)
