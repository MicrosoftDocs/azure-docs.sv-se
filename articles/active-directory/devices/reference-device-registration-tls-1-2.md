---
title: TLS 1,2 Enforcement-Azure Active Directory registrerings tjänst
description: Ta bort stöd för TLS 1,0 och 1,1 för Azure AD Device Registration Service
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 07/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2bb8c6c64e0a68f5176c4eb0c0177c5220394695
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89268765"
---
# <a name="enforce-tls-12-for-the-azure-ad-registration-service"></a>Framtvinga TLS 1,2 för registrerings tjänsten för Azure AD

Tjänsten Azure Active Directory (Azure AD) Device Registration används för att ansluta enheter till molnet med en enhets identitet. Azure AD Device Registration Service stöder för närvarande användning av Transport Layer Security (TLS) 1,2 för kommunikation med Azure. För att säkerställa säkerheten och den bästa krypteringen rekommenderar Microsoft att du inaktiverar TLS 1,0 och 1,1. Det här dokumentet innehåller information om hur du ser till att datorer som används för att slutföra registreringen och kommunicerar med Azure AD Device Registration Service använder TLS 1,2.

TLS-protokoll version 1,2 är ett kryptografi protokoll som är utformat för att tillhandahålla säker kommunikation. TLS-protokollet syftar främst till att tillhandahålla sekretess och data integritet. TLS har gått igenom många iterationer med version 1,2 som definieras i [RFC 5246 (extern länk)](https://tools.ietf.org/html/rfc5246).

Den aktuella analysen av anslutningar visar lite TLS 1,1-och 1,0-användning, men vi tillhandahåller den här informationen så att du kan uppdatera alla berörda klienter eller servrar efter behov innan stöd för TLS 1,1 och 1,0 upphör. Om du använder en lokal infrastruktur för Hybrid scenarier eller Active Directory Federation Services (AD FS) (AD FS) kontrollerar du att infrastrukturen har stöd för både inkommande och utgående anslutningar som använder TLS 1,2.

## <a name="update-windows-servers"></a>Uppdatera Windows-servrar

För Windows-servrar som använder Azure AD Device Registration Service eller agerar som proxyservrar använder du följande steg för att säkerställa att TLS 1,2 är aktiverat:

> [!IMPORTANT]
> När du har uppdaterat registret måste du starta om Windows Server för att ändringarna ska börja gälla.

### <a name="enable-tls-12"></a>Aktivera TLS 1.2

Se till att följande register strängar är konfigurerade som de visas:

- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ client
  - "DisabledByDefault" = DWORD: 00000000
  - "Enabled" = DWORD: 00000001
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ Server
  - "DisabledByDefault" = DWORD: 00000000
  - "Enabled" = DWORD: 00000001
- HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\. NETFramework\v4.0.30319
  - "SchUseStrongCrypto" = DWORD: 00000001

## <a name="update-non-windows-proxies"></a>Uppdatera icke-Windows-proxyservrar

Alla datorer som fungerar som proxyservrar mellan enheter och Azure AD Device Registration Service måste se till att TLS 1,2 är aktiverat. Följ din leverantörs vägledning för att garantera support.

## <a name="update-ad-fs-servers"></a>Uppdatera AD FS-servrar

Alla AD FS-servrar som används för att kommunicera med Azure AD Device Registration Service måste se till att TLS 1,2 är aktiverat. Mer information om hur du aktiverar/verifierar den här konfigurationen finns i [Hantera SSL/TLS-protokoll och chiffersviter för AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) .

## <a name="client-updates"></a>Klient uppdateringar

Eftersom alla kombinationer av klient-server-och webb läsar Server måste använda TLS 1,2 för att ansluta till Azure AD Device Registration Service kan du behöva uppdatera dessa enheter.

Följande klienter är kända för att inte stödja TLS 1,2. Uppdatera klienterna för att säkerställa oavbruten åtkomst.

- Android version 4,3 och tidigare
- Firefox version 5,0 och tidigare
- Internet Explorer-versioner 8-10 på Windows 7 och tidigare
- Internet Explorer 10 på Windows Phone 8,0
- Safari version 6.0.4 on OS X 10.8.4 och tidigare

## <a name="next-steps"></a>Nästa steg

[Översikt över TLS/SSL (Schannel SSP)](/windows-server/security/tls/tls-ssl-schannel-ssp-overview)