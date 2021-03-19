---
title: Hämta fler objekt eller poster med sid brytning
description: Ställ in sid brytning för att överskrida standard sid storleks gränsen för kopplings åtgärder i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: d46bf711a46e27b81a1284b5fc55cf403b7da048
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87090271"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>Hämta mer data, objekt eller poster med hjälp av sid brytning i Azure Logic Apps

När du hämtar data, objekt eller poster med hjälp av en kopplings åtgärd i [Azure Logic Apps](../logic-apps/logic-apps-overview.md)kan du få resultat uppsättningar så att åtgärden inte returnerar alla resultat på samma gång. Med vissa åtgärder kan antalet resultat överskrida kopplingens standard sid storlek. I det här fallet returnerar åtgärden bara den första resultat sidan. Till exempel är standard sid storleken för åtgärden **Hämta rader** i SQL Server Connector 2048, men kan variera beroende på andra inställningar.

Med vissa åtgärder kan du aktivera en *sid brytnings* inställning så att din Logi Kap par kan hämta fler resultat upp till sid brytnings gränsen, men returnera resultaten som ett enda meddelande när åtgärden har slutförts. När du använder sid brytning måste du ange ett *tröskelvärde* , vilket är det mål antal resultat som du vill att åtgärden ska returnera. Åtgärden hämtar resultat tills du når det angivna tröskelvärdet. När det totala antalet objekt är mindre än det angivna tröskelvärdet, hämtar åtgärden alla resultat.

Om du aktiverar sid brytnings inställningen hämtas sidor med resultat baserat på kopplingens sid storlek. Det här beteendet innebär ibland att du kan få fler resultat än det angivna tröskelvärdet. Till exempel när du använder åtgärden SQL Server **Hämta rader** , som har stöd för sid brytnings inställningen:

* Åtgärdens standard sid storlek är 2048 poster per sida.
* Anta att du har 10 000 poster och anger 5000 poster som minimum.
* Sid brytning hämtar sidor med poster, så för att få minst det angivna minimivärdet returnerar åtgärden 6144 poster (3 sidor x 2048 poster), inte 5000-poster.

Här är en lista med bara några av kopplingarna där du kan överskrida standard sid storleken för vissa åtgärder:

* [Azure Blob Storage](/connectors/azureblob/)
* [Dynamics 365](/connectors/dynamicscrmonline/)
* [Excel](/connectors/excel/)
* [HTTP](../connectors/connectors-native-http.md)
* [IBM DB2](/connectors/db2/)
* [Microsoft Teams](/connectors/teams/)
* [Oracle Database](/connectors/oracle/)
* [Salesforce](/connectors/salesforce/)
* [SharePoint](/connectors/sharepointonline/)
* [SQL Server](/connectors/sql/)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration ännu kan du [Registrera dig för ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/).

* Logic app och den åtgärd där du vill aktivera sid brytning. Om du inte har en logisk app, se [snabb start: skapa din första Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="turn-on-pagination"></a>Aktivera sid brytning

Ta reda på om en åtgärd stöder sid brytning i Logic App Designer genom att kontrol lera åtgärdens inställningar för **sid brytnings** inställningen. Det här exemplet visar hur du aktiverar sid brytning i SQL Servers åtgärd för **Hämta rader** .

1. I åtgärdens övre högra hörn väljer du knappen med tre punkter (**...**) och väljer **Inställningar**.

   ![Öppna åtgärdens inställningar](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   Om åtgärden stöder sid brytning, visar åtgärden inställningen för **sid brytning** .

1. Ändra **sid brytnings** **inställningen från till** **på**. I egenskapen **Threshold (tröskelvärde** ) anger du ett heltals värde för det mål antal resultat som du vill att åtgärden ska returnera.

   ![Ange det minsta antalet resultat som ska returneras](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. När du är klar väljer du **klar**.

## <a name="workflow-definition---pagination"></a>Arbets flödes definition – sid brytning

När du aktiverar sid brytning för en åtgärd som stöder den här funktionen, innehåller din Logic Apps-definition `"paginationPolicy"` egenskapen tillsammans med `"minimumItemCount"` egenskapen i den åtgärdens `"runtimeConfiguration"` egenskap, till exempel:

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.testuri.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "paginationPolicy": {
            "minimumItemCount": 1000
         }
      },
      "type": "Http"
   }
},
```

## <a name="get-support"></a>Få support

För frågor, besök [sidan Microsoft Q&en fråga för Azure Logic Apps](/answers/topics/azure-logic-apps.html).
