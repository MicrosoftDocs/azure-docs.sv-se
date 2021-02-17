---
title: Ledande hantering med Azure Table Storage – Microsoft Commercial Marketplace
description: Lär dig hur du använder Azure Table Storage för att konfigurera leads för Microsoft AppSource och Azure Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 08/25/2020
ms.openlocfilehash: 3c8b9444344e5bae414145e2b3367c265114a423
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100586851"
---
# <a name="use-azure-table-storage-to-manage-commercial-marketplace-leads"></a>Använd Azure Table Storage för att hantera leads på kommersiella marknads platser

Om ditt CRM-system (Customer Relations hip Management) inte uttryckligen stöds i Partner Center för att ta emot Microsoft AppSource och Azure Marketplace-leads, kan du använda Azure Table Storage för att hantera dessa leads. Du kan sedan välja att exportera data och importera dem till ditt CRM-system. Den här artikeln beskriver hur du skapar ett Azure Storage-konto och en tabell under det kontot. Dessutom kan du skapa ett nytt flöde med hjälp av funktionen för automatisk start för att skicka ett e-postmeddelande när ditt erbjudande tar emot ett lead.

## <a name="configure-an-azure-storage-account"></a>Konfigurera ett Azure Storage-konto

1. Om du inte har ett Azure-konto kan du [skapa ett kostnads fritt utvärderings konto](https://azure.microsoft.com/pricing/free-trial/).
1. När ditt Azure-konto är aktivt loggar du in på [Azure Portal](https://portal.azure.com).
1. I Azure Portal skapar du ett lagrings konto med hjälp av följande procedur:

    1. Välj **+ skapa en resurs** i den vänstra meny raden. Fönstret **nytt** visas till höger.
    1. Välj **lagring** i det **nya** fönstret. En **aktuell** lista visas till höger.
    1. Välj **lagrings konto** för att börja skapa konto. Följ instruktionerna i [skapa ett lagrings konto](../../storage/common/storage-account-create.md?tabs=azure-portal).

        :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png" alt-text="Steg för att skapa ett Azure Storage-konto.":::

        Mer information om lagrings konton finns i [snabb starts guide](../../storage/index.yml). Mer information om lagrings priser finns i [Storage-priser](https://azure.microsoft.com/pricing/details/storage/).

1. Vänta tills ditt lagrings konto har tillhandahållits. Den här processen tar vanligt vis några minuter.

## <a name="create-a-table-in-your-storage-account"></a>Skapa en tabell i ditt lagrings konto

1. På **Start** sidan för Azure Portal väljer du **Visa alla resurser** för att komma åt ditt lagrings konto. Du kan också välja **alla resurser** i den vänstra meny raden i Azure Portal.

    :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png" alt-text="Åtkomst till ditt Azure Storage-konto.":::

1. I fönstret lagrings konto väljer du **åtkomst nycklar** och kopierar värdet för **anslutnings strängen** för nyckeln. Spara det här värdet eftersom det är det **lagrings kontots anslutnings sträng** värde som du måste ange i publicerings portalen för att ta emot leads för ditt Azure Marketplace-erbjudande.

    Här är ett exempel på en anslutnings sträng.

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.screens.net
    ```

    :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png" alt-text="Azure Storage-nyckel.":::


1. I fönstret lagrings konto väljer du **tabeller** och sedan **+ tabell** för att skapa en tabell. Ange ett namn för tabellen och välj **OK**. Spara det här värdet eftersom du behöver det om du vill konfigurera ett flöde för att ta emot e-postaviseringar när leads tas emot.

    ![Azure-tabeller](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    Du kan använda [Azure Storage Explorer](https://www.storageexplorer.com) eller andra verktyg för att se data i lagrings tabellen. Du kan också exportera data i Azure-tabellen.

## <a name="optional-use-power-automate-to-get-lead-notifications"></a>Valfritt Använd energi automatisering för att få aviseringar om lead

Du kan använda automatisk [Energis par](/flow/) för att automatisera meddelanden varje gång ett lead läggs till i Azure Storages tabellen. Om du inte har ett konto kan du [Registrera dig för ett kostnads fritt konto](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Exempel på lead-avisering

Exemplet skapar ett flöde som automatiskt skickar ett e-postmeddelande när ett nytt lead läggs till i Azure Table Storage. Det här exemplet ställer in en upprepning för att skicka lead-information varje timme om tabell lagringen uppdateras.

1. Logga in på ditt energi automatiserade konto.
1. I det vänstra fältet väljer du **mina flöden**.
1. I det översta fältet väljer du **+ ny**.
1. I list rutan väljer du **+ schemalagd--från Tom**.

   ![Mina flöden + schemalagda – från Tom](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

1. I fönstret **skapa ett schemalagt flöde** , för **Upprepa var**, väljer du **1** för intervallet och **timmen** för frekvensen. Ge också flödet ett namn om du vill. Välj **Skapa**.

   >[!NOTE]
   >Även om det här exemplet använder ett intervall på en timme kan du välja det intervall och den frekvens som passar dina affärs behov bäst.

   ![Bygg ett schemalagt flöde](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

1. Välj **+ Nytt steg**.
1. I fönstret **Välj en åtgärd** söker du efter **Hämta tidigare tid**. Under **åtgärder** väljer du **Hämta tidigare tid**.

   ![Välj en åtgärd](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

1. I fönstret **Hämta tidigare tid** anger du **intervall** till **1**. I list rutan **tidsenhet** väljer du **timme**.

    >[!IMPORTANT]
    >Kontrol lera att intervallet och tidsenheten som du skickade i steg 8 matchar intervallet och frekvensen som du konfigurerade för upprepning i steg 5.

    ![Set hämta tidigare tidsintervall](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

   >[!TIP]
   >Du kan när som helst kontrol lera ditt flöde för att kontrol lera att varje steg har kon figurer ATS korrekt. Om du vill kontrol lera flödet väljer du **flödes kontroll** från **Flow** -meny raden.

   I nästa uppsättning steg ansluter du till din tabell och konfigurerar bearbetnings logiken för att hantera nya leads.

1. Välj **+ Nytt steg**. Sök sedan efter **Hämta entiteter** i fönstret **Välj en åtgärd** .
1. Under **åtgärder** väljer du **Hämta entiteter (Azure Table Storage)**.
1. I fönstret **Azure Table Storage** anger du information för följande rutor och väljer **skapa**:

    * **Anslutnings namn**: Ange ett beskrivande namn för anslutningen som du etablerar mellan det här flödet och tabellen.
    * **Lagrings konto namn**: Ange namnet på lagrings kontot för din tabell. Du kan hitta det här namnet på lagrings kontots **åtkomst nycklar** sida.
    * **Delad lagrings nyckel**: Ange nyckel värdet för ditt lagrings konto för din tabell. Du hittar det här värdet på sidan **åtkomst nycklar** för lagrings kontot.

      ![Fönstret Azure Table Storage](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

   När du har valt **skapa** visas fönstret **Hämta enheter** . Här väljer du **Visa avancerade alternativ** och ange information för följande rutor:

   * **Tabell**: Välj namnet på din tabell (från [skapa en tabell](#create-a-table-in-your-storage-account)). Följande bild visar frågan när `marketplaceleads` tabellen är markerad i det här exemplet.

     ![Fönstret Hämta entiteter](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

   * **Filter fråga**: Markera den här rutan och klistra in den här funktionen i rutan: `Timestamp gt datetime'@{body('Get_past_time')}'`

     ![Hämta entiteter, filtrera fråga rutan](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

1. Nu när du har slutfört konfigurationen av anslutningen till Azure-tabellen väljer du **nytt steg** för att lägga till ett villkor för att söka igenom Azure-tabellen efter nya leads.

1. I fönstret **Välj en åtgärd** väljer du **åtgärder**. Välj sedan **villkors kontroll**.

    ![Välj ett åtgärds fönster](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

1. I fönstret **villkor** väljer du **Välj ett värde**. Välj sedan **uttryck** i popup-fönstret.

1. Klistra in `length(body('Get_entities')?['value'])` i **FX** -rutan. Välj **OK** för att lägga till den här funktionen.

1. Så här slutför du konfigurationen av villkoret:
    1. Select **är större än** i den nedrullningsbara listan.
    2. Ange **0** som värde.

        ![Villkors fönster](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

   I de följande stegen ställer du in åtgärden som ska utföras baserat på resultatet av villkoret:

   * Om villkoret matchar **om nej**, gör ingenting.
   * Om villkoret är uppfyllt vid **Ja**, Utlös en åtgärd som ansluter ditt arbets-eller skol konto för att skicka ett e-postmeddelande. 

1. Välj **Lägg till en åtgärd** under **om ja**.

    ![Villkors fönster, om ja, Lägg till en åtgärd](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

1. Välj **Skicka ett e-postmeddelande (Office 365 Outlook)**.

    ![Villkors fönster, om ja, skicka ett e-postmeddelande](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!NOTE]
    >Om du vill använda en annan e-postprovider söker du efter och väljer **Skicka ett e-postmeddelande (e-post)** som åtgärd i stället. Anvisningarna visar hur du konfigurerar med Office 365 Outlook, men instruktionerna liknar en annan e-postprovider.

1. I Outlook-fönstret för Office 365 anger du information för följande rutor:

    1. **Till**: Ange en e-postadress för alla som får det här meddelandet.
    1. **Ämne**: Ange ett ämne för e-postmeddelandet. Ett exempel är **nya leads!**
    1. **Brödtext**: Lägg till den text som du vill inkludera i varje e-post (valfritt) och klistra in `body('Get_entities')?['value']` .

    >[!NOTE]
    >Du kan infoga ytterligare statiska eller dynamiska data punkter i bröd texten i det här e-postmeddelandet.

    ![Fönstret villkor, om ja, Office 365 Outlook-fönstret](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

1. Spara flödet genom att klicka på **Spara**. Automatisk avstängning testar flödet för fel. Om det inte finns några fel börjar flödet att köras när det har sparats.

Följande bild visar ett exempel på hur det slutliga flödet ska se ut.

![Ett exempel på det slutliga flödet](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>Hantera ditt flöde

Det är enkelt att hantera flödet när det är igång. Du har fullständig kontroll över ditt flöde. Du kan till exempel stoppa den, redigera den, se en körnings historik och få analys. Följande bild visar de alternativ som är tillgängliga för att hantera ett flöde.

 ![Hantera ett flöde](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

Flödet fortsätter att köras tills du avbryter det genom att använda alternativet **inaktivera flöde** av.

Om du inte får några e-postaviseringar om leads innebär det att nya leads inte har lagts till i Azure-tabellen. Om det uppstår flödes haverier får du ett e-postmeddelande som det här exemplet.

 ![E-postavisering om flödes problem](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Konfigurera ditt erbjudande för att skicka leads till Azure-tabellen

När du är redo att konfigurera ledar hanterings informationen för ditt erbjudande i publicerings portalen följer du de här stegen.

1. Gå till installations sidan för **erbjudandet** för ditt erbjudande.

1. Under avsnittet **kund leads** väljer du **Anslut**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-azure-table/customer-leads.png" alt-text="Kund ledare":::

1. I popup-fönstret **anslutnings information** väljer du Azure- **tabell** för lead- **målet**. 
     ![Lead-hantering, anslutnings information](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

1. Klistra in anslutnings strängen från Azure Storage-kontot som du skapade genom att följa de tidigare stegen i rutan **anslutnings sträng för lagrings konto** .
     ![Hantering av leads, anslutnings information lagrings konto](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

1. **Kontakta e-post**: Ange e-postmeddelanden för personer i företaget som ska få e-postaviseringar när ett nytt lead tas emot. Du kan ange flera e-postmeddelanden genom att avgränsa dem med semikolon.

1. Välj **OK**.

Om du vill kontrol lera att du har anslutit till ett lead-mål väljer du knappen **Verifiera** . Om det lyckas har du ett test lead i lead-målet.

>[!NOTE]
>Du måste slutföra konfigurationen av resten av erbjudandet och publicera den innan du kan ta emot leads för erbjudandet.

När leads skapas skickar Microsoft leads till Azure-tabellen. Om du har konfigurerat ett flöde skickas även ett e-postmeddelande till den e-postadress som du har konfigurerat.

## <a name="next-steps"></a>Nästa steg

- [Vanliga frågor och svar om hantering av leads](../lead-management-faq.md)
