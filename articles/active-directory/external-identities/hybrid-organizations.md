---
title: B2B-samarbete för Hybrid organisationer – Azure AD
description: Ge partner åtkomst till både lokala och molnbaserade resurser med Azure AD B2B-samarbete.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 427f7ad4d6a1b9839b1197ef9f7ca15400ea0f59
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87910106"
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Azure Active Directory B2B-samarbete för Hybrid organisationer

Azure Active Directory (Azure AD) B2B-samarbete gör det enkelt för dig att ge dina externa partner åtkomst till appar och resurser i din organisation. Detta gäller även i en hybrid konfiguration där du har både lokala och molnbaserade resurser. Det spelar ingen roll om du för närvarande hanterar externa partner konton lokalt i ditt lokala identitets system, eller om du hanterar de externa kontona i molnet som Azure AD B2B-användare. Du kan nu ge dessa användare åtkomst till resurser på någon av platserna, med samma inloggnings uppgifter för båda miljöerna.

## <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-apps"></a>Bevilja B2B-användare i Azure AD åtkomst till dina lokala appar

Om din organisation använder funktioner i Azure AD B2B-samarbete för att bjuda in gäst användare från partner organisationer till din Azure AD, kan du nu ge dessa B2B-användare åtkomst till lokala appar.

För appar som använder SAML-baserad autentisering kan du göra dessa appar tillgängliga för B2B-användare via Azure Portal med Azure AD-programproxy för autentisering.

För appar som använder integrerad Windows-autentisering (IWA) med Kerberos-begränsad delegering (KCD) använder du också Azure AD-proxy för autentisering. För att auktoriseringen ska fungera krävs dock ett användar objekt i den lokala Windows Server-Active Directory. Det finns två metoder som du kan använda för att skapa lokala användar objekt som representerar dina B2B-gäst användare.

- Du kan använda Microsoft Identity Manager (MIM) 2016 SP1 och MIM Management Agent för Microsoft Graph.
- Du kan använda ett PowerShell-skript. (Den här lösningen kräver inte MIM.)

Mer information om hur du implementerar dessa lösningar finns i [bevilja B2B-användare i Azure AD åtkomst till dina lokala program](hybrid-cloud-to-on-premises.md).

## <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources"></a>Bevilja lokalt hanterad partner konton åtkomst till moln resurser

Organisationer med lokala identitets system har traditionellt hanterade partner konton i sin lokala katalog innan Azure AD. Om du är en sådan organisation vill du se till att dina partners fortsätter att ha åtkomst när du flyttar dina appar och andra resurser till molnet. Helst vill du att dessa användare ska använda samma uppsättning autentiseringsuppgifter för att komma åt både molnet och lokala resurser. 

Nu erbjuder vi metoder där du kan använda Azure AD Connect för att synkronisera de lokala kontona till molnet som "gäst användare", där kontona fungerar precis som Azure AD B2B-användare.

För att skydda företagets data kan du kontrol lera åtkomsten till bara rätt resurser och konfigurera Auktoriseringsprinciper som behandlar dessa gäst användare på olika sätt från dina anställda.

Information om implementering finns i [bevilja lokalt hanterad partner konton åtkomst till moln resurser med Azure AD B2B-samarbete](hybrid-on-premises-to-cloud.md).
 
## <a name="next-steps"></a>Nästa steg

- [Bevilja B2B-användare i Azure AD åtkomst till dina lokala program](hybrid-cloud-to-on-premises.md)
- [Bevilja lokalt hanterade partnerkonton åtkomst till molnresurser med Azure AD B2B-samarbete](hybrid-on-premises-to-cloud.md)


