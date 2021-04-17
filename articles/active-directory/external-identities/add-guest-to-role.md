---
title: Lägga till en B2B-samarbetsanvändare till en roll – Azure Active Directory
description: Lägga till en gästanvändare till en roll i Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/08/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32a931fe43b6be406f0b2a4b8193c1631261f7e5
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575674"
---
# <a name="grant-permissions-to-users-from-partner-organizations-in-your-azure-active-directory-tenant"></a>Bevilja behörigheter till användare från partnerorganisationer i din Azure Active Directory klientorganisation

Azure Active Directory (Azure AD) B2B-samarbetsanvändare läggs till som gästanvändare i katalogen och gästbehörigheter i katalogen begränsas som standard. Ditt företag kan behöva vissa gästanvändare för att fylla roller med högre behörighet i din organisation. För att ge stöd för att definiera roller med högre behörighet kan gästanvändare läggas till i alla roller som du vill, baserat på organisationens behov.

Om en katalogroll tilldelas till en gästanvändare beviljas gästanvändaren ytterligare behörigheter som ingår i rollen, inklusive grundläggande läsbehörigheter. Se [Inbyggda roller i Azure AD.](https://docs.microsoft.com/azure/active-directory/roles/permissions-reference)

## <a name="default-role"></a>Standardroll

![Skärmbild som visar standardkatalogrollen](./media/add-guest-to-role/default-role.png)

## <a name="global-administrator-role"></a>Global administratörsroll

![Skärmbild som visar rollen global administratör](./media/add-guest-to-role/global-admin-role.png)

## <a name="limited-administrator-role"></a>Begränsad administratörsroll

![Skärmbild som visar den begränsade administratörsrollen](./media/add-guest-to-role/limited-admin-role.png)

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Användaregenskaper för B2B-samarbete](user-properties.md)
