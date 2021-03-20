---
title: Start princip för förfallo princip för grupp – Azure AD | Microsoft Docs
description: Förfallo datum för Microsoft 365 grupper – Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6bc76214ca2bedfc66ad3165cafe904584d42db
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96546326"
---
# <a name="quickstart-set-microsoft-365-groups-to-expire-in-azure-active-directory"></a>Snabb start: ange Microsoft 365 grupper upphör att gälla i Azure Active Directory

I den här snabb starten anger du förfallo principen för Microsoft 365 grupper. När användare kan konfigurera sina egna grupper kan det uppstå många oanvända grupper. Ett sätt att hantera oanvända grupper är att ställa in dessa grupper att upphöra, för att minska underhållet med att ta bort grupper manuellt.

Förfalloprinciper är enkla:

- Grupper med användar aktiviteter förnyas automatiskt när de upphör snart
- Gruppägare meddelas om att förnya en grupp som håller på att upphöra
- Grupper som inte förnyas tas bort
- En borttagen Microsoft 365 grupp kan återställas inom 30 dagar av en grupp ägare eller av en Azure AD-administratör

> [!NOTE]
> Nu använder grupper Azure AD Intelligence för att automatiskt förnya baserat på om de har använts nyligen. Detta förnyelse beslut baseras på användar aktivitet i grupper mellan Microsoft 365 tjänster som Outlook, SharePoint, teams, Yammer och andra.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisite"></a>Förutsättning

 Den minst privilegierade rollen som krävs för att konfigurera grupp förfallo datum är användar administratör i organisationen.

## <a name="turn-on-user-creation-for-groups"></a>Aktivera användarskapande för grupper

1. Logga in på [Azure Portal](https://portal.azure.com) med ett användar administratörs konto.

2. Välj **Grupper** och välj sedan **Allmänt**.
  
   ![Sidan Inställningar för självbetjänings grupp](./media/groups-quickstart-expiration/self-service-settings.png)

3. Ange att  **användare kan skapa Microsoft 365 grupper** till **Ja**.

4. Välj **Spara** för att spara gruppinställningarna när du är klar.

## <a name="set-group-expiration"></a>Ange gruppförfallotid

1. Logga in på [Azure Portal](https://portal.azure.com), Välj **Azure Active Directory**  >  **grupper**  >  **förfallo datum** för att öppna inställningarna för förfallo datum.
  
   ![Sidan förfallo inställningar för grupp](./media/groups-quickstart-expiration/expiration-settings.png)

2. Ange förfallointervallet. Välj ett förinställt värde eller ange ett anpassat värde över 31 dagar. 

3. Ange en e-postadress dit meddelanden om förfallotid ska skickas när en grupp inte har någon ägare.

4. I den här snabb starten anger du **Aktivera förfallo datum för dessa Microsoft 365 grupper** till **alla**.

5. Välj **Spara** för att spara förfalloinställningarna när du är klar.

Klart! I den här snabb starten har du ställt in utgångs principen för de valda Microsoft 365 grupperna.

## <a name="clean-up-resources"></a>Rensa resurser

### <a name="to-remove-the-expiration-policy"></a>Så här tar du bort förfalloprincipen

1. Se till att du är inloggad på [Azure Portal](https://portal.azure.com) med ett konto som är global administratör för din Azure AD-organisation.
2. Välj **Azure Active Directory**  >  **grupper**  >  **upphör att gälla**.
3. Ange **Aktivera förfallo datum för dessa Microsoft 365 grupper** till **ingen**.

### <a name="to-turn-off-user-creation-for-groups"></a>Så här stänger du av användarskapande för grupper

1. Välj **Azure Active Directory**  >  **grupper**  >  **Allmänt**. 
2. Ange att **användare kan skapa Microsoft 365 grupper i Azure portaler** till **Nej**.

## <a name="next-steps"></a>Nästa steg

Mer information om förfallo datum inklusive PowerShell-instruktioner och tekniska begränsningar finns i följande artikel:

> [!div class="nextstepaction"]
> [Utgångs princip PowerShell](groups-lifecycle.md)
