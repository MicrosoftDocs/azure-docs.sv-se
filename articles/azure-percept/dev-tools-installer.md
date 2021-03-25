---
title: Översikt över installations programmet för Azure percept dev tools Pack
description: Läs mer om hur du använder installations programmet för dev-paket för att påskynda avancerad utveckling med Azure percept
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 7639af0c6e616a6fd8683f2a529a570f7e856973
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047342"
---
# <a name="dev-tools-pack-installer-overview"></a>Översikt över installation av dev tools Pack

Installations programmet för dev tools är en lösning som installerar och konfigurerar alla verktyg som krävs för att utveckla en intelligent gräns lösning. Om du redan har installerat något av de program varu paket som anges nedan kommer installations programmet för utvecklingsverktyg att installera om dessa paket så att dina verktyg stämmer överens med installations programmets versioner.

## <a name="mandatory-tools-installed"></a>Obligatoriska verktyg är installerade

* [Visual Studio Code](https://code.visualstudio.com/)
* [Python 3,6 eller senare](https://www.python.org/)
* [Docker 19,03](https://www.docker.com/)
* [PIP3](https://pip.pypa.io/en/stable/user_guide/)
* [TensorFlow 1,13](https://www.tensorflow.org/)
* [Azure Machine Learning SDK 1,1](/python/api/overview/azure/ml/)

## <a name="optional-tools-available-for-installation"></a>Valfria verktyg som är tillgängliga för installation

* [NVIDIA DEEPSTREAM SDK 5](https://developer.nvidia.com/deepstream-sdk) (Toolkit för att utveckla lösningar för NVIDIA-acceleratorer)
* Intel openprodukts [toolkit 2020,2](https://docs.openvinotoolkit.org/) (Toolkit för att utveckla lösningar för Intel-acceleratorer)
* [Lobe.ai](https://lobe.ai/)  
* [Streamlit](https://www.streamlit.io/)
* [Pytorch 1.4.0 (Windows) eller 1.2.0 (Linux)](https://pytorch.org/)
* [Miniconda3](https://docs.conda.io/en/latest/miniconda.html)
* [Kedjar 5,2](https://chainer.org/)
* [Caffe](https://caffe.berkeleyvision.org/)
* [CUDA Toolkit-10.0.130](https://developer.nvidia.com/cuda-toolkit)
* [Microsoft Cognitive Toolkit 2.5.1](https://www.microsoft.com/research/product/cognitive-toolkit/?lang=fr_ca)

## <a name="known-issues"></a>Kända problem

- Valfri Caffe-installation kan Miss lyckas om Docker inte körs på rätt sätt i systemet. Om du vill installera Caffe kontrollerar du att Docker är installerat och körs innan du försöker installera Caffe med installations programmet för dev tools Pack. 

- Valfri CUDA-installation Miss lyckas på inkompatibla system. Innan du försöker installera [CUDA Toolkit-10.0.130](https://developer.nvidia.com/cuda-toolkit) via installations programmet för dev-paketet kontrollerar du systemets kompatibilitet.

## <a name="minimum-requirements"></a>Minimi krav

* Minimi krav för Docker:

    * Windows:
        * https://docs.docker.com/docker-for-windows/install/#system-requirements

        - Windows 10 64-bitars: Pro, Enterprise eller Education (version 16299 eller senare).

             För Windows 10 Home, se Installera Docker Desktop på Windows Home.
           - Hyper-V-och containers Windows-funktioner måste vara aktiverade.
           - Följande maskin varu krav måste vara uppfyllda för att klienten Hyper-V ska kunna köras på Windows 10:

              - 64-bitars processor med [SLAT (andra nivån adress översättning)](https://en.wikipedia.org/wiki/Second_Level_Address_Translation)
              - 4 GB system-RAM
              - Stöd för maskin varu virtualisering på BIOS-nivå måste vara aktiverat i BIOS-inställningarna. Mer information finns i virtualisering.

        > [!NOTE]
        > Docker har stöd för Docker-skrivbordet i Windows baserat på Microsofts support livs cykel för Windows 10-operativsystem. Mer information finns i [fakta bladet för Windows-livscykeln](https://support.microsoft.com/help/13853/windows-lifecycle-fact-sheet).

    * Mac:
        * https://docs.docker.com/docker-for-mac/install/#system-requirements
       
        Din Mac måste uppfylla följande krav för att kunna installera Docker Desktop:
         
         - **Mac-maskinvaran måste vara en 2010 eller en nyare modell med en Intel-processor**, med Intels maskin varu stöd för MMU-virtualisering (Memory Management Unit), inklusive utökade sid tabeller (EPT) och obegränsat läge. Du kan kontrol lera om din dator har det här stödet genom att köra följande kommando i en Terminal: ```sysctl kern.hv_support```

        Om din Mac stöder hypervisor-ramverket skrivs kommandot ut ```kern.hv_support: 1``` .

         - **MacOS måste vara version 10,14 eller senare**. Det vill säga Mojave, Catalina eller Big Sur. Vi rekommenderar att du uppgraderar till den senaste versionen av macOS.

        Om du får problem när du har uppgraderat macOS till version 10,15 måste du installera den senaste versionen av Docker Desktop för att vara kompatibel med den här versionen av macOS.

        - Minst 4 GB RAM-minne.
        - VirtualBox före version 4.3.30 får inte installeras eftersom det inte är kompatibelt med Docker Desktop.

        > [!NOTE]
        > Docker har stöd för Docker Desktop i de senaste versionerna av macOS. Det vill säga den aktuella versionen av macOS och de två tidigare versionerna. Eftersom nya huvud versioner av macOS görs allmänt tillgängliga, slutar Docker att stödja den äldsta versionen och har stöd för den senaste versionen av macOS (utöver de tidigare två versionerna). Docker Desktop stöder för närvarande macOS Mojave, macOS Catalina och macOS Big Sur.
        > 
        - Installations programmet stöds inte på Apple M1.

## <a name="instructions"></a>Instruktioner

1. Hämta installations programmet för dev-verktyg för [Windows](https://go.microsoft.com/fwlink/?linkid=2132187), [Linux](https://go.microsoft.com/fwlink/?linkid=2132186)och [Mac](https://go.microsoft.com/fwlink/?linkid=2132296).

1. Beroende på din plattform kommer det att finnas några skillnader i att starta installations programmet.

    1. För Windows:
    
        1. Klicka på verktyget **dev-tools-Pack-Installer** för att öppna installations guiden.
        
    1. För Mac:
    
        1. När du har laddat ned flyttar du Dev-Tools-Pack-Installer.app-filen till mappen program.
        
        1. Öppna installations guiden genom att klicka på **dev-tools-Pack-Installer.app** .
        
        1. Om du får en "oidentifierad utvecklare"-säkerhets dialog ruta:
        
            1. Gå till Systeminställningar – > säkerhets & sekretess-> allmänt och klicka på knappen "öppna ändå" bredvid "Dev-Tools-Pack-Installer.app"
        
            1. Klicka på ikonen Electron i dockan igen
        
            1. Klicka på knappen "öppna" i dialog rutan säkerhet
    
    1. För Linux:
    
        1. När du uppmanas från webbläsaren klickar du på Spara för att slutföra hämtningen av installations programmet
        
        1. Lägg till körnings behörigheter till **. appimage** -fil metod 1 (kommando rad):
            
            1. Öppna Linux-terminalen
            
            1. Skriv följande i terminalen för att gå till mappen Hämtade filer
            
                1. CD ~/downloads/
                
            1. Skriv följande i terminalen för att skapa den körbara AppImage
            
                1. chmod + x **dev-tools-Pack-Installer. AppImage**
                
            1. Skriv följande i terminalen för att köra installations programmet
            
                1. ./Dev-Tools-Pack-Installer.AppImage
        
        1. Lägg till körnings behörigheter till **. appimage** -fil metod 2 (UI):
        
            1. Högerklicka på. appimage-filen och välj egenskaper
            
            1. Öppna behörigheter-fliken
            
            1. Markera kryss rutan Tillåt att filen körs som ett program
            
            1. Stäng egenskaperna och öppna. appimage-filen

1. På sidan **Installera installations verktyg för dev-verktyget** klickar du på **Visa licens** för att Visa licens avtalet för varje program varu paket som ingår i installations programmet. Om du godkänner villkoren i licens avtalet markerar du kryss rutan och klickar på **Nästa**.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-license-agreements.png" alt-text="Fönstret licens avtal i installations programmet.":::

1. Klicka på **sekretess policy** för att läsa sekretess policyn för Microsoft. Om du godkänner villkoren för sekretess policyn och vill skicka diagnostikdata till Microsoft väljer du **Ja** och klickar på **Nästa**. Annars väljer du **Nej** och klickar på **Nästa**.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-privacy-statement.png" alt-text="Sidan sekretess policy avtal i installations programmet.":::

1. På sidan **Konfigurera komponenter** väljer du de valfria verktyg som du vill installera (de obligatoriska verktygen installeras som standard).

    1. Om du arbetar med Azure percept-ljudet som är en del av Azure percept DK, se till att installera Intel openprodukts Toolkit och Miniconda3.

    1. Klicka på **Installera** om du vill fortsätta med installationen.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-configure-components.png" alt-text="Installations skärmen visar tillgängliga program varu paket.":::

1. När installationen av alla valda komponenter har slutförts fortsätter guiden att **slutföra installations guide** sidan. Klicka på **Slutför** om du vill avsluta installations programmet.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-finish.png" alt-text="Installations programmets slut för ande skärm.":::

## <a name="docker-status-check"></a>Status kontroll för Docker

Om installations programmet meddelar dig att kontrol lera att Docker Skriv bordet är i ett tillstånd som körs, se följande steg:

   1. Windows:
   
      1. Expandera dolda ikoner i system fältet:
      
         1. Expandera dolda ikoner i system fältet om det är dolt:

            :::image type="content" source="./media/dev-tools-installer/system-tray.png" alt-text="System fält.":::
         
         1. Kontrol lera Docker-skrivbordets ikon visar att Docker Desktop körs:

            :::image type="content" source="./media/dev-tools-installer/docker-status-running.png" alt-text="Docker-status.":::
         
         1. Om du inte ser ikonen ovan som visas i system fältet kan du starta Docker Desktop från Start-menyn.
         
         1. Om Docker-prompten gör att du startar om datorn är det bra att stänga installations programmet och starta om när en omstart har slutförts och Docker är i ett körnings tillstånd. Alla program från tredje part som har installerats bör identifieras och installeras inte om automatiskt.

## <a name="next-steps"></a>Nästa steg

Kolla in [Azure percept Advanced Development-lagringsplatsen](https://github.com/microsoft/azure-percept-advanced-development) för att komma igång med avancerad utveckling för Azure percept dk.
