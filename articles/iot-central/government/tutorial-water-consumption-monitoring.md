---
title: 'Självstudie: Skapa en app för övervakning av vattenförbrukning med Azure IoT Central'
description: 'Självstudie: Lär dig att skapa ett program för övervakning av vattenförbrukning med hjälp Azure IoT Central programmallar.'
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: b65013bbf21faa8bffdcf799a991952b69f5fead
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714478"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-with-azure-iot-central"></a>Självstudie: Skapa ett program för övervakning av vattenförbrukning med Azure IoT Central

Den här självstudien visar hur du skapar Azure IoT Central ett program för övervakning av Azure IoT Central med hjälp av programmallen för övervakning av Azure IoT Central vattenförbrukning.

I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
> * Använd mallen Azure IoT Central för övervakning av vattenförbrukning för att skapa ditt program för övervakning av vattenförbrukning.
> * Utforska och anpassa operatörsinstrumentpanelen.
> * Utforska enhetsmallar.
> * Utforska simulerade enheter.
> * Utforska och konfigurera regler.
> * Konfigurera jobb.
> * Anpassa din programmärkning med hjälp av vitmärkning.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

- En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa en på [Azure-registreringssidan.](https://aka.ms/createazuresubscription)

## <a name="create-a-water-consumption-monitoring-app-with-azure-iot-central"></a>Skapa en app för övervakning av vattenförbrukning med Azure IoT Central

I det här avsnittet använder du mallen Azure IoT Central för övervakning av vattenförbrukning för att skapa ditt program för övervakning av vattenförbrukning Azure IoT Central.

Så här skapar du Azure IoT Central övervakningsapplikationen för vattenförbrukning:

1. Gå till [Azure IoT Central webbplats.](https://aka.ms/iotcentral)

    Om du har en Azure-prenumeration loggar du in med de autentiseringsuppgifter som du använder för att komma åt den. Annars loggar du in med en Microsoft-konto.

    ![Ange ditt organisationskonto](media/tutorial-waterconsumptionmonitoring/sign-in.png)

1. Välj **Skapa** i den vänstra rutan och välj **fliken** Myndigheter. På **sidan Myndigheter** visas flera mallar för myndighetsprogram.

   ![Skapa mallar för myndighetsappen](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. Välj **programmallen Övervakning av** vattenförbrukning.
Den här mallen innehåller en enhetsmall för vattenförbrukning, en simulerad enhet, en operatörsinstrumentpanel och förkonfigurerade övervakningsregler.

1. Välj **Skapa app** för att öppna formuläret **Skapa** nytt program med följande fält:
    * **Programnamn:** Som standard använder programmet övervakning av *vattenförbrukning följt* av en unik ID-sträng som Azure IoT Central genererar. Du kan också välja ett eget programnamn. Du kan också ändra namnet på programmet senare.
    * **URL:** Azure IoT Central automatiskt en URL baserat på programnamnet. Du kan välja att uppdatera URL:en efter behov. Du kan också ändra URL:en senare.
    * Om du har en Azure-prenumeration anger du **information om Katalog,** **Azure-prenumeration** **och** Plats. Om du inte har någon prenumeration kan du välja alternativet **för en kostnadsfri utvärderingsversion** på 7 dagar och fylla i de kontaktuppgifter som krävs.

    Mer information om kataloger och prenumerationer finns i Skapa [en snabbstart för program.](../core/quick-deploy-iot-central.md)

1. Välj **Skapa** längst ned på sidan.

    ![Azure IoT Central Ny programsida](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

    ![Azure IoT Central faktureringsinformationssidan](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring-billinginfo.png)

Nu har du skapat en övervakningsapp för vattenförbrukning med hjälp Azure IoT Central övervakningsmallen för vattenförbrukning.

Övervakningsprogrammet för vattenförbrukning levereras med förkonfigurerad:

* Exempeloperatorinstrumentpaneler.
* Exempel på fördefinierade enhetsmallar för vattenflöde och valvet.
* Simulerade vattenflöden och smart valve-enheter.
* Regler och jobb.
* Exempel på varumärkesmärkning med hjälp av vitmärkning.

Det är ditt program och du kan ändra det när som helst. Nu ska vi utforska programmet och göra några anpassningar.

## <a name="explore-and-customize-the-operator-dashboard"></a>Utforska och anpassa operatörsinstrumentpanelen

När du har skapat programmet öppnas instrumentpanelen **för Wide World-vattenförbrukning.**

   ![Instrumentpanel för övervakning av vattenförbrukning](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

Som byggare kan du skapa och anpassa vyer på instrumentpanelen för operatörer. Nu ska vi utforska instrumentpanelen innan du försöker anpassa den.

> [!NOTE]
> Alla data som visas på instrumentpanelen baseras på simulerade enhetsdata, vilket vi ska utforska i nästa avsnitt.
  
Instrumentpanelen består av olika typer av paneler:

* **Bildpanel för Wide World Water Utility:** Den första panelen på instrumentpanelen är en bildpanel av det fiktiva vattenverktyget Wide World Water. Du kan anpassa panelen genom att infoga en egen bild eller ta bort den.
* **KPI-panel för genomsnittligt** vattenflöde: KPI-panelen är konfigurerad för att visa ett exempel *på medelvärdet under de senaste 30 minuterna.* Du kan anpassa KPI-panelen och ange den till en annan typ och ett annat tidsintervall.
* **Paneler för enhetskommandon:** Dessa paneler inkluderar **panelerna Stäng-valvet,** **Öppna valvet** och **Ange valvet position.** Om du väljer kommandona kommer du till kommandosidan för den simulerade enheten. I Azure IoT Central är *ett kommando* en *enhetskapacitetstyp.* Vi ska utforska det här konceptet senare i avsnittet "Enhetsmall" i den här självstudien.
* **Vattendistributionsområdeskarta:** Kartan använder Azure Maps, som du kan konfigurera direkt i Azure IoT Central. Kartpanelen visar enhetens plats. Hovra över kartan och prova kontrollerna över kartan, till exempel *zooma in,* *zooma ut* eller *expandera*.

    ![Instrumentpanelskarta för övervakning av vattenförbrukning](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* **Linjediagram för genomsnittligt** vattenflöde och linjediagram för **miljövillkor:** Du kan visualisera en eller flera telemetrier för enheter som ritats som ett linjediagram över ett önskat tidsintervall.
* **Diagram över genomsnittlig heatmap** för valvettryck: Du kan välja visualiseringstypen heatmap för enhettelemetridata som du vill se distribuerade över ett tidsintervall med ett färgindex.
* **Innehållspanel för att återställa tröskelvärden för** aviseringar: Du kan inkludera anrop till åtgärd-innehållspaneler och bädda in en länk till en åtgärdssida. I det här fallet tar tröskelvärdet för återställningsavisering dig till programmet **Jobb**, där du kan köra uppdateringar av enhetsegenskaper. Vi utforskar det här alternativet senare i avsnittet "Konfigurera jobb" i den här självstudien.
* **Egenskapspaneler:** Instrumentpanelen visar **driftinformation för Valve,** **tröskelvärden för flödesaviseringar** och **informationspaneler för** underhåll.

### <a name="customize-the-dashboard"></a>Anpassa instrumentpanelen

Som byggare kan du anpassa vyer i instrumentpanelen för operatörer.

1. Välj **Redigera för** att anpassa **instrumentpanelen För vattenförbrukning i Wide World.** Du kan anpassa instrumentpanelen genom att välja **redigera-menyn.** När instrumentpanelen är i **redigeringsläge** kan du lägga till nya paneler eller konfigurera den.

     ![Redigera instrumentpanel](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

1. Välj **+ Ny för** att skapa en ny instrumentpanel och konfigurera den från grunden. Du kan ha flera instrumentpaneler och du kan flytta mellan dina instrumentpaneler på instrumentpanelens meny.

## <a name="explore-the-device-template"></a>Utforska enhetsmallen

En enhetsmall i Azure IoT Central definierar kapaciteten för en enhet, som kan vara telemetri, egenskap eller kommando. Som byggare kan du definiera en eller flera enhetsmallar i Azure IoT Central som representerar kapaciteten hos de enheter som du ska ansluta.

Övervakningsprogrammet för vattenförbrukning levereras med två referensenhetsmallar som representerar en *flödesmätare* och en *smart valve-enhet.*

Så här visar du enhetsmallen:

1. Välj **Enhetsmallar** i det vänstra fönstret i ditt program i Azure IoT Central. I listan **Enhetsmallar** visas två enhetsmallar, Smart Valve och **Flödesmätare.** 

   ![Enhetsmall](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

1. Välj **enhetsmallen** Flödesmätare och bekanta dig med enhetens funktioner.

     ![Flödesmätare för enhetsmall](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customize-the-device-template"></a>Anpassa enhetsmallen

Anpassa enhetsmallen:

1. Gå till **Anpassa** på **menyn Enhetsmallar.**
1. Hitta `Temperature` telemetritypen.
1. Uppdatera **Visningsnamn** för `Temperature` till `Reported temperature` .
1. Uppdatera måttenheten eller ange **min-värdet och** **maxvärdet**.
1. Välj **Spara** för att spara eventuella ändringar.

### <a name="add-a-cloud-property"></a>Lägga till en molnegenskap

1. Gå till **Molnegenskaper** på menyn **Enhetsmallar.**
1. Lägg till en ny molnegenskap genom att **välja + Lägg till molnegenskap**.
    I Azure IoT Central kan du lägga till en egenskap som är relevant för enheten. En molnegenskap kan till exempel vara ett tröskelvärde för aviseringar som är specifika för ett installationsområde, tillgångsinformation eller annan underhållsinformation.
1. Välj **Spara** för att spara ändringarna.

### <a name="views"></a>Vyer

Enhetsmallen för övervakning av vattenförbrukning innehåller fördefinierade vyer. Utforska vyerna så kan du göra uppdateringar. Vyerna definierar hur operatörer ser enhetsdata men anger även molnegenskaper.

  ![Vyer för enhetsmall](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>Publicera

Om du har gjort några ändringar ska du se **till att** publicera enhetsmallen.

### <a name="create-a-new-device-template"></a>Skapa en ny enhetsmall

Välj **+ Ny** för att skapa en ny enhetsmall och följ skapandeprocessen.
Du kan skapa en anpassad enhetsmall från grunden eller välja en enhetsmall från Azure-enhetskatalogen.

## <a name="explore-simulated-devices"></a>Utforska simulerade enheter

I Azure IoT Central kan du skapa simulerade enheter för att testa din enhetsmall och ditt program. Övervakningsprogrammet för vattenförbrukning har två simulerade enheter mappade till **flödesmätaren och** **Smart Valve-enhetsmallar.**

### <a name="view-the-devices"></a>Visa enheterna

1. Välj **Enheter**  >  **Alla enheter** i det vänstra fönstret.

   ![Fönstret Alla enheter](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

1. Välj **Smart Valve 1**.

    ![Smart Valve 1](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

1. På fliken **Kommandon** kan du se de tre enhetskommandona **(** Stäng valvet, **Öppna** valvet och Ange **valvet position**) som är funktioner som definieras i mallen **Smart Valve-enhet.**

1. Utforska fliken **Enhetsegenskaper** och fliken **Instrumentpanel för** enhet.

> [!NOTE]
> Observera att alla flikar konfigureras från enhetsmallvyerna.

### <a name="add-new-devices"></a>Lägga till nya enheter

Lägg till nya enheter genom **att välja +** Ny på **fliken** Enheter.

## <a name="explore-and-configure-rules"></a>Utforska och konfigurera regler

I Azure IoT Central kan du skapa regler för att automatiskt övervaka enhettelemetri och utlösa åtgärder när ett eller flera villkor uppfylls. Åtgärderna kan omfatta att skicka e-postaviseringar eller utlösa en Microsoft Power Automate åtgärd eller en webhook-åtgärd för att skicka data till andra tjänster.

Övervakningsprogrammet för vattenförbrukning som du skapade har tre förkonfigurerade regler.

### <a name="view-rules"></a>Visa regler

1. Välj **Regler** i det vänstra fönstret.

   ![Fönstret Regler](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

1. Välj **Avisering för högt** vattenflöde, som är en av de förkonfigurerade reglerna i programmet.

     ![Hög pH-avisering](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    Regeln `High water flow alert` är konfigurerad för att kontrollera mot villkoret `Flow` är `greater than` `Max flow threshold` . Tröskelvärdet för maximalt flöde är en molnegenskap som definieras **i mallen för Smart Valve-enheten.** Värdet för `Max flow threshold` anges per enhetsinstans.

Nu ska vi skapa en e-poståtgärd.

Så här lägger du till en åtgärd i regeln:

1. Välj **+ E-post**.
1. Ange **Hög flödesavisering** som **eget visningsnamn** för åtgärden.
1. Ange den e-postadress som är kopplad Azure IoT Central ditt konto i **Till**.
1. Du kan också ange en anteckning som ska ingå i e-postmeddelandet.
1. Välj **Klar** för att slutföra åtgärden.
1. Välj **Spara** för att spara den nya regeln.
1. Aktivera regeln.

Inom några minuter bör du få ett e-postmeddelande när det konfigurerade villkoret har uppfyllts.

> [!NOTE]
> Programmet skickar ett e-postmeddelande varje gång ett villkor uppfylls. Välj **Inaktivera om** du vill inaktivera regeln för att sluta ta emot e-post från den automatiserade regeln.
  
Så här skapar du en ny regel:

* Välj **+ Ny** på **fliken** Regler i den vänstra rutan.

## <a name="configure-jobs"></a>Konfigurera jobb

I Azure IoT Central kan du med jobb utlösa enhets- eller molnegenskapsuppdateringar på flera enheter. Förutom egenskaper kan du även använda jobb för att utlösa enhetskommandon på flera enheter. Azure IoT Central automatiserar arbetsflödet åt dig.

1. Välj **Jobb** i den vänstra rutan.
1. Välj **+ Nytt** och konfigurera ett eller flera jobb.

## <a name="customize-your-application"></a>Anpassa ditt program

Som byggare kan du ändra flera inställningar för att anpassa användarupplevelsen i ditt program.

1. Välj **Administration**  >  **Anpassa ditt program.**
1. Om du vill välja en bild att ladda upp **som programlogotyp** väljer **du knappen** Ändra.
1. Om du vill **välja en bild** av webbläsarikonen som visas på webbläsarflikar väljer du **knappen** Ändra.
1. Du kan också ersätta webbläsarens **standardfärger genom att** lägga till hexadecimala HTML-färgkoder.

   ![Val för programlogotyp, webbläsarikon och webbläsarfärger](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1. Du kan också ändra programavbildningar genom att **välja Inställningar för**  >  **administrationsprogram.** Om du vill välja en bild att ladda upp som programbild väljer du **knappen Välj** bild.
1. Slutligen kan du också ändra **tema genom** att välja **ikonen** Inställningar i det övre högra hörnet av programmet.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte tänker fortsätta att använda det här programmet tar du bort det.

1. Välj **Administration** i det vänstra fönstret i Azure IoT Central program.
1. Välj **Programinställningar** och välj sedan **Ta** bort längst ned på sidan.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om 

> [!div class="nextstepaction"]
> [Övervakningsbegrepp för vattenförbrukning.](./concepts-waterconsumptionmonitoring-architecture.md)
