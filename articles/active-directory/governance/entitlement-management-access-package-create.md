---
title: Skapa ett nytt Access-paket i hantering av rättigheter – Azure AD
description: Lär dig hur du skapar ett nytt Access-paket med resurser som du vill dela i Azure Active Directory rättighets hantering.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3df08272b352ee789c9879b1118105c435cffbd
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011093"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management"></a>Skapa ett nytt Access-paket i hantering av Azure AD-berättigande

Med ett Access-paket kan du utföra en enstaka konfiguration av resurser och principer som automatiskt administrerar åtkomst för åtkomst paketets livs längd. I den här artikeln beskrivs hur du skapar ett nytt Access-paket.

## <a name="overview"></a>Översikt

Alla åtkomst paket måste placeras i en behållare som kallas för en katalog. En katalog definierar vilka resurser du kan lägga till i ditt åtkomst paket. Om du inte anger någon katalog placeras ditt åtkomst paket i den allmänna katalogen. För närvarande kan du inte flytta ett befintligt Access-paket till en annan katalog.

Om du är en Access Package Manager kan du inte lägga till resurser som du äger i en katalog. Du är begränsad till att använda de resurser som är tillgängliga i katalogen. Om du behöver lägga till resurser i en katalog kan du be katalog ägaren.

Alla åtkomst paket måste ha minst en princip. Principer anger vem som kan begära åtkomst paketet och även godkännande-och livs cykel inställningar. När du skapar ett nytt Access-paket kan du skapa en första princip för användare i din katalog, för användare som inte finns i katalogen, endast för administratörs direkta tilldelningar eller så kan du välja att skapa principen senare.

![Skapa ett åtkomstpaket](./media/entitlement-management-access-package-create/access-package-create.png)

Här följer de övergripande stegen för att skapa ett nytt Access-paket.

1. Starta processen för att skapa ett nytt Access-paket i identitets styrning.

1. Välj den katalog som du vill skapa åtkomst paketet i.

1. Lägg till resurser från katalogen i ditt Access-paket.

1. Tilldela resurs roller för varje resurs.

1. Ange användare som kan begära åtkomst.

1. Ange inställningar för godkännande.

1. Ange livs cykel inställningar.

## <a name="start-new-access-package"></a>Starta nytt Access-paket

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Klicka på **Azure Active Directory** och klicka sedan på **identitets styrning**.

1. Klicka på **åtkomst paket** på den vänstra menyn.

1. Klicka på **nytt Access-paket**.
   
    ![Hantering av rättigheter i Azure Portal](./media/entitlement-management-shared/access-packages-list.png)

## <a name="basics"></a>Grundläggande inställningar

På fliken **grundläggande** ger du åtkomst paketet ett namn och anger vilken katalog som du vill skapa åtkomst paketet i.

1. Ange ett visnings namn och en beskrivning för Access-paketet. Användarna ser den här informationen när de skickar en begäran om åtkomst paketet.

1. I list rutan **katalog** väljer du den katalog som du vill skapa åtkomst paketet i. Du kan till exempel ha en katalog ägare som hanterar alla marknadsförings resurser som kan begäras. I det här fallet kan du välja marknadsförings katalogen.

    Du ser bara kataloger som du har behörighet att skapa åtkomst paket i. Om du vill skapa ett Access-paket i en befintlig katalog måste du vara global administratör eller användar administratör, eller så måste du vara katalog ägare eller åtkomst till paket hanteraren i katalogen.

    ![Åtkomst paket – grundläggande information](./media/entitlement-management-access-package-create/basics.png)

    Om du är global administratör, en användar administratör eller katalog skapad och du vill skapa ditt åtkomst paket i en ny katalog som inte finns med i listan klickar du på **Skapa ny katalog**. Ange katalogens namn och beskrivning och klicka sedan på **skapa**.

    Det åtkomst paket som du skapar och alla resurser som ingår i det kommer att läggas till i den nya katalogen. Du kan också lägga till ytterligare katalog ägare senare.

1. Klicka på **Nästa**.

## <a name="resource-roles"></a>Resursroller

På fliken **resurs roller** väljer du de resurser som du vill ta med i åtkomst paketet. Användare som begär och tar emot åtkomst paketet får alla resurs roller i åtkomst paketet.

1. Klicka på den resurs typ som du vill lägga till (**grupper och team**, **program** eller **SharePoint-platser**).

1. I fönstret Välj som visas väljer du en eller flera resurser i listan.

    ![Åtkomst paket – resurs roller](./media/entitlement-management-access-package-create/resource-roles.png)

    Om du skapar åtkomst paketet i den allmänna katalogen eller i en ny katalog kan du välja vilken resurs som helst från den katalog som du äger. Du måste vara minst en global administratör, en användar administratör eller en katalogs kapare.

    Om du skapar ett Access-paket i en befintlig katalog kan du välja vilken resurs som helst som redan finns i katalogen utan att du äger den.

    Om du är global administratör, en användar administratör eller en katalog ägare har du ytterligare möjlighet att välja resurser som inte finns i katalogen än. Om du väljer resurser som inte finns i den valda katalogen, kommer dessa resurser också att läggas till i katalogen för andra katalog administratörer för att bygga åtkomst paket med. Om du vill se alla resurser som kan läggas till i katalogen markerar du kryss rutan **Se alla** överst i fönstret Välj. Om du bara vill välja resurser som för närvarande finns i den valda katalogen lämnar du kryss rutan **alla** omarkerade (standard tillstånd).

1. När du har valt resurserna väljer du den roll som du vill att användarna ska tilldelas för resursen i listan **roll** .

    ![Åtkomst paket – resurs roll val](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Klicka på **Nästa**.

>[!NOTE]
>Du kan lägga till dynamiska grupper i en katalog och till ett Access-paket. Du kan dock bara välja ägar rollen när du hanterar en dynamisk grupp resurs i ett Access-paket.

## <a name="requests"></a>Begäranden

På fliken **förfrågningar** skapar du den första principen för att ange vem som kan begära åtkomst paketet och även godkännande inställningarna. Senare kan du skapa fler principer för begäran så att fler grupper av användare kan begära åtkomst paketet med sina egna godkännande inställningar.

![Fliken åtkomst paket – begär Anden](./media/entitlement-management-access-package-create/requests.png)

Utför stegen i något av följande avsnitt, beroende på vem du vill kunna begära det här åtkomst paketet.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="review--create"></a>Granska + skapa

På fliken **Granska + skapa** kan du granska dina inställningar och kontrol lera eventuella verifierings fel.

1. Granska åtkomst paketets inställningar

    ![Åtkomst paket – aktivera princip inställning](./media/entitlement-management-access-package-create/review-create.png)

1. Skapa åtkomst paketet genom att klicka på **skapa** .

    Det nya Access-paketet visas i listan över åtkomst paket.

## <a name="creating-an-access-package-programmatically"></a>Skapa ett Access-paket program mässigt

Du kan också skapa ett Access-paket med hjälp av Microsoft Graph.  En användare i en lämplig roll med ett program som har den delegerade `EntitlementManagement.ReadWrite.All` behörigheten kan anropa API: et för att

1. [Visa en lista över accessPackageResources i katalogen](/graph/api/accesspackagecatalog-list?tabs=http&view=graph-rest-beta) och [skapa en accessPackageResourceRequest](/graph/api/accesspackageresourcerequest-post?tabs=http&view=graph-rest-beta) för alla resurser som ännu inte finns i katalogen.
1. [Ange accessPackageResourceRoles](/graph/api/accesspackage-list-accesspackageresourcerolescopes?tabs=http&view=graph-rest-beta) för varje accessPackageResource i en accessPackageCatalog. Den här listan över roller används sedan för att välja en roll när du sedan skapar en accessPackageResourceRoleScope.
1. [Skapa en accessPackage](/graph/tutorial-access-package-api?view=graph-rest-beta).
1. [Skapa en accessPackageAssignmentPolicy](/graph/api/accesspackageassignmentpolicy-post?tabs=http&view=graph-rest-beta).
1. [Skapa en accessPackageResourceRoleScope](/graph/api/accesspackage-post-accesspackageresourcerolescopes?tabs=http&view=graph-rest-beta) för varje resurs roll som behövs i åtkomst paketet.

## <a name="next-steps"></a>Nästa steg

- [Dela länk för att begära ett Access-paket](entitlement-management-access-package-settings.md)
- [Ändra resurs roller för ett Access-paket](entitlement-management-access-package-resources.md)
- [Tilldela en användare direkt till åtkomst paketet](entitlement-management-access-package-assignments.md)
- [Skapa en åtkomst granskning för ett Access-paket](entitlement-management-access-reviews-create.md)
