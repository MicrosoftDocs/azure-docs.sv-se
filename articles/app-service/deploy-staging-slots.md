---
title: Konfigurera mellanlagrings miljöer
description: Lär dig hur du distribuerar appar till en icke-produktions plats och en Autobyte till produktion. Öka tillförlitligheten och ta bort avbrott i appen från distributioner.
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.topic: article
ms.date: 04/30/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 1c4cff264b63506432daf350be3557bae7234584
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "100594246"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Konfigurera mellanlagringsmiljöer i Azure App Service
<a name="Overview"></a>

När du distribuerar din webbapp, webbapp på Linux, mobil Server del eller API-appen till [Azure App Service](./overview.md)kan du använda en separat distributions plats i stället för standard produktions platsen när du kör på **standard**-, **Premium**-eller **isolerade** App Service plans nivå. Distributions platser är Live-appar med sina egna värdnamn. App-innehåll och konfigurations element kan växlas mellan två distributions platser, inklusive produktions platsen. 

Distribution av ditt program till en icke-produktions plats har följande fördelar:

* Du kan validera ändringar av appar på en mellanlagrings plats innan du växlar med produktions platsen.
* Om du distribuerar en app till en plats och byter ut den till produktion ser du till att alla instanser av facket har värmts upp innan du växlar till produktion. Detta eliminerar nedtid när du distribuerar din app. Omdirigeringen av trafiken är sömlös och inga förfrågningar tas bort på grund av växlings åtgärder. Du kan automatisera hela arbets flödet genom att konfigurera [Automatisk växling](#Auto-Swap) när det inte behövs för verifiering före växling.
* Efter en växling har facket med den tidigare mellanlagrade appen nu den tidigare produktions appen. Om ändringarna som utbyts till produktions platsen inte är som du förväntar dig kan du utföra samma växling direkt för att få tillbaka din "senast fungerande webbplats".

Varje App Service plan nivå har stöd för ett annat antal distributions platser. Det kostar inget extra att använda distributions platser. För att ta reda på antalet platser som din apps-nivå stöder, se [App Service gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits). 

Om du vill skala din app till en annan nivå ser du till att mål nivån stöder antalet platser som appen redan använder. Om din app till exempel har fler än fem platser kan du inte skala ned den till **standard** nivån eftersom **standard** nivån endast stöder fem distributions fack. 

<a name="Add"></a>

## <a name="add-a-slot"></a>Lägga till ett fack
Appen måste köras på nivån **standard**, **Premium** eller **isolerad** för att du ska kunna aktivera flera distributions platser.


1. i [Azure Portal](https://portal.azure.com/)söker du efter och väljer **app Services** och väljer din app. 
   
    ![Sök efter App Services](./media/web-sites-staged-publishing/search-for-app-services.png)
   

2. I det vänstra fönstret väljer du **distributions platser**  >  **Lägg till plats**.
   
    ![Lägg till en ny distributionsplats](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Om appen inte redan finns på **standard**-, **Premium**-eller den **isolerade** nivån får du ett meddelande som anger vilka nivåer som stöds för aktivering av mellanlagrad publicering. Nu har du möjlighet att välja **uppgradering** och gå till fliken **Scale (skala** ) i appen innan du fortsätter.
   > 

3. I dialog rutan **Lägg till en plats** anger du platsens namn och anger om du vill klona en app-konfiguration från en annan distributions plats. Välj **Lägg till** för att fortsätta.
   
    ![Konfigurations källa](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    Du kan klona en konfiguration från en befintlig plats. Inställningar som kan klonas är inställningar för appar, anslutnings strängar, språk Ramverks versioner, Web Sockets, HTTP-version och plattforms bitness.

4. När platsen har lagts till väljer du **Stäng** för att stänga dialog rutan. Den nya platsen visas nu på sidan **distributions platser** . Som standard är **trafik%** inställd på 0 för den nya platsen, där all kund trafik dirigeras till produktions platsen.

5. Välj den nya distributions platsen för att öppna platsens resurs sida.
   
    ![Rubrik för distributions plats](./media/web-sites-staged-publishing/StagingTitle.png)

    Mellanlagringsplatsen har en hanterings sida precis som andra App Service-appar. Du kan ändra platsens konfiguration. För att påminna dig om att du visar distributions platsen visas appens namn som **\<app-name>/\<slot-name>** och appens typ är **App Service (plats)**. Du kan också se facket som en separat app i resurs gruppen med samma utformningar.

6. Välj appens URL på platsens resurs sida. Distributions platsen har sitt eget värdnamn och är även en Live-app. Information om hur du begränsar offentlig åtkomst till distributions platsen finns i [Azure App Service IP-begränsningar](app-service-ip-restrictions.md).

Den nya distributions platsen har inget innehåll, även om du klonar inställningarna från en annan plats. Du kan till exempel [Publicera till den här platsen med git](./deploy-local-git.md). Du kan distribuera till platsen från en annan databas gren eller en annan lagrings plats.

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>Vad händer under en växling

### <a name="swap-operation-steps"></a>Steg för växlings åtgärd

När du växlar två platser (vanligt vis från en mellanlagringsplats till produktions platsen) gör App Service följande för att se till att mål platsen inte upplever drift stopp:

1. Använd följande inställningar från mål platsen (till exempel produktions platsen) till alla instanser av käll platsen: 
    - [Platsspecifika appinställningar och](#which-settings-are-swapped) anslutnings strängar, om tillämpligt.
    - Inställningar för [kontinuerlig distribution](deploy-continuous-deployment.md) , om aktive rad.
    - [App Service autentiseringsinställningarna](overview-authentication-authorization.md) om det är aktiverat.
    
    Alla dessa fall utlöser alla instanser på käll platsen för omstart. Vid [växling med förhands granskning](#Multi-Phase)markerar detta slutet av den första fasen. Växlings åtgärden har pausats och du kan kontrol lera att käll platsen fungerar korrekt med mål platsens inställningar.

1. Vänta tills varje instans på käll platsen har startats om. Om någon instans inte kan starta om återställer växlings åtgärden alla ändringar till käll platsen och stoppar åtgärden.

1. Om [lokal cache](overview-local-cache.md) har Aktiver ATS utlöser du initiering av lokalt cacheminne genom att göra en http-begäran till program roten ("/") på varje instans av käll platsen. Vänta tills varje instans returnerar HTTP-svar. Initiering av lokal cache orsakar en omstart av varje instans.

1. Om [Automatisk växling](#Auto-Swap) har Aktiver ATS med [anpassad uppvärmning](#Warm-up), Utlös [program initiering](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) genom att göra en http-begäran till program roten ("/") på varje instans av käll platsen.

    Om `applicationInitialization` inte anges utlöses en HTTP-begäran till käll platsens program rot på varje instans. 
    
    Om en instans returnerar ett HTTP-svar anses det vara värmat.

1. Om alla instanser på käll platsen har värmts upp korrekt byter du ut de två platserna genom att växla routningsregler för de två platserna. Efter det här steget har mål platsen (till exempel produktions platsen) den app som tidigare har förvärmts på käll platsen.

1. Nu när käll platsen har för hands växlings appen tidigare på mål platsen, utför du samma åtgärd genom att tillämpa alla inställningar och starta om instanserna.

När som helst i växlings åtgärden sker allt arbete vid initiering av de växlade apparna på käll platsen. Mål platsen är online medan käll platsen förbereds och förvärmas, oavsett var växlingen lyckas eller Miss lyckas. Om du vill byta mellan en mellanlagringsplats med produktions platsen kontrollerar du att produktions platsen alltid är mål platsen. På så sätt kommer växlings åtgärden inte att påverka din produktions program.

### <a name="which-settings-are-swapped"></a>Vilka inställningar byts ut?

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

Om du vill konfigurera en app-inställning eller anslutnings sträng för att fästa mot en angiven plats (som inte växlas) går du till sidan **konfiguration** för den platsen. Lägg till eller redigera en inställning och välj sedan **distributions plats inställning**. Om du markerar den här kryss rutan visas App Service att inställningen inte kan växlas. 

![Plats inställning](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Växla två platser 
Du kan växla distributions platser på sidan **distributions platser** för appar och **översikts** sidan. Teknisk information om plats växlingen finns i [vad som händer under växlingen](#AboutConfiguration).

> [!IMPORTANT]
> Innan du byter ut en app från en distributions plats i produktion kontrollerar du att produktionen är mål platsen och att alla inställningar i käll platsen är konfigurerade exakt som du vill ha dem i produktion.
> 
> 

Växla mellan distributions platser:

1. Gå till sidan **distributions platser** för appen och välj **Byt**.
   
    ![Växlings knapp](./media/web-sites-staged-publishing/SwapButtonBar.png)

    Dialog rutan **Växla** visar inställningar i de valda käll-och mål platserna som ska ändras.

2. Välj önskade **käll** -och **mål** platser. Normalt är målet produktions platsen. Välj också flikarna **käll ändringar** och **mål ändringar** och kontrol lera att konfigurations ändringarna förväntas. När du är klar kan du växla platserna direkt genom att välja **Växla**.

    ![Slutföra växlingen](./media/web-sites-staged-publishing/SwapImmediately.png)

    Om du vill se hur mål platsen kommer att köras med de nya inställningarna innan växlingen faktiskt sker väljer du inte **växling**, utan följer anvisningarna i [Växla med förhands granskning](#Multi-Phase).

3. När du är klar stänger du dialog rutan genom att välja **Stäng**.

Om du har problem kan du läsa [Felsöka växlingar](#troubleshoot-swaps).

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Växla med förhands granskning (växling i flera faser)

Innan du byter till produktion som mål plats kontrollerar du att appen körs med de växlade inställningarna. Käll platsen har också förvärmts innan växlings förslutning, vilket är önskvärt för verksamhets kritiska program.

När du utför en växling med för hands versionen utför App Service samma [växlings åtgärd](#AboutConfiguration) men pausas efter det första steget. Du kan sedan verifiera resultatet på mellanlagringsplatsen innan du slutför växlingen. 

Om du avbryter växlingen App Service återanvänder konfigurations elementen på käll platsen.

För att växla med för hands version:

1. Följ stegen i [Växla distributions fack](#Swap) men Välj **utför växling med förhands granskning**.

    ![Växla med för hands version](./media/web-sites-staged-publishing/SwapWithPreview.png)

    I dialog rutan visas hur konfigurationen i käll platsen ändras i fas 1, och hur käll-och mål platsen ändras i fas 2.

2. När du är redo att starta växlingen väljer du **Starta växling**.

    När fas 1 är klar får du ett meddelande i dialog rutan. Förhandsgranska växlingen på käll platsen genom att gå till `https://<app_name>-<source-slot-name>.azurewebsites.net` . 

3. När du är redo att slutföra den väntande växlingen väljer du **fullständig växling** i **växlings åtgärd** och väljer **Slutför växling**.

    Om du vill avbryta en väntande växling väljer du **Avbryt växling** i stället.

4. När du är klar stänger du dialog rutan genom att välja **Stäng**.

Om du har problem kan du läsa [Felsöka växlingar](#troubleshoot-swaps).

Om du vill automatisera en växling i flera faser, se [Automatisera med PowerShell](#automate-with-powershell).

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Återställa en växling
Om några fel inträffar på mål platsen (t. ex. produktions platsen) efter en plats växling, återställer du platserna till deras före växlings tillstånd genom att växla samma två platser direkt.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Konfigurera automatisk växling

> [!NOTE]
> Automatisk växling stöds inte i Web Apps på Linux.

Automatisk växling effektiviserar Azure DevOps-scenarier där du vill distribuera din app kontinuerligt med noll som kall startar och noll nedtid för kunder i appen. När automatisk växling har Aktiver ATS från en plats till produktion, byter App Service automatiskt [appen till produktion](#swap-operation-steps) när den har värmts upp på käll platsen när du skickar kod ändringarna till den platsen.

   > [!NOTE]
   > Innan du konfigurerar automatisk växling för produktions platsen bör du överväga att testa automatisk växling på ett mål fack som inte är för produktion.
   > 

Så här konfigurerar du automatisk växling:

1. Gå till appens resurs sida. Välj **distributions platser**  >  *\<desired source slot>*  >  **konfiguration**  >  **allmänna inställningar**.
   
2. För **Automatisk växling aktive rad** väljer du **på**. Välj sedan önskad mål plats för **Automatisk växling distributions plats** och välj **Spara** i kommando fältet. 
   
    ![Alternativ för att konfigurera automatisk växling](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Kör en kod push till käll platsen. Automatisk växling sker efter en kort tid och uppdateringen återspeglas på mål platsens URL.

Om du har problem kan du läsa [Felsöka växlingar](#troubleshoot-swaps).

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Ange anpassad uppvärmning

Vissa appar kan kräva anpassade värme åtgärder innan växlingen. Med `applicationInitialization` konfigurations elementet i web.config kan du ange anpassade initierings åtgärder. [Växlings åtgärden](#AboutConfiguration) väntar på att den här anpassade uppvärmningen ska slutföras innan den växlar till mål platsen. Här är ett exempel på web.config fragment.

```xml
<system.webServer>
    <applicationInitialization>
        <add initializationPage="/" hostName="[app hostname]" />
        <add initializationPage="/Home/About" hostName="[app hostname]" />
    </applicationInitialization>
</system.webServer>
```

Mer information om hur du anpassar `applicationInitialization` -elementet finns i [de flesta vanliga växlings fel i distributions fack och hur du åtgärdar dem](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/).

Du kan också anpassa det varmaste sättet med en eller båda av följande [appinställningar](configure-common.md):

- `WEBSITE_SWAP_WARMUP_PING_PATH`: Sökvägen till ping för att värma upp platsen. Lägg till den här appens inställningen genom att ange en anpassad sökväg som börjar med ett snedstreck som värde. Ett exempel är `/statuscheck`. Standardvärdet är `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Giltiga HTTP-svars koder för den varmande åtgärden. Lägg till den här appens inställningen med en kommaavgränsad lista över HTTP-koder. Ett exempel är `200,202` . Om den returnerade status koden inte finns i listan stoppas uppvärmnings och växlings åtgärder. Som standard är alla svars koder giltiga.

> [!NOTE]
> `<applicationInitialization>`Konfigurations elementet är en del av varje app-Start, medan de två app-inställningarna för att värma upp gäller endast för fack växlingar.

Om du har problem kan du läsa [Felsöka växlingar](#troubleshoot-swaps).

## <a name="monitor-a-swap"></a>Övervaka en växling

Om [växlings åtgärden](#AboutConfiguration) tar lång tid att slutföra kan du få information om växlings åtgärden i [aktivitets loggen](../azure-monitor/essentials/platform-logs-overview.md).

På appens resurs sida i portalen väljer du **aktivitets logg** i det vänstra fönstret.

En växlings åtgärd visas i logg frågan som `Swap Web App Slots` . Du kan expandera den och välja en av under åtgärder eller fel för att se information.

## <a name="route-traffic"></a>Dirigera trafik

Som standard dirigeras alla klient begär anden till appens produktions-URL ( `http://<app_name>.azurewebsites.net` ) till produktions platsen. Du kan dirigera en del av trafiken till en annan plats. Den här funktionen är användbar om du behöver feedback från användaren för en ny uppdatering, men du inte är redo att släppa den på produktionen.

### <a name="route-production-traffic-automatically"></a>Dirigera produktions trafik automatiskt

Så här dirigerar du produktions trafiken automatiskt:

1. Gå till appens resurs sida och välj **distributions platser**.

2. I kolumnen **Traffic%** på den plats som du vill dirigera till anger du ett procenttal (mellan 0 och 100) för att representera den totala trafik som du vill dirigera. Välj **Spara**.

    ![Ange en trafik procent](./media/web-sites-staged-publishing/RouteTraffic.png)

När inställningen har sparats dirigeras den angivna procent andelen av klienter slumpmässigt till den icke-produktions platsen. 

När en klient dirigeras automatiskt till en speciell plats, är den "fäst" på den platsen under den aktuella klient sessionen. I klientens webbläsare kan du se vilka platser som din session fästs på genom att titta på `x-ms-routing-name` cookien i dina HTTP-huvuden. En begäran som dirigeras till "mellanlagrings platsen" har cookien `x-ms-routing-name=staging` . En begäran som dirigeras till produktions platsen har cookien `x-ms-routing-name=self` .

   > [!NOTE]
   > Bredvid Azure Portal kan du också använda [`az webapp traffic-routing set`](/cli/azure/webapp/traffic-routing#az-webapp-traffic-routing-set) kommandot i Azure CLI för att ställa in procent andelar från CI/CD-verktyg som DevOps pipelines eller andra Automation-system.
   > 

### <a name="route-production-traffic-manually"></a>Dirigera produktions trafik manuellt

Förutom automatisk trafik dirigering kan App Service dirigera begär anden till en angiven plats. Detta är användbart när du vill att användarna ska kunna välja eller avanmäla din Beta-app. Om du vill dirigera produktions trafiken manuellt använder du `x-ms-routing-name` Frågeparametern.

Du kan till exempel använda den här länken på din webb sida om du vill låta användarna välja att använda beta appen:

```html
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

Strängen `x-ms-routing-name=self` anger produktions platsen. När klient läsaren har åtkomst till länken omdirigeras den till produktions platsen. Varje efterföljande begäran har den `x-ms-routing-name=self` cookie som fäster sessionen på produktions platsen.

Om du vill låta användarna välja beta-appen ställer du in samma frågeparameter till namnet på den plats som inte är för produktion. Här är ett exempel:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Som standard tilldelas nya platser en regel för routning, som `0%` visas i grått. När du uttryckligen anger det här värdet `0%` (visas i svart text) kan användarna komma åt mellanlagringsplatsen manuellt med hjälp av `x-ms-routing-name` Frågeparametern. Men de dirigeras inte till facket automatiskt eftersom routningsdomänens procents ATS är inställd på 0. Det här är ett avancerat scenario där du kan "dölja" mellanlagringsplatsen från allmänheten samtidigt som du tillåter interna team att testa ändringar på platsen.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>Ta bort en plats

Sök efter och välj din app. Välj **distributions platser**  >  *\<slot to delete>*  >  **– Översikt**. Appens typ visas som **App Service (plats)** för att påminna dig om att du visar en distributions plats. Välj **ta bort** i kommando fältet.  

![Ta bort ett distributions fack](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatisera med PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell är en modul som tillhandahåller cmdlets för att hantera Azure via Windows PowerShell, inklusive stöd för att hantera distributions platser i Azure App Service.

Information om hur du installerar och konfigurerar Azure PowerShell och hur du autentiserar Azure PowerShell med din Azure-prenumeration finns i [så här installerar och konfigurerar du Microsoft Azure PowerShell](/powershell/azure/).  

---
### <a name="create-a-web-app"></a>Skapa en webbapp
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-a-slot"></a>Skapa en plats
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>Påbörja en växling med en förhands granskning (växling vid flera faser) och Använd destinations plats konfigurationen för käll platsen
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>Avbryt en väntande växling (Byt till granskning) och Återställ käll plats konfigurationen
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Växla distributionsfack
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Övervaka växlings händelser i aktivitets loggen
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>Ta bort en plats
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

## <a name="automate-with-resource-manager-templates"></a>Automatisera med Resource Manager-mallar

[Azure Resource Manager mallar](../azure-resource-manager/templates/overview.md) är deklarativ JSON-filer som används för att automatisera distributionen och konfigurationen av Azure-resurser. Om du vill byta plats på platser med hjälp av Resource Manager-mallar anger du två egenskaper på resurserna *Microsoft. Web/Sites/fackes* och *Microsoft. Web/Sites* :

- `buildVersion`: det här är en sträng egenskap som representerar den aktuella versionen av appen som distribuerats på platsen. Exempel: "v1", "1.0.0.1" eller "2019-09-20T11:53:25.2887393-07:00".
- `targetBuildVersion`: det här är en sträng egenskap som anger vad `buildVersion` platsen ska ha. Om targetBuildVersion inte är samma som den aktuella `buildVersion` utlöser detta växlings åtgärden genom att söka efter den plats som har angivet `buildVersion` .

### <a name="example-resource-manager-template"></a>Exempel på en Resource Manager-mall

Följande Resource Manager-mall kommer att uppdatera `buildVersion` för mellanlagringsplatsen och ange `targetBuildVersion` på produktions platsen. Detta byter ut de två platserna. Mallen förutsätter att du redan har en webapp som skapats med en plats med namnet "mellanlagring".

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

Den här Resource Manager-mallen är idempotenta, vilket innebär att den kan köras upprepade gånger och producera samma tillstånd för platserna. Efter den första körningen `targetBuildVersion` kommer att matcha den aktuella `buildVersion` , så en växling kommer inte att utlösas.

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>Automatisera med CLI

[Azure CLI](https://github.com/Azure/azure-cli) -kommandon för distributions platser finns i [AZ webapp Deployment slot](/cli/azure/webapp/deployment/slot).

## <a name="troubleshoot-swaps"></a>Felsöka växlingar

Om ett fel inträffar under en [plats växling](#AboutConfiguration)är det inloggat i *D:\home\LogFiles\eventlog.xml*. Den är också inloggad i den programspecifika fel loggen.

Här följer några vanliga växlings fel:

- En HTTP-begäran till program roten har uppnåtts. Växlings åtgärden väntar i 90 sekunder för varje HTTP-begäran och försöker upp till 5 gånger. Om alla nya försök har överskridits stoppas växlings åtgärden.

- Initieringen av den lokala cachen kan Miss förfalla när innehållet i appen överskrider den lokala disk kvoten som angetts för den lokala cachen Mer information finns i [Översikt över Local cache](overview-local-cache.md).

- Vid [anpassad uppvärmning](#Warm-up)görs HTTP-begäranden internt (utan att gå via den externa URL: en). De kan inte utföras med vissa regler för URL-omskrivning i *Web.config*. Till exempel kan regler för omdirigering av domän namn eller tvingande HTTPS förhindra att värme begär Anden når appens kod. Undvik det här problemet genom att ändra dina omskrivnings regler genom att lägga till följande två villkor:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Utan en anpassad värme kan du fortfarande blockera HTTP-förfrågningar om reglerna för URL-omskrivning. Undvik det här problemet genom att ändra reglerna för att skriva om genom att lägga till följande villkor:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```

- När plats växlingen har växlats kan appen uppleva oväntade omstarter. Detta beror på att när du har växlat över bindningen för värdnamn, är bindnings bindningen inte synkroniserad, vilket inte leder till omstarter. Vissa underliggande lagrings händelser (till exempel lagrings volym växling vid fel) kan dock identifiera dessa avvikelser och tvinga alla arbets processer att starta om. Om du vill minimera de här typerna av omstarter ställer du in [ `WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG=1` app-inställningen](https://github.com/projectkudu/kudu/wiki/Configurable-settings#disable-the-generation-of-bindings-in-applicationhostconfig) på *alla platser*. Den här appens inställning fungerar dock *inte* med Windows Communication Foundation (WCF)-appar.

## <a name="next-steps"></a>Nästa steg
[Blockera åtkomst till platser som inte är för produktion](app-service-ip-restrictions.md)