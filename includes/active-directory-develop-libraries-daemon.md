---
ms.openlocfilehash: 0837c0a23c837065dc2214f947912ee25e4f1d2d
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008014"
---
| Språk/ramverk | Projekt på<br/>GitHub                                                                 | Paket                                                                                | Komma<br/>igång                           | Logga in användare                                            | Få åtkomst till webb-API: er                                                 | Allmänt tillgänglig (GA) *eller*<br/>Offentlig för hands version<sup>1</sup> |
|----------------------|---------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|:---------------------------------------------:|:--------------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)    | [Microsoft. Identity. client](https://www.nuget.org/packages/Microsoft.Identity.Client/) | [Snabbstart](../articles/active-directory/develop/quickstart-v2-netcore-daemon.md) | ![Bibliotek kan inte begära ID-token för användar inloggning.][n] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Allmän tillgänglighet (GA)                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)        | [msal4j](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html)          | —                                             | ![Bibliotek kan inte begära ID-token för användar inloggning.][n] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Allmän tillgänglighet (GA)                                                           |
| Nod               | [MSAL-nod](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal-nod](https://www.npmjs.com/package/@azure/msal-node)  | [Snabbstart](../articles/active-directory/develop/quickstart-v2-nodejs-console.md)  | ![Bibliotek kan inte begära ID-token för användar inloggning.][n] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Allmän tillgänglighet (GA)  |
| Python               | [MSAL python](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [msal – python](https://github.com/AzureAD/microsoft-authentication-library-for-python)  | [Snabbstart](../articles/active-directory/develop/quickstart-v2-python-daemon.md)  | ![Bibliotek kan inte begära ID-token för användar inloggning.][n] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Allmän tillgänglighet (GA)                                                           |
<!--
|PHP| [The PHP League oauth2-client](https://oauth2-client.thephpleague.com/usage/) | [League\OAuth2](https://oauth2-client.thephpleague.com/) | ![Green check mark.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [kompletterande användnings villkor för Microsoft Azure för][preview-tos] hands versioner gäller för bibliotek i *offentlig för hands version*.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/