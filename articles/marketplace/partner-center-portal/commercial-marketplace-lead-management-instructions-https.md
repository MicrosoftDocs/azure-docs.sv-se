---
title: Hantering av leads med en HTTPS-slutpunkt – Microsoft Commercial Marketplace
description: Lär dig hur du använder energi automatisering och en HTTPS-slutpunkt för att hantera leads från Microsoft AppSource och Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 04/09/2021
ms.openlocfilehash: 0da1fd7b98b86ebd75926cc8a2d2bb5294d048ca
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308193"
---
# <a name="use-an-https-endpoint-to-manage-commercial-marketplace-leads"></a>Använd en HTTPS-slutpunkt för att hantera affärs marknads platser

Om ditt CRM-system (Customer Relations hip Management) inte uttryckligen stöds i Partner Center för att ta emot Microsoft AppSource och Azure Marketplace-leads, kan du använda en HTTPS-slutpunkt i [energi automatisering](https://powerapps.microsoft.com/automate-processes/) för att hantera dessa leads. Med en HTTPS-slutpunkt kan leads i kommersiella marknads platser skickas ut som ett e-postmeddelande, eller så kan de skrivas till ett CRM-system som stöds av Power automatisering.

I den här artikeln beskrivs hur du skapar ett nytt flöde i Energis par för att generera HTTP POST-URL: en som du använder för att konfigurera leads i Partner Center. Den innehåller också steg för att testa ditt flöde med [Postman](https://www.getpostman.com/downloads/).

>[!NOTE]
>Den Energis par anslutning som används i dessa instruktioner kräver en betald prenumeration för att automatisera energi. Se till att du tar hänsyn till detta innan du konfigurerar det här flödet.

## <a name="create-a-flow-by-using-power-automate"></a>Skapa ett flöde med hjälp av energi automatisering

1. Öppna webb sidan [automatiserad start](https://flow.microsoft.com/) . Välj **Logga in**. Om du inte redan har ett konto väljer du **Registrera dig kostnads fritt** för att skapa ett kostnads fritt energi automatiserat konto.

1. Logga in och välj **mina flöden** i menyn.

    ![Logga in mina flöden](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

1. Under **+ ny** väljer du **+ flöde för direkt moln**.

    ![Mina flöden + automatiserad – från Tom](./media/commercial-marketplace-lead-management-instructions-https/https-my-flows-create-from-blank.png)

1. Namnge ditt flöde och välj sedan **när en HTTP-begäran tas emot** under **Välj hur det här flödet ska utlösas**.

    ![Knappen för att sätta upp en automatisk flödes fönster](./media/commercial-marketplace-lead-management-instructions-https/https-my-flows-pick-request-trigger.png)

1. Klicka på flödes steget för att expandera det.

    ![Expandera flödes steget](./media/commercial-marketplace-lead-management-instructions-https/expand-flow-step.png)

1. Använd någon av följande metoder för att konfigurera **begär ande texten JSON-schema**:

    - Kopiera JSON-schemat till text rutan **JSON-schema för begär ande** text.
    - Välj **Generera schemat genom att använda en exempelnyttolast**. Klistra in i JSON-exemplet i text rutan **Ange eller klistra in en JSON-nyttolast i exemplet** . Välj **färdig** för att skapa schemat.

    **JSON-schema**

    ```JSON
    {
      "$schema": "https://json-schema.org/draft-04/schema#",
      "definitions": {},
      "id": "http://example.com/example.json",
      "properties": {
        "ActionCode": {
          "id": "/properties/ActionCode",
          "type": "string"
        },
        "OfferTitle": {
          "id": "/properties/OfferTitle",
          "type": "string"
        },
        "LeadSource": {
          "id": "/properties/LeadSource",
          "type": "string"
        },
        "Description": {
          "id": "/properties/Description",
          "type": "string"
        },
        "UserDetails": {
          "id": "/properties/UserDetails",
          "properties": {
            "Company": {
              "id": "/properties/UserDetails/properties/Company",
              "type": "string"
            },
            "Country": {
              "id": "/properties/UserDetails/properties/Country",
              "type": "string"
            },
            "Email": {
              "id": "/properties/UserDetails/properties/Email",
              "type": "string"
            },
            "FirstName": {
              "id": "/properties/UserDetails/properties/FirstName",
              "type": "string"
            },
            "LastName": {
              "id": "/properties/UserDetails/properties/LastName",
              "type": "string"
            },
            "Phone": {
              "id": "/properties/UserDetails/properties/Phone",
              "type": "string"
            },
            "Title": {
              "id": "/properties/UserDetails/properties/Title",
              "type": "string"
            }
          },
          "type": "object"
        }
      },
      "type": "object"
    }
    ```

    **JSON-exempel**
    
    ```json
    {
      "UserDetails": {
        "FirstName": "Some",
        "LastName": "One",
        "Email": "someone@contoso.com",
        "Phone": "16175555555",
        "Country": "USA",
        "Company": "Contoso",
        "Title": "Esquire"
     },
      "LeadSource": "AzureMarketplace",
      "ActionCode": "INS",
      "OfferTitle": "Test Microsoft",
      "Description": "Test run through Power Automate"
    }
    ```

>[!NOTE]
>I det här läget i konfigurationen kan du välja att antingen ansluta till ett CRM-system eller konfigurera ett e-postmeddelande. Följ de återstående instruktionerna som du väljer.

### <a name="connect-to-a-crm-system"></a>Ansluta till ett CRM-system

1. Välj **+ Nytt steg**.
1. Välj önskat CRM-system genom att söka efter det där det står **Sök anslutningar och åtgärder**. Följande skärm bild visar hur du skapar en ny post (Common Data Service) som exempel.

    ![Skapa en ny post](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

1. Välj **fliken åtgärder** med åtgärden för att **skapa en ny post**.

    ![Välj Skapa en ny post](./media/commercial-marketplace-lead-management-instructions-https/select-create-new-record.png)

1. Ange **organisations namnet** som är kopplat till CRM-systemet. Välj **leads** i list rutan **entitetsnamn** .

    ![Välj leads](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

1. Energis par visar ett formulär för att tillhandahålla lead-information. Du kan mappa objekt från Indataporten genom att välja att lägga till dynamiskt innehåll. Följande skärm bild visar **OfferTitle** som ett exempel.

    ![Lägg till dynamiskt innehåll](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

1. Mappa de fält som du vill använda och välj sedan **Spara** för att spara ditt flöde. En HTTP POST-URL skapas och är tillgänglig i fönstret **när en HTTP-förfrågan tas emot** . Kopiera den här URL: en med hjälp av kopierings kontrollen som finns till höger om HTTP POST-URL: en. Att använda kopierings kontrollen är viktigt så att du inte saknar någon del av hela URL: en. Spara den här URL: en eftersom du behöver den när du konfigurerar hantering av leads i publicerings portalen.

    ![När en HTTP-begäran tas emot](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="set-up-email-notification"></a>Konfigurera e-postavisering

1. Nu när du har slutfört JSON-schemat väljer du **+ nytt steg**.
1. Under **Välj en åtgärd** väljer du **åtgärder**.
1. På fliken **åtgärder** väljer du **Skicka ett e-postmeddelande (Office 365 Outlook)**.

    >[!NOTE]
    >Om du vill använda en annan e-postprovider söker du efter och väljer **Skicka ett e-postmeddelande (e-post)** som åtgärd i stället.

    ![Lägg till en e-poståtgärd](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

1. I fönstret **Skicka ett e-postmeddelande** konfigurerar du följande obligatoriska fält:

   - **Till**: ange minst en giltig e-postadress dit leads ska skickas.
   - **Subject**: med energi alternativ får du möjlighet att lägga till dynamiskt innehåll, t. ex. **LeadSource** som visas på följande skärm bild. Börja med att ange ett fält namn. Välj sedan listan över dynamiskt innehåll i listan från popup-fönstret. 

        >[!NOTE] 
        > När du lägger till fält namn kan du följa varje namn med ett kolon (:) och välj sedan **RETUR** för att skapa en ny rad. När du har lagt till dina fält namn kan du lägga till varje associerad parameter från den dynamiska plock listan.

        ![Lägg till en e-poståtgärd med hjälp av dynamiskt innehåll](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Brödtext**: Lägg till den information som du vill ha i e-postmeddelandets brödtext i listan med dynamiskt innehåll. Använd till exempel LastName, FirstName, E-mail och Company. När du är klar med att konfigurera e-postmeddelandet ser det ut som exemplet på följande skärm bild.


       ![Exempel på e-postmeddelande](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

1. Välj **Spara** för att slutföra ditt flöde. En HTTP POST-URL skapas och är tillgänglig i fönstret **när en HTTP-förfrågan tas emot** . Kopiera den här URL: en med hjälp av kopierings kontrollen som finns till höger om HTTP POST-URL: en. Att använda den här kontrollen är viktigt så att du inte saknar någon del av hela URL: en. Spara den här URL: en eftersom du behöver den när du konfigurerar hantering av leads i publicerings portalen.

   ![HTTP POST-URL](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Testning

Du kan testa konfigurationen med [Postman](https://app.getpostman.com/app/download/win64). En online-hämtning av Postman är tillgänglig för Windows. 

1. Starta Postman och välj **ny**  >  **begäran** för att ställa in ditt test verktyg. 

   ![Förfrågan om att ställa in ditt test verktyg](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

1. Fyll i formuläret **Spara begäran** och spara sedan i den mapp som du skapade.

   ![Formulär för att spara begäran](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

1. Välj **post** från den nedrullningsbara listan. 

   ![Testa mitt flöde](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

1. Klistra in HTTP POST-URL: en från flödet som du skapade i energi spar läge där det står **Retur-URL**.

   ![Klistra in URL: en för HTTP-POST](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

1. Gå tillbaka till [Energis par automatisering](https://flow.microsoft.com/). Hitta det flöde som du skapade för att skicka leads genom att gå till **mina flöden** från meny raden för att automatisera snabb start. Välj ellipsen bredvid flödes namnet för att se fler alternativ och välj **Redigera**.


1. Välj **test** i det övre högra hörnet, Välj **Jag vill utföra utlösnings åtgärden** och välj sedan **testa**. Du ser en indikering överst på skärmen som testet har startat.

   ![Jag utför alternativet utlösnings åtgärd](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

1. Gå tillbaka till din Postman-app och välj **Skicka**.

   ![Knappen Skicka](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

1. Gå tillbaka till ditt flöde och kontrol lera resultatet. Om allt fungerar som förväntat visas ett meddelande som anger att flödet lyckades.

   ![Kontrol lera resultat](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

1. Du bör också ha fått ett e-postmeddelande. Kontrol lera din e-postinkorg. 

    >[!NOTE] 
    >Om du inte ser ett e-postmeddelande från testet kontrollerar du mapparna spam och skräp post. I följande skärm ser du bara fält etiketterna som du lade till när du konfigurerade e-postmeddelandet. Om detta var ett faktiskt lead som genererats från ditt erbjudande, skulle du också se den faktiska informationen från lead-kontakten i bröd texten och på ämnes raden.

   ![Mottaget e-postmeddelande](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Konfigurera ditt erbjudande för att skicka leads till HTTPS-slutpunkten

När du är redo att konfigurera ledar hanterings informationen för ditt erbjudande i publicerings portalen följer du de här stegen.

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/home).

1. Välj ditt erbjudande och gå till fliken **erbjudande konfiguration** .

1. Under avsnittet **kund leads** väljer du **Anslut**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-https/customer-leads.png" alt-text="Kund ledare":::

1. I popup-fönstret **anslutnings information** väljer du https- **slutpunkt** för lead- **målet**. Klistra in HTTP POST-URL: en från flödet som du skapade genom att följa tidigare steg i URL-fältet för **https-slutpunkt** .
    ![Anslutnings information kontakta e-post](./media/commercial-marketplace-lead-management-instructions-https/https-connection-details.png)

1. Under **Kontakta e-postadress** anger du e-postadresser för personer i företaget som ska få e-postaviseringar när ett nytt lead tas emot. Du kan ange flera e-postmeddelanden genom att avgränsa dem med semikolon.

1. Välj **OK**.

Om du vill kontrol lera att du har anslutit till ett lead-mål väljer du knappen **Verifiera** . Om det lyckas har du ett test lead i lead-målet.

>[!NOTE] 
>Du måste slutföra konfigurationen av resten av erbjudandet och publicera den innan du kan ta emot leads för erbjudandet.

När leads genereras skickar Microsoft leads till flödet. Leads dirigeras till CRM-systemet eller e-postadressen som du har konfigurerat.
