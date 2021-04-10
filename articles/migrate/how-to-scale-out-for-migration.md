---
title: Konfigurera en Azure Migrate skalbar apparat för migrering utan agent
description: Lär dig hur du konfigurerar en skalbar installation av Azure Migrate för att migrera virtuella Hyper-V-datorer.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: 5ca821cb4f85deb77595e4a9029cc10298dbb884
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105611981"
---
# <a name="scale-agentless-migration-of-vmware-virtual-machines-to-azure"></a>Skala agent lös migrering av virtuella VMware-datorer till Azure

Den här artikeln hjälper dig att förstå hur du använder en skalbar installation för att migrera ett stort antal virtuella VMware-datorer till Azure med hjälp av verktyget för migrering av Azure Migrate-Migreringsverktyg för virtuella VMware-datorer.

Med hjälp av metoden för att migrera virtuella VMware-datorer kan du:

- Replikera upp till 300 virtuella datorer från en enda vCenter-Server samtidigt med en Azure Migrate-apparat.
- Replikera upp till 500 virtuella datorer från en enda vCenter-Server samtidigt genom att distribuera en andra skalnings apparat för migrering.

I den här artikeln får du lära dig att:

- Lägg till en skalbar installation för utan agent migrering av virtuella VMware-datorer
- Migrera upp till 500 virtuella datorer samtidigt med den skalbara enheten.

##  <a name="prerequisites"></a>Förutsättningar

Innan du börjar måste du utföra följande steg:

- Skapa Azure Migrate-projektet.
- Distribuera Azure Migrate-installationen (den primära installationen) och fullständig identifiering av virtuella VMware-datorer som hanteras av vCenter-servern.
- Konfigurera replikering för en eller flera virtuella datorer som ska migreras.
> [!IMPORTANT]
> Du måste ha minst en replikerad virtuell dator i projektet innan du kan lägga till en skalbar installation för migrering.

Läs självstudien om hur du utför ovanstående genom att [migrera virtuella VMware-datorer till Azure med metoden för migrering utan agent](./tutorial-migrate-vmware.md).

## <a name="deploy-a-scale-out-appliance"></a>Distribuera en skalbar apparat

Om du vill lägga till en skalbar installation följer du stegen som anges nedan:

1. Klicka på **identifiera**  >  **dina datorer virtualiserade?** 
1. Välj **Ja, med VMware vSphere hypervisor.**
1. Välj replikering utan agent i nästa steg.
1. Välj **skala ut en befintlig primär apparat** på menyn Välj typ av installation.
1. Välj den primära installationen (enheten som använder den identifiering som utfördes) som du vill skala ut.

:::image type="content" source="./media/how-to-scale-out-for-migration/add-scale-out.png" alt-text="Sidan identifiera datorer för att bygga ut onboarding":::

### <a name="1-generate-the-azure-migrate-project-key"></a>1. generera Azure Migrate projekt nyckeln

1. I **generera Azure Migrate projekt nyckel** anger du ett suffix för den skalbara enheten. Suffixet får bara innehålla alfanumeriska tecken och får innehålla högst 14 tecken.
2. Klicka på **generera nyckel** för att starta skapandet av de nödvändiga Azure-resurserna. Stäng inte sidan identifiera när du skapar resurser.
3. Kopiera den genererade nyckeln. Du behöver nyckeln senare för att kunna slutföra registreringen av den skalbara installationen.

### <a name="2-download-the-installer-for-the-scale-out-appliance"></a>2. Ladda ned installations programmet för den skalbara produkten

I **Ladda ned Azure Migrate**-installationen klickar du på  **Hämta**. Du måste hämta PowerShell-installationsprogrammet för att distribuera den skalbara installationen på en befintlig server som kör Windows Server 2016 och med den nödvändiga maskin varu konfigurationen (32 GB RAM, 8 virtuella processorer, cirka 80 GB disk lagring och Internet åtkomst, antingen direkt eller via en proxyserver).
:::image type="content" source="./media/how-to-scale-out-for-migration/download-scale-out.png" alt-text="Ladda ned skript för skalbar installation":::

> [!TIP]
> Du kan verifiera kontroll summan för den hämtade ZIP-filen med hjälp av följande steg:
>
> 1. Öppna kommando tolken som administratör
> 2. Kör följande kommando för att generera hashen för den zippade filen:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel på användning för offentligt moln: ```C:\>Get-FileHash -Path .\AzureMigrateInstaller-VMware-Public-Scaleout.zip -Algorithm SHA256 ```
> 3. Ladda ned den senaste versionen av installations programmet för skalbar installation från portalen om det beräknade hash-värdet inte matchar den här strängen: 1E6B6E3EE8B2A800818B925F5DA67EF7874DAD87E32847120B32F3E21F5960F9

### <a name="3-run-the-azure-migrate-installer-script"></a>3. kör installations skriptet för Azure Migrate
Installations skriptet gör följande:

- Installerar Konfigurations hanteraren för gateway-agent och-utrustning för att utföra fler samtidiga Server replikeringar.
- Installera Windows-roller, inklusive Windows Activation Service, IIS och PowerShell ISE.
- Hämta och installera en skrivbar IIS-modul. [Läs mer](https://www.microsoft.com/download/details.aspx?id=7435).
- Uppdaterar en register nyckel (HKLM) med beständig inställnings information för Azure Migrate.
- Skapar följande filer under sökvägen:
    - **Config-filer**:%programdata%\Microsoft Azure\Config
    - **Loggfiler**:%programdata%\Microsoft Azure\Logs

Kör skriptet på följande sätt:

1. Extrahera zip-filen till en mapp på den server som ska vara värd för den skalbara installationen.  Kontrol lera att du inte kör skriptet på en server med en befintlig Azure Migrate-apparat.
2. Starta PowerShell på servern med administratörs behörighet (förhöjt).
3. Ändra PowerShell-katalogen till den mapp där innehållet har extraherats från den hämtade ZIP-filen.
4. Kör skriptet med namnet **AzureMigrateInstaller.ps1**  med hjälp av följande kommando:

    - För det offentliga molnet: 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```

    Skriptet startar Konfigurations hanteraren för installationen när körningen är klar.

Om du kommer över alla problem kan du komma åt skript loggarna på: <br/> C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log för fel sökning.


### <a name="4-configure-the-appliance"></a>4. Konfigurera enheten

Innan du börjar bör du se till att de [här Azure-slutpunkterna](migrate-appliance.md#public-cloud-urls) är tillgängliga från den skalbara produkten.

- Öppna en webbläsare på vilken dator som helst som kan ansluta till den skalbara installations servern och öppna URL: en för installationens konfigurations hanterare: **https://*namn eller IP-adress*: 44368**.

   Alternativt kan du öppna Configuration Manager från den skalbara apparatens servers skriv bord med hjälp av genvägen till Configuration Manager.
- Godkänn **licens villkoren** och Läs informationen från tredje part.
- I Configuration Manager > **Konfigurera krav**, gör du följande:
   - **Anslutning**: installationen kontrollerar att servern är ansluten till Internet. Om servern använder en proxyserver:
     1. Klicka på **Setup proxy** för att ange proxyadress (i formuläret http://ProxyIPAddress eller http://ProxyFQDN) lyssnande port).
     2. Ange autentiseringsuppgifter om proxyn kräver autentisering.
     3. Endast HTTP-proxy stöds.
     4. Om du har lagt till proxyinformation eller inaktiverat proxyn och/eller autentiseringen, klickar du på **Spara** för att utlösa anslutnings kontrollen igen.
   - **Tidssynkronisering**: tiden för installationen bör vara synkroniserad med Internet-tid för att identifieringen ska fungera korrekt.
   - **Installera uppdateringar: installationen** säkerställer att de senaste uppdateringarna är installerade. När kontrollen är klar kan du klicka på **Visa apparat-tjänster** för att se status och versioner för de tjänster som körs på installations servern.
   - **Installera vddk: installations** programmet kontrollerar att VMware vSphere Virtual Disk Development Kit (vddk) har installerats. Om den inte är installerad laddar du ned VDDK 6,7 från VMware och extraherar det hämtade ZIP-innehållet till den angivna platsen på enheten, enligt **anvisningarna i installations anvisningarna** Configuration Manager på skärmen installation av installations programmet.


### <a name="register-the-appliance-with-azure-migrate"></a>Registrera enheten med Azure Migrate

1. Klistra in **Azure Migrate projekt nyckeln** som har kopierats från portalen. Om du inte har nyckeln går du till **Server utvärdering> identifiera> hantera befintliga apparater**, väljer namnet på den primära enheten, letar upp den skalbara installation som är kopplad till den och kopierar motsvarande nyckel.
1. Du behöver en enhets kod för att autentisera med Azure. När du klickar på **Logga in** öppnas en modal enhets kod som visas nedan.
:::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="Modal visar enhets koden":::

1. Klicka på **Kopiera kod & inloggning** för att kopiera enhets koden och öppna en Azure-inloggnings tolk i en ny webbläsare-flik. Om den inte visas kontrollerar du att du har inaktiverat blockering av popup-fönster i webbläsaren.
1. På fliken nytt klistrar du in enhets koden och loggar in med ditt användar namn och lösen ord för Azure.
   
   Inloggning med en PIN-kod stöds inte.
3. Om du stänger fliken inloggning oavsiktligt utan att logga in, måste du uppdatera fliken webbläsare i Konfigurations hanteraren för att aktivera inloggnings knappen igen.
1. När du har loggat in går du tillbaka till föregående flik med installationen av Konfigurations hanteraren för enheten.
1. Om Azure-användarkontot som används för loggning har rätt behörigheter för de Azure-resurser som skapades under den här nyckeln, initieras registrerings enheten.
:::image type="content" source="./media/how-to-scale-out-for-migration/registration-scale-out.png" alt-text="Panel 2 på installationens konfigurations hanterare":::

#### <a name="import-appliance-configuration-from-primary-appliance"></a>Importera enhets konfiguration från primär apparat

Slutför registreringen av den skalbara installationen genom att klicka på **Importera** för att hämta de nödvändiga konfigurationsfilerna från den primära enheten.

1. Om du klickar på **Importera** öppnas ett popup-fönster med instruktioner om hur du importerar nödvändiga konfigurationsfiler från den primära installationen.
:::image type="content" source="./media/how-to-scale-out-for-migration/import-modal-scale-out.png" alt-text="Import konfiguration modal":::
1. Logga in (fjärr skrivbord) till den primära installationen och kör följande PowerShell-kommandon:

    ``` PS cd 'C:\Program Files\Microsoft Azure Appliance Configuration Manager\Scripts\PowerShell' ```
    
    ``` PS .\ExportConfigFiles.ps1 ```

1. Kopiera zip-filen som skapades genom att köra kommandona ovan till den skalbara enheten. Zip-filen innehåller konfigurationsfiler som krävs för att registrera den skalbara installationen.
1. I popup-fönstret som öppnas i föregående steg väljer du platsen för den kopierade zip-filen och klickar på **Spara**.

När filerna har importer ATS slutförs registreringen av den skalbara installationen och du får tidsstämpeln för den senaste lyckade importen. Du kan också se registrerings informationen genom att klicka på **Visa information**.
:::image type="content" source="./media/how-to-scale-out-for-migration/import-success.png" alt-text="Skärm bild som visar att du kan registrera skalbar utrustning med Azure Migrate projekt.":::

Nu bör du kontrol lera att den skalbara enheten kan ansluta till vCenter-servern. Verifiera vCenter Server anslutningen från en skalbar installation genom att klicka på **validera** igen.
:::image type="content" source="./media/how-to-scale-out-for-migration/view-sources.png" alt-text="Skärm bild som visar visnings uppgifter och identifierings källor som ska verifieras.":::

> [!IMPORTANT]
> Om du redigerar vCenter Server autentiseringsuppgifter på den primära installationen, se till att du importerar konfigurationsfilerna igen till den skalbara enheten för att få den senaste konfigurationen och fortsätta att utföra pågående replikeringar.<br/> Om du inte behöver den skalbara installationen längre, se till att du inaktiverar den skalbara enheten. [**Läs mer**](./common-questions-appliance.md) om hur du inaktiverar den skalbara installationen när det inte behövs.

## <a name="replicate"></a>Replikera

1. När den skalbara installationen har registrerats klickar du på **Replikera** i panelen Azure Migrate: Server-migrering.

2.  Följ anvisningarna på skärmen för att starta replikeringen av fler virtuella datorer. 

Med den skalbara enheten på plats kan du nu replikera 500 virtuella datorer samtidigt. Du kan också migrera virtuella datorer i batchar med 200 via Azure Portal.

Verktyget för migrering av Azure Migrate server tar hand om distribution av de virtuella datorerna mellan den primära och skalbara enheten för replikering. När replikeringen är färdig kan du migrera de virtuella datorerna.

> [!TIP]
> Vi rekommenderar att du migrerar virtuella datorer i batchar med 200 för optimala prestanda om du vill migrera ett stort antal virtuella datorer.
  
## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig:
- Så här konfigurerar du en skalbar apparat
- Så här replikerar du virtuella datorer med en skalbar utrustning


[Lär dig mer](./tutorial-migrate-vmware.md) om att migrera servrar till Azure med hjälp av Azure Migrate: Migreringsverktyg för Server.