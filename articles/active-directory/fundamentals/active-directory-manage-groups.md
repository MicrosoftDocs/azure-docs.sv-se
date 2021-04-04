---
title: Hantera app & Resource Access med grupper – Azure AD
description: Lär dig mer om hur du hanterar åtkomst till din organisations molnbaserade appar, lokala appar och resurser med hjälp av Azure Active Directory grupper.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: ajburnle
ms.reviewer: piotrci
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 735f838ad4532b140eddcb4ce1ced24fba9a81be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92369122"
---
# <a name="manage-app-and-resource-access-using-azure-active-directory-groups"></a>Hantera app- och resursåtkomst med Azure Active Directory-grupper
Azure Active Directory (Azure AD) låter dig använda grupper för att hantera åtkomst till dina molnbaserade appar, lokala appar och dina resurser. Dina resurser kan vara en del av Azure AD-organisationen, till exempel behörigheter för att hantera objekt via roller i Azure AD eller utanför organisationen, till exempel för SaaS-appar (program vara som en tjänst), Azure-tjänster, SharePoint-webbplatser och lokala resurser.

>[!NOTE]
> I Azure Portal kan du se vissa grupper vars medlemskap och grupp information som du inte kan hantera i portalen:
>
> - Grupper som synkroniserats från lokala Active Directory kan bara hanteras i lokala Active Directory.
> - Andra grupp typer, till exempel distributions listor och e-postaktiverade säkerhets grupper, hanteras endast i administrations Center för Exchange eller Microsoft 365 administrations Center. Du måste logga in i administrations Center för Exchange eller Microsoft 365 administrations Center för att hantera dessa grupper.

## <a name="how-access-management-in-azure-ad-works"></a>Så här fungerar åtkomst hantering i Azure AD

Med Azure AD kan du ge åtkomst till organisationens resurser genom att ge åtkomst behörighet till en enskild användare eller till en hel Azure AD-grupp. Med hjälp av grupper kan resursägaren (eller Azure AD Directory-ägaren) tilldela en uppsättning åtkomstbehörigheter till alla medlemmar i gruppen, i stället för att behöva ange behörigheterna individuellt. Resurs-eller katalog ägaren kan också ge hanterings behörighet för medlems listan till någon annan, till exempel en avdelnings chef eller en supportavdelningen-administratör, vilket gör att personen kan lägga till och ta bort medlemmar efter behov. Mer information om hur du hanterar grupp ägare finns i [Hantera grupp ägare](active-directory-accessmanagement-managing-group-owners.md)

![Åtkomsthanteringsdiagram i Azure Active Directory](./media/active-directory-manage-groups/active-directory-access-management-works.png)

## <a name="ways-to-assign-access-rights"></a>Sätt att tilldela åtkomst behörigheter

Det finns fyra sätt att tilldela resurs åtkomst rättigheter till dina användare:

- **Direkt tilldelning.** Resurs ägaren tilldelar användaren till resursen direkt.

- **Grupp tilldelning.** Resurs ägaren tilldelar en Azure AD-grupp till resursen, som automatiskt ger alla grupp medlemmar åtkomst till resursen. Grupp medlemskap hanteras av både grupp ägaren och resurs ägaren, vilket gör att ägaren kan lägga till eller ta bort medlemmar från gruppen. Mer information om hur du lägger till eller tar bort grupp medlemskap finns i [så här gör du: Lägg till eller ta bort en grupp från en annan grupp med hjälp av Azure Active Directory portalen](active-directory-groups-membership-azure-portal.md). 

- **Regel baserad tilldelning.** Resurs ägaren skapar en grupp och använder en regel för att definiera vilka användare som är tilldelade till en speciell resurs. Regeln baseras på attribut som tilldelas enskilda användare. Resurs ägaren hanterar regeln och avgör vilka attribut och värden som krävs för att tillåta åtkomst till resursen. Mer information finns i [skapa en dynamisk grupp och kontrol lera status](../enterprise-users/groups-create-rule.md).

    Du kan också titta på den här korta videon för en snabb förklaring av hur du skapar och använder dynamiska grupper:

    >[!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD--Introduction-to-Dynamic-Memberships-for-Groups/player]

- **Tilldelning av externa utfärdare.** Åtkomst kommer från en extern källa, till exempel en lokal katalog eller en SaaS-app. I den här situationen tilldelar resurs ägaren en grupp för att ge åtkomst till resursen och den externa källan hanterar grupp medlemmar.

   ![Översikt över åtkomsthanteringsdiagram](./media/active-directory-manage-groups/access-management-overview.png)

## <a name="can-users-join-groups-without-being-assigned"></a>Kan användarna ansluta till grupper utan att tilldelas?
Grupp ägaren kan låta användarna hitta sina egna grupper för att gå med i stället för att tilldela dem. Ägaren kan också ställa in gruppen för att automatiskt godkänna alla användare som ansluter till eller kräver godkännande.

När en användare begär att ansluta till en grupp vidarebefordras begäran till grupp ägaren. Om det behövs kan ägaren godkänna begäran och användaren meddelas om grupp medlemskapet. Men om du har flera ägare och en av dem avgodkänner, meddelas användaren, men läggs inte till i gruppen. Mer information och anvisningar om hur du gör det möjligt för dina användare att ansluta till grupper finns i [Konfigurera Azure AD så att användarna kan begära att ansluta till grupper](../enterprise-users/groups-self-service-management.md)

## <a name="next-steps"></a>Nästa steg
Nu när du har en introduktion till åtkomst hantering med hjälp av grupper börjar du hantera dina resurser och appar.

- [Skapa en ny grupp med Azure Active Directory](active-directory-groups-create-azure-portal.md) eller [skapa och hantera en ny grupp med PowerShell-cmdletar](../enterprise-users/groups-settings-v2-cmdlets.md)

- [Använda grupper för att tilldela åtkomst till en integrerad SaaS-app](../enterprise-users/groups-saasapps.md)

- [Synkronisera en lokal grupp till Azure med hjälp av Azure AD Connect](../hybrid/whatis-hybrid-identity.md)
