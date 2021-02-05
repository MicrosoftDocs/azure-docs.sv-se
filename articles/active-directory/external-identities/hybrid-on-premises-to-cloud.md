---
title: Synkronisera lokala partner konton till molnet som B2B-användare – Azure AD
description: Ge lokalt hanterade externa partners åtkomst till både lokala och molnbaserade resurser med samma autentiseringsuppgifter med Azure AD B2B-samarbete.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 11/03/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29aeca30e1fbdd28d3f69597d902a9b714056cd5
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575931"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Bevilja lokalt hanterade partnerkonton åtkomst till molnresurser med Azure AD B2B-samarbete

Innan Azure Active Directory (Azure AD) har organisationer med lokala identitets system traditionellt hanterade partner konton i sin lokala katalog. När du börjar flytta appar till Azure AD i en sådan organisation, vill du se till att dina partner har åtkomst till de resurser som de behöver. Det bör inte vara viktigt om resurserna är lokala eller i molnet. Du vill också att dina partner användare ska kunna använda samma inloggnings uppgifter för både lokala och Azure AD-resurser. 

Om du skapar konton för dina externa partner i din lokala katalog (till exempel skapar du ett konto med inloggnings namnet "wmoran" för en extern användare med namnet Wendy Moran i din partners.contoso.com-domän) kan du nu synkronisera dessa konton till molnet. Mer specifikt kan du använda Azure AD Connect för att synkronisera partner kontona till molnet, vilket skapar ett användar konto med UserType = gäst. Detta gör det möjligt för dina partner användare att komma åt moln resurser med samma autentiseringsuppgifter som de lokala kontona, utan att ge dem mer åtkomst än de behöver.

> [!NOTE]
> Se även hur du [bjuder in interna användare till B2B-samarbete](invite-internal-users.md). Med den här funktionen kan du bjuda in interna gäst användare att använda B2B-samarbete, oavsett om du har synkroniserat deras konton från din lokala katalog till molnet. När användaren har godkänt inbjudan att använda B2B-samarbete kan de använda sina egna identiteter och autentiseringsuppgifter för att logga in på de resurser som du vill att de ska ha åtkomst till. Du behöver inte hantera lösen ord eller hantera livs cykel för konton.

## <a name="identify-unique-attributes-for-usertype"></a>Identifiera unika attribut för UserType

Innan du aktiverar synkronisering av UserType-attributet måste du först bestämma hur UserType-attributet ska härledas från lokala Active Directory. Vilka parametrar i din lokala miljö är unika för dina externa medarbetare? Bestäm en parameter som särskiljer dessa externa medarbetare från medlemmar i din organisation.

Två vanliga metoder för detta är att:

- Ange ett oanvänt lokalt Active Directory-attribut (till exempel extensionAttribute1) som ska användas som källattribut. 
- Du kan också härleda värdet för attributet UserType från andra egenskaper. Till exempel vill du synkronisera alla användare som gäst om deras lokala Active Directory UserPrincipalName-attribut slutar med domänen *\@ partners.contoso.com*.
 
Detaljerade attribut krav finns i [Aktivera synkronisering av UserType](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Konfigurera Azure AD Connect för att synkronisera användare till molnet

När du har identifierat det unika attributet kan du konfigurera Azure AD Connect att synkronisera dessa användare till molnet, vilket skapar ett användar konto med UserType = gäst. Från en behörighets punkt i vyn kan de här användarna inte särskiljas från B2B-användare som skapats genom inbjudan till Azure AD B2B-samarbete.

Anvisningar för implementering finns i [Aktivera synkronisering av UserType](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Nästa steg

- [Azure Active Directory B2B-samarbete för Hybrid organisationer](hybrid-organizations.md)
- [Bevilja B2B-användare i Azure AD åtkomst till dina lokala program](hybrid-cloud-to-on-premises.md)
- En översikt över Azure AD Connect finns i [integrera dina lokala kataloger med Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

