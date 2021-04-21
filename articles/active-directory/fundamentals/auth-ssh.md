---
title: SSH-autentisering med Azure Active Directory
description: Arkitekturvägledning för att uppnå SSH-integrering med Azure Active Directory
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
ms.openlocfilehash: 315fe35a79ade39de9f541504fc2fe52754614de
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749356"
---
# <a name="ssh"></a>SSH  

Secure Shell (SSH) är ett nätverksprotokoll som tillhandahåller kryptering för att använda nätverkstjänster på ett säkert sätt över ett oskyddat nätverk. SSH tillhandahåller också en kommandorads-inloggning, kör fjärrkommandon och överför filer på ett säkert sätt. Det används ofta i UNIX-baserade system som Linux®. SSH ersätter Telnet-protokollet, som inte tillhandahåller kryptering i ett oskyddat nätverk. 

Azure Active Directory (Azure AD) tillhandahåller ett tillägg för virtuella datorer (VM) för Linux®baserade system som körs på Azure. 

## <a name="use-when"></a>Använd när 

* Arbeta med Linux®-baserade virtuella datorer som kräver fjärr inloggning

* Köra fjärrkommandon i Linux®-baserade system

* Överföra filer i ett oskyddat nätverk på ett säkert sätt

![diagram över Azure AD med SSH-protokoll](./media/authentication-patterns/ssh-auth.png)

SSH med Azure AD

## <a name="components-of-system"></a>Komponenter i systemet 

* **Användare:** Startar SSH-klienten för att konfigurera en anslutning till de virtuella Linux®datorerna och tillhandahåller autentiseringsuppgifter för autentisering.

* **Webbläsare:** Den komponent som användaren interagerar med. Den kommunicerar med identitetsprovidern (Azure AD) för att autentisera och auktorisera användaren på ett säkert sätt.

* **SSH-klient:** Driver anslutningsprocessen.

* **Azure AD:** Autentiserar användarens identitet med hjälp av enhetsflödet och utfärdar token till de virtuella Linux-datorerna.

* **Virtuell Linux-dator:** Accepterar token och ger en lyckad anslutning.

## <a name="implement-ssh-with-azure-ad"></a>Implementera SSH med Azure AD 

* [Logga in på en virtuell Linux®-dator med Azure Active Directory autentiseringsuppgifter – Azure Virtual Machines ](../../virtual-machines/linux/login-using-aad.md) 

* [OAuth 2.0-enhetskodflöde – Microsoft Identity Platform ](../develop/v2-oauth2-device-code.md)

* [Integrera med Azure Active Directory (akamai.com)](https://learn.akamai.com/en-us/webhelp/enterprise-application-access/enterprise-application-access/GUID-6B16172C-86CC-48E8-B30D-8E678BF3325F.html)

