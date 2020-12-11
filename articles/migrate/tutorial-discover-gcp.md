---
title: Identifiera GCP VM-instanser med Azure Migrate Server-utvärdering
description: Lär dig hur du identifierar GCP VM-instanser med Azure Migrate Server-utvärdering.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: 767617833789c71bfc2ecfc2d518166f8bcee6c6
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109529"
---
# <a name="tutorial-discover-google-cloud-platform-gcp-instances-with-server-assessment"></a>Självstudie: identifiera Google Cloud Platform-instanser (GCP) med Server utvärdering

Som en del av migreringen till Azure identifierar du dina servrar för utvärdering och migrering.

Den här självstudien visar hur du identifierar Google Cloud Platform-instanser (GCP) med verktyget Azure Migrate: Server bedömning med en förenklad Azure Migrate-installation. Du distribuerar installationen på en GCP VM-instans för att kontinuerligt identifiera dator-och prestanda-metadata.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera ett Azure-konto.
> * Förbered GCP VM-instanser för identifiering.
> * Skapa ett Azure Migrate-projekt.
> * Konfigurera Azure Migrate-enheten.
> * Starta kontinuerlig identifiering.

> [!NOTE]
> Självstudier visar den snabbaste sökvägen för att testa ett scenario och använda standard alternativ.  

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Innan du påbörjar den här självstudien måste du kontrol lera att du har dessa krav på plats.

**Krav** | **Information**
--- | ---
**Enhet** | Du behöver en GCP VM-instans där Azure Migrates apparaten ska köras. Datorn ska ha:<br/><br/> – Windows Server 2016 installerat. Det finns inte stöd för att köra installationen på en dator med Windows Server 2019.<br/><br/> – 16 GB RAM, 8 virtuella processorer, cirka 80 GB disk lagring och en extern virtuell växel.<br/><br/> – En statisk eller dynamisk IP-adress, med Internet åtkomst, antingen direkt eller via en proxyserver.
**Instanser av Windows VM** | Tillåt inkommande anslutningar på WinRM-port 5985 (HTTP), så att enheten kan hämta konfigurations-och prestanda-metadata.
**VIRTUELLA Linux-instanser** | Tillåt inkommande anslutningar på port 22 (TCP).

## <a name="prepare-an-azure-user-account"></a>Förbereda ett Azure-användarkonto

Om du vill skapa ett Azure Migrate-projekt och registrera Azure Migrate-enheten måste du ha ett konto med:
- Deltagar-eller ägar behörigheter för en Azure-prenumeration.
- Behörighet att registrera Azure Active Directory appar.

Om du nyligen skapade ett kostnadsfritt Azure-konto är du ägare av prenumerationen. Om du inte är prenumerations ägare kan du arbeta med ägaren för att tilldela behörigheterna på följande sätt:

1. I Azure Portal söker du efter "prenumerationer" och under **tjänster** väljer du **prenumerationer**.

    ![Sök i rutan för att söka efter Azure-prenumerationen](./media/tutorial-discover-gcp/search-subscription.png)

2. På sidan **prenumerationer** väljer du den prenumeration där du vill skapa ett Azure Migrate-projekt. 
3. I prenumerationen väljer du **åtkomst kontroll (IAM)**  >  **kontrol lera åtkomst**.
4. I **kontrol lera åtkomst** söker du efter det relevanta användar kontot.
5. I **Lägg till en roll tilldelning** klickar du på **Lägg till**.

    ![Sök efter ett användar konto för att kontrol lera åtkomst och tilldela en roll](./media/tutorial-discover-gcp/azure-account-access.png)

6. I **Lägg till roll tilldelning** väljer du rollen deltagare eller ägare och väljer kontot (azmigrateuser i vårt exempel). Klicka sedan på **Spara**.

    ![Öppnar sidan Lägg till roll tilldelning för att tilldela kontot en roll](./media/tutorial-discover-gcp/assign-role.png)

7. I portalen söker du efter användare och under **tjänster** väljer **du användare**.
8. I **användar inställningar** kontrollerar du att Azure AD-användare kan registrera program (anges till **Ja** som standard).

    ![Verifiera i användar inställningar som användare kan registrera Active Directory appar](./media/tutorial-discover-gcp/register-apps.png)


## <a name="prepare-gcp-instances"></a>Förbereda GCP-instanser

Konfigurera ett konto som kan användas av enheten för att komma åt GCP VM-instanser.

- För Windows-servrar
    - Konfigurera ett lokalt användar konto på datorer som inte är domänanslutna och ett domän konto på icke-domänanslutna datorer som du vill ska ingå i identifieringen. Lägg till användar kontot i följande grupper: 
        - Fjärrhanteringsanvändare
        - Användare av prestanda övervakning
        - Prestanda loggar användare.
- För Linux-servrar:
    - Du behöver ett rot konto på de Linux-servrar som du vill identifiera. Om du inte kan ange ett rot konto läser du instruktionerna i [support mat ris](migrate-support-matrix-physical.md#physical-server-requirements) för ett alternativ.
    - Azure Migrate använder lösenordsautentisering vid identifiering av AWS-instanser. AWS-instanser har inte stöd för lösenordsautentisering som standard. Innan du kan identifiera instansen måste du aktivera lösenordsautentisering.
        1. Logga in på varje Linux-dator.
        2. Öppna filen sshd_config: vi/etc/ssh/sshd_config
        3. Leta upp raden **PasswordAuthentication** i filen och ändra värdet till **Ja**.
        4. Spara filen och Stäng den. Starta om SSH-tjänsten.
    - Om du använder en rot användare för att identifiera dina virtuella Linux-datorer, måste du kontrol lera att rot inloggningen är tillåten på de virtuella datorerna.
        1. Logga in på varje Linux-dator
        2. Öppna filen sshd_config: vi/etc/ssh/sshd_config
        3. Leta upp raden **PermitRootLogin** i filen och ändra värdet till **Ja**.
        4. Spara filen och Stäng den. Starta om SSH-tjänsten.

## <a name="set-up-a-project"></a>Konfigurera ett projekt

Skapa ett nytt Azure Migrate-projekt.

1. I Azure-portalen > **Alla tjänster** söker du efter **Azure Migrate**.
2. Under **Tjänster** väljer du **Azure Migrate**.
3. I **Översikt** väljer du **skapa projekt**.
5. I **skapa projekt** väljer du din Azure-prenumeration och resurs grupp. Skapa en resurs grupp om du inte har någon.
6. I **projekt information** anger du projekt namnet och geografin som du vill skapa projektet i. Granska stödda geografiska områden för [offentliga](migrate-support-matrix.md#supported-geographies-public-cloud) och [offentliga moln](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Rutor för projekt namn och region](./media/tutorial-discover-gcp/new-project.png)

7. Välj **Skapa**.
8. Vänta några minuter tills Azure Migrate-projektet har distribuerats.

Verktyget **Azure Migrate: Server bedömning** läggs till som standard i det nya projektet.

![Sida som visar verktyget för Server bedömning som har lagts till som standard](./media/tutorial-discover-gcp/added-tool.png)

## <a name="set-up-the-appliance"></a>Konfigurera installationen

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
3. I **1: generera Azure Migrate projekt nyckel** anger du ett namn för Azure Migrate-installationen som ska konfigureras för identifiering av dina virtuella GCP-servrar. Namnet måste vara alfanumeriskt med 14 tecken eller färre.
4. Klicka på **generera nyckel** för att starta skapandet av de nödvändiga Azure-resurserna. Stäng inte sidan identifiera datorer när du skapar resurser.
5. När Azure-resurserna har skapats skapas en **Azure Migrate projekt nyckel** .
6. Kopiera nyckeln på samma sätt som du behöver den för att slutföra registreringen av enheten under konfigurationen.

### <a name="download-the-installer-script"></a>Ladda ned installations skriptet

I **2: Ladda ned Azure Migrate-enheten** klickar du på **Hämta**.


### <a name="verify-security"></a>Verifiera säkerhet

Kontrol lera att den zippade filen är säker innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera hashen för den zippade filen:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel på användning för offentligt moln: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - Exempel på användning av myndighets moln: ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256 ```
3.  Kontrol lera de senaste versions-och hash-värdena för produkten:
    - För det offentliga molnet:

        **Scenario** | **Ladda ned** | **Hash-värde**
        --- | --- | ---
        Fysisk (85 MB) | [Senaste version](https://go.microsoft.com/fwlink/?linkid=2140334) | ce5e6f0507936def8020eb7b3109173dad60fc51dd39c3bd23099bc9baaabe29

    - För Azure Government:

        **Scenario** | **Ladda ned** | **Hash-värde**
        --- | --- | ---
        Fysisk (85 MB) | [Senaste version](https://go.microsoft.com/fwlink/?linkid=2140338) | ae132ebc574caf231bf41886891040ffa7abbe150c8b50436818b69e58622276
 

### <a name="run-the-azure-migrate-installer-script"></a>Kör installations skriptet för Azure Migrate
Installations skriptet gör följande:

- Installerar agenter och ett webb program för identifiering och utvärdering av GCP-servrar.
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
1. Klicka på **Logga** in. En Azure-inloggning visas i en ny flik i webbläsaren. Om den inte visas kontrollerar du att du har inaktiverat blockering av popup-fönster i webbläsaren.
1. På fliken nytt loggar du in med ditt användar namn och lösen ord för Azure.
   
   Inloggning med en PIN-kod stöds inte.
3. När du har loggat in går du tillbaka till webbappen. 
4. Om Azure-användarkontot som används för loggning har rätt [behörigheter](#prepare-an-azure-user-account) för de Azure-resurser som skapades under den här nyckeln, initieras registrerings enheten.
5. När installationen av enheten har registrerats kan du se registrerings informationen genom att klicka på **Visa information**.


## <a name="start-continuous-discovery"></a>Starta kontinuerlig identifiering

Anslut nu från installationen till GCP-servrarna och starta identifieringen.

1. I **steg 1: ange autentiseringsuppgifter för identifiering av fysiska och virtuella Windows-och Linux-servrar**, klickar du på **Lägg till autentiseringsuppgifter**.
1. För Windows Server väljer du typ av källa som **Windows Server**, anger ett eget namn för autentiseringsuppgifter, lägger till användar namn och lösen ord. Klicka på **Spara**.
1. Om du använder lösenordsbaserad autentisering för Linux-server väljer du käll typen som **Linux-server (lösenordsbaserad)**, anger ett eget namn för autentiseringsuppgifter, lägger till användar namn och lösen ord. Klicka på **Spara**.
1. Om du använder SSH-baserad autentisering för Linux-server kan du välja källtyp som **Linux-server (SSH-nyckelbaserad)**, ange ett eget namn för autentiseringsuppgifter, lägga till användar namnet, bläddra och välja filen med den privata SSH-nyckeln. Klicka på **Spara**.

    - Azure Migrate stöder den privata SSH-nyckeln som genereras av ssh-keygen-kommandot med RSA-, DSA-, ECDSA-och ed25519-algoritmer.
    - För närvarande har Azure Migrate inte stöd för en lösen fras baserad SSH-nyckel. Använd en SSH-nyckel utan lösen fras.
    - För närvarande stöder Azure Migrate inte den privata SSH-nyckelfilen som genereras av SparaTillFil.

2. Om du vill lägga till flera autentiseringsuppgifter samtidigt klickar du på **Lägg till fler** för att spara och lägga till fler autentiseringsuppgifter. 
3. I **steg 2: Ange information om fysiska eller virtuella servrar** klickar du på **Lägg till identifierings källa** för att ange serverns **IP-adress/FQDN** och det egna namnet för autentiseringsuppgifter för att ansluta till servern.
4. Du kan antingen **lägga till ett enskilt objekt** i taget eller **lägga till flera objekt** i taget. Det finns också ett alternativ för att tillhandahålla Server information via **importera CSV**.

    - Om du väljer **Lägg till enstaka objekt** kan du välja typ av operativ system, ange ett eget namn för autentiseringsuppgifter, lägga till serverns **IP-adress/FQDN** och klicka på **Spara**.
    - Om du väljer **Lägg till flera objekt** kan du lägga till flera poster samtidigt genom att ange serverns **IP-adress/FQDN** med det egna namnet för autentiseringsuppgifter i text rutan. **Verifiera** de tillagda posterna och klicka på **Spara**.
    - Om du väljer **importera CSV** _(vald som standard)_ kan du ladda ned en CSV-mallfil, fylla i filen med serverns **IP-adress/FQDN** och eget namn för autentiseringsuppgifter. Sedan kan du importera filen till enheten, **Verifiera** posterna i filen och klicka på **Spara**.

5. När du klickar på Spara kommer installations programmet att försöka verifiera anslutningen till de servrar som lagts till och visa **verifierings status** i tabellen mot varje server.
    - Om verifieringen Miss lyckas för en server kan du granska felet genom att klicka på **verifieringen misslyckades** i kolumnen Status i tabellen. Åtgärda problemet och verifiera igen.
    - Klicka på **ta bort** om du vill ta bort en server.
6. Du kan **validera** anslutningen till servrar varje gång innan du påbörjar identifieringen.
7. Klicka på **Starta identifiering** för att starta identifiering av verifierade servrar. När identifieringen har påbörjats kan du kontrol lera identifierings statusen mot varje server i tabellen.


Detta startar identifieringen. Det tar ungefär 2 minuter per server för metadata om identifierad server som visas i Azure Portal.

## <a name="verify-servers-in-the-portal"></a>Verifiera servrarna i portalen

När identifieringen är klar kan du kontrol lera att servrarna visas i portalen.

1. Öppna instrumentpanelen för Azure Migrate.
2. På sidan **Azure Migrate-servrar**  >  **Azure Migrate: Server utvärdering** klickar du på ikonen som visar antalet för **identifierade servrar**.

## <a name="next-steps"></a>Nästa steg

- [Utvärdera GCP-servrar](tutorial-assess-gcp.md) för migrering till virtuella Azure-datorer.
- [Granska de data](migrate-appliance.md#collected-data---physical) som enheten samlar in under identifieringen.
