---
title: Defender för IoT-installation
description: Lär dig hur du installerar en sensor och den lokala hanterings konsolen för Azure Defender för IoT.
ms.date: 12/2/2020
ms.topic: how-to
ms.openlocfilehash: 5bdb292750ea041be68a22519583511f58b3b517
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782256"
---
# <a name="defender-for-iot-installation"></a>Defender för IoT-installation

Den här artikeln beskriver hur du installerar följande element i Azure Defender för IoT:

- **Sensor**: Defender för IoT-sensorer samlar in ICS-nätverks trafik genom att använda passiv (utan agent) övervakning. Vid passiv och inkräktande har sensorerna ingen påverkan på andra datorer och IoT-nätverk och-enheter. Sensorn ansluter till en intervall port eller nätverket trycker och börjar direkt övervaka nätverket. Identifieringar visas i sensor konsolen. Där kan du Visa, undersöka och analysera dem i en nätverks karta, enhets inventering och en omfattande uppsättning rapporter. Exempel är rapporter om riskbedömning, data utvinnings frågor och angrepps vektorer. Läs mer om sensor funktioner i [användar handboken för Defender för IoT-sensorn (direkt hämtning)](./getting-started.md).

- **Lokal hanterings konsol**: i den lokala hanterings konsolen kan du hantera enhets hantering, riskhantering och sårbarhets hantering. Du kan också använda den för att utföra hot övervakning och incident svar i företaget. Det ger en enhetlig vy över alla nätverks enheter, nyckel IoT och olika risk indikatorer och aviseringar som upptäckts i anläggningar där sensorer distribueras. Använd den lokala hanterings konsolen för att visa och hantera sensorer i Air-gapped nätverk.

Den här artikeln beskriver följande installations information:

  - **Maskin vara:** Information om Dell och HPE fysiska enheter.

  - **Program vara:** Sensor-och lokal hanterings konsol program varu installation.

  - **Virtuella enheter:** Information om virtuella datorer och program varu installation.

Efter installationen ansluter du sensorn till nätverket.

## <a name="about-defender-for-iot-appliances"></a>Om Defender för IoT-enheter 

Följande avsnitt innehåller information om Defender för IoT-sensor apparater och installationen av den lokala hanterings konsolen för Defender för IoT.

### <a name="physical-appliances"></a>Fysiska enheter

Defender for IoT-sensorn ansluter till en intervall port eller ett nätverk, och börjar omedelbart samla in ICS-nätverks trafik genom att använda passiv (utan agent) övervakning. Den här processen har ingen påverkan på Nätverks-och nätverks enheter eftersom den inte placeras i data Sök vägen och inte söker på enhets enheter aktivt.

Följande rack monterings anordningar är tillgängliga:

| **Distributions typ** | **Företag** | **Enterprise** | **SMB** | **Linje** |
|--|--|--|--|--|
| **Modell** | HPE DL360 | Dell PowerEdge R340 XL | HPE DL20 | HPE DL20 |
| **Övervaka portar** | upp till 15 RJ45 eller 8 OPT | upp till 9 RJ45 eller 6 OPT | upp till 8 RJ45 eller 6 OPT | 4 RJ45 |
| **Maximal bandbredd\*** | 3 GB/SEK | 1 GB/SEK | 1 GB/SEK | 100 MB/SEK |
| **Maximalt antal skyddade enheter** | 30 000 | 10 000 | 15 000 | 1 000 |

* Maximal bandbredds kapacitet kan variera beroende på protokoll distribution.

### <a name="virtual-appliances"></a>Virtuella installationer

Följande virtuella enheter är tillgängliga:

| **Distributions typ** | **Enterprise** | **SMB** | **Linje** |
|--|--|--|--|
| **Beskrivning** | Virtuell installation för företags distributioner | Virtuell installation för SMB-distributioner | Virtuell installation för rad distributioner |
| **Maximal bandbredd\*** | 150 MB/SEK | 15 MB/SEK | 3 MB/SEK |
| **Maximalt antal skyddade enheter** | 3 000 | 300 | 100 |
| **Distributions typ** | Stora företag | SMB | Linje |
| **Beskrivning** | Virtuell installation för företags distributioner | Virtuell installation för SMB-distributioner | Virtuell installation för rad distributioner |

* Maximal bandbredds kapacitet kan variera beroende på protokoll distribution.

### <a name="hardware-specifications-for-the-on-premises-management-console"></a>Specifikationer för maskin vara för den lokala hanterings konsolen

 | Objekt | Beskrivning |
 |----|--|
 **Beskrivning** | I en arkitektur med flera nivåer ger den lokala hanterings konsolen insyn och kontroll över geografiskt distribuerade platser. Den kan integreras med SOC-säkerhetsstackar, inklusive Siem, biljett system, nästa generations brand väggar, säkra plattformar för fjärråtkomst, och Defender för IoT ICS-Sand låda med skadlig kod. |
 **Distributions typ** | Stora företag |
 **Typ av apparat**  | Dell R340, VM |
 **Antal hanterade sensorer** | Obegränsat |

## <a name="prepare-for-the-installation"></a>Förbereda för installationen

### <a name="access-the-iso-installation-image"></a>Få åtkomst till ISO-installations avbildningen

Installations avbildningen är tillgänglig från Defender för IoT-portalen.

För att komma åt filen:

1. Logga in på ditt Defender för IoT-konto.

1. Gå till sidan **nätverks sensor** eller **lokal hanterings konsol** och välj en version som du vill ladda ned.

### <a name="install-from-dvd"></a>Installera från DVD

Innan du installerar bör du kontrol lera att du har:

- En bärbar DVD-enhet med USB-anslutningen.

- En ISO Installer-avbildning.

För att installera:

1. Bränn avbildningen på en DVD eller Förbered en disk på en nyckel. Anslut en bärbar DVD-enhet till datorn, högerklicka på ISO-avbildningen och välj **Bränn till disk**.

1. Anslut DVD-skivan eller disken i en nyckel och konfigurera installations programmet för att starta från DVD eller disk på en nyckel.

### <a name="install-from-disk-on-a-key"></a>Installera från disk på en nyckel

Innan du installerar bör du kontrol lera att du har:

  - Rufus installerad.
  
  - En disk i nyckeln med USB version 3,0 och senare. Den minsta storleken är 4 GB.

  - En avbildnings fil för ISO-installationen.

Disken i en nyckel kommer att raderas i den här processen.

Så här förbereder du en disk i en nyckel:

1. Kör Rufus och välj **sensor ISO**.

1. Anslut disken i en nyckel till Front panelen.

1. Ange BIOS för servern för att starta från USB-minnet.

## <a name="dell-poweredger340xl-installation"></a>Dell PowerEdgeR340XL-installation

Innan du installerar program varan på Dell-produkten måste du justera installations programmets BIOS-konfiguration:

  - [Dell PowerEdge R340 Front Panel](#dell-poweredge-r340-front-panel) och [Dell PowerEdge R340 back panel](#dell-poweredge-r340-back-panel) innehåller en beskrivning av panelerna fram och tillbaka, tillsammans med information som krävs för installation, till exempel driv rutiner och portar.

  - Med [Dell BIOS-konfiguration](#dell-bios-configuration) får du information om hur du ansluter till hanterings gränssnittet för Dell-utrustning och konfigurerar BIOS.

  - [Program varu installation (Dell R340)](#software-installation-dell-r340) beskriver den procedur som krävs för att installera programmet Defender för IoT-sensorn.

### <a name="dell-poweredge-r340xl-requirements"></a>Krav för Dell PowerEdge-R340XL 

För att installera Dell PowerEdge R340XL-enheten behöver du:

- Enterprise-licens för Dell Remote Access Controller (iDrac)

- XML för BIOS-konfiguration

- Versioner av serverns inbyggda program:

  - BIOS-version 2.1.6

  - iDrac-version 3.23.23.23

### <a name="dell-poweredge-r340-front-panel"></a>Dell PowerEdge R340-Front Panel

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-front-panel.jpg" alt-text="Dell PowerEdge R340-front panel.":::

 1. Vänster kontroll panel 
 1. Optisk enhet (valfritt) 
 1. Höger kontroll panel 
 1. Information-tagg 
 1. Enheter  

### <a name="dell-poweredge-r340-back-panel"></a>Dell PowerEdge-R340 på bak panelen

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-back-panel.jpg" alt-text="Dell PowerEdge R340 back panel.":::

1. Serie port 
1. NIC-port (GB 1) 
1. NIC-port (GB 1) 
1. Halv Höjds PCIe 
1. Kort plats för PCIe-expansion med full höjd 
1. Strömförsörjnings enhet 1 
1. Strömförsörjnings enhet 2 
1. System identifiering 
1. CMA-knapp (system status indikator kabel port) 
1. USB 3,0-port (2) 
1. iDRAC9 dedikerad nätverks port 
1. VGA-port 

### <a name="dell-bios-configuration"></a>Konfiguration av Dell BIOS

Dell BIOS-konfiguration krävs för att justera Dell-produkten så att den fungerar med program varan.

BIOS-konfigurationen utförs via en fördefinierad konfiguration. Filen är tillgänglig från [Hjälp Center](https://help.cyberx-labs.com/).

Importera konfigurations filen till Dell-produkten. Innan du använder konfigurations filen måste du upprätta kommunikationen mellan Dell-produkten och hanterings datorn.

Dell-produkten hanteras av en integrerad iDRAC med livs cykel styrenhet (LC). LC är inbäddad i alla Dell PowerEdge-servrar och innehåller funktioner som hjälper dig att distribuera, uppdatera, övervaka och underhålla dina Dell PowerEdge-enheter.

För att kunna upprätta kommunikationen mellan Dell-produkten och hanterings datorn måste du definiera iDRAC IP-adress och hanterings datorns IP-adress i samma undernät.

När anslutningen har upprättats kan BIOS konfigureras.

Så här konfigurerar du Dell BIOS:

1. [Konfigurera IP-adressen för iDRAC](#configure-idrac-ip-address)

1. [Importera konfigurations filen för BIOS](#import-the-bios-configuration-file)

#### <a name="configure-idrac-ip-address"></a>Konfigurera iDRAC IP-adress

1. Starta sensorn.

1. Om operativ systemet redan är installerat väljer du nyckeln F2 för att ange BIOS-konfigurationen.

1. Välj **Inställningar för Idrac**.

1. Välj **nätverk**.

   > [!NOTE]
   > Under installationen måste du konfigurera standard-iDRAC IP-adress och lösen ord som nämns i följande steg. Efter installationen ändrar du definitionerna.

1. Ändra den statiska IPv4-adressen till **10.100.100.250**.

1. Ändra den statiska nät masken till **255.255.255.0**.

   :::image type="content" source="media/tutorial-install-components/idrac-network-settings-screen-v2.png" alt-text="Skärm bild som visar den statiska nät masken.":::

1. Välj **tillbaka** till  >  **slutet**.

#### <a name="import-the-bios-configuration-file"></a>Importera konfigurations filen för BIOS

I den här artikeln beskrivs hur du konfigurerar BIOS med hjälp av konfigurations filen.

1. Anslut en dator med ett statiskt förkonfigurerat IP- **10.100.100.200** till **Idrac** -porten.

   :::image type="content" source="media/tutorial-install-components/idrac-port.png" alt-text="Skärm bild av den förkonfigurerade IP-adressen.":::

1. Öppna en webbläsare och ange **10.100.100.250** för att ansluta till Idrac-webbgränssnittet.

1. Logga in med Dells standard administratörs behörighet:

   - Användar namn: **rot**

   - Lösen ord: **Calvin**

1. Installationens autentiseringsuppgifter är:

   - Användar namn: **xxx**

   - Lösen ord: **xxx**

     Åtgärden Importera Server profil har initierats.

     > [!NOTE]
     > Innan du importerar filen måste du se till att:
     > - Du är den enda användare som för närvarande är ansluten till iDRAC.
     > - Systemet finns inte i BIOS-menyn.

1. Gå till konfigurations profil för **konfigurations**  >  **Server**. Ange följande parametrar:

   :::image type="content" source="media/tutorial-install-components/configuration-screen.png" alt-text="Skärm bild som visar konfigurationen av din server profil.":::

   | Parameter | Konfiguration |
   |--|--|
   | Typ av plats | Välj **lokal**. |
   | Fil Sök väg | Välj **Välj fil** och Lägg till konfigurations-XML-filen. |
   | Importera komponenter | Välj **BIOS, NIC, RAID**. |
   | Högsta väntetid | Välj **20 minuter**. |

1. Välj **Importera**.

1. Om du vill övervaka processen går du till **underhålls**  >  **jobb kön**.

   :::image type="content" source="media/tutorial-install-components/view-the-job-queue.png" alt-text="Skärm bild som visar jobbkö.":::

#### <a name="manually-configuring-bios"></a>Konfigurera BIOS manuellt 

Du måste konfigurera installations programmet för BIOS manuellt om:

- Du har inte köpt din apparat från pilen.

- Du har en-installation, men har inte åtkomst till XML-konfigurationsfilen.

När du har åtkomst till BIOS går du till **enhets inställningar**.

Konfigurera manuellt:

1. Få åtkomst till installationens BIOS direkt med hjälp av ett tangent bord och en skärm, eller Använd iDRAC.

   - Om installationen inte är en Defender för IoT-installation öppnar du en webbläsare och går till den IP-adress som har kon figurer ATS tidigare. Logga in med administratörs behörighet för Dell-administratörer. Använd **root** för användar namn och **Calvin** för lösen ordet.

   - Om installationen är en Defender för IoT-enhet loggar du in med **xxx** som användar namn och **xxx** för lösen ordet.

1. När du har åtkomst till BIOS går du till **enhets inställningar**.

1. Välj den RAID-styrda konfigurationen genom att välja **integrerad RAID-styrenhet 1: Dell PERC- \<PERC H330 Adapter\> konfigurationsverktyget**.

1. Välj **konfigurations hantering**.

1. Välj **Skapa virtuell disk**.

1. I fältet **Välj RAID-nivå** väljer du **RAID5**. I fältet **namn på virtuell disk** anger du **rot** och väljer **fysiska diskar**.

1. Välj Markera **alla** och välj sedan **tillämpa ändringar**

1. Välj **OK**.

1. Bläddra nedåt och välj **Skapa virtuell disk**.

1. Markera kryss rutan **Bekräfta** och välj **Ja**.

1. Välj **OK**.

1. Gå tillbaka till huvud skärmen och välj **system-BIOS**.

1. Välj **Start Inställningar**.

1. För alternativet **Start läge** väljer du **BIOS**.

1. Välj **tillbaka** och välj sedan **Slutför** för att avsluta BIOS-inställningarna.

### <a name="software-installation-dell-r340"></a>Program varu installation (Dell R340)

Installations processen tar cirka 20 minuter. Efter installationen startas systemet om flera gånger.

För att installera:

1. Kontrol lera att versions mediet är monterat på enheten på något av följande sätt:

   - Anslut den externa CD: n eller disken i en nyckel med versionen.

   - Montera ISO-avbildningen med hjälp av iDRAC. När du har loggat in på iDRAC väljer du den virtuella konsolen och väljer sedan **virtuella medier**.

1. I avsnittet **MAPPA CD/DVD** väljer du **Välj fil**.

1. Välj version ISO-avbildningsfilen för den här versionen i dialog rutan som öppnas.

1. Välj knappen **Mappa enhet** .

   :::image type="content" source="media/tutorial-install-components/mapped-device-on-virtual-media-screen-v2.png" alt-text="Skärm bild som visar en mappad enhet.":::

1. Mediet är monterat. Välj **Stäng**.

1. Starta enheten. När du använder iDRAC kan du starta om servrarna genom att välja knappen **konsulär kontroll** . I **tangent bords makrona** väljer du sedan knappen **tillämpa** som startar Ctrl + Alt + Delete-sekvensen.

1. Välj **engelska**.

1. Välj **sensor-release- \<version\> Enterprise**.

   :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Skärm bild som visar versions val.":::   

1. Definiera apparatens profil och nätverks egenskaper:

   :::image type="content" source="media/tutorial-install-components/appliance-profile-screen-v2.png" alt-text="Skärm bild som visar produkt profilen.":::   

   | Parameter | Konfiguration |
   |--|--|
   | **Maskinvaruprofil** | **Projektkostnad** |
   | **Hanterings gränssnitt** | **eno1** |
   | **Nätverks parametrar (tillhandahålls av kunden)** | - |
   |**IP-adress för hanterings nätverk:** | - |
   | **nätmask:** | - |
   | **datorns värdnamn:** | - |
   | **DNS** | - |
   | **IP-adress för standard-gateway:** | - |
   | **gränssnitt för indataport:** |  Systemet genererar en lista med indataportar åt dig. Om du vill spegla indataportarna kopierar du alla objekt som visas i listan med en tusentals avgränsare. Du behöver inte konfigurera brygga gränssnittet. Det här alternativet används endast för särskilda användnings fall. |

1. Efter 10 minuter visas de två uppsättningarna med autentiseringsuppgifter. En är för en **CyberX** -användare och en för en **support** användare.  

1. Spara installations-ID och lösen ord. Du behöver dessa autentiseringsuppgifter för att få åtkomst till plattformen första gången du använder den.

1. Fortsätt genom att välja **RETUR** .

## <a name="hpe-proliant-dl20-installation"></a>HPE DL20-installation

Den här artikeln beskriver installations processen för HPE-beroende DL20, som innehåller följande steg:

  - Aktivera fjärråtkomst och uppdatera standard administratörs lösen ordet.
  - Konfigurera BIOS-och RAID-inställningar.
  - Installera program varan.

### <a name="about-the-installation"></a>Om installationen

  - Enterprise-och SMB-enheter kan installeras. Installations processen är identisk för båda installations typerna, förutom för mat ris konfigurationen.
  - En administrativ användare som är standard. Vi rekommenderar att du ändrar lösen ordet under nätverks konfigurations processen.
  - Under nätverks konfigurations processen konfigurerar du iLO-porten på nätverks port 1.
  - Installations processen tar cirka 20 minuter. Efter installationen startas systemet om flera gånger.

### <a name="hpe-proliant-dl20-front-panel"></a>HPE DL20 Front Panel

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-front-panel-v2.png" alt-text="HPE DL20 front panel.":::

### <a name="hpe-proliant-dl20-back-panel"></a>HPE DL20 back panel

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-back-panel-v2.png" alt-text="Panelen på bak sidan av HPE DL20.":::

### <a name="enable-remote-access-and-update-the-password"></a>Aktivera fjärråtkomst och uppdatera lösen ordet

Använd följande procedur för att konfigurera nätverks alternativ och uppdatera standard lösen ordet.

Så här aktiverar och uppdaterar du lösen ordet:

1. Anslut en skärm och ett tangent bord till HP-installationen, sätt på enheten och tryck på **F9**.

    :::image type="content" source="media/tutorial-install-components/hpe-proliant-screen-v2.png" alt-text="Skärm bild som visar fönstret HPE.":::

1. Gå till **system verktyg**  >  **system konfiguration**  >  **ILO 5 konfigurations verktyg**  >  **nätverks alternativ**.

    :::image type="content" source="media/tutorial-install-components/system-configuration-window-v2.png" alt-text="Skärm bild som visar fönstret system konfiguration.":::

    1.  Välj **delad nätverks port – Lom** från fältet **nätverkskort för nätverks gränssnitt** .
    
    1.  Inaktivera DHCP.
    
    1.  Ange IP-adressen, nät masken och IP-adressen för gatewayen.

1. Välj **F10: Spara**.

1. Välj **ESC** för att återgå till **konfigurations verktyget för ILO 5** och välj sedan **användar hantering**.

1. Välj **Redigera/ta bort användare**. Administratören är den enda definierade standard användaren. 

1. Ändra standard lösen ordet och välj **F10: Spara**.

### <a name="configure-the-hpe-bios"></a>Konfigurera HPE BIOS

I följande procedur beskrivs hur du konfigurerar HPE BIOS för företags-och SMB-enheter.

Så här konfigurerar du HPE-BIOS:

1. Välj **system verktyg**  >  **system konfiguration**  >  **BIOS/plattforms konfiguration (RBSU)**.

1. Välj **Start alternativ** i **RBSU-formuläret (BIOS/Platform Configuration)** .

1. Ändra **Start läget** till **bakåtkompatibelt BIOS-läge** och välj sedan **F10: Spara**.

1. Välj **ESC** två gånger för att stänga formuläret **system konfiguration** .

#### <a name="for-the-enterprise-appliance"></a>För företags produkten

1. Välj **inbäddad RAID 1: HPE Smart Array P408i – en konfiguration av SR generation 10-**  >  **matris**  >  **skapa matris**.

1. I formuläret **skapa matris** väljer du alla alternativ. Tre alternativ är tillgängliga för **företags** enheten.

#### <a name="for-the-smb-appliance"></a>För SMB-installationen

1. Välj **inbäddad RAID 1: HPE Smart Array P208i – en konfiguration av SR generation 10-**  >  **matris**  >  **skapa matris**.

1. Välj **Fortsätt till nästa formulär**.

1. I formuläret **Ange RAID-nivå** ställer du in nivån på **RAID 5** för företags distributioner och **RAID 1** för SMB-distributioner.

1. Välj **Fortsätt till nästa formulär**.

1. I formuläret **etikett för logisk enhet** anger du **logisk enhet 1**.

1. Välj **skicka ändringar**.

1. I formuläret **Skicka** väljer **du tillbaka till huvud menyn**.

1. Välj **F10: Spara** och tryck sedan på **ESC** två gånger.

1. I fönstret **system verktyg** väljer du **Start menyn för en tidpunkt**.

1. I meny formuläret för **eng ång slö ren tid** väljer du **äldre BIOS-One-Time Start menyn**.

1. **Start i äldre** fönster och **Start åsidosättande** fönster visas. Välj ett alternativ för att åsidosätta datorn; till exempel för en CD-ROM-, USB-, HDD-eller UEFI-gränssnitt.

    :::image type="content" source="media/tutorial-install-components/boot-override-window-one-v2.png" alt-text="Skärm bild som visar det första fönstret för åsidosättning av omstart.":::

    :::image type="content" source="media/tutorial-install-components/boot-override-window-two-v2.png" alt-text="Skärm bild som visar fönstret andra start-åsidosättning.":::
### <a name="software-installation-hpe-proliant-dl20-appliance"></a>Program varu installation (HPE DL20-apparat)

Installations processen tar cirka 20 minuter. Efter installationen startas systemet om flera gånger.

Installera program varan:

1. Anslut skärmen och tangent bordet till enheten och Anslut sedan till CLI.

1. Anslut en extern CD eller disk i nyckeln med ISO-avbildningen som du laddade ned från sidan **uppdateringar** i Defender for IoT-portalen.

1. Starta enheten.

1. Välj **engelska**.

    :::image type="content" source="media/tutorial-install-components/select-english-screen.png" alt-text="Val av engelska i CLI-fönstret.":::

1. Välj **sensor-release- <version> Enterprise**.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Skärm bild av skärmen för att välja en version.":::

1. I installations guiden definierar du enhets profilen och nätverks egenskaperna:

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="Skärm bild som visar installations guiden.":::

    | Parameter | Konfiguration |
    | ----------| ------------- |
    | **Maskinvaruprofil** | Välj **Enterprise** eller **Office** för SMB-distributioner. |
    | **Hanterings gränssnitt** | **eno2** |
    | **Standard nätverks parametrar (vanligt vis parametrarna tillhandahålls av kunden)** | **IP-adress för hanterings nätverk:** <br/> <br/>**datorns värdnamn:** <br/>**DNS** <br/>**IP-adress för standard-gateway:**|
    | **gränssnitt för indataport:** | Systemet genererar en lista med indataportar åt dig.<br/><br/>Om du vill spegla indataportarna kopierar du alla objekt som visas i listan med en tusentals avgränsare: **eno5, eno3, eno1, eno6, eno4**<br/><br/>**För HPE-DL20: Visa inte eno1, enp1s0f4u4 (iLo-gränssnitt)**<br/><br/>**Brygga**: du behöver inte konfigurera brygga gränssnittet. Det här alternativet används endast för särskilda användnings fall. Tryck på **RETUR** för att fortsätta. |

1. Efter 10 minuter visas de två uppsättningarna med autentiseringsuppgifter. En är för en **CyberX** -användare och en för en **support** användare.

1. Spara installations programmets ID och lösen ord. Du behöver autentiseringsuppgifterna för att få åtkomst till plattformen för första gången.

1. Fortsätt genom att välja **RETUR** .

## <a name="hpe-proliant-dl360-installation"></a>HPE DL360-installation

  - En administrativ användare som är standard. Vi rekommenderar att du ändrar lösen ordet under nätverks konfigurationen.

  - Under nätverks konfigurationen konfigurerar du iLO-porten.

  - Installations processen tar cirka 20 minuter. Efter installationen startas systemet om flera gånger.

### <a name="hpe-proliant-dl360-front-panel"></a>HPE DL360 Front Panel

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-front-panel.png" alt-text="HPE DL360 front panel.":::

### <a name="hpe-proliant-dl360-back-panel"></a>HPE DL360 back panel

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-back-panel.png" alt-text="HPE DL360 back panel.":::

### <a name="enable-remote-access-and-update-the-password"></a>Aktivera fjärråtkomst och uppdatera lösen ordet

Se föregående avsnitt för HPE DL20-installation:

  - "Aktivera fjärråtkomst och uppdatera lösen ordet"

  - "Konfigurera HPE BIOS"

Företags konfigurationen är identisk.

> [!Note]
> I mat ris formuläret kontrollerar du att du väljer alla alternativ.

### <a name="ilo-remote-installation-from-a-virtual-drive"></a>Fjärran sluten iLO-installation (från en virtuell enhet)

Den här proceduren beskriver installationen av iLO från en virtuell enhet.

För att installera:

1. Logga in på den iLO-konsolen och högerklicka sedan på skärmen servrar.

1. Välj **HTML5-konsol**.

1. I-konsolen väljer du CD-ikonen och väljer alternativet CD/DVD.

1. Välj **lokal ISO-fil**.

1. I dialog rutan väljer du den relevanta ISO-filen.

1. Gå till vänster ikon, Välj **energi** och välj **Återställ**.

1. Installations processen startas om och körs.

### <a name="software-installation-hpe-dl360"></a>Program varu installation (HPE DL360)

Installations processen tar cirka 20 minuter. Efter installationen startas systemet om flera gånger.

För att installera:

1. Anslut skärmen och tangent bordet till enheten och Anslut sedan till CLI.

1. Anslut en extern CD eller disk i en nyckel med ISO-avbildningen som du laddade ned från sidan **uppdateringar** i Defender for IoT-portalen.

1. Starta enheten.

1. Välj **engelska**.

1. Välj **sensor-release- <version> Enterprise**.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Skärm bild som visar valet av version.":::

1. I installations guiden definierar du apparatens profil och nätverks egenskaper.

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="Skärm bild som visar installations guiden.":::

    | Parameter | Konfiguration |
    | ----------| ------------- |
    | **Maskinvaruprofil** | Välj **företag**. |
    | **Hanterings gränssnitt** | **eno2** |
    | **Standard nätverks parametrar (tillhandahålls av kunden)** | **IP-adress för hanterings nätverk:** <br/>**nätmask:** <br/>**datorns värdnamn:** <br/>**DNS** <br/>**IP-adress för standard-gateway:**|
    | **gränssnitt för indataport:**  | Systemet genererar en lista med indataportar åt dig.<br/><br/>Om du vill spegla indataportarna kopierar du alla objekt som visas i listan med en tusentals avgränsare.<br/><br/> Du behöver inte konfigurera brygga gränssnittet. Det här alternativet används endast för särskilda användnings fall. |

1. Efter 10 minuter visas de två uppsättningarna med autentiseringsuppgifter. En är för en **CyberX** -användare och en för en **support** användare.

1. Spara installations programmets ID och lösen ord. Du behöver dessa autentiseringsuppgifter för att få åtkomst till plattformen för första gången.

1. Fortsätt genom att välja **RETUR** .

## <a name="sensor-installation-for-the-virtual-appliance"></a>Sensor installation för den virtuella enheten

Du kan distribuera den virtuella datorn för Defender för IoT-sensorn i följande arkitekturer:


| Arkitektur | Specifikationer | Användning | Kommentarer |
|---|---|---|---|
| **Enterprise** | CPU: 8<br/>Minne: 32G RAM<br/>HÅRD DISK: 1800 GB | Produktions miljö | Standard och vanligaste |
| **Små företag** | CPU: 4 <br/>Minne: 8G RAM<br/>HÅRD DISK: 500 GB | Test miljöer eller små produktions miljöer | -  |
| **Office** | CPU: 4<br/>Minne: 8G RAM<br/>HÅRD DISK: 100 GB | Små test miljöer | -  |

### <a name="prerequisites"></a>Förutsättningar

Den lokala hanterings konsolen stöder distributions alternativen för VMware och Hyper-V. Kontrol lera att du har följande objekt innan du påbörjar installationen:

  - VMware (ESXi 5,5 eller senare) eller Hyper-V hypervisor (Windows 10 Pro eller Enterprise) installerat och fungerande

  - Tillgängliga maskin varu resurser för den virtuella datorn

  - ISO-installations fil för Azure Defender för IoT-sensor

Kontrol lera att hypervisorn körs.

### <a name="create-the-virtual-machine-esxi"></a>Skapa den virtuella datorn (ESXi)

1. Logga in på ESXi, Välj relevant **data lager** och välj **data lager läsare**.

1. **Ladda upp** avbildningen och välj **Stäng**.

1. Gå till **Virtual Machines** och välj **skapa/registrera VM**.

1. Välj **Skapa ny virtuell dator** och välj sedan **Nästa**.

1. Lägg till ett sensor namn och välj:

   - Kompatibilitet: **&lt; senaste ESXi- &gt; versionen**

   - Gäst operativ system familj: **Linux**

   - Gäst operativ system version: **Ubuntu Linux (64-bitars)**

1. Välj **Nästa**.

1. Välj relevant data lager och välj **Nästa**.

1. Ändra de virtuella maskin varu parametrarna enligt den arkitektur som krävs.

1. För **CD/DVD-enhet 1** väljer du **data lager ISO-fil** och väljer den ISO-fil som du överförde tidigare.

1. Välj **Nästa** > **Slutför**.

### <a name="create-the-virtual-machine-hyper-v"></a>Skapa den virtuella datorn (Hyper-V)

Den här proceduren beskriver hur du skapar en virtuell dator med hjälp av Hyper-V.

Så här skapar du en virtuell dator:

1. Skapa en virtuell disk i Hyper-V Manager.

1. Välj **format = VHDX**.

1. Välj **typ = dynamisk expandering**.

1. Ange namn och plats för den virtuella hård disken.

1. Ange den storlek som krävs (enligt arkitekturen).   

1. Granska sammanfattningen och välj **Slutför**.

1. På menyn **åtgärder** skapar du en ny virtuell dator.

1. Ange ett namn för den virtuella datorn.

1. Välj **Ange generations**  >  **generation 1**.

1. Ange minnesallokering (enligt arkitekturen) och markera kryss rutan för dynamiskt minne.

1. Konfigurera nätverks korts kortet enligt Server nätverk sto pol Ogin.

1. Anslut VHDX som skapades tidigare till den virtuella datorn.

1. Granska sammanfattningen och välj **Slutför**.

1. Högerklicka på den nya virtuella datorn och välj **Inställningar**.

1. Välj **Lägg till maskin vara** och Lägg till ett nytt nätverkskort.

1. Välj den virtuella växel som ska anslutas till sensor hanterings nätverket.

1. Allokera processor resurser (enligt arkitekturen).

1. Anslut hanterings konsolens ISO-avbildning till en virtuell DVD-enhet.

1. Starta den virtuella datorn.

2. På menyn **åtgärder** väljer du **Anslut** för att fortsätta program varu installationen.

### <a name="software-installation-esxi-and-hyper-v"></a>Program varu installation (ESXi och Hyper-V)

I det här avsnittet beskrivs ESXi-och Hyper-V-programinstallationen.

För att installera:

1. Öppna konsolen för virtuella datorer.

1. Den virtuella datorn kommer att starta från ISO-avbildningen och skärmen val av språk visas. Välj **engelska**.

1. Välj den arkitektur som krävs.

1. Definiera apparatens profil och nätverks egenskaper:

    | Parameter | Konfiguration |
    | ----------| ------------- |
    | **Maskinvaruprofil** | &lt;nödvändig arkitektur&gt; |
    | **Hanterings gränssnitt** | **ens192** |
    | **Nätverks parametrar (tillhandahålls av kunden)** | **IP-adress för hanterings nätverk:** <br/>**nätmask:** <br/>**datorns värdnamn:** <br/>**DNS** <br/>**standardgateway:** <br/>**gränssnitt för indataport:**|
    | **bro gränssnitt:** | Du behöver inte konfigurera brygga gränssnittet. Det här alternativet är endast för särskilda användnings fall. |

1. Godkänn inställningarna genom att ange **Y** .

1. Inloggnings uppgifter genereras och presenteras automatiskt. Kopiera användar namnet och lösen ordet på en säker plats, eftersom de krävs för inloggning och administration.

      - **Support**: den administrativa användaren för användar hantering.

      - **CyberX**: motsvarigheten till roten för att komma åt enheten.

1. Enheten startas om.

1. Få åtkomst till hanterings konsolen via IP-adressen som du konfigurerade tidigare: `https://ip_address` .

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Skärm bild som visar åtkomst till hanterings konsolen.":::

## <a name="virtual-appliance-on-premises-management-console-installation"></a>Virtuell installation: installation av lokal hanterings konsol

Den lokala hanterings konsolens virtuella dator stöder följande arkitekturer:

| Arkitektur | Specifikationer | Användning | 
|--|--|--|
| Stora företag <br/>(Standard och vanligaste) | CPU: 8 <br/>Minne: 32G RAM<br/> HÅRD DISK: 1,8 TB | Stora produktions miljöer | 
| Stora företag | CPU: 4 <br/> Minne: 8G RAM<br/> HÅRD DISK: 500 GB | Stora produktions miljöer |
| Stora företag | CPU: 4 <br/>Minne: 8G RAM <br/> HÅRD DISK: 100 GB | Små test miljöer | 
   
### <a name="prerequisites"></a>Förutsättningar

Den lokala hanterings konsolen stöder distributions alternativen för VMware och Hyper-V. Innan du påbörjar installationen kontrollerar du följande:

- VMware (ESXi 5,5 eller senare) eller Hyper-V hypervisor (Windows 10 Pro eller Enterprise) är installerat och fungerar.

- Maskin varu resurserna är tillgängliga för den virtuella datorn.

- Du har ISO-installationsprogrammet för den lokala hanterings konsolen.
    
- Hypervisor-körningen körs.

### <a name="create-the-virtual-machine-esxi"></a>Skapa den virtuella datorn (ESXi)

Till en virtuell dator för att skapa en virtuell dator (ESXi):

1. Logga in på ESXi, Välj relevant **data lager** och välj **data lager läsare**.

1. Ladda upp avbildningen och välj **Stäng**.

1. Gå till **Virtual Machines**.

1. Välj **skapa/registrera virtuell dator**.

1. Välj **Skapa ny virtuell dator** och välj **Nästa**.

1. Lägg till ett sensor namn och välj:

   - Kompatibilitetsläget \<latest ESXi version>

   - Gäst operativ system familj: Linux

   - Gäst operativ system version: Ubuntu Linux (64-bitars)

1. Välj **Nästa**.

1. Välj relevant data lager och välj **Nästa**.

1. Ändra de virtuella maskin varu parametrarna enligt den arkitektur som krävs.

1. För **CD/DVD-enhet 1** väljer du **data lager ISO-fil** och väljer den ISO-fil som du överförde tidigare.

1. Välj **Nästa** > **Slutför**.

### <a name="create-the-virtual-machine-hyper-v"></a>Skapa den virtuella datorn (Hyper-V)

Så här skapar du en virtuell dator med hjälp av Hyper-V:

1. Skapa en virtuell disk i Hyper-V Manager.

1. Välj formatet **VHDX**.

1. Välj **Nästa**.

1. Välj typ **dynamisk expandering**.

1. Välj **Nästa**.

1. Ange namn och plats för den virtuella hård disken.

1. Välj **Nästa**.

1. Ange den storlek som krävs (enligt arkitekturen).

1. Välj **Nästa**.

1. Granska sammanfattningen och välj **Slutför**.

1. På menyn **åtgärder** skapar du en ny virtuell dator.

1. Välj **Nästa**.

1. Ange ett namn för den virtuella datorn.

1. Välj **Nästa**.

1. Välj **generation** och Ställ in den på **generation 1**.

1. Välj **Nästa**.

1. Ange minnesallokering (enligt arkitekturen) och markera kryss rutan för dynamiskt minne.

1. Välj **Nästa**.

1. Konfigurera nätverks korts kortet enligt Server nätverk sto pol Ogin.

1. Välj **Nästa**.

1. Anslut VHDX som skapades tidigare till den virtuella datorn.

1. Välj **Nästa**.

1. Granska sammanfattningen och välj **Slutför**.

1. Högerklicka på den nya virtuella datorn och välj sedan **Inställningar**.

1. Välj **Lägg till maskin vara** och Lägg till ett nytt **kort för nätverkskort**.

1. För **virtuell växel** väljer du den växel som ska anslutas till sensor hanterings nätverket.

1. Allokera processor resurser (enligt arkitekturen).

1. Anslut hanterings konsolens ISO-avbildning till en virtuell DVD-enhet.

1. Starta den virtuella datorn.

1. På menyn **åtgärder** väljer du **Anslut** för att fortsätta program varu installationen.

### <a name="software-installation-esxi-and-hyper-v"></a>Program varu installation (ESXi och Hyper-V)

Om du startar den virtuella datorn startas installations processen från ISO-avbildningen. Du kan förbättra säkerheten genom att skapa ett andra nätverks gränssnitt i den lokala hanterings konsolen. Ett nätverks gränssnitt är dedikerat för dina användare och har stöd för konfigurationen av en gateway för dirigerade nätverk. Det andra nätverks gränssnittet är dedicerat till alla anslutna sensorer inom ett IP-adressintervall.

Både nätverks gränssnitten har användar gränssnittet aktiverat och alla funktioner som stöds av användar gränssnittet är tillgängliga i det sekundära nätverks gränssnittet när routning inte behövs. Hög tillgänglighet kommer att köras i det sekundära nätverks gränssnittet.

Om du väljer att inte distribuera ett sekundärt nätverks gränssnitt kommer alla funktioner att vara tillgängliga via det primära nätverks gränssnittet. 

Installera program varan:

1. Välj **engelska**.

1. Välj den arkitektur som krävs för din distribution.

1. Definiera nätverks gränssnittet för sensor hanterings nätverket: gränssnitt, IP, undernät, DNS-server och standard-gateway.

1. Valfritt Lägg till ett andra nätverks gränssnitt i den lokala hanterings konsolen.

    1. `Please type sensor monitoring interface (Optional. Applicable when sensors are on a different network segment. For more information see the Installation instructions): <name of interface>`
    
    1. `Please type an IP address for the sensor monitoring interface (accessible by the sensors): <ip address>`
    
    1. `Please type a subnet mask for the sensor monitoring interface (accessible by the sensors): <subnet>`

1. Inloggnings uppgifter genereras och presenteras automatiskt. Behåll dessa autentiseringsuppgifter på en säker plats, eftersom de krävs för inloggning och administration.

    | Användarnamn | Beskrivning |
    |--|--|
    | Support | Den administrativa användaren för användar hantering. |
    | CyberX | Motsvarigheten till roten för att komma åt enheten. |

1. Enheten startas om.

1. Få åtkomst till hanterings konsolen via IP-adressen som du konfigurerade tidigare: `<https://ip_address>` .

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-management-console-sign-in-screen.png" alt-text="Skärm bild som visar hanterings konsolens inloggnings skärm.":::

## <a name="post-installation-validation"></a>Verifiering efter installationen

Om du vill verifiera installationen av en fysisk installation måste du utföra många tester. Samma validerings process gäller för alla typer av utrustning.

Utför verifieringen med hjälp av GUI eller CLI. Verifieringen är tillgänglig för användar **supporten** och användaren **CyberX**.

Verifieringen efter installationen måste innehålla följande test:

  - **Sanity-test**: kontrol lera att systemet körs.

  - **Version**: kontrol lera att versionen är korrekt.

  - **ifconfig**: kontrol lera att alla indataportar som kon figurer ATS under installations processen körs.

### <a name="checking-system-health-by-using-the-gui"></a>Kontrollerar system hälsan med hjälp av det grafiska användar gränssnittet

:::image type="content" source="media/tutorial-install-components/system-health-check-screen.png" alt-text="Skärm bild som visar system hälso kontrollen.":::

#### <a name="sanity"></a>Sanity

- Installation av **utrustning**: kör Sanity-kontrollen. Du kan utföra samma kontroll genom att använda CLI-kommandot `system-sanity` .

- **Version**: visar version av versionen.

- **Nätverks egenskaper**: visar sensor nätverks parametrarna.

#### <a name="redis"></a>Redis

- **Minne**: visar den övergripande bilden av minnes användningen, till exempel hur mycket minne som använts och hur mycket som låg.

- **Längsta nyckel**: visar de längsta nycklar som kan orsaka omfattande minnes användning.

#### <a name="system"></a>System

- **Kärn logg**: innehåller de sista 500 raderna i kärn loggen, så att du kan visa de senaste logg raderna utan att exportera hela system loggen.

- **Aktivitets hanteraren**: översätter de uppgifter som visas i tabellen med processer till följande skikt: 
  
  - Beständigt lager (Redis) 
  - Kassa skikt (SQL)

- **Nätverks statistik**: visar din nätverks statistik.

- **Överkant**: visar en tabell med processer. Det är ett Linux-kommando som tillhandahåller en dynamisk real tids visning av det operativ system som körs.

- **Kontroll av säkerhets kopierings minne**: anger säkerhets kopierings minnets status, och kontrollerar följande:
  - Platsen för säkerhetskopieringsmappen 
  - Storleken på säkerhetskopieringsmappen
  - Begränsningar för säkerhetskopieringsmappen
  - När den senaste säkerhets kopieringen skedde
  - Hur mycket utrymme det finns för de extra säkerhetskopieringsfilerna

- **ifconfig**: visar parametrarna för apparatens fysiska gränssnitt.

- **CyberX ämta**: visar nätverks trafik och bandbredd med hjälp av de sex andra testerna.

- **Fel från kärna, log**: visar fel från kärn logg filen.

För att få åtkomst till verktyget:

1. Logga in på sensorn med **support** användarens autentiseringsuppgifter.

1. Välj **system statistik** i fönstret **system inställningar** .

    :::image type="icon" source="media/tutorial-install-components/system-statistics-icon.png" border="false":::

### <a name="checking-system-health-by-using-the-cli"></a>Kontrol lera systemets hälso tillstånd med hjälp av CLI

**Test 1: Sanity**

Kontrol lera att systemet är igång:

1. Anslut till CLI med Linux-terminalen (till exempel SparaTillFil) och användar **supporten**.

1. Ange `system sanity`.

1. Kontrol lera att alla tjänster är gröna (körs).

    :::image type="content" source="media/tutorial-install-components/support-screen.png" alt-text="Skärm bild som visar att tjänsterna körs.":::

1. Kontrol lera att **systemet är igång! (prod)** visas längst ned.

**Test 2: versions kontroll**

Kontrol lera att rätt version används:

1. Anslut till CLI med Linux-terminalen (till exempel SparaTillFil) och användar **supporten**.

1. Ange `system version`.

1. Kontrol lera att rätt version visas.

**Test 3: nätverks verifiering**

Kontrol lera att alla indataporter som kon figurer ATS under installations processen körs:

1. Anslut till CLI med Linux-terminalen (till exempel SparaTillFil) och användar **supporten**.

1. Ange `network list` (motsvarande Linux `ifconfig` -kommando).

1. Verifiera att de nödvändiga gränssnitten visas. Om exempelvis två nätverkskort med fyra koppar är installerade, bör det finnas 10 gränssnitt i listan.

    :::image type="content" source="media/tutorial-install-components/interface-list-screen.png" alt-text="Skärm bild som visar listan över gränssnitt.":::

**Test 4: hanterings åtkomst till användar gränssnittet**

Kontrol lera att du har åtkomst till konsolens webb gränssnitt:

1. Anslut en bärbar dator med en Ethernet-kabel till hanterings porten (**GB1**).

1. Definiera den bärbara datorns NIC-adress i samma intervall som enheten.

    :::image type="content" source="media/tutorial-install-components/access-to-ui.png" alt-text="Skärm bild som visar hanterings åtkomst till användar gränssnittet.":::

1. Pinga enhetens IP-adress från den bärbara datorn för att verifiera anslutningen (standard: 10.100.10.1).

1. Öppna Chrome-webbläsaren på den bärbara datorn och ange enhetens IP-adress.

1. I fönstret **anslutningen är inte privat** väljer du **Avancerat** och fortsätter.

1. Testet lyckas när inloggnings skärmen för Defender för IoT visas.

   :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Skärm bild som visar åtkomst till hanterings konsolen.":::

## <a name="troubleshooting"></a>Felsökning

### <a name="you-cant-connect-by-using-a-web-interface"></a>Du kan inte ansluta med ett webb gränssnitt

1. Kontrol lera att datorn du försöker ansluta är i samma nätverk som installationen.

1. Kontrol lera att GUI-nätverket är anslutet till hanterings porten.

1. Pinga produktens IP-adress. Om det inte finns något ping:

   1. Anslut en övervakare och ett tangent bord till-enheten.

   1. Använd **support** användaren och lösen ordet för att logga in.

   1. Använd kommandot `network list` för att se den aktuella IP-adressen.

      :::image type="content" source="media/tutorial-install-components/network-list.png" alt-text="Skärm bild som visar listan över nätverk.":::

1. Om nätverks parametrarna är felkonfigurerade kan du använda följande procedur för att ändra dem:

   1. Använd kommandot `network edit-settings` .

   1. Om du vill ändra IP-adressen för hanterings nätverket väljer du **Y**.

   1. Om du vill ändra nät masken väljer du **Y**.

   1. Om du vill ändra DNS väljer du **Y**.

   1. Om du vill ändra IP-adress för standard-gateway väljer du **Y**.

   1. Välj **N** för att ändra gränssnitt för indataport (endast sensor).

   1. Om du vill tillämpa inställningarna väljer du **Y**.

1. När du har startat om ansluter du till support användarens autentiseringsuppgifter och använder `network list` kommandot för att kontrol lera att parametrarna har ändrats.

1. Försök att pinga och ansluta från det grafiska användar gränssnittet igen.

### <a name="the-appliance-isnt-responding"></a>Enheten svarar inte

1. Anslut en bildskärm och ett tangent bord till enheten eller Använd SparaTillFil för att fjärrans luta till CLI.

1. Använd **support** användarens autentiseringsuppgifter för att logga in.

1. Använd `system sanity` kommandot och kontrol lera att alla processer körs.

    :::image type="content" source="media/tutorial-install-components/system-sanity-screen.png" alt-text="Skärm bild som visar kommandot system Sanity.":::

Kontakta [Microsoft Support](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)om du har problem.

## <a name="appendix-a-mirroring-port-on-vswitch-esxi"></a>Bilaga A: speglande port på vSwitch (ESXi)

### <a name="configure-a-span-port-on-an-existing-vswitch"></a>Konfigurera en SPAN-port på en befintlig vSwitch

En vSwitch har inte funktioner för spegling, men du kan använda en lösning för att implementera en intervall port.

Så här konfigurerar du en SPAN-port:

1. Öppna egenskaper för vSwitch.

1. Välj **Lägg till**.

1. Välj den **virtuella datorn**  >  **Nästa**.

1. Sätt in en nätverks etikett **span Network**, Välj **VLAN-ID**  >  **alla** och välj sedan **Nästa**.

1. Välj **Slutför**.

1. Välj **span Network** > **Edit*.

1. Välj **säkerhet** och kontrol lera att principen för **läge för läge** är inställd på **Accept** läge.

1. Välj **OK** och välj sedan **Stäng** för att stänga egenskaperna för vSwitch.

1. Öppna egenskaperna för den **virtuella XSense-datorn** .

1. För **nätverkskort 2** väljer du **span** -nätverket.

1. Välj **OK**.

1. Anslut till sensorn och kontrol lera att spegling fungerar.

## <a name="appendix-b-access-sensors-from-the-on-premises-management-console"></a>Bilaga B: få åtkomst till sensorer från den lokala hanterings konsolen

Du kan förbättra systemsäkerheten genom att förhindra direkt användar åtkomst till sensorn. Använd i stället proxy-tunnlar för att låta användarna komma åt sensorn från den lokala hanterings konsolen med en enda brand Väggs regel. Den här tekniken begränsar risken för obehörig åtkomst till nätverks miljön utöver sensorn. Användarens upplevelse när du loggar in på sensorn förblir densamma.

:::image type="content" source="media/tutorial-install-components/sensor-system-graph.png" alt-text="Skärm bild som visar åtkomst till sensorn.":::

Så här aktiverar du tunnel trafik:

1. Logga in på den lokala hanterings konsolens CLI med **CyberX** eller ge **support** för användarens autentiseringsuppgifter.

1. Ange `sudo cyberx-management-tunnel-enable`.

1. Välj **Retur**.

1. Ange `--port 10000`.

### <a name="next-steps"></a>Nästa steg

[Konfigurera ditt nätverk](how-to-set-up-your-network.md)