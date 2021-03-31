---
title: Kryptera innehåll med Azure Portal
description: Den här snabb starten visar hur du konfigurerar kryptering för ditt innehåll med hjälp av Azure Media Services i Azure Portal.
ms.topic: quickstart
ms.date: 08/31/2020
ms.openlocfilehash: e42b2d4487f2f51c082f22068160a02b3dcbb0a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89263645"
---
# <a name="quickstart-use-portal-to-encrypt-content"></a>Snabb start: Använd portalen för att kryptera innehåll

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Använd Azure Media Services för att skydda dina medier från den tid det lämnar datorn hela vägen genom lagring, bearbetning och leverans. Med Media Services kan du leverera direktsänd och innehåll på begäran som krypteras dynamiskt med Advanced Encryption Standard (AES-128) eller någon av de tre större Digital Rights Management-systemen (DRM): Microsoft PlayReady, Google Widevine och Apple FairPlay. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM-licenser (PlayReady, Widevine och FairPlay) till auktoriserade klienter. 
 
Om du vill ange krypterings alternativ (om sådana finns) i data strömmen använder du en **strömmande princip** och kopplar den till din strömmande lokalisering. Du skapar en **innehålls nyckel princip** för att konfigurera hur innehålls nyckeln (som ger säker åtkomst till **till gångar**) levereras till slut klienter. Du måste ange kraven (begränsningar) för den innehålls nyckel princip som måste uppfyllas för att nycklar med den angivna konfigurationen ska skickas till klienter. 

> [!NOTE]
> Innehålls nyckel principen behövs inte för att rensa strömma eller ladda ned.

När en data ström begärs av en spelare, använder Media Services den angivna nyckeln för att dynamiskt Kryptera ditt innehåll med hjälp av AES Clear Key eller DRM-kryptering. För att dekryptera data strömmen begär spelaren nyckeln från Media Services Key Delivery Service eller den nyckel leverans tjänst som du har angett. För att avgöra om användaren har behörighet att hämta nyckeln, utvärderar tjänsten den  **innehålls nyckel princip** som du har angett för nyckeln.

Den här snabb starten visar hur du skapar en princip för innehålls nycklar där du anger vilken kryptering som ska användas för din till gång när den strömmas. Snabb starten visar också hur du ställer in den konfigurerade krypteringen på din till gång.

### <a name="suggested-pre-reading"></a>Föreslagen för läsning

* [Dynamisk kryptering och nyckelleverans](content-protection-overview.md)
* [Positionerare för direktuppspelning](streaming-locators-concept.md)
* [Principer för direktuppspelning](streaming-policy-concept.md)
* [Principer för innehållsnycklar](content-key-policy-concept.md)

## <a name="prerequisites"></a>Förutsättningar

Ladda upp och bearbeta ditt innehåll på det sätt som beskrivs i [Hantera till gångar i Azure Portal](manage-assets-quickstart.md)

## <a name="create-a-content-key-policy"></a>Skapa en princip för innehålls nyckel

Skapa **innehålls nyckel principen** för att konfigurera hur innehålls nyckeln (som ger säker åtkomst till dina **till gångar**) levereras till slut klienter.

1. Logga in på [Azure Portal](https://portal.azure.com/).
1. Leta upp och klicka på ditt Media Services-konto.
1. Välj **principer för innehålls nyckel (ny)**.
1. Tryck på **+ Lägg till princip för innehålls nyckel** överst i fönstret. 

Fönstret **skapa en princip för innehålls nyckel** visas. I det här fönstret väljer du krypterings alternativ. Du kan välja att skydda dina medier genom att välja Digital Rights Management (DRM), Advanced Encryption Standard (AES) eller båda.  

![Skapa en princip för innehålls nyckel](./media/encrypt-content-quickstart/create-content-key-policy.png)

Oavsett om du väljer något av DRM-alternativen eller ett alternativ för att rensa AES-128, rekommenderar vi att du anger hur du vill konfigurera begränsningar. Du kan välja att ha en begränsning för öppen eller token. Detaljerad förklaring finns i [kontrol lera åtkomst till innehåll](content-protection-overview.md#controlling-content-access).

### <a name="add-a-drm-content-key"></a>Lägg till en DRM-innehålls nyckel

Du kan välja att skydda ditt innehåll med Microsoft PlayReady och/eller Google Widevine eller Apple FairPlay. Varje licens leverans typ verifierar innehålls nycklarna baserat på dina autentiseringsuppgifter i krypterat format.

#### <a name="license-templates"></a>Licensfiler

Mer information om licensfiler finns i:

* [Licens mal len för Google Widevine](widevine-license-template-overview.md)

    > [!NOTE]
    > Du kan skapa en tom licens mal len utan värden, bara " {} ." Sedan skapas en licens mal len med standardinställningar. Standard fungerar i de flesta fall.
* [Licenskrav för och konfiguration av Apple FairPlay](fairplay-license-overview.md)
* [PlayReady-licensmall](playready-license-template-overview.md)

### <a name="add-aes-clear-key"></a>Lägg till AES-rensad nyckel

Du kan också lägga till en AES-128-kryptering med klar text kryptering till ditt innehåll. Innehålls nyckeln överförs till klienten i ett okrypterat format.

![Klartextnyckeln AES](./media/encrypt-content-quickstart/aes-clear-key-policy.png)

## <a name="create-a-streaming-locator-for-your-asset"></a>Skapa en strömmande plats för din till gång

1. Leta upp och klicka på ditt Media Services-konto.
1. Välj **till gångar (ny)**.
1. I listan med till gångar väljer du den som du vill kryptera.  
1. I avsnittet **streaming Locator** för den valda till gången trycker du på **+ Lägg till en strömmande positionerare**. 
1. Välj en **strömmande princip** som är lämplig för den **innehålls nyckel princip** som du har konfigurerat.

    Avsnittet [strömmande principer](streaming-policy-concept.md) innehåller information om vilken strömmande princip som matchar vilken innehålls nyckel princip.
1. När du har valt lämplig strömmande princip kan du välja princip för innehålls nyckel från List rutan.
1. Tryck på **Lägg** till för att lägga till streaming Locator till din till gång.

    Detta publicerar till gången och genererar strömmande URL: er.

![En strömmande positionerare](./media/encrypt-content-quickstart/multi-drm.png)

## <a name="cleanup-resources"></a>Rensa resurser

Om du avser att testa de andra snabb starterna ska du hålla på de resurser som skapats. Annars går du till Azure Portal, bläddrar till resurs grupper, väljer den resurs grupp under vilken du körde den här snabb starten och tar bort alla resurser.

## <a name="next-steps"></a>Nästa steg

[Hantera tillgångar](manage-assets-quickstart.md)
