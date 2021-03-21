---
title: Skapa Logic app-arbetsflöden snabbare genom att använda fördefinierade mallar
description: Skapa snabbt Logic app-arbetsflöden genom att använda fördefinierade mallar som tillhandahålls av Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 10/15/2017
ms.openlocfilehash: 42c592e6aede4537dc983fd2cff043a878f81f1c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "100593095"
---
# <a name="create-logic-app-workflows-from-prebuilt-templates"></a>Skapa logikappsarbetsflöden från fördefinierade mallar

För att komma igång med att skapa arbets flöden snabbare, innehåller Logic Apps mallar, som är förbyggda Logic Apps som följer vanliga mönster. Använd de här mallarna som de har angett eller redigera dem så att de passar ditt scenario.

Här följer några Mallkategorier:

| Malltyp | Beskrivning | 
| ------------- | ----------- | 
| Mallar för företags moln | För integrering av Azure Blob, Dynamics CRM, Salesforce, Box och innehåller andra anslutningar för företagets moln behov. Du kan till exempel använda dessa mallar för att organisera affärs leads eller säkerhetskopiera företagets fildata. | 
| Personliga produktivitets mallar | Förbättra den personliga produktiviteten genom att ställa in dagliga påminnelser, omvandla viktiga arbets objekt till att göra-listor och automatisera långvariga uppgifter till ett enda användar godkännande steg. | 
| Mallar för konsument moln | För att integrera sociala medie tjänster som Twitter, slack och e-post. Användbart för att stärka marknadsförings initiativ för sociala medier. Dessa mallar innehåller också uppgifter som moln kopiering, vilket ökar produktiviteten genom att spara tid på traditionellt återkommande uppgifter. | 
| Mallar för Enterprise integration Pack | För att konfigurera VETER (validera, extrahera, transformera, utöka, Route), ta emot ett X12 EDI-dokument via AS2 och transformera till XML och hantera X12-, EDIFACT-och AS2-meddelanden. | 
| Mallar för protokoll mönster | För att implementera protokoll mönster som Request-Response över HTTP och integreringar över FTP och SFTP. Använd de här mallarna som anges eller utveckla dem för komplexa protokoll mönster. | 
||| 

Om du inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar. Mer information om hur du skapar en Logic app finns i [skapa en Logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-logic-apps-from-templates"></a>Skapa Logic Apps från mallar

1. Om du inte redan har gjort det loggar du in på [Azure Portal](https://portal.azure.com "Azure Portal").

2. Välj **skapa en resurs**  >  **Enterprise-integration**  >  **Logic app** på huvud menyn i Azure.

   ![Azure Portal, Ny, Enterprise-integration, Logikapp](./media/logic-apps-create-logic-apps-from-templates/azure-portal-create-logic-app.png)

3. Skapa din logikapp med inställningarna i tabellen under den här bilden:

   ![Tillhandahålla information om logikappar](./media/logic-apps-create-logic-apps-from-templates/logic-app-settings.png)

   | Inställning | Värde | Beskrivning | 
   | ------- | ----- | ----------- | 
   | **Namn** | *logikappens-namn* | Ange ett unikt namn för logikappen. | 
   | **Prenumeration** | *namn-på-Azure-prenumerationen* | Välj den Azure-prenumeration som du vill använda. | 
   | **Resursgrupp** | *namn-på-Azure-resursgruppen* | Skapa eller Välj en [Azure-resurs grupp](../azure-resource-manager/management/overview.md) för den här Logic-appen och organisera alla resurser som är associerade med den här appen. | 
   | **Plats** | *din-Azure-datacenter-region* | Välj datacenterregion för att distribuera logikappen, till exempel USA, västra. | 
   | **Log Analytics** | **Av** (standard) eller **på** | Konfigurera [diagnostikloggning](../logic-apps/monitor-logic-apps-log-analytics.md) för din Logic app med hjälp av [Azure Monitor loggar](../azure-monitor/logs/log-query-overview.md). Kräver att du redan har en Log Analytics-arbetsyta. | 
   |||| 

4. När du är klar väljer du **Fäst på instrumentpanelen**. På så sätt visas logikappen automatiskt på din Azure-instrumentpanel och öppnas efter distributionen. Välj **Skapa**.

   > [!NOTE]
   > Om du inte vill fästa din logikapp måste du manuellt hitta och öppna logikappen så att du kan fortsätta.

   När Azure har distribuerat din logikapp öppnas Logic Apps Designer och visar en sida med en introduktionsvideo. 
   Under videon finns mallar för vanliga logikappmönster. 

5. Rulla förbi introduktions videon och vanliga utlösare till **mallar**. Välj en fördefinierad mall. Exempel:

   ![Välj en mall för Logic app](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   > [!TIP]
   > Om du vill skapa din Logic app från grunden väljer du **Tom Logic app**.

   När du väljer en fördefinierad mall kan du Visa mer information om mallen. 
   Exempel:

   ![Välj en fördefinierad mall](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

6. Välj **Använd den här mallen** om du vill fortsätta med den valda mallen. 

7. Baserat på kopplingarna i mallen uppmanas du att göra något av följande:

   * Logga in med dina autentiseringsuppgifter för system eller tjänster som mallen refererar till.

   * Skapa anslutningar för tjänster eller system som refereras av mallen. Om du vill skapa en anslutning anger du ett namn för anslutningen och väljer sedan den resurs som du vill använda, om det behövs. 

   * Om du redan har konfigurerat de här anslutningarna väljer du **Fortsätt**.

   Exempel:

   ![Skapa anslutningar](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   När du är klar öppnas din Logic app och visas i Logic Apps designer.

   > [!TIP]
   > Om du vill återgå till mal Lav visaren väljer du **mallar** i verktygsfältet designer. Den här åtgärden tar bort alla ändringar som inte sparats, så ett varnings meddelande visas för att bekräfta din begäran.

8. Fortsätt att skapa din Logic app.

   > [!NOTE] 
   > Många mallar innehåller kopplingar som redan har förifyllda nödvändiga egenskaper. Vissa mallar kan dock fortfarande kräva att du anger värden innan du kan distribuera Logic app på rätt sätt. Om du försöker distribuera utan att fylla i de saknade egenskaps fälten visas ett fel meddelande. 

## <a name="update-logic-apps-with-templates"></a>Uppdatera Logic Apps med mallar

1. I [Azure Portal](https://portal.azure.com "Azure Portal")kan du söka efter och öppna din Logic app i th Logic App Designer.

2. I verktygsfältet designer väljer du **mallar**. Den här åtgärden tar bort alla ändringar som inte sparats, så ett varnings meddelande visas så att du kan bekräfta att du vill fortsätta. Bekräfta genom att välja **OK**. Exempel:

   ![Välj "mallar"](./media/logic-apps-create-logic-apps-from-templates/logic-app-update-existing-with-template.png)

3. Rulla förbi introduktions videon och vanliga utlösare till **mallar**. Välj en fördefinierad mall. Exempel:

   ![Välj en mall för Logic app](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   När du väljer en fördefinierad mall kan du Visa mer information om mallen. 
   Exempel:

   ![Välj en fördefinierad mall](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

4. Välj **Använd den här mallen** om du vill fortsätta med den valda mallen. 

5. Baserat på kopplingarna i mallen uppmanas du att göra något av följande:

   * Logga in med dina autentiseringsuppgifter för system eller tjänster som mallen refererar till.

   * Skapa anslutningar för tjänster eller system som refereras av mallen. Om du vill skapa en anslutning anger du ett namn för anslutningen och väljer sedan den resurs som du vill använda, om det behövs. 

   * Om du redan har konfigurerat de här anslutningarna väljer du **Fortsätt**.

   ![Skapa anslutningar](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Din Logic app öppnas nu och visas i Logic Apps designer.

8. Fortsätt att skapa din Logic app. 

   > [!TIP]
   > Om du inte har sparat ändringarna kan du ignorera ditt arbete och återgå till din tidigare Logic-app. I verktygsfältet designer väljer du **Ignorera**.

> [!NOTE] 
> Många mallar innehåller kopplingar som redan har förifyllda nödvändiga egenskaper. Vissa mallar kan dock fortfarande kräva att du anger värden innan du kan distribuera Logic app på rätt sätt. Om du försöker distribuera utan att fylla i de saknade egenskaps fälten visas ett fel meddelande.

## <a name="deploy-logic-apps-built-from-templates"></a>Distribuera Logic Apps som skapats utifrån mallar

När du har gjort dina ändringar i mallen kan du spara ändringarna. Den här åtgärden publicerar också den logiska appen automatiskt.

Välj **Spara** i designerverktygsfältet.

![Spara och publicera din Logic app](./media/logic-apps-create-logic-apps-from-templates/logic-app-save.png)  

## <a name="get-support"></a>Få support

* För frågor, besök [sidan Microsoft Q&en fråga för Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att skapa Logi Kap par med exempel, scenarier, kund berättelser och genom gångar.

> [!div class="nextstepaction"]
> [Granska exempel på logiska appar, scenarier och genom gångar](../logic-apps/logic-apps-examples-and-scenarios.md)
