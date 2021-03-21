---
title: Konfigurera en Azure Migrate-apparat för fysiska servrar
description: Lär dig hur du konfigurerar en Azure Migrate-apparat för fysisk server-utvärdering.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 04/15/2020
ms.openlocfilehash: 73c3d529978c91946632ed599f02b8938830621e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97705327"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Konfigurera en installation för fysiska servrar

I den här artikeln beskrivs hur du konfigurerar Azure Migrate-enheten om du utvärderar fysiska servrar med verktyget Azure Migrate: Server bedömning.

Azure Migrate-installationen är en förenklad installation som används av Azure Migrate Server bedömning för att göra följande:

- Identifiera lokala servrar.
- Skicka metadata och prestanda data för identifierade servrar till Azure Migrate Server bedömning.

[Läs mer](migrate-appliance.md) om Azure Migrate-enheten.


## <a name="appliance-deployment-steps"></a>Distributions steg för installationen

Så här konfigurerar du den apparat som du:
- Ange ett namn på apparaten och generera en Azure Migrate projekt nyckel i portalen.
- Ladda ned en zippad fil med Azure Migrate Installer-skript från Azure Portal.
- Extrahera innehållet från den zippade filen. Starta PowerShell-konsolen med administratörs behörighet.
- Kör PowerShell-skriptet för att starta webb programmet för installationen.
- Konfigurera enheten för första gången och registrera den med det Azure Migrate projektet med hjälp av Azure Migrate projekt nyckeln.

### <a name="generate-the-azure-migrate-project-key"></a>Generera Azure Migrate projekt nyckel

1. I **Migreringsmål** > **Servrar** > **Azure Migrate: Serverutvärdering** väljer du **Identifiera**.
2. I **identifiera datorer**  >  **är dina datorer virtualiserade?**, Välj **fysiska eller andra (AWS, GCP, Xen osv.)**.
3. I **1: generera Azure Migrate projekt nyckel** anger du ett namn för Azure Migrate-installationen som ska konfigureras för identifiering av fysiska eller virtuella servrar. Namnet måste vara alfanumeriskt med 14 tecken eller färre.
1. Klicka på **generera nyckel** för att starta skapandet av de nödvändiga Azure-resurserna. Stäng inte sidan identifiera datorer när du skapar resurser.
1. När Azure-resurserna har skapats skapas en **Azure Migrate projekt nyckel** .
1. Kopiera nyckeln på samma sätt som du behöver den för att slutföra registreringen av enheten under konfigurationen.

### <a name="download-the-installer-script"></a>Ladda ned installations skriptet

I **2: Ladda ned Azure Migrate-enheten** klickar du på **Hämta**.

   ![Val för identifiering av datorer](./media/tutorial-assess-physical/servers-discover.png)


   ![Val för generera nyckel](./media/tutorial-assess-physical/generate-key-physical.png)

### <a name="verify-security"></a>Verifiera säkerhet

Kontrol lera att den zippade filen är säker innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera hashen för den zippade filen:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel på användning för offentligt moln: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - Exempel på användning av myndighets moln: ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5 ```
3.  Kontrol lera den senaste versionen av installationen och inställningarna för [hash-värden](tutorial-discover-physical.md#verify-security) .
 

## <a name="run-the-azure-migrate-installer-script"></a>Kör installations skriptet för Azure Migrate
Installations skriptet gör följande:

- Installerar agenter och ett webb program för identifiering och utvärdering av fysiska servrar.
- Installera Windows-roller, inklusive Windows Activation Service, IIS och PowerShell ISE.
- Hämta och installera en skrivbar IIS-modul. [Läs mer](https://www.microsoft.com/download/details.aspx?id=7435).
- Uppdaterar en register nyckel (HKLM) med beständig inställnings information för Azure Migrate.
- Skapar följande filer under sökvägen:
    - **Config-filer**:%programdata%\Microsoft Azure\Config
    - **Loggfiler**:%programdata%\Microsoft Azure\Logs

Kör skriptet på följande sätt:

1. Extrahera den zippade filen till en mapp på den server som ska vara värd för-enheten.  Kontrol lera att du inte kör skriptet på en dator på en befintlig Azure Migrate-installation.
2. Starta PowerShell på servern med administratörs behörighet (förhöjt).
3. Ändra PowerShell-katalogen till den mapp där innehållet har extraherats från den hämtade zippade filen.
4. Kör skriptet med namnet **AzureMigrateInstaller.ps1** genom att köra följande kommando:

    - För det offentliga molnet: 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```
    - För Azure Government: 
    
        ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```

    Skriptet startar webb programmet för installationen när det har slutförts.

Om du kommer över alla problem kan du komma åt skript loggarna på C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log för fel sökning.



### <a name="verify-appliance-access-to-azure"></a>Verifiera åtkomst till enheten till Azure

Se till att den virtuella datorns virtuella datorer kan ansluta till Azure-URL: er för [offentliga](migrate-appliance.md#public-cloud-urls) och [offentliga](migrate-appliance.md#government-cloud-urls) moln.

### <a name="configure-the-appliance"></a>Konfigurera installationen

Konfigurera enheten för första gången.

1. Öppna en webbläsare på vilken dator som helst som kan ansluta till installationen och öppna URL: en för installations programmets webbapp: **https://*-enhetens namn eller IP-adress*: 44368**.

   Alternativt kan du öppna appen från Skriv bordet genom att klicka på genvägen till appen.
2. Godkänn **licens villkoren** och Läs informationen från tredje part.
1. I webbappen > **Konfigurera krav** gör du följande:
    - **Anslutning**: appen kontrollerar att servern är ansluten till Internet. Om servern använder en proxyserver:
        - Klicka på **Konfigurera proxy** till och ange proxyadress (i formuläret http://ProxyIPAddress eller http://ProxyFQDN) lyssnande port.
        - Ange autentiseringsuppgifter om proxyn kräver autentisering.
        - Endast HTTP-proxy stöds.
        - Om du har lagt till proxyinformation eller inaktiverat proxyn och/eller autentiseringen, klickar du på **Spara** för att utlösa anslutnings kontrollen igen.
    - **Tidssynkronisering**: tiden har verifierats. Tiden för installationen bör vara synkroniserad med Internet tid för att Server identifieringen ska fungera korrekt.
    - **Installera uppdateringar**: Azure Migrate Server Assessment kontrollerar att installations programmet har de senaste uppdateringarna installerade. När kontrollen är klar kan du klicka på **Visa apparat-tjänster** för att se status och versioner för komponenterna som körs på produkten.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrera enheten med Azure Migrate

1. Klistra in **Azure Migrate projekt nyckeln** som har kopierats från portalen. Om du inte har nyckeln går du till **Server utvärdering> identifiera> hantera befintliga apparater**, väljer det installations namn som du angav vid tidpunkten för att generera nyckeln och kopierar motsvarande nyckel.
1. Du behöver en enhets kod för att autentisera med Azure. När du klickar på **Logga in** öppnas en modal enhets kod som visas nedan.

    ![Modal visar enhets koden](./media/tutorial-discover-vmware/device-code.png)

1. Klicka på **Kopiera kod & inloggning** för att kopiera enhets koden och öppna en Azure-inloggnings tolk i en ny webbläsare-flik. Om den inte visas kontrollerar du att du har inaktiverat blockering av popup-fönster i webbläsaren.
1. På fliken nytt klistrar du in enhets koden och loggar in med ditt användar namn och lösen ord för Azure.
   
   Inloggning med en PIN-kod stöds inte.
3. Om du stänger fliken inloggning oavsiktligt utan att logga in, måste du uppdatera fliken webbläsare i Konfigurations hanteraren för att aktivera inloggnings knappen igen.
1. När du har loggat in går du tillbaka till föregående flik med installationen av Konfigurations hanteraren.
4. Om Azure-användarkontot som används för loggning har rätt [behörigheter](./tutorial-discover-physical.md) för de Azure-resurser som skapades under den här nyckeln, initieras registrerings enheten.
1. När installationen av enheten har registrerats kan du se registrerings informationen genom att klicka på **Visa information**.


## <a name="start-continuous-discovery"></a>Starta kontinuerlig identifiering

Anslut nu från installationen till de fysiska servrarna som ska identifieras och starta identifieringen.

1. I **steg 1: ange autentiseringsuppgifter för identifiering av fysiska och virtuella Windows-och Linux-servrar**, klickar du på **Lägg till autentiseringsuppgifter**.
1. För Windows Server väljer du typ av källa som **Windows Server**, anger ett eget namn för autentiseringsuppgifter, lägger till användar namn och lösen ord. Klicka på **Spara**.
1. Om du använder lösenordsbaserad autentisering för Linux-server väljer du käll typen som **Linux-server (lösenordsbaserad)**, anger ett eget namn för autentiseringsuppgifter, lägger till användar namn och lösen ord. Klicka på **Spara**.
1. Om du använder SSH-baserad autentisering för Linux-server kan du välja källtyp som **Linux-server (SSH-nyckelbaserad)**, ange ett eget namn för autentiseringsuppgifter, lägga till användar namnet, bläddra och välja filen med den privata SSH-nyckeln. Klicka på **Spara**.

    - Azure Migrate stöder den privata SSH-nyckeln som genereras av ssh-keygen-kommandot med RSA-, DSA-, ECDSA-och ed25519-algoritmer.
    - För närvarande har Azure Migrate inte stöd för en lösen fras baserad SSH-nyckel. Använd en SSH-nyckel utan lösen fras.
    - För närvarande stöder Azure Migrate inte den privata SSH-nyckelfilen som genereras av SparaTillFil.
    - Azure Migrate stöder OpenSSH-formatet för den privata SSH-nyckelfilen som visas nedan:
    
    ![Format som stöds av SSH privat nyckel](./media/tutorial-discover-physical/key-format.png)
1. Om du vill lägga till flera autentiseringsuppgifter samtidigt klickar du på **Lägg till fler** för att spara och lägga till fler autentiseringsuppgifter. Flera autentiseringsuppgifter stöds för identifiering av fysiska servrar.
1. I **steg 2: Ange information om fysiska eller virtuella servrar** klickar du på **Lägg till identifierings källa** för att ange serverns **IP-adress/FQDN** och det egna namnet för autentiseringsuppgifter för att ansluta till servern.
1. Du kan antingen **lägga till ett enskilt objekt** i taget eller **lägga till flera objekt** i taget. Det finns också ett alternativ för att tillhandahålla Server information via **importera CSV**.

    ![Val för att lägga till identifierings källa](./media/tutorial-assess-physical/add-discovery-source-physical.png)

    - Om du väljer **Lägg till enstaka objekt** kan du välja typ av operativ system, ange ett eget namn för autentiseringsuppgifter, lägga till serverns **IP-adress/FQDN** och klicka på **Spara**.
    - Om du väljer **Lägg till flera objekt** kan du lägga till flera poster samtidigt genom att ange serverns **IP-adress/FQDN** med det egna namnet för autentiseringsuppgifter i text rutan. **Verifiera** de tillagda posterna och klicka på **Spara**.
    - Om du väljer **importera CSV** _(vald som standard)_ kan du ladda ned en CSV-mallfil, fylla i filen med serverns **IP-adress/FQDN** och eget namn för autentiseringsuppgifter. Sedan kan du importera filen till enheten, **Verifiera** posterna i filen och klicka på **Spara**.

1. När du klickar på Spara kommer installations programmet att försöka verifiera anslutningen till de servrar som lagts till och visa **verifierings status** i tabellen mot varje server.
    - Om verifieringen Miss lyckas för en server kan du granska felet genom att klicka på **verifieringen misslyckades** i kolumnen Status i tabellen. Åtgärda problemet och verifiera igen.
    - Klicka på **ta bort** om du vill ta bort en server.
1. Du kan **validera** anslutningen till servrar varje gång innan du påbörjar identifieringen.
1. Klicka på **Starta identifiering** för att starta identifiering av verifierade servrar. När identifieringen har påbörjats kan du kontrol lera identifierings statusen mot varje server i tabellen.


Detta startar identifieringen. Det tar ungefär 2 minuter per server för metadata om identifierad server som visas i Azure Portal.

## <a name="verify-servers-in-the-portal"></a>Verifiera servrarna i portalen

När identifieringen är klar kan du kontrol lera att servrarna visas i portalen.

1. Öppna instrumentpanelen för Azure Migrate.
2. På sidan **Azure Migrate-servrar**  >  **Azure Migrate: Server utvärdering** klickar du på ikonen som visar antalet för **identifierade servrar**.


## <a name="next-steps"></a>Nästa steg

Testa [utvärderingen av fysiska servrar](tutorial-assess-physical.md) med Azure Migrate Server-utvärdering.