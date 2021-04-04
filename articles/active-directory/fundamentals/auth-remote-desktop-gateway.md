---
title: Remote Desktop Gateway-tjänster med Azure Active Directory
description: Arkitektur vägledning för att uppnå tjänster för fjärrskrivbordsgateway med Azure Active Directory.
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
ms.openlocfilehash: 4baaf2de6fbe4a56f64d449644b8594217dc432c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96172745"
---
# <a name="remote-desktop-gateway-services"></a>Tjänster för fjärrskrivbordsgateway

En vanlig Fjärrskrivbordstjänster-distribution (RDS) inkluderar olika [fjärr skrivbords roll tjänster](/windows-server/remote/remote-desktop-services/Desktop-hosting-logical-architecture) som körs på Windows Server. RDS-distributionen med Azure Active Directory (Azure AD) Application Proxy har en permanent utgående anslutning från servern som kör anslutnings tjänsten. Andra distributioner lämnar öppna inkommande anslutningar via en belastningsutjämnare. Med det här verifierings mönstret kan du erbjuda fler typer av program genom att publicera lokala program via Fjärrskrivbordstjänster. Den minskar också angrepps ytan för distributionen med hjälp av Azure AD-programproxy.

## <a name="use-when"></a>Använd när

Du måste ge fjärråtkomst och skydda din Fjärrskrivbordstjänster-distribution med förautentisering.

![arkitektur diagram](./media/authentication-patterns/rdp-auth.png)

## <a name="components-of-system"></a>System komponenter

* **Användare**: åtkomst till RDS som hanteras av Application Proxy.

* **Webbläsare**: komponenten som användaren interagerar med för att få åtkomst till den externa URL: en för programmet.

* **Azure AD**: autentiserar användaren. 

* **Application Proxy Service**: fungerar som omvänd proxy för att vidarebefordra begäran från användaren till fjärr skrivbords tjänster. Programproxyn kan också tillämpa principer för villkorlig åtkomst. 

* **Fjärrskrivbordstjänster**: fungerar som en plattform för enskilda virtualiserade program, vilket ger säker åtkomst till mobila och fjärr skrivbord och ger slutanvändarna möjlighet att köra sina program och skriv bord från molnet. 

## <a name="implement-remote-desktop-gateway-services-with-azure-ad"></a>Implementera gateway-tjänster för fjärr skrivbord med Azure AD

* [Publicera fjärr skrivbord med Azure AD-programproxy](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md) 

* [Lägg till ett lokalt program för fjärråtkomst via Application Proxy i Azure AD](../manage-apps/application-proxy-add-on-premises-application.md)

