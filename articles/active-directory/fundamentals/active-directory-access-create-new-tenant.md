---
title: Snabbstart – Komma & skapa ny klient – Azure AD
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
ms.openlocfilehash: fc51c645c470f2b5b0a009eaf831db2f1957617e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780146"
---
# <a name="quickstart-create-a-new-tenant-in-azure-active-directory"></a>Snabbstart: Skapa en ny klient i Azure Active Directory

Du kan utföra alla administrativa aktiviteter med hjälp av Azure Active Directory (Azure AD)-portalen, inklusive att skapa en ny klient för din organisation. 

I den här snabbstarten får du lära dig hur du kommer till Azure-portalen och Azure Active Directory och du får lära dig hur du skapar en grundläggande klient för din organisation.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-a-new-tenant-for-your-organization"></a>Så här skapar du en ny klient för din organisation

När du har loggat in på Azure-portalen kan du skapa en ny klient för din organisation. Den nya klientorganisationen representerar din organisation och hjälper dig att hantera en specifik instans av Microsoft-molntjänster för dina interna och externa användare.

### <a name="to-create-a-new-tenant"></a>Så här skapar du en ny klient

1. Logga in på [organisationens](https://portal.azure.com/)Azure Portal .

1. På Azure Portal väljer du **Azure Active Directory**.  

    <kbd>![Azure Active Directory – översiktssida – Skapa en klientorganisation](media/active-directory-access-create-new-tenant/azure-ad-portal.png)</kbd>  

1. Välj **Skapa en klientorganisation.**

1. På fliken Grundläggande inställningar väljer du den typ av klientorganisation som du vill skapa, **antingen Azure Active Directory** **eller Azure Active Directory (B2C).**

1. Välj **Nästa: Konfiguration** för att gå vidare till fliken Konfiguration.

    <kbd>![Azure Active Directory – Skapa en klientsida – fliken Konfiguration ](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)</kbd>

1.  Ange följande information på fliken Konfiguration:
    
    - Skriv _Contoso Organisation_ i **rutan Organisationsnamn.**

    - Skriv _Contosoorg_ i **rutan Ursprungligt** domännamn.

    - Lämna alternativet _USA_ i rutan **Land eller region**.

1. Välj **Nästa: Granska + skapa**. Granska informationen du angav och om informationen är korrekt väljer du **skapa**.

    <kbd>![Azure Active Directory – Granska och skapa klientsidan](media/active-directory-access-create-new-tenant/azure-ad-review.png)</kbd>

Den nya klientorganisationen skapas med domänen contoso.onmicrosoft.com.

## <a name="your-user-account-in-the-new-tenant"></a>Ditt användarkonto i den nya klientorganisationen

När du skapar en ny Azure AD-klientorganisation blir du den första användaren i klientorganisationen. Som första användare tilldelas du automatiskt rollen [Global](https://docs.microsoft.com/azure/active-directory/roles/permissions-reference#global-administrator) administratör. Kolla in ditt användarkonto genom att gå till [**sidan**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/MsGraphUsers) Användare.

Som standard visas du också som teknisk [kontakt för](https://docs.microsoft.com/microsoft-365/admin/manage/change-address-contact-and-more?view=o365-worldwide#what-do-these-fields-mean) klientorganisationen. Teknisk kontaktinformation är något som du kan ändra i [**Egenskaper**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).

> [!WARNING]
> Se till att din katalog har minst två konton med tilldelade behörigheter som global administratör. Detta hjälper om en global administratör är utelåst. Mer information finns i artikeln Hantera konton [för åtkomst vid akutfall i Azure AD.](../roles/security-emergency-access.md)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte tänker fortsätta att använda det här programmet kan du ta bort klienten med följande steg:

- Kontrollera att du är inloggad i den katalog som du vill ta bort via filtret **Katalog +** prenumeration i Azure Portal. Växla till målkatalogen om det behövs.
- Välj **Azure Active Directory** och klicka sedan på sidan **Contoso – översikt** och **Ta bort katalogen**.

    Klienten och dess tillhörande information tas bort.

    <kbd>![Översiktssidan, med den markerade knappen Ta bort katalog](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)</kbd>

## <a name="next-steps"></a>Nästa steg

- Information om att ändra eller lägga till fler domännamn finns i [Lägga till ett anpassat domännamn i Azure Active Directory](add-custom-domain.md)

- Se [Lägga till eller ta bort en ny användare](add-users-azure-active-directory.md) för att lägga till användare

- Se [Skapa en basgrupp och lägga till medlemmar](active-directory-groups-create-azure-portal.md) för att lägga till grupper och medlemmar

- Lär dig [mer om rollbaserad åtkomst med Privileged Identity Management](../../role-based-access-control/best-practices.md) och [villkorsstyrd](../../role-based-access-control/conditional-access-azure-management.md) åtkomst för att hantera organisationens program- och resursåtkomst.

- Lär dig mer om Azure AD, bland annat [grundläggande licensinformation, terminologi och associerade funktioner](active-directory-whatis.md).
