---
title: Begär anpassade anspråk (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du begär anpassade anspråk.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 08/26/2019
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: a570dccad5f14cf9adf5ca2825d8a3b31ae60d3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85477200"
---
# <a name="how-to-request-custom-claims-using-msal-for-ios-and-macos"></a>Gör så här: begär anpassade anspråk med MSAL för iOS och macOS

OpenID Connect gör att du kan begära att returnera enskilda anspråk från UserInfo-slutpunkten och/eller i ID-token. En begäran om anspråk visas som ett JSON-objekt som innehåller en lista över begärda anspråk. Se [OpenID Connect Core 1,0](https://openid.net/specs/openid-connect-core-1_0-final.html#ClaimsParameter) för mer information.

Microsoft Authentication Library (MSAL) för iOS och macOS gör det möjligt att begära vissa anspråk i både interaktiva och tysta token för hämtning. Detta görs via `claimsRequest` parametern.

Det finns flera scenarier där det behövs. Exempel:

- Begära anspråk utanför standard uppsättningen för ditt program.
- Begär specifika kombinationer av de standard anspråk som inte kan anges med hjälp av omfattningar för ditt program. Om till exempel en åtkomsttoken avvisas på grund av saknade anspråk, kan programmet begära anspråk som saknas med MSAL.

> [!NOTE]
> MSAL kringgår cachen för åtkomsttoken när en anspråks förfrågan anges. Det är viktigt att endast tillhandahålla `claimsRequest` parameter när ytterligare anspråk behövs (i stället för att alltid ha samma `claimsRequest` parameter i varje MSAL-API-anrop).

`claimsRequest` kan anges i `MSALSilentTokenParameters` och `MSALInteractiveTokenParameters` :

```objc
/*!
 MSALTokenParameters is the base abstract class for all types of token parameters (silent and interactive).
 */
@interface MSALTokenParameters : NSObject

/*!
 The claims parameter that needs to be sent to authorization or token endpoint.
 If claims parameter is passed in silent flow, access token will be skipped and refresh token will be tried.
 */
@property (nonatomic, nullable) MSALClaimsRequest *claimsRequest;

@end
```
`MSALClaimsRequest` kan skapas från en NSString-representation av begäran om JSON-anspråk. 

Mål-C:

```objc
NSError *claimsError = nil;
MSALClaimsRequest *request = [[MSALClaimsRequest alloc] initWithJsonString:@"{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}" error:&claimsError];
```

Införliva

```swift
var requestError: NSError? = nil
let request = MSALClaimsRequest(jsonString: "{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}",
                                        error: &requestError)
```



Den kan också ändras genom att begära ytterligare angivna anspråk:

Mål-C:

```objc
MSALIndividualClaimRequest *individualClaimRequest = [[MSALIndividualClaimRequest alloc] initWithName:@"custom_claim"];
individualClaimRequest.additionalInfo = [MSALIndividualClaimRequestAdditionalInfo new];
individualClaimRequest.additionalInfo.essential = @1;
individualClaimRequest.additionalInfo.value = @"myvalue";
[request requestClaim:individualClaimRequest forTarget:MSALClaimsRequestTargetIdToken error:&claimsError];
```

Införliva

```swift
let individualClaimRequest = MSALIndividualClaimRequest(name: "custom-claim")
let additionalInfo = MSALIndividualClaimRequestAdditionalInfo()
additionalInfo.essential = 1
additionalInfo.value = "myvalue"
individualClaimRequest.additionalInfo = additionalInfo
do {
  try request.requestClaim(individualClaimRequest, for: .idToken)
} catch let error as NSError {
  // handle error here  
}
        
```



`MSALClaimsRequest` ska sedan anges i token-parametrarna och anges till en av MSAL-API: er för hämtning av token:

Mål-C:

```objc
MSALPublicClientApplication *application = ...;
MSALWebviewParameters *webParameters = ...;

MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"user.read"]
                                                                                  webviewParameters:webParameters];
parameters.claimsRequest = request;
    
[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

Införliva

```swift
let application: MSALPublicClientApplication!
let webParameters: MSALWebviewParameters!
        
let parameters = MSALInteractiveTokenParameters(scopes: ["user.read"], webviewParameters: webParameters)
parameters.claimsRequest = request
        
application.acquireToken(with: parameters) { (result: MSALResult?, error: Error?) in            ...

```



## <a name="next-steps"></a>Nästa steg

Lär dig mer om [autentiserings flöden och program scenarier](authentication-flows-app-scenarios.md)
