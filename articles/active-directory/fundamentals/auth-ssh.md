---
title: SSH-autentisering med Azure Active Directory
description: Arkitektur vägledning för att uppnå SSH-integrering med Azure Active Directory
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/19/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b77ab0832fa19149c270d6ba5a6641069548cbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96172728"
---
# <a name="ssh"></a>SSH  

Secure Shell (SSH) är ett nätverks protokoll som tillhandahåller kryptering för drift nätverks tjänster på ett säkert sätt över ett oskyddat nätverk. SSH tillhandahåller också kommando rads inloggning, kör fjärrkommandon och överför filer på ett säkert sätt. Den används ofta i UNIX-baserade system som Linux®. SSH ersätter Telnet-protokollet, som inte tillhandahåller kryptering i ett oskyddat nätverk. 

Azure Active Directory (Azure AD) tillhandahåller ett tillägg för virtuella datorer (VM) för Linux®-baserade system som körs på Azure. 

## <a name="use-when"></a>Använd när 

* Arbeta med Linux®-baserade virtuella datorer som kräver fjärran sluten inloggning

* Köra fjärrkommandon i Linux®-baserade system

* Överföra filer säkert i ett oskyddat nätverk

![diagram över Azure AD med SSH-protokoll](./media/authentication-patterns/ssh-auth.png)

SSH med Azure AD

## <a name="components-of-system"></a>System komponenter 

* **Användare**: startar SSH-klienten för att upprätta en anslutning till Linux® virtuella datorer och ger autentiseringsuppgifter för autentisering.

* **Webbläsare**: komponenten som användaren interagerar med. Den kommunicerar med identitets leverantören (Azure AD) för att på ett säkert sätt autentisera och auktorisera användaren.

* **SSH-klient**: styr installationen av anslutnings processen.

* **Azure AD**: autentiserar användarens identitet med hjälp av enhets flödet och utfärdar token till de virtuella Linux-datorerna.

* **Virtuell Linux-dator**: accepterar token och ger lyckad anslutning.

## <a name="implement-ssh-with-azure-ad"></a>Implementera SSH med Azure AD 

* [Logga in på en Linux® virtuell dator med Azure Active Directory autentiseringsuppgifter – Azure Virtual Machines ](../../virtual-machines/linux/login-using-aad.md) 

* [OAuth 2,0 enhets kod flöde-Microsoft Identity Platform ](../develop/v2-oauth2-device-code.md)

* [Integrera med Azure Active Directory (akamai.com)](https://learn.akamai.com/webhelp/enterprise-application-access/enterprise-application-access/GUID-6B16172C-86CC-48E8-B30D-8E678BF3325F.html)

