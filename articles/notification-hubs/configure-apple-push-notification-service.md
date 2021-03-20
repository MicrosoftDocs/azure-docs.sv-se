---
title: Konfigurera Apple Push Notification Service i Azure Notification Hubs | Microsoft Docs
description: Lär dig hur du konfigurerar en Azure Notification Hub med Apple Push Notification Service-inställningar (APN).
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 06/22/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 63c7e0c9569428b55420911f253deee52ce440cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85255406"
---
# <a name="configure-apple-push-notification-service-settings-for-a-notification-hub-in-the-azure-portal"></a>Konfigurera Apple Push Notification Service inställningar för en Notification Hub i Azure Portal

Den här artikeln visar hur du konfigurerar Apple Push Notification Service inställningar (APN) för en Azure Notification Hub med hjälp av Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

Om du inte redan har skapat en Notification Hub skapar du ett nu. Mer information finns i [skapa en Azure Notification Hub i Azure Portal](create-notification-hub-portal.md).

## <a name="configure-apple-push-notification-service"></a>Konfigurera Apple Push Notification Service

Följande procedur visar hur du konfigurerar Apple Push Notification Service inställningar (APN) för en Notification Hub:

1. I Azure Portal på sidan **Notification Hub** väljer du **Apple (APNs)** på den vänstra menyn.

1. För **autentiseringsläge** väljer du antingen **certifikat** eller **token**.

   - Om du väljer **certifikat**:
      - Välj fil ikonen och välj sedan den *. p12* -fil som du vill ladda upp.
      - Ange ett lösen ord.
      - Välj **Sandbox**-läge. Eller, om du vill skicka push-meddelanden till användare som har köpt din app från Store, väljer du **produktions** läge.

     ![Skärm bild av en konfiguration av APN-certifikat i Azure Portal](./media/configure-apple-push-notification-service/notification-hubs-apple-config-cert.png)

   - Om du väljer **token**:
      - Ange värdena för **nyckel-ID**, **paket-ID**, **Team-ID** och **token**.
      - Välj **Sandbox**-läge. Eller, om du vill skicka push-meddelanden till användare som har köpt din app från Store, väljer du **produktions** läge.

     ![Skärm bild av en konfiguration av APN-token i Azure Portal](./media/configure-apple-push-notification-service/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Nästa steg

En själv studie kurs med stegvisa instruktioner för att skicka meddelanden till iOS-enheter finns i följande artikel: [skicka push-meddelanden till iOS-appar med hjälp av Azure Notification Hubs](ios-sdk-get-started.md).
