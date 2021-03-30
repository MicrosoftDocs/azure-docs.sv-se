---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: ceb2626a43ed44338bb0faad475ae2333af2de9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "67187575"
---
Kontrollera att du redan har [skapat ett Relay-namnområde ][namespace-how-to].

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla resurser** i menyn till vänster.
3. Välj det namnområde där du vill skapa hybridanslutningen. I det här fallet det **mynewns**.  
4. Under **Relay-namnområde** väljer du **Hybridanslutningar**.

    ![Skapa en hybridanslutning](./media/relay-create-hybrid-connection-portal/create-hc-1.png)

5. I fönstret för översikt av namnområde väljer du **+ Hybridanslutning**
   
    ![Välj hybridanslutningen](./media/relay-create-hybrid-connection-portal/create-hc-2.png)
6. Under **Skapa hybridanslutning** anger du ett värde för hybridanslutningens namn. Lämna de andra standardvärdena som de är.
   
    ![Välj ny](./media/relay-create-hybrid-connection-portal/create-hc-3.png)
7. Välj **Skapa**.

[namespace-how-to]: ../articles/service-bus-relay/relay-create-namespace-portal.md 