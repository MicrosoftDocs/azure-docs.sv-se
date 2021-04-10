---
ms.openlocfilehash: 15e809e1fa361f758a3df7e5b56d4fb80400f656
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103007908"
---
| Plattform          | Projekt på<br/>GitHub                                                                          | Paket                                                                               | Komma<br/>igång                    | Logga in användare                                         | Få åtkomst till webb-API: er                                                 | Allmänt tillgänglig (GA) *eller*<br/>Offentlig för hands version<sup>1</sup> |
|-------------------|------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:--------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Android (Java)    | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | [Snabbstart](../articles/active-directory/develop/quickstart-v2-android.md) | ![Bibliotek kan begära ID-token för användar inloggning.][y] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Allmän tillgänglighet (GA)                                                           |
| Android (Kotlin)  | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | —                                      | ![Bibliotek kan begära ID-token för användar inloggning.][y] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Allmän tillgänglighet (GA)                                                           |
| iOS (SWIFT/OBJ-C) | [MSAL för iOS och macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Självstudie](../articles/active-directory/develop/tutorial-v2-ios.md)         | ![Bibliotek kan begära ID-token för användar inloggning.][y] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Allmän tillgänglighet (GA)                                                           |
| Xamarin (.NET)    | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)             | [Microsoft. Identity. client](https://www.nuget.org/packages/Microsoft.Identity.Client) | —                                      | ![Bibliotek kan begära ID-token för användar inloggning.][y] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Allmän tillgänglighet (GA)                                                           |
<!--
| React Native |[React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [kompletterande användnings villkor för Microsoft Azure för][preview-tos] hands versioner gäller för bibliotek i *offentlig för hands version*.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/