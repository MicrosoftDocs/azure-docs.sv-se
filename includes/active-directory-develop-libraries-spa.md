---
ms.openlocfilehash: 05fc91667f34262c6b510c0e1464d3e5fad339bc
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107590186"
---
| Språk/ramverk | Project on<br/>GitHub                                                                                                    | Paket                                                                      | Få<br/>Startat                             | Logga in användare                                         | Få åtkomst till webb-API:er                                                 | Allmänt tillgänglig (GA) *eller*<br/>Offentlig förhandsversion<sup>1</sup> |
|----------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|:-----------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Angular              | [MSAL Angular v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)<sup>2</sup>         | [msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | —                                               | ![Biblioteket kan begära ID-token för användar inloggning.][y] | ![Biblioteket kan begära åtkomsttoken för skyddade webb-API:er.][y] | Offentlig förhandsversion                                               |
| Angular              | [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/msal-angular-v1/lib/msal-angular)<sup>3</sup> | [msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     |[Självstudie](../articles/active-directory/develop/tutorial-v2-angular.md)| ![Biblioteket kan begära ID-token för användar inloggning.][y] | ![Biblioteket kan begära åtkomsttoken för skyddade webb-API:er.][y] | Allmän tillgänglighet (GA)                                                           |
| AngularJS            | [MSAL AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs)<sup>3</sup>         | [msal-angularjs](https://www.npmjs.com/package/@azure/msal-angularjs) | —                                               | ![Biblioteket kan begära ID-token för användar inloggning.][y] | ![Biblioteket kan begära åtkomsttoken för skyddade webb-API:er.][y] | Offentlig förhandsversion                                               |
| JavaScript           | [MSAL.js v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)<sup>2</sup>              | [msal-browser](https://www.npmjs.com/package/@azure/msal-browser)     | [Självstudie](../articles/active-directory/develop/tutorial-v2-javascript-auth-code.md) | ![Biblioteket kan begära ID-token för användar inloggning.][y] | ![Biblioteket kan begära åtkomsttoken för skyddade webb-API:er.][y] | Allmän tillgänglighet (GA)                                                           |
|JavaScript|[MSAL.js 1.0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)<sup>3</sup> | [msal-core](https://www.npmjs.com/package/@azure/msal-core)    | [Självstudie](../articles/active-directory/develop/tutorial-v2-javascript-spa.md)| ![Biblioteket kan begära ID-token för användar inloggning.][y] | ![Biblioteket kan begära åtkomsttoken för skyddade webb-API:er.][y] | Allmän tillgänglighet (GA)                                                           |
| React                | [MSAL React](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)<sup>2</sup>                 | [msal-react](https://www.npmjs.com/package/@azure/msal-react)         | —                                               | ![Biblioteket kan begära ID-token för användar inloggning.][y] | ![Biblioteket kan begära åtkomsttoken för skyddade webb-API:er.][y] | Offentlig förhandsversion                                               |
<!--
| Vue | [Vue MSAL]( https://github.com/mvertopoulos/vue-msal) | [vue-msal]( https://www.npmjs.com/package/vue-msal) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Kompletterande villkor för användning Microsoft Azure förhandsversioner][preview-tos] gäller för bibliotek i den offentliga *förhandsversionen.*

<sup>2</sup> [Auth-kodflöde][auth-code-flow] med endast PCKE (rekommenderas). 

<sup>3 Endast</sup> [implicit beviljandeflöde.][implicit-flow]

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[auth-code-flow]: ../articles/active-directory/develop/v2-oauth2-auth-code-flow.md
[implicit-flow]: ../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md