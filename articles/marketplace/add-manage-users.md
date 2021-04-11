---
title: Lägga till och hantera användare för den kommersiella Marketplace – Azure Marketplace
description: Lär dig hur du hanterar användare i programmet för kommersiella marknads platser för ett Microsoft-konto för marknads plats i Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.date: 04/07/2021
ms.openlocfilehash: d5b9197bfd2526dd414406ebf1aca509d3b3fa91
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108411"
---
# <a name="add-and-manage-users-for-the-commercial-marketplace"></a>Lägga till och hantera användare för den kommersiella marknads platsen

**Lämpliga roller**

- Ägare
- Ansvarig

I avsnittet **användare** i Partner Center (under **konto inställningar**) kan du använda Azure AD för att hantera de användare, grupper och Azure AD-program som har åtkomst till ditt partner Center-konto. Ditt konto måste ha behörigheter på administratörs nivå för det [arbets konto (Azure AD-klient)](company-work-accounts.md) som du vill lägga till eller redigera användare i. Om du vill hantera användare inom ett annat arbets konto/klient organisation måste du logga ut och sedan logga in igen som en användare med **chefs** behörigheter för det arbets kontot/klient organisationen.

När du har loggat in med ditt arbets konto (Azure AD-klient) kan du lägga till och hantera användare.

## <a name="add-existing-users"></a>Lägg till befintliga användare

Så här lägger du till användare till ditt partner Center-konto som redan finns i företagets [arbets konto (Azure AD-klient)](company-work-accounts.md):

1. Gå till **användare** (under **konto inställningar**) och välj **Lägg till användare**.
1. Välj en eller flera användare i listan som visas. Du kan använda sökrutan för att söka efter vissa användare. * Om du väljer fler än en användare som ska läggas till i ditt partner Center-konto måste du tilldela dem samma roll eller uppsättning anpassade behörigheter. Om du vill lägga till flera användare med olika roller/behörigheter upprepar du de här stegen för varje roll eller uppsättning anpassade behörigheter.
1. När du är färdig med att välja användare väljer du **Lägg till markerade**.
1. I avsnittet **roller** anger du roll (er) eller anpassade behörigheter för de valda användarna.
1. Välj **Spara**.

## <a name="create-new-users"></a>Skapa nya användare

För att skapa helt nya användar konton måste du ha ett konto med [globala administratörs](/azure/active-directory/roles/permissions-reference) behörigheter.

1. Gå till **användare** (under **konto inställningar**), Välj **Lägg till användare** och välj sedan **skapa nya användare**.
1. Ange förnamn, efter namn och användar namn för varje ny användare.
1. Om du vill att den nya användaren ska ha ett globalt administratörs konto i din organisations katalog markerar du kryss rutan **gör användaren till global administratör i din Azure AD med fullständig kontroll över alla katalog resurser**. Detta ger användaren fullständig åtkomst till alla administrativa funktioner i ditt företags Azure AD. De kan lägga till och hantera användare i din organisations arbets konto (Azure AD-klient), men inte i Partner Center, om du inte beviljar kontot rätt roll/behörigheter.
1. Om du har markerat kryss rutan för att **göra den här användaren till global administratör** måste du ange ett *e-postmeddelande för återställning av lösen ord* för att användaren ska kunna återställa sina lösen ord vid behov.
1. I avsnittet **grupp medlemskap** väljer du de grupper som du vill att den nya användaren ska tillhöra.
1. I avsnittet **roller** anger du roll (er) eller anpassade behörigheter för användaren.
1. Välj **Spara**.

När du skapar en ny användare i Partner Center skapas även ett konto för den användaren i arbets kontot (Azure AD-klienten) som du är inloggad på. Om du gör ändringar i en användares namn i Partner Center görs samma ändringar i din organisations arbets konto (Azure AD-klient).

## <a name="invite-new-users-by-email"></a>Bjud in nya användare via e-post

Om du vill bjuda in användare som för närvarande inte är en del av ditt företags arbets konto (Azure AD-klient) via e-post måste du ha ett konto med [globala administratörs](/azure/active-directory/roles/permissions-reference) behörigheter.

1. Gå till **användare** (under **konto inställningar**), Välj **Lägg till användare** och välj **Bjud in användare via e-post**.
1. Ange en eller flera e-postadresser (upp till 10), avgränsade med kommatecken eller semikolon.
1. I avsnittet **roller** anger du roll (er) eller anpassade behörigheter för användaren.
1. Välj **Spara**.

De användare som du har bjudit in får en e-postinbjudan om att ansluta till ditt partner Center-konto. Ett nytt gäst användar konto kommer att skapas i ditt arbets konto (Azure AD-klient). Varje användare måste acceptera sin inbjudan innan de kan komma åt ditt konto.

Om du behöver skicka en inbjudan igen går du till sidan *användare* , söker efter inbjudan i listan med användare, väljer e-postadress (eller texten som säger att *inbjudan väntar*). Välj sedan **Skicka inbjudan igen** på sidan längst ned på sidan.

Om din organisation använder [katalog integrering](https://docs.microsoft.com/previous-versions/azure/azure-services/jj573653(v=azure.100)) för att synkronisera den lokala katalog tjänsten med din Azure AD kan du inte skapa nya användare, grupper eller Azure AD-program i Partner Center. Du (eller en annan administratör i din lokala katalog) måste skapa dem direkt i den lokala katalogen innan du kan se och lägga till dem i Partner Center.

## <a name="remove-a-user"></a>Ta bort en användare

Om du vill ta bort en användare från ditt arbets konto (Azure AD-klient) går du till **användare** (under **konto inställningar**) väljer du den användare som du vill ta bort med hjälp av kryss rutan längst till höger och väljer sedan **ta bort** från de tillgängliga åtgärderna. Ett popup-fönster visas där du bekräftar att du vill ta bort de markerade användarna.

## <a name="change-a-user-password"></a>Ändra ett användar lösen ord

Om en av dina användare behöver ändra sitt lösen ord kan de göra det själva om du har angett ett *e-postmeddelande för återställning av lösen ord* när du skapar användar kontot. Du kan också uppdatera en användares lösen ord genom att följa stegen nedan. Om du vill ändra en användares lösen ord på ditt företags arbets konto (Azure AD-klient) måste du vara inloggad på ett konto med [globala administratörs](/azure/active-directory/roles/permissions-reference) behörigheter. Detta kommer att ändra användarens lösen ord i din Azure AD-klient, tillsammans med lösen ordet som de använder för att komma åt Partner Center.

1. Från sidan **användare** (under **konto inställningar**) väljer du namnet på det användar konto som du vill redigera.
1. Välj knappen **Återställ lösen ord** längst ned på sidan.
1. En bekräftelse sida visas för att Visa inloggnings information för användaren, inklusive ett tillfälligt lösen ord. Se till att skriva ut eller kopiera den här informationen och ge den till användaren, eftersom du inte kan komma åt det tillfälliga lösen ordet när du har lämnat den här sidan.
