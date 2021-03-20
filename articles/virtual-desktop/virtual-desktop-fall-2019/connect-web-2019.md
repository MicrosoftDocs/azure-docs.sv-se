---
title: Anslut Windows Virtual Desktop (klassisk) webb klient – Azure
description: Så här ansluter du till Windows Virtual Desktop (klassisk) med hjälp av webb klienten.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c8a55e35ba89caefc362024fc871ac5eb800447b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88008431"
---
# <a name="connect-to-windows-virtual-desktop-classic-with-the-web-client"></a>Ansluta till Windows Virtual Desktop (klassisk) med webb klienten

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer (klassisk), vilket inte stöder Azure Resource Manager virtuella Skriv bords objekt i Windows. Om du försöker hantera Azure Resource Manager virtuella Windows Desktop-objekt, se [den här artikeln](../connect-web.md).

Med webb klienten kan du komma åt dina Windows-resurser för virtuella skriv bord från en webbläsare utan den långa installations processen.

>[!NOTE]
>Webb klienten har för närvarande inte stöd för mobila operativ system.

## <a name="supported-operating-systems-and-browsers"></a>Operativsystem och webbläsare som stöds

Även om en HTML5-kompatibel webbläsare ska fungera, har vi officiellt stöd för följande operativ system och webbläsare.

| Webbläsare           | Operativsystem som stöds                     | Kommentarer               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Version 55 eller senare |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Åtkomst till fjär resurser-feed

I en webbläsare, navigerar du till Windows Virtual Desktop-webbklienten på <https://rdweb.wvd.microsoft.com/webclient> och loggar in med ditt användar konto.

>[!NOTE]
>Om du använder Windows Virtual Desktop med Azure Resource Manager-integrering ansluter du till dina resurser i <https://rdweb.wvd.microsoft.com/arm/webclient> stället.

>[!NOTE]
>Om du redan har loggat in med ett annat Azure Active Directory-konto än det som du vill använda för Windows Virtual Desktop, bör du antingen logga ut eller använda ett privat webbläsarfönster.

När du har loggat in bör du nu se en lista över resurser. Du kan starta resurser genom att välja dem som en normal app på fliken **alla resurser** .

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om hur du använder webb klienten kan du läsa [komma igång med webb klienten](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
