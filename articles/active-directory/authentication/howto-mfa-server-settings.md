---
title: Konfigurera MFA Server – Azure Active Directory
description: Lär dig hur du konfigurerar inställningar för Azure MFA server i Azure Portal
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb28f415b7d08b4c4430ed90a7ccbfe3a333416d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101689053"
---
# <a name="configure-mfa-server-settings"></a>Konfigurera inställningar för MFA-Server

Den här artikeln hjälper dig att hantera Azure MFA Server-inställningar i Azure Portal.

> [!IMPORTANT]
> Från och med den 1 juli 2019 kommer Microsoft inte längre att erbjuda MFA Server för nya distributioner. Nya kunder som vill kräva Multi-Factor Authentication från sina användare bör använda molnbaserad Azure AD-Multi-Factor Authentication. Befintliga kunder som har aktiverat MFA Server tidigare än 1 juli kommer att kunna ladda ned den senaste versionen, framtida uppdateringar och generera autentiseringsuppgifter för aktivering som vanligt.

Följande inställningar för MFA-servern är tillgängliga:

| Funktion | Beskrivning |
| ------- | ----------- |
| Serverinställningar | Hämta MFA Server och generera autentiseringsuppgifter för aktivering för att initiera din miljö |
| [Kringgå vid ett tillfälle](#one-time-bypass) | Tillåt en användare att autentisera utan att utföra Multi-Factor Authentication under en begränsad tid. |
| [Regler för cachelagring](#caching-rules) |  Cachelagring används främst när lokala system, till exempel VPN, skickar flera verifierings begär Anden medan den första begäran fortfarande pågår. Den här funktionen gör att efterföljande begär Anden kan lyckas automatiskt när användaren har slutfört den första verifieringen. |
| Server status | Se status för dina lokala MFA-servrar, inklusive version, status, IP och senaste kommunikations tid och datum. |

## <a name="one-time-bypass"></a>Kringgå vid ett tillfälle

Med funktionen för att kringgå en funktion kan en användare autentisera en enstaka gång utan att utföra Multi-Factor Authentication. Bypass är tillfälligt och upphör att gälla efter ett angivet antal sekunder. I situationer där mobilappen eller telefon inte tar emot ett meddelande eller telefonsamtal kan du tillåta en eng ång slö fördröjning så att användaren kan komma åt den önskade resursen.

Utför följande steg för att skapa en eng ång slö fördröjning:

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
1. Sök efter och välj **Azure Active Directory** och bläddra sedan till **säkerhets**  >  **MFA**  >  **eng ång slö** sen.
1. Välj **Lägg till**.
1. Om det behövs väljer du replikeringsgruppen för bypass.
1. Ange användar namnet som `username@domain.com` . Ange antalet sekunder som förbikopplingen ska vara sist och orsaken till kringgåendet.
1. Välj **Lägg till**. Tids gränsen börjar gälla omedelbart. Användaren måste logga in innan eng ång slö tiden upphör att gälla.

Du kan också visa en kringgå-rapport i taget i samma fönster.

## <a name="caching-rules"></a>Regler för cachelagring

Du kan ange en tids period för att tillåta autentiseringsförsök när en användare autentiseras med hjälp av funktionen för _cachelagring_ . Efterföljande autentiseringsförsök för användaren inom den angivna tids perioden slutförs automatiskt.

Cachelagring används främst när lokala system, till exempel VPN, skickar flera verifierings begär Anden medan den första begäran fortfarande pågår. Den här funktionen gör att efterföljande begär Anden kan lyckas automatiskt när användaren har slutfört den första verifieringen.

>[!NOTE]
> Cachelagring-funktionen är inte avsedd att användas för inloggningar till Azure Active Directory (Azure AD).

Utför följande steg för att konfigurera cachelagring:

1. Bläddra till **Azure Active Directory**  >  **säkerhets**  >  regler för **MFA**-  >  **cachelagring**.
1. Välj **Lägg till**.
1. Välj **cache-typ** i list rutan. Ange maximalt antal cache- **sekunder**.
1. Om det behövs väljer du en autentiseringstyp och anger ett program.
1. Välj **Lägg till**.

## <a name="next-steps"></a>Nästa steg

Ytterligare konfigurations alternativ för MFA-servern är tillgängliga från webb konsolen för själva MFA-servern. Du kan också [Konfigurera Azure MFA Server för hög tillgänglighet](howto-mfaserver-deploy-ha.md).
