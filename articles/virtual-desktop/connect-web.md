---
title: Ansluta till Windows Virtual Desktop med webbklienten – Azure
description: Så här ansluter du Windows Virtual Desktop med webbklienten.
author: Heidilohr
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: 80a090abee45f9cb3ec6ee5406aad6abf1d73a59
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725016"
---
# <a name="connect-to-windows-virtual-desktop-with-the-web-client"></a>Ansluta till Windows Virtual Desktop med webbklienten

>[!IMPORTANT]
>Det här innehållet gäller för Windows Virtual Desktop med Azure Resource Manager Windows Virtual Desktop objekt. Om du använder en Windows Virtual Desktop (klassisk) utan att Azure Resource Manager objekt kan du läsa [den här artikeln.](./virtual-desktop-fall-2019/connect-web-2019.md)

Med webbklienten kan du komma Windows Virtual Desktop resurser från en webbläsare utan den långa installationsprocessen.

>[!NOTE]
>Webbklienten har för närvarande inte stöd för mobilt operativsystem.

## <a name="supported-operating-systems-and-browsers"></a>Operativsystem och webbläsare som stöds

Alla HTML5-kompatibla webbläsare bör fungera, men vi stöder officiellt följande operativsystem och webbläsare.

| Webbläsare           | Operativsystem som stöds                     | Kommentarer               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          | Version 11 eller senare |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Version 55 eller senare |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Få åtkomst till feeden för fjärrresurser

I en webbläsare navigerar du till Azure Resource Manager-integrerade versionen av Windows Virtual Desktop-webbklienten på och <https://rdweb.wvd.microsoft.com/arm/webclient> loggar in med ditt användarkonto.

>[!NOTE]
>Om du använder en Windows Virtual Desktop (klassisk) utan Azure Resource Manager kan du ansluta till dina resurser på <https://rdweb.wvd.microsoft.com/webclient> i stället.
>
> Om du använder portalen US Gov använder du <https://rdweb.wvd.azure.us/arm/webclient/index.html> .

>[!NOTE]
>Om du redan har loggat in med ett annat Azure Active Directory-konto än det som du vill använda för Windows Virtual Desktop bör du antingen logga ut eller använda ett privat webbläsarfönster.

När du har loggat in bör du nu se en lista över resurser. Du kan starta resurser genom att välja dem på samma sätt som du gör med en normal app på **fliken Alla** resurser.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder webbklienten finns i [Kom igång med webbklienten.](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)
