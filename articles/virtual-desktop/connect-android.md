---
title: Ansluta till Windows Virtual Desktop från Android – Azure
description: Ansluta till virtuella Windows-datorer med Android-klienten.
author: Heidilohr
ms.topic: how-to
ms.date: 03/25/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 07e0754cc9fb940218999e2012a9cb87341557cb
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448091"
---
# <a name="connect-to-windows-virtual-desktop-with-the-android-client"></a>Ansluta till virtuella Windows-datorer med Android-klienten

> Gäller för: Android 4,1 och senare, Chromebooks med Chrome 53 och senare.

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder Windows Virtual Desktop (klassisk) utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/connect-android-2019.md).

Du kan komma åt Windows-resurser för virtuella skriv bord från din Android-enhet med vår nedladdnings bara klient. Du kan också använda Android-klienten på Chromebook-enheter som har stöd för Google Play Butik. I den här guiden får du lära dig hur du konfigurerar Android-klienten.

## <a name="install-the-android-client"></a>Installera Android-klienten

Kom igång genom att [Ladda ned](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) och installera-klienten på din Android-enhet.

## <a name="subscribe-to-a-feed"></a>Prenumerera på en feed

Prenumerera på den feed som administratören har fått för att hämta listan över hanterade resurser som du har åtkomst till på din Android-enhet.

Så här prenumererar du på en feed:

1. I anslutnings Center trycker du på **+** och trycker sedan på **fjär resurs matning**.
2. Ange feed-URL: en i fältet **feed URL** . Feed-URL: en kan vara antingen en URL eller en e-postadress.
   - Om du använder en URL kan du använda den som din administratör har gett dig, vanligt vis <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery> .
   - Ange din e-postadress om du vill använda e-post. Klienten söker efter en URL som är kopplad till din e-postadress om din administratör har konfigurerat servern på det sättet.
   - Använd om du vill ansluta via US Gov-portalen <https://rdweb.wvd.azure.us/api/arm/feeddiscovery> .
3. Tryck på **NÄSTA**.
4. Ange dina autentiseringsuppgifter när du uppmanas till det.
   - Ge användar namnet behörighet att komma åt resurser för **användar namn**.
   - Ange lösen ordet som är associerat med användar namnet för **lösen ord**.
   - Du kan också uppmanas att ange ytterligare faktorer om din administratör har konfigurerat autentiseringen på det sättet.

När du har prenumererat bör anslutnings centret Visa fjär resurserna.

När du prenumererar på en feed uppdateras feedens innehåll automatiskt regelbundet. Resurser kan läggas till, ändras eller tas bort utifrån ändringar som gjorts av administratören.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder Android-klienten finns i [komma igång med Android-klienten](/windows-server/remote/remote-desktop-services/clients/remote-desktop-android/).
