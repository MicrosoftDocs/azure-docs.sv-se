---
ms.openlocfilehash: a393cf978318c39081805cae7e38c3386ff156f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103010717"
---
| Språk/ramverk | Projekt på<br/>GitHub                                                                                                    | Paket                                                                      | Komma<br/>igång                             | Logga in användare                                         | Få åtkomst till webb-API: er                                                 | Allmänt tillgänglig (GA) *eller*<br/>Offentlig för hands version<sup>1</sup> |
|----------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|:-----------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Angular              | [MSAL-vinkeln v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)<sup>2</sup>         | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | —                                               | ![Bibliotek kan begära ID-token för användar inloggning.][y] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Offentlig för hands version                                               |
| Angular              | [MSAL-vinkel](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/msal-angular-v1/lib/msal-angular)<sup>3</sup> | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     |[Självstudie](../articles/active-directory/develop/tutorial-v2-angular.md)| ![Bibliotek kan begära ID-token för användar inloggning.][y] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Allmän tillgänglighet (GA)                                                           |
| AngularJS            | [MSAL AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs)<sup>3</sup>         | [@azure/msal-angularjs](https://www.npmjs.com/package/@azure/msal-angularjs) | —                                               | ![Bibliotek kan begära ID-token för användar inloggning.][y] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Offentlig för hands version                                               |
| JavaScript           | [MSAL.js v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)<sup>2</sup>              | [@azure/msal-browser](https://www.npmjs.com/package/@azure/msal-browser)     | [Självstudie](../articles/active-directory/develop/tutorial-v2-javascript-auth-code.md) | ![Bibliotek kan begära ID-token för användar inloggning.][y] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Allmän tillgänglighet (GA)                                                           |
|JavaScript|[MSAL.js 1,0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)<sup>3</sup> | [@azure/msal-core](https://www.npmjs.com/package/@azure/msal-core)    | [Självstudie](../articles/active-directory/develop/tutorial-v2-javascript-spa.md)| ![Bibliotek kan begära ID-token för användar inloggning.][y] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Allmän tillgänglighet (GA)                                                           |
| React                | [MSAL reagerar](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)<sup>2</sup>                 | [@azure/msal-react](https://www.npmjs.com/package/@azure/msal-react)         | —                                               | ![Bibliotek kan begära ID-token för användar inloggning.][y] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Offentlig för hands version                                               |
<!--
| Vue | [Vue MSAL]( https://github.com/mvertopoulos/vue-msal) | [vue-msal]( https://www.npmjs.com/package/vue-msal) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [kompletterande användnings villkor för Microsoft Azure för][preview-tos] hands versioner gäller för bibliotek i *offentlig för hands version*.

<sup>2</sup> [auth Code Flow][auth-code-flow] med endast PCKE (rekommenderas). 

<sup>3</sup> endast [implicit beviljat beviljat flöde][implicit-flow] .

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[auth-code-flow]: ../articles/active-directory/develop/v2-oauth2-auth-code-flow.md
[implicit-flow]: ../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md