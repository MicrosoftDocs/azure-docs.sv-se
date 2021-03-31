---
title: Federera flera Azure AD med enkla AD FS – Azure
description: I det här dokumentet lär du dig hur du federerar flera Azure AD-instanser med en enda AD FS-instans.
keywords: federate, ADFS, AD FS, multiple tenants, single AD FS, one ADFS, multi-tenant federation, multi-forest adfs, aad connect, federation, cross-tenant federation
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/17/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 956428b6f197912e2ab7c3a94133ed9d59f37749
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89279932"
---
# <a name="federate-multiple-instances-of-azure-ad-with-single-instance-of-ad-fs"></a>Federera flera instanser av Azure AD med en enda instans av AD FS

En enda AD FS-servergrupp med hög tillgänglighet kan federera flera skogar om de har ett dubbelriktat förtroende. Dessa skogar kan men måste inte vara associerade med samma Azure Active Directory. Den här artikeln beskriver hur du konfigurerar federation mellan en enskild AD FS-distribution och mer än en skog som synkroniserar med en annan Azure AD.

![Federation med flera innehavare med en enda AD FS](./media/how-to-connect-fed-single-adfs-multitenant-federation/concept.png)
 
> [!NOTE]
> Tillbakaskrivning av enheter och automatisk enhetskoppling stöds inte i det här scenariot.

> [!NOTE]
> Azure AD Connect kan inte användas för att konfigurera federation i det här scenariot eftersom Azure AD Connect kan konfigurera federation för domäner i en enda Azure AD.

## <a name="steps-for-federating-ad-fs-with-multiple-azure-ad"></a>Steg för att federera AD FS med flera Azure AD-instanser

Tänk på att en contoso.com-domän i Azure Active Directory contoso.onmicrosoft.com redan är federerad med den lokala AD FS-instansen som är installerad i den lokala Active Directory-miljön contoso.com. Fabrikam.com är en domän i Azure Active Directory fabrikam.onmicrosoft.com.

## <a name="step-1-establish-a-two-way-trust"></a>Steg 1: Upprätta ett dubbelriktat förtroende
 
För att AD FS i contoso.com ska kunna autentisera användare i fabrikam.com krävs ett dubbelriktat förtroende mellan contoso.com och fabrikam.com. Följ riktlinjerna i den här [artikeln](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc816590(v=ws.10)) för att skapa det dubbelriktade förtroendet.
 
## <a name="step-2-modify-contosocom-federation-settings"></a>Steg 2: Ändra federationsinställningarna för contoso.com 
 
Standardutfärdaren för en enskild domän som federeras till AD FS är ”http\://ADFSServiceFQDN/adfs/services/trust”, till exempel `http://fs.contoso.com/adfs/services/trust`. Azure Active Directory kräver en unik utfärdare för varje federerad domän. Eftersom samma AD FS ska federera två domäner måste utfärdarens värde ändras så att det är unikt för varje domän som AD FS federerar med Azure Active Directory. 
 
Öppna Azure AD PowerShell på AD FS-servern (se till att MSOnline-modulen är installerad) och utför följande steg:
 
Anslut till den Azure Active Directory som innehåller domänen contoso.com Connect-MsolService Uppdatera federationsinställningarna för contoso.com Update-MsolFederatedDomain -DomainName contoso.com –SupportMultipleDomain
 
Utfärdaren i inställningen för domänfederation ändras till ”http\://contoso.com/adfs/services/trust” och en anspråksregel för utfärdande läggs till så att den förlitande Azure AD-parten kan utfärda rätt issuerId-värde baserat på UPN-suffixet.
 
## <a name="step-3-federate-fabrikamcom-with-ad-fs"></a>Steg 3: Federera fabrikam.com med AD FS
 
Utför följande steg i Azure AD PowerShell-sessionen: Anslut till Azure Active Directory som innehåller domänen fabrikam.com

```powershell
Connect-MsolService
```
Konvertera den hanterade domänen fabrikam.com till federerad:

```powershell
Convert-MsolDomainToFederated -DomainName fabrikam.com -Verbose -SupportMultipleDomain
```
 
Åtgärden ovan federerar domänen fabrikam.com med samma AD FS. Du kan kontrollera domäninställningarna genom att använda Get-MsolDomainFederationSettings för båda domänerna.

## <a name="next-steps"></a>Nästa steg
[Ansluta Active Directory med Azure Active Directory](whatis-hybrid-identity.md)