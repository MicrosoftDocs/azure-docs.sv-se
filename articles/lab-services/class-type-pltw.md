---
title: Konfigurera projekt Skapa på ett sätt labb med Azure Lab Services
description: Lär dig hur du konfigurerar labb för att lära projektet att leda till att klasser.
ms.topic: article
ms.date: 10/28/2020
ms.openlocfilehash: ca4fdae2372895c17c4a98dd3959935108846744
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95024627"
---
# <a name="set-up-labs-for-project-lead-the-way-classes"></a>Konfigurera labb för projekt som leder till att klasser

[Projektets bly (PLTW)](https://www.pltw.org/) är en ideell organisation som tillhandahåller PreK 12- &ndash; USA i dator vetenskap, teknik och biomedicinsk vetenskap.  I varje PLTW-klass använder studenter en mängd olika program vara som en del av deras praktiska inlärnings upplevelser.  Många av programmen kräver en snabb processor eller, i vissa fall, en GPU.  Den här artikeln visar hur du konfigurerar labb för följande PLTW-klasser, som vanligt vis erbjuds studenter i grader 9 &ndash; 12:

- **Introduktion till teknisk design**

    Studenter introduceras i processen för konstruktions design, som innefattar att använda [Autodesk Inventory CAD-program (Computer-Aided Design)](https://www.autodesk.com/products/inventor/new-features) för 3D-modellering.

- **Principer för teknik**
    
    Studenter lär sig mer om tekniska mekanismer, struktur-och material styrka och automatisering.  Den här klassen använder program vara som [MD-solider](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/MD+Solids/MD+Solids+Software+Installation+Guide.pdf), [västra Point Bridge designer](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/West+Point+Bridge+Builder/Installation+Guide+for+West+Point+Bridge+Designer.pdf)och [America Army-simulering](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/America's+Army/Installation+Guide+for+Americas+Army+Simulation+17-18.pdf).

- **Byggnads teknik och arkitektur**

    Eleverna lär sig att skapa och utforma och utveckla webbplatser med hjälp av [Autodesk Revit](https://www.autodesk.com/products/revit/overview) Architecture Design Software för 3D Building Information Modeling (BIM).

- **Dator integrerad tillverkning**

    Eleverna utforskar moderna tillverknings processer som omfattar Robotics och automatisering.   I den här klassen använder studenter [Autodesk inventora CAD](https://www.autodesk.com/products/inventor/new-features) och [Autodesk Inventory Computer-Aided Manufacturing (Cam)](https://www.autodesk.com/products/inventor-cam/overview) . 

- **Digital elektronik**

    Studenterna studerar elektroniska logik kretsar och enheter med hjälp av [National instrument Multisim](https://www.ni.com/en-us/shop/electronic-test-instrumentation/application-software-for-electronic-test-and-instrumentation-category/what-is-multisim.html) -simulering och krets design program.

- **Konstruktion och utveckling av teknik**

    Studenter bidrar till en lösning från slut punkt till slut punkt genom att kombinera forskning, utformning och testning som finns på en panel med tekniker.  I den här klassen använder eleverna [Autodesk Inventory CAD](https://www.autodesk.com/products/inventor/new-features) -programvara.

- **Viktig information för dator vetenskap**

    Studenter introduceras för beräknings koncept och verktyg.  De startar med block-baserad programmering och flyttar sedan till textbaserad kodning med hjälp av kod miljöer som [VEXcode V5-block](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/VEXcode+V5+Blocks/VexCode+V5+Blocks+Installation+Guide.pdf).

- **Principer för dator vetenskap**
    
    Eleverna växer sin programmerings expertis med [python](https://www.python.org/) med hjälp av [Microsoft Visual Studio kod utvecklings miljö](https://code.visualstudio.com/). 

- **Dator vetenskap A**

    Eleverna utökar sin programmerings behörighet i den här klassen genom att lära oss utveckla mobilappar.  I den här klassen får de lära sig [Java](https://www.java.com/) med hjälp av [Microsoft Visual Studio kod utvecklings miljö](https://code.visualstudio.com/).  Studenter använder också en emulator som gör det möjligt för dem att köra och testa sin kod för mobilapp.  [Kontakta Azure Lab Services](mailto:AzLabsCOVIDSupport@microsoft.com)om du vill ha information om hur du konfigurerar en emulator i Azure Lab Services.

En fullständig lista över klass program vara finns på PLTW- [webbplatsen](https://www.pltw.org/pltw-software) för varje klass.

## <a name="lab-configuration"></a>Labb konfiguration

För att börja konfigurera labb för PLTW behöver du ett Azure-prenumerations-och labb konto. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar. 

När du har skaffat en Azure-prenumeration kan du skapa ett nytt labb konto i Azure Lab Services. Mer information om hur du skapar ett nytt labb konto finns i [Konfigurera ett labb konto](./tutorial-setup-lab-account.md). Du kan också använda ett befintligt labb konto.

När du har konfigurerat ett labb konto bör du skapa ett separat labb för varje PLTW som din skola erbjuder.  Vi rekommenderar också att du skapar separata avbildningar för varje typ av PLTW-klass.  Mer information om hur du strukturerar dina labb och bilder finns i blogg inlägget [flytta från ett fysiskt labb till Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931).

### <a name="lab-account-settings"></a>Labb konto inställningar

Aktivera inställningarna för ditt labb konto enligt beskrivningen i följande tabell. Mer information om hur du aktiverar Azure Marketplace-avbildningar finns i [Ange de Azure Marketplace-avbildningar som är tillgängliga för labb skapare](./specify-marketplace-images.md).

| Inställning för labb konto | Instruktioner |
| -------------------- | ----- |
| Marketplace-avbildning | Aktivera Windows 10 Pro-avbildningen för användning i ditt labb konto. |

<br>

### <a name="lab-settings"></a>Labb inställningar
Storleken på den virtuella datorn (VM) som vi rekommenderar att du använder för PLTW-klasser beror på vilka typer av arbets belastningar som dina studenter gör i klassen.  För de tidigare listade klasserna rekommenderar vi att du använder liten GPU (visualisering) och stora VM-storlekar. När du konfigurerar labb för dina PLTW-klasser, se rikt linjerna i följande tabell:

| Labb inställning | Värde och beskrivning | Klass rekommendation |
| ------------ | ------------------ | --- |
| Storlek på virtuell dator | **Liten GPU (visualisering)**<br>Passar bäst för fjärrvisualisering, strömning, spel och kodning med ramverk som OpenGL och DirectX. | Vi rekommenderar att du använder den här storleken för följande PLTW-klasser: byggnads konstruktion och arkitektur, digital elektronik, integrerad tillverkning och teknisk utveckling.
| Storlek på virtuell dator | **Stor**<br>Passar bäst för program som behöver snabbare processorer, bättre prestanda för lokala diskar, stora databaser och stora cacheminnen. | Vi rekommenderar att du använder den här storleken för följande PLTW-klasser: Introduktion till teknisk design, principer för teknik, viktiga data för dator vetenskap, principer för dator vetenskap och dator vetenskap A. |

<br>

### <a name="license-server"></a>Licens Server
De flesta program som används i de tidigare nämnda PLTW-klasserna behöver *inte* åtkomst till en licens Server.  Du måste dock ha åtkomst till en licens server om du planerar att använda Autodesk nätverks licensierings modell för följande program vara:
-   Revit
-   Hållning
-   Inventerings kamera

Om du vill använda nätverks licensiering med Autodesk-programvara [innehåller PLTW detaljerade anvisningar](https://www.pltw.org/pltw-software) för att installera Autodesk Network License Manager på licens servern.  Den här licens servern finns vanligt vis antingen i ditt lokala nätverk eller på en virtuell Azure-dator (VM) i Azure Virtual Network.

När licens servern har kon figurer ATS måste du koppla [det virtuella nätverket](./how-to-connect-peer-virtual-network.md) till ditt [labb konto](./tutorial-setup-lab-account.md). Du måste använda nätverks-peering *innan* du skapar labbet så att dina virtuella labb datorer kan komma åt licens servern och vice versa.

Autodesk-genererade licensfiler bäddar in MAC-adressen för licens servern.  Om du väljer att vara värd för din licens Server genom att använda en virtuell Azure-dator är det viktigt att se till att licens serverns MAC-adress inte ändras. Om MAC-adressen ändras måste du återskapa dina licensfiler. Gör så här för att förhindra att MAC-adressen ändras:

- [Ange en statisk privat IP-adress och Mac-adress](./how-to-create-a-lab-with-shared-resource.md#static-private-ip-and-mac-address) för den virtuella Azure-dator som är värd för licens servern.
- Se till att konfigurera både ditt labb konto och licens serverns virtuella nätverk i en region eller plats som har tillräcklig VM-kapacitet så att du inte behöver flytta resurserna till en ny region eller plats senare.

Mer information finns i [Konfigurera en licens server som en delad resurs](./how-to-create-a-lab-with-shared-resource.md).

### <a name="template-machine"></a>Mall dator
Några av de installationsfiler som du behöver för PLTW är stora. När du laddar ned filerna till en virtuell labb mal len dator kan det ta lång tid att kopiera.

I stället för att hämta installationsfiler till mallen och installera allt där, rekommenderar vi att du skapar dina PLTW-avbildningar i din fysiska miljö.  Du kan sedan importera de anpassade bilderna till det delade bild galleriet så att du kan använda dem för att skapa dina labb.  Mer information finns i [Ladda upp en anpassad avbildning till den delade avbildnings galleriet](./upload-custom-image-shared-image-gallery.md).

När du följer den här rekommendationen noterar du de viktigaste uppgifterna för att skapa ett labb:

1. Skapa avbildningen för klassen i den fysiska miljön.

    a.  Använd PLTW för att ladda ned installationsfilerna och installera den program vara som krävs.

    > [!NOTE]    
    > När du installerar Autodesk-programmen måste den dator du installerar dem på kunna kommunicera med licens servern. I installations guiden för Autodesk uppmanas du att ange dator namnet på den dator som licens servern finns på.  Om du är värd för din licens server på en virtuell Azure-dator kan du behöva vänta på att installera Autodesk på den virtuella labb mal len för att få åtkomst till din licens server i installations guiden.

    b.  [Installera och konfigurera OneDrive](./how-to-prepare-windows-template.md#install-and-configure-onedrive) eller andra säkerhets kopierings alternativ som din skola kan använda.
    
    c.  [Installera och konfigurera Windows-uppdateringar](./how-to-prepare-windows-template.md#install-and-configure-updates).

1.  Överför den anpassade avbildningen till det [delade avbildnings galleriet som är kopplat till ditt labb konto](./how-to-attach-detach-shared-image-gallery.md).

1.  Skapa ett labb och välj sedan den anpassade avbildningen som du laddade upp i föregående steg.

1.  När labbet har skapats startar och ansluter du till mallen VM för att kontrol lera att avbildningen fungerar som förväntat.

1.  Publicera sedan den virtuella dator mal len för att skapa studenternas virtuella datorer.

## <a name="student-devices"></a>Elev enheter
Studenter kan ansluta till sina labbbaserade virtuella datorer från Windows-datorer, Mac-och Chromebook. Anvisningar finns i:

- [Anslut från Windows](./how-to-use-classroom-lab.md#connect-to-the-vm)
- [Anslut från Mac](./connect-virtual-machine-mac-remote-desktop.md)
- [Anslut från Chromebook](./connect-virtual-machine-chromebook-remote-desktop.md)

## <a name="cost"></a>Cost
Vi tar upp ett exempel på kostnads uppskattning för PLTW-klasserna.  Den här uppskattningen omfattar inte kostnaden för att köra en licens Server eller använda ett delat avbildnings Galleri. Anta att du har en klass av 25 studenter, som var och en har 20 timmar med schemalagda klass tider.  Varje student har också ytterligare 10 kvot timmar för läxor eller tilldelningar utanför den schemalagda klass tiden.  Här är de uppskattade kostnaderna:

- **Stor virtuell dator**

    25 studenter &times; (20 schemalagda timmar + 10 kvot timmar) &times; 70 labb enheter &times; USD 0,01 per timme = USD 525.00

- **Liten GPU (visualisering)**

    25 studenter &times; (20 schemalagda timmar + 10 kvot timmar) &times; 160 labb enheter &times; USD 0,01 per timme = USD 1200.00

> [!IMPORTANT] 
> Kostnads beräkningen är endast till exempel syfte.  Aktuell pris information finns i [Azure Lab Services prissättning](https://azure.microsoft.com/pricing/details/lab-services/).

> [!NOTE] 
> Många av PLTW-klasserna använder program som nås via en webbläsare, till exempel MIT app Inventory.  Dessa webbläsarbaserade program kräver ingen snabb CPU eller GPU, och du kan komma åt dem från valfri enhet som har en Internet anslutning.  När studenter använder dessa typer av program, rekommenderar vi att de använder webbläsaren på deras fysiska enhet i stället i webbläsaren på den virtuella datorn i labbet. Studenter kan hjälpa till att hålla kostnaderna nere genom att enbart använda sin labb-VM för program som kräver en snabb processor eller GPU.

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat ditt labb kan du läsa följande artiklar:

- [Lägg till användare](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ange kvoter](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ange ett schema](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [E-postregistrering länkar till studenter](how-to-configure-student-usage.md#send-invitations-to-users) 
