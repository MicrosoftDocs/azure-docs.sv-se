---
title: Skapa & hantera en katalog med resurser i berättigandehantering – Azure AD
description: Lär dig hur du skapar en ny container med resurser och åtkomstpaket i Azure Active Directory berättigandehantering.
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
ms.openlocfilehash: b8cea26bcb0926cd3af360a6489377767d681079
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532564"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Skapa och hantera en katalog med resurser i Azure AD-berättigandehantering

## <a name="create-a-catalog"></a>Skapa en katalog

En katalog är en container med resurser och åtkomstpaket. Du skapar en katalog när du vill gruppera relaterade resurser och komma åt paket. Den som skapar katalogen blir den första katalogägaren. En katalogägare kan lägga till ytterligare katalogägare.

**Förutsättningsroll:** Global administratör, Användaradministratör eller Katalogskapare

1. I dialogrutan Azure Portal du på **Azure Active Directory** sedan på **Identity Governance**.

1. I den vänstra menyn klickar du **på Kataloger.**

    ![Berättigandehanteringskataloger i Azure Portal](./media/entitlement-management-catalog-create/catalogs.png)

1. Klicka **på Ny katalog.**

1. Ange ett unikt namn för katalogen och ange en beskrivning.

    Användarna ser den här informationen i information om ett åtkomstpaket.

1. Om du vill att åtkomstpaketen i den här katalogen ska vara tillgängliga för användare att begära så snart de har skapats anger **du Aktiverad** till **Ja.**

1. Om du vill tillåta att användare i valda externa kataloger kan begära åtkomstpaket i den här katalogen anger du Aktiverad för externa **användare** till **Ja.**

    ![Fönstret Ny katalog](./media/entitlement-management-shared/new-catalog.png)

1. Klicka **på Skapa** för att skapa katalogen.

### <a name="creating-a-catalog-programmatically"></a>Skapa en katalog programmatiskt

Du kan också skapa en katalog med Microsoft Graph.  En användare i en lämplig roll med ett program som har delegerad behörighet kan anropa API:et för att `EntitlementManagement.ReadWrite.All` [skapa en accessPackageCatalog](/graph/api/accesspackagecatalog-post?view=graph-rest-beta&preserve-view=true).

## <a name="add-resources-to-a-catalog"></a>Lägga till resurser i en katalog

Om du vill inkludera resurser i ett åtkomstpaket måste resurserna finnas i en katalog. De typer av resurser som du kan lägga till är grupper, program och SharePoint Online-webbplatser. Grupperna kan vara molnskapade eller Microsoft 365-grupper Azure AD-säkerhetsgrupper som skapats i molnet. Programmen kan vara Azure AD-företagsprogram, inklusive både SaaS-program och dina egna program som är federerade till Azure AD. Webbplatserna kan vara SharePoint Online-webbplatser eller SharePoint Online-webbplatssamlingar.

**Förutsättningsroll:** Se [Nödvändiga roller för att lägga till resurser i en katalog](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. I dialogrutan Azure Portal du på **Azure Active Directory** sedan på **Identity Governance**.

1. I den vänstra menyn klickar du **på Kataloger** och öppnar sedan den katalog som du vill lägga till resurser i.

1. I den vänstra menyn klickar du på **Resurser.**

1. Klicka på **Lägg till resurser.**

1. Klicka på en resurstyp: **Grupper och Team,** **Program** eller **SharePoint-webbplatser.**

    Om du inte ser en resurs som du vill lägga till eller om du inte kan lägga till en resurs kontrollerar du att du har den nödvändiga Azure AD-katalogrollen och behörighetshanteringsrollen. Du kan behöva ha någon med de roller som krävs för att lägga till resursen i katalogen. Mer information finns i Nödvändiga [roller för att lägga till resurser i en katalog.](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Välj en eller flera resurser av den typ som du vill lägga till i katalogen.

    ![Lägga till resurser i en katalog](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. När du är klar klickar du på **Lägg till**.

    Dessa resurser kan nu ingå i åtkomstpaket i katalogen.

### <a name="add-a-multi-geo-sharepoint-site-preview"></a>Lägga till en Multi-Geo SharePoint-webbplats (förhandsversion)

1. Om du har [Multi-Geo](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365) aktiverat för SharePoint väljer du den miljö som du vill välja platser från.
    
    :::image type="content" source="media/entitlement-management-catalog-create/sharepoint-multigeo-select.png" alt-text="Åtkomstpaket – Lägg till resursroller – Välj SharePoint Multi-Geo-platser":::

1. Välj sedan de webbplatser som du vill ska läggas till i katalogen. 

### <a name="adding-a-resource-to-a-catalog-programmatically"></a>Lägga till en resurs i en katalog programmatiskt

Du kan också lägga till en resurs i en katalog med Microsoft Graph.  En användare i en lämplig roll, eller en katalog- och resursägare, med ett program som har den delegerade behörigheten kan anropa API:et för att skapa en `EntitlementManagement.ReadWrite.All` [accessPackageResourceRequest](/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta&preserve-view=true).

## <a name="remove-resources-from-a-catalog"></a>Ta bort resurser från en katalog

Du kan ta bort resurser från en katalog. En resurs kan bara tas bort från en katalog om den inte används i något av katalogens åtkomstpaket.

**Förutsättningsroll:** Se [Nödvändiga roller för att lägga till resurser i en katalog](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. I dialogrutan Azure Portal du på **Azure Active Directory** sedan på **Identity Governance**.

1. I den vänstra menyn klickar du **på Kataloger och** öppnar sedan den katalog som du vill ta bort resurser från.

1. I den vänstra menyn klickar du på **Resurser**.

1. Välj de resurser som du vill ta bort.

1. Klicka **på Ta** bort (eller klicka på ellipsen (**...**) och klicka sedan på Ta **bort resurs**).


## <a name="add-additional-catalog-owners"></a>Lägga till ytterligare katalogägare

Den användare som skapade en katalog blir den första katalogägaren. Om du vill delegera hanteringen av en katalog lägger du till användare i rollen katalogägare. På så sätt kan du dela kataloghanteringsansvaret. 

Följ dessa steg för att tilldela en användare till rollen katalogägare:

**Förutsättningsroll:** Global administratör, användaradministratör eller katalogägare

1. I dialogrutan Azure Portal du på **Azure Active Directory** sedan på **Identity Governance**.

1. I den vänstra menyn klickar du **på Kataloger och** öppnar sedan den katalog som du vill lägga till administratörer i.

1. I den vänstra menyn klickar du på **Roller och administratörer.**

    ![Katalogroller och administratörer](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Klicka **på Lägg till** ägare för att välja medlemmar för dessa roller.

1. Klicka **på Välj** för att lägga till dessa medlemmar.

## <a name="edit-a-catalog"></a>Redigera en katalog

Du kan redigera namn och beskrivning för en katalog. Användarna ser den här informationen i information om ett åtkomstpaket.

**Förutsättningsroll:** Global administratör, användaradministratör eller katalogägare

1. I dialogrutan Azure Portal du på **Azure Active Directory** sedan på **Identity Governance**.

1. I den vänstra menyn klickar du **på Kataloger** och öppnar sedan den katalog som du vill redigera.

1. Klicka på Redigera på **katalogens** **översiktssida.**

1. Redigera katalogens namn, beskrivning eller aktiverade inställningar.

    ![Redigera kataloginställningar](./media/entitlement-management-shared/catalog-edit.png)

1. Klicka på **Spara**.

## <a name="delete-a-catalog"></a>Ta bort en katalog

Du kan ta bort en katalog, men bara om den inte har några åtkomstpaket.

**Förutsättningsroll:** Global administratör, användaradministratör eller katalogägare

1. I dialogrutan Azure Portal du på **Azure Active Directory** sedan på **Identity Governance**.

1. I den vänstra menyn klickar du **på Kataloger** och öppnar sedan den katalog som du vill ta bort.

1. Klicka på Ta bort **i katalogens** **översikt.**

1. I meddelanderutan som visas klickar du på **Ja.**

### <a name="deleting-a-catalog-programmatically"></a>Ta bort en katalog programmatiskt

Du kan också ta bort en katalog med Microsoft Graph.  En användare i en lämplig roll med ett program som har delegerad behörighet kan anropa API:et för att `EntitlementManagement.ReadWrite.All` [ta bort en accessPackageCatalog](/graph/api/accesspackagecatalog-delete?view=graph-rest-beta&preserve-view=true).

## <a name="next-steps"></a>Nästa steg

- [Delegera åtkomststyrning till åtkomstpakethanterare](entitlement-management-delegate-managers.md)
