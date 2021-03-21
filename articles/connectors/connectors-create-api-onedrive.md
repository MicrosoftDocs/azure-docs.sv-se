---
title: Komma åt och hantera filer i Microsoft OneDrive
description: Ladda upp och hantera filer i OneDrive genom att skapa automatiserade arbets flöden i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/18/2016
tags: connectors
ms.openlocfilehash: 9fe4988b5499943f4b11ec5d640209ceb68e84ec
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87040238"
---
# <a name="access-and-manage-files-in-onedrive-connector-by-using-azure-logic-apps"></a>Komma åt och hantera filer i OneDrive-anslutning med hjälp av Azure Logic Apps

Genom att använda [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [OneDrive-anslutningen](/connectors/onedriveconnector/)kan du skapa automatiserade uppgifter och arbets flöden för att hantera dina filer, inklusive Ladda upp, Hämta, ta bort filer och mycket annat. Med OneDrive kan du utföra följande uppgifter:

* Bygg ditt arbets flöde genom att lagra filer i OneDrive eller uppdatera befintliga filer i OneDrive. 
* Använd utlösare för att starta arbets flödet när en fil skapas eller uppdateras i OneDrive.
* Använd åtgärder för att skapa en fil, ta bort en fil med mera. Till exempel, när ett nytt Office 365-e-postmeddelande tas emot med en bifogad fil (en utlösare), skapar du en ny fil i OneDrive (en åtgärd).

Den här artikeln visar hur du använder OneDrive-anslutningen i en Logic app och listar utlösare och åtgärder.

Mer information om Logic Apps finns i [Vad är](../logic-apps/logic-apps-overview.md) Logi Kap par och [skapa en Logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-onedrive"></a>Anslut till OneDrive

Innan din Logi Kap par kan komma åt en tjänst skapar du först en *anslutning* till tjänsten. En anslutning ger anslutning mellan en Logic app och en annan tjänst. Om du till exempel vill ansluta till OneDrive måste du först ha en OneDrive- *anslutning*. Om du vill skapa en anslutning anger du de autentiseringsuppgifter som du använder normalt för att få åtkomst till den tjänst som du vill ansluta till. Så, med OneDrive, anger du autentiseringsuppgifterna till ditt OneDrive-konto för att skapa anslutningen.

### <a name="create-the-connection"></a>Skapa anslutningen

[!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]

## <a name="use-a-trigger"></a>Använda en utlösare

En utlösare är en händelse som kan användas för att starta arbets flödet som definierats i en Logic app. Utlöser "avsökning"-tjänsten med ett intervall och en frekvens som du vill använda. [Läs mer om utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. I Logic App Designer skriver `onedrive` du för att hämta en lista över utlösare:  

   ![En dialog ruta med rubriken "Visa Microsoft Managed A s I" har en ruta som innehåller "OneDrive". Nedan visas en lista över fyra utlösare. Den första av dessa är "OneDrive-när en fil skapas". Den andra, "OneDrive-när en fil ändras", har marker ATS.](./media/connectors-create-api-onedrive/onedrive-1.png)

2. Välj **när en fil ändras**. Om det redan finns en anslutning väljer du knappen Visa väljare för att välja en mapp.

   ![En dialog ruta som heter "när en fil ändras" har en ruta med namnet "mapp" med en associerad bläddringsknappen.](./media/connectors-create-api-onedrive/sample-folder.png)

   Om du uppmanas att logga in anger du inloggnings informationen för att skapa anslutningen. [Skapa anslutningen](connectors-create-api-onedrive.md#create-the-connection) i den här artikeln som beskriver stegen.

   I det här exemplet körs Logic-appen när en fil i den mapp du väljer uppdateras. Om du vill se resultatet av den här utlösaren lägger du till en annan åtgärd som skickar ett e-postmeddelande. Lägg till exempel till Office 365 Outlook *skicka en e-* poståtgärd som skickar ett e-postmeddelande till dig när en fil uppdateras.

3. Välj knappen **Redigera** och ange värdena för **frekvens** och **intervall** . Om du till exempel vill att utlösaren ska avsöka var 15: e minut anger du **frekvensen** till **minut** och anger **intervallet** till **15**. 

   ![En dialog ruta med namnet "när en fil ändras" visar fem rutor märkta: "mapp", "frekvens", "intervall", "tidszon" och "START tid". Det finns List Rute listor för fälten "frekvens" och "tidszon".](./media/connectors-create-api-onedrive/trigger-properties.png)

4. **Spara** ändringarna (övre vänstra hörnet i verktygsfältet). Din Logic app sparas och kan aktive ras automatiskt.

## <a name="use-an-action"></a>Använd en åtgärd

En åtgärd är en åtgärd som utförs av arbets flödet som definierats i en Logic app. [Läs mer om åtgärder](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Välj plus tecknet. Du ser flera alternativ: **Lägg till en åtgärd**, **Lägg till ett villkor** eller något av de **fler** alternativen.

   ![En skärm bild visar fyra knappar: "+ nytt steg", "lägga till en åtgärd", "Lägg till ett villkor" och "... Mer ".](./media/connectors-create-api-onedrive/add-action.png)

2. Välj **Lägg till en åtgärd**.

3. I rutan Sök skriver `onedrive` du för att hämta en lista över alla tillgängliga åtgärder.

   ![En dialog ruta med rubriken "Visa Microsoft Managed A s I" har en ruta som innehåller "OneDrive". Nedan visas en lista över åtta åtgärder. Den första är "OneDrive-skapa fil" och är markerad.](./media/connectors-create-api-onedrive/onedrive-actions.png) 

4. I vårt exempel väljer du **OneDrive-skapa fil**. Om det redan finns en anslutning väljer du **mappsökvägen** för att placera filen, anger **fil namnet** och väljer det **fil innehåll** som du vill använda:  

   ![En dialog ruta med namnet "skapa fil" visar tre rutor med namnet "MAPPSÖKVÄG", "fil namn" och "MAPPINNEHÅLL". Det finns en katalogs bläddringsknappen bredvid rutan "MAPPSÖKVÄG".](./media/connectors-create-api-onedrive/sample-action.png)

   Om du uppmanas att ange anslutnings information anger du informationen för att [skapa anslutningen enligt beskrivningen](#create-the-connection) i det här avsnittet.

   I det här exemplet skapar du en ny fil i en OneDrive-mapp. Du kan använda utdata från en annan utlösare för att skapa OneDrive-filen. Lägg till exempel till Office 365 Outlook *när ett nytt e-postmeddelande kommer* till utlösare. Lägg sedan till åtgärden OneDrive *Skapa fil* som använder fälten bifogad fil och innehålls typ i en förhandling för att skapa den nya filen i OneDrive.

   ![En dialog ruta med namnet "för varje" har en ruta med etiketten "Välj utdata från föregående steg" som innehåller "bilagor". Det finns en dialog ruta för att skapa fil som täcker resten av rutorna för varje, med rutor som är märkta "MAPPSÖKVÄG", "fil namn" och "filinnehåll". ](./media/connectors-create-api-onedrive/foreach-action.png)

5. **Spara** ändringarna (övre vänstra hörnet i verktygsfältet). Din Logic app sparas och kan aktive ras automatiskt.

## <a name="connector-specific-details"></a>Anslutningsspecifika Detaljer

Visa eventuella utlösare och åtgärder som definierats i Swagger och se även eventuella begränsningar i [anslutnings informationen](/connectors/onedriveconnector/).

## <a name="next-steps"></a>Nästa steg

* [Anslutningsprogram för Azure Logic Apps](apis-list.md)