---
title: Identifiera AWS-instanser med identifiering och utvärdering av Azure Migrate
description: Lär dig hur du identifierar AWS-instanser med Azure Migrate identifiering och utvärdering.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/11/2021
ms.custom: mvc
ms.openlocfilehash: 295cd5a6831cb64d146bb92bca74b82ff7ab29df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104771489"
---
# <a name="tutorial-discover-aws-instances-with-azure-migrate-discovery-and-assessment"></a>Självstudie: identifiera AWS-instanser med Azure Migrate: identifiering och utvärdering

Som en del av migreringen till Azure identifierar du dina servrar för utvärdering och migrering.

Den här självstudien visar hur du identifierar Amazon Web Services-instanser (AWS) med verktyget Azure Migrate: identifiering och bedömning med en förenklad Azure Migrate-installation. Du distribuerar installationen som en fysisk server för att kontinuerligt identifiera metadata för dator och prestanda.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera ett Azure-konto.
> * Förbered AWS-instanser för identifiering.
> * Skapa ett projekt.
> * Konfigurera Azure Migrate-enheten.
> * Starta kontinuerlig identifiering.

> [!NOTE]
> Självstudier visar den snabbaste sökvägen för att testa ett scenario och använda standard alternativ.  

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Innan du påbörjar den här självstudien måste du kontrol lera att du har dessa krav på plats.

**Krav** | **Information**
--- | ---
**Enhet** | Du behöver en virtuell EC2-dator för att köra Azure Migrate-installationen. Datorn ska ha:<br/><br/> – Windows Server 2016 installerat.<br/> _Det finns inte stöd för att köra installationen på en dator med Windows Server 2019_.<br/><br/> – 16 GB RAM, 8 virtuella processorer, cirka 80 GB disk lagring och en extern virtuell växel.<br/><br/> – En statisk eller dynamisk IP-adress, med Internet åtkomst, antingen direkt eller via en proxyserver.
**Windows-instanser** | Tillåt inkommande anslutningar på WinRM-port 5985 (HTTP), så att enheten kan hämta konfigurations-och prestanda-metadata.
**Linux-instanser** | Tillåt inkommande anslutningar på port 22 (TCP).<br/><br/> Instanserna bör använda `bash` som standard gränssnitt, annars Miss känner identifieringen.

## <a name="prepare-an-azure-user-account"></a>Förbereda ett Azure-användarkonto

Om du vill skapa ett projekt och registrera Azure Migrate-enheten behöver du ett konto med:

* Deltagar-eller ägar behörigheter för en Azure-prenumeration.
* Behörighet att registrera Azure Active Directory-appar (AAD).

Om du nyligen skapade ett kostnadsfritt Azure-konto är du ägare av prenumerationen. Om du inte är prenumerations ägare kan du arbeta med ägaren för att tilldela behörigheterna på följande sätt:

1. I Azure Portal söker du efter "prenumerationer" och under **tjänster** väljer du **prenumerationer**.

    ![Sök i rutan för att söka efter Azure-prenumerationen](./media/tutorial-discover-aws/search-subscription.png)

2. På sidan **prenumerationer** väljer du den prenumeration där du vill skapa ett projekt.
3. I prenumerationen väljer du **åtkomst kontroll (IAM)**  >  **kontrol lera åtkomst**.
4. I **kontrol lera åtkomst** söker du efter det relevanta användar kontot.
5. I **Lägg till en roll tilldelning** klickar du på **Lägg till**.

    ![Sök efter ett användar konto för att kontrol lera åtkomst och tilldela en roll](./media/tutorial-discover-aws/azure-account-access.png)

6. I **Lägg till roll tilldelning** väljer du rollen deltagare eller ägare och väljer kontot (azmigrateuser i vårt exempel). Klicka sedan på **Spara**.

    ![Öppnar sidan Lägg till roll tilldelning för att tilldela kontot en roll](./media/tutorial-discover-aws/assign-role.png)

1. För att registrera installationen behöver ditt Azure-konto **behörighet att registrera AAD-appar.**
1. I Azure Portal navigerar du till **Azure Active Directory**  >  **användares**  >  **användar inställningar**.
1. I **användar inställningar** kontrollerar du att Azure AD-användare kan registrera program (anges till **Ja** som standard).

    ![Verifiera i användar inställningar som användare kan registrera Active Directory appar](./media/tutorial-discover-aws/register-apps.png)

1. Om inställningen "Appregistreringar" är inställd på "nej", ber du klienten/den globala administratören att tilldela den behörighet som krävs. Alternativt kan klient organisationen/den globala administratören tilldela rollen **programutvecklare** till ett konto för att tillåta registrering av AAD-appen. [Läs mer](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-aws-instances"></a>Förbereda AWS-instanser

Konfigurera ett konto som kan användas av enheten för att komma åt AWS-instanser.

- För **Windows-servrar** konfigurerar du ett lokalt användar konto på alla Windows-servrar som du vill ska ingå i identifieringen. Lägg till användar kontot i följande grupper:-fjärr styrnings användare – prestanda övervaknings användare – prestanda loggar användare.
 - För **Linux-servrar** behöver du ett rot konto på de Linux-servrar som du vill identifiera. Se anvisningarna i [support mat ris](migrate-support-matrix-physical.md#physical-server-requirements) för ett alternativ.
- Azure Migrate använder lösenordsautentisering vid identifiering av AWS-instanser. AWS-instanser har inte stöd för lösenordsautentisering som standard. Innan du kan identifiera instansen måste du aktivera lösenordsautentisering.
    - Tillåt WinRM-port 5985 (HTTP) för Windows-servrar. Detta tillåter fjärr-WMI-anrop.
    - För Linux-servrar:
        1. Logga in på varje Linux-dator.
        2. Öppna filen sshd_config: vi/etc/ssh/sshd_config
        3. Leta upp raden **PasswordAuthentication** i filen och ändra värdet till **Ja**.
        4. Spara filen och Stäng den. Starta om SSH-tjänsten.
    - Om du använder en rot användare för att upptäcka Linux-servrar måste du kontrol lera att rot inloggningen är tillåten på servrarna.
        1. Logga in på varje Linux-dator
        2. Öppna filen sshd_config: vi/etc/ssh/sshd_config
        3. Leta upp raden **PermitRootLogin** i filen och ändra värdet till **Ja**.
        4. Spara filen och Stäng den. Starta om SSH-tjänsten.

## <a name="set-up-a-project"></a>Konfigurera ett projekt

Skapa ett nytt projekt.

1. I Azure-portalen > **Alla tjänster** söker du efter **Azure Migrate**.
2. Under **Tjänster** väljer du **Azure Migrate**.
3. I **Översikt** väljer du **skapa projekt**.
5. I **skapa projekt** väljer du din Azure-prenumeration och resurs grupp. Skapa en resurs grupp om du inte har någon.
6. I **projekt information** anger du projekt namnet och geografin som du vill skapa projektet i. Granska stödda geografiska områden för [offentliga](migrate-support-matrix.md#supported-geographies-public-cloud) och [offentliga moln](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Rutor för projekt namn och region](./media/tutorial-discover-aws/new-project.png)

7. Välj **Skapa**.
8. Vänta några minuter tills projektet har distribuerats. Verktyget **Azure Migrate: identifiering och bedömning** läggs till som standard i det nya projektet.

![Sida som visar verktyget för Server bedömning som har lagts till som standard](./media/tutorial-discover-aws/added-tool.png)

> [!NOTE]
> Om du redan har skapat ett projekt kan du använda samma projekt för att registrera ytterligare enheter för att identifiera och utvärdera fler servrar. [Läs mer](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Konfigurera installationen

Azure Migrate-installationen är en förenklad utrustning som används av Azure Migrate: identifiering och utvärdering för att göra följande:

- Identifiera lokala servrar.
- Skicka metadata och prestanda data för identifierade servrar till Azure Migrate: identifiering och bedömning.

[Läs mer](migrate-appliance.md) om Azure Migrate-enheten.

Så här konfigurerar du den apparat som du:

1. Ange ett namn på apparaten och generera en projekt nyckel i portalen.
1. Ladda ned en zippad fil med Azure Migrate Installer-skript från Azure Portal.
1. Extrahera innehållet från den zippade filen. Starta PowerShell-konsolen med administratörs behörighet.
1. Kör PowerShell-skriptet för att starta webb programmet för installationen.
1. Konfigurera enheten för första gången och registrera den med projektet med hjälp av projekt nyckeln.

### <a name="1-generate-the-project-key"></a>1. generera projekt nyckeln

1. I **mål för migrering**  >  **fönster, Linux-och SQL-servrar**  >  **Azure Migrate: identifiering och bedömning**, Välj **identifiera**.
2. I **identifiera servrar**  >  **är dina servrar virtualiserade?**, Välj **fysiska eller andra (AWS, GCP, Xen osv.)**.
3. I **1: generera en projekt nyckel** anger du ett namn för Azure Migrate-installationen som ska konfigureras för identifiering av fysiska eller virtuella servrar. Namnet måste vara alfanumeriskt med 14 tecken eller färre.
1. Klicka på **generera nyckel** för att starta skapandet av de nödvändiga Azure-resurserna. Stäng inte sidan identifiera servrar när du skapar resurser.
1. När Azure-resurserna har skapats skapas en **projekt nyckel** .
1. Kopiera nyckeln på samma sätt som du behöver den för att slutföra registreringen av enheten under konfigurationen.

### <a name="2-download-the-installer-script"></a>2. Ladda ned installations skriptet

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

        **Scenario** | **Hämta** _ | _ *Hash-värde**
        --- | --- | ---
        Fysisk (85 MB) | [Senaste version](https://go.microsoft.com/fwlink/?linkid=2140334) | 207157bab39303dca1c2b93562d6f1deaa05aa7c992f480138e17977641163fb

    - För Azure Government:

        **Scenario** | **Hämta** _ | _ *Hash-värde**
        --- | --- | ---
        Fysisk (85 MB) | [Senaste version](https://go.microsoft.com/fwlink/?linkid=2140338) | ca67e8dbe21d113ca93bfe94c1003ab7faba50472cb03972d642be8a466f78ce
 

### <a name="3-run-the-azure-migrate-installer-script"></a>3. kör installations skriptet för Azure Migrate
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

Kontrol lera att enheten kan ansluta till Azure-URL: er för [offentliga](migrate-appliance.md#public-cloud-urls) och [offentliga](migrate-appliance.md#government-cloud-urls) moln.

### <a name="4-configure-the-appliance"></a>4. Konfigurera enheten

Konfigurera enheten för första gången.

1. Öppna en webbläsare på vilken dator som helst som kan ansluta till installationen och öppna URL: en för installations programmets webbapp: **https://*-enhetens namn eller IP-adress*: 44368**.

   Alternativt kan du öppna appen från Skriv bordet genom att klicka på genvägen till appen.
2. Godkänn **licens villkoren** och Läs informationen från tredje part.
1. I webbappen > **Konfigurera krav** gör du följande:
    - **Anslutning**: appen kontrollerar att servern är ansluten till Internet. Om servern använder en proxyserver:
        - Klicka på **Installera Proxy** och ange proxyadress (i formuläret http://ProxyIPAddress eller http://ProxyFQDN) lyssnande port.
        - Ange autentiseringsuppgifter om proxyn kräver autentisering.
        - Endast HTTP-proxy stöds.
        - Om du har lagt till proxyinformation eller inaktiverat proxyn och/eller autentiseringen, klickar du på **Spara** för att utlösa anslutnings kontrollen igen.
    - **Tidssynkronisering**: tiden har verifierats. Tiden för installationen bör vara synkroniserad med Internet tid för att Server identifieringen ska fungera korrekt.
    - **Installera uppdateringar**: Azure Migrate: identifiering och bedömning kontrollerar att installationen har de senaste uppdateringarna installerade. När kontrollen är klar kan du klicka på **Visa apparat-tjänster** för att se status och versioner för komponenterna som körs på produkten.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrera enheten med Azure Migrate

1. Klistra in **projekt nyckeln** som har kopierats från portalen. Om du inte har nyckeln går du till **Azure Migrate: identifierings-och utvärderings> identifiera> hantera befintliga apparater**, Välj det installations namn som du angav vid tidpunkten för att generera nyckeln och kopiera motsvarande nyckel.
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

    * Azure Migrate stöder den privata SSH-nyckeln som genereras av ssh-keygen-kommandot med hjälp av RSA-, DSA-, ECDSA-och ed25519-algoritmer.
    * För närvarande har Azure Migrate inte stöd för en lösen fras baserad SSH-nyckel. Använd en SSH-nyckel utan lösen fras.
    * För närvarande stöder Azure Migrate inte den privata SSH-nyckelfilen som genereras av SparaTillFil.
    * Azure Migrate stöder OpenSSH-formatet för den privata SSH-nyckelfilen som visas nedan:
    
    ![Format som stöds av SSH privat nyckel](./media/tutorial-discover-physical/key-format.png)


1. Om du vill lägga till flera autentiseringsuppgifter samtidigt klickar du på **Lägg till fler** för att spara och lägga till fler autentiseringsuppgifter. Flera autentiseringsuppgifter stöds för identifiering av fysiska servrar.
1. I **steg 2: Ange information om fysiska eller virtuella servrar** klickar du på **Lägg till identifierings källa** för att ange serverns **IP-adress/FQDN** och det egna namnet för autentiseringsuppgifter för att ansluta till servern.
1. Du kan antingen **lägga till ett enskilt objekt** i taget eller **lägga till flera objekt** i taget. Det finns också ett alternativ för att tillhandahålla Server information via **importera CSV**.


    - Om du väljer **Lägg till enstaka objekt** kan du välja typ av operativ system, ange ett eget namn för autentiseringsuppgifter, lägga till serverns **IP-adress/FQDN** och klicka på **Spara**.
    - Om du väljer **Lägg till flera objekt** kan du lägga till flera poster samtidigt genom att ange serverns **IP-adress/FQDN** med det egna namnet för autentiseringsuppgifter i text rutan. Verifiera * * de tillagda posterna och klicka på **Spara**.
    - Om du väljer **importera CSV** _(vald som standard)_ kan du ladda ned en CSV-mallfil, fylla i filen med serverns **IP-adress/FQDN** och eget namn för autentiseringsuppgifter. Sedan kan du importera filen till enheten, **Verifiera** posterna i filen och klicka på **Spara**.

1. När du klickar på Spara kommer installations programmet att försöka verifiera anslutningen till de servrar som lagts till och visa **verifierings status** i tabellen mot varje server.
    - Om verifieringen Miss lyckas för en server kan du granska felet genom att klicka på **verifieringen misslyckades** i kolumnen Status i tabellen. Åtgärda problemet och verifiera igen.
    - Klicka på **ta bort** om du vill ta bort en server.
1. Du kan **validera** anslutningen till servrarna när som helst innan du påbörjar identifieringen.
1. Klicka på **Starta identifiering** för att starta identifiering av verifierade servrar. När identifieringen har påbörjats kan du kontrol lera identifierings statusen mot varje server i tabellen.


Detta startar identifieringen. Det tar ungefär 2 minuter per server för metadata om identifierad server som visas i Azure Portal.

## <a name="verify-servers-in-the-portal"></a>Verifiera servrarna i portalen

När identifieringen är klar kan du kontrol lera att servrarna visas i portalen.

1. Öppna instrumentpanelen för Azure Migrate.
2. På sidan **Azure Migrate-Windows-, Linux-och SQL-servrar**  >  **Azure Migrate: identifiering och utvärdering** klickar du på ikonen som visar antalet för **identifierade servrar**.

## <a name="next-steps"></a>Nästa steg

- [Utvärdera fysiska servrar](tutorial-migrate-aws-virtual-machines.md) för migrering till virtuella Azure-datorer.
- [Granska de data](migrate-appliance.md#collected-data---physical) som enheten samlar in under identifieringen.
