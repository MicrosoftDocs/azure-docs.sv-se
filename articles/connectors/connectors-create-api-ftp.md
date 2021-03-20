---
title: Anslut till FTP-Server
description: Automatisera aktiviteter och arbets flöden som skapar, övervakar och hanterar filer på en FTP-server med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 12/15/2019
tags: connectors
ms.openlocfilehash: f4cad2b658547d56d00efdd5e1496110f8e4a5e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95999593"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>Skapa, övervaka och hantera FTP-filer med hjälp av Azure Logic Apps

Med Azure Logic Apps och FTP-anslutningen kan du skapa automatiserade uppgifter och arbets flöden som skapar, övervakar, skickar och tar emot filer via ditt konto på en FTP-server, tillsammans med andra åtgärder, till exempel:

* Övervaka när filer läggs till eller ändras.
* Hämta, skapa, kopiera, uppdatera, lista och ta bort filer.
* Hämta fil innehåll och metadata.
* Extrahera arkiv till mappar.

Du kan använda utlösare som får svar från FTP-servern och göra utdata tillgängliga för andra åtgärder. Du kan använda kör åtgärder i dina Logi Kap par för att hantera filer på din FTP-server. Du kan också använda andra åtgärder för att använda utdata från FTP-åtgärder. Om du till exempel regelbundet hämtar filer från FTP-servern kan du skicka e-post om filerna och deras innehåll med hjälp av Office 365 Outlook Connector eller Outlook.com Connector. Om du inte har använt Logic Apps igen kan du läsa mer [Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="limitations"></a>Begränsningar

* FTP-anslutningen har endast stöd för explicit FTP över TLS/SSL (FTPS) och är inte kompatibel med implicit FTPS.

* FTP-åtgärder kan som standard läsa eller skriva filer som är *50 MB eller mindre*. För att hantera filer som är större än 50 MB stöder FTP-åtgärder [meddelande segment](../logic-apps/logic-apps-handle-large-messages.md). Åtgärden **Hämta fil innehåll** använder implicit segment.

* FTP-utlösare stöder inte segment. När du begär fil innehåll väljer utlösare endast filer som är 50 MB eller mindre. Följ det här mönstret om du vill hämta filer som är större än 50 MB:

  * Använd en FTP-utlösare som returnerar fil egenskaper, till exempel **när en fil läggs till eller ändras (endast egenskaper)**.

  * Följ utlösaren med åtgärden **Hämta fil innehåll** i FTP, som läser den fullständiga filen och som implicit använder segment.

* Om du har en lokal FTP-server kan du överväga att skapa en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) eller använda [Azure App Service hybrid anslutningar](../app-service/app-service-hybrid-connections.md), som båda ger dig åtkomst till lokala data källor utan att använda en lokal datagateway.

## <a name="how-ftp-triggers-work"></a>Så här fungerar FTP-utlösare

FTP-utlösare fungerar genom att avsöka FTP-filsystemet och leta efter en fil som har ändrats sedan den senaste avsökningen. Med vissa verktyg kan du bevara tidsstämpeln när filerna ändras. I dessa fall måste du inaktivera den här funktionen så att utlösaren kan fungera. Här följer några vanliga inställningar:

| SFTP-klient | Action |
|-------------|--------|
| WinSCP | Gå till **alternativ**  >  **Inställningar**  >  **överför**  >  **Redigera**  >  **bevara tidsstämpel**  >  **inaktivera** |
| FileZilla | Gå till **överför**  >  **bevara tidsstämplar för överförda filer**  >  **inaktivera** |
|||

När en utlösare hittar en ny fil, kontrollerar utlösaren att den nya filen är fullständig och inte delvis skriven. En fil kan till exempel ha ändringar som pågår när utlösaren kontrollerar fil servern. För att undvika att returnera en delvis skriven fil, noterar utlösaren tidsstämpeln för filen som har nyligen gjorda ändringar, men returnerar inte omedelbart den filen. Utlösaren returnerar filen endast när servern avsöks igen. Ibland kan det här problemet orsaka en fördröjning som är upp till två gånger utlösaren för avsöknings intervall.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Din FTP-värds server adress och kontoautentiseringsuppgifter

  FTP-anslutningen kräver att FTP-servern är tillgänglig från Internet och att den fungerar i *passivt* läge. Dina autentiseringsuppgifter gör att din Logic app kan skapa en anslutning och komma åt ditt FTP-konto.

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Den Logic-app där du vill komma åt ditt FTP-konto. Starta med en FTP-utlösare genom att [skapa en tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en FTP-åtgärd startar du din Logic-app med en annan utlösare, till exempel utlösaren **upprepning** .

## <a name="connect-to-ftp"></a>Anslut till FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna din Logic app i Logic App Designer.

1. För tomma Logic Apps, i sökrutan, anger du `ftp` som filter. Välj den utlösare som du vill använda från listan **utlösare** .

   \- eller -

   För befintliga Logic Apps, under det sista steget där du vill lägga till en åtgärd, väljer du **nytt steg** och sedan **Lägg till en åtgärd**. I rutan Sök anger `ftp` du som filter. I listan **åtgärder** väljer du den åtgärd som du vill använda.

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

1. Ange din anslutnings information och välj **skapa**.

1. Ange informationen för den valda utlösaren eller åtgärden och fortsätt att skapa din Logic Apps-arbetsflöde.

## <a name="examples"></a>Exempel

<a name="file-added-modified"></a>

### <a name="add-ftp-trigger"></a>Lägg till FTP-utlösare

Utlösaren **när en fil läggs till eller ändras (endast egenskaper)** startar ett Logic app-arbetsflöde när utlösaren upptäcker att en fil läggs till eller ändras på en FTP-server. Du kan till exempel lägga till ett villkor som kontrollerar filens innehåll och bestämmer om innehållet ska hämtas baserat på om innehållet uppfyller ett visst villkor. Slutligen kan du lägga till en åtgärd som hämtar filens innehåll och lägga till innehållet i en annan mapp på SFTP-servern.

Du kan till exempel använda den här utlösaren för att övervaka en FTP-mapp för nya filer som beskriver kund beställningar. Du kan sedan använda en FTP-åtgärd, till exempel **Hämta metadata för filen** för att hämta egenskaperna för den nya filen och sedan använda **Hämta fil innehåll** för att hämta innehållet från filen för vidare bearbetning och lagra den i en order databas.

Här är ett exempel som visar hur du använder den **när en fil läggs till eller ändras (endast egenskaper)** utlösare.

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna din Logic app i Logic App Designer, om du inte redan har gjort det.

1. För tomma Logic Apps, i sökrutan, anger du `ftp` som filter. Välj den här utlösaren under listan utlösare: **när ett arkiverat läggs till eller ändras (endast egenskaper)**

   ![Sök efter och välj FTP-utlösaren](./media/connectors-create-api-ftp/select-ftp-trigger-logic-app.png)

1. Ange nödvändig information för anslutningen och välj sedan **skapa**.

   Som standard överför den här anslutningen filer i text format. Om du vill överföra filer i binärformat, till exempel WHERE och när kodning används, väljer du **binär transport**.

   ![Skapa anslutning till FTP-Server](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)

1. I rutan **mapp** väljer du mappikonen så att en lista visas. Om du vill hitta den mapp som du vill övervaka för nya eller redigerade filer väljer du högerpilen ( **>** ), bläddrar till mappen och väljer sedan mappen.

   ![Sök efter och välj mapp att övervaka](./media/connectors-create-api-ftp/select-folder-ftp-trigger.png)

   Den valda mappen visas i rutan **mapp** .

   ![Den valda mappen visas i "Folder"-egenskapen](./media/connectors-create-api-ftp/selected-folder-ftp-trigger.png)

1. Spara logikappen. I verktygsfältet designer väljer du **Spara**.

Nu när din Logic app har en utlösare lägger du till de åtgärder som du vill köra när din Logi Kap par söker efter en ny eller redige rad fil. I det här exemplet kan du lägga till en FTP-åtgärd som hämtar det nya eller uppdaterade innehållet.

<a name="get-content"></a>

### <a name="add-ftp-action"></a>Lägg till FTP-åtgärd

Åtgärden **Hämta filens metadata** hämtar egenskaperna för en fil som finns på FTP-servern och åtgärden **Hämta fil innehåll** hämtar fil innehållet baserat på informationen om filen på din FTP-server. Du kan till exempel lägga till utlösaren från föregående exempel och dessa åtgärder för att hämta filens innehåll när filen har lagts till eller redigerats.

1. Under utlösaren eller andra åtgärder väljer du **nytt steg**.

1. I rutan Sök anger `ftp` du som filter. Under listan åtgärder väljer du den här åtgärden: **Hämta filens metadata**

   ![Välj åtgärden Hämta fil metadata](./media/connectors-create-api-ftp/select-get-file-metadata-ftp-action.png)

1. Om du redan har en anslutning till FTP-servern och kontot går du till nästa steg. Annars anger du nödvändig information för anslutningen och väljer sedan **skapa**.

   ![Skapa anslutning till FTP-Server](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. När åtgärden **Hämta filens metadata** visas klickar du i rutan **fil** så att listan med dynamiskt innehåll visas. Nu kan du välja egenskaper för utdata från föregående steg. I listan med dynamiskt innehåll under **Hämta metadata för filen** väljer du egenskapen **lista över filer-ID** som refererar till den samling där filen lades till eller uppdaterades.

   ![Hitta och välj egenskapen "lista över filer-ID"](./media/connectors-create-api-ftp/select-list-of-files-id-output.png)

   **Listan med filer-ID-** egenskaper visas nu i rutan **fil** .

   ![Vald "lista över filer-ID"-egenskap](./media/connectors-create-api-ftp/selected-list-file-ids-ftp-action.png)

1. Lägg nu till denna FTP-åtgärd: **Hämta fil innehåll**

   ![Sök efter och Välj åtgärden Hämta fil innehåll](./media/connectors-create-api-ftp/select-get-file-content-ftp-action.png)

1. När åtgärden **Hämta fil innehåll** visas klickar du i rutan **fil** så att listan med dynamiskt innehåll visas. Nu kan du välja egenskaper för utdata från föregående steg. I listan med dynamiskt innehåll under **Hämta metadata för filen** väljer du egenskapen **ID** som refererar till filen som har lagts till eller uppdaterats.

   ![Hitta och välj egenskapen "ID"](./media/connectors-create-api-ftp/get-file-content-id-output.png)

   Egenskapen **ID** visas nu i rutan **fil** .

   ![Den valda egenskapen "ID"](./media/connectors-create-api-ftp/selected-get-file-content-id-ftp-action.png)

1. Spara logikappen.

## <a name="test-your-logic-app"></a>Testa din Logic app

Om du vill kontrol lera att arbets flödet returnerar det innehåll som du förväntar dig lägger du till en annan åtgärd som skickar innehållet från den överförda eller uppdaterade filen.

1. Under åtgärden **Hämta fil innehåll** lägger du till en åtgärd som kan skicka filens innehåll. Det här exemplet lägger till åtgärden **Skicka ett e-postmeddelande** för Office 365 Outlook.

   ![Lägg till en åtgärd för att skicka e-post](./media/connectors-create-api-ftp/select-send-email-action.png)

1. När åtgärden visas anger du informationen och inkluderar de egenskaper som du vill testa. Du kan till exempel ta med egenskapen för **fil innehåll** som visas i listan med dynamiskt innehåll när du har valt **Se mer** i avsnittet **Hämta fil innehåll** .

   ![Ange information om e-poståtgärd](./media/connectors-create-api-ftp/selected-send-email-action.png)

1. Spara logikappen. Om du vill köra och utlösa Logic-appen väljer du **Kör** i verktygsfältet och lägger sedan till en fil i FTP-mappen som din Logic app nu övervakar.

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer teknisk information om den här anslutningen, till exempel utlösare, åtgärder och begränsningar som beskrivs av kopplingens Swagger-fil finns på [kopplingens referens sida](/connectors/ftpconnector/).

> [!NOTE]
> För logi Kap par i en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)använder den här anslutningens ISE-märkta version [ISE-meddelandets gränser](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) i stället.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)

