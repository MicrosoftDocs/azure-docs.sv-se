---
title: Användar anspråk mappning för B2B-samarbete – Azure Active Directory
description: Anpassa användar anspråk som utfärdas i SAML-token för Azure Active Directory (Azure AD) B2B-användare.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/06/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3508865d9f89501cf70e09087c6a609beb6662b2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87910261"
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Användar anspråk mappning för B2B-samarbete i Azure Active Directory

Azure Active Directory (Azure AD) stöder anpassning av anspråk som utfärdas i SAML-token för B2B-samarbets användare. När en användare autentiserar sig till programmet utfärdar Azure AD en SAML-token till den app som innehåller information (eller anspråk) om den användare som unikt identifierar dem. Som standard innehåller detta användarens användar namn, e-postadress, förnamn och efter namn.

I [Azure Portal](https://portal.azure.com)kan du Visa eller redigera de anspråk som skickas i SAML-token till programmet. För att få åtkomst till inställningarna väljer du **Azure Active Directory**  >  **företags program** > programmet som är konfigurerat för enkel inloggning > **enkel inloggning**. Se inställningarna för SAML-token i avsnittet **användarattribut** .

![Visar attributen för SAML-token i användar gränssnittet](media/claims-mapping/view-claims-in-saml-token.png)

Det finns två möjliga orsaker till varför du kan behöva redigera anspråk som utfärdats i SAML-token:

1. Programmet kräver en annan uppsättning av anspråk-URI: er eller anspråks värden.

2. Programmet kräver att NameIdentifier-anspråk är något annat än det User Principal Name (UPN) som lagras i Azure AD.

Information om hur du lägger till och redigerar anspråk finns i [Anpassa anspråk som utfärdats i SAML-token för företags program i Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

För B2B-samarbets användare, förhindras mappning av NameID och UPN mellan klient av säkerhets skäl.

## <a name="next-steps"></a>Nästa steg

- Information om användar egenskaper för B2B-samarbete finns i [Egenskaper för en Azure Active Directory B2B-samarbets användare](user-properties.md).
- Information om användartoken för B2B-samarbets användare finns i [förstå användar-tokens i Azure AD B2B-samarbete](user-token.md).

