---
author: baanders
description: inkludera fil för steget åtkomst behörigheter i installations programmet för Azure Digitals
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: a905bb3b4effb0381facfbfaa37c8ea412b81287
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "100552901"
---
Azure Digitals dubbla använder [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) för rollbaserad åtkomst kontroll (RBAC). Det innebär att innan en användare kan göra data planet anrop till din Azure Digital-instansen, måste användaren tilldelas en roll med rätt behörighet för den.

För Azure Digitals, är den här rollen _**Azure Digitals sammanflätade data ägare**_. Du kan läsa mer om roller och säkerhet i [*begrepp: säkerhet för Azure Digitals dubbla lösningar*](../articles/digital-twins/concepts-security.md).

I det här avsnittet visas hur du skapar en roll tilldelning för en användare i din Azure Digitals-instans med hjälp av användarens e-post i Azure AD-klienten på din Azure-prenumeration. Beroende på din roll i din organisation kan du konfigurera den här behörigheten åt dig själv eller ställa in den på uppdrag av någon annan som ska hantera Azure Digitals-instansen.

### <a name="assign-the-role"></a>Tilldela rollen

Om du vill ge en användare behörighet att hantera en Azure Digitals-instans måste du tilldela dem rollen _**Azure Digitals data ägare**_ i instansen.

> [!NOTE]
> Observera att den här rollen skiljer sig från rollen Azure AD- *ägare* , som också kan tilldelas i omfånget för Azure Digital-instansen. Detta är två distinkta hanterings roller och Azure AD- *ägaren* beviljar inte åtkomst till data Plans funktioner som beviljas med *Azure Digitals data ägare*.