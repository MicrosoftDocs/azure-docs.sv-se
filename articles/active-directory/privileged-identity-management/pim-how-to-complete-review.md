---
title: Slutför åtkomst granskning av Azure AD-roller i PIM – Azure AD | Microsoft Docs
description: Lär dig att slutföra en åtkomst granskning av Azure AD-roller i Azure AD Privileged Identity Management (PIM) och visa resultatet
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa7e8089fbbf2ee653100a05383fdbdc877ffda4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84742224"
---
# <a name="complete-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Slutför en åtkomst granskning av Azure AD-roller i Privileged Identity Management

Privilegierade roll administratörer kan granska privilegie rad åtkomst när en [åtkomst granskning har startats](pim-how-to-start-security-review.md).  Privileged Identity Management (PIM) skickar automatiskt ett e-postmeddelande till användare i din Azure Active Directory-organisation (Azure AD) som efterfrågar dem för att granska deras åtkomst. Om en användare inte fick något e-postmeddelande kan du skicka dem till anvisningar i [så här gör du en åtkomst granskning](pim-how-to-perform-security-review.md).

När åtkomst gransknings perioden är över, eller om alla användare har avslutat sin egen granskning, följer du stegen i den här artikeln för att hantera granskningen och se resultatet.

## <a name="manage-access-reviews"></a>Hantera åtkomst granskningar

1. Gå till [Azure Portal](https://portal.azure.com/) och välj **Azure AD Privileged Identity Management** -tjänsten på instrument panelen.
1. Välj avsnittet **åtkomst granskningar** på instrument panelen.
1. Välj den åtkomst granskning som du vill hantera.

På bladet detalj gransknings information finns det ett antal alternativ för att hantera den här granskningen.

![Privileged Identity Management åtkomst gransknings knappar – skärm bild](./media/pim-how-to-complete-review/review-buttons.png)

### <a name="remind"></a>Kommer

Om en åtkomst granskning har kon figurer ATS så att användarna kan granska dem, skickar knappen **Påminn** ut ett meddelande.

### <a name="stop"></a>Stoppa

Alla åtkomst granskningar har ett slutdatum, men du kan använda **stopp** -knappen för att avsluta den tidigt. Om några användare inte har granskats av den här gången kan de inte göra det efter att du har stoppat granskningen. Du kan inte starta om en granskning när den har stoppats.

### <a name="apply"></a>Använd

När en åtkomst granskning har slutförts måste du, antingen på grund av att du nått slutdatumet eller stoppa den manuellt, **använda knappen Verkställ** för att implementera resultatet av granskningen. Om en användares åtkomst nekades i granskningen är detta det steg som tar bort roll tilldelningen.  

### <a name="export"></a>Exportera

Om du vill tillämpa resultatet av åtkomst granskningen manuellt kan du exportera granskningen. Knappen **Exportera** kommer att börja ladda ned en CSV-fil. Du kan hantera resultatet i Excel eller andra program som öppnar CSV-filer.

### <a name="delete"></a>Ta bort

Om du inte är intresse rad av granskningen tar du bort den. Med knappen **ta bort** tas granskningen bort från tjänsten Privileged Identity Management.

> [!IMPORTANT]
> Du kommer inte att behöva bekräfta denna destruktiva ändring, så kontrol lera att du vill ta bort den granskningen.

## <a name="next-steps"></a>Nästa steg

- [Starta en åtkomst granskning för Azure AD-roller i Privileged Identity Management](pim-how-to-start-security-review.md)
- [Utför en åtkomst granskning av mina Azure AD-roller i Privileged Identity Management](pim-how-to-perform-security-review.md)
