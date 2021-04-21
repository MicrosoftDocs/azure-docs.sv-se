---
title: Konfigurera mellanlagringsmiljöer
description: Lär dig hur du distribuerar appar till en plats som inte är en produktionsplats och automatisktwap till produktion. Öka tillförlitligheten och eliminera appavbrott från distributioner.
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.topic: article
ms.date: 04/30/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: b93fb61cc58360ddfcf15d2af2c936203d869500
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771541"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Konfigurera mellanlagringsmiljöer i Azure App Service
<a name="Overview"></a>

När du distribuerar webbappen, webbappen på Linux, den mobila backend-appen eller API-appen till [Azure App Service](./overview.md)kan du använda ett separat distributionsfack i stället för standardproduktionsplatsen när du kör på **nivån Standard,** **Premium** **eller** Isolerad App Service plan. Distributionsfack är live-appar med egna värdnamn. Appinnehåll och konfigurationselement kan växlas mellan två distributionsfack, inklusive produktionsplatsen. 

Att distribuera ditt program till en plats som inte är en produktionsplats har följande fördelar:

* Du kan verifiera appändringar i ett distributionsfack för mellanlagring innan du växlar det med produktionsplatsen.
* Genom att först distribuera en app till ett fack och växla den till produktion ser du till att alla instanser av platsen värms upp innan de växlas till produktion. Detta eliminerar stilleståndstiden när du distribuerar din app. Trafikomdirigeringen är sömlös och inga begäranden tas bort på grund av växlingsåtgärder. Du kan automatisera hela arbetsflödet genom att konfigurera [automatisk växling](#Auto-Swap) när validering i förväg inte behövs.
* Efter en växling har facket med tidigare mellanfasad app nu den tidigare produktionsappen. Om ändringarna som växlades till produktionsplatsen inte är som förväntat kan du utföra samma växling omedelbart för att få tillbaka din "senast kända bra plats".

Varje App Service plan har stöd för olika antal distributionsfack. Det tillkommer ingen extra kostnad för att använda distributionsfack. Information om hur många platser som din appnivå stöder finns i [App Service gränser.](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits) 

Om du vill skala appen till en annan nivå kontrollerar du att målnivån stöder det antal platser som din app redan använder. Om din app till exempel har fler än fem platser kan du inte skala ned den till **standardnivån** eftersom **standardnivån** endast stöder fem distributionsfack. 

<a name="Add"></a>

## <a name="add-a-slot"></a>Lägga till ett fack
Appen måste köras på nivån **Standard,** **Premium** eller **Isolerad** för att du ska kunna aktivera flera distributionsfack.


1. i [Azure Portal](https://portal.azure.com/)söker du efter och väljer **App Services** och väljer din app. 
   
    ![Sök efter App Services](./media/web-sites-staged-publishing/search-for-app-services.png)
   

2. I den vänstra rutan väljer du **Distributionsfack Lägg**  >  **till fack.**
   
    ![Lägg till en ny distributionsplats](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Om appen inte redan finns på **nivån Standard,** **Premium** eller **Isolerad** får du ett meddelande som anger vilka nivåer som stöds för att aktivera mellannivåpublicering. Nu har du möjlighet att välja Uppgradera **och gå** till fliken **Skala i** din app innan du fortsätter.
   > 

3. I dialogrutan **Lägg till ett fack** ger du facket ett namn och väljer om du vill klona en appkonfiguration från ett annat distributionsfack. Välj **Lägg till** för att fortsätta.
   
    ![Konfigurationskälla](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    Du kan klona en konfiguration från en befintlig plats. Inställningar som kan klonas är bland annat appinställningar, anslutningssträngar, språkramverksversioner, webbsocketar, HTTP-version och plattformsbithet.

4. När platsen har lagts till väljer **du Stäng** för att stänga dialogrutan. Det nya facket visas nu på **sidan Distributionsfack.** Som standard är **Traffic %** inställt på 0 för den nya platsen, där all kundtrafik dirigeras till produktionsplatsen.

5. Välj det nya distributionsfacket för att öppna platsens resurssida.
   
    ![Distributionsfackrubrik](./media/web-sites-staged-publishing/StagingTitle.png)

    Mellanlagringsplatsen har en hanteringssida precis som andra App Service app. Du kan ändra platsens konfiguration. Som en påminnelse om att du visar distributionsfacket visas appnamnet som och apptypen är **\<app-name>/\<slot-name>** **App Service (fack).** Du kan också se facket som en separat app i resursgruppen med samma benämningar.

6. Välj appens URL på platsens resurssida. Distributionsfacket har ett eget värdnamn och är också en live-app. Information om hur du begränsar offentlig åtkomst till distributionsfacket finns i [Azure App Service IP-begränsningar.](app-service-ip-restrictions.md)

Det nya distributionsfacket har inget innehåll, även om du klonar inställningarna från en annan plats. Du kan till exempel publicera [till det här facket med Git](./deploy-local-git.md). Du kan distribuera till platsen från en annan lagringsplatsgren eller en annan lagringsplats.

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>Vad händer under en växling

### <a name="swap-operation-steps"></a>Steg för växlingsåtgärd

När du växlar två platser (vanligtvis från en mellanlagringsplats till produktionsplatsen) gör App Service följande för att säkerställa att målplatsen inte drabbas av driftstopp:

1. Tillämpa följande inställningar från målplatsen (till exempel produktionsplatsen) på alla instanser av källfacket: 
    - [Platsspecifika appinställningar](#which-settings-are-swapped) och anslutningssträngar, om tillämpligt.
    - [Inställningar för kontinuerlig](deploy-continuous-deployment.md) distribution, om det är aktiverat.
    - [App Service autentiseringsinställningar,](overview-authentication-authorization.md) om det är aktiverat.
    
    Alla dessa fall utlöser att alla instanser på källplatsen startas om. Under [växling med förhandsversion](#Multi-Phase)markerar detta slutet på den första fasen. Växlingsåtgärden har pausats och du kan verifiera att källfacket fungerar korrekt med målplatsens inställningar.

1. Vänta tills varje instans i källfacket har startats om. Om någon instans inte startar om återställer växlingsåtgärden alla ändringar till källfacket och stoppar åtgärden.

1. Om [lokal cache](overview-local-cache.md) är aktiverad utlöser du initiering av lokal cache genom att göra en HTTP-begäran till programroten ("/") på varje instans av källplatsen. Vänta tills varje instans returnerar ett HTTP-svar. Initiering av lokal cache gör att varje instans startas om igen.

1. Om [automatisk växling](#Auto-Swap) är aktiverat med [](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) anpassad uppvärmning utlöser du programinitiering genom att göra en [HTTP-begäran](#Warm-up)till programroten ("/") på varje instans av källplatsen.

    Om `applicationInitialization` inte anges utlöser du en HTTP-begäran till programroten för källplatsen på varje instans. 
    
    Om en instans returnerar ett HTTP-svar anses den vara varm.

1. Om alla instanser på källplatsen har värmets upp kan du växla de två platserna genom att byta routningsregler för de två platserna. Efter det här steget har målplatsen (till exempel produktionsplatsen) appen som tidigare har värmets upp i källfacket.

1. Nu när källfacket har förväxlingsappen tidigare i målfacket, utför du samma åtgärd genom att tillämpa alla inställningar och starta om instanserna.

Vid vilken tidpunkt som helst av växlingsåtgärden utförs allt arbete med att initiera de växlade apparna på källfacket. Målplatsen förblir online medan källplatsen förbereds och värms upp, oavsett var växlingen lyckas eller misslyckas. Om du vill växla en mellanlagringsplats med produktionsplatsen kontrollerar du att produktionsplatsen alltid är målplatsen. På så sätt påverkar inte växlingsåtgärden din produktionsapp.

### <a name="which-settings-are-swapped"></a>Vilka inställningar växlas?

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

Om du vill konfigurera en appinställning eller anslutningssträng så att den håller sig till ett visst fack (inte växlas) går du till **sidan** Konfiguration för det facket. Lägg till eller redigera en inställning och välj sedan **distributionsfackinställning**. Om du markerar den här App Service anger att inställningen inte kan växlas. 

![Fackinställning](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Växla två fack 
Du kan växla distributionsfack på sidan **Distributionsfack för** din app och **sidan** Översikt. Teknisk information om fackväxlingen finns i [Vad händer under bytet.](#AboutConfiguration)

> [!IMPORTANT]
> Innan du växlar en app från ett distributionsfack till produktion kontrollerar du att produktion är målplatsen och att alla inställningar på källplatsen är konfigurerade exakt som du vill ha dem i produktion.
> 
> 

Så här växlar du distributionsfack:

1. Gå till sidan Distributionsfack **för din** app och välj **Växla**.
   
    ![Växlingsknapp](./media/web-sites-staged-publishing/SwapButtonBar.png)

    Dialogrutan **Växla** visar inställningar i de valda käll- och målfacken som kommer att ändras.

2. Välj önskade **käll-** och **målplatser.** Målet är vanligtvis produktionsplatsen. Välj också flikarna **Källändringar** **och Måländringar** och kontrollera att konfigurationsändringarna är förväntade. När du är klar kan du växla facken direkt genom att välja **Växla**.

    ![Slutföra växlingen](./media/web-sites-staged-publishing/SwapImmediately.png)

    Om du vill se hur målfacket skulle köras med de nya inställningarna innan växlingen faktiskt sker väljer du inte Växla **,** utan följer instruktionerna i Växla [med förhandsversion.](#Multi-Phase)

3. När du är klar stänger du dialogrutan genom att välja **Stäng**.

Om du har problem kan du gå till [Felsöka växlingar.](#troubleshoot-swaps)

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Växla med förhandsversion (växling i flera faser)

Innan du växlar till produktion som målplats kontrollerar du att appen körs med de växlade inställningarna. Källfacket värms också upp innan bytet slutförs, vilket är önskvärt för verksamhetskritiska program.

När du utför en växling med förhandsversionen App Service samma [växlingsåtgärd men](#AboutConfiguration) pausar efter det första steget. Du kan sedan kontrollera resultatet på mellanlagringsplatsen innan du slutför växlingen. 

Om du avbryter växlingen App Service konfigurationselementen på nytt till källfacket.

Så här växlar du med förhandsversion:

1. Följ stegen i Växla [distributionsfack men](#Swap) välj **Utför växling med förhandsversion.**

    ![Växla med förhandsversion](./media/web-sites-staged-publishing/SwapWithPreview.png)

    Dialogrutan visar hur konfigurationen i källfacket ändras i fas 1 och hur käll- och målplatsen ändras i fas 2.

2. När du är redo att starta växlingen väljer du **Starta växling.**

    När fas 1 är klar meddelas du i dialogrutan. Förhandsgranska växlingen i källfacket genom att gå till `https://<app_name>-<source-slot-name>.azurewebsites.net` . 

3. När du är redo att slutföra den väntande växlingen väljer du **Slutför växling** i **växlingsåtgärden** och väljer **Slutför växling.**

    Om du vill avbryta en väntande växling väljer du **Avbryt växling i** stället.

4. När du är klar stänger du dialogrutan genom att välja **Stäng**.

Om du har problem kan du gå till [Felsöka växlingar.](#troubleshoot-swaps)

Information om hur du automatiserar ett byte i flera faser [finns i Automatisera med PowerShell.](#automate-with-powershell)

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Återställa en växling
Om det uppstår fel i målfacket (till exempel produktionsplatsen) efter ett fackbyte återställer du platserna till deras förväxlings tillstånd genom att växla samma två platser direkt.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Konfigurera automatisk växling

> [!NOTE]
> Automatisk växling stöds inte i webbappar i Linux.

Automatisk växling effektiviserar Azure DevOps-scenarier där du vill distribuera din app kontinuerligt med noll kallstarter och noll driftstopp för appens kunder. När automatisk växling har [aktiverats](#swap-operation-steps) från en plats till produktion växlar App Service automatiskt appen till produktion varje gång du pushar kodändringarna till det facket när den har värmets upp i källfacket.

   > [!NOTE]
   > Innan du konfigurerar automatisk växling för produktionsplatsen bör du överväga att testa automatisk växling på en plats som inte är produktionsmålplats.
   > 

Så här konfigurerar du automatisk växling:

1. Gå till appens resurssida. Välj **Distributionsfack**  >  *\<desired source slot>*  >  **Allmänna inställningar**  >  **för Konfiguration.**
   
2. För **Autoväxling aktiverat** väljer du **På.** Välj sedan önskat målfack för **Automatisk växling av distributionsfack** och välj **Spara** i kommandofältet. 
   
    ![Val för att konfigurera automatisk växling](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Kör en kod-push till källfacket. Automatisk växling sker efter en kort tid och uppdateringen visas på målplatsens URL.

Om du har problem kan du gå till [Felsöka växlingar.](#troubleshoot-swaps)

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Ange anpassad uppvärmning

Vissa appar kan kräva anpassade uppvärmningsåtgärder före bytet. Med `applicationInitialization` konfigurationselementet i web.config kan du ange anpassade initieringsåtgärder. [Växlingsåtgärden](#AboutConfiguration) väntar på att den här anpassade uppvärmningen ska slutföras innan du växlar med målplatsen. Här är ett exempel på web.config fragment.

```xml
<system.webServer>
    <applicationInitialization>
        <add initializationPage="/" hostName="[app hostname]" />
        <add initializationPage="/Home/About" hostName="[app hostname]" />
    </applicationInitialization>
</system.webServer>
```

Mer information om hur du anpassar `applicationInitialization` elementet finns i De vanligaste [distributionsfackväxlingsfelen och hur du åtgärdar dem.](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/)

Du kan också anpassa uppvärmningsbeteendet med en eller båda av följande [appinställningar:](configure-common.md)

- `WEBSITE_SWAP_WARMUP_PING_PATH`: Sökvägen till ping för att värma upp webbplatsen. Lägg till den här appinställningen genom att ange en anpassad sökväg som börjar med ett snedstreck som värde. Ett exempel är `/statuscheck`. Standardvärdet är `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Giltiga HTTP-svarskoder för uppvärmningsåtgärden. Lägg till den här appinställningen med en kommaavgränsad lista med HTTP-koder. Ett exempel är `200,202` . Om den returnerade statuskoden inte finns i listan stoppas uppvärmnings- och växlingsåtgärder. Som standard är alla svarskoder giltiga.
- `WEBSITE_WARMUP_PATH`: En relativ sökväg på platsen som ska pingas när platsen startas om (inte bara under fackväxlingar). Exempelvärden är `/statuscheck` eller rotsökvägen, `/` .

> [!NOTE]
> Konfigurationselementet är en del av varje appstart, medan appinställningarna för två uppvärmningsbeteenden `<applicationInitialization>` endast gäller för fackväxlingar.

Om du har problem kan du gå till [Felsöka växlingar.](#troubleshoot-swaps)

## <a name="monitor-a-swap"></a>Övervaka en växling

Om [växlingsåtgärden](#AboutConfiguration) tar lång tid att slutföra kan du få information om växlingsåtgärden i [aktivitetsloggen](../azure-monitor/essentials/platform-logs-overview.md).

Välj Aktivitetslogg i den vänstra rutan på appens resurssida **i portalen.**

En växlingsåtgärd visas i loggfrågan som `Swap Web App Slots` . Du kan expandera den och välja något av underåtgärderna eller felen för att se informationen.

## <a name="route-traffic"></a>Dirigera trafik

Som standard dirigeras alla klientbegäranden till appens `http://<app_name>.azurewebsites.net` produktions-URL ( ) till produktionsplatsen. Du kan dirigera en del av trafiken till en annan plats. Den här funktionen är användbar om du behöver användarfeedback för en ny uppdatering, men du inte är redo att släppa den i produktion.

### <a name="route-production-traffic-automatically"></a>Dirigera produktionstrafik automatiskt

Så här dirigerar du produktionstrafik automatiskt:

1. Gå till appens resurssida och välj **Distributionsfack.**

2. I kolumnen **Trafik %** för den plats som du vill dirigera till anger du en procentandel (mellan 0 och 100) som representerar den totala trafik som du vill dirigera. Välj **Spara**.

    ![Ange en trafikprocent](./media/web-sites-staged-publishing/RouteTraffic.png)

När inställningen har sparats dirigeras den angivna procentandelen klienter slumpmässigt till icke-produktionsplatsen. 

När en klient automatiskt dirigeras till en specifik plats "fästs" den på den platsen under klientsessionens livslängd. I klientwebbläsaren kan du se vilket fack sessionen är fäst på genom att titta på `x-ms-routing-name` cookien i HTTP-huvudena. En begäran som dirigeras till mellanlagringsplatsen har cookien `x-ms-routing-name=staging` . En begäran som dirigeras till produktionsplatsen har cookien `x-ms-routing-name=self` .

   > [!NOTE]
   > Bredvid Azure Portal kan du också använda kommandot i Azure CLI för att ange routningsprocenten från [`az webapp traffic-routing set`](/cli/azure/webapp/traffic-routing#az_webapp_traffic_routing_set) CI/CD-verktyg som DevOps-pipelines eller andra automatiseringssystem.
   > 

### <a name="route-production-traffic-manually"></a>Dirigera produktionstrafik manuellt

Förutom automatisk trafikroutning kan App Service dirigera begäranden till en specifik plats. Detta är användbart när du vill att användarna ska kunna välja att använda eller välja bort din betaapp. Om du vill dirigera produktionstrafik manuellt använder du `x-ms-routing-name` frågeparametern .

Om du till exempel vill att användarna ska kunna välja bort din betaapp kan du lägga till den här länken på din webbsida:

```html
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

Strängen `x-ms-routing-name=self` anger produktionsplatsen. När klientwebbläsaren har åtkomst till länken omdirigeras den till produktionsplatsen. Varje efterföljande begäran har `x-ms-routing-name=self` cookien som fäster sessionen på produktionsplatsen.

Om du vill låta användarna välja att använda din betaapp anger du samma frågeparameter till namnet på icke-produktionsplatsen. Här är ett exempel:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Som standard får nya platser routningsregeln `0%` , som visas i grått. När du uttryckligen anger det här värdet till (visas i svart text) kan användarna komma åt `0%` mellanlagringsplatsen manuellt med hjälp av `x-ms-routing-name` frågeparametern. Men de dirigeras inte till platsen automatiskt eftersom dirigeringsprocenten är inställd på 0. Det här är ett avancerat scenario där du kan "dölja" mellanlagringsplatsen från allmänheten samtidigt som interna team kan testa ändringar på platsen.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>Ta bort ett fack

Sök efter och välj din app. Välj **Distributionsfack**  >  *\<slot to delete>*  >  **Översikt.** Apptypen visas som en **App Service (fack)** för att påminna dig om att du visar ett distributionsfack. Välj **Ta** bort i kommandofältet.  

![Ta bort ett distributionsfack](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatisera med PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell är en modul som innehåller cmdlets för att hantera Azure via Windows PowerShell, inklusive stöd för att hantera distributionsfack i Azure App Service.

Information om hur du installerar och konfigurerar Azure PowerShell och om hur du autentiserar Azure PowerShell med din Azure-prenumeration finns i Så här installerar och [konfigurerar](/powershell/azure/)du Microsoft Azure PowerShell .  

---
### <a name="create-a-web-app"></a>Skapa en webbapp
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-a-slot"></a>Skapa ett fack
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>Initiera en växling med en förhandsgranskning (växling i flera faser) och tillämpa målplatskonfigurationen på källfacket
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>Avbryt en väntande växling (byt med granskning) och återställ konfigurationen för källfacket
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Växla distributionsfack
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Övervaka växlingshändelser i aktivitetsloggen
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>Ta bort ett fack
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

## <a name="automate-with-resource-manager-templates"></a>Automatisera med Resource Manager mallar

[Azure Resource Manager är deklarativa](../azure-resource-manager/templates/overview.md) JSON-filer som används för att automatisera distributionen och konfigurationen av Azure-resurser. Om du vill växla fack med Resource Manager mallar anger du två egenskaper för *resurserna Microsoft.Web/sites/slots* och *Microsoft.Web/sites:*

- `buildVersion`: det här är en strängegenskap som representerar den aktuella versionen av appen som distribuerats i facket. Exempel: "v1", "1.0.0.1" eller "2019-09-20T11:53:25.2887393-07:00".
- `targetBuildVersion`: det här är en strängegenskap som anger `buildVersion` vad facket ska ha. Om targetBuildVersion inte är lika med den aktuella utlöser detta växlingsåtgärden genom att hitta `buildVersion` platsen som har angiven `buildVersion` .

### <a name="example-resource-manager-template"></a>Exempel Resource Manager mall

Följande mall Resource Manager för `buildVersion` mellanlagringsplatsen och anger `targetBuildVersion` på produktionsplatsen. Detta kommer att växla mellan de två facken. Mallen förutsätter att du redan har en webbapp som skapats med ett fack med namnet "staging".

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "my_site_name": {
            "defaultValue": "SwapAPIDemo",
            "type": "String"
        },
        "sites_buildVersion": {
            "defaultValue": "v1",
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Web/sites/slots",
            "apiVersion": "2018-02-01",
            "name": "[concat(parameters('my_site_name'), '/staging')]",
            "location": "East US",
            "kind": "app",
            "properties": {
                "buildVersion": "[parameters('sites_buildVersion')]"
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "apiVersion": "2018-02-01",
            "name": "[parameters('my_site_name')]",
            "location": "East US",
            "kind": "app",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites/slots', parameters('my_site_name'), 'staging')]"
            ],
            "properties": {
                "targetBuildVersion": "[parameters('sites_buildVersion')]"
            }
        }        
    ]
}
```

Den Resource Manager mallen är idempotent, vilket innebär att den kan köras upprepade gånger och generera samma tillstånd för platserna. Efter den första `targetBuildVersion` körningen matchar den aktuella `buildVersion` , så en växling utlöses inte.

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>Automatisera med CLI

Azure [CLI-kommandon](https://github.com/Azure/azure-cli) för distributionsfack finns i [az webapp deployment slot](/cli/azure/webapp/deployment/slot).

## <a name="troubleshoot-swaps"></a>Felsöka växlingar

Om något fel inträffar under [ett fackväxling](#AboutConfiguration)loggas det *D:\home\LogFiles\eventlog.xml*. Den loggas också i den programspecifika felloggen.

Här är några vanliga växlingsfel:

- En HTTP-begäran till programroten har fördr i tid. Växlingsåtgärden väntar i 90 sekunder för varje HTTP-begäran och försöker igen upp till 5 gånger. Om alla återförsök har time out stoppats växlingsåtgärden.

- Initiering av lokal cache kan misslyckas när appinnehållet överskrider den lokala diskkvoten som angetts för den lokala cachen. Mer information finns i Översikt [över lokal cache.](overview-local-cache.md)

- Under [anpassad uppvärmning görs HTTP-begäranden](#Warm-up)internt (utan att gå igenom den externa URL:en). De kan misslyckas med vissa URL-omskrivningsregler i *Web.config*. Regler för att omdirigera domännamn eller tillämpa HTTPS kan till exempel förhindra att uppvärmningsbegäranden når appkoden. Du kan komma runt det här problemet genom att ändra reglerna för omskrivning genom att lägga till följande två villkor:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Utan en anpassad uppvärmning kan URL-omskrivningsregler fortfarande blockera HTTP-begäranden. Du kan komma runt det här problemet genom att ändra reglerna för omskrivning genom att lägga till följande villkor:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```

- Efter fackväxlingar kan det uppstå oväntade omstarter i appen. Det beror på att efter en växling blir konfigurationen av värdnamnets bindning inte synkroniserad, vilket i sig inte orsakar omstarter. Vissa underliggande lagringshändelser (till exempel redundans för lagringsvolym) kan dock identifiera dessa avvikelser och tvinga alla arbetsprocesser att starta om. Om du vill minimera dessa typer av omstarter anger du [ `WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG=1` appinställningen](https://github.com/projectkudu/kudu/wiki/Configurable-settings#disable-the-generation-of-bindings-in-applicationhostconfig) på *alla platser*. Den här appinställningen fungerar *dock inte* med Windows Communication Foundation (WCF).

## <a name="next-steps"></a>Nästa steg
[Blockera åtkomst till platser som inte är produktionsplatser](app-service-ip-restrictions.md)
