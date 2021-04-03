---
title: Själv granskning av ett Access-paket i hantering av Azure AD-rättigheter
description: Lär dig hur du granskar användar åtkomst till åtkomst paket för rättighets hantering i Azure Active Directory åtkomst granskningar (för hands version).
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 31c44f2423cdc5c43638fe2515757bcb11a9814c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87798451"
---
# <a name="self-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Själv granskning av ett Access-paket i hantering av Azure AD-rättigheter

Hantering av Azure AD-rättigheten fören klar hur företag hanterar åtkomst till grupper, program och SharePoint-webbplatser. I den här artikeln beskrivs hur en användare gör en själv granskning av de tilldelade åtkomst paketen.

## <a name="open-the-access-review"></a>Öppna åtkomst granskningen

Om du vill göra en åtkomst granskning måste du först öppna åtkomst granskningen. Använd följande procedur för att hitta och öppna åtkomst granskningen:

1. Du kan få ett e-postmeddelande från Microsoft som ber dig att granska åtkomst. Leta upp e-postmeddelandet för att öppna åtkomst granskningen. Här är ett exempel på ett e-postmeddelande som begär en granskning av åtkomst: 
    
    ![Åtkomst granskning självbetjäning e-post](./media/entitlement-management-access-reviews-review-access/self-review-reviewer-email.png)

1. Klicka på länken **Granska åtkomst** .

1. Du kan också gå direkt till https://myaccess.microsoft.com för att hitta dina väntande åtkomst granskningar om du inte får ett e-postmeddelande.  (För amerikanska myndigheter använder du `https://myaccess.microsoft.us` i stället.)

1. Klicka på **åtkomst granskningar** i det vänstra navigerings fältet för att se en lista över väntande åtkomst granskningar som har tilldelats dig.


1.  Klicka på den granskning som du vill börja.

## <a name="perform-the-access-review"></a>Utför åtkomst granskningen

När du har öppnat åtkomst granskningen kan du se din åtkomst. Använd följande procedur för att göra åtkomst granskningen:

1.  Bestäm om du fortfarande behöver åtkomst till Access-paketet. Projektet du arbetar med är till exempel inte slutfört, så du behöver fortfarande åtkomst för att fortsätta arbeta med projektet.

1.  Klicka på **Ja** om du vill behålla åtkomsten eller på **Nej** om du vill ta bort åtkomsten.
    >[!NOTE]
    >Om du angav att du inte längre behöver åtkomst, tas du inte bort från Access-paketet direkt. Du kommer att tas bort från åtkomst paketet när granskningen upphör eller om en administratör stoppar granskningen.

1.  Om du klickade på **Ja** kan du behöva ta med en motiverings instruktion i rutan **orsak** .

1.  Klicka på **Skicka**.

Du kan gå tillbaka till granskningen om du ändrar dig och bestämmer dig för att ändra ditt svar innan du går igenom gransknings slutet.

## <a name="next-steps"></a>Nästa steg

- [Granska åtkomsten till åtkomst paket](entitlement-management-access-reviews-review-access.md) 
