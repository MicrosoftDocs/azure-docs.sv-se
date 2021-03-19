---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 07/21/2020
ms.openlocfilehash: 27df83552b450b7657c0595d1d419001df98148c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87040236"
---
## <a name="prerequisites"></a>Förutsättningar

* Ett [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) -konto 

Innan du kan använda ditt OneDrive-konto med Logic Apps måste du auktorisera Logic Apps för att ansluta till ditt OneDrive-konto i Azure Portal.

Följ de här stegen för att auktorisera Logic Apps att ansluta till ditt OneDrive-konto:  

1. Logga in på Azure-portalen. 

1. Under **Azure-tjänster** väljer du **Logic Apps**. Välj sedan namnet på din Logic-app i listan.

1. På din Logic Apps-meny väljer du **Logic Apps designer** under **utvecklingsverktyg**.

1. I Logic Apps designer väljer du **Visa Microsoft-hanterade API: er** i list rutan och anger sedan *OneDrive* i sökrutan. Välj den utlösare eller åtgärd som ska användas:

   ![Skärm bild av Logic Apps designer, som visar en lista över de OneDrive API-åtgärder som ska läggas till.](./media/connectors-create-api-onedrive/onedrive-1.png)

2. Om du inte redan har skapat några anslutningar till OneDrive, följer du anvisningarna för att logga in med dina OneDrive-autentiseringsuppgifter:  

   ![Skärm bild av Logic Apps designer som visar inloggnings tolken för OneDrive API.](./media/connectors-create-api-onedrive/onedrive-2.png)

3. Välj **Logga** in och ange ditt användar namn och lösen ord. Välj **Logga** in: 

   ![Skärm bild av Microsoft-konto inloggnings sida för OneDrive API-auktorisering.](./media/connectors-create-api-onedrive/onedrive-3.png)   

    Dessa autentiseringsuppgifter används för att auktorisera din Logic app för att komma åt data i ditt OneDrive-konto. 

4. Välj **Ja** för att godkänna att Logic-appen använder ditt OneDrive-konto:  

   ![Skärm bild av Microsoft-konto auktorisering för Logic Apps som visar tillåtna åtgärder.](./media/connectors-create-api-onedrive/onedrive-4.png)   
   
5. Anslutningen visas nu i steget. Välj Spara och fortsätt sedan skapa din Logic app. 

   ![Skärm bild av Logic Apps designer som visar åtgärds redigeraren med OneDrive API-anslutning.](./media/connectors-create-api-onedrive/onedrive-5.png)
