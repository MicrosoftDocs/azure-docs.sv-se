---
title: Felsök TLS/SSL-problem (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Läs om vad du kan göra om olika problem med hjälp av TLS/SSL-certifikat med MSAL. Mål-C-bibliotek.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 1507231c3ab395319d5ce95ec06dbb592c324aa6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "80881085"
---
# <a name="how-to-troubleshoot-msal-for-ios-and-macos-tlsssl-issues"></a>Gör så här: Felsöka MSAL för iOS-och macOS TLS/SSL-problem

Den här artikeln innehåller information som hjälper dig att felsöka problem som kan uppstå i när du använder [Microsoft Authentication Library (MSAL) för iOS och MacOS](reference-v2-libraries.md)

## <a name="network-issues"></a>Nätverks problem

**Fel-1200**: "ett SSL-fel har uppstått och det går inte att upprätta en säker anslutning till servern."

Det här felet innebär att anslutningen inte är säker. Det inträffar när ett certifikat är ogiltigt. Mer information, inklusive vilken server som inte har TLS-kontrollen, finns `NSURLErrorFailingURLErrorKey` i `userInfo` ord listan för felobjektet.

Det här felet är från Apples nätverks bibliotek. En fullständig lista över NSURL-felkoder finns i NSURLError. h i macOS-och iOS-SDK: er. Mer information om det här felet finns i [URL: er läser in system fel koder](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc).

## <a name="certificate-issues"></a>Certifikat problem

Om URL: en som tillhandahåller ett ogiltigt certifikat ansluter till den server som du tänker använda som en del av autentiseringsschemat, är det en välsamma start att diagnostisera problemet att testa URL: en med en SSL-validerings tjänst, till exempel [SSL-Server test](https://www.ssllabs.com/ssltest/analyze.html). Den testar servern mot en mängd olika scenarier och webbläsare och söker efter många kända sårbarheter.

Som standard tillämpar Apples nya [ATS-funktionen (app Transport Security)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) mer strikta säkerhets principer för appar som använder TLS/SSL-certifikat. Vissa operativ system och webbläsare har börjat tvinga fram några av dessa principer som standard. Av säkerhets skäl rekommenderar vi att du inte inaktiverar ATS.

Certifikat som använder SHA-1-hashar har kända sårbarheter. De flesta moderna webbläsare tillåter inte certifikat med SHA-1-hashar.

## <a name="captive-portals"></a>Intern Portal

En intern Portal presenterar en webb sida för en användare när de först ansluter till ett Wi-Fi nätverk och ännu inte beviljats åtkomst till det nätverket. Den fångar upp sin Internet trafik tills användaren uppfyller kraven för portalen. Nätverks fel eftersom användaren inte kan ansluta till nätverks resurser förväntas tills användaren ansluter via portalen.

## <a name="next-steps"></a>Nästa steg

Lär dig om [intern Portal](https://en.wikipedia.org/wiki/Captive_portal) och Apples nya [ATS-funktion (app Transport Security)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) .
