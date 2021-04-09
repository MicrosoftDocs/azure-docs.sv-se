---
title: Så här granskar du Synapse RBAC-roll tilldelningar i Synapse Studio
description: Den här artikeln beskriver hur du granskar Synapse RBAC-roll tilldelningar med Synapse Studio
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9065ca9c7638f3d2bda36e4831b81963936f4b45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100102163"
---
# <a name="how-to-review-synapse-rbac-role-assignments"></a>Granska Synapse RBAC-roll tilldelningar

Synapse RBAC-roller används för att tilldela behörigheter till användare, grupper och andra säkerhets objekt för att möjliggöra åtkomst och användning av Synapse-resurser.  [Läs mer](./synapse-workspace-synapse-rbac.md)

Den här artikeln förklarar hur du granskar de aktuella roll tilldelningarna för en arbets yta.

Med en Synapse RBAC-roll kan du Visa Synapse RBAC-roll tilldelningar för alla omfattningar, inklusive tilldelningar för objekt som du inte har åtkomst till. Endast en Synapse-administratör kan bevilja Synapse RBAC-åtkomst.  

>[!Note]
>Gäst användare (användare från en annan AD-klient) kan inte se eller hantera roll tilldelningar, även om de tilldelats Synapse-administratörs rollen.    

## <a name="open-synapse-studio"></a>Öppna Synapse Studio  

Om du vill granska roll tilldelningarna [öppnar du först Synapse Studio](https://web.azuresynapse.net/) och väljer din arbets yta. 

![Logga in på arbets ytan](./media/common/login-workspace.png) 
 
 När du har öppnat din arbets yta väljer du **Hantera** hubb till vänster och expanderar **säkerhets** avsnittet och väljer **åtkomst kontroll**. 

 ![Välj Access Control i säkerhets avsnittet till vänster](./media/how-to-manage-synapse-rbac-role-assignments/left-nav-security-access-control.png)

## <a name="review-workspace-role-assignments"></a>Granska tilldelningar för arbets ytans roll

Skärmen åtkomst kontroll visar alla aktuella roll tilldelningar för arbets ytan, grupperade efter roll. Varje tilldelning omfattar huvud namn, huvud typ, roll och omfattning.

![Access Control skärm](./media/how-to-review-synapse-rbac-role-assignments/access-control-assignments.png)

Om ett huvud konto tilldelas samma roll i olika omfång, ser du flera tilldelningar för huvud kontot, en för varje omfång.  

Om en roll tilldelas till en säkerhets grupp visas rollerna som uttryckligen tilldelas till gruppen, men inte roller som ärvts från överordnade grupper.  

Du kan filtrera listan efter huvud namn eller e-post och selektivt Filtrera objekt typerna, rollerna och omfattningarna. Ange ditt namn eller e-postalias i namn filtret om du vill se roller som har tilldelats dig. Endast en Synapse-administratör kan ändra dina roller.

>[!Important] 
>Om du är direkt eller indirekt medlem i en grupp som har tilldelats roller kan du ha behörigheter som inte visas.

>[!tip]
>Du kan hitta grupp medlemskap med Azure Active Directory i Azure Portal.  

Om du skapar en ny arbets yta tilldelas du och tjänsten för arbets ytans MSI-tjänstens huvud namn automatiskt Synapse-administratörs rollen vid arbets ytans omfång.

## <a name="next-steps"></a>Nästa steg

Lär dig [hur du hanterar SYNAPSE RBAC-roll tilldelningar](./how-to-manage-synapse-rbac-role-assignments.md).

Lär dig [vilken roll du behöver för att utföra vissa uppgifter](./synapse-workspace-understand-what-role-you-need.md)