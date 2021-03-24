---
title: Distribuera appar förutsägbart med ARM
description: Lär dig hur du distribuerar flera Azure App Service-appar som en enda enhet och på ett förutsägbart sätt med hjälp av Azure Resource Management-mallar och PowerShell-skript.
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 01/06/2016
ms.custom: seodec18
ms.openlocfilehash: aec23c28e075dd38fa65f1315f9abd9e21cdc9cb
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951478"
---
# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Etablera och distribuera mikrotjänster förutsägbart i Azure
Den här självstudien visar hur du etablerar och distribuerar ett program som består av [mikrotjänster](https://en.wikipedia.org/wiki/Microservices) i [Azure App Service](https://azure.microsoft.com/services/app-service/) som en enda enhet och på ett förutsägbart sätt med hjälp av JSON-mallar för resurs grupper och PowerShell-skript. 

Vid etablering och distribution av storskaliga program som består av mycket fristående mikrotjänster, är repeterbarhet och förutsägbara för att lyckas. Med [Azure App Service](https://azure.microsoft.com/services/app-service/) kan du skapa mikrotjänster som omfattar webbappar, mobila Server delar och API-appar. Med [Azure Resource Manager](../azure-resource-manager/management/overview.md) kan du hantera alla mikrotjänster som en enhet, tillsammans med resurs beroenden, till exempel inställningar för databas och käll kontroll. Nu kan du också distribuera ett sådant program med hjälp av JSON-mallar och enkla PowerShell-skript. 

## <a name="what-you-will-do"></a>Vad du ska göra
I självstudien distribuerar du ett program som innehåller:

* Två App Service-appar (dvs. två mikrotjänster)
* En server del SQL Database
* Appinställningar, anslutnings strängar och käll kontroll
* Application Insights, varningar, inställningar för automatisk skalning

## <a name="tools-you-will-use"></a>Verktyg som du kommer att använda
I den här självstudien ska du använda följande verktyg. Eftersom det inte är en omfattande diskussion om verktyg, ska jag gå till scenariot från slut punkt till slut punkt och bara ge dig en kort introduktion till var och en där du hittar mer information om den. 

### <a name="azure-resource-manager-templates-json"></a>Azure Resource Manager mallar (JSON)
Varje gång du skapar en app i Azure App Service kan Azure Resource Manager till exempel använda en JSON-mall för att skapa hela resurs gruppen med komponent resurserna. En komplex mall från [Azure Marketplace](../marketplace/index.yml) kan omfatta databasen, lagrings konton, App Service plan, själva appen, aviserings regler, appinställningar, inställningar för autoskalning med mera och alla dessa mallar är tillgängliga för dig via PowerShell. Mer information om Azure Resource Manager mallar finns i [redigera Azure Resource Manager mallar](../azure-resource-manager/templates/template-syntax.md)

### <a name="azure-sdk-26-for-visual-studio"></a>Azure SDK 2,6 för Visual Studio
Det senaste SDK: n innehåller förbättringar av stödet för Resource Manager-mallar i JSON-redigeraren. Du kan använda det här för att snabbt skapa en mall för en resurs grupp från början eller öppna en befintlig JSON-mall (till exempel en Hämtad galleri mall) för ändring, fylla i parameter filen och till och med distribuera resurs gruppen direkt från en Azure Resource Group-lösning.

Mer information finns i [Azure SDK 2,6 för Visual Studio](https://azure.microsoft.com/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### <a name="azure-powershell-080-or-later"></a>Azure PowerShell 0.8.0 eller senare
Från och med version 0.8.0 inkluderar den Azure PowerShell installationen Azure Resource Manager modulen förutom Azure-modulen. Med den här nya modulen kan du skripta distributionen av resurs grupper.

Mer information finns i [använda Azure PowerShell med Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md)

### <a name="azure-resource-explorer"></a>Azure Resource Explorer
Med det här för [hands versions verktyget](https://resources.azure.com) kan du utforska JSON-definitionerna för alla resurs grupper i din prenumeration och de enskilda resurserna. I verktyget kan du redigera JSON-definitionerna för en resurs, ta bort en hel hierarki med resurser och skapa nya resurser.  Den information som är tillgänglig i det här verktyget är mycket användbar för att skapa mallar, eftersom det visar vilka egenskaper du behöver ange för en viss typ av resurs, korrekta värden osv. Du kan även skapa en resurs grupp i [Azure Portal](https://portal.azure.com/)och sedan kontrol lera dess JSON-definitioner i Explorer-verktyget för att hjälpa dig att mallanpassa resurs gruppen.

### <a name="deploy-to-azure-button"></a>Distribuera till Azure (knapp)
Om du använder GitHub för käll kontroll kan du placera en [distribuera till Azure-knapp](../azure-resource-manager/templates/deploy-to-azure-button.md) i din README. MD, som möjliggör ett användar gränssnitt för en nyckel distribution till Azure. Även om du kan göra detta för alla enkla appar kan du utöka det så att du kan distribuera en hel resurs grupp genom att placera en azuredeploy.jspå filen i lagrings platsens rot. Den här JSON-filen, som innehåller resurs grupp mal len, kommer att användas av knappen distribuera till Azure för att skapa resurs gruppen. Ett exempel finns i [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) -exemplet som du kommer att använda i den här självstudien.

## <a name="get-the-sample-resource-group-template"></a>Hämta mall för exempel resurs grupp
Nu ska vi komma åt det.

1. Gå till [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) App Service-exemplet.
2. I readme.md klickar **du på distribuera till Azure**.
3. Du kommer till webbplatsen för att [distribuera till Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure-appservice-samples%2FToDoApp%2Fmaster%2Fazuredeploy.json) och uppmanas att ange distributions parametrar. Observera att de flesta fält fylls med databas namnet och vissa slumpmässiga strängar åt dig. Du kan ändra alla fält om du vill, men de enda saker som du måste ange är SQL Server Administratörs inloggning och lösen ordet. Klicka sedan på **Nästa**.
   
   ![Visar parametrarna för ingångs distribution på webbplatsen för distribution till Azure.](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. Starta sedan distributions processen genom att klicka på **distribuera** . När processen har slutförts klickar du på länken http://todoapp *XXXX*. azurewebsites.net för att bläddra i det distribuerade programmet. 
   
   ![Visar programmets distributions process.](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   Användar gränssnittet skulle vara lite långsamt när du först bläddrar till det eftersom apparna bara startas, men är övertygade om att det är ett fullständigt fungerande program.
5. Tillbaka på sidan distribuera klickar du på länken **Hantera** för att se det nya programmet i Azure-portalen.
6. I list rutan **Essentials** klickar du på länken resurs grupp. Observera också att appen redan är ansluten till GitHub-lagringsplatsen under **externt projekt**. 
   
   ![Visar länken resurs grupp i list rutan Essentials.](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. Observera att det redan finns två appar och en SQL Database i resurs gruppen på bladet resurs grupp.
   
   ![Visar tillgängliga resurser i resurs gruppen.](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

Allt som du precis såg på några få minuter är ett fullständigt distribuerat program med två mikrotjänster, med alla komponenter, beroenden, inställningar, databaser och kontinuerlig publicering, som konfigureras med ett automatiskt dirigering i Azure Resource Manager. Allt detta utfördes av två saker:

* Knappen distribuera till Azure
* azuredeploy.jspå i lagrings platsen-roten

Du kan distribuera samma program, hundratals eller tusentals gånger och ha exakt samma konfiguration varje gång. Repeterbarheten och den förutsägbara metoden i den här metoden gör det möjligt att distribuera storskaliga program med enkel och säkerhet.

## <a name="examine-or-edit-azuredeployjson"></a>Granska (eller redigera) AZUREDEPLOY.JSpå
Nu ska vi titta på hur GitHub-lagringsplatsen har kon figurer ATS. Du kommer att använda JSON-redigeraren i Azure .NET SDK, så om du inte redan har installerat [Azure .net sdk 2,6](https://azure.microsoft.com/downloads/)gör du det nu.

1. Klona [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) -lagringsplatsen med ditt favoritbaserade git-verktyg. I skärm bilden nedan gör jag det här i team Explorer i Visual Studio 2013.
   
   ![Visar hur du använder ett git-verktyg för att klona ToDoApp-lagringsplatsen.](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. Från lagrings platsens rot öppnar du azuredeploy.jspå i Visual Studio. Om du inte ser JSON-dispositions fönstret måste du installera Azure .NET SDK.
   
   ![Visar fönstret JSON-disposition i Visual Studio.](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Jag kommer inte att beskriva varje detalj i JSON-formatet, men avsnittet [fler resurser](#resources) innehåller länkar för att lära sig om resurs gruppens språk. Här kommer jag bara att visa de intressanta funktionerna som kan hjälpa dig att komma igång med att skapa en egen anpassad mall för app-distribution.

### <a name="parameters"></a>Parametrar
Ta en titt på avsnittet parametrar för att se att de flesta av dessa parametrar är vad som visas i knappen **distribuera till Azure** och du uppmanas att ange. Platsen bakom knappen **distribuera till Azure** fyller på indatamängds-gränssnittet med de parametrar som definierats i azuredeploy.jspå. Dessa parametrar används i alla resurs definitioner, t. ex. resurs namn, egenskaps värden osv.

### <a name="resources"></a>Resurser
I noden resurser kan du se att 4 toppnivå resurser definieras, inklusive en SQL Server instans, en App Service plan och två appar. 

#### <a name="app-service-plan"></a>App Service-plan
Vi börjar med en enkel resurs på rot nivå i JSON. I JSON-dispositionen klickar du på App Service plan med namnet **[hostingPlanName]** för att markera motsvarande JSON-kod. 

![Visar avsnittet [hostingPlanName] i JSON-koden.](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Observera att `type` elementet anger en sträng för en app service plan (den kallades för en Server grupp för lång tid sedan) och andra element och egenskaper fylls i med hjälp av de parametrar som definierats i JSON-filen och den här resursen har inga kapslade resurser.

> [!NOTE]
> Observera också att värdet för talar om för `apiVersion` Azure vilken version av REST API som ska använda JSON-resurs definitionen med, och det kan påverka hur resursen ska formateras inuti `{}` . 
> 
> 

#### <a name="sql-server"></a>SQL Server
Klicka sedan på SQL Server resursen med namnet **SQLServer** i JSON-dispositionen.

![Visar SQL Server resursen med namnet SQLServer i JSON-dispositionen.](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

Observera följande om den markerade JSON-koden:

* Användningen av parametrar säkerställer att de skapade resurserna namnges och konfigureras på ett sätt som gör dem konsekventa med varandra.
* SQLServer-resursen har två kapslade resurser, var och en har ett annat värde för `type` .
* De kapslade resurserna i `“resources”: […]` , där databasen och brand Väggs reglerna har definierats, har ett- `dependsOn` element som anger resurs-ID: t för rot nivåns SQLServer-resurs. Detta meddelar Azure Resource Manager, "innan du skapar den här resursen måste den andra resursen redan finnas. och om den andra resursen definieras i mallen skapar du den först.
  
  > [!NOTE]
  > Detaljerad information om hur du använder `resourceId()` funktionen finns i [Azure Resource Manager mall funktioner](../azure-resource-manager/templates/template-functions-resource.md#resourceid).
  > 
  > 
* Resultatet av `dependsOn` elementet är att Azure Resource Manager kan se vilka resurser som kan skapas parallellt och vilka resurser som måste skapas i tur och ordning. 

#### <a name="app-service-app"></a>App Service-app
Nu ska vi gå vidare till själva apparna, vilket är mer komplicerat. Klicka på appen [Variables (' apiSiteName ')] i JSON-dispositionen för att markera dess JSON-kod. Du ser att saker är mycket mer intressanta. För det här ändamålet pratar jag om funktionerna en i taget:

##### <a name="root-resource"></a>Rot resurs
Appen är beroende av två olika resurser. Det innebär att Azure Resource Manager endast skapar appen när både App Service plan och SQL Server-instansen har skapats.

![Visar beroenden för appen på App Service plan och SQL Server-instansen.](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>Appinställningar
Inställningarna för appen definieras också som en kapslad resurs.

![Visar inställningarna för appen som definierats som en kapslad resurs i JSON-koden.](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

I `properties` -elementet för `config/appsettings` har du två app-inställningar i formatet `"<name>" : "<value>"` .

* `PROJECT` är en [kudu-inställning](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) som talar om för Azure-distribution vilket projekt som ska användas i en Visual Studio-lösning med flera projekt. Jag kommer att visa dig senare hur käll kontroll konfigureras, men eftersom ToDoApp-koden finns i en Visual Studio-lösning med flera projekt behöver vi den här inställningen.
* `clientUrl` är bara en app-inställning som program koden använder.

##### <a name="connection-strings"></a>Anslutningssträngar
Anslutnings strängarna definieras också som en kapslad resurs.

![Visar hur anslutnings strängarna definieras som en kapslad resurs i JSON-koden.](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

I `properties` `config/connectionstrings` -elementet definieras även varje anslutnings sträng som ett namn: värde-par med det speciella formatet `"<name>" : {"value": "…", "type": "…"}` . För `type` elementet är möjliga värden,, `MySql` `SQLServer` `SQLAzure` , och `Custom` .

> [!TIP]
> Om du vill ha en slutgiltig lista över typer av anslutnings strängar kör du följande kommando i Azure PowerShell: \[ Enum]:: GetNames ("Microsoft. windowsazure. commands. Utilities. sites. Services. webentities. DatabaseType")
> 
> 

##### <a name="source-control"></a>Källkontroll
Inställningarna för käll kontroll definieras också som en kapslad resurs. Azure Resource Manager använder den här resursen för att konfigurera kontinuerlig publicering (se villkoret `IsManualIntegration` senare) och även för att starta distributionen av program kod automatiskt under bearbetningen av JSON-filen.

![Visar hur käll kontroll inställningarna definieras som en kapslad resurs i JSON-koden.](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl` och `branch` bör vara ganska intuitivt och ska peka på git-lagringsplatsen och namnet på grenen att publicera från. Dessa definieras av indataparametrarna. 

Observera att det `dependsOn` element som, utöver själva app-resursen, `sourcecontrols/web` också är beroende av `config/appsettings` och `config/connectionstrings` . Detta beror på att när `sourcecontrols/web` har kon figurer ATS försöker Azure-distributions processen automatiskt att distribuera, skapa och starta program koden. Det innebär att om du infogar det här beroendet ser du till att programmet har åtkomst till de nödvändiga appinställningar och anslutnings strängarna innan program koden körs. 

> [!NOTE]
> Observera också att `IsManualIntegration` är inställt på `true` . Den här egenskapen är nödvändig i den här självstudien eftersom du inte äger GitHub-lagringsplatsen, och därför inte kan ge Azure behörighet att konfigurera kontinuerlig publicering från [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) (dvs. push-uppdateringar av automatisk lagring i Azure). Du kan bara använda standardvärdet `false` för den angivna lagrings platsen om du har konfigurerat ägarens GitHub-autentiseringsuppgifter i [Azure Portal](https://portal.azure.com/) innan. Med andra ord, om du har konfigurerat käll kontroll till GitHub eller BitBucket för alla appar på Azure- [portalen](https://portal.azure.com/) tidigare, med hjälp av dina användarautentiseringsuppgifter, kommer Azure att komma ihåg autentiseringsuppgifterna och använda dem när du distribuerar en app från GitHub eller BitBucket i framtiden. Men om du inte redan har gjort det, kommer distributionen av JSON-mallen att Miss Miss kan när Azure Resource Manager försöker konfigurera appens inställningar för käll kontroll eftersom den inte kan logga in på GitHub eller BitBucket med databasens ägares autentiseringsuppgifter.
> 
> 

## <a name="compare-the-json-template-with-deployed-resource-group"></a>Jämför JSON-mallen med den distribuerade resurs gruppen
Här kan du gå igenom alla appars blad i [Azure-portalen](https://portal.azure.com/), men det finns ett annat verktyg som är lika användbart, om inte mer. Gå till [Azure Resource Explorer](https://resources.azure.com) för hands versions verktyget, som ger dig en JSON-representation av alla resurs grupper i dina prenumerationer, eftersom de faktiskt finns i Azure-backend-servern. Du kan också se hur resurs gruppens JSON-hierarki i Azure motsvarar hierarkin i mallfilen som används för att skapa den.

När jag till exempel går till [Azure Resource Explorer](https://resources.azure.com) verktyget och expanderar noderna i Explorer, kan jag se resurs gruppen och de resurser på rot nivå som samlas in under respektive resurs typ.

![Visa resurs gruppen och rot nivå resurserna i det utökade verktyget Azure Resource Explorer.](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Om du ökar detalj nivån till en app bör du kunna se konfigurations information för appar som liknar skärm bilden nedan:

![Öka detalj nivån för att Visa konfigurations informationen i appen.](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Återigen bör de kapslade resurserna ha en hierarki som liknar dem i din JSON-mallfil och du bör se appinställningar, anslutnings strängar osv., som visas korrekt i JSON-fönstret. Frånvaron av inställningar här kan tyda på ett problem med JSON-filen och kan hjälpa dig att felsöka din JSON-mallfil.

## <a name="deploy-the-resource-group-template-yourself"></a>Distribuera resurs grupps mal len själv
Knappen **distribuera till Azure** är perfekt, men du kan distribuera resurs grupp mal len i azuredeploy.jsbara om du redan har pushat azuredeploy.jstill GitHub. Azure .NET SDK innehåller även verktyg som du kan använda för att distribuera en JSON-mallfil direkt från den lokala datorn. Det gör du genom att följa stegen nedan:

1. I Visual Studio klickar du på **fil**  >  **nytt**  >  **projekt**.
2. Klicka på **Visual C#**  >  **Cloud**  >  **Azure resurs grupp** och klicka sedan på **OK**.
   
   ![Skapa ett nytt projekt som en Azure-resurs grupp i Azure .NET SDK.](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. I **Välj Azure-mall** väljer du **tom mall** och klickar på **OK**.
4. Dra azuredeploy.jstill mappen **mallar** i det nya projektet.
   
   ![Visar resultatet av att dra azuredeploy.jspå filen till mappen mallar i projektet.](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. Från Solution Explorer öppnar du den kopierade azuredeploy.jspå.
6. För att vi ska kunna använda demonstrationen ska vi lägga till några standard program Insight-resurser i vår JSON-fil genom att klicka på **Lägg till resurs**. Om du bara är intresse rad av att distribuera JSON-filen kan du gå vidare till distributions stegen.
   
   ![Visar knappen Lägg till resurs som du kan använda för att lägga till standard program Insight-resurser i JSON-filen.](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. Välj **Application Insights för Web Apps**, kontrol lera att en befintlig App Service plan och app är markerad och klicka sedan på **Lägg till**.
   
   ![Visar urvalet av Application Insights för Web Apps, namn, App Service plan och webbapp.](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   Nu kan du se flera nya resurser som, beroende på resurs och vad det gör, är beroende av antingen App Service plan eller appen. Dessa resurser har inte Aktiver ATS med den befintliga definitionen och du kommer att ändra den.
   
   ![Visa de nya resurser som har beroenden för App Service plan eller appen.](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. I JSON-dispositionen klickar du på **appInsights AutoScale** för att markera dess JSON-kod. Detta är skalnings inställningen för App Service plan.
9. Leta upp och ange egenskaperna i den markerade JSON-koden `location` `enabled` och ange dem som visas nedan.
   
   ![Visar plats och aktiverade egenskaper i appInsights-autoskalad JSON-kod och de värden som du ska ställa in dem på.](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. I JSON-dispositionen klickar du på **CPUHigh appInsights** för att markera dess JSON-kod. Detta är en avisering.
11. Leta upp `location` och `isEnabled` Ange egenskaperna och ange dem som visas nedan. Gör samma sak för de andra tre aviseringarna (lila lökar).
    
    ![Visar egenskaperna plats och isEnabled i CPUHigh appInsights JSON-koden och de värden som du ska ange dem för.](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. Nu är du redo att distribuera. Högerklicka på projektet och välj **distribuera**  >  **ny distribution**.
    
    ![Visar hur du distribuerar det nya projektet.](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. Logga in på ditt Azure-konto om du inte redan har gjort det.
14. Välj en befintlig resurs grupp i din prenumeration eller skapa en ny, Välj **azuredeploy.jspå** och klicka sedan på **Redigera parametrar**.
    
    ![Visar hur du redigerar parametrarna i azuredeploy.jsi filen.](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    Nu kan du redigera alla parametrar som definierats i mallfilen i en bra tabell. Parametrar som definierar standardvärden har redan sina standardvärden och parametrar som definierar en lista med tillåtna värden visas som List rutor.
    
    ![Visar parametrar som definierar en lista över tillåtna värden som List rutor.](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. Fyll i alla tomma parametrar och Använd [GitHub lagrings platsen-adressen för ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) i **enhälle**. Klicka sedan på **Spara**.
    
    ![Visar de nyligen fyllda parametrarna för azuredeploy.jsi filen.](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > Automatisk skalning är en funktion som erbjuds på **standard** -eller högre nivå, och aviseringar på plannivå är funktioner som erbjuds på nivån **Basic** eller högre. du måste ange **SKU** -parametern till **standard** eller **Premium** för att se att alla nya App Insights-resurser lyser upp.
    > 
    > 
16. Klicka på **Distribuera**. Om du har valt **Spara lösen ord**, kommer lösen ordet att sparas i parameter filen **i oformaterad text**. Annars uppmanas du att ange lösen ordet för databasen under distributions processen.

Klart! Nu behöver du bara gå till Azure- [portalen](https://portal.azure.com/) och [Azure Resource Explorer](https://resources.azure.com) -verktyget för att se de nya aviseringarna och inställningarna för autoskalning som lagts till i ditt JSON-distribuerade program.

Stegen i det här avsnittet gör det huvudsakligen följande:

1. För bereddes mallfilen
2. En parameter fil har skapats för att gå med mallfilen
3. Distribuerade mallfilen med parameter filen

Det sista steget utförs enkelt med en PowerShell-cmdlet. Om du vill se vad Visual Studio gjorde när programmet distribuerades öppnar du Scripts\Deploy-AzureResourceGroup.ps1. Det finns mycket kod där, men jag kommer bara att markera all relevant kod som du behöver för att distribuera mallfilen med parameter filen.

![Visar relevant kod i skriptet som du behöver använda för att distribuera mallfilen med parameter filen.](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

Den sista cmdleten, `New-AzureResourceGroup` är den som faktiskt utför åtgärden. Allt detta bör Visa för dig att, med hjälp av verktyg, det är relativt enkelt att distribuera moln programmet. Varje gång du kör cmdleten på samma mall med samma parameter fil kommer du att få samma resultat.

## <a name="summary"></a>Sammanfattning
I DevOps är repeterbarhet och förutsägbara nycklar till en lyckad distribution av ett storskaligt program som består av mikrotjänster. I den här självstudien har du distribuerat ett program med två mikrotjänster till Azure som en enda resurs grupp med hjälp av Azure Resource Manager-mallen. Förhoppnings vis har du fått den kunskap du behöver för att kunna börja konvertera ditt program i Azure till en mall och kan etablera och distribuera det förutsägbart. 

<a name="resources"></a>

## <a name="more-resources"></a>Fler resurser
* [Azure Resource Manager mallens språk](../azure-resource-manager/templates/template-syntax.md)
* [Redigera Azure Resource Manager mallar](../azure-resource-manager/templates/template-syntax.md)
* [Funktioner för Azure Resource Manager mallar](../azure-resource-manager/templates/template-functions.md)
* [Distribuera ett program med Azure Resource Manager-mall](../azure-resource-manager/templates/deploy-powershell.md)
* [Använda Azure PowerShell med Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md)
* [Felsöka resurs grupps distributioner i Azure](../azure-resource-manager/templates/common-deployment-errors.md)

## <a name="next-steps"></a>Nästa steg

Information om JSON-syntaxen och egenskaperna för resurs typer som distribueras i den här artikeln finns i:

* [Microsoft. SQL/Servers](/azure/templates/microsoft.sql/servers)
* [Microsoft. SQL/Servers/databaser](/azure/templates/microsoft.sql/servers/databases)
* [Microsoft. SQL/Servers/firewallRules](/azure/templates/microsoft.sql/servers/firewallrules)
* [Microsoft. Web/Server grupper](/azure/templates/microsoft.web/serverfarms)
* [Microsoft. Web/Sites](/azure/templates/microsoft.web/sites)
* [Microsoft. Web/Sites/lotss](/azure/templates/microsoft.web/sites/slots)
* [Microsoft. Insights/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings)