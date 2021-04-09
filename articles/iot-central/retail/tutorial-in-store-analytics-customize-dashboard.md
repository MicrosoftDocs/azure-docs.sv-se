---
title: Självstudie – anpassa instrument panelen för operatören i Azure IoT Central
description: I den här självstudien visas hur du anpassar instrument panelen för operatören i ett IoT Central program och hanterar enheter.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/12/2019
ms.openlocfilehash: 74ce36652b1a00ac6813c62a17d44e2a5486645f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831728"
---
# <a name="tutorial--customize-the-operator-dashboard-and-manage-devices-in-azure-iot-central"></a>Självstudie: anpassa instrument panelen för operatör och hantera enheter i Azure IoT Central


I den här självstudien, som ett verktyg, lär du dig att anpassa instrument panelen för operatören i Azure IoT Central i Store Analytics-program. Program operatörer kan använda den anpassade instrument panelen för att köra programmet och hantera anslutna enheter.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Ändra instrument panelens namn
> * Anpassa bild paneler på instrument panelen
> * Arrangera paneler för att ändra layouten
> * Lägg till telemetri paneler i visnings villkor
> * Lägg till egenskaps paneler för att Visa enhets information
> * Lägg till kommando paneler för att köra kommandon

## <a name="prerequisites"></a>Förutsättningar

Verktyget bör slutföra självstudien för att skapa Azure IoT Central i Store Analytics-program och lägga till enheter:

* [Skapa ett in-Store Analytics-program i Azure IoT Central](./tutorial-in-store-analytics-create-app.md) (krävs)

## <a name="change-the-dashboard-name"></a>Ändra instrument panelens namn
Om du vill anpassa instrument panelen för operatören måste du redigera standard instrument panelen i ditt program. Du kan också skapa ytterligare nya instrument paneler. Det första steget för att anpassa instrument panelen i programmet är att ändra namnet.

1. Gå till webbplatsen för [Azure IoT Central Application Manager](https://aka.ms/iotcentral) .

1. Öppna programmet för villkors övervakning som du skapade i själv studie kursen [skapa ett program för analys i Azure IoT Central](./tutorial-in-store-analytics-create-app.md) .

1. Välj **Redigera** i instrument panelens verktygsfält. I redigerings läge kan du anpassa utseendet, layouten och innehållet på instrument panelen.

    ![Azure IoT Central redigera instrument panel](./media/tutorial-in-store-analytics-customize-dashboard/dashboard-edit.png)

1. Alternativt kan du dölja det vänstra fönstret. Om du döljer det vänstra fönstret får du ett större arbets yta för att redigera instrument panelen.

1. Ange ett eget namn för instrument panelen i **namn på instrument panelen.** I den här självstudien används ett fiktivt företag som heter Contoso, och exempel på instrument panelen är *contoso-instrumentpanel*. 

1. Välj **Spara**. Ändringarna sparas i instrument panelen och redigerings läget är inaktiverat.

    ![Azure IoT Central ändra instrument panels namn](./media/tutorial-in-store-analytics-customize-dashboard/dashboard-change-name.png)

## <a name="customize-image-tiles-on-the-dashboard"></a>Anpassa bild paneler på instrument panelen
En instrument panel för Azure IoT Central-program består av en eller flera paneler. En panel är en rektangulär behållare för visning av innehåll på en instrument panel. Du kopplar olika typer av innehåll med paneler och du kan dra, släppa och ändra storlek på paneler för att anpassa en layout för instrument panelen. Det finns flera typer av paneler för att visa innehåll. Bild paneler innehåller bilder och du kan lägga till en URL som gör det möjligt för användare att klicka på avbildningen. Etikett paneler visar oformaterad text. Markdown paneler innehåller formaterat innehåll och gör att du kan ange en bild, en URL, en rubrik och markdown kod som återges som HTML. Telemetri, egenskaper eller kommando paneler visar enhets information. 

I det här avsnittet får du lära dig hur du anpassar bild paneler på instrument panelen.

Anpassa bild panelen som visar en varumärkes bild på instrument panelen:

1. Välj **Redigera** i instrument panelens verktygsfält. 

1. Välj **Konfigurera** på bild panelen som visar Northwinds varumärkes avbildningen. 

    ![Azure IoT Central redigera varumärkes avbildning](./media/tutorial-in-store-analytics-customize-dashboard/brand-image-edit.png)

1. Ändra **rubriken**. Rubriken visas när en användare hovrar över avbildningen.

1. Välj **bild**. En dialog ruta öppnas där du kan ladda upp en anpassad avbildning. 

1. Du kan också ange en URL för avbildningen.

1. Välj **uppdaterings konfiguration**. Knappen **Uppdatera konfiguration** sparar ändringar i instrument panelen och låter redigerings läget vara aktiverat.

    ![Azure IoT Central Spara varumärkes avbildning](./media/tutorial-in-store-analytics-customize-dashboard/brand-image-save.png)

1. Du kan också välja **Konfigurera** i panelen med namnet **dokumentation** och ange en URL för support innehåll. 

För att anpassa bild panelen som visar en karta över sensor zonerna i butiken:

1. Välj **Konfigurera** på panelen avbildning som visar standard kartan för lagrings zon. 

1. Välj **bild** och Använd dialog rutan för att ladda upp en anpassad avbildning av en lagrings zon karta. 

1. Välj **uppdaterings konfiguration**.

    ![Azure IoT Central Spara butiks karta](./media/tutorial-in-store-analytics-customize-dashboard/store-map-save.png)

    I exemplet contoso Store-kartan visas fyra zoner: två arbets zoner, en zon för kläder och personlig vård samt en zon för livsmedels-och Le. I den här självstudien kopplar du sensorer till dessa zoner för att ge telemetri.

    ![Azure IoT Central lagrings zoner](./media/tutorial-in-store-analytics-customize-dashboard/store-zones.png)

1. Välj **Spara**. 

## <a name="arrange-tiles-to-modify-the-layout"></a>Arrangera paneler för att ändra layouten
Ett viktigt steg i anpassningen av en instrument panel är att ordna om panelerna för att skapa en användbar vy. Program operatörer använder instrument panelen för att visualisera telemetri, hantera enheter och övervaka villkor i en butik. Azure IoT Central fören klar Application Builder-uppgiften att skapa en instrument panel. I redigerings läget för instrument panelen kan du snabbt lägga till, flytta, ändra storlek på och ta bort paneler. Program mal len **BA-utcheckning i butiken** fören klar också uppgiften att skapa en instrument panel. Den innehåller en fungerande instrument panels layout, med sensorer anslutna och paneler som visar antalet betalnings rader och miljö förhållanden.

I det här avsnittet ordnar du om instrument panelen i program mal len **BA-** betalningsprogram för att skapa en anpassad layout.

Ta bort paneler som du inte planerar att använda i ditt program:

1. Välj **Redigera** i instrument panelens verktygsfält. 

1. Välj **X ta** bort för att ta bort följande paneler: **tillbaka till alla zoner**, **gå till butiks instrument panel**, **vänte tid** och alla tre paneler som är kopplade till **utcheckning 3**. Contoso Store-instrumentpanelen använder inte dessa paneler. 

    ![Ta bort paneler i Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/delete-tiles.png)

1. Rulla för att visa de återstående instrument panelerna i vyn.

1. Välj **X ta** bort om du vill ta bort följande paneler: varm ställnings **zon**, **avkylnings zon**, **beläggnings sensor inställningar**, **termostat sensor inställningar** och **miljö förhållanden**. 

   ![Ta bort återstående paneler i Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/delete-tiles-2.png)

1. Välj **Spara**. Att ta bort oanvända paneler frigör utrymme på sidan Redigera och fören klar vyn för instrument panelen för operatörer.

1. Visa dina ändringar på instrument panelen.

   ![Azure-IoT Central efter borttagning av paneler](./media/tutorial-in-store-analytics-customize-dashboard/after-delete-tiles.png)

När du har tagit bort oanvända paneler ordnar du om de återstående panelerna för att skapa en ordnad layout. Den nya layouten innehåller utrymme för paneler som du lägger till i ett senare steg.

Så här ordnar du om de återstående panelerna:

1. Välj **Redigera**.

1. Välj den **inbyggda inbyggda program varans** panel och dra den till höger om batteri panelen i **besittning** .

1. Välj den **inbyggda program varans termostat** -panel och dra den till höger om batteri panelen för **termostat** .

1. Välj **Spara**.

1. Visa layoutändringar. 

    ![Batteri paneler för Azure IoT Central inbyggd program vara](./media/tutorial-in-store-analytics-customize-dashboard/firmware-battery-tiles.png)

## <a name="add-telemetry-tiles-to-display-conditions"></a>Lägg till telemetri paneler i visnings villkor
När du har anpassat instrument panelens layout är du redo att lägga till paneler för att Visa telemetri. Om du vill skapa en telemetri-panel väljer du en enhets mall och enhets instans och väljer sedan enhetsspecifika telemetri som ska visas i panelen. Program mal len **BA-utcheckning i butiken** innehåller flera telemetri paneler på instrument panelen. De fyra panelerna i de två arbets zonerna visar telemetri från den simulerade beläggnings sensorn. Panelen **person trafik** visar antalet i de två arbets zonerna. 

I det här avsnittet lägger du till två fler telemetri paneler för att Visa miljötelemetri från RuuviTag-sensorer som du har lagt till i självstudien [skapa ett in-Store-analys i Azure IoT Central](./tutorial-in-store-analytics-create-app.md) . 

För att lägga till paneler för att Visa miljö data från RuuviTag-sensorer:

1. Välj **Redigera**.

1. Välj `RuuviTag` i listan **enhets mal len** . 

1. Välj en **enhets instans** av någon av de två RuuviTag-sensorerna. I exempel contoso Store väljer `Zone 1 Ruuvi` du att skapa en telemetri panel för zon 1. 

1. Välj `Relative humidity` och `temperature` i listan **telemetri** . Detta är de telemetri-objekt som visas för varje zon på panelen.

1. Välj **kombinera**. 

    ![Azure IoT Central lägga till RuuviTag panel 1](./media/tutorial-in-store-analytics-customize-dashboard/add-zone1-ruuvi.png)

    En ny panel visas för att Visa kombinerad fuktighet och temperatur telemetri för den valda sensorn. 

1. Välj **Konfigurera** på den nya panelen för RuuviTag-sensorn. 

1. Ändra **rubriken** till *zon 1 miljö*. 

1. Välj **uppdaterings konfiguration**.

1. Upprepa föregående steg för att skapa en panel för den andra sensor instansen. Ange **titeln** zon 2- *miljö* och välj sedan **Uppdatera konfiguration.**

1. Dra panelen **zon 2 miljön** under den **inbyggda termostat** -panelen. 

1. Dra panelen med rubriken **zon 1 miljön** under **personens trafik** panel. 

1. Välj **Spara**. På instrument panelen visas Zone-telemetri i de två nya panelerna.

    ![Azure IoT Central alla RuuviTag-paneler](./media/tutorial-in-store-analytics-customize-dashboard/all-ruuvitag-tiles.png)

Så här redigerar du panelen **personers trafik** för att Visa telemetri för bara två arbets zoner:

1. Välj **Redigera**. 

1. Välj **Konfigurera** på **personens trafik** panel.

1. I **telemetri** väljer du **antal 1**, **antal 2** och **antal 3**. 

1. Välj **uppdaterings konfiguration**. Den befintliga konfigurationen på panelen rensas. 

1. Välj **Konfigurera** igen på **personens trafik** panel.

1. I **telemetri** väljer du **antal 1** och **antal 2**. 

1. Välj **uppdaterings konfiguration**. 

1. Välj **Spara**.  Den uppdaterade instrument panelen visar antal för dina två arbets zoner, som baseras på den simulerade beläggnings sensorn.

    ![Azure IoT Central människor trafik två band](./media/tutorial-in-store-analytics-customize-dashboard/people-traffic-two-lanes.png)

## <a name="add-property-tiles-to-display-device-details"></a>Lägg till egenskaps paneler för att Visa enhets information
Program operatörer använder instrument panelen för att hantera enheter och övervaka status. Lägg till en panel för varje RuuviTag för att tillåta operatörer att visa program varu versionen. 

Så här lägger du till en egenskaps panel för varje RuuviTag:

1. Välj **Redigera**.

1. Välj `RuuviTag` i listan **enhets mal len** . 

1. Välj en **enhets instans** av någon av de två RuuviTag-sensorerna. I exempel contoso Store väljer `Zone 1 Ruuvi` du att skapa en telemetri panel för zon 1. 

1. Välj **egenskaper > program varu version**.

1. Välj **kombinera**. 

1. Välj **Konfigurera** på den nyligen skapade panelen med titeln **program version**. 

1. Ändra **titeln** till *Ruuvi 1-program varu version*.

1. Välj **uppdaterings konfiguration**. 

1. Dra panel med rubriken **Ruuv 1-programvara** under panelen **zon 1s miljö** .

1. Upprepa föregående steg för att skapa en egenskaps panel för program varu version för den andra RuuviTag. 

1. Välj **Spara**.  

    ![Egenskaps paneler för Azure IoT Central RuuviTag](./media/tutorial-in-store-analytics-customize-dashboard/add-ruuvi-property-tiles.png)

## <a name="add-command-tiles-to-run-commands"></a>Lägg till kommando paneler för att köra kommandon
Program operatörer använder även instrument panelen för att hantera enheter genom att köra kommandon. Du kan lägga till kommando paneler på instrument panelen som ska köra fördefinierade kommandon på en enhet. I det här avsnittet lägger du till en kommando panel för att aktivera operatörer för att starta om Rigado-gatewayen. 

Så här lägger du till en kommando panel för att starta om gatewayen:

1. Välj **Redigera**. 

1. Välj `C500` i listan **enhets mal len** . Det är mallen för Rigado C500-gatewayen. 

1. Välj Gateway-instansen i **enhets instansen**.

1. Välj **kommandot > starta om** och dra det till instrument panelen bredvid butiks kartan. 

1. Välj **Spara**. 

1. Visa den slutförda contoso-instrumentpanelen. 

    ![Slutför anpassning av instrument panelen i Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/completed-dashboard.png)

1. Du kan också välja panelen **Starta** om för att köra kommandot starta om på din gateway.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

* Ändra instrument panelens namn
* Anpassa bild paneler på instrument panelen
* Arrangera paneler för att ändra layouten
* Lägg till telemetri paneler i visnings villkor
* Lägg till egenskaps paneler för att Visa enhets information
* Lägg till kommando paneler för att köra kommandon

Nu när du har anpassat instrument panelen i Azure IoT Central in-Store Analytics-program, här är det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Exportera data och visualisera insikter](./tutorial-in-store-analytics-export-data-visualize-insights.md)
