---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 07/21/2020
ms.openlocfilehash: 06a503e800309d8689735417931820d9f16a6e75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87103037"
---
## <a name="prerequisites"></a>Förutsättningar

* Ett [OneDrive för företag](https://OneDrive.com) -konto 

Innan du kan använda ditt OneDrive för företag-konto med Logic Apps måste du auktorisera Logic Apps för att ansluta till ditt OneDrive för företag-konto i Azure Portal.

Följ de här stegen för att auktorisera Logic Apps att ansluta till ditt OneDrive för företag-konto:  

1. Logga in på Azure-portalen. 

1. Under **Azure-tjänster** väljer du **Logic Apps**. Välj sedan namnet på din Logic-app i listan.

1. På din Logic Apps-meny väljer du **Logic Apps designer** under **utvecklingsverktyg**.

1. I Logic Apps designer väljer du **Visa Microsoft-hanterade API: er** i list rutan och anger sedan *OneDrive för företag* i sökrutan. Välj den utlösare eller åtgärd som ska användas:  

   ![Skärm bild av Logic Apps designer, som visar upprepnings utlösare med API-åtgärder för OneDrive för företag.](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-1.png)

2. Om du inte har skapat några anslutningar till OneDrive för företag tidigare, följer du anvisningarna för att ange dina autentiseringsuppgifter för OneDrive för företag. Dessa autentiseringsuppgifter används för att auktorisera din Logi Kap par åtkomst till ditt kontos data för OneDrive för företag:  

   ![Skärm bild av Logic Apps designer som visar inloggnings tolken för OneDrive för företag.](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-2.png)

3. Ange ditt användar namn och lösen ord för OneDrive för företag för att godkänna din Logic app:  

   ![Skärm bild av inloggnings sidan för OneDrive för företag med inloggnings prompt.](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-3.png)   

4. Anslutningen visas nu i steget. Välj Spara och fortsätt sedan skapa din Logic app. 

   ![Skärm bild av Logic Apps designer, som visar utlösare med OneDrive för företag-anslutning visas.](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-4.png)   
