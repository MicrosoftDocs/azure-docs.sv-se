---
title: Snabb start – åtkomst & skapa ny klient organisation – Azure AD
description: Instruktioner om hur du hittar Azure Active Directory och hur du skapar en ny klient för din organisation.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: eeea88d8c21ba754fbeadbb24891126b639616c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96437248"
---
# <a name="quickstart-create-a-new-tenant-in-azure-active-directory"></a>Snabb start: skapa en ny klient i Azure Active Directory
Du kan utföra alla administrativa aktiviteter med hjälp av Azure Active Directory (Azure AD)-portalen, inklusive att skapa en ny klient för din organisation. 

I den här snabbstarten får du lära dig hur du kommer till Azure-portalen och Azure Active Directory och du får lära dig hur du skapar en grundläggande klient för din organisation.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-a-new-tenant-for-your-organization"></a>Så här skapar du en ny klient för din organisation
När du har loggat in på Azure-portalen kan du skapa en ny klient för din organisation. Den nya klientorganisationen representerar din organisation och hjälper dig att hantera en specifik instans av Microsoft-molntjänster för dina interna och externa användare.

### <a name="to-create-a-new-tenant"></a>Så här skapar du en ny klient

1. Logga in på din organisations [Azure Portal](https://portal.azure.com/).

1. Välj **Azure Active Directory** på Azure Portal-menyn.  

    <kbd>![Sidan Azure Active Directory-översikt – skapa en klient](media/active-directory-access-create-new-tenant/azure-ad-portal.png)</kbd>  

1. Välj **skapa en klient**.

1. På fliken grundläggande väljer du vilken typ av klient som du vill skapa, antingen **Azure Active Directory** eller **Azure Active Directory (B2C)**.

1. Välj **Nästa: konfiguration** för att gå vidare till fliken konfiguration.

    <kbd>![Azure Active Directory – skapa en klient sida – fliken konfiguration ](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)</kbd>

1.  Ange följande information på fliken Konfiguration:
    
    - Skriv _contoso-organisation_ i rutan **organisations namn** .

    - Skriv _Contosoorg_ i rutan **inledande domän namn** .

    - Lämna alternativet _USA_ i rutan **Land eller region**.

1. Välj **Nästa: granska + skapa**. Granska informationen som du har angett och om informationen är korrekt väljer du **skapa**.

    <kbd>![Azure Active Directory-granska och skapa klient Sidan](media/active-directory-access-create-new-tenant/azure-ad-review.png)</kbd>

Den nya klientorganisationen skapas med domänen contoso.onmicrosoft.com.

## <a name="clean-up-resources"></a>Rensa resurser
Om du inte kommer att fortsätta att använda det här programmet kan du ta bort klienten med hjälp av följande steg:

- Se till att du är inloggad på den katalog som du vill ta bort via filtret **katalog + prenumeration** i Azure Portal och växla till mål katalogen om det behövs.
- Välj **Azure Active Directory** och klicka sedan på sidan **Contoso – översikt** och **Ta bort katalogen**.

    Klienten och dess tillhörande information tas bort.

    <kbd>![Översikts sida med markerad ta bort katalog-knapp](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)</kbd>

## <a name="next-steps"></a>Nästa steg
- Information om att ändra eller lägga till fler domännamn finns i [Lägga till ett anpassat domännamn i Azure Active Directory](add-custom-domain.md)

- Se [Lägga till eller ta bort en ny användare](add-users-azure-active-directory.md) för att lägga till användare

- Se [Skapa en basgrupp och lägga till medlemmar](active-directory-groups-create-azure-portal.md) för att lägga till grupper och medlemmar

- Lär dig mer om [rollbaserad åtkomst med hjälp av Privileged Identity Management](../../role-based-access-control/best-practices.md) och [villkorlig åtkomst](../../role-based-access-control/conditional-access-azure-management.md) för att hantera organisationens program-och resurs åtkomst.

- Lär dig mer om Azure AD, bland annat [grundläggande licensinformation, terminologi och associerade funktioner](active-directory-whatis.md).
