---
title: Hantera Synapse RBAC-tilldelningar i Synapse Studio
description: Den här artikeln beskriver hur du tilldelar och återkallar Synapse RBAC-roller till AAD säkerhets objekt
author: billgib
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/1/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: c88371eb450d5b0e2e8b821cdec0e87190e291f8
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98218203"
---
# <a name="how-to-manage-synapse-rbac-role-assignments-in-synapse-studio"></a>Hantera Synapse RBAC-roll tilldelningar i Synapse Studio

Synapse RBAC använder roller för att tilldela behörigheter till användare, grupper och andra säkerhets objekt för att möjliggöra åtkomst och användning av Synapse-resurser och kod artefakter.  [Läs mer](./synapse-workspace-synapse-rbac.md)

Den här artikeln visar hur du lägger till och tar bort Synapse RBAC-roll tilldelningar.

>[!Note]
>- För att hantera Synapse RBAC-roll tilldelningar måste du ha rollen Synapse-administratör på arbets ytan eller på en lågnivå omfattning som innehåller de objekt som du vill hantera. Om du är Synapse-administratör på arbets ytan kan du bevilja åtkomst till alla objekt i arbets ytan. 
>- **Gäst användare** från en annan AD-klient kan inte se eller hantera roll tilldelningar, även om de tilldelats Synapse-administratörs rollen.
>- För att hjälpa dig att få åtkomst till en arbets yta i händelse av att inga Synapse-administratörer har tilldelats eller är tillgängliga för dig kan användare med behörighet att hantera roll tilldelningar för **Azure RBAC** på arbets ytan även hantera Synapse-roll tilldelningar för  , vilket gör att Synapse-administratören eller andra Synapse roll tilldelningar kan läggas till.
>- Åtkomst till SQL-pooler hanteras med hjälp av SQL-behörigheter.  Med undantag för Synapse-administratören och Synapse SQL-administratörs roller beviljar Synapse RBAC-roller inte åtkomst till SQL-pooler.

>[!important]
>- Ändringar som görs i Synapse-roll tilldelningar för RBAC kan ta 2-5 minuter att börja gälla. 
>- Om du hanterar Synapse RBAC-behörigheter genom att ändra medlemskap i säkerhets grupper hanteras ändringar i medlemskap med hjälp av Azure Active Directory.  Ändringar i grupp medlemskap kan ta 10-15 minuter eller längre att börja gälla.

## <a name="open-synapse-studio"></a>Öppna Synapse Studio  

Om du vill tilldela en roll till en användare, grupp, tjänstens huvud namn eller hanterad identitet öppnar du först [Synapse Studio](https://web.azuresynapse.net/) och väljer din arbets yta. 

![Logga in på arbets ytan](./media/common/login-workspace.png) 
 
 När du har öppnat arbets ytan expanderar du avsnittet **säkerhet** till vänster och väljer **åtkomst kontroll**. 

 ![Välj Access Control i säkerhets avsnittet till vänster](./media/how-to-manage-synapse-rbac-role-assignments/left-nav-security-access-control.png)

Skärmen åtkomst kontroll visar aktuella roll tilldelningar.  Du kan filtrera listan efter huvud namn eller e-post och selektivt Filtrera objekt typer, roller eller omfattningar som ingår. Från den här skärmen kan du lägga till eller ta bort roll tilldelningar.  

## <a name="add-a-synapse-role-assignment"></a>Lägg till en Synapse roll tilldelning

På skärmen åtkomst kontroll väljer du **+ Lägg** till för att skapa en ny roll tilldelning

![Klicka på + Lägg till för att skapa en ny roll tilldelning](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add.png)

På fliken Lägg till roll tilldelning kan du skapa roll tilldelningar i omfång för arbets ytan eller området för arbets ytans objekt. 

## <a name="add-workspace-scoped-role-assignment"></a>Lägg till arbets yta-omfattning av roll tilldelning

Välj först **arbets yta** som omfång och välj sedan **Synapse RBAC-rollen**.  Välj de **säkerhets objekt** som ska tilldelas rollen och skapa sedan roll tilldelningarna. 

![Lägg till roll tilldelning för arbets yta – Välj roll](./media/how-to-manage-synapse-rbac-role-assignments/access-control-workspace-role-assignment.png) 

Den tilldelade rollen gäller för alla tillämpliga objekt i arbets ytan.

## <a name="add-workspace-item-scoped-role-assignment"></a>Lägg till arbets ytans objekt – roll tilldelning med omfång

Om du vill tilldela en roll med en mer kornig omfattning väljer du **arbets ytans objekt** som omfång och väljer sedan **objekt typen** omfattning.       

![Lägg till roll tilldelning för arbets ytans objekt – Välj objekt typ](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-item-type.png) 

Välj det **objekt** som ska användas som omfång och välj sedan den **roll** som ska tilldelas från List rutan.  List rutan visar bara de roller som är giltiga för den valda objekt typen. [Läs mer](./synapse-workspace-synapse-rbac.md).  

![Lägg till roll tilldelning för arbets ytans objekt – Välj roll](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-role.png) 
 
**Välj sedan de säkerhets objekt** som rollen ska tilldelas till.  Du kan välja flera huvud konton iterativt.  Välj **tillämpa** för att skapa roll tilldelningarna.

## <a name="remove-a-synapse-rbac-role-assignment"></a>Ta bort en Synapse RBAC-roll tilldelning

Om du vill återkalla Synapse RBAC-åtkomst tar du bort lämpliga roll tilldelningar.  På skärmen åtkomst kontroll använder du filtren för att hitta roll tilldelningarna som ska tas bort.  Kontrol lera roll tilldelningarna och välj sedan **ta bort åtkomst**.   

![Ta bort en roll tilldelning för att ta bort åtkomst](./media/how-to-manage-synapse-rbac-role-assignments/access-control-remove-access.png)

Kom ihåg att ändringar i roll tilldelningar tar 2-5 minuter att börja gälla.   

## <a name="next-steps"></a>Nästa steg

[Förstå de Synapse RBAC-roller som krävs för att utföra vanliga uppgifter](./synapse-workspace-understand-what-role-you-need.md)