---
title: 'Självstudie: Konfigurera en Gen2-miljö – Azure Time Series Insights Gen2 | Microsoft Docs'
description: 'Självstudie: Lär dig hur du konfigurerar en miljö i Azure Time Series Insights Gen2.'
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 02/25/2021
ms.custom: seodec18
ms.openlocfilehash: 76a33bdb773645c9e8f97a47b1378d813b165631
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103464674"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-gen2-environment"></a>Självstudie: Konfigurera en Azure Time Series Insights Gen2-miljö

Den här självstudien vägleder dig genom processen att skapa en *Azure Time Series Insights Gen2 PAYG-miljö* ().

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Skapa en Azure Time Series Insights Gen2-miljö.
> * Anslut Azure Time Series Insights Gen2-miljön till en IoT Hub.
> * Kör ett Solution Accelerator-exempel för att strömma data till Azure Time Series Insights Gen2-miljön.
> * Utföra grundläggande analys av data.
> * Definiera en typ av och hierarki för tidsseriemodell och associera dem med dina instanser.

>[!TIP]
> [IoT Solution Accelerators](https://www.azureiotsolutions.com/Accelerators) tillhandahåller förkonfigurerade lösningar i företags klass som du kan använda för att påskynda utvecklingen av anpassade IoT-lösningar.

Registrera dig för en [kostnads fri Azure-prenumeration](https://azure.microsoft.com/free/) om du inte redan har en.

## <a name="prerequisites"></a>Förutsättningar

* Du måste minst ha **deltagar** rollen för Azure-prenumerationen. Mer information finns [i tilldela Azure-roller med hjälp av Azure Portal](../role-based-access-control/role-assignments-portal.md).

* Skapa en miljö med antingen [Azure Portal](#create-an-azure-time-series-insights-gen2-environment) eller [CLI](how-to-create-environment-using-cli.md).

## <a name="create-a-device-simulation"></a>Skapa en enhetssimulering

I det här avsnittet ska du skapa tre simulerade enheter som skickar data till en Azure IoT Hub-instans.

1. Gå till [Sidan för Azure IoT-lösningsacceleratorer](https://www.azureiotsolutions.com/Accelerators). Logga in med ditt Azure-konto och välj sedan **enhets simulering**.

   [![Sidan Azure IoT Solution Accelerators.](media/tutorial-set-up-environment/iot-solution-accelerators-landing-page.png)](media/tutorial-set-up-environment/iot-solution-accelerators-landing-page.png#lightbox)

1. Rulla nedåt för att läsa avsnitten [Översikt](https://github.com/Azure/azure-iot-pcs-device-simulation#overview) och [komma igång](https://github.com/Azure/azure-iot-pcs-device-simulation#getting-started) .

1. Följ [distributions anvisningarna](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/deployment/README.md) i avsnittet komma igång.

   Det kan ta upp till 20 minuter att slutföra den här processen.

1. När distributionen är färdig anges du URL: en till simuleringen. Behåll sidan öppen eftersom du kommer tillbaka till den senare.

   >[!IMPORTANT]
   > Ange inte Solution Accelerator ännu! Behåll den här webb sidan öppen eftersom du kommer tillbaka till den senare.

   [![Lösnings etableringen för enhets simulering har slutförts.](media/tutorial-set-up-environment/iot-solution-accelerator-ready.png)](media/tutorial-set-up-environment/iot-solution-accelerator-ready.png#lightbox)

1. Granska nu de nyligen skapade resurserna i Azure Portal. På sidan **resurs grupper** ser du att en ny resurs grupp har skapats med hjälp av den `solutionName` du angav i din parameter fil för arm-mall. Anteckna de resurser som har skapats för enhets simuleringen.

   [![Resurser för enhets simulering.](media/tutorial-set-up-environment/device-sim-solution-resources.png)](media/tutorial-set-up-environment/device-sim-solution-resources.png#lightbox)

## <a name="create-an-azure-time-series-insights-gen2-environment"></a>Skapa en Azure Time Series Insights Gen2-miljö

I det här avsnittet beskrivs hur du skapar en Azure Time Series Insights Gen2-miljö och ansluter den till IoT Hub som skapats av IoT Solution Accelerator med hjälp av [Azure Portal](https://portal.azure.com/).

1. Logga in på [Azure Portal](https://portal.azure.com) med ditt Azure-prenumerations konto.
1. Välj **+ skapa en resurs** i det övre vänstra hörnet.
1. Välj kategorin **Sakernas Internet** och välj sedan **Time Series Insights**.

   [![Välj Time Series Insights miljö resurs.](media/tutorial-set-up-environment/create-new-environment.png)](media/tutorial-set-up-environment/create-new-environment.png#lightbox)

1. I fönstret **skapa Time Series Insights miljö** går du till fliken **grundläggande** och anger följande parametrar:

    | Parameter | Action |
    | --- | ---|
    | **Miljönamn** | Ange ett unikt namn för Azure Time Series Insights Gen2-miljön. |
    | **Prenumeration** | Ange den prenumeration där du vill skapa Azure Time Series Insights Gen2-miljön. Ett bra tips är att använda samma prenumeration som resten av IoT-resurserna som skapas av enhets simulatorn. |
    | **Resursgrupp** | Välj en befintlig resurs grupp eller skapa en ny resurs grupp för resursen Azure Time Series Insights Gen2-miljö. En resursgrupp är en container för Azure-resurser. Ett bra tips är att använda samma resurs grupp som andra IoT-resurser som skapas av enhets simulatorn. |
    | **Plats** | Välj en data Center region för din Azure Time Series Insights Gen2-miljö. För att undvika ytterligare svars tid är det bäst att skapa din Azure Time Series Insights Gen2-miljö i samma region som din IoT-hubb som skapats av enhets simulatorn. |
    | **Nivå** |  Välj **Gen2 (L1)**. Detta är SKU: n för den Azure Time Series Insights Gen2-produkten. |
    | **Egenskaps namn för Time Series ID** | Ange ett namn på en egenskap som innehåller värden som unikt identifierar tids serie instanserna. Det värde som du anger i rutan **egenskaps namn** som Time Series ID kan inte ändras senare. I den här självstudien anger du **_iothub-Connection-Device-ID_**. Om du vill veta mer om Time Series ID inklusive sammansatt Time Series ID, Läs [metod tips för att välja ett Time Series-ID](./how-to-select-tsid.md). |
    | **Namn på lagringskonto** | Ange ett globalt unikt namn för ett nytt lagrings konto.|
    | **Typ av lagrings konto** | Välj lagrings typ för ett nytt lagrings konto. Vi rekommenderar StorageV2|
    | **Replikering av lagrings konto** | Välj lagrings typ för ett nytt lagrings konto. Utifrån val av plats kan du välja mellan LRS, GRS och ZRS. I den här självstudien kan du välja LRS|
    | **Hierarkisk namnrymd** |Det här alternativet kan väljas när du har valt den lagrings typ som ska StorageV2. Som standard är den inaktive rad. I den här självstudien kan du lämna den i *inaktiverat* standard läge|
    |**Aktivera varmt Arkiv**|Välj **Ja** om du vill aktivera varmt arkiv. Den här inställningen kan inaktive ras och aktive ras igen efter att miljön har skapats. |
    |**Data lagring (i dagar)**|Välj standard alternativet 7 dagar. |

    [![Ny Azure Time Series Insights miljö konfiguration.](media/tutorial-set-up-environment/environment-configuration.png)](media/tutorial-set-up-environment/environment-configuration.png#lightbox)
    [![Ny Azure Time Series Insights miljö konfiguration, fortsatte.](media/tutorial-set-up-environment/environment-configuration2.png)](media/tutorial-set-up-environment/environment-configuration2.png#lightbox)

1. Välj **Nästa: händelse källa**.

   [![Konfigurera Time Series-ID för miljön.](media/tutorial-set-up-environment/time-series-id-selection.png)](media/tutorial-set-up-environment/time-series-id-selection.png#lightbox)

1. Ange följande parametrar på fliken **händelse källa** :

   | Parameter | Action |
   | --- | --- |
   | **Vill du skapa en händelse källa?** | Välj **Ja**.|
   | **Typ av källa** | Välj **IoT Hub**. |
   | **Namn** | Ange ett unikt värde för händelse källans namn. |
   | **Välj en hubb** | Välj **Välj befintlig**. |
   | **Prenumeration** | Välj den prenumeration som du använde för enhets simulatorn. |
   | **IoT Hub namn** | Välj det IoT Hub-namn som du skapade för enhets simulatorn. |
   | **Åtkomstprincip för IoT-hubb** | Välj **iothubowner**. |
   | **IoT Hub konsument grupp** | Välj **nytt**, ange ett unikt namn och välj sedan **+ Lägg till**. Konsument gruppen måste vara ett unikt värde i Azure Time Series Insights Gen2. |
   | **Egenskap för tidsstämpel** | Det här värdet används för att identifiera **timestamp** -egenskapen i dina inkommande telemetridata. I den här självstudien lämnar du rutan tom. Den här simulatorn använder den inkommande tidsstämpeln från IoT Hub, vilket Azure Time Series Insights Gen2 som standard. |

1. Välj **Granska + skapa**.

   [![Konfigurera den skapade IoT-hubben som en händelse källa.](media/tutorial-set-up-environment/configure-event-source.png)](media/tutorial-set-up-environment/configure-event-source.png#lightbox)

1. Välj **Granska + skapa**.

    [![Granska + skapa sida med knappen Skapa.](media/tutorial-set-up-environment/environment-confirmation.png)](media/tutorial-set-up-environment/environment-confirmation.png#lightbox)

    Du kan granska statusen för din distribution:

    [![Meddelande om att distributionen har slutförts.](media/tutorial-set-up-environment/deployment-notification.png)](media/tutorial-set-up-environment/deployment-notification.png#lightbox)

1. Expandera distributions information.

## <a name="stream-data"></a>Strömma data

Nu när du har distribuerat din Azure Time Series Insights Gen2-miljö börjar du strömma data för analys.

1. Du får en URL när du har slutfört distributionen av Solution Accelerator.

1. Klicka på URL: en för att starta enhets simuleringen.

1. Välj **+ ny simulering**.

    1. Ange de parametrar som krävs efter att installations sidan för **simulering** har lästs in.

        | Parameter | Action |
        | --- | --- |
        | **Namn** | Ange ett unikt namn för en simulator. |
        | **Beskrivning** | Ange en definition. |
        | **Simulerings varaktighet** | Ange till **Kör på obestämd tid**. |
        | **Enhetsmodell** | Klicka på + **Lägg till en enhets typ** <br />**Namn**: ange **hiss**. <br />**Belopp**: ange **3**. <br /> Lämna kvar standardvärdena |
        | **Mål-IoT Hub** | Ange till **Använd i förväg etablerad IoT Hub**. |

        [![Konfigurera parametrar och starta.](media/tutorial-set-up-environment/launch-solution-accelerator.png)](media/tutorial-set-up-environment/launch-solution-accelerator.png#lightbox)

    1. Välj **Starta simulering**. På instrument panelen för enhets simulering visas **aktiva enheter** och **Totalt antal meddelanden** .

        [![Instrument panel för Azure IoT-simulering.](media/tutorial-set-up-environment/see-active-devices-and-messages.png)](media/tutorial-set-up-environment/see-active-devices-and-messages.png#lightbox)

## <a name="analyze-data"></a>Analysera data

I det här avsnittet ska du utföra grundläggande analyser av dina Time Series-data med hjälp av [Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md).

1. Gå till Azure Time Series Insights Gen2 Explorer genom att välja URL: en från resurs sidan i [Azure Portal](https://portal.azure.com/).

    [![URL: en för Azure Time Series Insights Gen2 Explorer.](media/tutorial-set-up-environment/select-explorer-url.png)](media/tutorial-set-up-environment/select-explorer-url.png#lightbox)

1. I Azure Time Series Insights Gen2 Explorer visas ett fält som sträcker sig över skärmens överkant. Det här är din tillgänglighets väljare. Se till att du har valt minst 2 2 m och om det behövs expanderar du tids ramen genom att välja och dra väljarna handtag till vänster och höger.

1. **Time Series-instanser** visas på den vänstra sidan.

    [![Lista över överordnade instanser.](media/tutorial-set-up-environment/explorer-unparented-instances.png)](media/tutorial-set-up-environment/explorer-unparented-instances.png#lightbox)

1. Välj serie instansen för första gången. Välj sedan **Visa temperatur**.

    [![Den valda Time Series-instansen med meny kommando för att Visa medel temperatur.](media/tutorial-set-up-environment/select-instance-and-temperature.png)](media/tutorial-set-up-environment/select-instance-and-temperature.png#lightbox)

    Ett tids serie diagram visas. Ändra **intervallet** till **30 s**.

1. Upprepa föregående steg med de andra två tids serie instanserna så att du ser alla tre, som du ser i det här diagrammet:

    [![Diagram för alla tids serier.](media/tutorial-set-up-environment/explorer-add-three-instances.png)](media/tutorial-set-up-environment/explorer-add-three-instances.png#lightbox)

1. Välj tids periods väljaren i det övre högra hörnet. Här kan du välja särskilda start-och slut tider i millisekunder, eller välja bland förkonfigurerade alternativ, till exempel de **senaste 30 minuterna**. Du kan också ändra standard tids zonen.

    [![Ange tidsintervallet till de senaste 30 minuterna.](media/tutorial-set-up-environment/explorer-thirty-minute-time-range.png)](media/tutorial-set-up-environment/explorer-thirty-minute-time-range.png#lightbox)

    Solution Accelerator-förloppet under de **senaste 30 minuterna** visas nu i Azure Time Series Insights Gen2 Explorer.

## <a name="define-and-apply-a-model"></a>Definiera och tillämpa en modell

I det här avsnittet tillämpar du en modell för att strukturera data. För att slutföra modellen definierar du typer, hierarkier och instanser. Läs mer om data modellering i [tids serie modellen](./concepts-model-overview.md).

1. I Utforskaren väljer du fliken **modell** :

   [![Visa fliken modell i Utforskaren.](media/tutorial-set-up-environment/select-model-view.png)](media/tutorial-set-up-environment/select-model-view.png#lightbox)

   På fliken **typer** väljer du **+ Lägg till**.

1. Ange följande parametrar:

    | Parameter | Action |
    | --- | ---|
    | **Namn** | Ange **hiss** |
    | **Beskrivning** | Ange **Detta är en typ definition för hissen** |

1. Välj sedan fliken **variabler** .

    1. Välj **+ Lägg till variabel** och fyll i följande värden för den första variabeln i hiss typen. Du kommer att skriva tre variabler totalt.

        | Parameter | Action |
        | --- | --- |
        | **Namn** | Ange **Genomsnittlig temperatur**. |
        | **Variant** | Välj **numerisk** |
        | **Värde** | Välj från förval: Välj **temperatur (dubbel)**. <br /> Obs! det kan ta några minuter innan **värdet** fylls i automatiskt när Azure Time Series Insights Gen2 börjar ta emot händelser.|
        | **Sammansättningsåtgärd** | Expandera **Avancerade alternativ**. <br /> Välj **AVG**. |

    1. Välj **Använd**. Sedan, **+ Lägg till variabel** igen och ange följande värden:

        | Parameter | Action |
        | --- | --- |
        | **Namn** | Ange **genomsnittlig vibration**. |
        | **Variant** | Välj **numerisk** |
        | **Värde** | Välj från förval: Välj **vibrationer (dubbel)**. <br /> Obs! det kan ta några minuter innan **värdet** fylls i automatiskt när Azure Time Series Insights Gen2 börjar ta emot händelser.|
        | **Sammansättningsåtgärd** | Expandera **Avancerade alternativ**. <br /> Välj **AVG**. |

    1. Välj **Använd**. Sedan, **+ Lägg till variabel** igen och ange följande värden för den tredje och sista variabeln:

        | Parameter | Action |
        | --- | --- |
        | **Namn** | Ange **basyta**. |
        | **Variant** | Välj **kategoriska** |
        | **Värde** | Välj från förval: Välj **basyta (dubbel)**. <br /> Obs! det kan ta några minuter innan **värdet** fylls i automatiskt när Azure Time Series Insights Gen2 börjar ta emot händelser.|
        | **Kategorier** | <span style="text-decoration: underline">Etikett</span>   -  <span style="text-decoration: underline">Värden</span> <br /> Lägre: 1, 2, 3, 4 <br /> Mellan: 5, 6, 7, 8, 9 <br /> Versal: 10, 11, 12, 13, 14, 15 |
        | **Standard kategori** | Ange **okänd** |

        [![Lägg till typer av variabler.](media/tutorial-set-up-environment/add-type-variables.png)](media/tutorial-set-up-environment/add-type-variables.png#lightbox)

    1. Välj **Använd**.
    1. Välj **Spara**. Tre variabler skapas och visas.

        [![När du har lagt till typen granskar du den i vyn modell.](media/tutorial-set-up-environment/add-type-and-view.png)](media/tutorial-set-up-environment/add-type-and-view.png#lightbox)

1. Välj fliken **hierarkier** . Välj sedan **+ Lägg till**.

   1. I fönstret **Redigera hierarki** anger du följande parametrar:

        | Parameter | Action |
        | --- | ---|
        | **Namn** | Ange **Platshierarkin**. |
        |**Nivåer**| Ange **land** som namn på den första nivån<br />Välj **+ Lägg till nivå**<br />Ange **stad** för den andra nivån och välj sedan **+ Lägg till nivå**<br />Ange **build** som namn på den tredje och den slutgiltiga nivån |

   1. Välj **Spara**.

        [![Visa din nya hierarki i vyn modell.](media/tutorial-set-up-environment/add-hierarchy-and-view.png)](media/tutorial-set-up-environment/add-hierarchy-and-view.png#lightbox)

1. Navigera till **instanser**.

    1. Under **åtgärder** längst till höger, och välj Penn ikonen för att redigera den första instansen med följande värden:

        | Parameter | Action |
        | --- | --- |
        | **Typ** | Välj **hiss**. |
        | **Namn** | Ange **hiss 1**|
        | **Beskrivning** | Ange **instans för hiss 1** |

    1. Navigera till **instans fälten** och ange följande värden:

        | Parameter | Action |
        | --- | --- |
        | **Hierarkier** | Välj **platshierarki** |
        | **Land** | Ange **USA** |
        | **City** | Ange **Seattle** |
        | **Skapa** | Ange **områdes nål** |

    1. Välj **Spara**.

1. Upprepa föregående steg med de andra två instanserna medan du använder följande värden:

    **För hiss 2:**

    | Parameter | Action |
    | --- | --- |
    | **Typ** | Välj **hiss**. |
    | **Namn** | Ange **hiss 2**|
    | **Beskrivning** | Ange **instansen för hiss 2** |
    | **Hierarkier** | Välj **platshierarki** |
    | **Land** | Ange **USA** |
    | **City** | Ange **Seattle** |
    | **Skapa** | Ange **Pacific Science Center** |

    **För hiss 3:**

    | Parameter | Action |
    | --- | --- |
    | **Typ** | Välj **hiss**. |
    | **Namn** | Ange **hiss 3**|
    | **Beskrivning** | Ange **instansen för hiss 3** |
    | **Hierarkier** | Välj **platshierarki** |
    | **Land** | Ange **USA** |
    | **City** | Ange **New York** |
    | **Skapa** | Ange **Empire-tillstånds byggnad** |

    [![Visa de uppdaterade instanserna.](media/tutorial-set-up-environment/iot-solution-accelerator-instances.png)](media/tutorial-set-up-environment/iot-solution-accelerator-instances.png#lightbox)

1. Gå tillbaka till fliken **analysera** för att visa diagram fönstret. Under **platshierarki**, expandera alla hierarkinivåer för att Visa tids serie instanserna:

    [![Visa alla hierarkier i diagramvyn.](media/tutorial-set-up-environment/iot-solution-accelerator-view-hierarchies.png)](media/tutorial-set-up-environment/iot-solution-accelerator-view-hierarchies.png#lightbox)

1. Under **Pacific Science Center** väljer du tids serie instansen **hiss 2** och väljer sedan **Visa medel temperatur**.

1. För samma instans, **hiss 2**, väljer du **Visa basyta**.

    Med din kategoriska-variabel kan du avgöra hur lång tid en hiss ägnat åt övre, nedre och mittersta golv.

    [![Visualisera hiss 2 med hierarki och data.](media/tutorial-set-up-environment/iot-solution-accelerator-elevator-two.png)](media/tutorial-set-up-environment/iot-solution-accelerator-elevator-two.png#lightbox)

## <a name="clean-up-resources"></a>Rensa resurser

Nu när du har slutfört självstudien rensar du de resurser som du har skapat:

1. Välj **alla resurser** på den vänstra menyn i [Azure Portal](https://portal.azure.com), leta upp resurs gruppen Azure Time Series Insights Gen2.
1. Ta antingen bort hela resurs gruppen (och alla resurser som ingår i den) genom att välja **ta bort** eller ta bort varje resurs individuellt.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

* Skapa och kör acceleratorn för enhetssimulering.
* Skapa en Azure Time Series Insights Gen2 PAYG-miljö.
* Anslut Azure Time Series Insights Gen2-miljön till en IoT-hubb.
* Kör ett Solution Accelerator-exempel för att strömma data till Azure Time Series Insights Gen2-miljön.
* Utföra en grundläggande analys av data.
* Definiera en typ av och hierarki för tidsseriemodell och associera dem med dina instanser.

Nu när du vet hur du skapar en egen Azure Time Series Insights Gen2-miljö kan du läsa mer om viktiga begrepp i Azure Time Series Insights Gen2.

Läs om Azure Time Series Insights Gen2-inmatning:

> [!div class="nextstepaction"]
> [Översikt över Azure Time Series Insights Gen2 data inmatning](./concepts-ingestion-overview.md)

Läs mer om Azure Time Series Insights Gen2-lagring:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Gen2 data Storage](./concepts-storage.md)

Läs mer om Time Series-modeller:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Gen2 Data Modeling](./concepts-model-overview.md)

Lär dig mer om hur du ansluter din miljö till Power BI:

> [!div class="nextstepaction"]
> [Visualisera data från Azure Time Series Insights Gen2 i Power BI](./how-to-connect-power-bi.md)
