---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: ceb2626a43ed44338bb0faad475ae2333af2de9e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
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