---
title: Ansluta till virtuella Windows-datorer med webb klienten – Azure
description: Ansluta till virtuella Windows-datorer med hjälp av webb klienten.
author: Heidilohr
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: c8a4b22966c3d4db268e212bb3f2d1bbb78fee74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89400644"
---
# <a name="connect-to-windows-virtual-desktop-with-the-web-client"></a>Ansluta till virtuella Windows-datorer med webb klienten

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder Windows Virtual Desktop (klassisk) utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/connect-web-2019.md).

Med webb klienten kan du komma åt dina Windows-resurser för virtuella skriv bord från en webbläsare utan den långa installations processen.

>[!NOTE]
>Webb klienten har för närvarande inte stöd för mobila operativ system.

## <a name="supported-operating-systems-and-browsers"></a>Operativsystem och webbläsare som stöds

Även om en HTML5-kompatibel webbläsare ska fungera, har vi officiellt stöd för följande operativ system och webbläsare.

| Webbläsare           | Operativsystem som stöds                     | Kommentarer               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          | Version 11 eller senare |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Version 55 eller senare |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Åtkomst till fjär resurser-feed

I en webbläsare går du till den Azure Resource Manager-integrerade versionen av webb klienten för virtuella Windows-datorer på <https://rdweb.wvd.microsoft.com/arm/webclient> och loggar in med ditt användar konto.

>[!NOTE]
>Om du använder Windows Virtual Desktop (klassisk) utan Azure Resource Manager-integrering ansluter du till dina resurser i <https://rdweb.wvd.microsoft.com/webclient> stället.
>
> Om du använder US Gov portalen använder du <https://rdweb.wvd.azure.us/arm/webclient/index.html> .

>[!NOTE]
>Om du redan har loggat in med ett annat Azure Active Directory-konto än det som du vill använda för Windows Virtual Desktop, bör du antingen logga ut eller använda ett privat webbläsarfönster.

När du har loggat in bör du nu se en lista över resurser. Du kan starta resurser genom att välja dem som en normal app på fliken **alla resurser** .

## <a name="using-an-input-method-editor"></a>Använda ett IME-program

Webb klienten stöder användning av IME (Input Method Editor) i fjärrsessionen i version **1.0.21.16 eller senare**. Språk paketet för tangent bordet som du vill använda i fjärrsessionen måste vara installerat på den virtuella värd datorn. Om du vill veta mer om hur du konfigurerar språk paket i fjärrsessionen kan du ta [en titt på Lägg till språk paket i en Windows 10-avbildning med flera sessioner](language-packs.md).

Så här aktiverar du IME-ininformation med webb klienten:

1. Innan du ansluter till fjärrsessionen går du till panelen för webb klient **Inställningar** .

2. Växla inställningen **Aktivera IME för Indatamängds metod** till **på**.

3. I list menyn väljer du det tangent bord som du vill använda i fjärrsessionen.

4. Anslut till fjärrsessionen.

Webb klienten ignorerar det lokala IME-fönstret när du fokuserar på fjärrsessionen. Att ändra IME-inställningar när du redan har anslutit till fjärrsessionen har ingen påverkan.

>[!NOTE]
>Om språk paketet inte är installerat på den virtuella värd datorn kommer fjärrsessionen att standardvärdet för engelska (USA).

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om hur du använder webb klienten kan du läsa [komma igång med webb klienten](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
