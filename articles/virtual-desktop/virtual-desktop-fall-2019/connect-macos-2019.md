---
title: Ansluta till Windows Virtual Desktop (klassisk) från macOS – Azure
description: Så här ansluter du till Windows Virtual Desktop (klassisk) med macOS-klienten.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 4014de262490259ed2e61eb17c80b40679e62773
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445218"
---
# <a name="connect-to-windows-virtual-desktop-classic-with-the-macos-client"></a>Ansluta till Windows Virtual Desktop (klassisk) med macOS-klienten

> Gäller för: macOS 10,12 eller senare

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer (klassisk), vilket inte stöder Azure Resource Manager virtuella Skriv bords objekt i Windows. Om du försöker hantera Azure Resource Manager virtuella Windows Desktop-objekt, se [den här artikeln](../connect-macos.md).

Du kan komma åt Windows virtuella Skriv bords resurser från dina macOS-enheter med vår nedladdnings bara klient. I den här guiden får du lära dig hur du konfigurerar-klienten.

## <a name="install-the-client"></a>Installera klienten

Kom igång genom att [Ladda ned](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) och installera-klienten på din MacOS-enhet.

## <a name="subscribe-to-a-feed"></a>Prenumerera på en feed

Prenumerera på den feed som administratören fick dig att hämta listan över hanterade resurser som är tillgängliga för dig på din macOS-enhet.

Så här prenumererar du på en feed:

1. Välj **Lägg till arbets yta** på huvud sidan för att ansluta till tjänsten och hämta dina resurser.
2. Ange feed-URL: en. Detta kan vara en URL eller en e-post adress:
   - Om du använder en URL kan du använda den som din administratör fick. Normalt är URL: en <https://rdweb.wvd.microsoft.com> .
   - Ange din e-postadress om du vill använda e-post. Detta instruerar klienten att söka efter en URL som är kopplad till din e-postadress om administratören har konfigurerat servern på det sättet.
3. Välj **Lägg till**.
4. Logga in med ditt användar konto när du uppmanas till det.

När du har loggat in bör du se en lista över tillgängliga resurser.

När du prenumererar på en feed uppdateras innehållet i feeden automatiskt regelbundet. Resurser kan läggas till, ändras eller tas bort utifrån ändringar som gjorts av administratören.

## <a name="next-steps"></a>Nästa steg

Mer information om macOS-klienten finns i [Kom igång med MacOS-klientens](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/) dokumentation.