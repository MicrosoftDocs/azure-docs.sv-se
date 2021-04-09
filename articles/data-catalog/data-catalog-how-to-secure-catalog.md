---
title: Skydda åtkomsten till Azure Data Catalog
description: Den här artikeln förklarar hur du skyddar en data katalog och dess data till gångar i Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 537254d7fb153e182be8ece13675a357a9a3e24e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674826"
---
# <a name="how-to-secure-access-to-data-catalog-and-data-assets"></a>Skydda åtkomsten till Data Catalog och data till gångar

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

> [!IMPORTANT]
> Den här funktionen är endast tillgänglig i standard versionen av Azure Data Catalog.

Med Azure Data Catalog kan du ange vem som kan komma åt data katalogen och vilka åtgärder (registrera, kommentera, bli ägare) som de kan utföra på metadata i katalogen. 

## <a name="catalog-users-and-permissions"></a>Katalog användare och behörigheter

För att ge en användare eller grupp åtkomst till en data katalog och ange behörigheter:

1. Klicka på **Inställningar** i verktygsfältet på [Start sidan i din Data Catalog](https://www.azuredatacatalog.com).

   ![Knappen Azure Data Catalog start sid inställningar](media/data-catalog-how-to-secure-catalog/data-catalog-settings.png)

2. På sidan Inställningar expanderar du avsnittet **katalog användare** .

   ![Azure Data Catalog användare Lägg till knapp](media/data-catalog-how-to-secure-catalog/data-catalog-add-button.png)

3. Klicka på **Lägg till**.

4. Ange det fullständigt kvalificerade **användar namnet** eller namnet på **säkerhets gruppen** i Azure Active Directory (AAD) som är associerad med katalogen. Använd kommatecken (,) som avgränsare om du lägger till fler än en användare eller grupp.

   ![Azure Data Catalog användare – användare eller grupper](media/data-catalog-how-to-secure-catalog/data-catalog-users-groups.png)

5. Tryck på **RETUR** eller **tabba** ut ur text rutan. 

6. Bekräfta att alla behörigheter (**anteckna**, **Registrera** och **bli ägare**) är kopplade till dessa användare eller grupper som standard. Det innebär att användaren eller gruppen kan [registrera data till gångar]( data-catalog-how-to-register.md), [kommentera data till gångar]( data-catalog-how-to-annotate.md)och [bli ägare till data till gångar]( data-catalog-how-to-manage.md). 

   ![Azure Data Catalog användare – standard behörigheter](media/data-catalog-how-to-secure-catalog/data-catalog-default-permissions.png)

7. Om du vill ge en användare eller grupp endast Läs behörighet till katalogen tar du bort alternativet **anteckna** för användaren eller gruppen. När du gör det kan användaren eller gruppen inte kommentera data till gångar i katalogen, men de kan visa dem. 

8. Om du vill neka en användare eller grupp från att registrera data till gångar avmarkerar du alternativet **Registrera** för användaren eller gruppen.

9. Om du vill neka en användare från att bli ägare till en data till gång avmarkerar du alternativet **bli ägare** för den användaren eller gruppen. 

10. Om du vill ta bort en användare/grupp från katalog användare klickar du på **x** för användaren/gruppen längst ned i listan. 

   ![Azure Data Catalog katalog användare – ta bort användare X-ikonen](media/data-catalog-how-to-secure-catalog/data-catalog-delete-user.png)

   > [!IMPORTANT]
   > Vi rekommenderar att du lägger till säkerhets grupper i katalog användare i stället för att lägga till användare direkt och tilldela behörigheter. Lägg sedan till användare i de säkerhets grupper som matchar deras roller och deras nödvändiga åtkomst till katalogen.

## <a name="special-considerations"></a>Särskilda överväganden

- Behörigheterna som tilldelas till säkerhets grupper är additiva. Anta att en användare finns i två grupper. En grupp har kommentarer behörigheter och andra grupper har inte behörighet till kommentarer. Sedan har användaren antecknings behörigheter. 
- Behörigheterna som tilldelas direkt till en användare åsidosätter de behörigheter som tilldelats grupper som användaren tillhör. I det tidigare exemplet lade du uttryckligen till användaren i katalog användare och tilldelar inte behörighet till kommentarer. Användaren kan inte kommentera data till gångar, även om användaren är medlem i en grupp som har kommentarer till behörigheter.

## <a name="next-steps"></a>Nästa steg

- [Kom igång med Azure Data Catalog](data-catalog-get-started.md)
