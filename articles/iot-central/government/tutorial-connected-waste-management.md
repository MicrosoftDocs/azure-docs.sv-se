---
title: 'Självstudie: skapa en ansluten hanterings app för slöseri med Azure IoT Central'
description: 'Självstudie: Lär dig att bygga ett anslutet program för hantering av program med hjälp av Azure IoT Central programmallar'
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 65245b3c4b7e18670682f7e9e890453e32337644
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820087"
---
# <a name="tutorial-create-a-connected-waste-management-app"></a>Självstudie: skapa en ansluten app för avfalls hantering

Den här självstudien visar hur du använder Azure IoT Central för att skapa ett anslutet hanterings program för skräp hantering. 

Mer specifikt kan du lära dig att: 

> [!div class="checklist"]
> Använd mallen Azure IoT Central- *ansluten avfalls hantering* för att skapa din app.
> Utforska och anpassa instrument panelen för operatorn. Utforska enhets mal len för den anslutna avfalls platsen.
> Utforska simulerade enheter.
> Utforska och konfigurera regler.
> Konfigurera jobb.
> Anpassa din program anpassning.

## <a name="prerequisites"></a>Förutsättningar

En Azure-prenumeration rekommenderas. Du kan också använda en kostnads fri 7-dagars utvärderings version. Om du inte har någon Azure-prenumeration kan du skapa en på [sidan för Azure-registrering](https://aka.ms/createazuresubscription).

## <a name="create-your-app-in-azure-iot-central"></a>Skapa din app i Azure IoT Central

I det här avsnittet använder du mallen för hantering av anslutna avfalls hantering för att skapa din app i Azure IoT Central. Gör så här:

1. Gå till [Azure IoT Central](https://aka.ms/iotcentral).

    Om du har en Azure-prenumeration loggar du in med de autentiseringsuppgifter som du använder för att komma åt den. Logga annars in med en Microsoft-konto:

    ![Skärm bild av Microsoft-inloggning.](./media/tutorial-connectedwastemanagement/sign-in.png)

1. Välj **build** i det vänstra fönstret. Välj fliken **myndigheter** . Sidan myndigheter visar flera myndighets programmallar.

    ![Skärm bild av Azure IoT Central build-sidan.](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. Välj program mal len **ansluten avfalls hantering** . Den här mallen innehåller ett exempel på en enhets mall för en ansluten avfalls lager plats, en simulerad enhet, en instrument panel för operatör och förkonfigurerade övervaknings regler.    

1. Välj **skapa app**, som öppnar dialog rutan **nytt program** . Fyll i informationen för följande fält:
    * **Program namn**. Som standard använder programmet **ansluten avfalls hantering**, följt av en unik ID-sträng som Azure IoT Central genererar. Alternativt kan du välja ett eget program namn. Du kan också ändra program namnet senare.
    * **URL**. Du kan också välja önskad URL. Du kan ändra URL senare. 
    * **Pris plan**. Om du har en Azure-prenumeration anger du din katalog, din Azure-prenumeration och region i lämpliga fält i dialog rutan **fakturerings information** . Om du inte har någon prenumeration väljer du **kostnads fri** för att aktivera utvärderings versionen för 7 dagar och slutför nödvändig kontakt information.  

    Mer information om kataloger och prenumerationer finns i [snabb start – skapa ett Azure IoT Central-program](../core/quick-deploy-iot-central.md).

1. Klicka på **skapa** längst ned på sidan. 

    ![Skärm bild av Azure IoT Central skapa nytt program dialog ruta.](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)
    
    ![Skärm bild av dialog rutan information om Azure IoT Central fakturerings information.](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement-billinginfo.png)

 
Det nyligen skapade programmet levereras med förkonfigurerade:
* Exempel instrument panels instrument paneler.
* Exempel på fördefinierade enhets mallar för anslutet pappers korgen.
* Simulerade anslutna avfalls lager enheter.
* Regler och jobb.
* Exempel anpassning. 

Det är ditt program och du kan ändra det när som helst. Nu ska vi utforska programmet och göra några anpassningar.  

## <a name="explore-and-customize-the-operator-dashboard"></a>Utforska och anpassa instrument panelen för operatören 

Ta en titt på **instrument panelen för avfalls hantering i hela världen**, som du ser när du har skapat din app.

   ![Skärm bild av Wide World avfalls hanterings instrument panel.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

Som ett verktyg kan du skapa och anpassa vyer på instrument panelen för operatörer. Först ska vi utforska instrument panelen. 

>[!NOTE]
>Alla data som visas i instrument panelen baseras på simulerade enhets data, som du ser mer av i nästa avsnitt. 

Instrument panelen består av olika paneler:

* **Bild panel för bred avfalls verktyg**: den första panelen i instrument panelen är en bild panel med ett fiktivt avlopps verktyg, "Wide World avfall". Du kan anpassa panelen och placera den i en egen bild eller ta bort den. 

* **Bild panel för avfalls fack**: du kan använda bild-och innehålls paneler för att skapa en visuell representation av enheten som övervakas, tillsammans med en beskrivning. 

* **KPI-panel på fyllnings nivå**: den här panelen visar ett värde som rapporteras av en sensor för *fyllnads nivå* på en pappers korg. Fyllnings nivå och andra sensorer, t. ex. *odor meter* eller *vikt* i en avfalls lager, kan fjärrövervakas. En operatör kan vidta åtgärder, t. ex. sändning av en Last bil för SOP insamling. 

* **Områdes karta för avfalls övervakning**: den här panelen använder Azure Maps, som du kan konfigurera direkt i Azure IoT Central. Kart panelen visar enhetens plats. Försök att hovra över kartan och testa kontrollerna över kartan, t. ex. Zooma in, zooma ut eller expandera.

     ![Skärm bild av instrument panels kartan för anslutna avfalls hantering.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


* **Fyllning, odor, stapeldiagram för viktnivå**: du kan visualisera en eller flera typer av telemetridata för enheter i ett stapeldiagram. Du kan också expandera stapeldiagrammet.  

  ![Skärm bild av diagram för mall för instrument panel för ansluten avfalls hantering.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **Fält tjänster**: instrument panelen innehåller en länk till hur du integrerar med Dynamics 365 Field Services från ditt Azure IoT Central-program. Du kan till exempel använda fält tjänster för att skapa biljetter för utförsel av skräp insamlings tjänster. 


### <a name="customize-the-dashboard"></a>Anpassa instrumentpanelen 

Du kan anpassa instrument panelen genom att välja **Redigera** -menyn. Sedan kan du lägga till nya paneler eller konfigurera befintliga. Så här ser instrument panelen ut i redigerings läge: 

![Skärm bild av instrument panelen för den anslutna avfalls hanterings mal len i redigerings läge.](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

Du kan också välja **+ nytt** för att skapa en ny instrument panel och konfigurera från början. Du kan ha flera instrument paneler och du kan växla mellan dina instrument paneler på instrument panelens meny. 

## <a name="explore-the-device-template"></a>Utforska enhetens mall

En enhets mall i Azure IoT Central definierar funktionerna för en enhet, vilket kan omfatta telemetri, egenskaper eller kommandon. Som ett verktyg kan du definiera enhetsspecifika mallar som representerar funktionerna i de enheter som du ansluter till. 

Anslutet hanterings program för skräp insamling levereras med en exempel mall för en ansluten enhet för spill lager plats.

Så här visar du enhets mal len:

1. I Azure IoT Central väljer du **enhetsspecifika** i den vänstra rutan i appen. 

    ![Skärm bild som visar listan över enhetsspecifika mallar i programmet.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

1. I listan **enhets mallar** väljer du **anslutet pappers lager**.

1. Granska funktionerna i enhets mal len. Du kan se att den definierar sensorer som **Fyllnings nivå**, **odor-mätare**, **vikt** och **plats**.

   ![Skärm bild som visar information om enhets mal len för den anslutna avfalls platsen.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)


### <a name="customize-the-device-template"></a>Anpassa enhets mal len

Försök att anpassa följande:
1. Från menyn enhets mall väljer du **Anpassa**.
1. Hitta telemetri typen **odor meter** .
1. Uppdatera **visnings namnet** för **odor-mätaren** till **odor-nivån**.
1. Försök att uppdatera mått enheten eller ange **minsta värde** och **högsta värde**.
1. Välj **Spara**. 

### <a name="add-a-cloud-property"></a>Lägg till en moln egenskap 

Gör så här:
1. Från menyn enhets mall väljer du **moln egenskap**.
1. Välj **+ Lägg till moln egenskap**. I Azure IoT Central kan du lägga till en egenskap som är relevant för enheten men som inte förväntas skickas av en enhet. Till exempel kan en moln egenskap vara en aviserings tröskel som är speciell för installations ytan, till gångs information eller underhålls information. 
1. Välj **Spara**. 
 
### <a name="views"></a>Vyer 
Enhets mal len för den anslutna avfalls platsen innehåller fördefinierade vyer. Utforska vyerna och uppdatera dem om du vill. Vyerna definierar hur operatörer ser enhets data och moln egenskaper för indata. 

  ![Skärm bild av den anslutna mallen för avfalls hantering i enhets mallar.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>Publicera 

Kom ihåg att publicera enhets mal len om du har gjort några ändringar. 

### <a name="create-a-new-device-template"></a>Skapa en ny enhetsmall 

Om du vill skapa en ny enhets mall väljer du **+ ny** och följer stegen. Du kan skapa en anpassad enhets mall från början, eller så kan du välja en enhets mall från Azures enhets katalog. 

## <a name="explore-simulated-devices"></a>Utforska simulerade enheter

I Azure IoT Central kan du skapa simulerade enheter för att testa enhetens mall och program. 

Anslutet hanterings program för program vara har två simulerade enheter som är kopplade till enhets mal len för den anslutna avfalls platsen. 

### <a name="view-the-devices"></a>Visa enheterna

1. I den vänstra rutan i Azure IoT Central väljer du **enhet**. 

   ![Skärm bild av enheter med mall för anslutna avfalls hantering.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

1. Välj **ansluten spill lager** enhet.  

     ![Skärm bild av enhets egenskaperna för den anslutna avfalls hanterings mal len.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

1. Gå till fliken **moln egenskaper** . Uppdatera värdet för **fullständig aviserings tröskel för bin** från **95** till **100**. 

Utforska flikarna **Egenskaper** och **enhets instrument panel** . 

> [!NOTE]
> Alla flikar har kon figurer ATS från Device Template-vyerna.

### <a name="add-new-devices"></a>Lägg till nya enheter

Du kan lägga till nya enheter genom att välja **+ ny** på fliken **enheter** . 

## <a name="explore-and-configure-rules"></a>Utforska och konfigurera regler

I Azure IoT Central kan du skapa regler för automatisk övervakning av enhets telemetri och utlösa åtgärder när ett eller flera villkor uppfylls. Åtgärderna kan omfatta att skicka e-postmeddelanden, utlösa en åtgärd i Energis par automatisering eller starta en webhook-åtgärd för att skicka data till andra tjänster.

Anslutet hanterings program för skräp hantering har fyra exempel regler.

### <a name="view-rules"></a>Visa regler
1. I den vänstra rutan i Azure IoT Central väljer du **regler**.

   ![Skärm bild av regler för anslutna avfalls hanterings mallar.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

1. Markera **fullständig bin-avisering**.

     ![Skärm bild av fullständig avisering för bin.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 1. Den **fullständiga varningen för bin** kontrollerar följande villkor: **Fill-nivån är större än eller lika med tröskelvärdet för fullständig avisering för bin**.

    **Tröskelvärdet för fullständig avisering för bin** är en moln egenskap som definieras i enhets mal len för den anslutna avfalls platsen. 

Nu ska vi skapa en e-poståtgärd.

### <a name="create-an-email-action"></a>Skapa en e-poståtgärd

I **Åtgärds listan för** regeln kan du konfigurera en e-post-åtgärd:
1. Välj **+ e-post**. 
1. För **visnings namn** anger du **hög pH-avisering**.
1. För **till** anger du den e-postadress som är kopplad till ditt Azure IoT Central-konto. 
1. Du kan också ange en anteckning som ska inkluderas i e-postmeddelandets text.
1. Välj **färdig**  >  **Spara**. 

Nu får du ett e-postmeddelande när det konfigurerade villkoret är uppfyllt.

>[!NOTE]
>Programmet skickar ett e-postmeddelande varje gången ett villkor är uppfyllt. Inaktivera regeln om du vill sluta ta emot e-post från den automatiserade regeln. 
  
Om du vill skapa en ny regel väljer du **+ ny** i det vänstra fönstret av **regler**.

## <a name="configure-jobs"></a>Konfigurera jobb

I Azure IoT Central kan du använda jobb för att utlösa enhets-eller moln egenskaper uppdateringar på flera enheter. Du kan också använda jobb för att utlösa enhets kommandon på flera enheter. Azure IoT Central automatiserar arbets flödet åt dig. 

1. I den vänstra rutan i Azure IoT Central väljer du **jobb**. 
1. Välj **+ ny** och konfigurera ett eller flera jobb. 

## <a name="customize-your-application"></a>Anpassa ditt program 

Som verktyg kan du ändra flera inställningar för att anpassa användar upplevelsen i ditt program.

### <a name="change-the-application-theme"></a>Ändra programmets tema

Gör så här:
1. Gå till **Administration**  >  **Anpassa ditt program**.
1. Välj **ändra** för att välja en bild att ladda upp för **program logo typen**.
1. Välj **ändra** för att välja en bild som ska laddas upp för **webb läsar ikonen** (en bild som visas på webb läsar flikar).
1. Du kan också ersätta standard webbläsarens färger genom att lägga till HTML-hexadecimala färg koder. Använd **rubrik** -och **dekor** fält för det här ändamålet.

   ![Skärm bild av den kopplade hanterings mal len för Slösaren anpassa ditt program.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

1. Du kan också ändra program avbildningar. Välj inställningar för **administrations**  >  **program**  >  **Välj bild** för att välja en bild som ska laddas upp som program avbildning.
1. Slutligen kan du även ändra temat genom att välja **Inställningar** på program huvud rubriken för programmet.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort programmet med följande steg:

1. Välj **Administration** i den vänstra rutan i Azure IoT Central-appen.
1. Välj **program inställningar**  >  **ta bort**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Metoder för ansluten avfalls hantering](./concepts-connectedwastemanagement-architecture.md)
