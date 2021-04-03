---
title: Konfigurera Microsoft Push Notification Service i Azure Notification Hubs | Microsoft Docs
description: Lär dig hur du konfigurerar inställningar för Microsoft Push Notification Service för en Azure Notification Hub.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 08/04/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 6784331dccd779390ace7dc632d54fe54aedc458
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87760999"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-in-the-azure-portal"></a>Konfigurera MPNS-inställningar (Microsoft Push Notification Service) i Azure Portal

Den här artikeln visar hur du konfigurerar MPNS-inställningar (Microsoft Push Notification Service) för en Azure Notification Hub med hjälp av Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

Om du inte redan har skapat en Notification Hub skapar du ett nu. Mer information finns i [skapa en Azure Notification Hub i Azure Portal](create-notification-hub-portal.md).

## <a name="configure-microsoft-push-notification-service-mpns"></a>Konfigurera Microsoft Push Notification Service (MPNS)

Följande procedur beskriver hur du konfigurerar inställningar för Microsoft Push Notification Service (MPNS) för en Notification Hub:

1. I Azure Portal på sidan **Notification Hub** väljer du **Windows Phone (MPNS)** på den vänstra menyn.
2. Aktivera antingen oautentiserade eller autentiserade push-meddelanden:

   a. Om du vill aktivera oautentiserade push-meddelanden väljer du **Aktivera oautentiserad push**  >  **Spara**.

      ![Skärm bild som visar hur du aktiverar oautentiserade push-meddelanden](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Så här aktiverar du autentiserade push-meddelanden:
      * I verktygsfältet väljer du **Ladda upp certifikat**.
      * Välj fil ikonen och välj sedan certifikat filen.
      * Ange lösenordet för certifikatet.
      * Välj **OK**.
      * På sidan **Windows Phone (MPNS)** väljer du **Spara**.

## <a name="next-steps"></a>Nästa steg

En själv studie kurs med stegvisa instruktioner för att skicka meddelanden till Windows Phone-enheter med hjälp av Azure Notification Hubs och Microsoft Push Notification Service (MPNS) finns i [skicka push-meddelanden till Windows Phone appar med hjälp av Notification Hubs](notification-hubs-windows-mobile-push-notifications-mpns.md).
