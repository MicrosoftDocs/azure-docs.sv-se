---
title: Skapa en Azure-meddelandehubb med hjälp av Azure-portalen | Microsoft Docs
description: I den här självstudien lär du dig att skapa en Azure SQL-meddelandehubb med hjälp av Azure-portalen.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 08/04/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 141812e83653b458a2562bf73b70117c39bf7be0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92426555"
---
# <a name="quickstart-create-an-azure-notification-hub-in-the-azure-portal"></a>Snabb start: skapa en Azure Notification Hub i Azure Portal

Azure Notification Hubs innehåller en lättanvänd och uppskalad push-motor som gör det möjligt för dig att skicka meddelanden till valfri plattform (iOS, Android, Windows, Kindle, Baidu osv) från valfri serverdel (molnet eller lokalt). Mer information om tjänsten finns i [Vad är Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

I den här snabbstarten skapar du en meddelandehubb i Azure-portalen. Det första avsnittet innehåller steg för att skapa en Notification Hubs-namnrymd och en hubb i den namnrymden. Det andra avsnittet innehåller steg för att skapa en meddelandehubb i en befintlig Notification Hubs-namnrymd.

## <a name="create-a-namespace-and-a-notification-hub"></a>Skapa en namnrymd och en meddelandehubb

I det här avsnittet skapar du en namnrymd och en hubb i namnrymden.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

## <a name="create-a-notification-hub-in-an-existing-namespace"></a>Skapa en meddelandehubb i en befintlig namnrymd

I det här avsnittet skapar du en meddelandehubb i en befintlig namnrymd.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla tjänster** på den vänstra menyn, sök efter **Notification Hub** (Meddelandehubb), välj **stjärnan** (`*`) intill **Notification Hub Namespaces** (Namnrymder för meddelandehubb) för att lägga till den i avsnittet **FAVORITES** (Favoriter) på den vänstra menyn. Välj **Notification Hub Namespaces** (Namnrymder för meddelandehubb).

      ![Azure-portalen – välj Namnrymder för meddelandehubb](./media/create-notification-hub-portal/select-notification-hub-namespaces-all-services.png)
3. På sidan **Notification Hub Namespaces** (Namnrymder för meddelandehubb) väljer du din namnrymd i listan.

      ![Välj namnrymd i listan](./media/create-notification-hub-portal/select-namespace.png)
4. På sidan **Notification Hub Namespaces** (Namnrymder för meddelandehubb) väljer du **Add Hub** (Lägg till hubb) i verktygsfältet.

      ![Namnrymder för meddelandehubb – knappen Lägg till hubb](./media/create-notification-hub-portal/add-hub-button.png)
5. På sidan **New Notification Hub** (Ny meddelandehubb) anger du ett namn på meddelandehubben och väljer **OK**.

      ![Sidan Ny meddelandehubb -> ange ett namn på hubben](./media/create-notification-hub-portal/new-notification-hub-page.png)
6. Välj **Meddelanden** (klockikonen) längst upp för att se status för distributionen av den nya hubben. Välj **X** i det högra hörnet för att stänga meddelandefönstret.

      ![Distributionsmeddelande](./media/create-notification-hub-portal/deployment-notification.png)
7. Uppdatera webbplatsen **Notification Hub Namespaces** (Namnrymder för meddelandehubb) för att se den nya hubben i listan.

      ![Skärm bild som visar webb sidan för Notification Hub-namnområden med den nya hubben i listan.](./media/create-notification-hub-portal/new-hub-in-list.png)
8. Välj din **meddelandehubb** för att se startsidan för meddelandehubben.

      ![Skärm bild som visar start sidan för Notification Hub.](./media/create-notification-hub-portal/hub-home-page.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat en meddelandehubb. Om du vill lära dig att konfigurera hubben med inställningar för plattformsspecifikt meddelandesystem (PNS) läser du avsnittet om att [konfigurera en meddelandehubb med PNS-inställningar](configure-notification-hub-portal-pns-settings.md).
