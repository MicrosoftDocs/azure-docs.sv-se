---
title: Skydda externt samarbete i Azure Active Directory
description: En guide för arkitekter och IT-administratörer om att skydda extern åtkomst till interna resurser
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc8ec364380fc5cb9f6e33a29cd8dac96ea1fb69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98219835"
---
# <a name="securing-external-collaboration-in-azure-active-directory-and-microsoft-365"></a>Skydda externt samarbete i Azure Active Directory och Microsoft 365

Ett säkert samarbete med externa partners säkerställer att de rätt externa partnerna har lämplig åtkomst till interna resurser under rätt tid. Genom en holistisk styrnings metod kan du minska säkerhets riskerna, uppfylla efterlevnads målen och se till att du vet vem som har åtkomst.

Ett oskyddat samarbete leder till bristande klarhet vid åtkomst till ägarskapet och risken för att känsliga resurser exponeras. Att flytta till säkert och reglerat samarbete kan säkerställa att det finns tydliga rader av ägarskap och ansvar för externa användares åtkomst. Du måste bland annat:

* Hantera externa organisationer och användare inom dem som har åtkomst till resurser.

* Se till att åtkomsten är lämplig, granskad och tids gräns, om det behövs.

* Ge företags ägare möjlighet att hantera samarbete inom IT-skapade vakt-spår.

Om du måste uppfylla ramverken för efterlevnad kan du använda det styrda samarbetet för att intyga att åtkomsten är korrekt.

Microsoft erbjuder omfattande utbud av verktyg för säker extern åtkomst.  Azure Active Directory (Azure AD) B2B-samarbete är i mitten av alla externa samarbets planer. Azure AD B2B kan integreras med andra verktyg i Azure AD, och verktyg i Microsoft 365 Services, för att skydda och hantera din externa åtkomst.

Den här dokument uppsättningen är utformad för att du ska kunna flytta från ad hoc eller lösa externt samarbete till ett säkrare tillstånd. 

## <a name="next-steps"></a>Nästa steg

Se följande artiklar om hur du skyddar extern åtkomst till resurser. Vi rekommenderar att du vidtar åtgärder i den angivna ordningen.


1. [Bestäm din säkerhets position för extern åtkomst](1-secure-access-posture.md)

2. [Identifiera ditt nuvarande tillstånd](2-secure-access-current-state.md)

3. [Skapa en styrnings plan](3-secure-access-plan.md)

4. [Använda grupper för säkerhet](4-secure-access-groups.md)

5. [Över gång till Azure AD B2B](5-secure-access-b2b.md)

6. [Säker åtkomst med hantering av rättigheter](6-secure-access-entitlement-managment.md)

7. [Säker åtkomst med principer för villkorlig åtkomst](7-secure-access-conditional-access.md)

8. [Säker åtkomst med känslighets etiketter](8-secure-access-sensitivity-labels.md)

9. [Säker åtkomst till Microsoft Teams, OneDrive och SharePoint](9-secure-access-teams-sharepoint.md)
