---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 03/02/2018
ms.openlocfilehash: 1aabc1b33722c571c6519b6620fb62211a44c2e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95999558"
---
1. Skapa en tom Logic-app i [Azure Portal](https://portal.azure.com). 

2. I Logic Apps designer anger du "GitHub" som filter. 

3. Välj GitHub-anslutningen och den utlösare som du vill använda.

   ![Välj GitHub-anslutningen och en utlösare](./media/connectors-create-api-github/github-connector.png)

   > [!NOTE]
   > Alla Logic app-arbetsflöden måste börja med en utlösare. Du kan bara välja åtgärder när ditt logik arbets flöde redan börjar med en utlösare. 

4. Om du inte redan har skapat en anslutning väljer du **Logga** in så att du kan ange dina GitHub-autentiseringsuppgifter när du uppmanas till det.  

   ![Logga in med dina GitHub-autentiseringsuppgifter](./media/connectors-create-api-github/github-connector-sign-in-credentials.png)

   Din Logic app använder dessa autentiseringsuppgifter för att godkänna anslutning och åtkomst till data för ditt GitHub-konto. 

5. Ange ditt GitHub användar namn och lösen ord och bekräfta sedan din auktorisering.

   ![Ange autentiseringsuppgifter och bekräfta auktorisering](./media/connectors-create-api-github/github-connector-authorize.png)   

   Din anslutning skapas nu i Azure Portal och är redo att användas.

6. Fortsätt definiera ditt Logic app-arbetsflöde.

   ![Lägg till fler åtgärder i ditt Logic app-arbetsflöde](./media/connectors-create-api-github/github-connector-logic-app.png)

