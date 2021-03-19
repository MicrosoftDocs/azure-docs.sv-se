---
title: Kör Azure Stream Analytics på Azure Stack
description: Skapa ett Azure Stream Analytics Edge-jobb och distribuera det till Azure Stack Hub via IoT Edge Runtime.
ms.service: stream-analytics
author: an-emma
ms.author: raan
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: 315b2efa042721a19fa779794c4921f9ced1fc83
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589811"
---
# <a name="run-azure-stream-analytics-on-azure-stack"></a>Kör Azure Stream Analytics på Azure Stack 

Du kan köra Azure Stream Analytics på Azure Stack Hub som en IoT Edge modul. Konfigurationer har lagts till i IoT Edge-modulen som gör det möjligt att interagera med Blob Storage, Event Hubs och IoT-hubbar som körs i en prenumeration på en Azure Stack hubb genom att tillåta anpassade webb adresser som finns i varje Azure Stack Hub-anställning.

Med Stream Analytics på Azure Stack kan du bygga verkligt hybrid arkitekturer för strömnings bearbetning i ditt eget privata, autonoma moln – som kan anslutas eller frånkopplas med molnbaserade appar med hjälp av enhetliga Azure-tjänster lokalt. 

Den här artikeln visar hur du strömma data från IoT Hub eller Händelsehubben till en annan Event Hub eller Blob Storage i en prenumeration på en Azure Stack hubb och bearbetar den med Stream Analytics.

## <a name="set-up-environments"></a>Konfigurera miljöer

Azure Stream Analytics är en hybrid tjänst på Azure Stack Hub. Det är en IoT Edge modul som är konfigurerad i Azure men som kan köras på Azure Stack Hub.  

Om du inte har använt Azure Stack hubb eller IoT Edge kan du följa anvisningarna nedan för att konfigurera miljöer.

### <a name="prepare-the-azure-stack-hub-environment"></a>Förbereda Azure Stack Hub-miljön

Skapa en Azure Stack hubb-prenumeration. Mer information finns i [självstudierna för att skapa en Azure Stack hubb-prenumeration.](/azure-stack/user/azure-stack-subscribe-services/)

Om du vill utvärdera Azure Stack hubb på din server kan du använda Azure Stack Development Kit (ASDK).  Mer information om ASDK finns i [Översikt över ASDK](/azure-stack/asdk/).

### <a name="install-the-iot-edge-runtime"></a>Installera IoT Edge-körningen

Om du vill köra Azure Stream Analytics på Azure Stack Hub måste enheten ha IoT Edge-körningsmiljön och måste ha nätverks anslutning till Azure Stack hubben eller vara en virtuell dator som körs på Azure Stack Hub. Med IoT Edge runtime kan Stream Analytics Edge-jobb integreras med Azure Storage och Azure Event Hubs som körs på ditt Azure Stack Hub. Mer information finns i [Azure Stream Analytics på IoT Edge](stream-analytics-edge.md) 

Förutom att ha nätverks åtkomst till Azure Stack Hub-resurserna behöver IoT Edges enheten (eller den virtuella datorn) åtkomst till Azure-IoT Hub i det offentliga Azure-molnet för att konfigurera Stream Analytics-modulen. 

Följande guider visar hur du konfigurerar IoT Edge runtime på enheten eller den virtuella datorn:

* [Installera Azure IoT Edge-körningen i Windows](../iot-edge/how-to-install-iot-edge.md)
* [Installera Azure IoT Edge-körningen på Debian-baserade Linux-system](../iot-edge/how-to-install-iot-edge.md)


## <a name="create-an-azure-stream-analytics-edge-job"></a>Skapa ett Azure Stream Analytics Edge-jobb

ASA Edge-jobb körs i behållare som distribuerats till Azure IoT Edge enheter. De består av två delar:
* En moln del som är ansvarig för jobb definition: användare definierar indata, utdata, fråga och andra inställningar (i händelse av ordnings händelser osv.) i molnet.
* En modul som körs på dina IoT-enheter. Den innehåller ASA-motorn och tar emot jobb definitionen från molnet.

### <a name="create-a-storage-account"></a>Skapa ett lagringskonto

När du skapar ett Azure Stream Analytics-jobb som ska köras på en IoT Edge-enhet måste det lagras på ett sätt som kan anropas från enheten. Du kan använda ett befintligt Azure Storage-konto eller skapa ett nytt.
1. I Azure Portal går du till **skapa en resurs > lagring > lagrings konto – BLOB, fil, tabell, kö**.
2. Skapa lagringskontot genom att ange följande värden:

   | Fält | Värde |
   | --- | --- |
   | Namn | Ange ett unikt namn för lagringskontot. |
   | Location | Välj en plats i närheten av dig.|
   | Prenumeration | Välj samma prenumeration som din IoT-hubb.|
   | Resursgrupp | Vi rekommenderar att du använder samma resurs grupp för alla test resurser som du skapar under [IoT Edge snabb starter](../iot-edge/quickstart.md) och självstudier. Till exempel **IoTEdgeResources**. |

3. Behåll standardvärdena för de andra fälten och välj **Skapa**.


### <a name="create-a-new-job"></a>Skapa ett nytt jobb

1. I Azure Portal går du till **skapa en resurs > Sakernas Internet > Stream Analytics jobb**.
2. Skapa lagringskontot genom att ange följande värden:

   | Fält | Värde |
   | --- | --- |
   | Jobbnamn | Ange ett namn för ditt jobb. Till exempel **IoTEdgeJob** |
   | Prenumeration | Välj samma prenumeration som din IoT-hubb.|
   | Resursgrupp | Vi rekommenderar att du använder samma resurs grupp för alla test resurser som du skapar under [IoT Edge snabb starter](../iot-edge/quickstart.md) och självstudier. Till exempel **IoTEdgeResources**. |
   | Location | Välj en plats i närheten av dig. |
   | Värd miljö | Välj **Gräns**. |

3. Välj **Skapa**.

### <a name="configure-your-job"></a>Konfigurera jobbet

När Stream Analytics-jobbet har skapats på Azure Portal kan du konfigurera det med indata, utdata och en fråga som ska köras mot de data som skickas. Du kan manuellt ange indata från en IoT Hub eller en händelsehubben i en Azure Stack Hub-prenumeration.

1. Gå till Stream Analytics-jobbet på Azure Portal.
2. Under **Konfigurera** väljer du **Inställningar för lagrings konto** och väljer det lagrings konto som du skapade i föregående steg.
   > [!div class="mx-imgBorder"]
   > [![Inställning ](media/on-azure-stack/storage-account-settings.png) för jobb lagrings konto](media/on-azure-stack/storage-account-settings.png#lightbox)
3. Under **jobb sto pol Ogin** **väljer du indata och** lägger sedan **till Stream-indata.**
4. Välj **IoT Hub**, **Event Hub** eller **Edge Hub (hubb** ) i list rutan. 
5. Om indata är en händelsehubben eller IoT Hub i en Azure Stack Hub-prenumeration anger du informationen manuellt enligt nedan.

   #### <a name="event-hub"></a>Händelsehubb

   | Fält | Värde |
   | --- | --- |
   | Inmatat alias | Ett eget namn som du använder i jobbets fråga för att referera till den här indatamängden. |
   | Service Bus namnrymd | Namn området är en behållare för en uppsättning meddelande enheter. När du skapar en ny händelsehubben skapar du även namn området. (Exempel: *SB:// <Event Hub Name> . eventhub.Shanghai.azurestack.Corp.Microsoft.com*) |
   | Namn på händelsehubb | Namnet på händelsehubben som ska användas som indatamängd. |
   | Principnamn för Event Hub | Principen för delad åtkomst som ger åtkomst till Händelsehubben. Varje princip för delad åtkomst har ett namn, behörigheter som du anger och åtkomst nycklar. Det här alternativet fylls i automatiskt, om du inte väljer alternativet att ange inställningar för Händelsehubben manuellt. |
   | Princip nyckel för Event Hub | Den delade åtkomst nyckeln som används för att ge åtkomst till Händelsehubben. Det här alternativet fylls i automatiskt om du inte väljer alternativet för att ange inställningar för Händelsehubben manuellt. Du hittar den i Event Hub-inställningarna. |
   | Konsument grupp för Event Hub (valfritt) | Vi rekommenderar starkt att du använder en distinkt konsument grupp för varje Stream Analytics jobb. Den här strängen identifierar den konsument grupp som ska användas för att mata in data från händelsehubben. Om ingen konsument grupp har angetts använder Stream Analytics jobbet $Default konsument gruppen. |
   | Antal partitioner | Antal partitioner är antalet partitioner i en Event Hub. |

   > [!div class="mx-imgBorder"]
   > [![Inmatade ](media/on-azure-stack/event-hub-input.png) Event Hub](media/on-azure-stack/event-hub-input.png#lightbox)

   #### <a name="iot-hub"></a>IoT Hub

   | Fält | Värde |
   | --- | --- |
   | Inmatat alias | Ett eget namn som du använder i jobbets fråga för att referera till den här indatamängden. |
   | IoT Hub | Namnet på IoT Hub som ska användas som indatatyp. (Exempel:*<IoT Hub Name> . Shanghai.azurestack.Corp.Microsoft.com*) |
   | Namn på princip för delad åtkomst | Principen för delad åtkomst som ger åtkomst till IoT Hub. Varje princip för delad åtkomst har ett namn, behörigheter som du anger och åtkomst nycklar. |
   | Nyckel för delad åtkomst princip | Den delade åtkomst nyckeln som används för att ge åtkomst till IoT Hub. Det här alternativet fylls i automatiskt om du inte väljer alternativet att tillhandahålla IoT Hub-inställningarna manuellt. |
   | Konsument grupp (valfritt) | Vi rekommenderar starkt att du använder en annan konsument grupp för varje Stream Analytics jobb. Konsument gruppen används för att mata in data från IoT Hub. Stream Analytics använder $Default konsument gruppen om du inte anger något annat. |
   | Antal partitioner | Antal partitioner är antalet partitioner i en Event Hub. |

   > [!div class="mx-imgBorder"]
   > [![IoT Hub inmatade ](media/on-azure-stack/iot-hub-input.png)](media/on-azure-stack/iot-hub-input.png#lightbox)

6. Behåll standardvärdena för de andra fälten och välj Spara.
7. Öppna Utdata under Jobbtopologi och välj sedan Lägg till.
8. Välj Blob Storage, Event Hub eller Edge Hub (hubb) i list rutan.
9. Om utdata är en händelsehubben eller Blob Storage i en Azure Stack Hub-prenumeration anger du informationen manuellt enligt nedan.

   #### <a name="event-hub"></a>Händelsehubb

   | Fält | Värde |
   | --- | --- |
   | Utdataalias | Ett eget namn som används i frågor för att dirigera frågeresultatet till den här händelsehubben. |
   | Service Bus namnrymd | En behållare för en uppsättning meddelande enheter. När du skapade en ny händelsehubben, skapade du även ett Service Bus-namnområde. (Exempel: *SB:// <Event Hub Name> . eventhub.Shanghai.azurestack.Corp.Microsoft.com*) |
   | Namn på händelsehubb | Namnet på din Event Hub-utdata. |
   | Principnamn för Event Hub | Principen för delad åtkomst, som du kan skapa på fliken Konfigurera på händelsehubben. Varje princip för delad åtkomst har ett namn, behörigheter som du anger och åtkomst nycklar. |
   | Princip nyckel för Event Hub | Den delade åtkomst nyckeln som används för att autentisera åtkomsten till Event Hub-namnområdet. |

   > [!div class="mx-imgBorder"]
   > [![Utdata ](media/on-azure-stack/event-hub-output.png) för Event Hub](media/on-azure-stack/event-hub-output.png#lightbox)

   #### <a name="blob-storage"></a>Blob Storage 

   | Fält | Värde |
   | --- | --- |
   | Utdataalias | Ett eget namn som används i frågor för att dirigera frågeresultatet till blob-lagringen. |
   | Lagringskonto | Namnet på det lagrings konto där du ska skicka dina utdata. (Exempel: *<Storage Account Name> . blob.Shanghai.azurestack.Corp.Microsoft.com*) |
   | Lagrings konto nyckel | Den hemliga nyckeln som är kopplad till lagrings kontot. Det här alternativet fylls i automatiskt om du inte väljer alternativet för att tillhandahålla Blob Storage-inställningar manuellt. |

> [!NOTE]
> Parquet-formatet stöds inte för Edge-jobb på Azure Stack Hub. Använd 0 eller lämna tomt för minsta antal rader och maximal tid.


## <a name="deploy-stream-analytics-on-a-vm-or-device-connected-to-azure-stack"></a>Distribuera Stream Analytics på en virtuell dator eller enhet som är ansluten till Azure Stack

1. Öppna IoT Hub i Azure Portal. Navigera till **IoT Edge** och klicka på enheten (VM) som du vill använda som mål för den här distributionen.
2. Välj **Ange moduler**. Välj sedan **+ Lägg till** och välj **Azure Stream Analytics modul**. 
3. Välj den prenumeration och det jobb för ång Analytics-Edge som du skapade. Klicka på **Spara** och välj **Nästa: vägar**.

   > [!div class="mx-imgBorder"]
   > [![Lägg till moduler ](media/on-azure-stack/edge-modules.png)](media/on-azure-stack/edge-modules.png#lightbox)

4. Klicka på **Granska + skapa >**.
5. I steget **Granska + skapa** väljer du **skapa**. 
   > [!div class="mx-imgBorder"]
   > [![Manifest ](media/on-azure-stack/module-content.png)](media/on-azure-stack/module-content.png#lightbox)
6. Bekräfta att modulen har lagts till i listan.
   > [!div class="mx-imgBorder"]
   > [![Distributions sida ](media/on-azure-stack/edge-deployment.png)](media/on-azure-stack/edge-deployment.png#lightbox)

## <a name="next-steps"></a>Nästa steg
- [Azure Stream Analytics på IoT Edge](./stream-analytics-edge.md)
- [Utveckla Stream Analytics Edge-jobb](/stream-analytics-query/stream-analytics-query-language-reference)
