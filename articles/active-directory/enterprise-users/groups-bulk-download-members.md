---
title: Samlings lista över grupp medlemskap – Azure Active Directory Portal | Microsoft Docs
description: Lägg till användare i grupp i Azure administrations centret.
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
ms.openlocfilehash: d4055fb3bfdbcfd7e4a74d9290017a6193578146
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96547704"
---
# <a name="bulk-download-members-of-a-group-in-azure-active-directory"></a>Mass nedladdning av medlemmar i en grupp i Azure Active Directory

Med hjälp av Azure Active Directory (Azure AD)-portalen kan du hämta medlemmarna i en grupp i din organisation till en fil med kommaavgränsade värden (CSV).

## <a name="to-bulk-download-group-membership"></a>Till grupp medlemskap för grupp hämtning

1. Logga in på [Azure Portal](https://portal.azure.com) med ett användar administratörs konto i organisationen. Grupp ägare kan också massredigera medlemmar av grupper som de äger.
1. I Azure AD väljer du **grupper**  >  **alla grupper**.
1. Öppna den grupp vars medlemskap du vill hämta och välj sedan **medlemmar**.
1. På sidan **medlemmar** väljer du **Hämta medlemmar** för att ladda ned en CSV-fil som visar grupp medlemmarna.

   ![Kommandot ladda ned medlemmar finns på profil sidan för gruppen](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Kontrol lera hämtnings status

Du kan se statusen för alla väntande Mass förfrågningar på resultat sidan för **Mass åtgärder** .

[![Kontrol lera status på sidan med Mass åtgärds resultat.](./media/groups-bulk-download-members/bulk-center.png)](./media/groups-bulk-download-members/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Begränsningar för Mass nedladdnings tjänst

Varje Mass aktivitet för att ladda ned en lista över grupp medlemmar kan köras i upp till en timme. På så sätt kan du ladda ned en lista med minst 500 000 medlemmar.

## <a name="next-steps"></a>Nästa steg

- [Grupp medlemmar för Mass import](groups-bulk-import-members.md)
- [Mass borttagning av grupp medlemmar](groups-bulk-download-members.md)
