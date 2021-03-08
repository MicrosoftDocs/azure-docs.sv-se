---
title: Stöd för Media Services Apple FairPlay-licens
description: Det här avsnittet ger en översikt över licens krav och konfiguration för Apple-FairPlay.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 187c1e60d97e0bebb3b6216b0055ddffe6e6cb4c
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102454389"
---
# <a name="apple-fairplay-license-requirements-and-configuration"></a>Licenskrav för och konfiguration av Apple FairPlay

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Med Azure Media Services kan du kryptera ditt HLS-innehåll med **Apple Fairplay** (AES-128 CBC). Media Services tillhandahåller också en tjänst för att leverera FairPlay-licenser. När en spelare försöker spela upp ditt FairPlayt innehåll skickas en begäran till licens leverans tjänsten för att få en licens. Om licens tjänsten godkänner begäran utfärdar den den licens som skickas till klienten och används för att dekryptera och spela upp det angivna innehållet.

Media Services tillhandahåller också API: er som du kan använda för att konfigurera dina FairPlay-licenser. Det här avsnittet beskriver licens krav för FairPlay och visar hur du kan konfigurera en **Fairplay** -licens med hjälp av Media Services-API: er. 

## <a name="requirements"></a>Krav

Följande krävs när du använder Media Services för att kryptera ditt HLS-innehåll med **Apple Fairplay** och använda Media Services för att leverera Fairplay-licenser:

* Registrera dig med [Apple Development-programmet](https://developer.apple.com/).
* Apple kräver att innehålls ägaren hämtar [distributions paketet](https://developer.apple.com/contact/fps/). Ange att du redan har implementerat KSM (Key Security Module) med Media Services och att du begär det slutliga FPS-paketet. Det finns instruktioner i det sista FPS-paketet för att generera certifiering och hämta programmets hemliga nyckel (fråga). Du kan använda be att konfigurera FairPlay.
* Följande saker måste anges på Media Services nyckel/licens leverans sida:

    * **App-certifikat (AC)**: det här är en PFX-fil som innehåller den privata nyckeln. Du skapar filen och krypterar den med ett lösen ord. . Pfx-filen ska vara i base64-format.

        Följande steg beskriver hur du skapar en. PFX-certifikat fil för FairPlay:

        1. Installera OpenSSL från https://slproweb.com/products/Win32OpenSSL.html .

            Gå till mappen där FairPlay-certifikatet och andra filer som levererats av Apple är.
        2. Kör följande kommando från kommandoraden. Detta konverterar. CER-filen till en. PEM-fil.

            "C:\OpenSSL-Win32\bin\openssl.exe" x509-informerad der-in FairPlay. cer-out FairPlay-out. pem
        3. Kör följande kommando från kommandoraden. Detta konverterar. pem-filen till en. pfx-fil med den privata nyckeln. Lösen ordet för. pfx-filen uppmanas sedan av OpenSSL.

            "C:\OpenSSL-Win32\bin\openssl.exe" PKCS12-export FairPlay-out. pfx-INKEY PrivateKey. pem-in FairPlay-out. pem-Passin file:privatekey-pem-pass.txt
            
    * **Lösen ord för app-certifikat**: lösen ordet för att skapa. pfx-filen.
    * **Fråga**: den här nyckeln tas emot när du genererar certifieringen med hjälp av Apple Developer-portalen. Varje utvecklings grupp får en unik fråga. Spara en kopia av frågan och lagra den på ett säkert ställe. Du måste konfigurera fråga som FairPlayAsk med Media Services.
    
* Följande saker måste anges av klient sidan för FPS:

  * **App-certifikat (AC)**: det här är en CER/. der-fil som innehåller den offentliga nyckeln, som operativ systemet använder för att kryptera vissa nytto laster. Media Services behöver veta om det, eftersom det krävs av spelaren. Nyckel leverans tjänsten dekrypterar den med motsvarande privata nyckel.

* Om du vill spela upp en FairPlay-krypterad ström, få en riktig fråga först och sedan skapa ett verkligt certifikat. Den här processen skapar alla tre delarna:

  * . der-fil
  * . pfx-fil
  * lösen ord för. pfx
  
> [!NOTE]
> Azure Media Services kontrollerar inte certifikatets förfallo datum under paketering eller nyckel leverans. Den kommer att fortsätta att fungera när certifikatet har gått ut.

## <a name="fairplay-and-player-apps"></a>FairPlay-och Player-appar

När ditt innehåll krypteras med **Apple-Fairplay** krypteras de enskilda video-och ljud exemplen med hjälp av **CBC-läget AES-128** . **Fairplay Streaming** (FPS) är integrerat i enhetens operativ system, med inbyggt stöd för iOS och Apple TV. Safari på OS X aktiverar FPS med hjälp av EME-gränssnittet (Encrypted Media Extensions).

Azure Media Player stöder också uppspelning av FairPlay. Mer information finns i [Azure Media Player-dokumentationen](https://amp.azure.net/libs/amp/latest/docs/index.html).

Du kan utveckla dina egna Player-appar med hjälp av iOS SDK. För att kunna spela upp FairPlay innehåll måste du implementera License Exchange-protokollet. Det här protokollet har inte angetts av Apple. Det är upp till varje app att skicka förfrågningar om nyckel leverans. Den Media Services FairPlay Key Delivery service förväntar sig att SPC ska komma som ett meddelande via www-form-URL-kodat inlägg i följande format:

```
spc=<Base64 encoded SPC>
```

## <a name="fairplay-configuration-net-example"></a>FairPlay Configuration .NET-exempel

Du kan använda Media Services API för att konfigurera FairPlay-licenser. När spelaren försöker spela upp ditt FairPlayt innehåll skickas en begäran till licens leverans tjänsten för att erhålla licensen. Om licens tjänsten godkänner begäran utfärdar tjänsten licensen. Den skickas till klienten och används för att dekryptera och spela upp det angivna innehållet.

> [!NOTE]
> Vanligt vis vill du bara konfigurera FairPlay-principinställningar en gång, eftersom du bara kommer att ha en uppsättning certifiering och en fråga.

I följande exempel används [Media Services .NET SDK](/dotnet/api/microsoft.azure.management.media.models) för att konfigurera licensen.

```csharp
private static ContentKeyPolicyFairPlayConfiguration ConfigureFairPlayPolicyOptions()
{

    string askHex = "";
    string FairPlayPfxPassword = "";

    var appCert = new X509Certificate2("FairPlayPfxPath", FairPlayPfxPassword, X509KeyStorageFlags.Exportable);

    byte[] askBytes = Enumerable
        .Range(0, askHex.Length)
        .Where(x => x % 2 == 0)
        .Select(x => Convert.ToByte(askHex.Substring(x, 2), 16))
        .ToArray();

    ContentKeyPolicyFairPlayConfiguration fairPlayConfiguration =
    new ContentKeyPolicyFairPlayConfiguration
    {
        Ask = askBytes,
        FairPlayPfx =
                Convert.ToBase64String(appCert.Export(X509ContentType.Pfx, FairPlayPfxPassword)),
        FairPlayPfxPassword = FairPlayPfxPassword,
        RentalAndLeaseKeyType =
                ContentKeyPolicyFairPlayRentalAndLeaseKeyType
                .PersistentUnlimited,
        RentalDuration = 2249 // in seconds
    };

    return fairPlayConfiguration;
}
```

## <a name="next-steps"></a>Nästa steg

Ta en titt på hur du [skyddar med DRM](protect-with-drm.md)
