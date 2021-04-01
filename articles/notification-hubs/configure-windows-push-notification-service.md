---
title: Konfigurera Windows Push Notification Service i Azure Notification Hubs | Microsoft Docs
description: Lär dig hur du konfigurerar inställningar för Windows Push Notification Service för en Azure Notification Hub.
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
ms.openlocfilehash: 62d73a23fff3bd15d873a579d451b2f91509b7fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87758738"
---
# <a name="configure-windows-push-notification-service-settings-in-the-azure-portal"></a>Konfigurera inställningarna för Windows Push Notification Service i Azure Portal

Den här artikeln visar hur du konfigurerar inställningar för Windows Notification Service (WNS) för en Azure Notification Hub med hjälp av Azure Portal.  

## <a name="prerequisites"></a>Förutsättningar

Om du inte redan har skapat en Notification Hub skapar du ett nu. Mer information finns i [skapa en Azure Notification Hub i Azure Portal](create-notification-hub-portal.md).

## <a name="configure-windows-push-notification-service-wns"></a>Konfigurera Windows Push Notification Service (WNS)

Följande procedur beskriver stegen för att konfigurera WNS-inställningar (Windows Push Notification Service) för en Notification Hub:

1. I Azure Portal på sidan **Notification Hub** väljer du **Windows (WNS)** på den vänstra menyn.
2. Ange värden för **Package SID** (Paket-SID) och **Security Key** (Säkerhetsnyckel).
3. Välj **Spara**.

   ![Skärm bild som visar rutorna paket-SID och säkerhets nyckel](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Nästa steg

En själv studie kurs med stegvisa instruktioner för att skicka push-meddelanden till Universell Windows-plattform program med hjälp av Azure Notification Hubs och Windows Push Notification Service (WNS) finns i [skicka meddelanden till UWP-appar med hjälp av Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
