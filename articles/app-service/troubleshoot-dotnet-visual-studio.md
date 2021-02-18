---
title: Felsöka med Visual Studio
description: Lär dig hur du felsöker en App Service-app med hjälp av fjärr fel sökning, spårning och loggnings verktyg som är inbyggda i Visual Studio 2013.
ms.assetid: def8e481-7803-4371-aa55-64025d116c97
ms.devlang: dotnet
ms.topic: article
ms.date: 08/29/2016
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: 65eb4e8fefb74219c927fe507baceca7c55462be
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095724"
---
# <a name="troubleshoot-an-app-in-azure-app-service-using-visual-studio"></a>Felsöka en app i Azure App Service med Visual Studio
## <a name="overview"></a>Översikt
Den här självstudien visar hur du använder Visual Studio-verktyg för att felsöka en app i [App Service](./overview.md), genom att köra i [fel söknings läge](/visualstudio/debugger/) via fjärr anslutning eller genom att visa program loggar och webb server loggar.

Du får lära dig detta:

* Vilka funktioner för hantering av appar är tillgängliga i Visual Studio.
* Så här använder du Visual Studio-fjärrläge för att göra snabba ändringar i en fjärran sluten app.
* Så här kör du fel söknings läget på distans medan ett projekt körs i Azure, både för en app och för ett webb jobb.
* Skapa program spårnings loggar och visa dem medan programmet skapar dem.
* Så här visar du webb server loggar, inklusive detaljerade fel meddelanden och spårning av misslyckade begär Anden.
* Skicka diagnostikloggar till ett Azure Storage konto och visa dem där.

Om du har Visual Studio Ultimate kan du också använda [IntelliTrace](/visualstudio/debugger/intellitrace) för fel sökning. IntelliTrace ingår inte i den här självstudien.

## <a name="prerequisites"></a><a name="prerequisites"></a>Förutsättningar
Den här självstudien fungerar med utvecklings miljön, webb projekt och App Service app som du konfigurerar i [skapa en ASP.net-app i Azure App Service](quickstart-dotnet-framework.md). För WebJobs-avsnitten behöver du det program som du skapar i [Kom igång med Azure WEBJOBS SDK][GetStartedWJ].

Kod exemplen som visas i den här självstudien gäller för ett C# MVC-webbprogram, men fel söknings procedurerna är desamma för Visual Basic-och webb formulär program.

I självstudien förutsätter vi att du använder Visual Studio 2019. 

Funktionen för strömnings loggar fungerar bara för program som är riktade till .NET Framework 4 eller senare.

## <a name="app-configuration-and-management"></a><a name="sitemanagement"></a>Konfiguration och hantering av appar
Visual Studio ger åtkomst till en delmängd av program hanterings funktionerna och de konfigurations inställningar som är tillgängliga i [Azure Portal](https://go.microsoft.com/fwlink/?LinkId=529715). I det här avsnittet ser du vad som är tillgängligt med hjälp av **Server Explorer**. Om du vill se de senaste funktionerna för Azure-integrering kan du även testa **Cloud Explorer** . Du kan öppna båda Fönstren från **Visa** -menyn.

1. Om du inte redan har loggat in på Azure i Visual Studio högerklickar du på **Azure** och väljer anslut till **Microsoft Azure prenumeration** i **Server Explorer**.

    Ett alternativ är att installera ett hanterings certifikat som ger åtkomst till ditt konto. Om du väljer att installera ett certifikat högerklickar du på **Azure** -noden i **Server Explorer** och väljer sedan **Hantera och filtrera prenumerationer** på snabb menyn. I dialog rutan **hantera Microsoft Azure prenumerationer** klickar du på fliken **certifikat** och klickar sedan på **Importera**. Följ anvisningarna för att ladda ned och importera en prenumerations fil (kallas även en *. publishsettings* -fil) för ditt Azure-konto.

   > [!NOTE]
   > Om du hämtar en prenumerations fil sparar du den i en mapp utanför dina käll kods kataloger (till exempel i mappen Hämtade filer) och tar sedan bort den när importen har slutförts. En obehörig användare som får åtkomst till prenumerations filen kan redigera, skapa och ta bort dina Azure-tjänster.
   >
   >

    Mer information om hur du ansluter till Azure-resurser från Visual Studio finns i [tilldela Azure-roller med hjälp av Azure Portal](../role-based-access-control/role-assignments-portal.md).
2. I **Server Explorer** expanderar du **Azure** och expanderar **App Service**.
3. Expandera resurs gruppen som innehåller den app som du skapade i [skapa en ASP.net-app i Azure App Service](quickstart-dotnet-framework.md)och högerklicka sedan på noden app och klicka på **Visa inställningar**.

    ![Visa inställningar i Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    Fliken **Azure-** webbapp visas och du kan se de program hanterings-och konfigurations uppgifter som är tillgängliga i Visual Studio.

    ![Fönstret Azure Web App](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    I den här självstudien använder du List rutorna loggning och spårning. Du ska också använda fjärrfelsökning, men du kommer att använda en annan metod för att aktivera den.

    Information om rutorna appinställningar och anslutnings strängar i det här fönstret finns [Azure App Service: hur program strängar och anslutnings strängar fungerar](https://azure.microsoft.com/blog/windows-azure-web-sites-how-application-strings-and-connection-strings-work/).

    Om du vill utföra en app Management-uppgift som inte kan utföras i det här fönstret klickar du på **Öppna i hanteringsportal** för att öppna ett webbläsarfönster till Azure Portal.

## <a name="access-app-files-in-server-explorer"></a><a name="remoteview"></a>Komma åt AppData i Server Explorer
Du distribuerar vanligt vis ett webb projekt med `customErrors` flaggan i Web.config-filen inställd på `On` eller `RemoteOnly` , vilket innebär att du inte får ett användbart fel meddelande när något går fel. För många fel är allt du får en sida som en av följande:

**Server fel i programmet '/':**

:::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png" alt-text="Skärm bild som visar ett Server fel i program fel i en webbläsare.":::

**Ett fel uppstod:**

:::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png" alt-text="Skärm bild som visar ett exempel på ett allmänt fel som inträffar i en webbläsare.":::

**Sidan kan inte visas på webbplatsen**

:::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png" alt-text="Skärm bild som visar en webbplats kan inte visa sidan fel i en webbläsare.":::

Det enklaste sättet att hitta orsaken till felet är ofta att aktivera detaljerade fel meddelanden, som den första av de föregående skärm bilderna förklarar hur du gör. Detta kräver en ändring i den distribuerade Web.configs filen. Du kan redigera *Web.config* -filen i projektet och distribuera om projektet, eller skapa en [Web.config omvandling](https://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) och distribuera en fel söknings version, men det finns ett snabbare sätt: i **Solution Explorer** kan du direkt Visa och redigera filer i fjärrappen med hjälp av funktionen för *fjärrvisning* .

1. I **Server Explorer** expanderar du **Azure**, expanderar **App Service**, expanderar resurs gruppen som din app finns i och expanderar sedan noden för din app.

    Du ser noder som ger dig åtkomst till appens innehållsfiler och loggfiler.
2. Expandera noden **filer** och dubbelklicka på filen *Web.config* .

    ![Öppna Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    Visual Studio öppnar Web.config-filen från fjärrappen och visar [Remote] bredvid fil namnet i namn listen.
3. Lägg till följande rad i `system.web` elementet:

    `<customErrors mode="Off"></customErrors>`

    ![Redigera Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)
4. Uppdatera webbläsaren som visar ett fel meddelande om att det inte går att hjälpa och nu får du ett detaljerat fel meddelande, till exempel i följande exempel:

    ![Detaljerat felmeddelande](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (Det fel som visas skapades genom att lägga till raden som visas i rött till *Views\Home\Index.cshtml*.)

Att redigera Web.config-filen är bara ett exempel på scenarier där möjligheten att läsa och redigera filer på din App Service app gör fel sökningen enklare.

## <a name="remote-debugging-apps"></a><a name="remotedebug"></a>Fjärrfelsökning av appar
Om det detaljerade fel meddelandet inte innehåller tillräckligt med information, och det inte går att återskapa felet lokalt, är ett annat sätt att köra fel söknings läge på distans. Du kan ange Bryt punkter, ändra minne direkt, stega genom kod och även ändra kodens sökväg.

Fjärrfelsökning fungerar inte i Express-versioner av Visual Studio.

Det här avsnittet visar hur du felsöker fel sökning med det projekt som du skapar i [skapa en ASP.net-app i Azure App Service](quickstart-dotnet-framework.md).

1. Öppna det webb projekt som du skapade i [skapa en ASP.net-app i Azure App Service](quickstart-dotnet-framework.md).

1. Öppna *Controllers\HomeController.cs*.

1. Ta bort `About()` metoden och infoga följande kod i sitt ställe.

    ```csharp
    public ActionResult About()
    {
        string currentTime = DateTime.Now.ToLongTimeString();
        ViewBag.Message = "The current time is " + currentTime;
        return View();
    }
    ```

1. [Ange en Bryt punkt](/visualstudio/debugger/) för `ViewBag.Message` raden.

1. I **Solution Explorer** högerklickar du på projektet och klickar på **publicera**.

1. I list rutan **profil** väljer du samma profil som du använde i [skapa en ASP.net-app i Azure App Service](quickstart-dotnet-framework.md). Klicka sedan på inställningar.

1. I dialog rutan **publicera** klickar du på fliken **Inställningar** och ändrar sedan **konfigurationen** till **Felsök** och klickar sedan på **Spara**.

    ![Publicera i fel söknings läge](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

1. Klicka på **Publicera**. När distributionen är klar och webbläsaren öppnas till Azure-URL: en för din app, Stäng webbläsaren.

1. Högerklicka på din app i **Server Explorer** och klicka sedan på **bifoga fel sökare**.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png" alt-text="Skärm bild av fönstret Server Explorer som visar att en app har valts och sedan klickar på Bifoga fel sökare.":::

    Webbläsaren öppnas automatiskt på Start sidan som körs i Azure. Du kan behöva vänta 20 sekunder eller så medan Azure konfigurerar servern för fel sökning. Den här fördröjningen sker bara första gången du kör i fel söknings läge på en app i en 48-timmarsperiod. När du startar fel sökningen igen under samma period är det ingen fördröjning.

    > [!NOTE] 
    > Om du har problem med att starta fel söknings programmet försöker du göra det med hjälp av **Cloud Explorer** i stället för att **Server Explorer**.
    >

1. Klicka på **om** på menyn.

    Visual Studio stoppas på Bryt punkten och koden körs i Azure, inte på den lokala datorn.

1. Hovra över `currentTime` variabeln för att se tid svärdet.

    ![Visa variabel i fel söknings läge som körs i Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

    Tiden som visas är Azure Server-tiden, som kan finnas i en annan tidszon än den lokala datorn.

1. Ange ett nytt värde för `currentTime` variabeln, till exempel "körs nu i Azure".

1. Tryck på F5 för att fortsätta köra.

     Sidan om att köra i Azure visar det nya värdet som du angav i currentTime-variabeln.

     ![Om sidan med nytt värde](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remote-debugging-webjobs"></a><a name="remotedebugwj"></a> WebJobs för fjärrfelsökning
Det här avsnittet visar hur du felsöker fel sökning med det projekt och den app som du skapar i [Kom igång med Azure WEBJOBS SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Funktionerna som visas i det här avsnittet är endast tillgängliga i Visual Studio 2013 med uppdatering 4 eller senare.

Fjärrfelsökning fungerar bara med kontinuerliga WebJobs. Schemalagda webbjobb på begäran stöder inte fel sökning.

1. Öppna det webb projekt som du skapade i [Kom igång med Azure WEBJOBS SDK][GetStartedWJ].

2. Öppna *functions.cs* i ContosoAdsWebJob-projektet.

3. [Ange en Bryt punkt](/visualstudio/debugger/) för den första instruktionen i `GnerateThumbnail` metoden.

    ![Ange Bryt punkt](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

4. I **Solution Explorer** högerklickar du på webbprojektet (inte webb jobbets projekt) och klickar på **publicera**.

5. I list rutan **profil** väljer du samma profil som du använde i [kom igång med Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

6. Klicka på fliken **Inställningar** och ändra **konfigurationen** till **Felsök** och klicka sedan på **publicera**.

    Visual Studio distribuerar webb-och webb jobbs projekt och webbläsaren öppnas till Azure-URL: en för din app.

7. I **Server Explorer** expanderar du **Azure > App Service > din resurs grupp > dina app > WebJobs > kontinuerlig** och högerklicka sedan på **ContosoAdsWebJob**.

8. Klicka på **bifoga fel sökare**.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png" alt-text="Skärm bild av Server Explorer som visar ContosoAdsWebJob som marker ATS på den nedrullningsbara menyn och bifoga fel sökning har valts.":::

    Webbläsaren öppnas automatiskt på Start sidan som körs i Azure. Du kan behöva vänta 20 sekunder eller så medan Azure konfigurerar servern för fel sökning. Den här fördröjningen sker bara första gången du kör i fel söknings läge på en app i en 48-timmarsperiod. När du startar fel sökningen igen under samma period är det ingen fördröjning.

9. I webbläsaren som öppnas på Start sidan för Contoso-annonser skapar du en ny AD.

    När du skapar en AD skapas ett köat meddelande som hämtas av webb jobbet och bearbetas. När WebJobs-SDK: n anropar funktionen för att bearbeta Queue meddelandet, träffar koden din Bryt punkt.

10. När fel söknings verktyget bryts på Bryt punkten kan du undersöka och ändra variabel värden medan programmet kör molnet. I följande bild visar fel söknings programmet innehållet i blobInfo-objektet som skickades till- `GenerateThumbnail` metoden.

     ![blobInfo-objekt i fel söknings läge](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)

11. Tryck på F5 för att fortsätta köra.

     `GenerateThumbnail`Metoden är klar med att skapa miniatyr bilden.

12. Uppdatera index-sidan i webbläsaren så visas miniatyr bilden.

13. I Visual Studio trycker du på SHIFT + F5 för att stoppa fel sökningen.

14. Högerklicka på ContosoAdsWebJob-noden i **Server Explorer** och klicka på **Visa instrument panel**.

15. Logga in med dina autentiseringsuppgifter för Azure och klicka sedan på webb jobb namnet för att gå till sidan för ditt webb jobb.

     ![Klicka på ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

     Instrument panelen visar att `GenerateThumbnail` funktionen har utförts nyligen.

     (Nästa gången du klickar på **Visa instrument panel** behöver du inte logga in och webbläsaren går direkt till sidan för ditt webb jobb.)

16. Klicka på funktions namnet om du vill se information om funktions körningen.

     ![Funktions information](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Om din funktion [skrev loggar](https://github.com/Azure/azure-webjobs-sdk/wiki)kan du klicka på **ToggleOutput** för att se dem.

## <a name="notes-about-remote-debugging"></a>Information om fjärrfelsökning

* Det rekommenderas inte att du kör i fel söknings läge i produktion. Om din produktions program inte har skalats ut till flera Server instanser förhindrar fel sökning att webb servern svarar på andra begär Anden. Om du har flera webb Server instanser, när du ansluter till fel söknings programmet, får du en slumpmässig instans och du har inget sätt att se till att efterföljande webb läsar begär Anden går till samma instans. Dessutom kan du normalt inte distribuera en fel söknings version till produktion, och kompilator optimeringar för versions byggen kan göra det omöjligt att visa vad som händer rad för rad i käll koden. Vid fel sökning av produktions problem är den bästa resursen program spårning och webb server loggar.
* Undvik långa stopp vid Bryt punkter vid fjärrfelsökning. Azure behandlar en process som har stoppats under en längre tid än några minuter, och stänger av den.
* När du felsöker kommer servern att skicka data till Visual Studio, vilket kan påverka bandbredds avgifter. Information om bandbredds hastigheter finns i [priser för Azure](https://azure.microsoft.com/pricing/calculator/).
* Kontrol lera att `debug` attributet för `compilation` elementet i *Web.config* -filen har angetts till true. Värdet är true som standard när du publicerar en fel söknings versions konfiguration.

    ```xml
    <system.web>
      <compilation debug="true" targetFramework="4.5" />
      <httpRuntime targetFramework="4.5" />
    </system.web>
    ```
* Om du upptäcker att fel söknings programmet inte stegar i den kod som du vill felsöka kan du behöva ändra Just My Codes inställningen.  Mer information finns i [Ange om du vill felsöka endast användar kod med just My Code i Visual Studio](/visualstudio/debugger/just-my-code).
* En timer startar på servern när du aktiverar funktionen för fjärrfelsökning, och efter 48 timmar inaktive ras funktionen automatiskt. Denna gräns på 48 timmar görs av säkerhets-och prestanda skäl. Du kan enkelt aktivera funktionen igen så många gånger som du vill. Vi rekommenderar att du lämnar den inaktive rad när du inte aktivt felsöker.
* Du kan manuellt koppla fel söknings programmet till en process, inte bara app-processen (w3wp.exe). Mer information om hur du använder fel söknings läge i Visual Studio finns i [fel sökning i Visual Studio](/visualstudio/debugger/debugging-in-visual-studio).

## <a name="diagnostic-logs-overview"></a><a name="logsoverview"></a>Översikt över diagnostikloggar
Ett ASP.NET-program som körs i en App Service-app kan skapa följande typer av loggar:

* **Spårnings loggar för program**<br/>
  Programmet skapar dessa loggar genom att anropa metoder i klassen [system. Diagnostics. trace](/dotnet/api/system.diagnostics.trace) .
* **Webb server loggar**<br/>
  Webb servern skapar en loggpost för varje HTTP-begäran till appen.
* **Detaljerade fel meddelande loggar**<br/>
  Webb servern skapar en HTML-sida med ytterligare information om misslyckade HTTP-begäranden (begär Anden som resulterar i status kod 400 eller senare).
* **Spårnings loggar för misslyckade förfrågningar**<br/>
  Webb servern skapar en XML-fil med detaljerad spårnings information för misslyckade HTTP-begäranden. Webb servern tillhandahåller också en XSL-fil för att formatera XML i en webbläsare.

Loggning påverkar app-prestanda, så Azure ger dig möjlighet att aktivera eller inaktivera varje typ av logg vid behov. För program loggar kan du ange att endast loggar över en viss allvarlighets grad ska skrivas. När du skapar en ny app inaktive ras all loggning som standard.

Loggarna skrivs till filer i en mapp för *loggfiler* i appens fil system och är tillgängliga via FTP. Webb server loggar och program loggar kan också skrivas till ett Azure Storage-konto. Du kan behålla en större mängd loggar i ett lagrings konto än vad som är möjligt i fil systemet. Du är begränsad till högst 100 MEGABYTE av loggar när du använder fil systemet. (Fil system loggar gäller endast för kortsiktig kvarhållning. Azure tar bort gamla loggfiler för att göra plats för nya när gränsen har uppnåtts.)  

## <a name="create-and-view-application-trace-logs"></a><a name="apptracelogs"></a>Skapa och Visa program spårnings loggar
I det här avsnittet ska du utföra följande uppgifter:

* Lägg till spårnings instruktioner till det webb projekt som du skapade i [Kom igång med Azure och ASP.net](quickstart-dotnet-framework.md).
* Visa loggarna när du kör projektet lokalt.
* Visa loggarna när de genereras av programmet som körs i Azure.

Information om hur du skapar program loggar i WebJobs finns i [så här fungerar du med Azure Queue Storage med WebJobs SDK – så här skriver du loggar](https://github.com/Azure/azure-webjobs-sdk/wiki). Följande instruktioner för att visa loggar och kontrol lera hur de lagras i Azure gäller även för program loggar som skapats av WebJobs.

### <a name="add-tracing-statements-to-the-application"></a>Lägg till spårnings uttryck i programmet
1. Öppna *Controllers\HomeController.cs* och Ersätt `Index` metoderna, och `About` `Contact` med följande kod för att lägga till `Trace` instruktioner och en `using` instruktion för `System.Diagnostics` :

    ```csharp
    public ActionResult Index()
    {
        Trace.WriteLine("Entering Index method");
        ViewBag.Message = "Modify this template to jump-start your ASP.NET MVC application.";
        Trace.TraceInformation("Displaying the Index page at " + DateTime.Now.ToLongTimeString());
        Trace.WriteLine("Leaving Index method");
        return View();
    }
    
    public ActionResult About()
    {
        Trace.WriteLine("Entering About method");
        ViewBag.Message = "Your app description page.";
        Trace.TraceWarning("Transient error on the About page at " + DateTime.Now.ToShortTimeString());
        Trace.WriteLine("Leaving About method");
        return View();
    }
    
    public ActionResult Contact()
    {
        Trace.WriteLine("Entering Contact method");
        ViewBag.Message = "Your contact page.";
        Trace.TraceError("Fatal error on the Contact page at " + DateTime.Now.ToLongTimeString());
        Trace.WriteLine("Leaving Contact method");
        return View();
    }        
    ```

1. Lägg till en `using System.Diagnostics;` instruktion överst i filen.

### <a name="view-the-tracing-output-locally"></a>Visa spårning av utdata lokalt
1. Tryck på F5 för att köra programmet i fel söknings läge.

    Standard spårnings lyssnaren skriver alla spårningsutdata till **utdatafönstret** , tillsammans med andra fel söknings resultat. Följande bild visar utdata från de spårnings instruktioner som du har lagt till i- `Index` metoden.

    ![Spårning i fel söknings fönster](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    Följande steg visar hur du visar spårningsutdata i en webb sida, utan att kompilera i fel söknings läge.
1. Öppna programmet Web.config-filen (den som finns i projektmappen) och Lägg till ett `<system.diagnostics>` element i slutet av filen precis före det avslutande `</configuration>` elementet:

    ``` xml
    <system.diagnostics>
    <trace>
      <listeners>
        <add name="WebPageTraceListener"
            type="System.Web.WebPageTraceListener,
            System.Web,
            Version=4.0.0.0,
            Culture=neutral,
            PublicKeyToken=b03f5f7f11d50a3a" />
      </listeners>
    </trace>
    </system.diagnostics>
    ```

Med `WebPageTraceListener` kan du Visa spårningsutdata genom att bläddra till `/trace.axd` .
1. Lägg till ett <a href="/previous-versions/dotnet/netframework-4.0/6915t83k(v=vs.100)">spårnings element</a> under `<system.web>` i Web.config-filen, till exempel följande exempel:

    ``` xml
    <trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />
    ```

1. Tryck på CTRL+F5 för att köra programmet.
1. Lägg till *trace. AXD* i webb adressen i adress fältet i webbläsarfönstret och tryck sedan på RETUR (URL: en liknar `http://localhost:53370/trace.axd` ).
1. På sidan **program spårning** klickar du på **Visa information** på den första raden (inte på BrowserLink-raden).

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png" alt-text="Skärm bild av sidan program spårning i en webbläsare som visar Visa detaljer valda på den första raden.":::

    Sidan **information om begäran** visas och i avsnittet **Spåra information** visas utdata från de spårnings instruktioner som du har lagt till i- `Index` metoden.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png" alt-text="Skärm bild av sidan förfrågnings information i en webbläsare som visar ett meddelande markerat i avsnittet spåra information.":::

    Som standard `trace.axd` är endast tillgängligt lokalt. Om du vill göra den tillgänglig från en fjärran sluten app kan du lägga till `localOnly="false"` `trace` elementet i *Web.config* -filen, som visas i följande exempel:

    ```xml
    <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />
    ```

    `trace.axd`Att aktivera i en app för produktion rekommenderas dock inte av säkerhets skäl. I följande avsnitt ser du ett enklare sätt att läsa spårnings loggar i en App Service app.

### <a name="view-the-tracing-output-in-azure"></a>Visa spårnings resultatet i Azure
1. Högerklicka på webb projektet i **Solution Explorer** och klicka på **publicera**.
2. I dialog rutan **Publicera webbplats** klickar du på **publicera**.

    När Visual Studio har publicerat din uppdatering öppnas ett webbläsarfönster till din start sida (förutsatt att du inte har rensat **mål-URL: en** på fliken **anslutning** ).
3. I **Server Explorer** högerklickar du på din app och väljer **Visa strömmande loggar**.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png" alt-text="Skärm bild av Server Explorer när du har högerklickat på din app, med Visa strömmande loggar som marker ATS i ett nytt fönster.":::

    Fönstret **utdata** visar att du är ansluten till tjänsten för logg strömning och lägger till en meddelande rad varje minut som går genom att visa utan att logga in.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png" alt-text="Skärm bild av fönstret utdata som visar ett exempel på en anslutning till en logg strömnings tjänst med meddelande rader.":::

4. I webbläsarfönstret som visar programmets start sida klickar du på **Kontakta**.

    Inom några sekunder visas utdata från spårningen på fel nivå som du har lagt till `Contact` i-metoden i fönstret **utdata** .

    ![Fel spårning i fönstret utdata](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    Visual Studio visar bara spårning på fel nivå eftersom det är standardinställningen när du aktiverar logg övervaknings tjänsten. När du skapar en ny App Service-app är all loggning inaktive rad som standard, som du såg när du öppnade sidan inställningar tidigare:

    ![Program utloggning](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    Men när du har valt **Visa strömmande loggar**, ändrade Visual Studio automatiskt **program loggning (fil system)** till **fel**, vilket innebär att fel nivå loggar rapporteras. För att kunna se alla spårnings loggar kan du ändra den här inställningen till **utförlig**. När du väljer en allvarlighets grad som är lägre än fel rapporteras även alla loggar för högre allvarlighets grader. När du väljer utförlig visas även informations-, varnings-och fel loggar.  

5. I **Server Explorer** högerklickar du på appen och klickar sedan på **Visa inställningar** som du gjorde tidigare.
6. Ändra **program loggning (fil system)** till **utförlig** och klicka sedan på **Spara**.

    ![Inställning av spårnings nivå till utförlig](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)
7. I webbläsarfönstret som nu visar din **kontakt** sida klickar du på **Start**, sedan på **om** och sedan på **kontakt**.

    Inom några sekunder visas alla dina spårnings utdata i fönstret **utdata** .

    ![Utförlig spårning av utdata](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    I det här avsnittet har du aktiverat och inaktiverat loggning med hjälp av appinställningar. Du kan också aktivera och inaktivera spårnings lyssnare genom att ändra Web.config-filen. Men om du ändrar Web.config-filen kommer appens domän att återanvändas, samtidigt som loggningen via app-konfigurationen aktive ras. Om problemet tar lång tid att återskapa, eller om det är tillfälligt, kan åter användning av program domänen "korrigera" och tvinga dig att vänta tills det sker igen. Genom att aktivera diagnostik i Azure kan du börja samla in fel information omedelbart utan att återvinna app-domänen.

### <a name="output-window-features"></a>Funktioner i utmatnings fönster
Fliken **Microsoft Azure loggar** i fönstret **utdata** har flera knappar och en text ruta:

:::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png" alt-text="Skärm bild som visar knapparna och text rutan på fliken Microsoft Azure loggar i fönstret utdata.":::

Dessa utför följande funktioner:

* Rensa fönstret **utdata** .
* Aktivera eller inaktivera Radbyte.
* Starta eller stoppa övervaknings loggar.
* Ange vilka loggar som ska övervakas.
* Hämta loggar.
* Filtrera loggar baserat på en Sök sträng eller ett reguljärt uttryck.
* Stäng fönstret **utdata** .

Om du anger en Sök sträng eller ett reguljärt uttryck filtrerar Visual Studio loggnings information på klienten. Det innebär att du kan ange villkoret när loggarna visas i fönstret **utdata** och du kan ändra filtrerings villkor utan att behöva återskapa loggarna.

## <a name="view-web-server-logs"></a><a name="webserverlogs"></a>Visa webb server loggar
Webb server loggar registrerar alla HTTP-aktiviteter för appen. För att kunna se dem i fönstret **utdata** måste du aktivera dem för appen och berätta för Visual Studio att du vill övervaka dem.

1. På fliken **konfiguration av Azure-webbapp** som du öppnade från **Server Explorer** ändrar du webb serverns loggning till **på** och klickar sedan på **Spara**.

    ![Aktivera webb Server loggning](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)
2. I fönstret **utdata** klickar du på knappen **Ange vilken Microsoft Azure loggar som ska övervakas** .

    ![Ange vilka Azure-loggar som ska övervakas](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)
3. I dialog rutan **Microsoft Azure loggnings alternativ** väljer du **webb server loggar** och klickar sedan på **OK**.

    ![Övervaka webb server loggar](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)
4. I webbläsarfönstret som visar appen, klicka på **Start**, klicka på **om** och klicka sedan på **kontakt**.

    Program loggarna visas vanligt vis först, följt av webb server loggarna. Du kan behöva vänta en stund tills loggarna visas.

    ![Webb server loggar i fönstret utdata](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

Som standard när du först aktiverar webb server loggar med hjälp av Visual Studio skriver Azure loggar till fil systemet. Alternativt kan du använda Azure Portal för att ange att webb server loggar ska skrivas till en BLOB-behållare i ett lagrings konto.

Om du använder portalen för att aktivera loggning av webb server till ett Azure Storage-konto och sedan inaktiverar loggning i Visual Studio, återställs inställningarna för lagrings kontot när du aktiverar loggning i Visual Studio.

## <a name="view-detailed-error-message-logs"></a><a name="detailederrorlogs"></a>Visa detaljerade fel meddelande loggar
Detaljerade fel loggar innehåller ytterligare information om HTTP-begäranden som resulterar i fel svars koder (400 eller senare). För att kunna se dem i fönstret **utdata** måste du aktivera dem för appen och berätta för Visual Studio att du vill övervaka dem.

1. På fliken **konfiguration av Azure-webbapp** som du öppnade från **Server Explorer** ändrar du **detaljerade fel meddelanden** till **på** och klickar sedan på **Spara**.

    ![Aktivera detaljerade fel meddelanden](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. I fönstret **utdata** klickar du på knappen **Ange vilken Microsoft Azure loggar som ska övervakas** .

3. Klicka på **alla loggar** i dialog rutan **Microsoft Azure loggnings alternativ** och klicka sedan på **OK**.

    ![Övervaka alla loggar](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. I adress fältet i webbläsarfönstret lägger du till ett extra Character till URL: en för att orsaka ett 404-fel (till exempel `http://localhost:53370/Home/Contactx` ) och trycker på RETUR.

    Efter några sekunder visas den detaljerade fel loggen i fönstret **utdata** i Visual Studio.

    ![Detaljerad fel logg för fönstret utdata](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    Ctrl + klicka på länken om du vill se loggens utdata som är formaterade i en webbläsare:

    ![Detaljerad fel logg – webbläsarfönster](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="download-file-system-logs"></a><a name="downloadlogs"></a>Hämta fil system loggar
Alla loggar som du kan övervaka i fönstret **utdata** kan också laddas ned som en *. zip* -fil.

1. Klicka på **Hämta strömmande loggar** i fönstret **utdata** .

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png" alt-text="Skärm bild av fönstret utdata som visar knappen Hämta direkt uppspelnings loggar markerad.":::

    Utforskaren öppnas i mappen *hämtade filer* med den nedladdade filen vald.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png" alt-text="Skärm bild av mappen hämtningar i Utforskaren med en nedladdad fil vald.":::

2. Extrahera *. zip* -filen och se följande mappstruktur:

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png" alt-text="Skärm bild av fil strukturen. zip-filmappstrukturen när filen har extraherats.":::

   * Spårnings loggar för program är i *txt* -filer i mappen *LogFiles\Application* .
   * Webb server loggar finns i *. log* -filer i mappen *LogFiles\http\RawLogs* Du kan använda ett verktyg som [log parser](https://www.iis.net/downloads/community/2010/04/log-parser-22) för att visa och manipulera de här filerna.
   * Detaljerade fel meddelande loggar finns i *HTML-* filer i mappen *LogFiles\DetailedErrors* .

     (Mappen *distributioner* är för filer som skapats av käll kontroll publicering. den har inget relaterat till Visual Studio-publicering. *Git* -mappen är för spårningar som rör käll kontroll publicering och logg filen streaming service.)  

<!-- ## <a name="storagelogs"></a>View storage logs
Application tracing logs can also be sent to an Azure storage account, and you can view them in Visual Studio. To do that you'll create a storage account, enable storage logs in the Azure portal, and view them in the **Logs** tab of the **Azure Web App** window.

You can send logs to any or all of three destinations:

* The file system.
* Storage account tables.
* Storage account blobs.

You can specify a different severity level for each destination.

Tables make it easy to view details of logs online, and they support streaming; you can query logs in tables and see new logs as they are being created. Blobs make it easy to download logs in files and to analyze them using HDInsight, because HDInsight knows how to work with blob storage. For more information, see **Hadoop and MapReduce** in [Data Storage Options (Building Real-World Cloud Apps with Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

You currently have file system logs set to verbose level; the following steps walk you through setting up information level logs to go to storage account tables. Information level means all logs created by calling `Trace.TraceInformation`, `Trace.TraceWarning`, and `Trace.TraceError` will be displayed, but not logs created by calling `Trace.WriteLine`.

Storage accounts offer more storage and longer-lasting retention for logs compared to the file system. Another advantage of sending application tracing logs to storage is that you get some additional information with each log that you don't get from file system logs.

1. Right-click **Storage** under the Azure node, and then click **Create Storage Account**.

![Create Storage Account](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

1. In the **Create Storage Account** dialog, enter a name for the storage account.

    The name must be must be unique (no other Azure storage account can have the same name). If the name you enter is already in use you'll get a chance to change it.

    The URL to access your storage account will be *{name}*.core.windows.net.
2. Set the **Region or Affinity Group** drop-down list to the region closest to you.

    This setting specifies which Azure datacenter will host your storage account. For this tutorial your choice won't make a noticeable difference, but for a production web app you want your web server and your storage account to be in the same region to minimize latency and data egress charges. The web app (which you'll create later) should run in a region as close as possible to the browsers accessing your web app in order to minimize latency.
3. Set the **Replication** drop-down list to **Locally redundant**.
   
    When geo-replication is enabled for a storage account, the stored content is replicated to a secondary datacenter to enable failover to that location in case of a major disaster in the primary location. Geo-replication can incur additional costs. For test and development accounts, you generally don't want to pay for geo-replication. For more information, see [Create, manage, or delete a storage account](../storage/common/storage-account-create.md).
4. Click **Create**.

    ![New storage account](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)    
5. In the Visual Studio **Azure Web App** window, click the **Logs** tab, and then click **Configure Logging in Management Portal**.

     <!-- todo:screenshot of new portal if the VS page link goes to new portal -- >
    ![Configure logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

    This opens the **Configure** tab in the portal for your web app.
6. In the portal's **Configure** tab, scroll down to the application diagnostics section, and then change **Application Logging (Table Storage)** to **On**.
7. Change **Logging Level** to **Information**.
8. Click **Manage Table Storage**.

    ![Click Manage TableStorage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

    In the **Manage table storage for application diagnostics** box, you can choose your storage account if you have more than one. You can create a new table or use an existing one.

    ![Manage table storage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)
9. In the **Manage table storage for application diagnostics** box, click the check mark to close the box.
10. In the portal's **Configure** tab, click **Save**.
11. In the browser window that displays the application web app, click **Home**, then click **About**, and then click **Contact**.

     The logging information produced by browsing these web pages is written to the storage account.
12. In the **Logs** tab of the **Azure Web App** window in Visual Studio, click **Refresh** under **Diagnostic Summary**.

     ![Click Refresh](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

     The **Diagnostic Summary** section shows logs for the last 15 minutes by default. You can change the period to see more logs.

     (If you get a "table not found" error, verify that you browsed to the pages that do the tracing after you enabled **Application Logging (Storage)** and after you clicked **Save**.)

     ![Storage logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

     Notice that in this view you see **Process ID** and **Thread ID** for each log, which you don't get in the file system logs. You can see additional fields by viewing the Azure storage table directly.
13. Click **View all application logs**.

     The trace log table appears in the Azure storage table viewer.

     (If you get a "sequence contains no elements" error, open **Server Explorer**, expand the node for your storage account under the **Azure** node, and then right-click **Tables** and click **Refresh**.)

     ![Storage logs in table view](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

     This view shows additional fields you don't see in any other views. This view also enables you to filter logs by using special Query Builder UI for constructing a query. For more information, see Working with Table Resources - Filtering Entities in [Browsing Storage Resources with Server Explorer](https://msdn.microsoft.com/library/ff683677.aspx).
14. To look at the details for a single row, double-click one of the rows.

     ![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)
 -->
## <a name="view-failed-request-tracing-logs"></a><a name="failedrequestlogs"></a>Visa spårnings loggar för misslyckade begär Anden
Spårnings loggar för misslyckade förfrågningar är användbara när du behöver veta mer om hur IIS hanterar en HTTP-begäran i scenarier som URL-omskrivning eller autentiseringsfel.

App Service appar använder samma funktioner för spårning av misslyckade begär Anden som har varit tillgängliga i IIS 7,0 och senare. Du har inte åtkomst till de IIS-inställningar som konfigurerar vilka fel som ska loggas. Om du aktiverar spårning av misslyckade begär Anden, fångas alla fel.

Du kan aktivera spårning av misslyckade förfrågningar med hjälp av Visual Studio, men du kan inte visa dem i Visual Studio. Dessa loggar är XML-filer. Den strömmande logg tjänsten övervakar bara filer som bedöms som läsbara i läget oformaterad text:  *. txt*-, *. html*-och *. log* -filer.

Du kan visa spårnings loggar för misslyckade förfrågningar i en webbläsare direkt via FTP eller lokalt när du har använt ett FTP-verktyg för att ladda ned dem till den lokala datorn. I det här avsnittet ska du visa dem i en webbläsare direkt.

1. På fliken **konfiguration** i fönstret **Azure Web App** som du öppnade från **Server Explorer**, ändra spårning av **misslyckade begär Anden** till **på** och klicka sedan på **Spara**.

    ![Aktivera spårning av misslyckade begär Anden](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)
2. I adress fältet i webbläsarfönstret som visar appen, Lägg till ett extra-värde till URL: en och klicka på RETUR för att orsaka ett 404-fel.

    Detta leder till att en spårnings logg för misslyckade begär Anden skapas och att följande steg visar hur du visar eller laddar ned loggen.

3. I Visual Studio, på fliken **konfiguration** i fönstret **Azure Web App** , klickar du på **Öppna i hanteringsportal**.

4. På sidan [Azure Portal](https://portal.azure.com) **Inställningar** för din app klickar du på **autentiseringsuppgifter för distribution** och anger sedan ett nytt användar namn och lösen ord.

    ![Nytt användar namn och lösen ord för FTP](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    > [!NOTE]
    > När du loggar in måste du använda det fullständiga användar namnet med det namn som har prefixet. Om du till exempel anger "myid" som ett användar namn och platsen är "mitt exempel", logga du in som "myexample\myid".
    >

5. I ett nytt webbläsarfönster går du till den URL som visas under **FTP hostname** eller **FTPS-värdnamn** på **översikts** sidan för din app.

6. Logga in med de FTP-autentiseringsuppgifter som du skapade tidigare (inklusive app Name-prefixet för användar namnet).

    Webbläsaren visar appens rotmapp.

7. Öppna mappen *loggfiler* .

    ![Öppna loggfiler-mappen](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

8. Öppna mappen med namnet W3SVC plus ett numeriskt värde.

    ![Öppna mappen W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    Mappen innehåller XML-filer för eventuella fel som har loggats efter aktivering av spårning av misslyckade begär Anden och en XSL-fil som en webbläsare kan använda för att formatera XML-filen.

    ![Mappen W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

9. Klicka på XML-filen för den misslyckade begäran som du vill visa spårnings information för.

    Följande bild visar en del av spårnings informationen för ett exempel fel.

    ![Spårning av misslyckade begär anden i webbläsare](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

## <a name="next-steps"></a><a name="nextsteps"></a>Nästa steg
Du har sett hur Visual Studio gör det enkelt att visa loggar som skapats av en App Service app. I följande avsnitt finns länkar till fler resurser i närliggande ämnen:

* App Service fel sökning
* Fel sökning i Visual Studio
* Fjärrfelsökning i Azure
* Spårning i ASP.NET-program
* Analysera webb server loggar
* Analysera loggar för spårning av misslyckade begär Anden
* Felsöka Cloud Services

### <a name="app-service-troubleshooting"></a>App Service fel sökning
Mer information om fel sökning av appar i Azure App Service finns i följande resurser:

* [Övervaka appar](web-sites-monitor.md)
* [Undersöka minnes läckor i Azure App Service med Visual Studio 2013](https://devblogs.microsoft.com/devops/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013/). Microsoft ALM blogg inlägg om Visual Studio-funktioner för att analysera hanterade minnes problem.
* [Azure App Service online-verktyg som du bör känna](https://azure.microsoft.com/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/)till. Blogg inlägg från Apple-Amit.

Om du behöver hjälp med en detaljerad fel söknings fråga startar du en tråd i något av följande Forum:

* [Azure-forumet på ASP.NET-webbplatsen](https://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [Azure-forumet på Microsoft Q&A](/answers/topics/azure-webapps.html).
* [StackOverflow.com](https://www.stackoverflow.com).

### <a name="debugging-in-visual-studio"></a>Fel sökning i Visual Studio
Mer information om hur du använder fel söknings läge i Visual Studio finns i [fel sökning i Visual Studio](/visualstudio/debugger/debugging-in-visual-studio) och [fel söknings tips med Visual Studio 2010](https://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx).

### <a name="remote-debugging-in-azure"></a>Fjärrfelsökning i Azure
Mer information om fjärrfelsökning för App Service appar och WebJobs finns i följande resurser:

* [Introduktion till fjärrfelsökning Azure App Service](https://azure.microsoft.com/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Introduktion till fjärrfelsökning Azure App Service del 2 – inuti fjärrfelsökning](https://azure.microsoft.com/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Introduktion till fjärrfelsökning på Azure App Service del 3 – miljö med flera instanser och GIT](https://azure.microsoft.com/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [Fel sökning av WebJobs (video)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Om din app använder ett Azure webb-API eller Mobile Services server del och du behöver felsöka, se [fel sökning av .NET-Server delen i Visual Studio](/archive/blogs/azuremobile/debugging-net-backend-in-visual-studio).

### <a name="tracing-in-aspnet-applications"></a>Spårning i ASP.NET-program
Det finns inga ingående och aktuella introduktioner till ASP.NET-spårning på Internet. Det bästa du kan göra är att komma igång med det gamla introduktions materialet som är skrivet för webb formulär eftersom MVC ännu inte fanns och kompletterar med nyare blogg inlägg som fokuserar på specifika problem. Några lämpliga platser för att starta är följande resurser:

* [Övervakning och telemetri (skapa Real-World molnappar med Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br>
  E-bok kapitel med rekommendationer för spårning i Azure Cloud-program.
* [ASP.NET-spårning](/previous-versions/dotnet/articles/ms972204(v=msdn.10))<br/>
  Gammal men fortfarande en lämplig resurs för en grundläggande introduktion till ämnet.
* [Spårnings lyssnare](/dotnet/framework/debug-trace-profile/trace-listeners)<br/>
  Information om spårnings lyssnare, men nämner inte [WebPageTraceListener](/dotnet/api/system.web.webpagetracelistener).
* [Genom gång: integrera ASP.NET-spårning med system. Diagnostics-spårning](/previous-versions/b0ectfxd(v=vs.140))<br/>
  Den här artikeln är också gammal, men innehåller ytterligare information som den inledande artikeln inte behandlar.
* [Spårning i ASP.NET MVC kniv-vyer](https://devblogs.microsoft.com/aspnet/tracing-in-asp-net-mvc-razor-views/)<br/>
  Förutom att spåra i kniva vyer förklarar inlägget även hur du skapar ett fel filter för att logga alla ohanterade undantag i ett MVC-program. Information om hur du loggar alla ohanterade undantag i ett webb formulär program finns i exemplet global. asax i [fullständigt exempel för fel hanterare](/previous-versions/bb397417(v=vs.140)) på MSDN. Om du vill logga vissa undantag i design-eller webb formulär, men låta standard Ramverks hanteringen verkställas, kan du fånga och återanvända som i följande exempel:

    ```csharp
    try
    {
       // Your code that might cause an exception to be thrown.
    }
    catch (Exception ex)
    {
        Trace.TraceError("Exception: " + ex.ToString());
        throw;
    }
    ```

* [Spårning av strömmande diagnostik från Azure-kommandoraden (plus, på nivå!)](https://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  Så här använder du kommando raden för att göra vad den här självstudien visar hur du gör i Visual Studio. [Det är ett](https://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) verktyg för att felsöka ASP.NET-program.
* [Använda Web Apps loggning och diagnostik – med David Ebbo](https://azure.microsoft.com/documentation/videos/azure-web-site-logging-and-diagnostics/) och [strömmande loggar från Web Apps – med David Ebbo](https://azure.microsoft.com/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Videor efter Scott Hanselman och David Ebbo.

Vid fel loggning är ett alternativ till att skriva en egen spårnings kod att använda ett loggnings ramverk med öppen källkod, till exempel [ELMAH](https://nuget.org/packages/elmah/). Mer information finns i [Glenn Hanselmans blogg inlägg om ELMAH](https://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Du behöver inte heller använda ASP.NET eller `System.Diagnostics` spårning för att hämta strömmande loggar från Azure. Den App Service strömmande logg tjänsten strömmar en *. txt*-, *. html*-eller *. log* -fil som hittas i mappen *loggfiler* . Därför kan du skapa ett eget loggnings system som skriver till appens fil system, och filen strömmas och hämtas automatiskt. Allt du behöver göra är att skriva program kod som skapar filer i *d:\home\logfiles* -mappen.

### <a name="analyzing-web-server-logs"></a>Analysera webb server loggar
Mer information om hur du analyserar webb server loggar finns i följande resurser:

* [LogParser](https://www.iis.net/downloads/community/2010/04/log-parser-22)<br/>
  Ett verktyg för att visa data i webb server loggar (*. log* -filer).
* [Felsöka prestanda problem eller program fel i IIS med LogParser](https://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  En introduktion till logg tolks verktyget som du kan använda för att analysera webb server loggar.
* [Blogg inlägg från Robert Mcmurrays om att använda LogParser](/archive/blogs/robert_mcmurray/using-logparser-with-ftp-7-x-sessions)<br/>
* [HTTP-statuskoden i IIS 7,0, IIS 7,5 och IIS 8,0](https://support.microsoft.com/kb/943891)

### <a name="analyzing-failed-request-tracing-logs"></a>Analysera loggar för spårning av misslyckade begär Anden
Webbplatsen för Microsoft TechNet innehåller avsnittet om [spårning av misslyckade begär Anden](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing) , vilket kan vara användbart för att förstå hur du använder dessa loggar. Den här dokumentationen fokuserar dock främst på konfiguration av spårning av misslyckade begär anden i IIS, som du inte kan göra i Azure App Service.

[GetStarted]: quickstart-dotnetcore.md?pivots=platform-windows
[GetStartedWJ]: https://github.com/Azure/azure-webjobs-sdk/wiki