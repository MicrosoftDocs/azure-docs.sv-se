---
title: Konfigurera Google Firebase Cloud Messaging i Azure Notification Hubs | Microsoft Docs
description: Lär dig hur du konfigurerar en Azure Notification Hub med Google Firebase Cloud Messaging-inställningar.
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
ms.openlocfilehash: 0da103c11e2412108535ca322917632f5d95559d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96003573"
---
# <a name="configure-google-firebase-settings-for-a-notification-hub-in-the-azure-portal"></a>Konfigurera Google Firebase-inställningar för en Notification Hub i Azure Portal

Den här artikeln visar hur du konfigurerar inställningar för Google Firebase Cloud Messaging (FCM) för en Azure Notification Hub med hjälp av Azure Portal.  

## <a name="prerequisites"></a>Förutsättningar

Om du inte redan har skapat en Notification Hub skapar du ett nu. Mer information finns i [skapa en Azure Notification Hub i Azure Portal](create-notification-hub-portal.md).

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>Konfigurera Google Firebase Cloud Messaging (FCM)

Följande procedur beskriver stegen för att konfigurera inställningar för Google Firebase Cloud Messaging (FCM) för en Notification Hub:

1. I Azure Portal på sidan **Notification Hub** väljer du **Google (GCM/FCM)** på den vänstra menyn.
2. Klistra in **API-nyckeln** för det FCM-projekt som du sparade tidigare.
3. Välj **Spara**.

   ![Skärm bild som visar hur du konfigurerar Notification Hubs för Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>Nästa steg

En själv studie kurs med stegvisa instruktioner för att skicka meddelanden till Android-enheter med hjälp av Azure Notification Hubs och Google Firebase Cloud Messaging finns i [skicka push-meddelanden till Android-enheter med Notification Hubs och Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md).
