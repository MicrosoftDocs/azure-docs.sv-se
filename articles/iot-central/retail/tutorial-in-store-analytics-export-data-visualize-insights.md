---
title: Självstudie – exportera data och visualisera insikter i Azure IoT Central
description: I den här självstudien får du lära dig hur du exporterar data från IoT Central och visualiserar insikter på en Power BI instrument panel.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: dobett
author: dominicbetts
ms.date: 11/12/2019
ms.openlocfilehash: 2695b34745ff02d55f18cebbe87a468f807ca77a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831696"
---
# <a name="tutorial-export-data-from-azure-iot-central-and-visualize-insights-in-power-bi"></a>Självstudie: exportera data från Azure IoT Central och visualisera insikter i Power BI


I de två föregående självstudierna har du skapat och anpassat ett IoT Central-program med hjälp av programmallen **för BA-utcheckning i butiken** . I den här självstudien konfigurerar du IoT Central-programmet för att exportera telemetri som samlats in från enheterna. Du använder sedan Power BI för att skapa en anpassad instrument panel för Store Manager för att visualisera de insikter som härletts från Telemetrin.

I den här självstudien får du lära dig hur man:
> [!div class="checklist"]
> * Konfigurera ett IoT Central program för att exportera telemetri till en Event Hub.
> * Använd Logic Apps för att skicka data från en händelsehubben till en Power BI strömmande data uppsättning.
> * Skapa en instrument panel för Power BI för att visualisera data i den strömmande data uppsättningen.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* För att slutföra de två föregående självstudierna [skapar du ett in-Store Analytics-program i Azure IoT Central](./tutorial-in-store-analytics-create-app.md) och [anpassar instrument panelen för operatören och hanterar enheter i Azure IoT Central](./tutorial-in-store-analytics-customize-dashboard.md).
* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
* Ett Power BI-konto. Om du inte har ett Power BI konto kan du registrera dig för en [kostnads fri Power BI Pro utvärdering](https://app.powerbi.com/signupredirect?pbi_source=web) innan du börjar.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Innan du skapar din Event Hub-och Logic-app måste du skapa en resurs grupp för att hantera dem. Resurs gruppen måste vara på samma plats som din IoT Central program **för analys i butiken** . Så här skapar du en resursgrupp:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **resurs grupper** i det vänstra navigerings fältet. Välj **Lägg till**.
1. För **prenumeration** väljer du namnet på den Azure-prenumeration som du använde för att skapa ditt IoT Central-program.
1. För **resurs gruppens** namn, anger du _Retail-Store-Analysis_*.
1. För **regionen** väljer du samma region som du valde för IoT Central programmet.
1. Välj **Granska + skapa**.
1. Välj **Granska** i fönstret **Granska + Skapa**.

Nu har du en resurs grupp med namnet **Retail-Store-Analysis** i din prenumeration.

## <a name="create-an-event-hub"></a>Skapa en händelsehubb

Innan du kan konfigurera programmet för detalj handels övervakning för att exportera telemetri måste du skapa en händelsehubben för att ta emot exporterade data. Följande steg visar hur du skapar händelsehubben:

1. I Azure Portal väljer du **skapa en resurs** längst upp till vänster på skärmen.
1. I **Sök på Marketplace**, ange _Event Hubs_ och tryck sedan på **RETUR**.
1. På sidan **Event Hubs** väljer du **skapa**.
1. Utför följande steg på sidan **skapa namn område** :
    * Ange ett unikt namn för namn området, till exempel _dittnamn-Retail-Store-Analysis_. Systemet kontrollerar om det här namnet är tillgängligt.
    * Välj den **grundläggande** pris nivån.
    * Välj samma **prenumeration** som du använde för att skapa ditt IoT Central-program.
    * Välj resurs gruppen **Retail-Store-Analysis** .
    * Välj samma plats som du använde för IoT Central programmet.
    * Välj **Skapa**. Du kan behöva vänta några minuter på att systemet ska etablera resurserna.
1. I portalen navigerar du till resurs gruppen **detalj handels lager-analys** . Vänta tills distributionen har slutförts. Du kan behöva välja **Uppdatera** för att uppdatera distributions statusen. Du kan också kontrol lera status för skapande av Event Hub-namnrymd i **meddelandena**.
1. I resurs gruppen **Retail-Store-Analysis** väljer du **Event Hubs namn området**. Du ser start sidan för din **Event Hubs namn område** i portalen.

Nu har du ett **Event Hubs namn område** som du kan använda för att skapa en **händelsehubben** som ska användas med ditt IoT Central-program:

1. På Start sidan för din **Event Hubs namn område** i portalen väljer du **+ Event Hub**.
1. På sidan **skapa händelsehubben** anger du _Store-telemetri_ som namn och väljer sedan **skapa**.

Nu har du en Event Hub som du kan använda när du konfigurerar data export från IoT Central-programmet:

![Händelsehubb](./media/tutorial-in-store-analytics-visualize-insights/event-hub.png)

## <a name="configure-data-export"></a>Konfigurera data export

Nu har du en Event Hub, du kan konfigurera ditt program för **analys i butiken** för att exportera telemetri från de anslutna enheterna. Följande steg visar hur du konfigurerar exporten:

1. Logga in på ditt IoT Central program **för analys i butiken** .
1. Välj **data export** i det vänstra fönstret.
1. Välj **ny > Azure Event Hubs**.
1. Ange _telemetri-export_ som **visnings namn**.
1. Välj din **Event Hubs namn område**.
1. Välj händelsehubben för **Store-telemetri** .
1. Stäng av **enheter** och **mallar** för enheter i avsnittet **data att exportera** .
1. Välj **Spara**.

Det kan ta några minuter innan data exporten börjar skicka telemetri till Event Hub. Du kan se status för exporten på sidan **data export** :

![Konfiguration av kontinuerlig data export](./media/tutorial-in-store-analytics-visualize-insights/export-configuration.png)

## <a name="create-the-power-bi-datasets"></a>Skapa Power BI data uppsättningar

Din Power BI-instrumentpanel kommer att visa data från ditt program för detalj handels övervakning. I den här lösningen använder du Power BI strömmande data uppsättningar som data källa för Power BI instrument panelen. I det här avsnittet definierar du schemat för strömmande data uppsättningar så att Logic-appen kan vidarebefordra data från händelsehubben. Följande steg visar hur du skapar två strömmande data uppsättningar för miljö sensorer och en strömmande data uppsättning för beläggnings sensorn:

1. Logga in på ditt **Power BI**-konto.
1. Välj **arbets ytor** och välj sedan **skapa en arbets yta**.
1. På sidan **skapa en arbets yta** anger du _in-Store Analytics-check_ som **arbets ytans namn**.
1. Rulla längst ned i **Välkommen till sidan arbets yta för Analytics-betalningsarbets ytan** och välj **hoppa över**.
1. På sidan arbets yta väljer du **skapa > strömmande data uppsättning**.
1. Välj **API** på sidan **ny strömmande data uppsättning** och välj sedan **Nästa**.
1. Ange _zon 1 sensor_ som **data uppsättnings namn**.
1. Ange de tre **värdena från Stream** i följande tabell:

    | Värdenamn  | Värdetyp |
    | ----------- | ---------- |
    | Timestamp   | DateTime   |
    | Luftfuktighet    | Antal     |
    | Temperatur | Antal     |

1. Växla **historiska data analyser** på.
1. Välj **skapa** och sedan **Slutför**.
1. Skapa en annan strömmande data uppsättning som kallas **zon 2 sensor** med samma schema och inställningar som **zon 1 sensor** strömmande data uppsättning.

Nu har du två strömmande data uppsättningar. Logic app dirigerar telemetri från de två miljö sensorer som är anslutna till ditt program för **Analytics-utcheckning** till dessa två data uppsättningar:

![Zon data uppsättningar](./media/tutorial-in-store-analytics-visualize-insights/dataset-1.png)

Den här lösningen använder en strömmande data uppsättning för varje sensor eftersom det inte går att tillämpa filter för att strömma data i Power BI.

Du behöver också en strömmande data uppsättning för telemetri:

1. På sidan arbets yta väljer du **skapa > strömmande data uppsättning**.
1. Välj **API** på sidan **ny strömmande data uppsättning** och välj sedan **Nästa**.
1. Ange _besittnings sensor_ som **data uppsättnings namn**.
1. Ange de fem **värdena från Stream** i följande tabell:

    | Värdenamn     | Värdetyp |
    | -------------- | ---------- |
    | Timestamp      | DateTime   |
    | Kölängd 1 | Antal     |
    | Kölängd 2 | Antal     |
    | Uppehålls tid 1   | Antal     |
    | Bostadens tid 2   | Antal     |

1. Växla **historiska data analyser** på.
1. Välj **skapa** och sedan **Slutför**.

Nu har du en tredje strömmande data uppsättning som lagrar värden från den simulerade beläggnings sensorn. Den här sensorn rapporterar köns längd vid de två utcheckningarna i butiken och hur länge kunderna väntar i följande köer:

![Användnings data uppsättning](./media/tutorial-in-store-analytics-visualize-insights/dataset-2.png)

## <a name="create-a-logic-app"></a>Skapa en logikapp

I den här lösningen läser Logic-appen telemetri från händelsehubben, tolkar data och skickar dem sedan till de Power BI strömmande data uppsättningar som du har skapat.

Innan du skapar Logic-appen behöver du enhets-ID: n för de två RuuviTag-sensorer som du har anslutit till ditt IoT Central-program i självstudien [skapa ett internt analys program i Azure IoT Central](./tutorial-in-store-analytics-create-app.md) :

1. Logga in på ditt IoT Central program **för analys i butiken** .
1. Välj **enheter** i den vänstra rutan. Välj sedan **RuuviTag**.
1. Anteckna **enhets-ID: n**. I följande skärm bild är ID: na **f5dcf4ac32e8** och **e29ffc8d5326**:

    ![Enhets-ID](./media/tutorial-in-store-analytics-visualize-insights/device-ids.png)

Följande steg visar hur du skapar Logic-appen i Azure Portal:

1. Logga in på [Azure Portal](https://portal.azure.com) och välj **skapa en resurs** längst upp till vänster på skärmen.
1. I **Sök på Marketplace** _, ange Logi Kap par och tryck_ sedan på **RETUR**.
1. På sidan **Logic app** väljer du **skapa**.
1. På sidan för att skapa **Logic app** :
    * Ange ett unikt namn för din Logic app, till exempel _dittnamn-butik-analys_.
    * Välj samma **prenumeration** som du använde för att skapa ditt IoT Central-program.
    * Välj resurs gruppen **Retail-Store-Analysis** .
    * Välj samma plats som du använde för IoT Central programmet.
    * Välj **Skapa**. Du kan behöva vänta några minuter på att systemet ska etablera resurserna.
1. I Azure Portal navigerar du till din nya Logic-app.
1. På sidan **Logic Apps designer** rullar du ned och väljer **Tom Logic app**.
1. I **Sök anslutningar och utlösare** anger du _Event Hubs_.
1. I **utlösare** väljer du **när händelser är tillgängliga i Event Hub**.
1. Ange _Spara telemetri_ som **anslutnings namn** och välj **Event Hubs namn området**.
1. Välj **RootManageSharedAccess** -principen och välj **skapa**.
1. I **när händelser är tillgängliga i Event Hub** -åtgärden:
    * I **Event Hub-namn** väljer du **Store-telemetri**.
    * I **innehålls typ** väljer du **program/JSON**.
    * Ange **intervallet** till tre och **frekvensen** till sekunder
1. Välj **Spara**, så sparas logikappen.

Om du vill lägga till logiken i din Logic app-design väljer du **kodvyn**:

1. Ersätt `"actions": {},` med följande JSON. Ersätt de två plats hållarna `[YOUR RUUVITAG DEVICE ID 1]` och `[YOUR RUUVITAG DEVICE ID 2]` med de ID: n som du antecknade för dina två RuuviTag-enheter:

    ```json
    "actions": {
        "Initialize_Device_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "DeviceID",
                        "type": "String"
                    }
                ]
            },
            "runAfter": {},
            "type": "InitializeVariable"
        },
        "Initialize_Interface_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "InterfaceID",
                        "type": "String",
                        "value": "Other"
                    }
                ]
            },
            "runAfter": {
                "Initialize_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "InitializeVariable"
        },
        "Parse_Properties": {
            "inputs": {
                "content": "@triggerBody()?['Properties']",
                "schema": {
                    "properties": {
                        "iothub-connection-auth-generation-id": {
                            "type": "string"
                        },
                        "iothub-connection-auth-method": {
                            "type": "string"
                        },
                        "iothub-connection-device-id": {
                            "type": "string"
                        },
                        "iothub-enqueuedtime": {
                            "type": "string"
                        },
                        "iothub-interface-name": {
                            "type": "string"
                        },
                        "iothub-message-source": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "integer"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Parse_Telemetry": {
            "inputs": {
                "content": "@triggerBody()?['ContentData']",
                "schema": {
                    "properties": {
                        "DwellTime1": {
                            "type": "number"
                        },
                        "DwellTime2": {
                            "type": "number"
                        },
                        "count1": {
                            "type": "number"
                        },
                        "count2": {
                            "type": "number"
                        },
                        "humidity": {
                            "type": "number"
                        },
                        "temperature": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Set_Device_ID_variable": {
            "inputs": {
                "name": "DeviceID",
                "value": "@body('Parse_Properties')?['iothub-connection-device-id']"
            },
            "runAfter": {
                "Parse_Properties": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Set_Interface_ID_variable": {
            "inputs": {
                "name": "InterfaceID",
                "value": "@body('Parse_Properties')?['iothub-interface-name']"
            },
            "runAfter": {
                "Set_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Switch_by_DeviceID": {
            "cases": {
                "Occupancy": {
                    "actions": {
                        "Switch_by_InterfaceID": {
                            "cases": {
                                "Dwell_Time_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_v2_1l0"
                                },
                                "People_Count_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_iv"
                                }
                            },
                            "default": {
                                "actions": {}
                            },
                            "expression": "@variables('InterfaceID')",
                            "runAfter": {},
                            "type": "Switch"
                        }
                    },
                    "case": "Occupancy"
                },
                "Zone 2 environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 2]"
                },
                "Zone_1_environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 1]"
                }
            },
            "default": {
                "actions": {}
            },
            "expression": "@variables('DeviceID')",
            "runAfter": {
                "Parse_Telemetry": [
                    "Succeeded"
                ],
                "Set_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "Switch"
        }
    },
    ```

1. Välj **Spara** och välj sedan **Designer** för att se den visuella versionen av logiken som du har lagt till:

    ![Logic app-design](./media/tutorial-in-store-analytics-visualize-insights/logic-app.png)

1. Expandera åtgärden genom att välja **Växla efter DeviceID** . Välj **zon 1 miljö** och välj sedan **Lägg till en åtgärd**.
1. I **Sök anslutningar och åtgärder** anger du **Power BI** och trycker sedan på **RETUR**.
1. Välj åtgärden **Lägg till rader i en data mängd (förhands granskning)** .
1. Välj **Logga** in och följ anvisningarna för att logga in på ditt Power BI-konto.
1. När inloggnings processen är klar går **du till åtgärden Lägg till rader till en data uppsättning** :
    * Välj **i butiken Analytics-utcheckning** som arbets yta.
    * Välj **zon 1 sensor** som data uppsättning.
    * Välj **RealTimeData** som tabell.
    * Välj **Lägg till ny parameter** och välj sedan fälten **tidsstämpel**, **fuktighet** och **temperatur** .
    * Välj fältet **tidsstämpelfält** och välj sedan **x-opt-enqueuedtime** från listan med **dynamiskt innehåll** .
    * Välj fältet **fuktighet** och välj sedan **Visa mer** bredvid **parsa telemetri**. Välj sedan **fuktighet**.
    * Välj fältet **temperatur** och välj sedan **Visa mer** bredvid **parsa telemetri**. Välj sedan **temperatur**.
    * Välj **Spara** för att spara ändringarna. **Zon 1 miljö** åtgärden ser ut som följande skärm bild: ![ zon 1 miljö](./media/tutorial-in-store-analytics-visualize-insights/zone-1-action.png)
1. Välj åtgärden **zon 2 miljö** och välj sedan **Lägg till en åtgärd**.
1. I **Sök anslutningar och åtgärder** anger du **Power BI** och trycker sedan på **RETUR**.
1. Välj åtgärden **Lägg till rader i en data mängd (förhands granskning)** .
1. I åtgärden **Lägg till rader i en data uppsättning 2** :
    * Välj **i butiken Analytics-utcheckning** som arbets yta.
    * Välj **zon 2 sensor** som data uppsättning.
    * Välj **RealTimeData** som tabell.
    * Välj **Lägg till ny parameter** och välj sedan fälten **tidsstämpel**, **fuktighet** och **temperatur** .
    * Välj fältet **tidsstämpelfält** och välj sedan **x-opt-enqueuedtime** från listan med **dynamiskt innehåll** .
    * Välj fältet **fuktighet** och välj sedan **Visa mer** bredvid **parsa telemetri**. Välj sedan **fuktighet**.
    * Välj fältet **temperatur** och välj sedan **Visa mer** bredvid **parsa telemetri**. Välj sedan **temperatur**.
    Välj **Spara** för att spara ändringarna.  **Zon 2 miljö** åtgärden ser ut som följande skärm bild: ![ zon 2 miljö](./media/tutorial-in-store-analytics-visualize-insights/zone-2-action.png)
1. Välj åtgärden **för att använda** och välj sedan åtgärden **Växla efter gränssnitts-ID** .
1. Välj åtgärden **uppehålls tids gränssnitt** och välj sedan **Lägg till en åtgärd**.
1. I **Sök anslutningar och åtgärder** anger du **Power BI** och trycker sedan på **RETUR**.
1. Välj åtgärden **Lägg till rader i en data mängd (förhands granskning)** .
1. I åtgärden **Lägg till rader till en data uppsättning** :
    * Välj **i butiken Analytics-utcheckning** som arbets yta.
    * Välj **beläggnings sensor** som data uppsättning.
    * Välj **RealTimeData** som tabell.
    * Välj **Lägg till ny parameter** och sedan fälten **tidsstämpel**, **bostadens tid 1** och **bostadens tid 2** .
    * Välj fältet **tidsstämpelfält** och välj sedan **x-opt-enqueuedtime** från listan med **dynamiskt innehåll** .
    * Välj fältet **bostads tid 1** och välj sedan **Visa mer** bredvid **parsa telemetri**. Välj sedan **DwellTime1**.
    * Välj fältet **bostads tid 2** och välj sedan **Visa mer** bredvid **parsa telemetri**. Välj sedan **DwellTime2**.
    * Välj **Spara** för att spara ändringarna. **Tids gränssnitts åtgärden för bostaden** ser ut som följande skärm bild: ![ skärm bild som visar åtgärden "bostads tids gränssnitt".](./media/tutorial-in-store-analytics-visualize-insights/occupancy-action-1.png)
1. Välj åtgärds åtgärden **antal personer** och välj **Lägg till en åtgärd**.
1. I **Sök anslutningar och åtgärder** anger du **Power BI** och trycker sedan på **RETUR**.
1. Välj åtgärden **Lägg till rader i en data mängd (förhands granskning)** .
1. I åtgärden **Lägg till rader till en data uppsättning** :
    * Välj **i butiken Analytics-utcheckning** som arbets yta.
    * Välj **beläggnings sensor** som data uppsättning.
    * Välj **RealTimeData** som tabell.
    * Välj **Lägg till ny parameter** och välj sedan fälten **tidstämpel**, **Kölängd 1** och **Kölängd 2** .
    * Välj fältet **tidsstämpelfält** och välj sedan **x-opt-enqueuedtime** från listan med **dynamiskt innehåll** .
    * Välj fältet **Kölängd 1** och välj sedan **Visa mer** bredvid **parsa telemetri**. Välj sedan **count1**.
    * Välj fältet **Kölängd 2** och välj sedan **Visa mer** bredvid **parsa telemetri**. Välj sedan **count2**.
    * Välj **Spara** för att spara ändringarna. Åtgärds åtgärden **antal människor** ser ut som följande skärm bild: användnings ![ åtgärd](./media/tutorial-in-store-analytics-visualize-insights/occupancy-action-2.png)

Logic App körs automatiskt. Om du vill se status för varje körning går du till **översikts** sidan för Logic app i Azure Portal:

## <a name="create-a-power-bi-dashboard"></a>Skapa en Power BI-instrumentpanel

Nu har du telemetri som flödar från ditt IoT Central-program via händelsehubben. Sedan parsar din Logic-app Event Hub-meddelanden och lägger till dem i en Power BI strömmande data uppsättning. Nu kan du skapa en Power BI instrument panel för att visualisera Telemetrin:

1. Logga in på ditt **Power BI**-konto.
1. Välj **arbets ytor > i butiken Analytics-utcheckning**.
1. Välj **skapa > instrument panel**.
1. Ange **Store Analytics** som instrument panels namn och välj **skapa**.

### <a name="add-line-charts"></a>Lägga till linje diagram

Lägg till fyra linje diagram paneler för att Visa temperaturen och fukten från de två miljö sensorerna. Använd informationen i följande tabell för att skapa panelerna. Om du vill lägga till varje panel börjar du med att välja **... (Fler alternativ) > Lägg till panel**. Välj **anpassade strömmande data** och välj sedan **Nästa**:

| Inställning | Diagram #1 | Diagram #2 | Diagram #3 | Diagram #4 |
| ------- | -------- | -------- | -------- | -------- |
| Datamängd | Zon 1 sensor | Zon 1 sensor | Zon 2 sensor | Zon 2 sensor |
| Typ av visualisering | Linjediagram | Linjediagram | Linjediagram | Linjediagram |
| Axel | Timestamp | Timestamp | Timestamp | Timestamp |
| Värden | Temperatur | Luftfuktighet | Temperatur | Luftfuktighet |
| Tids period | 60 minuter | 60 minuter | 60 minuter | 60 minuter |
| Rubrik | Temperatur (1 timme) | Fuktighet (1 timme) | Temperatur (1 timme) | Fuktighet (1 timme) |
| Underrubrik | Zon 1 | Zon 1 | Zon 2 | Zon 2 |

Följande skärm bild visar inställningarna för det första diagrammet:

![Inställningar för linjediagram](./media/tutorial-in-store-analytics-visualize-insights/line-chart.png)

### <a name="add-cards-to-show-environmental-data"></a>Lägg till kort för att Visa miljö data

Lägg till fyra kort paneler för att visa de senaste temperatur-och fuktighets värdena från de två miljö sensorerna. Använd informationen i följande tabell för att skapa panelerna. Om du vill lägga till varje panel börjar du med att välja **... (Fler alternativ) > Lägg till panel**. Välj **anpassade strömmande data** och välj sedan **Nästa**:

| Inställning | Kort #1 | Kort #2 | Kort #3 | Kort #4 |
| ------- | ------- | ------- | ------- | ------- |
| Datamängd | Zon 1 sensor | Zon 1 sensor | Zon 2 sensor | Zon 2 sensor |
| Typ av visualisering | Kort | Kort | Kort | Kort |
| Fält | Temperatur | Luftfuktighet | Temperatur | Luftfuktighet |
| Rubrik | Temperatur (F) | Fuktighet (%) | Temperatur (F) | Fuktighet (%) |
| Underrubrik | Zon 1 | Zon 1 | Zon 2 | Zon 2 |

Följande skärm bild visar inställningarna för det första kortet:

![Kort inställningar](./media/tutorial-in-store-analytics-visualize-insights/card-settings.png)

### <a name="add-tiles-to-show-checkout-occupancy-data"></a>Lägg till paneler för att Visa indata från utcheckning

Lägg till fyra kort paneler för att Visa Kölängd och bostads tid för de två utcheckningarna i butiken. Använd informationen i följande tabell för att skapa panelerna. Om du vill lägga till varje panel börjar du med att välja **... (Fler alternativ) > Lägg till panel**. Välj **anpassade strömmande data** och välj sedan **Nästa**:

| Inställning | Kort #1 | Kort #2 | Kort #3 | Kort #4 |
| ------- | ------- | ------- | ------- | ------- |
| Datamängd | Beläggnings sensor | Beläggnings sensor | Beläggnings sensor | Beläggnings sensor |
| Typ av visualisering | Grupperat stående stapeldiagram | Grupperat stående stapeldiagram | Mätare | Mätare |
| Axel    | Timestamp | Timestamp | Saknas | Saknas |
| Värde | Uppehålls tid 1 | Bostadens tid 2 | Kölängd 1 | Kölängd 2 |
| Tids period | 60 minuter | 60 minuter |  Saknas | Saknas |
| Rubrik | Bostads tid | Bostads tid | Kölängd | Kölängd |
| Underrubrik | Utcheckning 1 | Utcheckning 2 | Utcheckning 1 | Utcheckning 2 |

Ändra storlek på och ordna om panelerna på instrument panelen för att se ut som på följande skärm bild:

![Skärm bild som visar Power B I-instrumentpanelen med storleks ändring och omarrangerade paneler.](./media/tutorial-in-store-analytics-visualize-insights/pbi-dashboard.png)

Du kan lägga till några extra grafik resurser för att ytterligare anpassa instrument panelen:

![Power BI-instrumentpanel](./media/tutorial-in-store-analytics-visualize-insights/pbi-dashboard-graphics.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du är klar med ditt IoT Central-program kan du ta bort det genom att logga in på programmet och navigera till **program inställnings** sidan i avsnittet **Administration** .

Om du vill behålla programmet men minska kostnaderna som är kopplade till den, inaktiverar du den data export som skickar telemetri till Event Hub.

Du kan ta bort Event Hub-och Logic-appen i Azure Portal genom att ta bort resurs gruppen med namnet **Retail-Store-Analysis**.

Du kan ta bort dina Power BI data uppsättningar och instrument paneler genom att ta bort arbets ytan från sidan Power BI inställningar för arbets ytan.

## <a name="next-steps"></a>Nästa steg

De här tre självstudierna visar en komplett lösning som använder program mal len **för analys av IoT Central i butiken** . Du har anslutit enheter till programmet, använt IoT Central för att övervaka enheterna och använt Power BI för att bygga en instrument panel för att Visa insikter från enhetens telemetri. Ett rekommenderat nästa steg är att utforska en av de andra IoT Central Programmallarna:

> [!div class="nextstepaction"]
> [Skapa energilösningar med IoT Central](../energy/overview-iot-central-energy.md)
