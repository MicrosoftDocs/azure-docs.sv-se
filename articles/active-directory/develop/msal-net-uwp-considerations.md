---
title: UWP-överväganden (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om att tänka på när du använder Universell Windows-plattform (UWP) med Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2021
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 8a8aab447007eb574a7a4bc532d8177bd0d8b345
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122484"
---
# <a name="considerations-for-using-universal-windows-platform-with-msalnet"></a>Att tänka på när du använder Universell Windows-plattform med MSAL.NET
Utvecklare av program som använder Universell Windows-plattform (UWP) med MSAL.NET bör tänka på de begrepp som den här artikeln visar.

## <a name="the-usecorporatenetwork-property"></a>Egenskapen UseCorporateNetwork
På Windows Runtime-plattformen (WinRT) `PublicClientApplication` har egenskapen Boolean `UseCorporateNetwork` . Den här egenskapen gör att Windows 10-program och UWP-program kan dra nytta av integrerad Windows-autentisering (IWA) om användaren är inloggad på ett konto som har en federerad Azure Active Directory-klient (Azure AD). Användare som är inloggade på operativ systemet kan också använda enkel inloggning (SSO). När du anger `UseCorporateNetwork` egenskapen använder MSAL.net en Web Authentication Broker (WAB).

> [!IMPORTANT]
> Om du ställer in `UseCorporateNetwork` egenskapen på True förutsätts att programutvecklaren har aktiverat IWA i programmet. Så här aktiverar du IWA:
> - I ditt UWP-program `Package.appxmanifest` , på fliken **funktioner** , aktiverar du följande funktioner:
>   - **Enterprise-autentisering**
>   - **Privata nätverk (klient & Server)**
>   - **Delat användar certifikat**

IWA är inte aktiverat som standard eftersom Microsoft Store kräver en hög verifierings nivå innan den accepterar program som begär funktionerna i företagets autentisering eller delade användar certifikat. Alla utvecklare vill inte utföra den här verifierings nivån.

På UWP-plattformen fungerar inte den underliggande WAB-implementeringen som den ska i företags scenarier där villkorlig åtkomst är aktive rad. Användare ser symptom på det här problemet när de försöker logga in med hjälp av Windows Hello. När användaren uppmanas att välja ett certifikat:

- Det gick inte att hitta certifikatet för PIN-koden.
- När användaren har valt ett certifikat uppmanas de inte att ange PIN-koden.

Du kan försöka undvika det här problemet genom att använda en alternativ metod, till exempel username-Password och telefonen, men upplevelsen är inte felfri.

## <a name="troubleshooting"></a>Felsökning

Vissa kunder har rapporterat följande inloggnings fel i vissa företags miljöer där de vet att de har en Internet anslutning och att anslutningen fungerar med ett offentligt nätverk.

```Text
We can't connect to the service you need right now. Check your network connection or try this again later.
```

Du kan undvika det här problemet genom att se till att WAB (den underliggande Windows-komponenten) tillåter ett privat nätverk. Det kan du göra genom att ange en register nyckel:

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

Mer information finns i [Web Authentication Broker-Fiddler](/windows/uwp/security/web-authentication-broker#fiddler).

## <a name="next-steps"></a>Nästa steg
Följande exempel innehåller mer information.

Exempel | Plattform | Beskrivning 
|------ | -------- | -----------|
|[Active Directory – dotNet-Native-UWP-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Ett UWP klient program som använder MSAL.NET. Den har åtkomst till Microsoft Graph för en användare som autentiserar med hjälp av en Azure AD 2,0-slutpunkt. <br>![Topologi](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[Active-Directory-Xamarin-Native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | En Xamarin Forms-app som visar hur du använder MSAL för att autentisera Microsoft-personliga konton och Azure AD via Microsoft Identity Platform. Den visar också hur du kommer åt Microsoft Graph och visar den resulterande token. <br>![Diagram som visar hur du använder MSAL för att autentisera Microsoft-personliga konton och Azure AD via Microsoft Identity Platform.](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
