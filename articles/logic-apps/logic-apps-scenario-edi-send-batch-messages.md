---
title: Batch bearbeta EDI-meddelanden som en grupp
description: Skicka och ta emot EDI-meddelanden som batchar, grupper eller samlingar med batch-bearbetning i Azure Logic Apps
services: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/19/2018
ms.openlocfilehash: 6fc0833f70e3e9cd98100f193b52e5a1bfa4d651
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000410"
---
# <a name="exchange-edi-messages-as-batches-or-groups-between-trading-partners-in-azure-logic-apps"></a>Exchange EDI-meddelanden som batchar eller grupper mellan handels partner i Azure Logic Apps

I Business to Business (B2B)-scenarier, utbyte ofta meddelanden i grupper eller *batchar*. När du skapar en batch-lösning med Logic Apps kan du skicka meddelanden till handels partner och bearbeta dessa meddelanden tillsammans i batchar. Den här artikeln visar hur du kan bearbeta EDI-meddelanden med hjälp av X12 som exempel genom att skapa en "batch-avsändare"-Logic-app och en "batch-mottagare". 

Batching X12-meddelanden fungerar som batching av andra meddelanden. du använder en batch-utlösare som samlar in meddelanden i en batch och en batch-åtgärd som skickar meddelanden till batchen. Dessutom innehåller X12 batching ett X12-kodnings steg innan meddelandena skickas till handels partnern eller till ett annat mål. Mer information om batch-utlösare och åtgärder finns i [batch-bearbeta meddelanden](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

I den här artikeln skapar du en batch-lösning genom att skapa två Logi Kap par i samma Azure-prenumeration, Azure-region och enligt följande ordning:

* En Logic-app med ["batch-mottagare"](#receiver) som godkänner och samlar in meddelanden i en batch tills dina angivna kriterier är uppfyllda för att släppa och bearbeta dessa meddelanden. I det här scenariot kodar batch-mottagaren meddelandena i batchen med hjälp av det angivna X12-avtalet eller partner identiteter.

  Se till att du först skapar batch-mottagaren så att du senare kan välja batch-målet när du skapar batch-avsändaren.

* En ["batch avsändare"](#sender) -Logic-app som skickar meddelandena till den tidigare skapade batch-mottagaren. 

Se till att batch-mottagaren och batch-avsändaren delar samma Azure-prenumeration *och* Azure-region. Om de inte gör det kan du inte välja batch-mottagaren när du skapar batch-avsändaren eftersom de inte är synliga för varandra.

## <a name="prerequisites"></a>Förutsättningar

För att kunna följa det här exemplet behöver du följande objekt:

* En Azure-prenumeration. Om du inte har en prenumeration kan du [börja med ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Eller [Registrera dig för en prenumeration där du betalar per](https://azure.microsoft.com/pricing/purchase-options/)användning.

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Ett befintligt [integrations konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) som är associerat med din Azure-prenumeration och som är länkat till dina Logic Apps

* Minst två befintliga [partner](../logic-apps/logic-apps-enterprise-integration-partners.md) i ditt integrations konto. Varje partner måste använda X12 (standardbärvåg alpha Code) som en affärs identitet i partnerns egenskaper.

* Ett befintligt [X12-avtal](../logic-apps/logic-apps-enterprise-integration-x12.md) i ditt integrations konto

* Om du vill använda Visual Studio i stället för Azure Portal, se till att du [konfigurerar Visual Studio för att arbeta med Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="receiver"></a>

## <a name="create-x12-batch-receiver"></a>Skapa X12 batch-mottagare

Innan du kan skicka meddelanden till en batch måste den batch först finnas som mål där du skickar meddelandena. Först måste du skapa Logic-appen "batch-mottagare" som börjar med **batch** -utlösaren. På så sätt kan du välja Logic-appen för batch-mottagare när du skapar Logic-appen "batch-avsändare". Batch-mottagaren fortsätter att samla in meddelanden tills dina angivna kriterier är uppfyllda för att släppa och bearbeta dessa meddelanden. Även om batch-mottagare inte behöver känna till något om batch-avsändare måste batch-avsändare känna till målet där de skickar meddelandena. 

För batch-mottagare anger du batch-läge, namn, versions villkor, X12-avtal och andra inställningar. 

1. I [Azure Portal](https://portal.azure.com) eller Visual Studio skapar du en Logic-app med följande namn: "BatchX12Messages"

2. [Länka din Logic app till ditt integrations konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

3. I Logic Apps designer lägger du till **batch** -utlösaren som startar ditt Logic app-arbetsflöde. I rutan Sök anger du "batch" som filter. Välj den här utlösaren: **batch-meddelanden**

   ![Lägg till batch-utlösare](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

4. Ange egenskaper för batch-mottagare: 

   | Egenskap | Värde | Obs! | 
   |----------|-------|-------|
   | **Batch-läge** | Infogad |  |  
   | **Batch-namn** | TestBatch | Endast tillgängligt med **inline** batch-läge | 
   | **Versions villkor** | Antal meddelanden baserat, schema baserat | Endast tillgängligt med **inline** batch-läge | 
   | **Antal meddelanden** | 10 | Endast tillgängligt med villkor för **antal meddelanden som baseras** | 
   | **Intervall** | 10 | Endast tillgängligt med **schemabaserade** versions villkor | 
   | **Frekvens** | minut | Endast tillgängligt med **schemabaserade** versions villkor | 
   ||| 

   ![Ange information om batch-utlösare](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-release-criteria.png)

   > [!NOTE]
   > Det här exemplet ställer inte in någon partition för batchen, så varje batch använder samma partitionsnyckel. Mer information om partitioner finns i [batch-bearbeta meddelanden](../logic-apps/logic-apps-batch-process-send-receive-messages.md#batch-sender).

5. Lägg nu till en åtgärd som kodar varje batch: 

   1. Under batch-utlösaren väljer du **nytt steg**.

   2. Skriv "X12 batch" som filter i sökrutan och välj den här åtgärden (valfri version): **batch-koda <*version*>-X12** 

      ![Välj X12 batch Encode-åtgärd](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)

   3. Om du inte tidigare har anslutit till ditt integrations konto skapar du anslutningen nu. Ange ett namn för anslutningen, Välj det integrations konto som du vill använda och välj sedan **skapa**.

      ![Skapa anslutning mellan batch-kodare och integrations konto](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encoder-connect-integration-account.png)

   4. Ange följande egenskaper för din Batch Encoder-åtgärd:

      | Egenskap | Beskrivning |
      |----------|-------------|
      | **Namn på X12-avtal** | Öppna listan och välj ditt befintliga avtal. <p>Om listan är tom, se till att du [länkar din Logic app till det integrations konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) som har det avtal som du vill ha. | 
      | **Åtgärdsindatan** | Klicka i den här rutan och när listan med dynamiskt innehåll visas väljer du token för **batch-namn** . | 
      | **Partitionsnamn** | Klicka i den här rutan och när listan med dynamiskt innehåll visas väljer du **partitionens namn** -token. | 
      | **Poster** | Stäng rutan objekt information och klicka sedan i den här rutan. När listan med dynamiskt innehåll visas väljer du token för **batch-objekt** . | 
      ||| 

      ![Åtgärds information för batch-koda](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

      För rutan **objekt** :

      ![Åtgärds objekt för batch-koda](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-items.png)

6. Spara logikappen. 

7. Om du använder Visual Studio kontrollerar du att du [distribuerar din Logic app för batch-mottagare till Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). Annars kan du inte välja batch-mottagaren när du skapar batch-avsändaren.

### <a name="test-your-logic-app"></a>Testa din Logic app

För att se till att batch-mottagaren fungerar som förväntat, kan du lägga till en HTTP-åtgärd i test syfte och skicka ett batch-meddelande till [tjänsten Request bin](https://requestbin.com/). 

1. Välj **nytt steg** under X12-instruktionen. 

2. I rutan Sök anger du "http" som filter. Välj den här åtgärden: **http-http**
    
   ![Välj HTTP-åtgärd](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action.png)

3. Ange egenskaperna för HTTP-åtgärden:

   | Egenskap | Beskrivning | 
   |----------|-------------|
   | **Metod** | Välj **post** i listan. | 
   | **URI** | Generera en URI för din begär ande bin och ange sedan denna URI i den här rutan. | 
   | **Brödtext** | Klicka i den här rutan och när listan med dynamiskt innehåll öppnas väljer du **Body** -token, som visas i avsnittet, **batch-koda efter avtals namn**. <p>Om du inte ser **Body** -token, bredvid **batch-koda efter avtals namn**, väljer du **Se fler**. | 
   ||| 

   ![Ange information om HTTP-åtgärd](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action-details.png)

4. Spara logikappen. 

   Din Logic app för batch-mottagare ser ut så här: 

   ![Spara din Logic-app för batch-mottagare](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-finished.png)

<a name="sender"></a>

## <a name="create-x12-batch-sender"></a>Skapa X12 batch Sender

Skapa nu en eller flera Logi Kap par som skickar meddelanden till appen för batch-mottagares Logic. I varje batch-avsändare kan du ange Logic app för batch-mottagare och batch-namn, meddelande innehåll och andra inställningar. Du kan också ange en unik partitionsnyckel för att dela upp batchen i del mängder för att samla in meddelanden med den nyckeln. 

* Se till att du redan har [skapat batch-mottagaren](#receiver) så att du kan välja den befintliga batch-mottagaren som mål när du skapar batch-avsändaren. Även om batch-mottagare inte behöver känna till något om batch-avsändare måste batch-avsändare veta var du ska skicka meddelanden. 

* Se till att batch-mottagaren och batch-avsändaren delar samma Azure-region *och* Azure-prenumeration. Om de inte gör det kan du inte välja batch-mottagaren när du skapar batch-avsändaren eftersom de inte är synliga för varandra.

1. Skapa en annan Logic-app med följande namn: "SendX12MessagesToBatch" 

2. I rutan Sök anger du "när en http-begäran" som filter. Välj den här utlösaren: **när en HTTP-begäran tas emot** 
   
   ![Lägg till begär ande utlösare](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

3. Lägg till en åtgärd för att skicka meddelanden till en batch.

   1. Under åtgärden HTTP-begäran väljer du **nytt steg**.

   2. I rutan Sök anger du "batch" som filter. 
   Välj listan **åtgärder** och välj sedan den här åtgärden: **Välj ett Logic Apps arbets flöde med batch-utlösare – skicka meddelanden till batch**

      ![Välj "Välj ett Logic Apps arbets flöde med batch-utlösare"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-trigger.png)

   3. Välj nu din "BatchX12Messages"-Logic-app som du skapade tidigare.

      ![Välj Logic-appen "batch mottagare"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-receiver.png)

   4. Välj den här åtgärden: **Batch_messages-<*din-batch-mottagare* >**

      ![Välj åtgärden Batch_messages](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-messages-action.png)

4. Ange egenskaperna för batch-avsändaren.

   | Egenskap | Beskrivning | 
   |----------|-------------| 
   | **Batch-namn** | Batch-namnet som definieras av mottagar logiks appen, som är "TestBatch" i det här exemplet <p>**Viktigt**: batch-namnet verifieras vid körning och måste matcha det namn som anges av mottagar logiks appen. Om du ändrar batch-namnet Miss växlar batch-avsändaren. | 
   | **Meddelande innehåll** | Innehållet för det meddelande som du vill skicka, som är **Body** -token i det här exemplet | 
   ||| 
   
   ![Ange egenskaper för batch](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-set-batch-properties.png)

5. Spara logikappen. 

   Din app för batch-avsändare ser ut som i det här exemplet:

   ![Spara din Logic app för batch-avsändare](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Testa dina Logic Apps

Om du vill testa din batching-lösning skickar du X12-meddelanden till din batch avsändarens Logic-app från [Postman](https://www.getpostman.com/postman) eller ett liknande verktyg. Snart börjar du få X12-meddelanden på din begär ande bin, antingen var tionde minut eller i batchar på 10, alla med samma partitionsnyckel.

## <a name="next-steps"></a>Nästa steg

* [Bearbeta meddelanden som batchar](../logic-apps/logic-apps-batch-process-send-receive-messages.md) 
