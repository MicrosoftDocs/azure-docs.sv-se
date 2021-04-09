---
title: Konfigurera GPU för Windows Virtual Desktop – Azure
description: Aktivera GPU-accelererad åter givning och kodning i Windows Virtual Desktop.
author: gundarev
ms.topic: how-to
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: f95b9c1615cc58d9cc0589bad98c7315e571686e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105709471"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop"></a>Konfigurera GPU-acceleration (grafikprocessoracceleration) för Windows Virtual Desktop

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder Windows Virtual Desktop (klassisk) utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/configure-vm-gpu-2019.md).

Windows Virtual Desktop stöder GPU-accelererad åter givning och kodning för bättre prestanda och skalbarhet för appar. GPU-acceleration är särskilt viktigt för grafik intensiva appar.

Följ anvisningarna i den här artikeln för att skapa en GPU-optimerad virtuell Azure-dator, lägga till den i din värd pool och konfigurera den för att använda GPU-acceleration för åter givning och kodning. Den här artikeln förutsätter att du redan har konfigurerat en Windows-klient för virtuella skriv bord.

## <a name="select-an-appropriate-gpu-optimized-azure-virtual-machine-size"></a>Välj en lämplig GPU-optimerad storlek för virtuell Azure-dator

Välj en av Azures storlekar för [NV-serien](../virtual-machines/nv-series.md), [NVv3-serien](../virtual-machines/nvv3-series.md)eller [NVv4-serien](../virtual-machines/nvv4-series.md) . De är skräddarsydda för app-och skriv bords virtualisering och gör det möjligt för de flesta appar och Windows-användargränssnittet att accelereras med GPU. Det rätta valet för din värd pool är beroende av ett antal faktorer, inklusive dina specifika arbets belastningar för appar, önskad kvalitet på användar upplevelsen och kostnad. I allmänhet erbjuder större och mer kompatibla GPU: er en bättre användar upplevelse vid en specifik användar täthet, medan mindre och fraktionerat GPU-storlek ger mer detaljerad kontroll över kostnader och kvalitet.

>[!NOTE]
>De virtuella datorerna i Azures NC-, NCv2-, NCv3-, ND-och NDv2-serien är vanligt vis inte lämpliga för Windows virtuella skrivbord-värdar. De här virtuella datorerna är skräddarsydda för specialiserade, högpresterande beräknings-eller maskin inlärnings verktyg, till exempel de som skapats med NVIDIA-CUDA. De stöder inte GPU-acceleration för de flesta appar eller Windows-användargränssnittet.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Skapa en värdbaserad pool, etablera den virtuella datorn och konfigurera en app-grupp

Skapa en ny adresspool med en virtuell dator med den storlek som du har valt. Mer information finns i [Självstudier: skapa en värdbaserad pool med Azure Portal](./create-host-pools-azure-marketplace.md).

Windows Virtual Desktop stöder GPU-accelererad åter givning och kodning i följande operativ system:

* Windows 10 version 1511 eller senare
* Windows Server 2016 eller senare

Du måste också konfigurera en app-grupp eller använda standard gruppen för Skriv bords appar (med namnet "Skriv bords grupp") som skapas automatiskt när du skapar en ny adresspool. Mer information finns i [Självstudier: hantera program grupper för Windows Virtual Desktop](./manage-app-groups.md).

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Installera grafik driv rutiner som stöds på den virtuella datorn

Om du vill dra nytta av GPU-funktionerna i virtuella datorer i Azure N-serien i Windows Virtual Desktop måste du installera lämpliga grafik driv rutiner. Följ instruktionerna i [operativ system och driv rutiner som stöds](../virtual-machines/sizes-gpu.md#supported-operating-systems-and-drivers) för att installera driv rutiner. Endast driv rutiner som distribueras med Azure stöds.

* För virtuella datorer i Azure NV-serien eller NVv3-serien är det bara NVIDIA GRID-drivrutiner och inte NVIDIA CUDA-drivrutiner som stöder GPU-acceleration för de flesta appar och användar gränssnittet i Windows. Om du väljer att installera driv rutiner manuellt ska du se till att installera RUTNÄTs driv rutiner. Om du väljer att installera driv rutiner med hjälp av tillägget Azure VM installeras RUTNÄTs driv rutiner automatiskt för dessa VM-storlekar.
* För virtuella datorer i Azure NVv4-serien installerar du de AMD-drivrutiner som tillhandahålls av Azure. Du kan installera dem automatiskt med hjälp av tillägget för virtuella Azure-datorer, eller så kan du installera dem manuellt.

Efter installationen av driv rutinen krävs en omstart av datorn. Använd verifierings stegen i ovanstående instruktioner för att bekräfta att grafik driv rutinerna har installerats.

## <a name="configure-gpu-accelerated-app-rendering"></a>Konfigurera åter givning av GPU-accelererad app

Som standard återges appar och skriv bord som körs i konfigurationer med flera sessioner med processorn och utnyttjar inte tillgängliga GPU: er för åter givning. Konfigurera grupprincip för sessions värden för att aktivera GPU-accelererad åter givning:

1. Anslut till Skriv bordet för den virtuella datorn med ett konto med lokal administratörs behörighet.
2. Öppna Start-menyn och skriv "gpedit. msc" för att öppna grupprincip redigeraren.
3. Navigera i trädet till **dator konfiguration**  >  **administrativa mallar**  >  **Windows-komponenter**  >  **Fjärrskrivbordstjänster**  >  **fjärr skrivbord**-  >  **fjärrsessions miljö**.
4. Välj princip **Använd grafik kort för maskin vara för alla Fjärrskrivbordstjänster sessioner** och ange att den här principen **är aktive rad** för att aktivera GPU-rendering i fjärrsessionen.

## <a name="configure-gpu-accelerated-frame-encoding"></a>Konfigurera GPU-accelererad ram kodning

Fjärr skrivbord kodar alla bilder som återges av appar och skriv bord (oavsett om de återges med GPU eller CPU) för överföring till fjärr skrivbords klienter. När en del av skärmen uppdateras ofta kodas den här delen av skärmen med en video-codec (H. 264/AVC). Som standard utnyttjar inte fjärr skrivbord tillgängliga GPU: er för den här kodningen. Konfigurera grupprincip för sessions värden för att aktivera GPU-accelererad ram kodning. Fortsätta med stegen ovan:

>[!NOTE]
>GPU-accelererad ram kodning är inte tillgänglig i virtuella datorer i NVv4-serien.

1. Välj princip **Konfigurera H. 264/AVC-maskinvarubaserad kodning för fjärr skrivbords anslutningar** och ange att den här principen **är aktive rad** för att aktivera maskin varu kodning för AVC/H. 264 i fjärrsessionen.

    >[!NOTE]
    >I Windows Server 2016 väljer du alternativet **föredra att AVC-maskinvarubaserad kodning** **alltid försöker**.

2. Nu när grup principerna har redigerats måste du framtvinga en uppdatering av grup principen. Öppna kommando tolken och skriv:

    ```cmd
    gpupdate.exe /force
    ```

3. Logga ut från fjärrskrivbordssessionen.

## <a name="configure-fullscreen-video-encoding"></a>Konfigurera video kodning för hel skärms läge

Om du ofta använder program som producerar hög bild Rute frekvens innehåll, till exempel 3D-modellering, CAD/CAM och video program, kan du välja att aktivera en video kodning för hel skärms läge för en fjärran sluten session. Video profilen i hel skärms läge ger en högre bild Rute hastighet och bättre användar upplevelse för sådana program vid kostnad av nätverks bandbredd och både värd-och klient resurser för sessioner. Vi rekommenderar att du använder GPU-accelererad ram kodning för hel skärms video kodning. Konfigurera grupprincip för sessions värden för att aktivera video kodning för hel skärms läge. Fortsätta med stegen ovan:

1. Välj princip **prioritering h. 264/avc 444-grafik läge för fjärr skrivbords anslutningar** och ange att principen har **Aktiver ATS** för att tvinga H. 264/AVC 444-codec i fjärrsessionen.
2. Nu när grup principerna har redigerats måste du framtvinga en uppdatering av grup principen. Öppna kommando tolken och skriv:

    ```cmd
    gpupdate.exe /force
    ```

3. Logga ut från fjärrskrivbordssessionen.
## <a name="verify-gpu-accelerated-app-rendering"></a>Verifiera åter givning av GPU-accelererad app

Om du vill kontrol lera att appar använder GPU: n för rendering kan du prova något av följande:

* För virtuella Azure-datorer med en NVIDIA-GPU använder du `nvidia-smi` verktyget enligt beskrivningen i [Verifiera driv rutins installation](../virtual-machines/windows/n-series-driver-setup.md#verify-driver-installation) för att kontrol lera GPU-belastningen när du kör dina appar.
* I operativ system versioner som stöds kan du använda aktivitets hanteraren för att kontrol lera GPU-användningen. Välj GPU på fliken "prestanda" för att se om appar använder GPU: n.

## <a name="verify-gpu-accelerated-frame-encoding"></a>Kontrol lera att GPU-accelererad ram kodning

Verifiera att fjärr skrivbord använder GPU-accelererad kodning:

1. Anslut till den virtuella datorns Station ära dator med hjälp av Windows Virtual Desktop-klienten.
2. Starta Loggboken och navigera till följande nod: program- **och tjänst loggar**  >  **Microsoft**  >  **Windows**  >  **RemoteDesktopServices-RdpCoreCDV**  >  **Operational**
3. Ta reda på om GPU-accelererad kodning används genom att leta efter händelse-ID 170. Om du ser "AVC Hardware Encoder Enabled: 1" används GPU-kodning.

## <a name="verify-fullscreen-video-encoding"></a>Verifiera video encoding för hel skärms läge

Så här kontrollerar du att fjärr skrivbordet använder video kodning för hel skärms läge:

1. Anslut till den virtuella datorns Station ära dator med hjälp av Windows Virtual Desktop-klienten.
2. Starta Loggboken och navigera till följande nod: program- **och tjänst loggar**  >  **Microsoft**  >  **Windows**  >  **RemoteDesktopServices-RdpCoreCDV**  >  **Operational**
3. Om du vill ta reda på om video encoding för hel skärms läge används letar du efter händelse-ID 162. Om du ser "AVC tillgängligt: 1 initial profil: 2048" används AVC 444.

## <a name="next-steps"></a>Nästa steg

Dessa instruktioner bör vara igång med GPU-acceleration på en sessions värd (en virtuell dator). Ytterligare överväganden för att aktivera GPU-acceleration i en större värddator:

* Överväg att använda ett [VM-tillägg](../virtual-machines/extensions/overview.md) för att förenkla installation och uppdateringar av driv rutiner på flera virtuella datorer. Använd [nVidia GPU-drivrutinens tillägg](../virtual-machines/extensions/hpccompute-gpu-windows.md) för virtuella datorer med nVidia GPU: er och Använd [AMD GPU-drivrutinens tillägg](../virtual-machines/extensions/hpccompute-amd-gpu-windows.md) för virtuella datorer med AMD GPU: er.
* Överväg att använda Active Directory grupprincip för att förenkla konfigureringen av grup principer på flera virtuella datorer. Information om hur du distribuerar grupprincip i Active Directory-domänen finns i [arbeta med Grupprincip objekt](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731212(v=ws.11)).