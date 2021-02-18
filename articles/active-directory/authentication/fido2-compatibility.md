---
title: Webb läsar stöd för FIDO2-lösenordsautentisering | Azure Active Directory
description: Webbläsare och operativ system kombinationer stöder FIDO2 lösenordsautentisering för appar som använder Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: nichola
author: knicholasa
manager: martinco
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8ed99338a10eb226823c4bd4857d812038ff632
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101094504"
---
# <a name="browser-support-of-fido2-passwordless-authentication"></a>Webb läsar stöd för FIDO2-lösenordsbaserad autentisering

Azure Active Directory tillåter att [FIDO2-säkerhetsnycklar](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless#fido2-security-keys) används som en enhet med lösen ords skydd. Tillgängligheten för FIDO2-autentisering för Microsoft-konton [annonserades i 2018](https://techcommunity.microsoft.com/t5/identity-standards-blog/all-about-fido2-ctap2-and-webauthn/ba-p/288910). Som beskrivs i meddelandet måste vissa valfria funktioner och tillägg till FIDO2 CTAP-specifikationen implementeras för att ge stöd för säker autentisering med Microsoft-och Azure Active Directory-konton. I följande diagram visas vilka webbläsare och operativ system kombinationer som stöder lösenordsautentisering med hjälp av FIDO2 med Azure Active Directory.

## <a name="supported-browsers"></a>Webbläsare som stöds

I den här tabellen visas stöd för att autentisera Azure Active Directory (Azure AD) och Microsoft-konton (MSA). Microsoft-konton skapas av konsumenter för tjänster som Xbox, Skype eller Outlook.com. Enhets typer som stöds är **USB**, kommunikation i nära fält (**NFC**) och Bluetooth låg energi (**Bell**).

|  | Chrome |  |  | Edge |  |  | Firefox |  |  |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| | USB | NFC | A | USB | NFC | A | USB | NFC | A |
| **Windows**  | ![Chrome stöder USB på Windows för AAD-konton.][y] | ![Chrome stöder NFC på Windows för AAD-konton.][y] | ![Chrome stöder tabell i Windows för AAD-konton.][y] | ![Edge stöder USB på Windows för AAD-konton.][y] | ![Edge stöder NFC på Windows för AAD-konton.][y] | ![Edge stöder Bell på Windows för AAD-konton.][y] | ![Firefox stöder USB på Windows för AAD-konton.][y] | ![Firefox stöder NFC på Windows för AAD-konton.][y] | ![Firefox stöder Bell på Windows för AAD-konton.][y] |
| **macOS**  | ![Chrome har stöd för USB på macOS för AAD-konton.][y] | ![Chrome stöder inte NFC på macOS för AAD-konton.][n] | ![Chrome stöder inte Bell på macOS för AAD-konton.][n] | ![Edge har stöd för USB på macOS för AAD-konton.][y] | ![Edge stöder inte NFC på macOS för AAD-konton.][n] | ![Edge stöder inte Bell på macOS för AAD-konton.][n] | ![Firefox stöder inte USB på macOS för AAD-konton.][n] | ![Firefox stöder inte NFC på macOS för AAD-konton.][n] | ![Firefox har inte stöd för Bell på macOS för AAD-konton.][n] |
| **Linux**  | ![Chrome stöder USB på Linux för AAD-konton.][y] | ![Chrome stöder inte NFC på Linux för AAD-konton.][n] | ![Chrome stöder inte Bell på Linux för AAD-konton.][n] | ![Edge stöder inte USB på Linux för AAD-konton.][n] | ![Edge stöder inte NFC på Linux för AAD-konton.][n] | ![Edge stöder inte Bell på Linux för AAD-konton.][n] | ![Firefox stöder inte USB på Linux för AAD-konton.][n] | ![Firefox stöder inte NFC på Linux för AAD-konton.][n] | ![Firefox har inte stöd för Bell på Linux för AAD-konton.][n] |

## <a name="unsupported-browsers"></a>Webbläsare som inte stöds

Följande kombinationer av operativ system och webbläsare stöds inte, men framtida support och testning unders öks. Om du vill se ytterligare stöd för operativ system och webbläsare lämnar du feedback med verktyget produkt feedback längst ned på sidan.

| Operativsystem | Webbläsare |
| ---- | ---- |
| iOS | Safari, Brave |
| macOS | Safari |
| Android | Chrome |
| Chrome | Chrome |

## <a name="operating-system-versions-tested"></a>Testade operativ Systems versioner

Informationen i tabellen ovan har testats för följande operativ system versioner.

| Operativsystem | Senaste testade version |
| --- | --- |
| Windows | Windows 10-20H2 |
| macOS | OS X 11 Big Sur |
| Linux | Fedora 32-arbets Station |

## <a name="next-steps"></a>Nästa steg
[Aktivera inloggning med lösen ords skydd (för hands version)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key)

<!--Image references-->
[y]: ./media/fido2-compatibility/yes.png
[n]: ./media/fido2-compatibility/no.png
