---
title: Defender for IoT-installation
description: Lär dig hur du installerar en sensor och den lokala hanteringskonsolen för Azure Defender for IoT.
ms.date: 4/20/2021
ms.topic: how-to
ms.openlocfilehash: e8366a3408e64d95e6c4d50e3ddef84309b4e8e5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829741"
---
# <a name="defender-for-iot-installation"></a>Defender for IoT-installation

Den här artikeln beskriver hur du installerar följande element i Azure Defender for IoT:

- **Sensor:** Defender for IoT-sensorer samlar in ICS-nätverkstrafik med hjälp av passiv (agentlös) övervakning. Passiva och icke-inintrusiva har sensorerna ingen inverkan på OT- och IoT-nätverk och -enheter. Sensorn ansluter till en SPAN-port eller nätverks-TAP och börjar omedelbart övervaka nätverket. Identifieringar visas i sensorkonsolen. Där kan du visa, undersöka och analysera dem i en nätverkskarta, enhetsinventering och en omfattande mängd rapporter. Exempel är riskbedömningsrapporter, datautvinningsfrågor och attackvektorer. Läs mer om sensorfunktioner i [användarhandboken för Defender for IoT-sensor (direktnedladdning).](./getting-started.md)

- **Lokal hanteringskonsol:** Med den lokala hanteringskonsolen kan du utföra enhetshantering, riskhantering och sårbarhetshantering. Du kan också använda den för att utföra hotövervakning och incidenter i hela företaget. Den ger en enhetlig vy över alla nätverksenheter, viktiga IoT- och OT-riskindikatorer och aviseringar som identifierats i anläggningar där sensorer distribueras. Använd den lokala hanteringskonsolen för att visa och hantera sensorer i trådlösa nätverk.

Den här artikeln beskriver följande installationsinformation:

  - **Maskinvara:** Information om den fysiska Dell- och HPE-installationen.

  - **Programvara:** Installation av programvara för sensor och lokal hanteringskonsol.

  - **Virtuella installationer:** Information om virtuella datorer och programvaruinstallation.

Efter installationen ansluter du sensorn till nätverket.

## <a name="about-defender-for-iot-appliances"></a>Om Defender för IoT-installationer 

Följande avsnitt innehåller information om Defender for IoT-sensorinstallationer och installationen för den lokala hanteringskonsolen för Defender for IoT.

### <a name="physical-appliances"></a>Fysiska installationer

Defender for IoT-enhetens sensor ansluter till en SPAN-port eller nätverks-TAP och börjar omedelbart samla in ICS-nätverkstrafik med hjälp av passiv (agentlös) övervakning. Den här processen har ingen inverkan på OT-nätverk och enheter eftersom den inte placeras i datasökvägen och inte aktivt genomsöker OT-enheter.

Följande rackmonteringsutrustning är tillgängliga:

| **Distributionstyp** | **Företag** | **Enterprise** | **SMB** | **Linje** |
|--|--|--|--|--|
| **Modell** | HPE ProLiant DL360 | Dell PowerEdge R340 XL | HPE ProLiant DL20 | HPE ProLiant DL20 |
| **Övervaka portar** | upp till 15 RJ45 eller 8 OPT | upp till 9 RJ45 eller 6 OPT | upp till 8 RJ45 eller 6 OPT | 4 RJ45 |
| **Maximal bandbredd\*** | 3 Gb/sek | 1 Gb/sek | 1 Gb/sek | 100 MB/s |
| **Maximalt antal skyddade enheter** | 30,000 | 10 000 | 15 000 | 1 000 |

*Maximal bandbreddskapacitet kan variera beroende på protokolldistribution.

### <a name="virtual-appliances"></a>Virtuella installationer

Följande virtuella installationer är tillgängliga:

| **Distributionstyp** | **Enterprise** | **SMB** | **Linje** |
|--|--|--|--|
| **Beskrivning** | Virtuell installation för företagsdistributioner | Virtuell installation för SMB-distributioner | Virtuell installation för raddistributioner |
| **Maximal bandbredd\*** | 150 MB/s | 15 MB/s | 3 MB/s |
| **Maximalt antal skyddade enheter** | 3 000 | 300 | 100 |
| **Distributionstyp** | Stora företag | SMB | Linje |
| **Beskrivning** | Virtuell installation för företagsdistributioner | Virtuell installation för SMB-distributioner | Virtuell installation för raddistributioner |

*Maximal bandbreddskapacitet kan variera beroende på protokolldistribution.

### <a name="hardware-specifications-for-the-on-premises-management-console"></a>Maskinvaruspecifikationer för den lokala hanteringskonsolen

 | Objekt | Beskrivning |
 |----|--|
 **Beskrivning** | I en arkitektur med flera platser ger den lokala hanteringskonsolen synlighet och kontroll över geografiskt distribuerade platser. Den integreras med SOC-säkerhetsstackar, inklusive SIEM,biljettsystem, nästa generations brandväggar, säkra fjärråtkomstplattformar och sandbox-miljön för Defender for IoT ICS. |
 **Distributionstyp** | Stora företag |
 **Typ av installation**  | Dell R340, VM |
 **Antal hanterade sensorer** | Obegränsat |

## <a name="prepare-for-the-installation"></a>Förbereda för installationen

### <a name="access-the-iso-installation-image"></a>Få åtkomst till ISO-installationsavbildningen

Installationsavbildningen är tillgänglig från Defender for IoT-portalen.

Så här kommer du åt filen:

1. Logga in på ditt Defender for IoT-konto.

1. Gå till sidan **Nätverkssensor** eller **Lokal hanteringskonsol och** välj en version att ladda ned.

### <a name="install-from-dvd"></a>Installera från DVD

Kontrollera att du har följande innan du installerar:

- En bärbar DVD-enhet med USB-anslutningen.

- En ISO-installationsavbildning.

Så här installerar du:

1. Burn the image to a DVD or prepare a disk on a key. Anslut en bärbar DVD-enhet till datorn, högerklicka på ISO-avbildningen och välj **Burn to disk (Burn to disk).**

1. Anslut DVD:n eller disken på en nyckel och konfigurera enheten så att den startar från EN DVD eller disk på en nyckel.

### <a name="install-from-disk-on-a-key"></a>Installera från disk på en nyckel

Kontrollera att du har följande innan du installerar:

  - Rufus installerat.
  
  - En disk på nyckeln med USB-version 3.0 och senare. Den minsta storleken är 4 GB.

  - En ISO-installationsavbildningsfil.

Disken på en nyckel raderas i den här processen.

Så här förbereder du en disk på en nyckel:

1. Kör Rufus och välj **SENSOR ISO.**

1. Anslut disken på en nyckel till frontpanelen.

1. Ställ in SERVERNs BIOS så att det startar från USB-enheten.

## <a name="dell-poweredger340xl-installation"></a>Dell PowerEdgeR340XL-installation

Innan du installerar programvaran på Dell-installationen måste du justera installationens BIOS-konfiguration:

  - [Dell PowerEdge R340 Front Panel](#dell-poweredge-r340-front-panel) och [Dell PowerEdge R340 Back Panel](#dell-poweredge-r340-back-panel) innehåller beskrivningen av front- och bakpaneler, tillsammans med information som krävs för installation, till exempel drivrutiner och portar.

  - [Dell BIOS-konfiguration](#dell-bios-configuration) innehåller information om hur du ansluter till Dells gränssnitt för installationshantering och konfigurerar BIOS.

  - [Programvaruinstallation (Dell R340)](#software-installation-dell-r340) beskriver den procedur som krävs för att installera programvaran för Defender for IoT-sensorn.

### <a name="dell-poweredge-r340xl-requirements"></a>Dell PowerEdge R340XL-krav 

Om du vill installera Dell PowerEdge R340XL-installationen behöver du:

- Enterprise-licens för Dell Remote Access Controller (iDrac)

- XML för BIOS-konfiguration

- Versioner av inbyggd programvara för server:

  - BIOS version 2.1.6

  - iDrac version 3.23.23.23

### <a name="dell-poweredge-r340-front-panel"></a>Dell PowerEdge R340-frontpanelen

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-front-panel.jpg" alt-text="Dell PowerEdge R340-frontpanelen.":::

 1. Vänster kontrollpanel 
 1. Optisk enhet (valfritt) 
 1. Höger kontrollpanel 
 1. Informationstagg 
 1. Enheter  

### <a name="dell-poweredge-r340-back-panel"></a>Dell PowerEdge R340-bakpanel

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-back-panel.jpg" alt-text="Dell PowerEdge R340-bakpanelen.":::

1. Serieport 
1. NIC-port (Gb 1) 
1. NIC-port (Gb 1) 
1. PCIe med halv höjd 
1. PCIe-kortplats för PCIe-expansion i full höjd 
1. Strömförsörjningsenhet 1 
1. Strömförsörjningsenhet 2 
1. Systemidentifiering 
1. Knappen Kabelport för systemstatusindikator (CMA) 
1. USB 3.0-port (2) 
1. Dedikerad iDRAC9-nätverksport 
1. VGA-port 

### <a name="dell-bios-configuration"></a>Dell BIOS-konfiguration

Dell BIOS-konfiguration krävs för att justera Dell-installationen så att den fungerar med programvaran.

BIOS-konfigurationen utförs via en fördefinierad konfiguration. Filen är tillgänglig från [hjälpcentret.](https://help.cyberx-labs.com/)

Importera konfigurationsfilen till Dell-installationen. Innan du använder konfigurationsfilen måste du upprätta kommunikationen mellan Dell-installationen och hanteringsdatorn.

Dell-installationen hanteras av en integrerad iDRAC med livscykelkontrollant (LC). LC är inbäddad i varje Dell PowerEdge-server och innehåller funktioner som hjälper dig att distribuera, uppdatera, övervaka och underhålla dina Dell PowerEdge-apparater.

För att upprätta kommunikationen mellan Dell-installationen och hanteringsdatorn måste du definiera iDRAC-IP-adressen och hanteringsdatorns IP-adress i samma undernät.

När anslutningen har upprättats kan BIOS konfigureras.

Så här konfigurerar du Dell BIOS:

1. [Konfigurera IP-adressen för iDRAC](#configure-idrac-ip-address)

1. [Importera BIOS-konfigurationsfilen](#import-the-bios-configuration-file)

#### <a name="configure-idrac-ip-address"></a>Konfigurera IP-adress för iDRAC

1. Ström till sensorn.

1. Om operativsystemet redan är installerat väljer du F2-tangenten för att ange BIOS-konfigurationen.

1. Välj **iDRAC-inställningar.**

1. Välj **Nätverk**.

   > [!NOTE]
   > Under installationen måste du konfigurera standard-IP-adressen och lösenordet för iDRAC som anges i följande steg. Efter installationen ändrar du dessa definitioner.

1. Ändra den statiska IPv4-adressen **till 10.100.100.250**.

1. Ändra den statiska nätmasken till **255.255.255.0**.

   :::image type="content" source="media/tutorial-install-components/idrac-network-settings-screen-v2.png" alt-text="Skärmbild som visar den statiska nätmasken.":::

1. Välj **Back** Finish  >  **(Tillbaka slutför).**

#### <a name="import-the-bios-configuration-file"></a>Importera BIOS-konfigurationsfilen

I den här artikeln beskrivs hur du konfigurerar BIOS med hjälp av konfigurationsfilen.

1. Anslut en dator med en statisk förkonfigurerad **IP-adress 10.100.100.200** **till iDRAC-porten.**

   :::image type="content" source="media/tutorial-install-components/idrac-port.png" alt-text="Skärmbild av den förkonfigurerade IP-adressporten.":::

1. Öppna en webbläsare och ange **10.100.100.250** för att ansluta till iDRAC-webbgränssnittet.

1. Logga in med Dells administratörsbehörighet som standard:

   - Användarnamn: **rot**

   - Lösenord: **sn**

1. Installationens autentiseringsuppgifter är:

   - Användarnamn: **XXX**

   - Lösenord: **XXX**

     Importserverprofilåtgärden initieras.

     > [!NOTE]
     > Kontrollera följande innan du importerar filen:
     > - Du är den enda användaren som för närvarande är ansluten till iDRAC.
     > - Systemet finns inte i BIOS-menyn.

1. Gå till  >  **Konfigurationsserverkonfigurationsprofil**. Ange följande parametrar:

   :::image type="content" source="media/tutorial-install-components/configuration-screen.png" alt-text="Skärmbild som visar konfigurationen av din serverprofil.":::

   | Parameter | Konfiguration |
   |--|--|
   | Platstyp | Välj **Lokal**. |
   | Filsökväg | Välj **Välj fil och** lägg till XML-konfigurationsfilen. |
   | Importera komponenter | Välj **BIOS, NIC, RAID**. |
   | Högsta väntetid | Välj **20 minuter**. |

1. Välj **Importera**.

1. Om du vill övervaka processen går du till  >  **Underhållsjobbkö**.

   :::image type="content" source="media/tutorial-install-components/view-the-job-queue.png" alt-text="Skärmbild som visar Jobbkö.":::

#### <a name="manually-configuring-bios"></a>Konfigurera BIOS manuellt 

Du måste konfigurera installationens BIOS manuellt om:

- Du har inte köpt enheten från Arrow.

- Du har en apparat, men har inte åtkomst till XML-konfigurationsfilen.

När du har åtkomst till BIOS går du till **Enhetsinställningar.**

Så här konfigurerar du manuellt:

1. Få åtkomst till installationens BIOS direkt med hjälp av ett tangentbord och en skärm, eller använd iDRAC.

   - Om installationen inte är en Defender for IoT-installation öppnar du en webbläsare och går till den IP-adress som konfigurerades tidigare. Logga in med Dells administratörsbehörighet som standard. Använd **root** som användarnamn och **sing** för lösenordet.

   - Om installationen är en Defender for IoT-installation loggar du in med **XXX** som användarnamn och **XXX** som lösenord.

1. När du har åtkomst till BIOS går du **till Enhetsinställningar.**

1. Välj den RAID-kontrollerade konfigurationen genom att välja **Integrated RAID controller 1: Dell PERC \<PERC H330 Adapter\> Configuration Utility**.

1. Välj **Konfigurationshantering.**

1. Välj **Skapa virtuell disk.**

1. I fältet **Välj RAID-nivå** väljer du **RAID5.** I fältet **Namn på virtuell** disk anger du **ROOT** och väljer **Fysiska diskar.**

1. Välj **Kontrollera alla** och välj sedan Tillämpa **ändringar**

1. Välj **OK**.

1. Rulla nedåt och välj **Skapa virtuell disk.**

1. Markera kryssrutan **Bekräfta** och välj **Ja.**

1. Välj **OK**.

1. Gå tillbaka till huvudskärmen och välj **System BIOS**.

1. Välj **Startinställningar.**

1. För alternativet **Startläge** väljer du **BIOS**.

1. Välj **Tillbaka** och välj sedan Slutför **för** att avsluta BIOS-inställningarna.

### <a name="software-installation-dell-r340"></a>Programvaruinstallation (Dell R340)

Installationsprocessen tar cirka 20 minuter. Efter installationen startas systemet om flera gånger.

Så här installerar du:

1. Kontrollera att versionsmediet är monterat på apparaten på något av följande sätt:

   - Anslut den externa CD:n eller disken på en nyckel med versionen.

   - Montera ISO-avbildningen med hjälp av iDRAC. När du har loggat in på iDRAC väljer du den virtuella konsolen och sedan **Virtuell media.**

1. I avsnittet **Map CD/DVD (Mappa CD/DVD)** **väljer du Choose File (Välj fil).**

1. Välj ISO-avbildningsfil för den här versionen i dialogrutan som öppnas.

1. Välj knappen **Mappa** enhet.

   :::image type="content" source="media/tutorial-install-components/mapped-device-on-virtual-media-screen-v2.png" alt-text="Skärmbild som visar en mappad enhet.":::

1. Mediet monteras. Välj **Stäng**.

1. Starta installationen. När du använder iDRAC kan du starta om servrarna genom att välja knappen **Consul Control (Consul-kontroll).** På tangentbordsma **makron väljer** du sedan **knappen Tillämpa,** som startar sekvensen Ctrl + Alt + Ta bort.

1. Välj **Engelska.**

1. Välj **SENSOR-RELEASE- \<version\> Enterprise**.

   :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Välj din sensorversion och företagstyp.":::   

1. Definiera enhetens profil och nätverksegenskaper:

   :::image type="content" source="media/tutorial-install-components/appliance-profile-screen-v2.png" alt-text="Skärmbild som visar enhetens profil och nätverksegenskaper.":::   

   | Parameter | Konfiguration |
   |--|--|
   | **Maskinvaruprofil** | **Företag** |
   | **Hanteringsgränssnitt** | **eno1** |
   | **Nätverksparametrar (tillhandahålls av kunden)** | - |
   |**IP-adress för hanteringsnätverk:** | - |
   | **nätmask:** | - |
   | **enhetens värdnamn:** | - |
   | **Dns:** | - |
   | **ip-adress för standardgateway:** | - |
   | **indatagränssnitt:** |  Systemet genererar listan över indatagränssnitt åt dig. Om du vill spegla indatagränssnitten kopierar du alla objekt som visas i listan med en kommaavgränsare. Du behöver inte konfigurera brygggränssnittet. Det här alternativet används endast för särskilda användningsfall. |

1. Efter cirka 10 minuter visas de två uppsättningarna med autentiseringsuppgifter. En är för en **CyberX-användare** och en för en **supportanvändare.**  

1. Spara enhetens ID och lösenord. Du behöver dessa autentiseringsuppgifter för att få åtkomst till plattformen första gången du använder den.

1. Välj **Retur för** att fortsätta.

## <a name="hpe-proliant-dl20-installation"></a>INSTALLATION av HPE ProLiant DL20

I den här artikeln beskrivs installationsprocessen för HPE ProLiant DL20, som innehåller följande steg:

  - Aktivera fjärråtkomst och uppdatera standardadministratörslösenordet.
  - Konfigurera BIOS- och RAID-inställningar.
  - Installera programvaran.

### <a name="about-the-installation"></a>Om installationen

  - Företags- och SMB-installationer kan installeras. Installationsprocessen är identisk för båda installationstyperna, förutom matriskonfigurationen.
  - En administrativ standardanvändare tillhandahålls. Vi rekommenderar att du ändrar lösenordet under nätverkskonfigurationsprocessen.
  - Under nätverkskonfigurationsprocessen konfigurerar du iLO-porten på nätverksport 1.
  - Installationsprocessen tar cirka 20 minuter. Efter installationen startas systemet om flera gånger.

### <a name="hpe-proliant-dl20-front-panel"></a>HPE ProLiant DL20-frontpanelen

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-front-panel-v2.png" alt-text="HPE ProLiant DL20-frontpanelen.":::

### <a name="hpe-proliant-dl20-back-panel"></a>HPE ProLiant DL20-bakpanel

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-back-panel-v2.png" alt-text="Baksidan av HPE ProLiant DL20.":::

### <a name="enable-remote-access-and-update-the-password"></a>Aktivera fjärråtkomst och uppdatera lösenordet

Använd följande procedur för att konfigurera nätverksalternativ och uppdatera standardlösenordet.

Så här aktiverar och uppdaterar du lösenordet:

1. Anslut en skärm och ett tangentbord till HP-enheten, aktivera enheten och tryck på **F9.**

    :::image type="content" source="media/tutorial-install-components/hpe-proliant-screen-v2.png" alt-text="Skärmbild som visar fönstret HPE ProLiant.":::

1. Gå till **SystemVerktyg**  >  **SystemKonfiguration av**  >  **iLO 5 Nätverksalternativ för**  >  **konfigurationsverktyget**.

    :::image type="content" source="media/tutorial-install-components/system-configuration-window-v2.png" alt-text="Skärmbild som visar fönstret Systemkonfiguration.":::

    1.  Välj **Shared Network Port-LOM (Port-LOM** för delat **nätverk) i fältet Nätverkskort.**
    
    1.  Inaktivera DHCP.
    
    1.  Ange IP-adress, nätmask och gateway-IP-adress.

1. Välj **F10: Spara**.

1. Välj **Esc** för att gå tillbaka **till iLO 5-konfigurationsverktyget** och välj sedan **Användarhantering.**

1. Välj **Redigera/ta bort användare.** Administratören är den enda standardanvändare som har definierats. 

1. Ändra standardlösenordet och **välj F10: Spara**.

### <a name="configure-the-hpe-bios"></a>Konfigurera HPE BIOS

Följande procedur beskriver hur du konfigurerar HPE BIOS för företaget och SMB-installationer.

Så här konfigurerar du HPE BIOS:

1. Välj **SystemVerktyg**  >  **Systemkonfiguration**  >  **BIOS/Plattformskonfiguration (RBSU).**

1. I formuläret **BIOS/Platform Configuration (RBSU)** väljer du **Startalternativ.**

1. Ändra **Startläge** till **äldre BIOS-läge** och välj sedan **F10: Spara**.

1. Välj **Esc två** gånger för att stänga formuläret **Systemkonfiguration.**

#### <a name="for-the-enterprise-appliance"></a>För företagsinstallationen

1. Välj **Embedded RAID 1: HPE Smart Array P408i-a SR Gen 10**  >  **Array Configuration Create**  >  **Array**.

1. I **formuläret Skapa** matris väljer du alla alternativ. Tre alternativ är tillgängliga för **Enterprise-installationen.**

#### <a name="for-the-smb-appliance"></a>För SMB-installationen

1. Välj **Embedded RAID 1: HPE Smart Array P208i-a SR Gen 10**  >  **Array Configuration Create**  >  **Array**.

1. Välj **Fortsätt till nästa formulär.**

1. I formuläret **Ange RAID-nivå** anger du nivån **till RAID 5** för företagsdistributioner och RAID **1** för SMB-distributioner.

1. Välj **Fortsätt till nästa formulär.**

1. I formuläret **Logisk enhetsetikett** anger du **Logisk enhet 1.**

1. Välj **Skicka ändringar.**

1. I formuläret **Skicka** väljer du **Tillbaka till huvudmenyn.**

1. Välj **F10: Spara och** tryck sedan på Esc **två** gånger.

1. I fönstret **Systemverktyg** väljer du **Startmeny för en gång.**

1. I formuläret **One-Time Boot Menu (Startmeny** för en gång) väljer du Legacy BIOS One-Time Boot Menu (Äldre **BIOS-startmeny).**

1. Fönstren **Booting in Legacy (Start i äldre)** **och Boot Override (Åsidosätt** start) visas. Välj ett alternativ för att åsidosätta start. till exempel till ett CD-ROM-, USB-, HDD- eller UEFI-gränssnitt.

    :::image type="content" source="media/tutorial-install-components/boot-override-window-one-v2.png" alt-text="Skärmbild som visar det första fönstret Åsidosätt start.":::

    :::image type="content" source="media/tutorial-install-components/boot-override-window-two-v2.png" alt-text="Skärmbild som visar det andra fönstret Åsidosätt start.":::
### <a name="software-installation-hpe-proliant-dl20-appliance"></a>Programvaruinstallation (HPE ProLiant DL20-installation)

Installationsprocessen tar cirka 20 minuter. Efter installationen startas systemet om flera gånger.

Så här installerar du programvaran:

1. Anslut skärmen och tangentbordet till enheten och anslut sedan till CLI.

1. Anslut en extern CD eller disk på nyckeln med ISO-avbildningen som du laddade ned från sidan Uppdateringar i Defender for IoT-portalen. 

1. Starta installationen.

1. Välj **Engelska.**

    :::image type="content" source="media/tutorial-install-components/select-english-screen.png" alt-text="Val av engelska i CLI-fönstret.":::

1. Välj **SENSOR-RELEASE- <version> Enterprise**.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Skärmbild av skärmen där du väljer en version.":::

1. I installationsguiden definierar du maskinvaruprofilen och nätverksegenskaperna:

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="Skärmbild som visar installationsguiden.":::

    | Parameter | Konfiguration |
    | ----------| ------------- |
    | **Maskinvaruprofil** | Välj **Enterprise** eller **Office** för SMB-distributioner. |
    | **Hanteringsgränssnitt** | **eno2** |
    | **Standardnätverksparametrar (vanligtvis anges parametrarna av kunden)** | **IP-adress för hanteringsnätverket:** <br/> <br/>**enhetens värdnamn:** <br/>**Dns:** <br/>**IP-adressen för standardgatewayen:**|
    | **indatagränssnitt:** | Systemet genererar listan över indatagränssnitt åt dig.<br/><br/>Om du vill spegla indatagränssnitten kopierar du alla objekt som visas i listan med en kommaavgränsare: **eno5, eno3, eno1, eno6, eno4**<br/><br/>**För HPE DL20: Visa inte eno1, enp1s0f4u4 (iLo-gränssnitt)**<br/><br/>**BRIDGE**: Du behöver inte konfigurera brygggränssnittet. Det här alternativet används endast för särskilda användningsfall. Tryck på **RETUR** för att fortsätta. |

1. Efter cirka 10 minuter visas de två uppsättningarna med autentiseringsuppgifter. En är för en **CyberX-användare** och en för en **supportanvändare.**

1. Spara enhetens ID och lösenord. Du behöver autentiseringsuppgifterna för att få åtkomst till plattformen för första gången.

1. Välj **Retur för** att fortsätta.

## <a name="hpe-proliant-dl360-installation"></a>INSTALLATION av HPE ProLiant DL360

  - En administrativ standardanvändare tillhandahålls. Vi rekommenderar att du ändrar lösenordet under nätverkskonfigurationen.

  - Under nätverkskonfigurationen konfigurerar du iLO-porten.

  - Installationsprocessen tar cirka 20 minuter. Efter installationen startas systemet om flera gånger.

### <a name="hpe-proliant-dl360-front-panel"></a>HPE ProLiant DL360-frontpanelen

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-front-panel.png" alt-text="HPE ProLiant DL360-frontpanelen.":::

### <a name="hpe-proliant-dl360-back-panel"></a>HPE ProLiant DL360-bakpanel

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-back-panel.png" alt-text="HPE ProLiant DL360-bakpanel.":::

### <a name="enable-remote-access-and-update-the-password"></a>Aktivera fjärråtkomst och uppdatera lösenordet

Se föregående avsnitt för HPE ProLiant DL20-installation:

  - "Aktivera fjärråtkomst och uppdatera lösenordet"

  - "Konfigurera HPE BIOS"

Företagskonfigurationen är identisk.

> [!Note]
> I matrisformuläret kontrollerar du att du har valt alla alternativ.

### <a name="ilo-remote-installation-from-a-virtual-drive"></a>iLO-fjärrinstallation (från en virtuell enhet)

Den här proceduren beskriver iLO-installationen från en virtuell enhet.

Så här installerar du:

1. Logga in på iLO-konsolen och högerklicka sedan på servrarna.

1. Välj **HTML5-konsolen**.

1. Välj CD-ikonen i konsolen och välj alternativet CD/DVD.

1. Välj **Lokal ISO-fil**.

1. Välj relevant ISO-fil i dialogrutan.

1. Gå till den vänstra ikonen, **välj Power** och välj **Återställ.**

1. Installationen startar om och kör sensorinstallationsprocessen.

### <a name="software-installation-hpe-dl360"></a>Programvaruinstallation (HPE DL360)

Installationsprocessen tar cirka 20 minuter. Efter installationen startas systemet om flera gånger.

Så här installerar du:

1. Anslut skärmen och tangentbordet till enheten och anslut sedan till CLI.

1. Anslut en extern CD eller disk på en nyckel med  ISO-avbildningen som du laddade ned från sidan Uppdateringar i Defender for IoT-portalen.

1. Starta installationen.

1. Välj **Engelska.**

1. Välj **SENSOR-RELEASE- <version> Enterprise**.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Skärmbild som visar val av version.":::

1. Definiera installationsprofilen och nätverksegenskaperna i installationsguiden.

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="Skärmbild som visar installationsguiden.":::

    | Parameter | Konfiguration |
    | ----------| ------------- |
    | **Maskinvaruprofil** | Välj **företag**. |
    | **Hanteringsgränssnitt** | **eno2** |
    | **Standardnätverksparametrar (tillhandahålls av kunden)** | **IP-adress för hanteringsnätverk:** <br/>**nätmask:** <br/>**enhetens värdnamn:** <br/>**Dns:** <br/>**IP-adressen för standardgatewayen:**|
    | **indatagränssnitt:**  | Systemet genererar en lista över indatagränssnitt åt dig.<br/><br/>Om du vill spegla indatagränssnitten kopierar du alla objekt som visas i listan med en kommaavgränsare.<br/><br/> Du behöver inte konfigurera brygggränssnittet. Det här alternativet används endast för särskilda användningsfall. |

1. Efter cirka 10 minuter visas de två uppsättningarna med autentiseringsuppgifter. En är för en **CyberX-användare** och en är för en **supportanvändare.**

1. Spara enhetens ID och lösenord. Du behöver dessa autentiseringsuppgifter för att få åtkomst till plattformen för första gången.

1. Välj **Retur för** att fortsätta.

## <a name="sensor-installation-for-the-virtual-appliance"></a>Sensorinstallation för den virtuella installationen

Du kan distribuera den virtuella datorn för Defender for IoT-sensorn i följande arkitekturer:


| Arkitektur | Specifikationer | Användning | Kommentarer |
|---|---|---|---|
| **Enterprise** | CPU: 8<br/>Minne: 32 G RAM<br/>HDD: 1800 GB | Produktionsmiljö | Standard och vanligaste |
| **Små företag** | CPU: 4 <br/>Minne: 8G RAM<br/>HDD: 500 GB | Testmiljöer eller små produktionsmiljöer | -  |
| **Office** | CPU: 4<br/>Minne: 8G RAM<br/>HDD: 100 GB | Små testmiljöer | -  |

### <a name="prerequisites"></a>Förutsättningar

Den lokala hanteringskonsolen stöder både VMware- och Hyper-V-distributionsalternativ. Kontrollera att du har följande innan du påbörjar installationen:

  - VMware (ESXi 5.5 eller senare) eller Hyper-V-hypervisor (Windows 10 Pro eller Enterprise) installerat och i drift

  - Tillgängliga maskinvaruresurser för den virtuella datorn

  - ISO-installationsfil för Azure Defender for IoT sensorn

Kontrollera att hypervisor-programmet körs.

### <a name="create-the-virtual-machine-esxi"></a>Skapa den virtuella datorn (ESXi)

1. Logga in på ESXi, välj relevant **datalager och** välj **Datastore Browser**.

1. **Ladda** upp avbildningen och välj **Stäng**.

1. Gå till **Virtual Machines** och välj sedan **Skapa/registrera virtuell dator.**

1. Välj **Skapa ny virtuell dator** och välj sedan **Nästa.**

1. Lägg till ett sensornamn och välj:

   - Kompatibilitet: senaste **&lt; ESXi-versionen &gt;**

   - Gästoperativsystemsfamilj: **Linux**

   - Gästoperativsystemversion: **Ubuntu Linux (64-bitars)**

1. Välj **Nästa**.

1. Välj relevant datalager och välj **Nästa.**

1. Ändra parametrarna för virtuell maskinvara enligt den arkitektur som krävs.

1. För **CD/DVD-enhet 1** väljer **du Iso-fil för datalager** och den ISO-fil som du laddade upp tidigare.

1. Välj **Nästa** > **Slutför**.

### <a name="create-the-virtual-machine-hyper-v"></a>Skapa den virtuella datorn (Hyper-V)

Den här proceduren beskriver hur du skapar en virtuell dator med hjälp av Hyper-V.

Så här skapar du en virtuell dator:

1. Skapa en virtuell disk i Hyper-V Manager.

1. Välj **format = VHDX**.

1. Välj **typ = Dynamiskt expanderande**.

1. Ange namn och plats för den virtuella hårddisken.

1. Ange önskad storlek (enligt arkitekturen).   

1. Granska sammanfattningen och välj **Slutför**.

1. Skapa en **ny** virtuell dator på menyn Åtgärder.

1. Ange ett namn för den virtuella datorn.

1. Välj **Ange generation**  >  **1.**

1. Ange minnesallokeringen (enligt arkitekturen) och markera kryssrutan för dynamiskt minne.

1. Konfigurera nätverksadaptern enligt serverns nätverkstopologi.

1. Anslut den VHDX som skapades tidigare till den virtuella datorn.

1. Granska sammanfattningen och välj **Slutför**.

1. Högerklicka på den nya virtuella datorn och välj **Inställningar.**

1. Välj **Lägg till maskinvara** och lägg till ett nytt nätverkskort.

1. Välj den virtuella växel som ska ansluta till sensorhanteringsnätverket.

1. Allokera CPU-resurser (enligt arkitekturen).

1. Anslut hanteringskonsolens ISO-avbildning till en virtuell DVD-enhet.

1. Starta den virtuella datorn.

2. På menyn **Åtgärder** väljer du **Anslut för** att fortsätta programinstallationen.

### <a name="software-installation-esxi-and-hyper-v"></a>Programvaruinstallation (ESXi och Hyper-V)

I det här avsnittet beskrivs installationen av ESXi- och Hyper-V-programvaran.

Så här installerar du:

1. Öppna den virtuella datorkonsolen.

1. Den virtuella datorn startar från ISO-avbildningen och skärmen för val av språk visas. Välj **Engelska.**

1. Välj den arkitektur som krävs.

1. Definiera enhetens profil och nätverksegenskaper:

    | Parameter | Konfiguration |
    | ----------| ------------- |
    | **Maskinvaruprofil** | &lt;arkitektur som krävs&gt; |
    | **Hanteringsgränssnitt** | **ens192** |
    | **Nätverksparametrar (tillhandahålls av kunden)** | **IP-adress för hanteringsnätverk:** <br/>**nätmask:** <br/>**enhetens värdnamn:** <br/>**Dns:** <br/>**Standardgateway:** <br/>**indatagränssnitt:**|
    | **bridge-gränssnitt:** | Du behöver inte konfigurera bridge-gränssnittet. Det här alternativet är endast för särskilda användningsfall. |

1. Ange **Y** för att acceptera inställningarna.

1. Inloggningsuppgifter genereras och visas automatiskt. Kopiera användarnamnet och lösenordet på en säker plats eftersom de krävs för inloggning och administration.

      - **Stöd:** Den administrativa användaren för användarhantering.

      - **CyberX:** Motsvarigheten till roten för åtkomst till apparaten.

1. Installationen startas om.

1. Få åtkomst till hanteringskonsolen via den IP-adress som tidigare konfigurerats: `https://ip_address` .

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Skärmbild som visar åtkomst till hanteringskonsolen.":::

## <a name="on-premises-management-console-installation"></a>Installation av lokal hanteringskonsol

Innan du installerar programvaran på enheten måste du justera installationens BIOS-konfiguration:

### <a name="bios-configuration"></a>BIOS-konfiguration

Så här konfigurerar du BIOS för din enhet:

1. [Aktivera fjärråtkomst och uppdatera lösenordet](#enable-remote-access-and-update-the-password).

1. [Konfigurera BIOS.](#configure-the-hpe-bios)

### <a name="software-installation"></a>Programvaruinstallation

Installationsprocessen tar cirka 20 minuter. Efter installationen startas systemet om flera gånger. 

Under installationsprocessen kan du lägga till ett sekundärt nätverkskort. Om du väljer att inte installera det sekundära nätverkskortet under installationen kan du [lägga till ett sekundärt nätverkskort](#add-a-secondary-nic) vid ett senare tillfälle. 

Så här installerar du programvaran:

1. Välj önskat språk för installationsprocessen.

   :::image type="content" source="media/tutorial-install-components/on-prem-language-select.png" alt-text="Välj önskat språk för installationsprocessen.":::     

1. Välj **MANAGEMENT-RELEASE- \<version\> \<deployment type\>**.

   :::image type="content" source="media/tutorial-install-components/on-prem-install-screen.png" alt-text="Välj din version.":::   

1. I installationsguiden definierar du nätverksegenskaperna:

   :::image type="content" source="media/tutorial-install-components/on-prem-first-steps-install.png" alt-text="Skärmbild som visar enhetens profil.":::   

   | Parameter | Konfiguration |
   |--|--|
   | **konfigurera hanteringsnätverksgränssnittet** | För Dell: **eth0, eth1** <br /> För HP: **enu1, enu2** <br /> eller <br />**möjligt värde** |
   | **konfigurera IP-adress för hanteringsnätverk:** | **IP-adress som anges av kunden** |
   | **konfigurera nätmask:** | **IP-adress som anges av kunden** |
   | **konfigurera DNS:** | **IP-adress som anges av kunden** |
   | **konfigurera ip-adressen för standardgatewayen:** | **IP-adress som anges av kunden** |
   
1. **(Valfritt)** Om du vill installera ett sekundärt nätverkskort (NIC) definierar du följande installationsprofil och nätverksegenskaper:

    :::image type="content" source="media/tutorial-install-components/on-prem-secondary-nic-install.png" alt-text="Skärmbild som visar installationsfrågor för sekundärt nätverkskort.":::

   | Parameter | Konfiguration |
   |--|--|
   | **konfigurera gränssnittet för sensorövervakning (valfritt):** | **eth1**, eller **möjligt värde** |
   | **konfigurera en IP-adress för gränssnittet för sensorövervakning:** | **IP-adress som anges av kunden** |
   | **konfigurera en nätmask för gränssnittet för sensorövervakning:** | **IP-adress som anges av kunden** |

1. Acceptera set-erna och fortsätt genom att skriva `Y` . 

1. Efter cirka 10 minuter visas de två uppsättningarna med autentiseringsuppgifter. En är för en **CyberX-användare** och en är för en **supportanvändare.**

   :::image type="content" source="media/tutorial-install-components/credentials-screen.png" alt-text="Kopiera dessa autentiseringsuppgifter eftersom de inte visas igen.":::  

   Spara användarnamnen och lösenorden. Du behöver dessa autentiseringsuppgifter för att få åtkomst till plattformen första gången du använder den.

1. Välj **Retur för** att fortsätta.

Information om hur du hittar den fysiska porten i installationen finns i [Hitta din port.](#find-your-port)

### <a name="add-a-secondary-nic"></a>Lägga till ett sekundärt nätverkskort

Du kan förbättra säkerheten i din lokala hanteringskonsol genom att lägga till ett sekundärt nätverkskort. Genom att lägga till ett sekundärt nätverkskort har du ett dedikerat för dina användare och det andra har stöd för konfiguration av en gateway för dirigerade nätverk. Det andra nätverkskortet är dedikerat till alla anslutna sensorer inom ett IP-adressintervall.

Båda nätverkskorten har användargränssnittet aktiverat. Om routning inte behövs är alla funktioner som stöds av användargränssnittet tillgängliga på det sekundära nätverkskortet. Hög tillgänglighet körs på det sekundära nätverkskortet.

Om du väljer att inte distribuera ett sekundärt nätverkskort blir alla funktioner tillgängliga via det primära nätverkskortet. 

Om du redan har konfigurerat din lokala hanteringskonsol och vill lägga till ett sekundärt nätverkskort i den lokala hanteringskonsolen använder du följande steg:

1. Använd kommandot för att konfigurera om nätverket:

    ```bash
    sudo cyberx-management-network-reconfigure
    ```

1. Ange följande svar på följande frågor:

    :::image type="content" source="media/tutorial-install-components/network-reconfig-command.png" alt-text="Ange följande svar för att konfigurera installationen.":::

    | Parametrar | Svar att ange |
    |--|--|
    | **IP-adress för hanteringsnätverk** | `N` |
    | **Nätmask** | `N` |
    | **DNS** | `N` |
    | **IP-adress för standardgateway** | `N` |
    | **Gränssnitt för sensorövervakning (valfritt. Gäller när sensorer finns i ett annat nätverkssegment. Mer information finns i installationsanvisningarna)**| `Y`väljer **du ett möjligt värde** |
    | **En IP-adress för sensorövervakningsgränssnittet (kan nås av sensorerna)** | `Y`, **IP-adress som tillhandahålls av kunden**|
    | **En nätmask för sensorövervakningsgränssnittet (tillgänglig för sensorerna)** | `Y`, **IP-adress som tillhandahålls av kunden** |
    | **Värdnamn** | **tillhandahålls av kunden** |

1. Granska alla alternativ och ange för `Y` att acceptera ändringarna. Systemet startas om.

### <a name="find-your-port"></a>Hitta din port

Om du har problem med att hitta den fysiska porten på enheten kan du använda följande kommando för att:

```bash
sudo ethtool -p <port value> <time-in-seconds>
```

Det här kommandot gör att lampan på porten blinkar under den angivna tidsperioden. Om du till `sudo ethtool -p eno1 120` exempel anger , kommer porten eno1 att flasha i 2 minuter så att du kan hitta porten på baksidan av installationen. 

## <a name="virtual-appliance-on-premises-management-console-installation"></a>Virtuell installation: Installation av den lokala hanteringskonsolen

Den lokala hanteringskonsolens virtuella dator stöder följande arkitekturer:

| Arkitektur | Specifikationer | Användning | 
|--|--|--|
| Stora företag <br/>(Standard och vanligaste) | CPU: 8 <br/>Minne: 32 G RAM<br/> HDD: 1,8 TB | Stora produktionsmiljöer | 
| Stora företag | CPU: 4 <br/> Minne: 8G RAM<br/> HDD: 500 GB | Stora produktionsmiljöer |
| Stora företag | CPU: 4 <br/>Minne: 8G RAM <br/> HDD: 100 GB | Små testmiljöer | 
   
### <a name="prerequisites"></a>Förutsättningar

Den lokala hanteringskonsolen har stöd för både VMware- och Hyper-V-distributionsalternativ. Kontrollera följande innan du påbörjar installationen:

- VMware (ESXi 5.5 eller senare) eller Hyper-V-hypervisorn (Windows 10 Pro eller Enterprise) installeras och fungerar.

- Maskinvaruresurserna är tillgängliga för den virtuella datorn.

- Du har ISO-installationsfilen för den lokala hanteringskonsolen.
    
- Hypervisor-programmet körs.

### <a name="create-the-virtual-machine-esxi"></a>Skapa den virtuella datorn (ESXi)

Så här skapar du en virtuell dator (ESXi):

1. Logga in på ESXi, välj relevant **datalager och** välj **Datastore Browser**.

1. Ladda upp avbildningen och välj **Stäng**.

1. Gå till **Virtual Machines**.

1. Välj **Skapa/registrera virtuell dator.**

1. Välj **Skapa ny virtuell dator** och välj **Nästa.**

1. Lägg till ett sensornamn och välj:

   - Kompatibilitet: \<latest ESXi version>

   - Gästoperativsystemsfamilj: Linux

   - Gästoperativsystemversion: Ubuntu Linux (64-bitars)

1. Välj **Nästa**.

1. Välj relevant datalager och välj **Nästa.**

1. Ändra parametrarna för virtuell maskinvara enligt den arkitektur som krävs.

1. För **CD/DVD-enhet 1** väljer **du Iso-fil för datalager** och den ISO-fil som du laddade upp tidigare.

1. Välj **Nästa** > **Slutför**.

### <a name="create-the-virtual-machine-hyper-v"></a>Skapa den virtuella datorn (Hyper-V)

Så här skapar du en virtuell dator med hjälp av Hyper-V:

1. Skapa en virtuell disk i Hyper-V Manager.

1. Välj formatet **VHDX**.

1. Välj **Nästa**.

1. Välj typen Dynamiskt **expanderande**.

1. Välj **Nästa**.

1. Ange namn och plats för den virtuella hårddisken.

1. Välj **Nästa**.

1. Ange önskad storlek (enligt arkitekturen).

1. Välj **Nästa**.

1. Granska sammanfattningen och välj **Slutför**.

1. Skapa en **ny** virtuell dator på menyn Åtgärder.

1. Välj **Nästa**.

1. Ange ett namn för den virtuella datorn.

1. Välj **Nästa**.

1. Välj **Generation** och ställ in den **på Generation 1.**

1. Välj **Nästa**.

1. Ange minnesallokeringen (enligt arkitekturen) och markera kryssrutan för dynamiskt minne.

1. Välj **Nästa**.

1. Konfigurera nätverksadaptern enligt serverns nätverkstopologi.

1. Välj **Nästa**.

1. Anslut den VHDX som skapades tidigare till den virtuella datorn.

1. Välj **Nästa**.

1. Granska sammanfattningen och välj **Slutför**.

1. Högerklicka på den nya virtuella datorn och välj sedan **Inställningar.**

1. Välj **Lägg till** maskinvara och lägg till ett nytt nätverkskort för **Nätverkskort.**

1. För **Virtuell växel** väljer du den växel som ska ansluta till sensorhanteringsnätverket.

1. Allokera CPU-resurser (enligt arkitekturen).

1. Anslut hanteringskonsolens ISO-avbildning till en virtuell DVD-enhet.

1. Starta den virtuella datorn.

1. På menyn **Åtgärder** väljer du **Anslut för** att fortsätta programinstallationen.

### <a name="software-installation-esxi-and-hyper-v"></a>Programvaruinstallation (ESXi och Hyper-V)

Om du startar den virtuella datorn startas installationsprocessen från ISO-avbildningen.

Så här installerar du programvaran:

1. Välj **Engelska.**

1. Välj den arkitektur som krävs för distributionen.

1. Definiera nätverksgränssnittet för sensorhanteringsnätverket: gränssnitt, IP, undernät, DNS-server och standardgateway.

1. Inloggningsuppgifter genereras automatiskt. Spara användarnamnet och lösenorden. Du behöver dessa autentiseringsuppgifter för att få åtkomst till plattformen första gången du använder den.

   Installationen startas sedan om.

1. Öppna hanteringskonsolen via den IP-adress som tidigare konfigurerades: `<https://ip_address>` .

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-management-console-sign-in-screen.png" alt-text="Skärmbild som visar hanteringskonsolens inloggningsskärm.":::

## <a name="post-installation-validation"></a>Validering efter installation

För att verifiera installationen av en fysisk installation måste du utföra många tester. Samma valideringsprocess gäller för alla typer av apparater.

Utför verifieringen med hjälp av det grafiska användargränssnittet eller CLI. Verifieringen är tillgänglig för användaren **Support** och användaren **CyberX.**

Verifiering efter installationen måste innehålla följande tester:

  - **Sanity test (Sanity test):** Kontrollera att systemet körs.

  - **Version**: Kontrollera att versionen är korrekt.

  - **ifconfig:** Kontrollera att alla indatagränssnitt som konfigureras under installationsprocessen körs.

### <a name="checking-system-health-by-using-the-gui"></a>Kontrollera systemets hälsa med hjälp av det grafiska användargränssnittet

:::image type="content" source="media/tutorial-install-components/system-health-check-screen.png" alt-text="Skärmbild som visar systemhälsokontrollen.":::

#### <a name="sanity"></a>Förstånd

- **Apparat:** Kör enhetens sanitetskontroll. Du kan utföra samma kontroll med hjälp av CLI-kommandot `system-sanity` .

- **Version:** Visar installationens version.

- **Nätverksegenskaper:** Visar sensornätverksparametrarna.

#### <a name="redis"></a>Redis

- **Minne:** Ger en övergripande bild av minnesanvändningen, till exempel hur mycket minne som användes och hur mycket som fanns kvar.

- **Längst nyckel:** Visar de längsta nycklarna som kan orsaka omfattande minnesanvändning.

#### <a name="system"></a>System

- **Kärnlogg:** Innehåller de sista 500 raderna i kärnloggen, så att du kan visa de senaste loggraderna utan att exportera hela systemloggen.

- **Aktivitetshanteraren:** Översätter de uppgifter som visas i tabellen med processer till följande lager: 
  
  - Beständigt lager (Redis) 
  - Cash layer (SQL)

- **Nätverksstatistik:** Visar nätverksstatistiken.

- **TOP:** Visar tabellen med processer. Det är ett Linux-kommando som ger en dynamisk realtidsvy över det system som körs.

- **Minneskontroll för säkerhetskopiering:** Visar status för säkerhetskopieringsminnet och kontrollerar följande:
  - Platsen för säkerhetskopieringsmappen 
  - Storleken på säkerhetskopieringsmappen
  - Begränsningarna för säkerhetskopieringsmappen
  - När den senaste säkerhetskopieringen inträffade
  - Hur mycket utrymme det finns för de extra säkerhetskopierade filerna

- **ifconfig:** Visar parametrarna för installationens fysiska gränssnitt.

- **CyberX-nload:** Visar nätverkstrafik och bandbredd med hjälp av sex sekunders tester.

- **Fel från Core, log**: Visar fel från kärnloggfilen.

Så här kommer du åt verktyget:

1. Logga in på sensorn med **autentiseringsuppgifterna för supportanvändaren.**

1. Välj **Systemstatistik** i **fönstret Systeminställningar.**

    :::image type="icon" source="media/tutorial-install-components/system-statistics-icon.png" border="false":::

### <a name="checking-system-health-by-using-the-cli"></a>Kontrollera systemets hälsotillstånd med hjälp av CLI

**Test 1: Sanity**

Kontrollera att systemet är igång:

1. Anslut till CLI med Linux-terminalen (till exempel PuTTY) och **användarens support**.

1. Ange `system sanity`.

1. Kontrollera att alla tjänster är gröna (körs).

    :::image type="content" source="media/tutorial-install-components/support-screen.png" alt-text="Skärmbild som visar tjänster som körs.":::

1. Kontrollera att **systemet är UPP! (prod)** visas längst ned.

**Test 2: Versionskontroll**

Kontrollera att rätt version används:

1. Anslut till CLI med Linux-terminalen (till exempel PuTTY) och **användarens support**.

1. Ange `system version`.

1. Kontrollera att rätt version visas.

**Test 3: Nätverksverifiering**

Kontrollera att alla indatagränssnitt som konfigureras under installationsprocessen körs:

1. Anslut till CLI med Linux-terminalen (till exempel PuTTY) och **användarens support**.

1. Ange `network list` (motsvarigheten till Linux-kommandot `ifconfig` ).

1. Kontrollera att nödvändiga indatagränssnitt visas. Om till exempel två quad-kopparnic är installerade bör det finnas 10 gränssnitt i listan.

    :::image type="content" source="media/tutorial-install-components/interface-list-screen.png" alt-text="Skärmbild som visar listan över gränssnitt.":::

**Test 4: Hanteringsåtkomst till användargränssnittet**

Kontrollera att du har åtkomst till konsolens webbgränssnitt:

1. Anslut en bärbar dator med en Ethernet-kabel till hanteringsporten (**Gb1**).

1. Definiera att nätverkskortadressen för den bärbara datorn ska vara i samma intervall som enheten.

    :::image type="content" source="media/tutorial-install-components/access-to-ui.png" alt-text="Skärmbild som visar hanteringsåtkomst till användargränssnittet.":::

1. Pinga installationens IP-adress från den bärbara datorn för att verifiera anslutningen (standard: 10.100.10.1).

1. Öppna webbläsaren Chrome på den bärbara datorn och ange enhetens IP-adress.

1. I fönstret **Din anslutning är inte privat** väljer du **Avancerat** och fortsätter.

1. Testet lyckas när inloggningsskärmen för Defender for IoT visas.

   :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Skärmbild som visar åtkomst till hanteringskonsolen.":::

## <a name="troubleshooting"></a>Felsökning

### <a name="you-cant-connect-by-using-a-web-interface"></a>Du kan inte ansluta med hjälp av ett webbgränssnitt

1. Kontrollera att datorn som du försöker ansluta till finns i samma nätverk som installationen.

1. Kontrollera att GUI-nätverket är anslutet till hanteringsporten.

1. Pinga installationens IP-adress. Om det inte finns någon ping:

   1. Anslut en bildskärm och ett tangentbord till enheten.

   1. Använd **supportanvändaren** och lösenordet för att logga in.

   1. Använd kommandot för `network list` att se den aktuella IP-adressen.

      :::image type="content" source="media/tutorial-install-components/network-list.png" alt-text="Skärmbild som visar listan över nätverk.":::

1. Om nätverksparametrarna är felkonfigurerade använder du följande procedur för att ändra dem:

   1. Använd kommandot `network edit-settings` .

   1. Om du vill ändra IP-adressen för hanteringsnätverket väljer **du Y**.

   1. Om du vill ändra nätmasken väljer du **Y**.

   1. Om du vill ändra DNS väljer du **Y**.

   1. Om du vill ändra IP-adressen för standardgatewayen väljer **du Y**.

   1. För ändring av indatagränssnittet (endast sensor) väljer du **N**.

   1. Om du vill tillämpa inställningarna väljer du **Y**.

1. Efter omstart ansluter du med autentiseringsuppgifterna för supportanvändaren och använder `network list` kommandot för att verifiera att parametrarna har ändrats.

1. Försök att pinga och ansluta från det grafiska användargränssnittet igen.

### <a name="the-appliance-isnt-responding"></a>Installationen svarar inte

1. Anslut en bildskärm och ett tangentbord till enheten eller använd PuTTY för att fjärransluta till CLI.

1. Använd **supportanvändarens** autentiseringsuppgifter för att logga in.

1. Använd kommandot `system sanity` och kontrollera att alla processer körs.

    :::image type="content" source="media/tutorial-install-components/system-sanity-screen.png" alt-text="Skärmbild som visar kommandot system sanity.":::

Om du har andra problem kontaktar du [Microsoft Support](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="appendix-a-mirroring-port-on-vswitch-esxi"></a>Bilaga A: Speglingsport på vSwitch (ESXi)

### <a name="configure-a-span-port-on-an-existing-vswitch"></a>Konfigurera en SPAN-port på en befintlig vSwitch

En vSwitch har inte speglingsfunktioner, men du kan använda en lösning för att implementera en SPAN-port.

Så här konfigurerar du en SPAN-port:

1. Öppna vSwitch-egenskaper.

1. Välj **Lägg till**.

1. Välj **Virtuell dator**  >  **nästa.**

1. Infoga nätverksetiketten **SPAN Network (SPAN-nätverk)** och **välj VLAN ID**  >  **All**(Alla) och välj **sedan Next (Nästa).**

1. Välj **Slutför**.

1. Välj **SPAN Network** > **Redigera*.

1. Välj **Säkerhet** och kontrollera att **principen För icke-diskret läge** är inställd på **Acceptera-läge.**

1. Välj **OK** och välj sedan **Stäng för** att stänga vSwitch-egenskaperna.

1. Öppna egenskaperna för **den virtuella XSense-datorn.**

1. För **Nätverkskort 2** väljer du **SPAN-nätverket.**

1. Välj **OK**.

1. Anslut till sensorn och kontrollera att speglingen fungerar.

## <a name="appendix-b-access-sensors-from-the-on-premises-management-console"></a>Bilaga B: Åtkomstsensorer från den lokala hanteringskonsolen

Du kan förbättra systemsäkerheten genom att förhindra direkt användaråtkomst till sensorn. Använd i stället proxytunnlar för att ge användarna åtkomst till sensorn från den lokala hanteringskonsolen med en enda brandväggsregel. Den här tekniken begränsar risken för obehörig åtkomst till nätverksmiljön utanför sensorn. Användarens upplevelse när du loggar in på sensorn förblir densamma.

:::image type="content" source="media/tutorial-install-components/sensor-system-graph.png" alt-text="Skärmbild som visar åtkomst till sensorn.":::

Så här aktiverar du tunneling:

1. Logga in på den lokala hanteringskonsolens CLI med Autentiseringsuppgifter **för CyberX** **eller Support.**

1. Ange `sudo cyberx-management-tunnel-enable`.

1. Välj **Retur**.

1. Ange `--port 10000`.

### <a name="next-steps"></a>Nästa steg

[Konfigurera ditt nätverk](how-to-set-up-your-network.md)