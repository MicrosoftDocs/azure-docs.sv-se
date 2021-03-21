---
title: Hybrid identitet krävs portar och protokoll – Azure | Microsoft Docs
description: Den här sidan är en teknisk referens sida för portar som måste vara öppna för Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: de97b225-ae06-4afc-b2ef-a72a3643255b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 681d92ed162feff0510067556d7eeaa47b12d988
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102520038"
---
# <a name="hybrid-identity-required-ports-and-protocols"></a>Portar och protokoll som krävs för hybrididentitet
Följande dokument är en teknisk referens för de portar och protokoll som krävs för att implementera en hybrid identitets lösning. Använd följande bild och referera till motsvarande tabell.

![Vad är Azure AD Connect?](./media/reference-connect-ports/required3.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Tabell 1 – Azure AD Connect och lokal AD
I den här tabellen beskrivs de portar och protokoll som krävs för kommunikation mellan Azure AD Connect Server och lokal AD.

| Protokoll | Portar | Beskrivning |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |DNS-sökningar i mål skogen. |
| Kerberos |88 (TCP/UDP) |Kerberos-autentisering till AD-skogen. |
| MS-RPC |135 (TCP) |Används under den inledande konfigurationen av Azure AD Connects guiden när den binder till AD-skogen och även under Lösenordssynkronisering. |
| LDAP |389 (TCP/UDP) |Används för data import från AD. Data krypteras med Kerberos-signera & sigill. |
| SMB | 445 (TCP) |Används av sömlös SSO för att skapa ett dator konto i AD-skogen. |
| LDAP/SSL |636 (TCP/UDP) |Används för data import från AD. Data överföringen är signerad och krypterad. Används endast om du använder TLS. |
| RPC |49152-65535 (slumpmässig hög RPC-port) (TCP) |Används under den inledande konfigurationen av Azure AD Connect när den binder till AD-skogar och under Lösenordssynkronisering. Om den dynamiska porten har ändrats måste du öppna den porten. Mer information finns i [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017)och [KB224196](https://support.microsoft.com/kb/224196) . |
|WinRM  | 5985 (TCP) |Används endast om du installerar AD FS med gMSA av Azure AD Connect-guiden|
|AD DS-webbtjänster | 9389 (TCP) |Används endast om du installerar AD FS med gMSA av Azure AD Connect-guiden |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tabell 2 – Azure AD Connect och Azure AD
I den här tabellen beskrivs de portar och protokoll som krävs för kommunikation mellan Azure AD Connect-servern och Azure AD.

| Protokoll | Portar | Beskrivning |
| --- | --- | --- |
| HTTP |80 (TCP) |Används för att hämta CRL: er (listor över återkallade certifikat) för att verifiera TLS/SSL-certifikat. |
| HTTPS |443 (TCP) |Används för att synkronisera med Azure AD. |

En lista över URL: er och IP-adresser som du måste öppna i brand väggen finns i [Office 365-URL: er och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) och [fel sökning av Azure AD Connect anslutning](tshoot-connect-connectivity.md#troubleshoot-connectivity-issues-in-the-installation-wizard).

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>Tabell 3 – Azure AD Connect och AD FS Federations Servrar/WAP
I den här tabellen beskrivs de portar och protokoll som krävs för kommunikation mellan Azure AD Connect-servern och AD FS Federation/WAP-servrar.  

| Protokoll | Portar | Beskrivning |
| --- | --- | --- |
| HTTP |80 (TCP) |Används för att hämta CRL: er (listor över återkallade certifikat) för att verifiera TLS/SSL-certifikat. |
| HTTPS |443 (TCP) |Används för att synkronisera med Azure AD. |
| WinRM |5985 |WinRM-lyssnare |

## <a name="table-4---wap-and-federation-servers"></a>Tabell 4 – WAP-och Federations servrar
I den här tabellen beskrivs de portar och protokoll som krävs för kommunikation mellan Federations servrar och WAP-servrar.

| Protokoll | Portar | Beskrivning |
| --- | --- | --- |
| HTTPS |443 (TCP) |Används för autentisering. |

## <a name="table-5---wap-and-users"></a>Tabell 5 – WAP och användare
I den här tabellen beskrivs de portar och protokoll som krävs för kommunikation mellan användare och WAP-servrar.

| Protokoll | Portar | Beskrivning |
| --- | --- | --- |
| HTTPS |443 (TCP) |Används för enhetsautentisering. |
| TCP |49443 (TCP) |Används för certifikatautentisering. |

## <a name="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso"></a>Tabell 6a & 6b-direktautentisering med enkel inloggning (SSO) och hash-synkronisering av lösen ord med enkel inloggning (SSO)
I följande tabeller beskrivs de portar och protokoll som krävs för kommunikation mellan Azure AD Connect och Azure AD.

### <a name="table-6a---pass-through-authentication-with-sso"></a>Tabell 6a – direktautentisering med SSO
|Protokoll|Portnummer|Beskrivning
| --- | --- | ---
|HTTP|80|Aktivera utgående HTTP-trafik för säkerhets validering, till exempel SSL. Krävs också för att funktionen för automatisk uppdatering av Connector ska fungera korrekt.
|HTTPS|443| Aktivera utgående HTTPS-trafik för åtgärder som att aktivera och inaktivera funktionen, registrera anslutningar, hämta anslutnings uppdateringar och hantera alla användares inloggnings begär Anden.

Dessutom måste Azure AD Connect kunna göra direkta IP-anslutningar till [Azure Data Center IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="table-6b---password-hash-sync-with-sso"></a>Tabell 6b – hash-synkronisering av lösen ord med SSO

|Protokoll|Portnummer|Beskrivning
| --- | --- | ---
|HTTPS|443| Aktivera SSO-registrering (krävs endast för SSO-registrerings processen).

Dessutom måste Azure AD Connect kunna göra direkta IP-anslutningar till [Azure Data Center IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653). Detta krävs bara för SSO-registreringen.

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabell 7a & 7b-Azure AD Connect Health Agent för (AD FS/synkronisering) och Azure AD
I följande tabeller beskrivs de slut punkter, portar och protokoll som krävs för kommunikation mellan Azure AD Connect Health agenter och Azure AD

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabell 7a – portar och protokoll för Azure AD Connect Health Agent för (AD FS/synkronisering) och Azure AD
I den här tabellen beskrivs följande utgående portar och protokoll som krävs för kommunikation mellan Azure AD Connect Health-agenter och Azure AD.  

| Protokoll | Portar | Beskrivning |
| --- | --- | --- |
| HTTPS |443 (TCP) |Utgående |
| Azure Service Bus |5671 (TCP) |Utgående |

Azure Service Bus port 5671 krävs inte längre för den senaste versionen av agenten. Den senaste versionen av Azure AD Connect Health Agent version krävde bara port 443.

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7b-slut punkter för Azure AD Connect Health Agent för (AD FS/synkronisering) och Azure AD
En lista över slut punkter finns i [avsnittet krav för Azure AD Connect Health agenten](how-to-connect-health-agent-install.md#requirements).

