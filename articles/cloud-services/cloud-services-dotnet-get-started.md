---
title: Kom igång med Azure Cloud Services och ASP.NET | Microsoft Docs
description: Lär dig hur du kan skapa en app för flera nivåer med ASP.NET MVC och Azure. Appen körs i en molntjänst med en webbroll och en arbetsroll. Appen använder Entity Framework, SQL Database och Azure Storage-köer och -blobbar.
services: cloud-services, storage
documentationcenter: .net
author: tgore03
manager: carmonm
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: tagore
ms.openlocfilehash: ac843ec2084cd019ec9d3bc90f6c8bbcb5c34279
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590361"
---
# <a name="get-started-with-azure-cloud-services-and-aspnet"></a>Kom igång med Azure Cloud Services och ASP.NET

## <a name="overview"></a>Översikt
Under den här kursen får du lära dig hur du skapar ett .NET-program på flera nivåer med en ASP.NET MVC-klientdel, samt att distribuera det till en [Azure-molntjänst](cloud-services-choose-me.md). Programmet använder [Azure SQL Database](/previous-versions/azure/ee336279(v=azure.100)), [Azure Blob-tjänsten](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage) och [Azure-kötjänsten](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern). Du kan [hämta Visual Studio-projektet](https://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4) från MSDN Code Gallery.

Under kursen får du lära dig hur du skapar och kör programmet lokalt, hur du distribuerar det till Azure och kör det i molnet, och hur du skapar det från grunden. Du kan börja med att skapa från grunden och sedan göra test- och distributionsstegen efteråt om du föredrar det.

## <a name="contoso-ads-application"></a>Contoso Ads-program
Programmet är en anslagstavla för annonser. Användare skapar en annons genom att skriva in text och ladda upp en bild. De kan se en lista över annonser med miniatyrbilder, och de kan se bilden i full storlek när de markerar en annons för att se detaljerna.

![Annonslista](./media/cloud-services-dotnet-get-started/list.png)

Programmet använder det [köcentriska arbetsmönstret](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern) för att avlasta det processorintensiva arbetet med att skapa miniatyrbilder till en serverdelsprocess.

## <a name="alternative-architecture-app-service-and-webjobs"></a>Alternativ arkitektur: App Service och WebJobs
Under den här kursen får du lära dig hur du kör både klient- och serverdelen i en Azure-molntjänst. Ett alternativ är att köra klientdelen i en [Azure App Service](/azure/app-service/) och använda [WebJobs](https://go.microsoft.com/fwlink/?LinkId=390226)-funktionen för serverdelen. Om du vill följa en kurs som använder WebJobs går du till [Kom igång med Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki). Mer information om hur du väljer de tjänster som bäst passar din situation finns i [Jämförelse mellan Azure App Service, Cloud Services och Virtual Machines](/azure/architecture/guide/technology-choices/compute-decision-tree).

## <a name="what-youll-learn"></a>Det här lär du dig
* Hur du aktiverar datorn för Azure-utveckling genom att installera Azure SDK.
* Hur du skapar ett Visual Studio-molntjänstprojekt med en ASP.NET MVC-webbroll och en arbetsroll.
* Så här testar du moln tjänst projektet lokalt med hjälp av Azure Storage emulatorn.
* Hur du publicerar molnprojektet på en Azure-molntjänst och testar det med ett Azure-lagringskonto.
* Hur du laddar upp filer och lagrar dem i Azure Blob-tjänsten.
* Hur du använder Azure-kötjänsten för kommunikation mellan nivåer.

## <a name="prerequisites"></a>Förutsättningar
Kursen förutsätter att du förstår [grundläggande koncept om Azure-molntjänster](cloud-services-choose-me.md), t.ex. termerna *webbroll* och *arbetsroll*.  Det förutsätts även att du kan använda [ASP.NET MVC](https://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)- eller [Web Forms](https://www.asp.net/web-forms/tutorials/aspnet-45/getting-started-with-aspnet-45-web-forms/introduction-and-overview)-projekt i Visual Studio. Exempelprogrammet använder MVC, men större delen av kursen gäller också Web Forms.

Du kan köra appen lokalt utan en Azure-prenumeration, men du behöver en prenumeration för att kunna distribuera programmet i molnet. Om du inte har ett konto kan du [aktivera MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A55E3C668) eller [registrera dig för en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A55E3C668).

Själv studie anvisningarna fungerar med någon av följande produkter:

* Visual Studio 2013
* Visual Studio 2015
* Visual Studio 2017
* Visual Studio 2019

Om du inte har någon av dessa kan Visual Studio installeras automatiskt när du installerar Azure SDK.

## <a name="application-architecture"></a>Programmets arkitektur
Appen lagrar annonser i en SQL-databas och använder Entity Framework Code First för att skapa tabellerna och komma åt data. Databasen lagrar två URL:er för varje annons, en för bilden i full storlek och en för miniatyrbilden.

![Annonstabell](./media/cloud-services-dotnet-get-started/adtable.png)

När en användare laddar upp en bild, lagrar klientdelen som körs i en webbroll bilden i en [Azure-blob](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage), och den lagrar annonsinformationen i databasen med en URL som pekar på blobben. Samtidigt skriver den ett meddelande till en Azure-kö. En serverdelsprocess som körs i en arbetsroll söker regelbundet i kön efter nya meddelanden. När ett nytt meddelande dyker upp, skapar arbetsrollen en miniatyrbild för den bilden och uppdaterar miniatyrbildens URL-databasfält för den annonsen. I följande diagram visas hur programmets olika delar fungerar tillsammans.

![Contoso Ads-arkitektur](./media/cloud-services-dotnet-get-started/apparchitecture.png)

[!INCLUDE [install-sdk](../../includes/install-sdk-2017-2015-2013.md)]

## <a name="download-and-run-the-completed-solution"></a>Hämta och köra den färdiga lösningen
1. Hämta och packa upp den [färdiga lösningen](https://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4).
2. Starta Visual Studio.
3. Gå till **File** (Arkiv-menyn) och välj **Open project** (Öppna projekt), navigera till platsen där du sparade den hämtade lösningen och öppna sedan lösningsfilen.
4. Tryck på CTRL+SKIFT+B för att skapa lösningen.

    Som standard återställer Visual Studio automatiskt NuGet-paketinnehållet som inte ingick i *.zip*-filen. Om paketinnehållet inte återställs kan du installera det manuellt genom att öppna dialogrutan **Manage NuGet Packages for Solution** (Hantera NuGet-paket för lösningen) och klicka på knappen **Restore** (Återställ) högst upp till höger.
5. I **Solution Explorer** ska du kontrollera att **ContosoAdsCloudService** har markerats som startprojekt.
6. Om du använder Visual Studio 2015 eller högre, ska du ändra SQL Server-anslutningssträngen i filen *Web.config* i ContosoAdsWeb-projektet samt i filen *ServiceConfiguration.Local.cscfg* i ContosoAdsCloudService-projektet. I båda fallen ska du ändra ”(localdb)\v11.0” till ”(localdb)\MSSQLLocalDB”.
7. Tryck på CTRL+F5 för att köra programmet.

    När du kör ett molntjänstprojekt lokalt anropar Visual Studio automatiskt *beräkningsemulatorn* och *lagringsemulatorn* i Azure. Beräkningsemulatorn använder datorns resurser för att simulera webbrolls- och arbetsrollsmiljöerna. Lagringsemulatorn använder en [SQL Server Express LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)-databas för att simulera Azure-molnlagring.

    Första gången du kör ett molntjänstprojekt tar det ungefär en minut för emulatorerna att starta. När emulatorerna har startats öppnas standardwebbläsaren med programmets startsida.

    ![Contoso Ads-arkitektur](./media/cloud-services-dotnet-get-started/home.png)
8. Klicka på **Create an Ad** (Skapa en annons).
9. Ange lite testdata och välj en *.jpg*-bild som ska laddas upp, och klicka sedan på **Create** (Skapa).

    ![Sidan Create (Skapa)](./media/cloud-services-dotnet-get-started/create.png)

    Appen går till indexsidan, men den visar inte en miniatyrbild för den nya annonsen eftersom den bearbetningen inte har utförts än.
10. Vänta en stund och uppdatera sedan indexsidan om du vill se miniatyrbilden.

     ![Sidan Index](./media/cloud-services-dotnet-get-started/list.png)
11. Klicka på **Details** (Detaljer) för din annons om du vill se bilden i full storlek.

     ![Sidan Details (Detaljer)](./media/cloud-services-dotnet-get-started/details.png)

Du har kört programmet helt på din lokala dator utan anslutning till molnet. Lagringsemulatorn lagrar kö- och blobbdata i en SQL Server Express LocalDB-databas, och programmet lagrar annonsdata i en annan LocalDB-databas. Entity Framework Code First skapade automatiskt annonsdatabasen första gången webbappen försökte få tillgång till den.

I följande avsnitt får du konfigurera lösningen så att den använder Azure-molnresurser för köer, blobbar och programdatabasen när den körs i molnet. Om du vill fortsätta att köra lösningen lokalt men använda molnet och databasresurser kan du göra det. Det är bara att ställa in anslutningssträngar, vilket du får lära dig här.

## <a name="deploy-the-application-to-azure"></a>Distribuera appen till Azure
Följ dessa steg för att köra programmet i molnet:

* Skapa en Azure-molntjänst.
* Skapa en databas i Azure SQL Database.
* Skapa ett Azure-lagringskonto.
* Konfigurera lösningen så att den använder databasen när den körs i Azure.
* Konfigurera lösningen så att den använder ditt Azure-lagringskonto när den körs i Azure.
* Distribuera projektet till Azure-molntjänsten.

### <a name="create-an-azure-cloud-service"></a>Skapa en Azure-molntjänst
En Azure-molntjänst är den miljö som programmet kommer att köras i.

1. Öppna [Azure-portalen](https://portal.azure.com) i webbläsaren.
2. Klicka på **Skapa en resurs > Compute > Molntjänst**.

3. Ange ett URL-prefix för molntjänsten i textrutan för DNS-namn.

    Den här URL:en måste vara unik.  Du får ett felmeddelande om det prefix du har valt redan används.
4. Ange en ny resursgrupp för tjänsten. Klicka på **Skapa nytt** och ange ett namn i textrutan för resursgruppen, till exempel CS_contososadsRG.

5. Välj den region där du vill distribuera programmet.

    Det här fältet anger vilket datacenter som ska vara värd åt molntjänsten. Om det gäller ett produktionsprogram väljer du den region som ligger närmast dina kunder. Under den här kursen väljer du den region som ligger närmast dig.
5. Klicka på **Skapa**.

    I följande bild skapas en molntjänst med URL:en CSvccontosoads.cloudapp.net.

    ![Ny molntjänst](./media/cloud-services-dotnet-get-started/newcs.png)

### <a name="create-a-database-in-azure-sql-database"></a>Skapa en databas i Azure SQL Database
När appen körs i molnet använder den en molnbaserad databas.

1. I [Azure-portalen](https://portal.azure.com) klickar du på **Skapa en resurs > Databaser > SQL Database**.
2. Ange *contosoads* i rutan **Database Name** (Databasnamn).
3. I **resursgruppen** klickar du på **använd befintliga** och markerar den resursgrupp som används för molntjänsten.
4. I följande bild klickar du på **Server – konfigurera nödvändiga inställningar** och **Skapa en ny server**.

    ![Tunnel till databasservern](./media/cloud-services-dotnet-get-started/newdb.png)

    Om din prenumeration redan har en server kan du istället välja den servern i listrutan.
5. I rutan **Servernamn** anger du *csvccontosodbserver*.

6. Ange ett **inloggningsnamn** och **lösenord** med administratörsbehörighet.

    Om du har valt **Skapa en ny server** anger du inte ett befintligt namn och lösenord här. Du har angett ett nytt namn och lösenord som du definierar nu för när du vill komma åt databasen i framtiden. Om du valde en server som du har skapat vid ett tidigare tillfälle, uppmanas du att ange lösenordet för det administrativa användarkontot som du redan har skapat.
7. Välj samma **Plats** som du valde för molntjänsten.

    När molntjänsten och databasen är i olika datacenter (olika regioner), ökar svarstiden och du debiteras för bandbredden utanför datacentret. Bandbredd inom ett datacenter är kostnadsfri.
8. Markera **Ge Azure-tjänster åtkomst till servern**.
9. Klicka på **Välj** för den nya servern.

    ![Ny server](./media/cloud-services-dotnet-get-started/newdbserver.png)
10. Klicka på **Skapa**.

### <a name="create-an-azure-storage-account"></a>Skapa ett Azure Storage-konto
Ett Azure-lagringskonto tillhandahåller resurser för att lagra kö- och blobbdata i molnet.

I ett riktigt program skapar du vanligtvis separata konton för programdata jämfört med loggningsdata, samt separata konton för testdata jämfört med produktionsdata. Under den här kursen använder du bara ett konto.

1. I [Azure-portalen](https://portal.azure.com) klickar du på **Skapa en resurs > Storage > Storage Account – blob, file, table, queue**.
2. Ange ett URL-prefix i **Namn**-rutan.

    Det här prefixet och texten som du ser under rutan utgör den unika URL:en till ditt lagringskonto. Om det prefix du anger redan har använts av någon annan, måste du välja ett annat prefix.
3. Ange **distributionsmodellen** som *Klassisk*.

4. Välj **Locally redundant storage** (Lokalt redundant) i listrutan **Replication** (Replikering).

    När geo-replikering har aktiverats för ett lagringskonto, replikeras det lagrade innehållet till ett sekundärt datacenter för att aktivera redundans om det skulle inträffa en större katastrof på den primära platsen. Geo-replikering kan medföra ytterligare kostnader. När det gäller test- och utvecklingskonton ska du vanligtvis inte betala för geo-replikering. Mer information finns i [Skapa, hantera eller ta bort ett lagringskonto](../storage/common/storage-create-storage-account.md).

5. I **resursgruppen** klickar du på **använd befintliga** och markerar den resursgrupp som används för molntjänsten.
6. Ställ in rullgardinsmenyn **Plats** i samma region som du skulle välja för en molntjänst.

    När molntjänsten och lagringskontot är i olika datacenter (olika regioner), ökar svarstiden och du debiteras för bandbredden utanför datacentret. Bandbredd inom ett datacenter är kostnadsfri.

    Azure-tillhörighetsgrupper tillhandahåller en funktion som minskar avståndet mellan resurser i datacentret, vilket kan förkorta svarstiden. Under den här kursen används inte tillhörighetsgrupper. Mer information finns i [Skapa en tillhörighetsgrupp i Azure](/previous-versions/azure/reference/gg715317(v=azure.100)).
7. Klicka på **Skapa**.

    ![Nytt lagringskonto](./media/cloud-services-dotnet-get-started/newstorage.png)

    I avbildningen skapas ett lagringskonto med URL:en `csvccontosoads.core.windows.net`.

### <a name="configure-the-solution-to-use-your-database-in-azure-sql-database-when-it-runs-in-azure"></a>Konfigurera lösningen så att den använder databasen i Azure SQL Database när den körs i Azure

Webbprojektet och arbets Rolls projektet var och en har sin egen databas anslutnings sträng, och varje måste peka på databasen i Azure SQL Database när appen körs i Azure.

Du kommer att använda en [Web.config-transformering](https://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) för webbrollen och en inställning för molntjänstmiljö för arbetsrollen.

> [!NOTE]
> I det här och i nästa avsnitt får du lagra autentiseringsuppgifter i projektfiler. [Lagra inte känsliga data i offentliga källkodslager](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#secrets).
>
>

1. Öppna transformeringsfilen *Web.Release.config* i ContosoAdsWeb-projektet för programmets *Web.config*-fil, ta bort kommentarblocket som innehåller ett `<connectionStrings>`-element, och ersätt det med följande kod.

    ```xml
    <connectionStrings>
        <add name="ContosoAdsContext" connectionString="{connectionstring}"
        providerName="System.Data.SqlClient" xdt:Transform="SetAttributes" xdt:Locator="Match(name)"/>
    </connectionStrings>
    ```

    Lämna filen öppen för redigering.
2. I den [klassiska Azure-portalen](https://portal.azure.com) klickar du på **SQL Databases** i den vänstra rutan, klickar på databasen som du skapade för den här kursen och sedan på **Show connection strings** (Visa anslutningssträngar).

    ![Visa anslutningssträngar](./media/cloud-services-dotnet-get-started/showcs.png)

    Portalen visar anslutningssträngar med en platshållare för lösenordet.

    ![Anslutningssträngar](./media/cloud-services-dotnet-get-started/connstrings.png)
3. I transformeringsfilen *Web.Release.config* tar du bort `{connectionstring}` och ersätter den med ADO.NET-anslutningssträngen från Azure-portalen.
4. I anslutningssträngen som du klistrade in i transformeringsfilen *Web.Release.config* ska du ersätta `{your_password_here}` med lösenordet du skapade för den nya SQL-databasen.
5. Spara filen.  
6. Markera och kopiera anslutningssträngen (utan omgivande citattecken) och använd den i följande steg för att konfigurera arbetsrollsprojektet.
7. I **Solution Explorer** går du till **Roles** (Roller) i molntjänstprojektet och högerklickar på **ContosoAdsWorker** och klickar sedan på **Properties** (Egenskaper).

    ![Rollegenskaper](./media/cloud-services-dotnet-get-started/rolepropertiesworker.png)
8. Klicka på fliken **Settings** (Inställningar).
9. Ändra **Service Configuration** (Tjänstkonfiguration) till **Cloud** (Moln).
10. Markera fältet **Value** (Värde) för inställningen `ContosoAdsDbConnectionString`, och klistra sedan in anslutningssträngen som du kopierade i kursens förra avsnitt.

     ![Databasanslutningssträng för arbetsrollen](./media/cloud-services-dotnet-get-started/workerdbcs.png)
11. Spara ändringarna.  

### <a name="configure-the-solution-to-use-your-azure-storage-account-when-it-runs-in-azure"></a>Konfigurera lösningen så att den använder ditt Azure-lagringskonto när den körs i Azure
Azure-lagringskontots anslutningssträngar för både webbrollsprojektet och arbetsrollsprojektet lagras i miljöinställningar i molntjänstprojektet. Det finns en separat uppsättning inställningar som ska användas när programmet körs lokalt och när det körs i molnet för varje projekt. Du kommer att uppdatera molnmiljöinställningarna för både webb- och arbetsrollsprojektet.

1. I **Solution Explorer** högerklickar du på **ContosoAdsWeb** under **Roles** (Roller) i **ContosoAdsCloudService**-projektet. Klicka sedan på **Properties** (Egenskaper).

    ![Rollegenskaper](./media/cloud-services-dotnet-get-started/roleproperties.png)
2. Klicka på fliken **Inställningar** . I list rutan **tjänst konfiguration** väljer du **moln**.

    ![Molnkonfiguration](./media/cloud-services-dotnet-get-started/sccloud.png)
3. Markera posten **StorageConnectionString** och sedan ser du en ellipsknapp (**...**) till höger om raden. Klicka på ellipsknappen för att öppna dialogrutan **Create Storage Connection String** (Skapa lagringsanslutningssträng).

    ![Öppna dialogrutan för att skapa anslutningssträng](./media/cloud-services-dotnet-get-started/opencscreate.png)
4. I dialogrutan **Create Storage Connection String** (Skapa lagringsanslutningssträng) markerar du **Your subscription** (Din prenumeration), väljer det lagringskonto som du skapade tidigare och klickar sedan på **OK**. Om du inte redan har loggat in uppmanas du ange dina Azure-autentiseringsuppgifter.

    ![Skapa lagringsanslutningssträng](./media/cloud-services-dotnet-get-started/createstoragecs.png)
5. Spara ändringarna.
6. Följ samma steg som du använde för anslutningssträngen `StorageConnectionString` för att ange anslutningssträngen `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`.

    Den här anslutningssträngen används för loggning.
7. Följ samma steg som du använde för **ContosoAdsWeb**-rollen för att ange båda anslutningssträngarna för **ContosoAdsWorker**-rollen. Glöm inte att ställa in **Service Configuration** (Tjänstkonfiguration) till **Cloud** (Moln).

Rollmiljöinställningarna som du har konfigurerat i Visual Studio-gränssnittet lagras i följande filer i ContosoAdsCloudService-projektet:

* *ServiceDefinition.csdef* – anger inställningsnamnen.
* *ServiceConfiguration.Cloud.cscfg* – tillhandahåller värden när appen körs i molnet.
* *ServiceConfiguration.Local.cscfg* – tillhandahåller värden när appen körs lokalt.

ServiceDefinition.csdef innehåller till exempel följande definitioner:

```xml
<ConfigurationSettings>
    <Setting name="StorageConnectionString" />
    <Setting name="ContosoAdsDbConnectionString" />
</ConfigurationSettings>
```

Filen *ServiceConfiguration.Cloud.cscfg* innehåller värdena du angav för de inställningarna i Visual Studio.

```xml
<Role name="ContosoAdsWorker">
    <Instances count="1" />
    <ConfigurationSettings>
        <Setting name="StorageConnectionString" value="{yourconnectionstring}" />
        <Setting name="ContosoAdsDbConnectionString" value="{yourconnectionstring}" />
        <!-- other settings not shown -->

    </ConfigurationSettings>
    <!-- other settings not shown -->

</Role>
```

Inställningen `<Instances>` anger det antal virtuella datorer som Azure kommer att köra arbetsrollskoden på. I avsnittet [Nästa steg](#next-steps) hittar du länkar till mer information om att skala ut en molntjänst.

### <a name="deploy-the-project-to-azure"></a>Distribuera projektet till Azure
1. I **Solution Explorer** högerklickar du på **ContosoAdsCloudService**-molnprojektet och väljer sedan **Publish** (Publicera).

   ![Publicera-menyn](./media/cloud-services-dotnet-get-started/pubmenu.png)
2. Klicka på **Next** (Nästa) i **inloggningssteget** i **publiceringsguiden för Azure-program**.

    ![Inloggningssteg](./media/cloud-services-dotnet-get-started/pubsignin.png)
3. Klicka på **Next** (Nästa) i guidens **inställningssteg**.

    ![Inställningssteg](./media/cloud-services-dotnet-get-started/pubsettings.png)

    Standardinställningarna på fliken **Advanced** (Avancerat) fungerar bra för den här kursen. Mer information om fliken Advanced (Avancerat) finns i [Publiceringsguide för Azure-program](https://docs.microsoft.com/azure/vs-azure-tools-publish-azure-application-wizard).
4. Klicka på **Publish** (Publicera) i **sammanfattningssteget**.

    ![Sammanfattningssteg](./media/cloud-services-dotnet-get-started/pubsummary.png)

   Fönstret med **Azure-aktivitetsloggen** öppnas i Visual Studio.
5. Klicka på högerpilen för att visa distributionsinformationen.

    Distributionen kan ta upp till 5 minuter eller mer att slutföra.

    ![Fönstret med Azure-aktivitetsloggen](./media/cloud-services-dotnet-get-started/waal.png)
6. När distributionen har slutförts ska du klicka på **webbappens URL** för att starta programmet.
7. Nu kan du testa appen genom att skapa, visa och redigera vissa annonser, precis som du gjorde när du körde programmet lokalt.

> [!NOTE]
> När du har gjort dina tester kan du ta bort eller stoppa molntjänsten. Även om du inte använder molntjänsten uppstår avgifter eftersom det finns reserverade virtuella datorresurser som är avsedda för den. Om du låter den köra kan dessutom alla som hittar URL:en skapa och visa annonser. Gå till **översiktsfliken** för din molntjänst i [Azure-portalen](https://portal.azure.com), och klicka sedan på knappen **Delete** (Ta bort) längst upp på sidan. Om du bara tillfälligt vill förhindra andra från att komma åt webbplatsen klickar du på **Stop** (Stoppa) i stället. I så fall fortsätter avgifterna att tillkomma. Du kan följa en liknande procedur om du vill ta bort SQL-databasen och lagringskontot när du inte längre behöver dem.
>
>

## <a name="create-the-application-from-scratch"></a>Skapa programmet från grunden
Om du inte redan har hämtat [det färdiga programmet](https://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4) ska du göra det nu. Du kommer att kopiera filer från det hämtade projektet till det nya projektet.

Contoso Ads-programmet skapas i följande steg:

* Skapa en Visual Studio-lösning med molntjänst.
* Uppdatera och lägg till NuGet-paket.
* Ange projektreferenser.
* Konfigurera anslutningssträngar.
* Lägg till kodfiler.

När lösningen har skapats granskar du koden som är unik för molntjänstprojekt samt Azure-blobbar och -köer.

### <a name="create-a-cloud-service-visual-studio-solution"></a>Skapa en Visual Studio-lösning med molntjänst
1. I Visual Studio väljer du **New Project** (Nytt projekt) på menyn **File** (Arkiv).
2. Visa **Visual C#** i den vänstra rutan i dialogrutan **New Project** (Nytt projekt), och välj **molnmallar**. Välj sedan **Azure-molntjänstmallen**.
3. Ange namnet ContosoAdsCloudService för projektet och lösningen, och klicka sedan på **OK**.

    ![Nytt projekt](./media/cloud-services-dotnet-get-started/newproject.png)
4. Lägg till en webbroll och en arbetsroll i dialogrutan **New Azure Cloud Service** (Ny Azure-molntjänst). Ange namnet ContosoAdsWeb för webbrollen och ContosoAdsWorker för arbetsrollen. (Använd pennikonen i den högra rutan för att ändra standardnamnen på rollerna.)

    ![Nytt molntjänstprojekt](./media/cloud-services-dotnet-get-started/newcsproj.png)
5. När du ser dialogrutan **New ASP.NET Project** (Nytt ASP.NET-projekt) för webbrollen, väljer du MVC-mallen och klickar sedan på **Change Authentication** (Ändra autentisering).

    ![Välj autentisering](./media/cloud-services-dotnet-get-started/chgauth.png)
6. I dialogrutan **Change Authentication** (Ändra autentisering) väljer du **No Authentication** (Ingen autentisering) och klickar sedan på **OK**.

    ![Ingen autentisering](./media/cloud-services-dotnet-get-started/noauth.png)
7. Klicka på **OK** i dialogrutan **New ASP.NET Project** (Nytt ASP.NET-projekt).
8. I **Solution Explorer** högerklickar du på lösningen (inte på ett av projekten) och väljer **Add – New Project** (Lägg till – Nytt projekt).
9. I dialogrutan **Add New Project** (Lägg till nytt projekt) väljer du **Windows** under **Visual C#** i den vänstra rutan, och klickar sedan på **klassbiblioteksmallen**.  
10. Ange namnet *ContosoAdsCommon* på projektet och klicka sedan på **OK**.

    Du måste referera till Entity Framework-kontexten och datamodellen från både webb- och arbetsrollsprojektet. Alternativt kan du ange de EF-relaterade klasserna i webbrollsprojektet och referera till det projektet från arbetsrollsprojektet. Men i den alternativa metoden måste arbetsrollsprojektet ha en referens till webbsammansättningar som det inte behöver.

### <a name="update-and-add-nuget-packages"></a>Uppdatera och lägga till NuGet-paket
1. Öppna dialogrutan **Manage NuGet Packages** (Hantera NuGet-paket) för lösningen.
2. Välj **Updates** (Uppdateringar) högst upp i fönstret.
3. Leta efter paketet *WindowsAzure.Storage*, och om du hittar det i listan ska du markera det och välja de webb- och arbetsrollsprojekt där du vill uppdatera det. Klicka sedan på **Update** (Uppdatera).

    Lagringsklientbiblioteket uppdateras oftare än Visual Studio-projektmallar, så det kan ofta hända att versionen i ett projekt som nyligen skapats måste uppdateras.
4. Välj **Browse** (Bläddra) högst upp i fönstret.
5. Leta upp NuGet-paketet *EntityFramework* och installera det i alla tre projekt.
6. Leta upp NuGet-paketet *Microsoft.WindowsAzure.ConfigurationManager* och installera det i arbetsrollsprojektet.

### <a name="set-project-references"></a>Ange projektreferenser
1. Ange en referens till ContosoAdsCommon-projektet i ContosoAdsWeb-projektet. Högerklicka på projektet ContosoAdsWeb och klicka sedan på **referenser**  -  **Lägg till referenser**. Välj **Solution – Projects** (Lösning – Projekt) i den vänstra rutan i dialogrutan **Reference Manager** (Referenshanterare). Välj sedan **ContosoAdsCommon** och klicka på **OK**.
2. Ange en referens till ContosoAdsCommon-projektet i ContosoAdsWorker-projektet.

    ContosoAdsCommon innehåller Entity Framework-datamodellen och -kontextklassen som kommer att användas både av klientdelen och serverdelen.
3. Ange en referens till `System.Drawing` i ContosoAdsWorker-projektet.

    Den här sammansättningen används av serverdelen för att konvertera bilder till miniatyrbilder.

### <a name="configure-connection-strings"></a>Konfigurera anslutningssträngar
I det här avsnittet konfigurerar du Azure Storage- och SQL-anslutningssträngar för lokal testning. Distributionsanvisningarna tidigare i kursen visar hur du anger anslutningssträngarna när appen körs i molnet.

1. Öppna programmets Web.config-file i ContosoAdsWeb-projektet och infoga följande `connectionStrings`-element efter `configSections`-elementet.

    ```xml
    <connectionStrings>
        <add name="ContosoAdsContext" connectionString="Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;" providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    Om du använder Visual Studio 2015 eller högre ersätter du ”v11.0” med ”MSSQLLocalDB”.
2. Spara ändringarna.
3. Högerklicka på ContosoAdsWeb under **Roles** (Roller) i ContosoAdsCloudService-projektet, och klicka sedan på **Properties** (Egenskaper).

    ![Rollegenskaper](./media/cloud-services-dotnet-get-started/roleproperties.png)
4. Klicka på fliken **Settings** (Inställningar) i egenskapsfönstret för **ContosoAdsWeb [roll]**, och klicka sedan på **Add Setting** (Lägg till inställning).

    Lämna **Service Configuration** (Tjänstkonfiguration) inställd på **All Configurations** (Alla konfigurationer).
5. Lägg till en inställning med namnet *StorageConnectionString*. Ange **typen** som *ConnectionString*, och ställ in **värdet** till *UseDevelopmentStorage=true*.

    ![Ny anslutningssträng](./media/cloud-services-dotnet-get-started/scall.png)
6. Spara ändringarna.
7. Följ samma procedur för att lägga till en lagringsanslutningssträng i ContosoAdsWorker-rollegenskaperna.
8. När du har egenskapsfönstret för **ContosoAdsWorker [roll]** öppet lägger du till ytterligare en anslutningssträng:

   * Namn: ContosoAdsDbConnectionString
   * Typ: Sträng
   * Värde: Klistra in samma anslutningssträng som du använde för webbrollsprojektet. (Följande exempel gäller Visual Studio 2013. Glöm inte att ändra datakällan om du kopierar det här exemplet och använder Visual Studio 2015 eller högre.)

       ```
       Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;
       ```

### <a name="add-code-files"></a>Lägg till kodfiler
I det här avsnittet får du kopiera kodfiler från den hämtade lösningen till den nya lösningen. I följande avsnitt visas och förklaras viktiga delar av den här koden.

Om du vill lägga till filer i ett projekt eller en mapp högerklickar du på projektet eller mappen och klickar på **Lägg till**  -  **befintligt objekt**. Välj de filer du vill ha och klicka sedan på **Add** (Lägg till). Om du blir tillfrågad om du vill ersätta befintliga filer klickar du på **Yes** (Ja).

1. Ta bort filen *Class1.cs* i ContosoAdsCommon-projektet och lägg i stället till filerna *Ad.cs* och *ContosoAdscontext.cs* från det hämtade projektet.
2. Lägg till följande filer från det hämtade projektet i ContosoAdsWeb-projektet.

   * *Global.asax.cs*.  
   * I mappen *Views\Shared*: *\_Layout.cshtml*.
   * I mappen *Views\Home*: *Index.cshtml*.
   * I mappen *Controllers*: *AdController.cs*.
   * I mappen *Views\Ad* (skapa mappen först): fem *.cshtml*-filer.
3. Lägg till *WorkerRole.cs* från det hämtade projektet i ContosoAdsWorker-projektet.

Du kan nu skapa och köra programmet enligt de tidigare anvisningarna i kursen, och appen använder lokala databas- och lagringsemulatorresurser.

I de följande avsnitten beskrivs den kod som gäller när du arbetar med Azure-miljön, -blobbar och -köer. Under den här kursen förklaras inte hur du skapar MVC-kontrollanter och vyer med scaffold-teknik, hur du skriver Entity Framework-kod som fungerar med SQL Server-databaser eller grundläggande information om asynkron programmering i ASP.NET 4.5. Mer information om de här ämnena finns i följande resurser:

* [Kom igång med MVC 5](https://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)
* [Kom igång med EF 6 och MVC 5](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc)
* [Introduktion till asynkron programmering i .NET 4.5](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async).

### <a name="contosoadscommon---adcs"></a>ContosoAdsCommon – Ad.cs
Filen Ad.cs definierar en uppräkning för annonskategorier och en POCO-entitetsklass för annonsinformation.

```csharp
public enum Category
{
    Cars,
    [Display(Name="Real Estate")]
    RealEstate,
    [Display(Name = "Free Stuff")]
    FreeStuff
}

public class Ad
{
    public int AdId { get; set; }

    [StringLength(100)]
    public string Title { get; set; }

    public int Price { get; set; }

    [StringLength(1000)]
    [DataType(DataType.MultilineText)]
    public string Description { get; set; }

    [StringLength(1000)]
    [DisplayName("Full-size Image")]
    public string ImageURL { get; set; }

    [StringLength(1000)]
    [DisplayName("Thumbnail")]
    public string ThumbnailURL { get; set; }

    [DataType(DataType.Date)]
    [DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
    public DateTime PostedDate { get; set; }

    public Category? Category { get; set; }
    [StringLength(12)]
    public string Phone { get; set; }
}
```

### <a name="contosoadscommon---contosoadscontextcs"></a>ContosoAdsCommon – ContosoAdsContext.cs
ContosoAdsContext-klassen anger att annonsklassen används i en DbSet-samling som Entity Framework lagrar i en SQL-databas.

```csharp
public class ContosoAdsContext : DbContext
{
    public ContosoAdsContext() : base("name=ContosoAdsContext")
    {
    }
    public ContosoAdsContext(string connString)
        : base(connString)
    {
    }
    public System.Data.Entity.DbSet<Ad> Ads { get; set; }
}
```

Klassen har två konstruktorer. Den första används av webbprojektet och anger namnet på en anslutningssträng som lagras i Web.config-filen. Den andra konstruktorn gör att du kan överföra den faktiska anslutningssträngen som används av arbetsrollsprojektet, eftersom det inte har en Web.config-fil. Du såg tidigare var den här anslutningssträngen lagras, och du kommer längre fram att få se hur koden hämtar anslutningssträngen när den instantierar DbContext-klassen.

### <a name="contosoadsweb---globalasaxcs"></a>ContosoAdsWeb – Global.asax.cs
Kod som anropas från metoden `Application_Start` skapar en blobcontainer för *images* och en kö för *images* om dessa inte redan finns. Det innebär att när du börjar använda ett nytt lagringskonto eller börjar använda lagringsemulatorn på en ny dator, skapas den nödvändiga blobcontainern och kön automatiskt.

Koden får tillgång till lagringskontot genom att använda lagringsanslutningssträngen från *.cscfg*-filen.

```csharp
var storageAccount = CloudStorageAccount.Parse
    (RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
```

Sedan hämtar den en referens till blobcontainern för *images*, skapar containern om den inte redan finns, och anger åtkomstbehörighet för den nya containern. Som standard tillåter nya containrar enbart klienter med lagringskontouppgifter att få tillgång till blobbar. Webbplatsen kräver att blobbarna är offentliga så att den kan visa bilder med URL:er som pekar på bildblobbarna.

```csharp
var blobClient = storageAccount.CreateCloudBlobClient();
var imagesBlobContainer = blobClient.GetContainerReference("images");
if (imagesBlobContainer.CreateIfNotExists())
{
    imagesBlobContainer.SetPermissions(
        new BlobContainerPermissions
        {
            PublicAccess =BlobContainerPublicAccessType.Blob
        });
}
```

Liknande kod hämtar en referens till *images*-kön och skapar en ny kö. I så fall krävs ingen ändring av behörigheter.

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
var imagesQueue = queueClient.GetQueueReference("images");
imagesQueue.CreateIfNotExists();
```

### <a name="contosoadsweb---_layoutcshtml"></a>ContosoAdsWeb – \_Layout.cshtml
Filen *_Layout.cshtml* anger appnamnet i sidhuvudet och sidfoten, och skapar en menypost som heter ”Ads”.

### <a name="contosoadsweb---viewshomeindexcshtml"></a>ContosoAdsWeb – Views\Home\Index.cshtml
Filen *Views\Home\Index.cshtml* visar kategorilänkar på startsidan. Länkarna överför heltalsvärdet för uppräkningen `Category` i en QueryString-variabel till Ads-indexsidan. 

```razor
<li>@Html.ActionLink("Cars", "Index", "Ad", new { category = (int)Category.Cars }, null)</li>
<li>@Html.ActionLink("Real estate", "Index", "Ad", new { category = (int)Category.RealEstate }, null)</li>
<li>@Html.ActionLink("Free stuff", "Index", "Ad", new { category = (int)Category.FreeStuff }, null)</li>
<li>@Html.ActionLink("All", "Index", "Ad", null, null)</li>
```

### <a name="contosoadsweb---adcontrollercs"></a>ContosoAdsWeb – AdController.cs
I filen *AdController.cs* anropar konstruktorn metoden `InitializeStorage` för att skapa Azure Storage-klientbiblioteksobjekt som tillhandahåller en API som kan användas för blobbar och köer.

Sedan hämtar koden en referens till blobcontainern för *images* som du såg tidigare i *Global.asax.cs*. När den gör det anger den en [standardpolicy för återförsök](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling) som är lämplig för en webbapp. Standard principen för exponentiell backoff-återförsök kan orsaka att webbappen slutar svara under mer än en minut vid upprepade försök för ett tillfälligt fel. Återförsökspolicyn som anges här väntar i tre sekunder efter varje försök i upp till tre försök.

```csharp
var blobClient = storageAccount.CreateCloudBlobClient();
blobClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
imagesBlobContainer = blobClient.GetContainerReference("images");
```

Liknande kod hämtar en referens till *images*-kön.

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
queueClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
imagesQueue = queueClient.GetQueueReference("images");
```

Större delen av kontrollantkoden är typisk när du arbetar med en Entity Framework-datamodell med en DbContext-klass. Ett undantag är HttpPost-metoden `Create`, som laddar upp en fil och sparar den i Blob Storage. Modellbindaren tillhandahåller ett [HttpPostedFileBase](/dotnet/api/system.web.httppostedfilebase)-objekt till metoden.

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<ActionResult> Create(
    [Bind(Include = "Title,Price,Description,Category,Phone")] Ad ad,
    HttpPostedFileBase imageFile)
```

Om användaren har valt en fil att ladda upp, laddar koden upp filen, sparar den i en blob och uppdaterar Ad-databasposten med en URL som pekar på blobben.

```csharp
if (imageFile != null && imageFile.ContentLength != 0)
{
    blob = await UploadAndSaveBlobAsync(imageFile);
    ad.ImageURL = blob.Uri.ToString();
}
```

Koden som utför uppladdningen är i metoden `UploadAndSaveBlobAsync`. Den skapar ett GUID-namn för blobben, laddar upp och sparar filen, och returnerar en referens till den sparade blobben.

```csharp
private async Task<CloudBlockBlob> UploadAndSaveBlobAsync(HttpPostedFileBase imageFile)
{
    string blobName = Guid.NewGuid().ToString() + Path.GetExtension(imageFile.FileName);
    CloudBlockBlob imageBlob = imagesBlobContainer.GetBlockBlobReference(blobName);
    using (var fileStream = imageFile.InputStream)
    {
        await imageBlob.UploadFromStreamAsync(fileStream);
    }
    return imageBlob;
}
```

När HttpPost-metoden `Create` laddar upp en blob och uppdaterar databasen, skapar den ett kömeddelande för att informera den serverdelsprocessen att en bild är redo för konvertering till en miniatyrbild.

```csharp
string queueMessageString = ad.AdId.ToString();
var queueMessage = new CloudQueueMessage(queueMessageString);
await queue.AddMessageAsync(queueMessage);
```

Koden för HttpPost-metoden `Edit` är liknande, förutom att om användaren väljer en ny bildfil måste alla blobbar som redan finns tas bort.

```csharp
if (imageFile != null && imageFile.ContentLength != 0)
{
    await DeleteAdBlobsAsync(ad);
    imageBlob = await UploadAndSaveBlobAsync(imageFile);
    ad.ImageURL = imageBlob.Uri.ToString();
}
```

I nästa exempel visas den kod som tar bort blobbar när du tar bort en annons.

```csharp
private async Task DeleteAdBlobsAsync(Ad ad)
{
    if (!string.IsNullOrWhiteSpace(ad.ImageURL))
    {
        Uri blobUri = new Uri(ad.ImageURL);
        await DeleteAdBlobAsync(blobUri);
    }
    if (!string.IsNullOrWhiteSpace(ad.ThumbnailURL))
    {
        Uri blobUri = new Uri(ad.ThumbnailURL);
        await DeleteAdBlobAsync(blobUri);
    }
}
private static async Task DeleteAdBlobAsync(Uri blobUri)
{
    string blobName = blobUri.Segments[blobUri.Segments.Length - 1];
    CloudBlockBlob blobToDelete = imagesBlobContainer.GetBlockBlobReference(blobName);
    await blobToDelete.DeleteAsync();
}
```

### <a name="contosoadsweb---viewsadindexcshtml-and-detailscshtml"></a>ContosoAdsWeb – Views\Ad\Index.cshtml och Details.cshtml
Filen *Index.cshtml* visar miniatyrbilder med andra annonsdata.

```razor
<img src="@Html.Raw(item.ThumbnailURL)" />
```

Filen *Details.cshtml* visar bilden i full storlek.

```razor
<img src="@Html.Raw(Model.ImageURL)" />
```

### <a name="contosoadsweb---viewsadcreatecshtml-and-editcshtml"></a>ContosoAdsWeb – Views\Ad\Create.cshtml och Edit.cshtml
Filerna *Create.cshtml* och *Edit.cshtml* anger formkodning som gör att kontrollanten kan hämta objektet `HttpPostedFileBase`.

```razor
@using (Html.BeginForm("Create", "Ad", FormMethod.Post, new { enctype = "multipart/form-data" }))
```

Ett `<input>`-element instruerar webbläsaren att tillhandahålla en dialogruta för filval.

```razor
<input type="file" name="imageFile" accept="image/*" class="form-control fileupload" />
```

### <a name="contosoadsworker---workerrolecs---onstart-method"></a>ContosoAdsWorker – WorkerRole.cs – OnStart-metoden
Azure-arbetsrollsmiljön anropar metoden `OnStart` i klassen `WorkerRole` när arbetsrollen påbörjas, och den anropar metoden `Run` när metoden `OnStart` avslutas.

Metoden `OnStart` hämtar databasanslutningssträngen från *.cscfg*-filen och skickar den till Entity Framework DbContext-klassen. SQLClient-leverantören används som standard så leverantören behöver inte anges.

```csharp
var dbConnString = CloudConfigurationManager.GetSetting("ContosoAdsDbConnectionString");
db = new ContosoAdsContext(dbConnString);
```

Därefter hämtar metoden en referens till lagringskontot och skapar blobcontainern och kön om de inte redan finns. Koden för den åtgärden liknar det du redan har sett i metoden `Application_Start` för webbrollen.

### <a name="contosoadsworker---workerrolecs---run-method"></a>ContosoAdsWorker – WorkerRole.cs – Run-metoden
Metoden `Run` anropas när metoden `OnStart` har avslutat initieringsarbetet. Metoden kör en oändlig loop som söker efter nya kömeddelanden och bearbetar dem när de kommer in.

```csharp
public override void Run()
{
    CloudQueueMessage msg = null;

    while (true)
    {
        try
        {
            msg = this.imagesQueue.GetMessage();
            if (msg != null)
            {
                ProcessQueueMessage(msg);
            }
            else
            {
                System.Threading.Thread.Sleep(1000);
            }
        }
        catch (StorageException e)
        {
            if (msg != null && msg.DequeueCount > 5)
            {
                this.imagesQueue.DeleteMessage(msg);
            }
            System.Threading.Thread.Sleep(5000);
        }
    }
}
```

Om inget kömeddelande hittas efter varje upprepning av loopen, försätts programmet i viloläge i en sekund. Det förhindrar att arbetsrollen använder orimlig processortid och orsakar lagringstransaktionskostnader. Microsoft Customer Advisory Team berättar om en utvecklare som glömde bort att ta med det här, distribuerade till produktion, och åkte på semester. När de blev tillbaka är deras överblicks kostnad mer än semestern.

Ibland orsakar innehållet i ett kömeddelande ett fel i bearbetningen. Det kallas för *meddelande om ej utförd åtgärd*, och om du precis har loggat ett fel och startat loopen igen kan du försöka bearbeta det meddelandet i oändlighet.  Därför innehåller catch-blocket en if-sats som kontrollerar hur många gånger appen har försökt att bearbeta det aktuella meddelandet, och om det har hänt fler än fem gånger, tas meddelandet bort från kön.

`ProcessQueueMessage` anropas när ett kömeddelande hittas.

```csharp
private void ProcessQueueMessage(CloudQueueMessage msg)
{
    var adId = int.Parse(msg.AsString);
    Ad ad = db.Ads.Find(adId);
    if (ad == null)
    {
        throw new Exception(String.Format("AdId {0} not found, can't create thumbnail", adId.ToString()));
    }

    CloudBlockBlob inputBlob = this.imagesBlobContainer.GetBlockBlobReference(ad.ImageURL);

    string thumbnailName = Path.GetFileNameWithoutExtension(inputBlob.Name) + "thumb.jpg";
    CloudBlockBlob outputBlob = this.imagesBlobContainer.GetBlockBlobReference(thumbnailName);

    using (Stream input = inputBlob.OpenRead())
    using (Stream output = outputBlob.OpenWrite())
    {
        ConvertImageToThumbnailJPG(input, output);
        outputBlob.Properties.ContentType = "image/jpeg";
    }

    ad.ThumbnailURL = outputBlob.Uri.ToString();
    db.SaveChanges();

    this.imagesQueue.DeleteMessage(msg);
}
```

Den här koden läser databasen för att hämta bildens URL, konverterar bilden till en miniatyrbild, sparar miniatyren i en blob, uppdaterar databasen med URL:en för miniatyren i bloben och tar bort kömeddelandet.

> [!NOTE]
> Koden i metoden `ConvertImageToThumbnailJPG` använder klasser i namnområdet System.Drawing för enkelhetens skull. Klasserna i det här namnområdet har emellertid skapats för användning med Windows Forms. De stöds inte för användning i en Windows- eller ASP.NET-tjänst. Mer information om alternativ för bildbearbetning finns i [Dynamic Image Generation](https://www.hanselman.com/blog/BackToBasicsDynamicImageGenerationASPNETControllersRoutingIHttpHandlersAndRunAllManagedModulesForAllRequests.aspx) och [Deep Inside Image Resizing](https://www.hanselminutes.com/313/deep-inside-image-resizing-and-scaling-with-aspnet-and-iis-with-imageresizingnet-author-na).
>
>

## <a name="troubleshooting"></a>Felsökning
Om något inte fungerar när du följer anvisningarna i den här kursen visar vi här några exempel på vanliga fel och hur du kan lösa dem.

### <a name="serviceruntimeroleenvironmentexception"></a>ServiceRuntime.RoleEnvironmentException
Objektet `RoleEnvironment` tillhandahålls av Azure när du kör ett program i Azure eller när du kör programmet lokalt med Azure-beräkningsemulatorn.  Om det här felet uppstår när du kör programmet lokalt ska du kontrollera att du har angett ContosoAdsCloudService-projektet som startprojekt. Då konfigureras projektet att köras med Azure-beräkningsemulatorn.

En av de saker som programmet använder Azure RoleEnvironment till är att hämta anslutningssträngvärdena som lagras i *.cscfg*-filerna, så en annan möjlig orsak till det här undantaget är en anslutningssträng som saknas. Kontrollera att du har skapat StorageConnectionString-inställningen både för moln- och lokala konfigurationer i ContosoAdsWeb-projektet, samt att du har skapat båda anslutningssträngar för båda konfigurationerna i ContosoAdsWorker-projektet. Om du gör sökningen **Sök alla** efter StorageConnectionString i hela lösningen, bör du se den nio gånger i sex filer.

### <a name="cannot-override-to-port-xxx-new-port-below-minimum-allowed-value-8080-for-protocol-http"></a>Cannot override to port xxx. (Det går inte att åsidosätta till port xxx.) New port below minimum allowed value 8080 for protocol http. (Ny port under minsta tillåtna värde 8080 för protokollet http.)
Försök att ändra portnumret som används av webbprojektet. Högerklicka på ContosoAdsWeb-projektet och klicka sedan på **Properties** (Egenskaper). Klicka på fliken **Web** (Webb) och ändra sedan portnumret i inställningen **Project Url** (Projekt-URL).

Ett annat alternativ som eventuellt kan lösa problemet finns i följande avsnitt.

### <a name="other-errors-when-running-locally"></a>Andra fel när du kör programmet lokalt
Som standard använder nya molntjänstprojekt expressversionen av Azure-beräkningsemulatorn för att simulera Azure-miljön. Detta är en förenklad version av den fullständiga beräkningsemulatorn, och under vissa förhållanden fungerar den fullständiga emulatorn men inte expressversionen.  

Om du vill ändra så att projektet använder den fullständiga emulatorn, högerklickar du på ContosoAdsCloudService-projektet och klickar sedan på **Properties** (Egenskaper). Klicka på fliken **Web** (Webb) i fönstret **Properties** (Egenskaper), och markera sedan alternativknappen **Use Full Emulator** (Använd fullständig emulator).

Du måste öppna Visual Studio med administratörsbehörighet för att köra programmet med den fullständiga emulatorn.

## <a name="next-steps"></a>Nästa steg
Contoso Ads-programmet har med avsikt förenklats för den här komma igång-kursen. Det implementerar exempelvis inte [beroendeinmatning](https://www.asp.net/mvc/tutorials/hands-on-labs/aspnet-mvc-4-dependency-injection) eller [centrallager och arbetsenhetsmönster](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application#repo), det använder inte [ett gränssnitt för loggning](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry#log) och inte heller [EF Code First Migrations](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application) för att hantera datamodelländringar eller [EF Connection Resiliency](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application) för att hantera tillfälliga nätverksfel osv.

Här följer några exempelprogram för molntjänster som visar fler verklighetsbaserade kodningsexempel, i ordningen från mindre till mer komplexa:

* [PhluffyFotos](https://code.msdn.microsoft.com/PhluffyFotos-Sample-7ecffd31). Liknande koncept som i Contoso Ads men här finns fler funktioner och fler verklighetsbaserade kodningsexempel.
* [Azure Cloud Service Multi-Tier Application with Tables, Queues, and Blobs](https://code.msdn.microsoft.com/windowsazure/Windows-Azure-Multi-Tier-eadceb36). Introducerar Azure Storage-tabeller samt blobbar och köer. Baserat på en äldre version av Azure SDK för .NET, kräver vissa ändringar för att fungera med den aktuella versionen.

Allmän information om hur du utvecklar för molnet finns i [Skapa verkliga molnappar med Azure](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/introduction).

Om du vill se en videointroduktion till bästa metoder och mönster i Azure Storage, kan du se [Microsoft Azure Storage – What's New, Best Practices and Patterns](https://channel9.msdn.com/Events/Build/2014/3-628).

Mer information finns i följande resurser:

* [Azure Cloud Services, del 1: Inledning](https://justazure.com/microsoft-azure-cloud-services-part-1-introduction/)
* [Hantera molntjänster](cloud-services-how-to-manage-portal.md)
* [Azure Storage](https://docs.microsoft.com/azure/storage/)
* [Hur man väljer molntjänstleverantör](https://azure.microsoft.com/overview/choosing-a-cloud-service-provider/)
