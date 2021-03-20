---
title: Kerberos-begränsad delegering med Azure Active Directory
description: Arkitektur vägledning för att uppnå Kerberos-begränsad delegering med Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1c7aa4d2300a6dee44da067b122fc7af97f7aa9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96172864"
---
# <a name="windows-authentication---kerberos-constrained-delegation-with-azure-active-directory"></a>Windows-autentisering – Kerberos-begränsad delegering med Azure Active Directory

Kerberos-begränsad delegering (KCD) ger begränsad delegering mellan resurser och baseras på tjänst princip namn. Den kräver domän administratörer för att skapa delegeringarna och är begränsad till en enda domän. Resursbaserade KCD används ofta som ett sätt att tillhandahålla Kerberos-autentisering för ett webb program som har användare i flera domäner inom en Active Directory skog.

Azure Active Directory-programproxy kan tillhandahålla enkel inloggning (SSO) och fjärråtkomst till KCD-baserade program som kräver en Kerberos-biljett för åtkomst och Kerberos-begränsad delegering (KCD).

Du aktiverar SSO till dina lokala KCD-program som använder integrerad Windows-autentisering (IWA) genom att ge Application Proxy-anslutningar behörighet att personifiera användare i Active Directory. Application Proxy Connector använder den här behörigheten för att skicka och ta emot token för användarens räkning.

## <a name="use-when"></a>Använd när

Du måste tillhandahålla fjärråtkomst, skydda med förautentisering och tillhandahålla SSO till lokala IWA-program.

![Diagram över arkitektur](./media/authentication-patterns/kcd-auth.png)

## <a name="components-of-system"></a>System komponenter

* **Användare**: åtkomst till äldre program som hanteras av Application Proxy.

* **Webbläsare**: komponenten som användaren interagerar med för att få åtkomst till den externa URL: en för programmet.

* **Azure AD**: autentiserar användaren. 

* **Application Proxy Service**: fungerar som omvänd proxy för att skicka begäran från användaren till det lokala programmet. Den är placerad i Azure AD. Programproxyn kan också tillämpa principer för villkorlig åtkomst.

* **Application Proxy Connector**: installeras lokalt på Windows-servrar för att tillhandahålla anslutning till programmet. Returnerar svaret till Azure AD. Utför KCD-förhandling med Active Directory som imiterar användaren att hämta en Kerberos-token till programmet.

* **Active Directory**: skickar Kerberos-token för programmet till Application Proxy-anslutningsprogrammet.

* **Äldre program**: program som tar emot användar förfrågningar från programproxyn. De äldre programmen returnerar svaret till Application Proxy Connector.

## <a name="implement-windows-authentication-kcd-with-azure-ad"></a>Implementera Windows-autentisering (KCD) med Azure AD

* [Kerberos-begränsad delegering för enkel inloggning till appar med Programproxy](../manage-apps/application-proxy-configure-single-sign-on-with-kcd.md) 

* [Lägg till ett lokalt program för fjärråtkomst via Application Proxy i Azure Active Directory](../manage-apps/application-proxy-add-on-premises-application.md)

