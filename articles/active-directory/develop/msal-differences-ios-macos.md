---
title: MSAL för iOS & macOS-skillnader | Azure
titleSuffix: Microsoft identity platform
description: Beskriver användnings skillnaderna i Microsoft Authentication Library (MSAL) mellan iOS och macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 59e4111b6dda386777e820c9be16ace9ff01135c
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2021
ms.locfileid: "98064923"
---
# <a name="microsoft-authentication-library-for-ios-and-macos-differences"></a>Skillnader mellan Microsoft Authentication Library för iOS och macOS

I den här artikeln förklaras skillnaderna mellan funktionerna i Microsoft Authentication Library (MSAL) för iOS och macOS.

> [!NOTE]
> På Mac-datorn stöder MSAL bara macOS-appar.

## <a name="general-differences"></a>Allmänna skillnader

MSAL för macOS är en delmängd av de funktioner som är tillgängliga för iOS.

MSAL för macOS stöder inte:

- olika webb läsar typer, till exempel `ASWebAuthenticationSession` , `SFAuthenticationSession` , `SFSafariViewController` .
- Brokered Authentication via Microsoft Authenticator app stöds inte för macOS.

Delning av nyckel ringar mellan appar från samma utgivare är mer begränsad i macOS 10,14 och tidigare. Använd [åtkomst kontrol listor](https://developer.apple.com/documentation/security/keychain_services/access_control_lists?language=objc) för att ange sökvägar till de appar som ska dela nyckel ringen. Användaren kan se ytterligare prompter för nyckel ringar.

På macOS 10.15 +, är MSAL-beteendet detsamma mellan iOS och macOS. MSAL använder [nyckel rings åtkomst grupper](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) för delning av nyckel ringar. 

### <a name="conditional-access-authentication-differences"></a>Skillnader i autentisering av villkorlig åtkomst

I scenarier med villkorlig åtkomst visas färre användar meddelanden när du använder MSAL för iOS. Detta beror på att iOS använder Service Broker-appen (Microsoft Authenticator), vilket eliminerar behovet av att meddela användaren i vissa fall.

### <a name="project-setup-differences"></a>Skillnader mellan projekt konfiguration

**macOS**

- När du konfigurerar projektet på macOS måste du kontrol lera att programmet är signerat med ett giltigt utvecklings-eller produktions certifikat. MSAL fungerar fortfarande i osignerat läge, men det fungerar annorlunda med avseende på beständighet i cacheminnet. Appen ska bara köras osignerad för fel söknings syfte. Om du distribuerar appen osignerad kommer den att:
1. På 10,14 och tidigare uppmanas användaren att ange ett lösen ord för nyckel ringen varje gång appen startas om.
2. På 10.15 + kommer MSAL att uppmana användaren att ange autentiseringsuppgifter för varje hämtning av token. 

- macOS-appar behöver inte implementera AppDelegate-anropet.

**iOS**

- Det finns ytterligare steg för att konfigurera ditt projekt så att det stöder Authentication Broker-flödet. Stegen visas i självstudien.
- iOS-projekt behöver registrera anpassade scheman i filen info. plist. Detta krävs inte på macOS.
