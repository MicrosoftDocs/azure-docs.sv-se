---
title: Tjänst beroenden för villkorlig åtkomst – Azure Active Directory
description: Lär dig hur villkor används i Azure Active Directory villkorlig åtkomst för att utlösa en princip.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6592add5e33ba240c0f1d9fdbd23d82adfe5229
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91258616"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Vad är tjänst beroenden i Azure Active Directory villkorlig åtkomst? 

Med principer för villkorlig åtkomst kan du ange åtkomst krav för webbplatser och tjänster. Åtkomst kraven kan till exempel innefatta krav på Multi-Factor Authentication (MFA) eller [hanterade enheter](require-managed-devices.md). 

När du ansluter till en webbplats eller tjänst direkt är effekten av en relaterad princip vanligt vis lätt att utvärdera. Om du till exempel har en princip som kräver Multi-Factor Authentication (MFA) för SharePoint Online har kon figurer ATS, tillämpas MFA för varje inloggning till SharePoint-webbportalen. Det är dock inte alltid rakt framåt för att bedöma effekten av en princip eftersom det finns molnappar med beroenden till andra molnappar. Microsoft Teams kan till exempel ge åtkomst till resurser i SharePoint Online. När du har åtkomst till Microsoft Teams i vårt nuvarande scenario, omfattas du också av SharePoint MFA-principen. 

> [!TIP]
> Att använda [office 365](concept-conditional-access-cloud-apps.md#office-365) -appen är riktad mot alla Office-appar för att undvika problem med tjänst beroenden i Office-stacken.

## <a name="policy-enforcement"></a>Policyframtvingande 

Om ett tjänst beroende har kon figurer ATS kan principen tillämpas med hjälp av en tidig eller sen begränsad tvångs åtgärd. 

- **Tidig bindning av principer** innebär att en användare måste uppfylla den beroende tjänst principen innan de får åtkomst till den anropande appen. En användare måste till exempel uppfylla SharePoint-principer innan de kan logga in på MS Teams. 
- **Princip för sent kopplade principer** inträffar när användaren loggar in på den anropande appen. Tvångs åtgärd uppskjuts vid anrop av app-begäranden, en token för den underordnade tjänsten. Exempel är MS Teams som har åtkomst till Planner och Office.com åtkomst till SharePoint. 

Diagrammet nedan illustrerar MS Teams tjänst beroenden. Fyllda pilar visar tidig, tvingande, den streckade pilen för Planner anger att tvångs tvångs åtgärd ska verkställas. 

![MS Teams tjänst beroenden](./media/service-dependencies/01.png)

Som bästa praxis bör du ställa in gemensamma principer för relaterade appar och tjänster närhelst det är möjligt. Med en konsekvent säkerhets position får du den bästa användar upplevelsen. Om du till exempel anger en gemensam princip för Exchange Online, SharePoint Online, Microsoft Teams och Skype för företag, minskas avsevärt oväntade frågor som kan uppstå från olika principer som tillämpas på underordnade tjänster. 

Ett bra sätt att göra detta med program i Office-stacken är att använda [Office 365-appen](concept-conditional-access-cloud-apps.md#office-365) i stället för att rikta in enskilda program.

I tabellen nedan visas ytterligare tjänst beroenden där klient programmen måste uppfylla  

| Klientappar         | Underordnad tjänst                          | Lås |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Microsoft Azure hantering (portal och API) | Tidig bindning |
| Microsoft-klass rum | Exchange                                    | Tidig bindning |
|                     | SharePoint                                  | Tidig bindning |
| Microsoft Teams     | Exchange                                    | Tidig bindning |
|                     | MS Planner                                  | Sent knuten  |
|                     | Microsoft Stream                            | Sent knuten  |
|                     | SharePoint                                  | Tidig bindning |
|                     | Skype för företag – Online                   | Tidig bindning |
| Office-portalen       | Exchange                                    | Sent knuten  |
|                     | SharePoint                                  | Sent knuten  |
| Outlook-grupper      | Exchange                                    | Tidig bindning |
|                     | SharePoint                                  | Tidig bindning |
| PowerApps           | Microsoft Azure hantering (portal och API) | Tidig bindning |
|                     | Windows Azure Active Directory              | Tidig bindning |
| Project             | Dynamics CRM                                | Tidig bindning |
| Skype för företag  | Exchange                                    | Tidig bindning |
| Visual Studio       | Microsoft Azure hantering (portal och API) | Tidig bindning |
| Microsoft Forms     | Exchange                                    | Tidig bindning |
|                     | SharePoint                                  | Tidig bindning |
| Microsoft To-Do     | Exchange                                    | Tidig bindning |

## <a name="next-steps"></a>Nästa steg

Information om hur du implementerar villkorlig åtkomst i din miljö finns i [Planera din distribution av villkorlig åtkomst i Azure Active Directory](plan-conditional-access.md).
