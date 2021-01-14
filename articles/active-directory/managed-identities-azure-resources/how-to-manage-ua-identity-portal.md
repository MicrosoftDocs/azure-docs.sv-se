---
title: Hantera en användare som tilldelats en hanterad identitet i Azure Portal – Azure AD
description: Stegvisa instruktioner för hur du skapar, visar, tar bort och tilldelar en roll till en användardefinierad hanterad identitet.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/26/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd4cccdfc3a2cf8dd3827776028a7738ae769673
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98184838"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>Skapa, Visa, ta bort eller tilldela en roll till en användare som tilldelats en hanterad identitet med hjälp av Azure Portal

Hanterade identiteter för Azure-resurser ger Azure-tjänster med en hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva ange autentiseringsuppgifter i koden. 

I den här artikeln får du lära dig hur du skapar, visar, tar bort eller tilldelar en roll till en användare som tilldelats en hanterad identitet med hjälp av Azure Portal.

## <a name="prerequisites"></a>Krav

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [avsnittet Översikt](overview.md). **Se till att granska [skillnaden mellan en tilldelad och användardefinierad hanterad identitet](overview.md#managed-identity-types)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

## <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet

För att skapa en användardefinierad hanterad identitet måste ditt konto ha roll tilldelningen [hanterad identitets deltagare](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är kopplat till Azure-prenumerationen för att skapa den tilldelade hanterade identiteten.
2. I rutan Sök skriver du *hanterade identiteter* och under **tjänster** klickar du på **hanterade identiteter**.
3. Klicka på **Lägg till** och ange värden i följande fält under **skapa användare tilldelade hanterade** identitets fönster:
    - **Prenumeration**: Välj den prenumeration som du vill skapa den användarspecifika hanterade identiteten för under.
    - **Resurs grupp**: Välj en resurs grupp om du vill skapa den användar tilldelnings hanterade identiteten i eller klicka på **Skapa ny** för att skapa en ny resurs grupp.
    - **Region**: Välj en region för att distribuera den tilldelade hanterade identiteten, till exempel **västra USA**.
    - **Namn**: det här är namnet på din användarspecifika hanterade identitet, till exempel UAI1.
    ![Skapa en användartilldelad hanterad identitet](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)
4. Granska ändringarna genom att klicka på **Granska + skapa** .
5. Klicka på **Skapa**.

## <a name="list-user-assigned-managed-identities"></a>Lista användare-tilldelade hanterade identiteter

Om du vill visa en lista över/läsa en användardefinierad hanterad identitet måste ditt konto ha roll tilldelningen [hanterad identitet](../../role-based-access-control/built-in-roles.md#managed-identity-operator) eller [hanterad identitets deltagare](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är associerat med Azure-prenumerationen för att visa en lista över användare som tilldelats hanterade identiteter.
2. I rutan Sök skriver du *hanterade identiteter* och under tjänster klickar du på **hanterade identiteter**.
3. En lista över de användare tilldelade hanterade identiteterna för din prenumeration returneras.  Om du vill se information om en användardefinierad hanterad identitet klickar du på dess namn.

![Lista användare-tilldelad hanterad identitet](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Ta bort en användare som tilldelats en hanterad identitet

För att ta bort en användardefinierad hanterad identitet måste ditt konto ha roll tilldelningen [hanterad identitets deltagare](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

Om du tar bort en tilldelad identitet tas den inte bort från den virtuella datorn eller resursen som den tilldelades.  Ta bort den användare som tilldelats identiteten från en virtuell dator genom att [ta bort en användardefinierad hanterad identitet från en virtuell dator](./qs-configure-portal-windows-vm.md#remove-a-user-assigned-managed-identity-from-a-vm).

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är kopplat till Azure-prenumerationen för att ta bort en tilldelad hanterad identitet.
2. Välj den användare-tilldelade hanterade identiteten och klicka på **ta bort**.
3. Välj **Ja** under bekräftelse rutan.

![Ta bort användardefinierad hanterad identitet](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>Tilldela en roll till en användare som tilldelats en hanterad identitet 

För att tilldela en roll till en användardefinierad hanterad identitet måste ditt konto ha roll tilldelningen [administratör för användar åtkomst](../../role-based-access-control/built-in-roles.md#user-access-administrator) .

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är associerat med Azure-prenumerationen för att visa en lista över användare som tilldelats hanterade identiteter.
2. I rutan Sök skriver du *hanterade identiteter* och under tjänster klickar du på **hanterade identiteter**.
3. En lista över de användare tilldelade hanterade identiteterna för din prenumeration returneras.  Välj den användar tilldelnings hanterade identitet som du vill tilldela en roll.
4. Välj **Åtkomstkontroll (IAM)** och sedan **Lägg till rolltilldelning**.

   ![Användare som tilldelats hanterad identitets start](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. Konfigurera följande värden i bladet Lägg till roll tilldelning och klicka sedan på **Spara**:
   - **Roll** – rollen som ska tilldelas
   - **Tilldela åtkomst till**  -resursen för att tilldela den användare tilldelade hanterade identiteten
   - **Välj** -medlemmen att tilldela åtkomst
   
   ![Användarens tilldelade hanterade identitet-IAM](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)
