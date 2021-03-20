---
title: Bearbeta meddelanden i grupp
description: Skicka och ta emot meddelanden i grupper mellan dina arbets flöden med batch-bearbetning i Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.date: 07/31/2020
ms.openlocfilehash: 0985afe3ddfd0d9de3c36ad6b030b6f259708c88
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87458355"
---
# <a name="send-receive-and-batch-process-messages-in-azure-logic-apps"></a>Skicka, ta emot och gruppera process meddelanden i Azure Logic Apps

Om du vill skicka och bearbeta meddelanden tillsammans på ett särskilt sätt som grupper kan du skapa en batch-lösning. Den här lösningen samlar in meddelanden i en *batch* och väntar tills dina angivna kriterier är uppfyllda innan du släpper och bearbetar batch-meddelandena. Satsvis kompilering kan minska hur ofta din Logic app bearbetar meddelanden.

Den här artikeln visar hur du skapar en batch-lösning genom att skapa två Logi Kap par i samma Azure-prenumeration, Azure-region och i följande ordning:

1. Logic-appen ["batch-mottagare"](#batch-receiver) , som godkänner och samlar in meddelanden i en batch tills dina angivna kriterier är uppfyllda för att släppa och bearbeta dessa meddelanden. Se till att du först skapar den här batch-mottagaren så att du senare kan välja batch-målet när du skapar batch-avsändaren.

1. En eller flera Logic-appar för [batch-avsändare](#batch-sender) , som skickar meddelanden till den tidigare skapade batch-mottagaren.

   Du kan också ange en unik nyckel, till exempel ett kund nummer, som *partitionerar* eller delar upp mål gruppen i logiska del mängder baserat på den nyckeln. På så sätt kan mottagar appen samla in alla objekt med samma nyckel och bearbeta dem tillsammans.

Din batch-mottagare och batch-avsändare måste dela samma Azure-prenumeration *och* Azure-region. Om de inte gör det kan du inte välja batch-mottagaren när du skapar batch-avsändaren eftersom de inte är synliga för varandra.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto och prenumeration. Om du inte har en prenumeration kan du [börja med ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Eller [Registrera dig för en prenumeration där du betalar per](https://azure.microsoft.com/pricing/purchase-options/)användning.

* Ett e-postkonto med en [e-postprovider som stöds av Azure Logic Apps](../connectors/apis-list.md)

  > [!IMPORTANT]
  > Om du vill använda Gmail Connector kan endast företags konton i G-Suite använda den här anslutningen utan begränsning i Logic Apps. Om du har ett Gmail-konto kan du använda den här anslutningen med endast vissa Google-godkända tjänster, eller så kan du [skapa en Google-klient som används för autentisering med din Gmail-anslutning](/connectors/gmail/#authentication-and-bring-your-own-application). Mer information finns i [principer för data säkerhet och sekretess för Google Connectors i Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Om du vill använda Visual Studio i stället för Azure Portal, se till att du [konfigurerar Visual Studio för att arbeta med Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="batch-receiver"></a>

## <a name="create-batch-receiver"></a>Skapa batch-mottagare

Innan du kan skicka meddelanden till en batch måste den batch först finnas som mål där du skickar meddelandena. Först måste du skapa Logic-appen "batch-mottagare" som börjar med **batch** -utlösaren. På så sätt kan du välja Logic-appen för batch-mottagare när du skapar Logic-appen "batch-avsändare". Batch-mottagaren fortsätter att samla in meddelanden tills dina angivna kriterier är uppfyllda för att släppa och bearbeta dessa meddelanden. Även om batch-mottagare inte behöver känna till något om batch-avsändare måste batch-avsändare känna till målet där de skickar meddelandena.

1. Skapa en logisk app med det här namnet i [Azure Portal](https://portal.azure.com) eller Visual Studio: `BatchReceiver`

1. I Logic Apps designer lägger du till **batch** -utlösaren som startar ditt Logic app-arbetsflöde. I rutan Sök anger `batch` du och väljer den här utlösaren: **batch-meddelanden**

   ![Lägg till utlösaren "batch meddelanden"](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

1. Ange dessa egenskaper för batch-mottagaren:

   | Egenskap | Beskrivning |
   |----------|-------------|
   | **Batch-läge** | - **Infogad**: för att definiera versions villkor i batch-utlösaren <br>- **Integrations konto**: för att definiera konfigurationer av flera utgivnings kriterier via ett [integrations konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Med ett integrations konto kan du underhålla alla konfigurationer på en plats snarare än i separata Logi Kap par. |
   | **Batch-namn** | Namnet på batchen, som är "TestBatch" i det här exemplet och gäller endast för **inline** batch-läge |
   | **Versions villkor** | Gäller endast för **infogad** batch-läge och väljer kriterier för att uppfylla innan varje batch bearbetas: <p>- **Antal meddelanden som baseras**: släpp batchen baserat på antalet meddelanden som samlats in av batchen. <br>- **Storlek baserad**: Frisläpp batchen baserat på den totala storleken i byte för alla meddelanden som samlats in av batchen. <br>- **Schema**: Frisläpp batchen baserat på ett upprepnings schema som anger ett intervall och en frekvens. I de avancerade alternativen kan du också välja en tidszon och ange start datum och start tid. <br>- **Markera alla**: Använd alla angivna villkor. |
   | **Antal meddelanden** | Antalet meddelanden som ska samlas in i batchen, till exempel 10 meddelanden. En Batchs gräns är 8 000 meddelanden. |
   | **Batchstorlek** | Den totala storleken i byte som ska samlas in i batchen, till exempel 10 MB. En grupps storleks gräns är 80 MB. |
   | **Schema** | Intervall och frekvens mellan batch-versioner, till exempel 10 minuter. Den minsta upprepningen är 60 sekunder eller 1 minut. Bråk minuter avrundas effektivt upp till 1 minut. Om du vill ange en tidszon eller start datum och start tid öppnar du listan **Lägg till ny parameter** och väljer motsvarande egenskaper. |
   |||

   > [!NOTE]
   >
   > Om du ändrar versions villkoren medan utlösaren fortfarande har batch-och meddelanden som inte skickats, använder utlösaren de uppdaterade versions kriterierna för hantering av meddelanden som inte har skickats.

   I det här exemplet visas alla kriterier, men för dina egna tester kan du prova bara ett kriterium:

   ![Ange information om batch-utlösare](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-criteria.png)

1. Lägg nu till en eller flera åtgärder som bearbetar varje batch.

   I det här exemplet lägger du till en åtgärd som skickar ett e-postmeddelande när batch-utlösaren utlöses. Utlösaren kör och skickar ett e-postmeddelande när batchen antingen har 10 meddelanden, når 10 MB eller efter 10 minuter.

   1. Under batch-utlösaren väljer du **nytt steg**.

   1. I rutan Sök anger `send email` du som filter. Välj en e-postanslutning baserat på din e-postprovider.

      Om du till exempel har ett arbets-eller skol konto, till exempel @fabrikam.com eller @fabrikam.onmicrosoft.com , väljer du **Microsoft 365 Outlook** Connector. Om du har ett personligt konto, till exempel @outlook.com eller @hotmail.com , väljer du **Outlook.com** -anslutningen. I det här exemplet används Microsoft 365 Outlook Connector.

   1. Välj åtgärden "Skicka ett e-postmeddelande" för din Provider, till exempel:

      ![Välj åtgärden "Skicka ett e-postmeddelande" för din e-postleverantör](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-send-email-action.png)

1. Logga in på ditt e-postkonto om du uppmanas att göra det.

1. Ange egenskaperna för den åtgärd som du har lagt till.

   * Ange mottagarens e-postadress i fältet **Till**. I testsyfte kan du använda din egen e-postadress.

   * I rutan **ämne** , när listan med dynamiskt innehåll visas, väljer du fältet **partitionsnamn** .

     ![Välj "partitionsnamn" i listan med dynamiskt innehåll](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     Senare i batch-avsändaren kan du ange en unik partitionsnyckel som delar upp mål gruppen i logiska under uppsättningar där du kan skicka meddelanden. Varje uppsättning har ett unikt nummer som genereras av Logic-appen för batch-avsändare. Med den här funktionen kan du använda en enda batch med flera del mängder och definiera varje delmängd med det namn som du anger.

     > [!IMPORTANT]
     > En partition har en gräns på 5 000 meddelanden eller 80 MB. Om något av villkoren uppfylls kan Logic Apps släppa batchen, även när ditt definierade versions villkor inte är uppfyllt.

   * I rutan **brödtext** , när listan med dynamiskt innehåll visas, väljer du fältet **meddelande-ID** .

     Logic App Designer lägger automatiskt till en **för varje** slinga runt åtgärden skicka e-post eftersom den här åtgärden behandlar utdata från föregående åtgärd som en samling, i stället för en batch.

     ![För "brödtext" väljer du "meddelande-ID"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

1. Spara logikappen. Nu har du skapat en batch-mottagare.

    ![Spara din logikapp](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

   > [!IMPORTANT]
   > Om du använder Visual Studio måste du innan du fortsätter till nästa avsnitt se till att du först [ *distribuerar* din Logic app för batch-mottagare till Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). Annars kan du inte välja batch-mottagaren när du skapar batch-avsändaren.

<a name="batch-sender"></a>

## <a name="create-batch-sender"></a>Skapa batch-avsändare

Skapa nu en eller flera Logic-appar för batch-avsändare som skickar meddelanden till appen för batch-mottagare. I varje batch-avsändare anger du batch-mottagare och batch-namn, meddelande innehåll och andra inställningar. Du kan också ange en unik partitionsnyckel för att dela upp batchen i logiska del mängder för insamling av meddelanden med den nyckeln.

* Se till att du har [skapat och distribuerat batch-mottagaren](#batch-receiver) tidigare, så när du skapar batch-avsändaren kan du välja den befintliga batch-mottagaren som mål grupp. Även om batch-mottagare inte behöver känna till något om batch-avsändare måste batch-avsändare veta var du ska skicka meddelanden.

* Se till att batch-mottagaren och batch-avsändaren delar samma Azure-region *och* Azure-prenumeration. Om de inte gör det kan du inte välja batch-mottagaren när du skapar batch-avsändaren eftersom de inte är synliga för varandra.

1. Skapa en annan Logic-app med det här namnet: `BatchSender`

   1. I rutan Sök anger `recurrence` du som filter. Välj den här utlösaren i listan utlösare: **upprepning**

      ![Lägg till upprepnings utlösaren](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-sender.png)

   1. Ange intervall och frekvens för att köra avsändar Logic-appen varje minut.

      ![Ange frekvens och intervall för upprepnings utlösare](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-sender-details.png)

1. Lägg till en ny åtgärd för att skicka meddelanden till en batch.

   1. Under **upprepnings** utlösaren väljer du **nytt steg**.

   1. I rutan Sök anger `batch` du som filter och väljer den här åtgärden: **välj ett Logic Apps arbets flöde med batch-utlösare**

      ![Välj "Välj ett Logic Apps arbets flöde med batch-utlösare"](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

      En lista visas och visar bara de Logic Apps som har batch-utlösare och som finns i samma Azure-region *och* Azure-prenumeration som din batch avsändar Logic-app.

   1. I listan Logi Kap par väljer du den batch mottagare Logic-app som du skapade tidigare.

      ![Välj din Logic-app för batch-mottagare](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch-receiver.png)

      > [!IMPORTANT]
      > Om du använder Visual Studio och du inte ser några batch-mottagare att välja, kontrollerar du att du tidigare har skapat och distribuerat batch-mottagaren till Azure. Om du inte har det kan du lära dig [hur du distribuerar din Logic app för batch-mottagare till Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure).

   1. Välj den här åtgärden i listan åtgärder: **Batch_messages-<*ditt-Logic-App-Name* >**

      ![Välj den här åtgärden: "Batch_messages-<din Logic-app>"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch.png)

1. Ange egenskaperna för batch-avsändaren:

   | Egenskap | Beskrivning |
   |----------|-------------|
   | **Batch-namn** | Batch-namnet som definieras av mottagar logiks appen, som är `TestBatch` i det här exemplet <p>**Viktigt**: batch-namnet verifieras vid körning och måste matcha det namn som anges av mottagar logiks appen. Om du ändrar batch-namnet Miss växlar batch-avsändaren. |
   | **Meddelande innehåll** | Innehållet för det meddelande som du vill skicka |
   |||

   > [!NOTE]
   > Egenskaps värden för **Utlösarens namn** och **arbets flöde** fylls i automatiskt från den valda Logic-appen.

   I det här exemplet lägger du till det här uttrycket, som infogar aktuellt datum och tid i meddelande innehållet som du skickar till batchen:

   1. Klicka i rutan **meddelande innehåll** .

   1. När listan med dynamiskt innehåll visas väljer du **uttryck**.

   1. Ange uttrycket `utcnow()` och välj **OK**.

      ![I "meddelande innehåll" väljer du "uttryck", anger "utcnow ()" och väljer "OK".](./media/logic-apps-batch-process-send-receive-messages/batch-sender-details.png)

1. Nu skapar du en partition för batchen. `BatchReceiver`Öppna listan **Lägg till ny parameter** i åtgärden och välj följande egenskaper:

   | Egenskap | Beskrivning |
   |----------|-------------|
   | **Partitionsnamn** | En valfri unik partitionsnyckel som ska användas för att dela upp mål gruppen i logiska under uppsättningar och samla in meddelanden baserat på nyckeln |
   | **Meddelande-ID** | En valfri meddelande identifierare som är en genererad globalt unik identifierare (GUID) som är tom |
   |||

   I det här exemplet lägger du till ett uttryck som genererar ett slumpmässigt tal mellan ett och fem i rutan **partitionsnamn** . Lämna rutan **meddelande-ID** tom.

   1. Klicka i rutan **partitionsnamn** så att listan med dynamiskt innehåll visas.

   1. I listan med dynamiskt innehåll väljer du **uttryck**.

   1. Ange uttrycket `rand(1,6)` och välj sedan **OK**.

      ![Konfigurera en partition för mål gruppen](./media/logic-apps-batch-process-send-receive-messages/batch-sender-partition-advanced-options.png)

      Den här **slump** -funktionen genererar ett tal mellan ett och fem. Så du delar upp den här gruppen i fem numrerade partitioner, vilket detta uttryck dynamiskt anger.

1. Spara logikappen. Appen för avsändar logik ser nu ut ungefär som i det här exemplet:

   ![Spara avsändar Logic-appen](./media/logic-apps-batch-process-send-receive-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Testa dina Logic Apps

Du testar din batching-lösning genom att låta dina Logi Kap par köras i några minuter. Snart börjar du få e-postmeddelanden i grupper om fem, alla med samma partitionsnyckel.

Din Logic app för batch-avsändare körs varje minut, genererar ett slumptal mellan en och fem och använder det här genererade numret som partitionsnyckel för mål batchen där meddelanden skickas. Varje gång batchen har fem objekt med samma partitionsnyckel utlöses din batch-mottagares Logic app och skickar e-post för varje meddelande.

> [!IMPORTANT]
> När du är klar med testningen ska du se till att du inaktiverar `BatchSender` Logic-appen för att sluta skicka meddelanden och slipper överlagring av Inkorgen.

## <a name="next-steps"></a>Nästa steg

* [Batchbearbeta och skicka EDI-meddelanden](../logic-apps/logic-apps-scenario-edi-send-batch-messages.md)
* [Bygg på Logic app-definitioner med JSON](../logic-apps/logic-apps-author-definitions.md)
* [Bygg en app utan server i Visual Studio med Azure Logic Apps och funktioner](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Undantags hantering och fel loggning för Logic Apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
