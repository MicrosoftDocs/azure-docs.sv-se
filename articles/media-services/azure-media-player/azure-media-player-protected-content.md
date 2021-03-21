---
title: Azure Media Player skyddat innehåll
description: Azure Media Player stöder för närvarande AES-128-bitars krypterat innehåll och vanligt krypterat innehåll.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: 83f144c06c23f3ab5507e3561be4a12350e20a42
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91329694"
---
# <a name="protected-content"></a>Skyddat innehåll #

Azure Media Player stöder för närvarande AES-128-bitars krypterat innehåll och gemensamt krypterat innehåll (via PlayReady och Widevine) eller krypterat innehåll via FairPlay. För att kunna spela upp skyddat innehåll korrekt måste du tala om Azure Media Player `protectionInfo` . Den här informationen finns per källa och kan läggas till direkt i `<source>` taggen via `data-setup` .  Du kan också lägga till `protectionInfo` direkt som en parameter om du anger källan dynamiskt.

`protectionInfo` accepterar ett JSON-objekt och inkluderar:

- `type`: `AES` eller `PlayReady` eller `Widevine``FairPlay`
- `certificateUrl`: det här ska vara en direkt länk till ditt värdbaserade FairPlay-certifikat

- `authenticationToken`: det här är ett alternativ fält för att lägga till en kodad autentiseringstoken

> [!IMPORTANT]
> **CertificateUrl** -objektet behövs bara för Fairplay DRM. * * *
>[!NOTE]
> Standard-techOrder har ändrats för att hantera den nya Tech-enheten `html5FairPlayHLS` för att spela upp Fairplay innehåll internt i webbläsare som stöder det (Safari på OSX 8 +). Om du har FairPlay innehåll till uppspelning **och** du har ändrat standard-techOrder till en anpassad i ditt program, måste du lägga till den nya Tech-filen i techOrder-objektet. Vi rekommenderar att du inkluderar det före Silverlight så att innehållet inte spelas upp via PlayReady.

## <a name="code-sample"></a>Kodexempel ##

```html
Ex:

    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source
            src="//example/path/to/myVideo.ism/manifest"
            type="application/vnd.ms-sstr+xml"
            data-setup='{"protectionInfo": [{"type": "AES", "authenticationToken": "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=8130520b-c116-45a9-824e-4a0082f3cb3c&Audience=urn%3atest&ExpiresOn=1450207516&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=eV7HDgZ9msp9H9bnEPGN91sBdU7XsZ9OyB6VgFhKBAU%3d"}]}'
        />
    </video>
or

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
            type: "PlayReady",
            authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
        }] }, ]
    );
```

eller, med flera DRM

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
                type: "PlayReady",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                type: "Widevine",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                   type: "FairPlay",
                  certificateUrl: "//example/path/to/myFairplay.der",
                   authenticationToken: "Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1cm46bWljcm9zb2Z0OmF6dXJlOm1lZGlhc2VydmljZXM6Y29udGVudGtleWlkZW50aWZpZXIiOiIyMTI0M2Q2OC00Yjc4LTRlNzUtYTU5MS1jZWMzMDI0NDNhYWMiLCJpc3MiOiJodHRwOi8vY29udG9zbyIsImF1ZCI6InVybjp0ZXN0IiwiZXhwIjoxNDc0NTkyNDYzLCJuYmYiOjE0NzQ1ODg1NjN9.mE7UxgNhkieMMqtM_IiYQj-FK1KKIzB6lAptw4Mi67A"
        }] } ]
    );
```

> [!NOTE]
> Inte alla webbläsare/plattformar kan spela upp skyddat innehåll. Mer information om vad som stöds finns i avsnittet [uppspelnings teknik](azure-media-player-playback-technology.md) .
> [!IMPORTANT]
> Den token som skickades till spelaren är avsedd för skyddat innehåll och används endast för autentiserade användare. Det förutsätts att programmet använder SSL eller någon annan form av säkerhets mått. Slutanvändaren måste också vara betrodd för att inte missbruka token. om så inte är fallet, kan du inkludera dina säkerhets experter.

## <a name="next-steps"></a>Nästa steg ##

- [Azure Media Player snabb start](azure-media-player-quickstart.md)