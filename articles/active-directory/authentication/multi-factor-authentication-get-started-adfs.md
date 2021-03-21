---
title: Tvåstegsverifiering Azure AD MFA och ADFS-Azure Active Directory
description: Det här är Azure AD Multi-Factor Authentication-sidan som beskriver hur du kommer igång med Azure AD MFA och AD FS.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0023d40fdc26fa1c42a67ce78a9259643098abb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96741413"
---
# <a name="getting-started-with-azure-ad-multi-factor-authentication-and-active-directory-federation-services"></a>Komma igång med Azure AD Multi-Factor Authentication och Active Directory Federation Services (AD FS)

<center>

![Komma igång med Azure AD MFA och ADFS](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Om din organisation har federerad din lokala Active Directory med Azure Active Directory med AD FS, finns det två alternativ för att använda Azure AD Multi-Factor Authentication.

* Skydda moln resurser med Azure AD Multi-Factor Authentication eller Active Directory Federation Services (AD FS)
* Skydda molnet och lokala resurser med Azure Multi-Factor Authentication Server

I följande tabell sammanfattas verifierings upplevelsen mellan att skydda resurser med Azure AD Multi-Factor Authentication och AD FS

| Verifieringsupplevelse – webbläsarbaserade appar | Verifieringsupplevelse – appar som inte är webbläsarbaserade |
|:--- |:--- |
| Skydda Azure AD-resurser med hjälp av Azure AD Multi-Factor Authentication |<li>Det första verifieringssteget utförs lokalt med hjälp av AD FS.</li> <li>Det andra steget är en telefonbaserad metod som utförs med hjälp av molnautentisering.</li> |
| Skydda Azure AD-resurser med hjälp av Active Directory Federation Services |<li>Det första verifieringssteget utförs lokalt med hjälp av AD FS.</li><li>Det andra steget utförs lokalt genom att anspråket tillämpas.</li> |

Varningar med applösenord för federerade användare:

* Applösenord verifieras med molnautentisering och kringgår därför federation. Federation används endast aktivt när applösenorden konfigureras.
* Inställningar för lokal klientåtkomstkontroll respekteras inte av applösenord.
* Du kan inte använda lokal autentiseringsloggning med applösenord.
* Inaktiveringen eller borttagningen av konton kan ta upp till tre timmar för katalogsynkronisering, vilket försenar inaktiveringen eller borttagningen av applösenord i molnidentiteten.

Information om hur du konfigurerar Azure AD Multi-Factor Authentication eller Azure-Multi-Factor Authentication-server med AD FS finns i följande artiklar:

* [Skydda moln resurser med Azure AD Multi-Factor Authentication och AD FS](howto-mfa-adfs.md)
* [Skydda molnresurser och lokala resurser med Azure Multi-Factor Authentication Server med Windows Server 2012 R2 AD FS](howto-mfaserver-adfs-2012.md)
* [Skydda molnet och lokala resurser med Azure Multi-Factor Authentication Server med AD FS 2.0](howto-mfaserver-adfs-2.md)
