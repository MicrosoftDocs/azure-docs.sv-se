---
title: Principer för data säkerhet och sekretess för Google-anslutningar
description: Lär dig mer om påverkan på Google Security-och Privacy-principer har på Google connectors, till exempel Gmail, i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: conceptual
ms.date: 06/05/2020
ms.openlocfilehash: 2a5204be638f108b40e431b148c9cb97788c4a52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91400765"
---
# <a name="data-security-and-privacy-policies-for-google-connectors-in-azure-logic-apps"></a>Principer för data säkerhet och sekretess för Google-anslutningar i Azure Logic Apps

Från och med den **1 maj 2020** kan ändringar på grund av Googles [principer för data säkerhet och sekretess](https://www.blog.google/technology/safety-security/project-strobe/) påverka dina Logic app-arbetsflöden som använder [Gmail-anslutningen](/connectors/gmail/). Om dina Logi Kap par använder Gmail-anslutningen med ett Gmail-användarkonto (e-postadress som slutar med @gmail.com eller @googlemail.com ) kan dina Logi Kap par endast använda vissa [Google-godkända utlösare, åtgärder och anslutningar](#approved-connectors).

> [!NOTE]
> Om dina Logi Kap par använder Gmail-anslutningen med ett företags konto i G-svit (e-postadress med en anpassad domän), påverkas inte dina Logi Kap par och har inga begränsningar för att använda Gmail-anslutningsprogrammet.

## <a name="affected-logic-apps"></a>Berörda Logic Apps

Om du har Logi Kap par som använder Gmail Connector får du ett e-postmeddelande om potentiellt inaktuella Logic Apps. Men från och med den **15 juni 2020** inaktive ras alla icke-kompatibla arbets flöden. Du kan vidta någon av följande åtgärder:

* Uppdatera de berörda Logic Apps genom [att följa stegen i det här avsnittet](#update-affected-workflows). Du måste skapa en Google client-app, som innehåller ett klient-ID och klient hemlighet som du använder för autentisering i din Gmail-utlösare eller åtgärd.

* Uppdatera de berörda Logic Apps så att de bara använder de [Google-godkända kopplingarna](#approved-connectors) innan du aktiverar de inaktiverade Logic Apps igen.

<a name="approved-connectors"></a>

## <a name="google-approved-connectors"></a>Google-godkända anslutningar

När du använder ett Gmail-konto i den här principen kan du använda Gmail-anslutningen med enbart vissa Google-godkända tjänster, som kan ändras. Våra tekniska team fortsätter att arbeta med Google för att lägga till fler tjänster i listan. Nu är här de Google-godkända utlösare, åtgärder och kopplingar som du kan använda i samma Logic app-arbetsflöde med Gmail Connector när du använder ett Gmail-konsument konto:

* Logic Apps inbyggda utlösare och åtgärder: batch, kontroll, data åtgärder, datum tid, flat fil, flytande, begäran, schema, variabler och XML

  Inbyggda utlösare och åtgärder som inte är godkända av Google, till exempel HTTP, Azure Functions, Azure Logic Apps och andra, gör en Logic app icke-kompatibel med Gmail Connector eftersom appen kan skicka eller ta emot data från var som helst.

* Google-tjänster: Gmail, Google Calendar, Google Contacts, Google Drive, Google Sheets och Google tasks

* Godkända Microsoft-tjänster: Dynamics 365, Excel Online, Microsoft Teams, Microsoft 365, OneDrive och SharePoint Online

* Anslutningar för Kundhanterade data Källor: FTP, RSS, SFTP, SMTP och SQL Server

## <a name="non-compliant-examples"></a>Icke-kompatibla exempel

Här följer några exempel som använder Gmail Connector med inbyggda utlösare och åtgärder eller hanterade anslutningar som inte har godkänts av Google:

* Den här Logic-appen använder Gmail-anslutningen med den inbyggda HTTP-utlösaren:

  ![Icke-kompatibel Logic-app-exempel 1](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-1.png)
  
  Logic app använder också Google Calendar Connector, som är godkänd.

* Den här Logic-appen använder Gmail-anslutningen med Azure Blob Storage-anslutningen:

  ![Icke-kompatibel Logic-app-exempel 2](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-2.png)

* Den här Logic-appen använder Gmail-anslutningen med Twitter-kopplingen:

  ![Icke-kompatibel Logic-app-exempel 3](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-3.png)

Den senaste informationen finns i [Gmail Connectors tekniska referens dokumentation](/connectors/gmail/).

<a name="update-affected-workflows"></a>

## <a name="steps-for-affected-logic-apps"></a>Steg för berörda Logic Apps

Om du behöver använda Gmail-anslutaren med ett Gmail-konto och Google-icke-godkända anslutningar i en Logic app, kan du skapa en egen Google-app för personlig eller intern användning i företaget. I det här scenariot är det de övergripande steg som du måste vidta:

1. Skapa en Google client-app med hjälp av [Google API-konsolen](https://console.developers.google.com).

1. I Gmail-anslutningsprogrammet använder du klient-ID och klientens hemliga värden från din Google client-app.

Mer information finns i dokumentationen om [Gmail Connectors tekniska referenser](/connectors/gmail/#authentication-and-bring-your-own-application).

### <a name="create-google-client-app"></a>Skapa Google client-app

Om du vill konfigurera ett projekt för din klient app använder du [guiden Google API-konsol](https://console.developers.google.com/start/api?id=gmail&credential=client_key) och följer anvisningarna. Alternativt kan du läsa anvisningarna i [Gmail Connectors tekniska referens dokumentation](/connectors/gmail/#authentication-and-bring-your-own-application).

När du är klar ser skärmen ut som i det här exemplet, förutom att du har dina egna **klient-ID** och värden för **klient hemlighet** , som du senare använder i din Logic-app.

![Klient-ID och klient hemlighet för din Google client-app](./media/connectors-google-data-security-privacy-policy/google-api-console.png)

### <a name="use-client-app-settings-in-logic-app"></a>Använd inställningar för klient program i Logic app

Följ dessa steg om du vill använda klient-ID och klient hemlighet från din Google client-app i din Gmail-utlösare eller åtgärd:

1. I [Azure Portal](https://portal.azure.com)öppnar du din Logic app i Logic Apps designer.

1. Om du lägger till en ny Gmail-utlösare eller åtgärd och skapar en helt ny anslutning fortsätter du till nästa steg. Annars, i Gmail-utlösaren eller åtgärden, väljer du **ändra anslutning**  >  **Lägg till ny**, till exempel:

   ![Välj "ändra anslutning" > Lägg till ny "](./media/connectors-google-data-security-privacy-policy/change-gmail-connection.png)

1. Ange din anslutnings information, till exempel:

   ![Ange anslutnings information](./media/connectors-google-data-security-privacy-policy/authentication-type-bring-your-own.png)

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Autentiseringstyp** | **Ta med ditt eget program** | Anger att du ska använda din egen klient-app för autentisering. |
   | **Klient-ID** | <*klient-ID*> | Klient-ID från din Google client-app |
   | **Klient hemlighet** | <*klient hemlighet*> | Klient hemligheten från din Google client-app |
   ||||

1. När du är klar väljer du **Logga** in.

   En sida visas som visar den klient app som du har skapat. Om du använder ett Gmail-användarkonto kan du få en sida som visar att din klient app inte verifieras av Google och du uppmanas att först tillåta åtkomst till ditt Google-konto.

   ![Begär åtkomst till ditt Google-konto](./media/connectors-google-data-security-privacy-policy/allow-access-authorized-domain.png)

1. Om det behövs väljer du **Tillåt**.

   Du kan nu använda Gmail Connector utan begränsningar i din Logic app.

## <a name="next-steps"></a>Nästa steg

Läs mer om [Gmail Connector](/connectors/gmail/)

