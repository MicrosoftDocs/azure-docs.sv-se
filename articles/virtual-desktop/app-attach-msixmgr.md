---
title: Använda MSIXMGR-verktyget – Azure
description: Så här använder du MSIXMGR-verktyget för virtuella Windows-datorer.
author: Heidilohr
ms.topic: how-to
ms.date: 02/23/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4b34fb0d3bb2d49255007b9722a0a636c1441b8c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746283"
---
# <a name="using-the-msixmgr-tool"></a>Använda verktyget MSIXMGR

Verktyget MSIXMGR är till för att expandera MSIX-paketerade program till MSIX-avbildningar. Verktyget tar ett MSIX program (. MSIX) och expanderar den till en VHD-, VHDx-eller CIM-fil. Den resulterande MSIX-avbildningen lagras i det Azure Storage konto som används av distributionen av virtuella Windows-datorer. I den här artikeln visas hur du använder MSIXMGR-verktyget.

>[!NOTE]
>För att garantera kompatibiliteten bör du se till att CIMs som lagrar dina MSIX-avbildningar skapas på den OS-version som du kör i dina Windows-pooler för virtuella skriv bord. MSIXMGR kan skapa CIM-filer, men du kan bara använda dessa filer med en adresspool som kör Windows 10 20H2.

## <a name="requirements"></a>Krav

Innan du kan följa anvisningarna i den här artikeln måste du göra följande:

- [Ladda ned MSIXMGR-verktyget](https://aka.ms/msixmgr)
- Hämta ett MSIX-paketerat program (. MSIX-fil)
- Få administratörs behörighet på datorn där du ska skapa MSIX-avbildningen

## <a name="create-an-msix-image"></a>Skapa en MSIX-avbildning

Expansion är en process för att ta ett MSIX paketerat program (. MSIX) och zippa upp den till en MSIX-avbildning (. VHD (x) eller. CIM-fil).

Expandera en MSIX-fil:

1. [Hämta MSIXMGR-verktyget](https://aka.ms/msixmgr) om du inte redan gjort det.

2. Zippa upp MSIXMGR.zip till en lokal mapp.

3. Öppna en kommando tolk i förhöjd läge.

4. Hitta den lokala mappen från steg 2.

5. Kör följande kommando i kommando tolken för att skapa en MSIX-avbildning.

    ```cmd
    msixmgr.exe -Unpack -packagePath <path to package> -destination <output folder> [-applyacls] [-create] [-vhdSize <size in MB>] [-filetype <CIM | VHD | VHDX>] [-rootDirectory <rootDirectory>]
    ```

    Kom ihåg att ersätta plats hållarnas värden med relevanta värden. Exempel:

    ```cmd
    msixmgr.exe -Unpack -packagePath "C:\Users\%username%\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.vhdx" -applyacls -create -vhdSize 200 -filetype "vhdx" -rootDirectory apps
    ```

6. Nu när du har skapat avbildningen går du till målmappen och kontrollerar att du har skapat MSIX-avbildningen (. VHDX).

## <a name="create-an-msix-image-in-a-cim-file"></a>Skapa en MSIX-avbildning i en CIM-fil

Du kan också använda kommandot i [steg 5](#create-an-msix-image) för att skapa CIM-och VHDX-filer genom att ersätta filtypen och mål Sök vägen.

Så här använder du exempelvis kommandot för att skapa en CIM-fil:

```cmd
msixmgr.exe -Unpack -packagePath "C:\Users\ssa\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.cim" -applyacls -create -vhdSize 200 -filetype "cim" -rootDirectory apps
```

Så här använder du kommandot för att skapa en VHDX:

```cmd
msixmgr.exe -Unpack -packagePath "C:\Users\ssa\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.vhdx" -applyacls -create -vhdSize 200 -filetype "vhdx" -rootDirectory apps
```

## <a name="next-steps"></a>Nästa steg

Vill du veta mer om MSIX-appen ansluta i [Vad är MSIX app Attach?](what-is-app-attach.md)

Information om hur du konfigurerar app Attach finns i följande artiklar:

- [Konfigurera MSIX-appbifogning med Azure Portal](app-attach-azure-portal.md)
- [Konfigurera MSIX-appen koppla med PowerShell](app-attach-powershell.md)
- [Skapa PowerShell-skript för MSIX app Attach](app-attach.md)
- [Förbereda en MSIX-avbildning för Windows Virtual Desktop](app-attach-image-prep.md)
- [Konfigurera en fil resurs för MSIX-appens koppling](app-attach-file-share.md)

Om du har frågor om MSIX app Attach kan du läsa [vanliga frågor och svar](app-attach-faq.md) och bifogade [ord i appen](app-attach-glossary.md).
