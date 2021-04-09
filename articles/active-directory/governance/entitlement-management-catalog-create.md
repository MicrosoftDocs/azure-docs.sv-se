---
title: Skapa & hantera en katalog med resurser i hantering av rättigheter – Azure AD
description: Lär dig hur du skapar en ny behållare med resurser och åtkomst paket i Azure Active Directory rättighets hantering.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: ajburnle
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: d21783f0ef32289aad4b7d136dc8200d61fc0852
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592939"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Skapa och hantera en katalog med resurser i hantering av Azure AD-rättigheter

## <a name="create-a-catalog"></a>Skapa en katalog

En katalog är en behållare för resurser och åtkomst paket. Du skapar en katalog när du vill gruppera relaterade resurser och åtkomst paket. Vem som skapar katalogen blir den första katalog ägaren. En katalog ägare kan lägga till ytterligare katalog ägare.

**Nödvändig roll:** Global administratör, användar administratör eller katalog skapare

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. På den vänstra menyn klickar du på **kataloger**.

    ![Rättighets hanterings kataloger i Azure Portal](./media/entitlement-management-catalog-create/catalogs.png)

1. Klicka på **ny katalog**.

1. Ange ett unikt namn för katalogen och ange en beskrivning.

    Användare ser den här informationen i ett åtkomst pakets information.

1. Om du vill att åtkomst paketen i den här katalogen ska vara tillgängliga för användare att begära så fort de skapas, ställer du in **aktiverat** på **Ja**.

1. Om du vill tillåta att användare i valda externa kataloger kan begära åtkomst paket i den här katalogen, ställer du in **aktiverat för externa användare** på **Ja**.

    ![Fönstret ny katalog](./media/entitlement-management-shared/new-catalog.png)

1. Skapa katalogen genom att klicka på **skapa** .

### <a name="creating-a-catalog-programmatically"></a>Skapa en katalog program mässigt

Du kan också skapa en katalog med hjälp av Microsoft Graph.  En användare i en lämplig roll med ett program som har den delegerade `EntitlementManagement.ReadWrite.All` behörigheten kan anropa API: et för att [skapa en accessPackageCatalog](/graph/api/accesspackagecatalog-post?view=graph-rest-beta).

## <a name="add-resources-to-a-catalog"></a>Lägga till resurser i en katalog

Om du vill inkludera resurser i ett Access-paket måste resurserna finnas i en katalog. De typer av resurser som du kan lägga till är grupper, program och SharePoint Online-webbplatser. Grupperna kan vara molnbaserad Microsoft 365 grupper eller molnbaserade Azure AD-säkerhetsgrupper. Programmen kan vara Azure AD Enterprise-program, inklusive både SaaS-program och dina egna program federerade till Azure AD. Platserna kan vara SharePoint Online-webbplatser eller SharePoint Online-webbplats samlingar.

**Nödvändig roll:** Se de [roller som krävs för att lägga till resurser i en katalog](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. Klicka på **kataloger** i den vänstra menyn och öppna den katalog som du vill lägga till resurser i.

1. Klicka på **resurser** på den vänstra menyn.

1. Klicka på **Lägg till resurser**.

1. Klicka på en resurs typ: **grupper och team**, **program** eller **SharePoint-webbplatser**.

    Om du inte ser en resurs som du vill lägga till, eller om du inte kan lägga till en resurs, kontrollerar du att du har den nödvändiga rollen för Azure AD-katalogen och rättighets hanterings rollen. Du kanske måste ha någon med de nödvändiga rollerna för att lägga till resursen i katalogen. Mer information finns i de [roller som krävs för att lägga till resurser i en katalog](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Välj en eller flera resurser av den typ som du vill lägga till i katalogen.

    ![Lägga till resurser i en katalog](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. Klicka på **Lägg till** när du är färdig.

    Dessa resurser kan nu inkluderas i åtkomst paket i katalogen.

### <a name="add-a-multi-geo-sharepoint-site"></a>Lägg till en SharePoint-webbplats med flera geo-platser

1. Om du har aktiverat [multi-geo](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365) för SharePoint väljer du den miljö som du vill välja platser från.
    
    :::image type="content" source="media/entitlement-management-catalog-create/sharepoint-multigeo-select.png" alt-text="Åtkomst paket – Lägg till resurs roller – Välj SharePoint multi-geo-platser":::

1. Välj sedan de platser som du vill lägga till i katalogen. 

### <a name="adding-a-resource-to-a-catalog-programmatically"></a>Lägga till en resurs i en katalog program mässigt

Du kan också lägga till en resurs i en katalog med hjälp av Microsoft Graph.  En användare i en lämplig roll, eller en katalog-och resurs ägare, med ett program som har den delegerade `EntitlementManagement.ReadWrite.All` behörigheten kan anropa API: et för att [skapa en accessPackageResourceRequest](/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta).

## <a name="remove-resources-from-a-catalog"></a>Ta bort resurser från en katalog

Du kan ta bort resurser från en katalog. En resurs kan bara tas bort från en katalog om den inte används i någon av katalogens åtkomst paket.

**Nödvändig roll:** Se de [roller som krävs för att lägga till resurser i en katalog](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. Klicka på **kataloger** i den vänstra menyn och öppna den katalog som du vill ta bort resurser från.

1. Klicka på **resurser** på den vänstra menyn.

1. Välj de resurser som du vill ta bort.

1. Klicka på **ta bort** (eller klicka på ellipsen (**...**) och klicka sedan på **ta bort resurs**).


## <a name="add-additional-catalog-owners"></a>Lägg till ytterligare katalog ägare

Användaren som skapade en katalog blir den första katalog ägaren. Om du vill delegera hantering av en katalog lägger du till användare i Katalogens ägare roll. Detta hjälper dig att dela ansvars områden för katalog hantering. 

Följ dessa steg för att tilldela en användare rollen som katalog ägare:

**Nödvändig roll:** Global administratör, användar administratör eller katalog ägare

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. Klicka på **kataloger** i den vänstra menyn och öppna den katalog som du vill lägga till administratörer i.

1. På den vänstra menyn klickar du på **roller och administratörer**.

    ![Katalog roller och administratörer](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Klicka på **Lägg till ägare** för att välja medlemmar för dessa roller.

1. Klicka på **Välj** för att lägga till dessa medlemmar.

## <a name="edit-a-catalog"></a>Redigera en katalog

Du kan redigera namn och beskrivning för en katalog. Användarna ser den här informationen i ett åtkomst pakets information.

**Nödvändig roll:** Global administratör, användar administratör eller katalog ägare

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. Klicka på **kataloger** i den vänstra menyn och öppna den katalog som du vill redigera.

1. På sidan **Översikt** för katalogen klickar du på **Redigera**.

1. Redigera katalogens namn, beskrivning eller aktiverade inställningar.

    ![Redigera katalog inställningar](./media/entitlement-management-shared/catalog-edit.png)

1. Klicka på **Spara**.

## <a name="delete-a-catalog"></a>Ta bort en katalog

Du kan ta bort en katalog, men bara om den inte har några åtkomst paket.

**Nödvändig roll:** Global administratör, användar administratör eller katalog ägare

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. Klicka på **kataloger** i den vänstra menyn och öppna den katalog som du vill ta bort.

1. Klicka på **ta bort** i katalogens **Översikt**.

1. Klicka på **Ja** i meddelande rutan som visas.

### <a name="deleting-a-catalog-programmatically"></a>Ta bort en katalog program mässigt

Du kan också ta bort en katalog med hjälp av Microsoft Graph.  En användare i en lämplig roll med ett program som har den delegerade `EntitlementManagement.ReadWrite.All` behörigheten kan anropa API: et för att [ta bort en accessPackageCatalog](/graph/api/accesspackagecatalog-delete?view=graph-rest-beta).

## <a name="next-steps"></a>Nästa steg

- [Delegera åtkomst styrning för att komma åt paket hanterare](entitlement-management-delegate-managers.md)