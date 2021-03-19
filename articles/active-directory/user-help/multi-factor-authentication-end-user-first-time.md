---
title: Vad är sidan Ytterligare verifiering? – Azure AD
description: Så här kommer du till sidan för ytterligare säkerhets verifiering för tvåstegsverifiering
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 05/28/2020
ms.author: curtand
ms.openlocfilehash: e5b07f8f7ae766d110c87a495a3e1623b815e526
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88795991"
---
# <a name="what-is-the-additional-verification-page"></a>Vad är sidan Ytterligare verifiering?

Din organisation tar extra steg för att kontrol lera att du är den som du säger att du är när du loggar in. Den här extra säkerhets verifieringen kallas även tvåstegsverifiering. Det består av en kombination av ditt användar namn, lösen ord och en mobil enhet eller telefon. Om du vill göra en inaktive ring av tvåstegsverifiering för en Microsoft-konto, till exempel alain@outlook.com , använder du instruktionerna för [att aktivera eller inaktivera tvåstegsverifiering för din Microsoft-konto](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

<center>

![Bild av metoder för konceptuell autentisering](../authentication/media/concept-mfa-howitworks/methods.png)</center>

Tvåstegsverifiering är säkrare än bara ett lösen ord eftersom det är beroende av två typer av autentisering:

- Något du känner till, t. ex. ditt lösen ord.
- Något du har, till exempel en telefon eller annan enhet som du bär.

Tvåstegsverifiering kan hjälpa dig att stoppa skadliga hackare från att vara du. Även om de har ditt lösen ord är strider att de inte har din enhet.

>[!Important]
>Om du är administratör för att söka efter information om hur du aktiverar tvåstegsverifiering för dina anställda eller andra användare kan du läsa mer i [dokumentationen för Azure Active Directory autentisering](../authentication/index.yml). Den här artikeln är avsedd för användare som försöker använda tvåstegsverifiering med ett arbets-eller skol konto (till exempel alain@contoso.com ).

## <a name="who-decides-if-you-use-this-feature"></a>Vem bestämmer om du använder den här funktionen?

Vem som bestämmer om du använder tvåstegsverifiering beror på vilken typ av konto du har:

- **Arbets-eller skol konto.** Om du använder ett arbets-eller skol konto (till exempel alain@contoso.com ), är det upp till din organisation oavsett om du använder tvåstegsverifiering, tillsammans med de olika verifierings metoderna. Eftersom din organisation har valt att du måste använda den här funktionen, finns det inget sätt för dig att stänga av den separat.

- **Personligt Microsoft-konto.** Du kan välja att konfigurera en tvåstegsverifiering för dina personliga Microsoft-konton (till exempel alain@outlook.com ). Du kan aktivera eller inaktivera den när du vill, med hjälp av enkla instruktioner för att [Aktivera eller inaktivera tvåstegsverifiering för din Microsoft-konto](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

    >[!Note]
    >Om du har andra problem med tvåstegsverifiering och ett av dina personliga Microsoft-konton finns det fler förslag på [hur du använder tvåstegsverifiering med Microsoft-konto](https://support.microsoft.com/help/12408/microsoft-account-how-to-use-two-step-verification).

## <a name="open-the-additional-security-verification-page"></a>Öppna sidan ytterligare säkerhets verifiering

När din organisation har aktiverat tvåstegsverifiering varje gång du loggar in får du en uppmaning som talar om att du får mer information om hur du kan skydda ditt konto.

![Prompt för mer information krävs](media/multi-factor-authentication-verification-methods/multi-factor-authentication-initial-prompt.png)

### <a name="to-access-the-additional-security-verification-page"></a>Så här öppnar du sidan ytterligare säkerhets verifiering

1. Välj **Nästa** från frågan **Mer information krävs** .

    Sidan **ytterligare säkerhets verifiering** visas.

2. På sidan **ytterligare säkerhets verifiering** väljer du den tvåstegsverifiering som du vill använda för att kontrol lera att du är den som du anger när du loggar in på ditt arbets-eller skol konto. Du kan välja:

    | Kontakt metod | Beskrivning |
    | --- | --- |
    | Mobilapp | <ul><li>**Ta emot meddelanden om verifiering.** Med det här alternativet skickas ett meddelande till Authenticator-appen på din smartphone eller surfplatta. Visa meddelandet och välj **autentisera** i appen om det är giltigt. Ditt arbete eller din skola kan kräva att du anger en PIN-kod innan du autentiserar.</li><li>**Använd verifierings kod.** I det här läget genererar appen en verifierings kod som uppdateras var 30: e sekund. Ange den mest aktuella verifierings koden på inloggnings skärmen.<br>Microsoft Authenticator-appen finns för [Android](https://go.microsoft.com/fwlink/?linkid=866594) och [iOS](https://go.microsoft.com/fwlink/?linkid=866594).</li></ul> |
    | Telefon för autentisering | <ul><li>**Telefonsamtal** placerar ett automatiskt röst samtal till det telefonnummer som du anger. Besvara anropet och tryck på fyrkant (#) på telefon knappen för att autentisera.</li><li>**SMS** avslutar ett textmeddelande som innehåller en verifierings kod. Följ anvisningarna i texten, antingen svara på textmeddelandet eller ange verifierings koden som anges i inloggnings gränssnittet.</li></ul> |
    | Arbetstelefon | Placerar ett automatiskt röst samtal till det telefonnummer som du anger. Besvara anropet och tryck på fyrkant (#) på telefon knappen för att autentisera. |

## <a name="next-steps"></a>Nästa steg

När du har valt en metod för tvåstegsverifiering på sidan **ytterligare säkerhets verifiering** måste du konfigurera den:

- [Konfigurera din mobila enhet som din verifierings metod](multi-factor-authentication-setup-phone-number.md)

- [Konfigurera din arbets telefon som din verifierings metod](multi-factor-authentication-setup-office-phone.md)

- [Konfigurera Microsoft Authenticator-appen som din verifierings metod](multi-factor-authentication-setup-auth-app.md)

## <a name="related-resources"></a>Relaterade resurser

- [Logga in med tvåstegsverifiering](multi-factor-authentication-end-user-signin.md)

- [Få hjälp med tvåstegsverifiering](multi-factor-authentication-end-user-troubleshoot.md)