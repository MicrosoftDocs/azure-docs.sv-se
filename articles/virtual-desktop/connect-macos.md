---
title: Ansluta till Windows Virtual Desktop från macOS – Azure
description: Ansluta till virtuella Windows-datorer med macOS-klienten.
author: Heidilohr
ms.topic: how-to
ms.date: 04/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f437c43c1e9ad960910e7576db4b3ddf3f6623ec
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89230882"
---
# <a name="connect-to-windows-virtual-desktop-with-the-macos-client"></a>Ansluta till virtuella Windows-datorer med macOS-klienten

> Gäller för: macOS 10,12 eller senare

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder Windows Virtual Desktop (klassisk) utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/connect-macos-2019.md).

Du kan komma åt Windows virtuella Skriv bords resurser från dina macOS-enheter med vår nedladdnings bara klient. I den här guiden får du lära dig hur du konfigurerar-klienten.

## <a name="install-the-client"></a>Installera klienten

Kom igång genom att [Ladda ned](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) och installera-klienten på din MacOS-enhet.

## <a name="subscribe-to-a-feed"></a>Prenumerera på en feed

Prenumerera på den feed som administratören fick dig att hämta listan över hanterade resurser som är tillgängliga för dig på din macOS-enhet.

Så här prenumererar du på en feed:

1. Välj **Lägg till arbets yta** på huvud sidan för att ansluta till tjänsten och hämta dina resurser.
2. Ange feed-URL: en. Detta kan vara en URL eller en e-post adress:
   - Om du använder en URL kan du använda den som din administratör fick. Normalt är URL: en <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery> .
   - Ange din e-postadress om du vill använda e-post. Detta instruerar klienten att söka efter en URL som är kopplad till din e-postadress om administratören har konfigurerat servern på det sättet.
   - Använd om du vill ansluta via US Gov-portalen <https://rdweb.wvd.azure.us/api/arm/feeddiscovery> .
3. Välj **Lägg till**.
4. Logga in med ditt användar konto när du uppmanas till det.

När du har loggat in bör du se en lista över tillgängliga resurser.

När du prenumererar på en feed uppdateras innehållet i feeden automatiskt regelbundet. Resurser kan läggas till, ändras eller tas bort utifrån ändringar som gjorts av administratören.

## <a name="next-steps"></a>Nästa steg

Mer information om macOS-klienten finns i [Kom igång med MacOS-klientens](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/) dokumentation.
