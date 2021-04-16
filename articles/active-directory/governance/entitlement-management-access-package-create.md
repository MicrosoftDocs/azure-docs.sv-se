---
title: Skapa ett nytt åtkomstpaket i berättigandehantering – Azure AD
description: Lär dig hur du skapar ett nytt åtkomstpaket med resurser som du vill dela Azure Active Directory berättigandehantering.
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
ms.openlocfilehash: cb0312d905284f8c5a9817e9550d340bf6135032
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532201"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management"></a>Skapa ett nytt åtkomstpaket i Azure AD-berättigandehantering

Med ett åtkomstpaket kan du göra en enda konfiguration av resurser och principer som automatiskt administrerar åtkomsten under åtkomstpaketets livslängd. Den här artikeln beskriver hur du skapar ett nytt åtkomstpaket.

## <a name="overview"></a>Översikt

Alla åtkomstpaket måste finnas i en container som kallas en katalog. En katalog definierar vilka resurser du kan lägga till i ditt åtkomstpaket. Om du inte anger någon katalog kommer ditt åtkomstpaket att läggas till i katalogen Allmänt. För närvarande kan du inte flytta ett befintligt åtkomstpaket till en annan katalog.

Om du är en åtkomstpakethanterare kan du inte lägga till resurser som du äger i en katalog. Du är begränsad till att använda de resurser som är tillgängliga i katalogen. Om du behöver lägga till resurser i en katalog kan du be katalogägaren.

Alla åtkomstpaket måste ha minst en princip. Principer anger vem som kan begära åtkomstpaketet och inställningar för godkännande och livscykel. När du skapar ett nytt åtkomstpaket kan du skapa en inledande princip för användare i din katalog, för användare som inte är i din katalog, endast för administratörs direkttilldelningar, eller så kan du välja att skapa principen senare.

![Skapa ett åtkomstpaket](./media/entitlement-management-access-package-create/access-package-create.png)

Här är de avancerade stegen för att skapa ett nytt åtkomstpaket.

1. I Identity Governance startar du processen för att skapa ett nytt åtkomstpaket.

1. Välj den katalog som du vill skapa åtkomstpaketet i.

1. Lägg till resurser från katalogen i ditt åtkomstpaket.

1. Tilldela resursroller för varje resurs.

1. Ange användare som kan begära åtkomst.

1. Ange eventuella godkännandeinställningar.

1. Ange livscykelinställningar.

## <a name="start-new-access-package"></a>Starta nytt åtkomstpaket

**Förutsättningsroll:** Global administratör, användaradministratör, katalogägare eller åtkomstpakethanterare

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Klicka **Azure Active Directory** och sedan på **Identity Governance**.

1. Klicka på Åtkomstpaket på **den vänstra menyn.**

1. Klicka **på Nytt åtkomstpaket**.
   
    ![Berättigandehantering i Azure Portal](./media/entitlement-management-shared/access-packages-list.png)

## <a name="basics"></a>Grundläggande inställningar

På fliken **Grundläggande ger** du åtkomstpaketet ett namn och anger vilken katalog som åtkomstpaketet ska skapas i.

1. Ange ett visningsnamn och en beskrivning för åtkomstpaketet. Användarna ser den här informationen när de skickar en begäran om åtkomstpaketet.

1. I **listrutan** Katalog väljer du den katalog som du vill skapa åtkomstpaketet i. Du kan till exempel ha en katalogägare som hanterar alla marknadsföringsresurser som kan begäras. I det här fallet kan du välja marknadsföringskatalogen.

    Du ser bara kataloger som du har behörighet att skapa åtkomstpaket i. Om du vill skapa ett åtkomstpaket i en befintlig katalog måste du vara Global administratör administratör eller användaradministratör, eller så måste du vara katalogägare eller åtkomstpakethanterare i katalogen.

    ![Åtkomstpaket – Grunder](./media/entitlement-management-access-package-create/basics.png)

    Om du är en Global administratör, en användaradministratör eller katalogskapare och du vill skapa ditt åtkomstpaket i en ny katalog som inte visas klickar du på **Skapa ny katalog.** Ange katalognamnet och beskrivningen och klicka sedan på **Skapa.**

    Det åtkomstpaket som du skapar och alla resurser som ingår i det läggs till i den nya katalogen. Du kan också lägga till ytterligare katalogägare senare.

1. Klicka på **Nästa**.

## <a name="resource-roles"></a>Resursroller

På fliken **Resursroller** väljer du de resurser som ska ingå i åtkomstpaketet. Användare som begär och tar emot åtkomstpaketet får alla resursroller i åtkomstpaketet.

1. Klicka på den resurstyp som du vill lägga till (**Grupper och Team,** **Program** eller **SharePoint-webbplatser**).

1. I fönstret Välj som visas väljer du en eller flera resurser i listan.

    ![Åtkomstpaket – resursroller](./media/entitlement-management-access-package-create/resource-roles.png)

    Om du skapar åtkomstpaketet i den allmänna katalogen eller i en ny katalog kan du välja en resurs från den katalog som du äger. Du måste vara minst en Global administratör, användaradministratör eller katalogskapare.

    Om du skapar åtkomstpaketet i en befintlig katalog kan du välja en resurs som redan finns i katalogen utan att äga den.

    Om du är Global administratör, användaradministratör eller katalogägare kan du välja resurser som du äger och som ännu inte finns i katalogen. Om du väljer resurser som för närvarande inte finns i den valda katalogen läggs dessa resurser också till i katalogen så att andra katalogadministratörer kan skapa åtkomstpaket med. Om du vill se alla resurser som kan läggas till i katalogen markerar du **kryssrutan** Visa alla överst i fönstret Välj. Om du bara vill välja resurser som för närvarande finns i den valda katalogen lämnar du kryssrutan **Visa alla** avmarkerade (standardtillstånd).

1. När du har valt resurserna går du till **listan** Roll och väljer den roll som du vill att användarna ska tilldelas för resursen.

    ![Åtkomstpaket – Val av resursroll](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Klicka på **Nästa**.

>[!NOTE]
>Du kan lägga till dynamiska grupper i en katalog och i ett åtkomstpaket. Du kan dock bara välja rollen Ägare när du hanterar en dynamisk gruppresurs i ett åtkomstpaket.

## <a name="requests"></a>Begäranden

På fliken **Begäranden** skapar du den första principen för att ange vem som kan begära åtkomstpaketet och även godkännandeinställningarna. Senare kan du skapa fler begärandeprinciper så att ytterligare grupper av användare kan begära åtkomstpaketet med sina egna godkännandeinställningar.

![Åtkomstpaket – fliken Begäranden](./media/entitlement-management-access-package-create/requests.png)

Beroende på vem du vill ska kunna begära det här åtkomstpaketet utför du stegen i något av följande avsnitt.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="review--create"></a>Granska + skapa

På fliken **Granska + skapa** kan du granska inställningarna och söka efter verifieringsfel.

1. Granska åtkomstpaketets inställningar

    ![Åtkomstpaket – Aktivera principinställning](./media/entitlement-management-access-package-create/review-create.png)

1. Klicka **på Skapa** för att skapa åtkomstpaketet.

    Det nya åtkomstpaketet visas i listan över åtkomstpaket.

## <a name="creating-an-access-package-programmatically"></a>Skapa ett åtkomstpaket programmatiskt

Du kan också skapa ett åtkomstpaket med hjälp av Microsoft Graph.  En användare i en lämplig roll med ett program som har den delegerade `EntitlementManagement.ReadWrite.All` behörigheten kan anropa API:et till

1. [Visa en lista med accessPackageResources](/graph/api/accesspackagecatalog-list?tabs=http&view=graph-rest-beta&preserve-view=true) i katalogen och skapa en [accessPackageResourceRequest](/graph/api/accesspackageresourcerequest-post?tabs=http&view=graph-rest-beta&preserve-view=true) för alla resurser som ännu inte finns i katalogen.
1. [Visa en lista med accessPackageResourceRoles](/graph/api/accesspackage-list-accesspackageresourcerolescopes?tabs=http&view=graph-rest-beta&preserve-view=true) för varje accessPackageResource i en accessPackageCatalog. Den här listan över roller används sedan för att välja en roll när du därefter skapar ett accessPackageResourceRoleScope.
1. [Skapa ett accessPackage](/graph/tutorial-access-package-api&view=graph-rest-beta&preserve-view=true).
1. [Skapa en accessPackageAssignmentPolicy](/graph/api/accesspackageassignmentpolicy-post?tabs=http&view=graph-rest-beta&preserve-view=true).
1. [Skapa ett accessPackageResourceRoleScope för](/graph/api/accesspackage-post-accesspackageresourcerolescopes?tabs=http&view=graph-rest-beta&preserve-view=true) varje resursroll som behövs i åtkomstpaketet.

## <a name="next-steps"></a>Nästa steg

- [Dela länk för att begära ett åtkomstpaket](entitlement-management-access-package-settings.md)
- [Ändra resursroller för ett åtkomstpaket](entitlement-management-access-package-resources.md)
- [Tilldela en användare direkt till åtkomstpaketet](entitlement-management-access-package-assignments.md)
- [Skapa en åtkomstgranskning för ett åtkomstpaket](entitlement-management-access-reviews-create.md)
