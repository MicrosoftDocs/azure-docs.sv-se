---
title: Hantera data, kalkyl blad och tabeller i Excel Online
description: Hantera data i kalkyl blad och tabeller i Excel Online för företag eller Excel Online för OneDrive genom att använda Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: 097db6683127b410e713be53e6de838cf7734ddc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91400731"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>Hantera Excel Online-data med Azure Logic Apps

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [Excel Online för Business](/connectors/excelonlinebusiness/) Connector eller [Excel Online för OneDrive](/connectors/excelonline/) Connector kan du skapa automatiserade uppgifter och arbets flöden baserat på dina data i Excel Online för företag eller OneDrive. Den här anslutningen innehåller åtgärder som hjälper dig att arbeta med dina data och hantera kalkyl blad, till exempel:

* Skapa nya kalkyl blad och tabeller.
* Hämta och hantera kalkyl blad, tabeller och rader.
* Lägg till enstaka rader och nyckel kolumner.

Du kan sedan använda utdata från dessa åtgärder med åtgärder för andra tjänster. Om du till exempel använder en åtgärd som skapar kalkyl blad varje vecka kan du använda en annan åtgärd som skickar ett bekräftelse meddelande med hjälp av Office 365 Outlook Connector.

Om du är nybörjare på Logi Kap par kan du läsa om [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> [Excel Online för företag](/connectors/excelonlinebusiness/) och [Excel Online för OneDrive](/connectors/excelonline/) -kopplingar fungerar med Azure Logic Apps och skiljer sig från [Excel Connector för PowerApps](/connectors/excel/).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Ett [arbets-eller skol konto](https://www.office.com/) för ditt arbets konto eller personliga Microsoft-konto

  Dina Excel-data kan finnas som en fil med kommaavgränsade värden (CSV) i en lagringsmapp, till exempel i OneDrive. 
  Du kan också använda samma CSV-fil med den [fasta fil anslutningen](../logic-apps/logic-apps-enterprise-integration-flatfile.md).

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Den Logic app där du vill komma åt dina Excel Online-data. Den här kopplingen tillhandahåller endast åtgärder, så för att starta din Logi Kap par väljer du en separat utlösare, till exempel utlösaren **upprepning** .

## <a name="add-excel-action"></a>Lägg till Excel-åtgärd

1. I [Azure Portal](https://portal.azure.com)öppnar du din Logic app i Logic App Designer, om den inte redan är öppen.

1. Under utlösaren väljer du **nytt steg**.

1. Skriv "Excel" som filter i rutan Sök. Under listan åtgärder väljer du den åtgärd som du vill använda.

   > [!NOTE]
   > Logic App Designer kan inte läsa in tabeller som har 100 eller fler kolumner. Minska om möjligt antalet kolumner i den markerade tabellen så att designern kan läsa in tabellen.

1. Logga in på ditt arbets-eller skol konto om du uppmanas till det.

   Dina autentiseringsuppgifter auktoriserar din Logic app för att skapa en anslutning till Excel Online och komma åt dina data.

1. Fortsätt att ange nödvändig information för den valda åtgärden och skapa din Logic Apps-arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

För teknisk information, till exempel utlösare, åtgärder och gränser, som beskrivs av kopplingens OpenAPI-filer (tidigare Swagger), se följande kopplings referens sidor:

* [Excel Online för företag](/connectors/excelonlinebusiness/)
* [Excel Online för OneDrive](/connectors/excelonline/)

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)
