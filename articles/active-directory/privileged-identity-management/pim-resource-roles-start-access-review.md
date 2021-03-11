---
title: Skapa en åtkomst granskning av Azure Resource roles i PIM – Azure AD | Microsoft Docs
description: Lär dig hur du skapar en åtkomst granskning av Azure Resource roles i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 03/09/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4276b48584ecbad91794de58abafd7e3367f6877
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564041"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Skapa en åtkomst granskning av Azures resurs roller i Privileged Identity Management

Behovet av åtkomst till behöriga Azure-resurs roller av anställda ändras med tiden. För att minska risken för inaktuella roll tilldelningar bör du regelbundet granska åtkomst. Du kan använda Azure Active Directory (Azure AD) Privileged Identity Management (PIM) för att skapa åtkomst granskningar för privilegie rad åtkomst till Azure-resurs roller. Du kan också konfigurera återkommande åtkomst granskningar som sker automatiskt. Den här artikeln beskriver hur du skapar en eller flera åtkomst granskningar.

## <a name="prerequisite-role"></a>Nödvändig roll

 Om du vill skapa åtkomst granskningar måste du ha tilldelats rollen [ägare](../../role-based-access-control/built-in-roles.md#owner) eller [administratör för användar åtkomst administratör](../../role-based-access-control/built-in-roles.md#user-access-administrator) för resursen.

## <a name="open-access-reviews"></a>Öppna åtkomst granskningar

1. Logga in på [Azure Portal](https://portal.azure.com/) med en användare som är tilldelad en av de nödvändiga rollerna.

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Azure-resurser** på den vänstra menyn.

1. Välj den resurs som du vill hantera, till exempel en prenumeration.

1. Under hantera väljer du **åtkomst granskningar**.

    ![Azure-resurser – åtkomst gransknings listan visar status för alla granskningar](./media/pim-resource-roles-start-access-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>Starta åtkomst granskningen

När du har angett inställningarna för åtkomst granskning klickar du på **Start**. Åtkomst granskningen visas i listan med en indikator för dess status.

![Listan åtkomst granskningar som visar statusen för den startade granskningen](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

Som standard skickar Azure AD ett e-postmeddelande till granskare strax efter att granskningen startar. Om du väljer att inte låta Azure AD skicka e-postmeddelandet måste du meddela granskarna att en åtkomst granskning väntar på att de ska slutföras. Du kan visa dem i anvisningarna för hur du [granskar åtkomst till Azures resurs roller](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Hantera åtkomst granskningen

Du kan följa förloppet när granskarna har slutfört sina granskningar på **översikts** sidan för åtkomst granskningen. Ingen åtkomst behörighet har ändrats i katalogen förrän [granskningen har slutförts](pim-resource-roles-complete-access-review.md).

![Översikts sidan åtkomst granskningar som visar information om granskningen](./media/pim-resource-roles-start-access-review/access-review-overview.png)

Om det här är en engångs granskning kan du efter att åtkomst gransknings perioden är över eller administratören stoppar åtkomst granskningen genom att följa stegen i [slutföra en åtkomst granskning av Azure Resource roles](pim-resource-roles-complete-access-review.md) för att se och tillämpa resultatet.  

Om du vill hantera en serie åtkomst granskningar navigerar du till åtkomst granskningen och du hittar kommande förekomster i schemalagda granskningar och redigerar slutdatumet eller lägger till/tar bort granskare i enlighet med detta.

Baserat på dina val i **när inställningarna för slut för ande slutförs**, utförs automatiskt tillämpande efter slutdatum för granskningen eller när du stoppar granskningen manuellt. Status för granskningen ändras från **slutförd** till mellanliggande tillstånd som att **tillämpa** och slutligen tillämpa **tillstånd.** Du bör förvänta dig att se till att nekade användare, om de finns, tas bort från roller på några minuter.

## <a name="next-steps"></a>Nästa steg

- [Granska åtkomst till Azures resurs roller](pim-resource-roles-perform-access-review.md)
- [Slutför en åtkomst granskning av Azures resurs roller](pim-resource-roles-complete-access-review.md)
- [Skapa en åtkomst granskning av Azure AD-roller](pim-how-to-start-security-review.md)
