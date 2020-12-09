---
title: Aktivera stegvis distribution av funktioner för mål grupper
titleSuffix: Azure App Configuration
description: Lär dig hur du aktiverar stegvis distribution av funktioner för riktade mål grupper
ms.service: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.topic: conceptual
ms.date: 11/20/2020
ms.openlocfilehash: c415eaeab2edd0a1b324bba4266266201cb50cbf
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929692"
---
# <a name="enable-staged-rollout-of-features-for-targeted-audiences"></a>Aktivera stegvis distribution av funktioner för mål grupper

Med funktions flaggor kan du aktivera eller inaktivera funktioner dynamiskt i ditt program. Funktions filter bestämmer status för en funktions flagga varje gången den utvärderas. `Microsoft.FeatureManagement`Biblioteket innehåller `TargetingFilter` , som aktiverar en funktions flagga för en angiven lista med användare och grupper, eller för en viss procent andel av användarna. `TargetingFilter` är "trög". Det innebär att när en enskild användare får en funktion fortsätter de att se den funktionen på alla framtida begär Anden. Du kan använda `TargetingFilter` för att aktivera en funktion för ett särskilt konto under en demonstration, för att progressivt distribuera nya funktioner till användare i olika grupper eller "ringar", och mycket mer.

I den här artikeln får du lära dig hur du distribuerar en ny funktion i ett ASP.NET Core-webbprogram till angivna användare och grupper med hjälp av `TargetingFilter` Azure App konfiguration.

## <a name="create-a-web-application-with-feature-flags-and-authentication"></a>Skapa ett webb program med funktions flaggor och autentisering

Om du vill distribuera funktioner baserat på användare och grupper behöver du ett webb program som gör det möjligt för användarna att logga in.

1. Skapa ett webb program som autentiserar mot en lokal databas med följande kommando:

   ```dotnetcli
   dotnet new mvc --auth Individual -o TestFeatureFlags
   ```

1. Skapa och kör och välj sedan länken **Registrera** i det övre högra hörnet för att skapa ett nytt användar konto. Använd en e-postadress för `test@contoso.com` . På sidan **Registrera bekräftelse** väljer du **Klicka här för att bekräfta ditt konto**.

1. Följ anvisningarna i [snabb start: Lägg till funktions flaggor i en ASP.net Core app](./quickstart-feature-flag-aspnet-core.md) för att lägga till en funktions flagga i det nya webb programmet.

1. Växla funktions flaggan i app-konfigurationen. Kontrol lera att den här åtgärden styr visningen av **beta** objekt i navigerings fältet.

## <a name="update-the-web-application-code-to-use-targetingfilter"></a>Uppdatera webb program koden för att använda TargetingFilter

I det här läget kan du använda funktions flaggan för att aktivera eller inaktivera `Beta` funktionen för alla användare. Om du vill aktivera funktions flaggan för vissa användare samtidigt som du inaktiverar den för andra, kan du uppdatera koden för att använda `TargetingFilter` . I det här exemplet ska du använda den inloggade användarens e-postadress som användar-ID och domän namns delen av e-postadressen som gruppen. Du lägger till användaren och gruppen i `TargetingContext` . I `TargetingFilter` används den här kontexten för att fastställa status för funktions flaggan för varje begäran.

1. Uppdatera till den senaste versionen av `Microsoft.FeatureManagement.AspNetCore` paketet.

   ```dotnetcli
   dotnet add package Microsoft.FeatureManagement.AspNetCore
   ```

1. Lägg till en *TestTargetingContextAccessor.cs* -fil:

    ```csharp
    using Microsoft.AspNetCore.Http;
    using Microsoft.FeatureManagement.FeatureFilters;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;

    namespace TestFeatureFlags
    {
        public class TestTargetingContextAccessor : ITargetingContextAccessor
        {
            private const string TargetingContextLookup = "TestTargetingContextAccessor.TargetingContext";
            private readonly IHttpContextAccessor _httpContextAccessor;

            public TestTargetingContextAccessor(IHttpContextAccessor httpContextAccessor)
            {
                _httpContextAccessor = httpContextAccessor ?? throw new ArgumentNullException(nameof(httpContextAccessor));
            }

            public ValueTask<TargetingContext> GetContextAsync()
            {
                HttpContext httpContext = _httpContextAccessor.HttpContext;
                if (httpContext.Items.TryGetValue(TargetingContextLookup, out object value))
                {
                    return new ValueTask<TargetingContext>((TargetingContext)value);
                }
                List<string> groups = new List<string>();
                if (httpContext.User.Identity.Name != null)
                {
                    groups.Add(httpContext.User.Identity.Name.Split("@", StringSplitOptions.None)[1]);
                }
                TargetingContext targetingContext = new TargetingContext
                {
                    UserId = httpContext.User.Identity.Name,
                    Groups = groups
                };
                httpContext.Items[TargetingContextLookup] = targetingContext;
                return new ValueTask<TargetingContext>(targetingContext);
            }
        }
    }
    ```

1. I *startup.cs* lägger du till en referens till namn området *Microsoft. FeatureManagement. FeatureFilters* :

    ```csharp
    using Microsoft.FeatureManagement.FeatureFilters;
    ```

1. Uppdatera *ConfigureServices* -metoden för registrering `TargetingFilter` genom att följa anropet till `AddFeatureManagement()` :

    ```csharp
    services.AddFeatureManagement()
            .AddFeatureFilter<TargetingFilter>();
    ```

1. Uppdatera *ConfigureServices* -metoden för att lägga till den `TestTargetingContextAccessor` skapade i föregående steg i tjänst samlingen. *TargetingFilter* använder den för att fastställa mål kontexten varje gång funktions flaggan utvärderas.

    ```csharp
      services.AddSingleton<ITargetingContextAccessor, TestTargetingContextAccessor>();
    ```

Hela *ConfigureServices* -metoden ser ut så här:

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    services.AddDefaultIdentity<IdentityUser>(options => options.SignIn.RequireConfirmedAccount = true)
            .AddEntityFrameworkStores<ApplicationDbContext>();
    services.AddControllersWithViews();
    services.AddRazorPages();

    // Add feature management, targeting filter, and ITargetingContextAccessor to service collection
    services.AddFeatureManagement().AddFeatureFilter<TargetingFilter>();
    services.AddSingleton<ITargetingContextAccessor, TestTargetingContextAccessor>();
    }
```

## <a name="update-the-feature-flag-to-use-targetingfilter"></a>Uppdatera funktions flaggan om du vill använda TargetingFilter

1. Gå till appens konfigurations Arkiv i Azure Portal och välj **funktions hanteraren**.

1. Välj snabb menyn för den *beta* funktions flagga som du skapade i snabb starten. Välj **Redigera**.

    > [!div class="mx-imgBorder"]
    > ![Redigera beta funktions flagga](./media/edit-beta-feature-flag.png)

1. På sidan **Redigera** markerar du kryss rutan **Aktivera funktions flagga** om den inte redan är markerad. Markera sedan kryss rutan **Använd funktions filter** .

1. Välj alternativ knappen för **anpassning** .

1. Välj följande alternativ:

    - **Standard procent**: 0
    - **Grupper**: Ange ett **namn** på _contoso.com_ och en **procent andel** av _50_
    - **Användare**: `test@contoso.com`

    Funktions filtrets skärm ser ut så här:

    > [!div class="mx-imgBorder"]
    > ![Flagga för villkorlig funktion](./media/feature-flag-filter-enabled.png)

    De här inställningarna resulterar i följande beteende:

    - Funktions flaggan är alltid aktive rad för användaren `test@contoso.com` , eftersom `test@contoso.com` den visas i avsnittet _användare_ .
    - Funktions flaggan är aktive rad för 50% av andra användare i _contoso.com_ -gruppen eftersom _contoso.com_ anges i _grupper_ -avsnittet med en _procents ATS_ på _50_.
    - Funktionen är alltid inaktive rad för alla andra användare, eftersom _standard procent andelen_ är inställd på _0_.

1. Välj **tillämpa** för att spara inställningarna och återgå till **funktions hanterarens** skärm.

1. **Funktions filtret** för funktions flaggan visas nu som *mål*. Det här läget anger att funktions flaggan kommer att aktive ras eller inaktive ras för varje begäran, baserat på de kriterier som tillämpas av funktions filtret för *mål* .

## <a name="targetingfilter-in-action"></a>TargetingFilter i praktiken

Skapa och kör programmet om du vill se effekterna av den här funktions flaggan. I början visas inte *beta* objektet i verktygsfältet eftersom _standard procent andelen_ är inställd på 0.

Logga nu in som `test@contoso.com` med det lösen ord som du angav när du registrerade dig. *Beta* -objektet visas nu i verktygsfältet, eftersom `test@contoso.com` har angetts som en mål användare.

Följande video visar hur det fungerar i praktiken.

> [!div class="mx-imgBorder"]
> ![TargetingFilter i praktiken](./media/feature-flags-targetingfilter.gif)

Du kan skapa ytterligare användare med `@contoso.com` e-postadresser för att se hur grupp inställningarna fungerar. 50% av dessa användare kommer att se *beta* -objektet. Den andra 50% ser inte *beta* -objektet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Översikt över funktions hantering](./concept-feature-management.md)