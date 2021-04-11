---
title: Hantera grupper för den kommersiella Marketplace – Azure Marketplace
description: Lär dig hur du hanterar grupper i programmet för kommersiella marknads platser i Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.date: 04/07/2021
ms.openlocfilehash: aa75082142aa5735350cd7fed30194314b6e7368
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108465"
---
# <a name="manage-groups-for-the-commercial-marketplace"></a>Hantera grupper för den kommersiella marknads platsen

Med grupper kan du styra flera användar roller och behörigheter tillsammans.

## <a name="add-an-existing-group"></a>Lägg till en befintlig grupp

Så här lägger du till en grupp som redan finns i din organisations arbets konto (Azure AD-klient) till ditt partner Center-konto:

1. Välj **Lägg till grupper** på sidan **användare** (under **konto inställningar**).
1. Välj en eller flera grupper i listan som visas. Du kan söka efter vissa grupper med hjälp av sökrutan. Om du väljer fler än en grupp som ska läggas till i ditt partner Center-konto måste du tilldela dem samma roll eller uppsättning anpassade behörigheter. Om du vill lägga till flera grupper med olika roller/behörigheter upprepar du de här stegen för varje roll eller uppsättning anpassade behörigheter.
1. När du är färdig med att välja grupper väljer du **Lägg till markerade**.
1. I avsnittet **roller** anger du roll (er) eller anpassade behörigheter för de valda grupperna. Alla medlemmar i gruppen kommer att ha åtkomst till ditt partner Center-konto med de behörigheter som du tillämpar på gruppen, oavsett vilka roller och behörigheter som är kopplade till sitt eget konto.
1. Välj **Spara**.

När du lägger till en befintlig grupp kommer alla användare som är medlemmar i gruppen att kunna komma åt ditt partner Center-konto, med de behörigheter som är kopplade till gruppens tilldelade roll.

## <a name="add-a-new-group"></a>Lägga till en ny grupp

Så här lägger du till en helt ny grupp till ditt partner Center-konto:

1. Välj **Lägg till grupper** på sidan **användare** (under **konto inställningar**).
1. Välj **ny grupp** på nästa sida.
1. Ange visnings namnet för den nya gruppen.
1. Ange roll (er) eller anpassade behörigheter för gruppen. Alla medlemmar i gruppen kommer att ha åtkomst till ditt partner Center-konto med de behörigheter som du använder här, oavsett vilka roller/behörigheter som är kopplade till sitt eget konto.
1. Välj användare för den nya gruppen i listan som visas. Du kan använda sökrutan för att söka efter vissa användare.
1. När du är färdig med att välja användare väljer du **Lägg till markerade** för att lägga till dem i den nya gruppen.
1. Välj **Spara**.

Den nya gruppen skapas i din organisations arbets konto (Azure AD-klient) också, inte bara i ditt partner Center-konto.

## <a name="remove-a-group"></a>Ta bort en grupp

Så här tar du bort en grupp från ditt arbets konto (Azure AD-klient):
1. Gå till **användare** (under **konto inställningar**).
1. Välj den grupp som du vill ta bort med hjälp av kryss rutan längst till höger.
1. Välj **ta bort** från tillgängliga åtgärder. Ett popup-fönster visas där du bekräftar att du vill ta bort de valda grupperna.
