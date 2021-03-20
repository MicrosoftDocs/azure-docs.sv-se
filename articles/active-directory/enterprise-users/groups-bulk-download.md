---
title: Hämta en lista med grupper i Azure Active Directory Portal | Microsoft Docs
description: Hämta grupp egenskaper i grupp i Azure administrations centret i Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 12/02/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98c61679fed04c0a696b60bb7ee53009a8a530e0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96547686"
---
# <a name="bulk-download-a-list-of-groups-in-azure-active-directory"></a>Hämta en lista över grupper i Azure Active Directory

Med hjälp av Azure Active Directory-portalen (Azure AD) kan du hämta en lista över alla grupper i organisationen till en fil med kommaavgränsade värden (CSV).

## <a name="to-download-a-list-of-groups"></a>Hämta en lista över grupper

1. Logga in på [Azure Portal](https://portal.azure.com) med ett administratörs konto i organisationen.
1. I Azure AD väljer du **grupper**  >  **hämtnings grupper**.
1. På sidan **hämtning av grupper** väljer du **Start** för att ta emot en CSV-fil som visar dina grupper.

   ![Kommandot ladda ned grupper finns på sidan alla grupper](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Kontrol lera hämtnings status

Du kan se statusen för alla väntande Mass förfrågningar på resultat sidan för **Mass åtgärder** .

[![Kontrol lera status på sidan med Mass åtgärds resultat.](./media/groups-bulk-download/bulk-center.png)](./media/groups-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Begränsningar för Mass nedladdnings tjänst

Varje Mass aktivitet för att hämta en grupp lista kan köras i upp till en timme. På så sätt kan du ladda ned en lista med minst 300 000 grupper.

## <a name="next-steps"></a>Nästa steg

- [Mass borttagning av grupp medlemmar](groups-bulk-remove-members.md)
- [Ladda ned medlemmar i en grupp](groups-bulk-download-members.md)
