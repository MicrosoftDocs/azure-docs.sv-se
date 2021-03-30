---
title: Korrigeringar för fel meddelandet "du kan inte ta dig dit härifrån", fel – Azure AD
description: Hitta möjliga korrigeringar för varför du får fel meddelandet "du kan inte ta dig dit härifrån".
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 10/10/2018
ms.author: curtand
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: df3941c895ce67862eb53b8e96bc7a6d53c1ed02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88799425"
---
# <a name="possible-fixes-for-the-you-cant-get-there-from-here-error-message"></a>Eventuella korrigeringar för fel meddelandet "du kan inte ta dig dit härifrån"

Vid åtkomst till din organisations interna webbappar eller tjänster kan du få ett fel meddelande som säger att **du inte kan komma dit** härifrån. Det här meddelandet innebär att din organisation har placerat en princip på plats som hindrar enheten från att komma åt din organisations resurser. Du kanske måste kontakta supportavdelningen för att åtgärda problemet. här är några saker som du kan prova först.

## <a name="make-sure-youre-using-a-supported-browser"></a>Kontrol lera att du använder en webbläsare som stöds
Om du får det **här meddelandet kan du inte se** att du försöker komma åt din organisations webbplatser från en webbläsare som inte stöds, kontrol lera vilken webbläsare som körs.

![Fel meddelande som rör webb läsar stöd](media/user-help-device-remediation/browser-version.png)

För att åtgärda det här problemet måste du installera och köra en webbläsare som stöds baserat på operativ systemet. Om du använder Windows 10 innehåller de webbläsare som stöds Microsoft Edge, Internet Explorer och Google Chrome. Om du använder ett annat operativ system kan du kontrol lera den fullständiga listan över [webbläsare som stöds](../conditional-access/concept-conditional-access-conditions.md#supported-browsers).

## <a name="make-sure-youre-using-a-supported-operating-system"></a>Kontrol lera att du använder ett operativ system som stöds
Kontrol lera att du kör en version av operativ systemet som stöds, inklusive:

- **Windows-klient.** Windows 7 eller senare.

- **Windows Server.** Windows Server 2008 R2 eller senare.

- **MacOS.** macOS X eller senare

- **Android och iOS.** Senaste versionen av Android och iOS mobila operativ system

För att åtgärda det här problemet måste du installera och köra ett operativ system som stöds.

## <a name="make-sure-your-device-is-joined-to-your-network"></a>Kontrol lera att enheten är ansluten till nätverket
Om du får det **här meddelandet kan du inte se** att enheten är inkompatibel med din organisations åtkomst princip, se till att du har anslutit enheten till din organisations nätverk.

![Fel meddelande som rör om du är i nätverket](media/user-help-device-remediation/network-version.png)

### <a name="to-check-whether-your-device-is-joined-to-your-network"></a>Kontrol lera om enheten är ansluten till nätverket
1. Logga in i Windows med ditt arbets-eller skol konto. Till exempel alain@contoso.com.

2. Anslut till din organisations nätverk via ett virtuellt privat nätverk (VPN) eller DirectAccess.

3. När du är ansluten trycker du på **Windows-tangenten + L** för att låsa enheten.

4. Lås upp enheten med ditt arbets-eller skol konto och försök sedan komma åt den problematiska appen eller tjänsten igen.

    Om du ser att det här fel meddelandet **inte** visas igen väljer du länken **Mer information** och kontaktar supportavdelningen med information.

### <a name="to-join-your-device-to-your-network"></a>Så här ansluter du enheten till nätverket
Om enheten inte är ansluten till din organisations nätverk kan du göra något av följande:

- **Anslut till din arbets enhet.** Anslut din arbetsägda Windows 10-enhet till din organisations nätverk så att du kan komma åt potentiellt begränsade resurser. Mer information och stegvisa anvisningar finns i [ansluta din arbets enhet till din organisations nätverk](user-help-join-device-on-network.md).

- **Registrera din personliga enhet för arbete.** Registrera din personliga enhet, vanligt vis en telefon eller surfplatta, på organisationens nätverk. När enheten har registrerats kan den komma åt din organisations begränsade resurser. Mer information och stegvisa anvisningar finns i [Registrera din personliga enhet i din organisations nätverk](user-help-register-device-on-network.md).

## <a name="next-steps"></a>Nästa steg
- [Vad är portalen för Mina appar?](./my-apps-portal-end-user-access.md)

- [Logga in med telefonen, inte med ditt lösenord](user-help-auth-app-sign-in.md)