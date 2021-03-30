---
title: Egenskaper för flera klient interaktioner – Azure AD | Microsoft Docs
description: Förstå Azure Active Directory organisationernas data självständighet
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: overview
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ce3e4c6a7708fba15560564577c9b01722c8aec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96548060"
---
# <a name="understand-how-multiple-azure-active-directory-organizations-interact"></a>Förstå hur flera Azure Active Directory organisationer interagerar

I Azure Active Directory (Azure AD) är varje Azure AD-organisation helt oberoende: en peer som är logiskt oberoende av de andra Azure AD-organisationer som du hanterar. Detta oberoende mellan organisationer omfattar resurs oberoende, administrativt oberoende och för oberoende synkronisering. Det finns ingen överordnad-underordnad relation mellan organisationer.

## <a name="resource-independence"></a>Resurs oberoende

* Om du skapar eller tar bort en Azure AD-resurs i en organisation, har den ingen inverkan på någon resurs i en annan organisation, med undantag för externa användare.
* Om du registrerar ett av dina domän namn med en organisation kan den inte användas av någon annan organisation.

## <a name="administrative-independence"></a>Administrativt oberoende

Om en icke-administrativ användare av organisation ' contoso ' skapar en test organisations "test":

* Som standard läggs den användare som skapar en organisation till som en extern användare i den nya organisationen och tilldelas rollen global administratör i organisationen.
* Administratörerna av organisationen contoso har ingen direkt administratörs behörighet till organisationen "test", såvida inte en administratör av "test" specifikt ger dem dessa privilegier. Administratörer av contoso kan dock kontrol lera åtkomsten till organisationens test om de styr det användar konto som skapade "test".
* Om du lägger till eller tar bort en Azure AD-roll för en användare i en organisation, påverkar inte ändringen de roller som användaren har tilldelats i någon annan Azure AD-organisation.

## <a name="synchronization-independence"></a>Oberoende synkronisering

Du kan konfigurera varje Azure AD-organisation fristående för att hämta data som synkroniseras från en enda instans av antingen:

* Azure AD Connect-verktyget, för att synkronisera data med en enda AD-skog.
* Azure Active Directory Connector för Forefront Identity Manager, för att synkronisera data med en eller flera lokala skogar och/eller andra data källor än Azure AD.

## <a name="add-an-azure-ad-organization"></a>Lägg till en Azure AD-organisation

Om du vill lägga till en Azure AD-organisation i Azure Portal loggar du in på [Azure Portal](https://portal.azure.com) med ett konto som är en global Azure AD-administratör och väljer **ny**.

> [!NOTE]
> Till skillnad från andra Azure-resurser är Azure AD-organisationerna inte underordnade resurser till en Azure-prenumeration. Om din Azure-prenumeration har avbrutits eller har upphört att gälla kan du fortfarande komma åt din Azure AD-organisations data med hjälp av Azure PowerShell, Microsoft Graph-API: et eller Microsoft 365 administrations centret. Du kan också [associera en annan prenumeration med organisationen](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Nästa steg

Information om Azure AD-licensiering och bästa praxis finns i [Vad är Azure Active Directory-licensiering?](../fundamentals/active-directory-licensing-whatis-azure-portal.md).
