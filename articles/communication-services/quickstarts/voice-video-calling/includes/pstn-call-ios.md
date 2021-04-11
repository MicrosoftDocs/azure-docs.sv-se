---
author: nikuklic
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: nikuklic
ms.openlocfilehash: 43e3463a3284f57825073888146b38fa14cbf5d3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105109052"
---
[!INCLUDE [Emergency Calling Notice](../../../includes/emergency-calling-notice-include.md)]
## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- En distribuerad kommunikations tjänst resurs. [Skapa en kommunikations tjänst resurs](../../create-communication-resource.md).
- Ett telefonnummer som anskaffats i kommunikations tjänst resursen. [så här hämtar du ett telefonnummer](../../telephony-sms/get-phone-number.md).
- A `User Access Token` för att aktivera anrops klienten. Mer information om [hur du hämtar en `User Access Token` ](../../access-tokens.md)
- Slutför snabb starten för att [komma igång med att lägga till samtal till ditt program](../getting-started-with-calling.md)

### <a name="prerequisite-check"></a>Krav kontroll

- Om du vill visa de telefonnummer som är associerade med kommunikations tjänst resursen loggar du in på [Azure Portal](https://portal.azure.com/), letar upp resursen för kommunikations tjänster och öppnar fliken **telefonnummer** i det vänstra navigerings fönstret.
- Du kan skapa och köra din app med Azure Communication Services som anropar SDK för iOS:

## <a name="setting-up"></a>Konfigurera

## <a name="start-a-call-to-phone"></a>Starta ett samtal till telefon

Ange telefonnummer som du har köpt i kommunikations tjänst resursen, som ska användas för att starta anropet:
> [!WARNING]
> Observera att telefonnummer måste anges i formatet E. 164 internationellt standard. (t. ex.: + 12223334444)

Ändra `startCall` händelse hanterare som ska utföras när knappen *starta samtal* är knackad:

```swift
func startCall() {
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            let startCallOptions = ACSStartCallOptions()
            startCallOptions!.alternateCallerID = PhoneNumber(phoneNumber: "+12223334444")
            self.call = self.callAgent!.startCall([PhoneNumber(phoneNumber: self.callee)], options: startCallOptions)
            self.callDelegate = CallDelegate(self)
            self.call!.delegate = self.callDelegate
        }
    }
}
```

## <a name="run-the-code"></a>Kör koden

Du kan skapa och köra din app på iOS-simulatorn genom att välja **produkt**  >  **körning** eller genom att använda kortkommandot (&#8984;-R).

![Det slutliga utseendet och känslan av snabb starts appen](../media/ios/quick-start-make-call.png)

Du kan ringa ett samtal till telefon genom att ange ett telefonnummer i fältet tillagd text och klicka på knappen **starta samtal** .
> [!WARNING]
> Observera att telefonnummer måste anges i formatet E. 164 internationellt standard. (t. ex.: + 12223334444)

> [!NOTE]
> Första gången du gör ett samtal uppmanas du att få åtkomst till mikrofonen. I ett produktions program bör du använda API- `AVAudioSession` [kontrollen för att kontrol lera behörighetens status och på](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) ett smidigt sätt uppdatera programmets beteende när behörighet inte beviljas.
