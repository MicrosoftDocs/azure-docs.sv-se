---
title: Identifiera servrar på Hyper-V med Azure Migrate identifiering och utvärdering
description: Lär dig hur du identifierar lokala servrar på Hyper-V med verktyget för Azure Migrate identifiering och bedömning.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/25/2021
ms.custom: mvc
ms.openlocfilehash: f461778f988fafeacc480e100b00be7d4c165dfb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612525"
---
# <a name="tutorial-discover-servers-running-on-hyper-v-with-azure-migrate-discovery-and-assessment"></a>Självstudie: identifiera servrar som körs på Hyper-V med Azure Migrate: identifiering och utvärdering

Som en del av migreringen till Azure identifierar du din lokala inventering och dina arbets belastningar.

I den här självstudien lär du dig att identifiera lokala servrar på Hyper-V-värdar med Azure Migrate: identifierings-och utvärderings verktyget med en förenklad Azure Migrate-enhet. Du distribuerar installationen som en server på Hyper-V-värden för att kontinuerligt identifiera dator-och prestanda-metadata.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera ett Azure-konto
> * Förbered Hyper-V-miljön för identifiering.
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
**Hyper-V-värd** | Hyper-V-värdar på vilka servrar finns kan vara fristående eller i ett kluster.<br/><br/> Värden måste köra Windows Server 2019, Windows Server 2016 eller Windows Server 2012 R2.<br/><br/> Kontrol lera att inkommande anslutningar är tillåtna på WinRM-port 5985 (HTTP), så att enheten kan ansluta till hämtning av serverns metadata och prestanda data med hjälp av en Common Information Model CIM-session.
**Distribution av utrustning** | Hyper-V-värden behöver resurser för att allokera en server för enheten:<br/><br/> – 16 GB RAM, 8 virtuella processorer och cirka 80 GB disk lagring.<br/><br/> – En extern virtuell växel och Internet åtkomst på enheten, direkt eller via en proxyserver.
**Servrar** | Servrar kan köra ett Windows-eller Linux-operativsystem.

## <a name="prepare-an-azure-user-account"></a>Förbereda ett Azure-användarkonto

Om du vill skapa ett projekt och registrera Azure Migrate-enheten behöver du ett konto med:
- Deltagar-eller ägar behörigheter för en Azure-prenumeration.
- Behörighet att registrera Azure Active Directory-appar (AAD).

Om du nyligen skapade ett kostnadsfritt Azure-konto är du ägare av prenumerationen. Om du inte är prenumerations ägare kan du arbeta med ägaren för att tilldela behörigheterna på följande sätt:

1. I Azure Portal söker du efter "prenumerationer" och under **tjänster** väljer du **prenumerationer**.

    ![Sök i rutan för att söka efter Azure-prenumerationen](./media/tutorial-discover-hyper-v/search-subscription.png)

2. På sidan **prenumerationer** väljer du den prenumeration där du vill skapa ett projekt.
3. I prenumerationen väljer du **åtkomst kontroll (IAM)**  >  **kontrol lera åtkomst**.
4. I **kontrol lera åtkomst** söker du efter det relevanta användar kontot.
5. I **Lägg till en roll tilldelning** klickar du på **Lägg till**.

    ![Sök efter ett användar konto för att kontrol lera åtkomst och tilldela en roll](./media/tutorial-discover-hyper-v/azure-account-access.png)

6. I **Lägg till roll tilldelning** väljer du rollen deltagare eller ägare och väljer kontot (azmigrateuser i vårt exempel). Klicka sedan på **Spara**.

    ![Öppnar sidan Lägg till roll tilldelning för att tilldela kontot en roll](./media/tutorial-discover-hyper-v/assign-role.png)

1. För att registrera installationen behöver ditt Azure-konto **behörighet att registrera AAD-appar.**
1. I Azure Portal navigerar du till **Azure Active Directory**  >  **användares**  >  **användar inställningar**.
1. I **användar inställningar** kontrollerar du att Azure AD-användare kan registrera program (anges till **Ja** som standard).

    ![Verifiera i användar inställningar som användare kan registrera Active Directory appar](./media/tutorial-discover-hyper-v/register-apps.png)

9. Om inställningen "Appregistreringar" är inställd på "nej", ber du klienten/den globala administratören att tilldela den behörighet som krävs. Alternativt kan klient organisationen/den globala administratören tilldela rollen **programutvecklare** till ett konto för att tillåta registrering av AAD-appen. [Läs mer](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-hyper-v-hosts"></a>Förbereda Hyper-V-värdar

Du kan förbereda Hyper-V-värdar manuellt eller använda ett skript. Förberedelse stegen sammanfattas i tabellen. Skriptet förbereder dessa automatiskt.

**Steg** | **Över** | **Manuell**
--- | --- | ---
Kontrol lera värd kraven | Kontrollerar att värden kör en version av Hyper-V som stöds och Hyper-V-rollen.<br/><br/>Aktiverar WinRM-tjänsten och öppnar portarna 5985 (HTTP) och 5986 (HTTPS) på värden (krävs för metadata-insamling). | Värden måste köra Windows Server 2019, Windows Server 2016 eller Windows Server 2012 R2.<br/><br/> Kontrol lera att inkommande anslutningar är tillåtna på WinRM-port 5985 (HTTP), så att enheten kan ansluta till hämtning av serverns metadata och prestanda data med hjälp av en Common Information Model CIM-session.<br/><br/> Skriptet stöds för närvarande inte på värdar med ett annat språk än engelska.  
Verifiera PowerShell-version | Kontrollerar att du kör skriptet på en PowerShell-version som stöds. | Kontrol lera att du kör PowerShell version 4,0 eller senare på Hyper-V-värden.
Skapa ett konto | Kontrollerar att du har rätt behörigheter på Hyper-V-värden.<br/><br/> Gör att du kan skapa ett lokalt användar konto med rätt behörighet. | Alternativ 1: Förbered ett konto med administratörs åtkomst till Hyper-V-värddatorn.<br/><br/> Alternativ 2: Förbered ett lokalt administratörs konto eller ett domän administratörs konto och Lägg till kontot i dessa grupper: fjärrhanterings användare, Hyper-V-administratörer och användare av prestanda övervakning.
Aktivera PowerShell-fjärrkommunikation | Aktiverar PowerShell-fjärrkommunikation på värden, så att Azure Migrate-installationen kan köra PowerShell-kommandon på värden via en WinRM-anslutning. | Om du vill konfigurera, öppnar du en PowerShell-konsol som administratör på varje värd och kör det här kommandot: ``` powershell Enable-PSRemoting -force ```
Konfigurera Hyper-V integrerings tjänster | Kontrollerar att integrerings tjänsterna för Hyper-V är aktiverade på alla servrar som hanteras av värden. | [Aktivera Hyper-V integrations tjänster](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) på varje server.<br/><br/> Om du kör Windows Server 2003 följer du [dessa anvisningar](prepare-windows-server-2003-migration.md).
Delegera autentiseringsuppgifter om Server diskar finns på fjärr-SMB-resurser | Delegerar autentiseringsuppgifter | Kör det här kommandot för att aktivera CredSSP för att delegera autentiseringsuppgifter på värdar som kör servrar med diskar på SMB-resurser: ```powershell Enable-WSManCredSSP -Role Server -Force ```<br/><br/> Du kan köra det här kommandot via fjärr anslutning på alla Hyper-V-värdar.<br/><br/> Om du lägger till nya noder i ett kluster läggs de automatiskt till för identifiering, men du måste aktivera CredSSP manuellt.<br/><br/> När du konfigurerar installationen slutför du installationen av CredSSP genom att [Aktivera den på enheten](#delegate-credentials-for-smb-vhds). 

### <a name="run-the-script"></a>Kör skriptet

1. Hämta skriptet från [Microsoft Download Center](https://aka.ms/migrate/script/hyperv). Skriptet har signerats kryptografiskt av Microsoft.
2. Verifiera skript integriteten med hjälp av antingen MD5-eller SHA256-hash-filer. Värdena för hashtagg anges nedan. Kör det här kommandot för att generera hashen för skriptet:

    ```powershell
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Exempel på användning:

    ```powershell
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1 SHA256
    ```
3. När du har verifierat skript integriteten kör du skriptet på varje Hyper-V-värd med PowerShell-kommandot med förhöjd behörighet:

    ```powershell
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```
Hash-värden är:

**Hash** |  **Värde**
--- | ---
MD5 | 0ef418f31915d01f896ac42a80dc414e
SHA256 | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2

## <a name="set-up-a-project"></a>Konfigurera ett projekt

Skapa ett nytt projekt.

1. I Azure-portalen > **Alla tjänster** söker du efter **Azure Migrate**.
2. Under **Tjänster** väljer du **Azure Migrate**.
3. I **Översikt** väljer du **skapa projekt**.
5. I **skapa projekt** väljer du din Azure-prenumeration och resurs grupp. Skapa en resurs grupp om du inte har någon.
6. I **projekt information** anger du projekt namnet och geografin som du vill skapa projektet i. Granska stödda geografiska områden för [offentliga](migrate-support-matrix.md#supported-geographies-public-cloud) och [offentliga moln](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Rutor för projekt namn och region](./media/tutorial-discover-hyper-v/new-project.png)

7. Välj **Skapa**.
8. Vänta några minuter tills projektet har distribuerats. Verktyget **Azure Migrate: identifiering och bedömning** läggs till som standard i det nya projektet.

![Sida som visar Azure Migrate: verktyget för identifiering och bedömning har lagts till som standard](./media/tutorial-discover-hyper-v/added-tool.png)

> [!NOTE]
> Om du redan har skapat ett projekt kan du använda samma projekt för att registrera ytterligare enheter för att identifiera och utvärdera fler servrar. [Läs mer](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Konfigurera installationen

Azure Migrate använder en förenklad Azure Migrate-apparat. Installationen utför Server identifiering och skickar metadata för Server konfiguration och prestanda till Azure Migrate. Installationen kan konfigureras genom att distribuera en VHD-fil som kan laddas ned från projektet.

> [!NOTE]
> Om du av någon anledning inte kan konfigurera installationen med hjälp av mallen kan du konfigurera den med hjälp av ett PowerShell-skript på en befintlig Windows Server 2016-Server. [Läs mer](deploy-appliance-script.md#set-up-the-appliance-for-hyper-v).

Den här självstudien konfigurerar installationen på en server som körs i Hyper-V-miljön, enligt följande:

1. Ange ett namn på apparaten och generera en projekt nyckel i portalen.
1. Ladda ned en komprimerad virtuell Hyper-V-hårddisk från Azure Portal.
1. Skapa enheten och kontrol lera att den kan ansluta till Azure Migrate: identifiering och bedömning.
1. Konfigurera enheten för första gången och registrera den med projektet med hjälp av projekt nyckeln.

### <a name="1-generate-the-project-key"></a>1. generera projekt nyckeln

1. I **mål**  >  **servrar** för migrering  >  **Azure Migrate: identifiering och bedömning**, Välj **identifiera**.
2. I **Discover-servrar**  >  **är servrarna virtualiserade?** väljer du **Ja, med Hyper-V**.
3. I **1: generera en projekt nyckel** anger du ett namn för Azure Migrate-installationen som ska konfigureras för identifiering av servrar. Namnet måste vara alfanumeriskt med 14 tecken eller färre.
1. Klicka på **generera nyckel** för att starta skapandet av de nödvändiga Azure-resurserna. Stäng inte sidan identifiera server när du skapar resurser.
1. När Azure-resurserna har skapats skapas en **projekt nyckel** .
1. Kopiera nyckeln på samma sätt som du behöver den för att slutföra registreringen av enheten under konfigurationen.

### <a name="2-download-the-vhd"></a>2. Ladda ned den virtuella hård disken

I **2: Ladda ned Azure Migrate-enheten** väljer du. VHD-fil och klicka på **Hämta**.

### <a name="verify-security"></a>Verifiera säkerhet

Kontrol lera att den zippade filen är säker innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.

2. Kör följande PowerShell-kommando för att generera hash för ZIP-filen
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Exempel på användning: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```

3.  Kontrol lera de senaste versions-och hash-värdena för produkten:

    - För det offentliga Azure-molnet:

        **Scenario** | **Ladda ned** | **SHA256**
        --- | --- | ---
        Hyper-V (8,91 GB) | [Senaste version](https://go.microsoft.com/fwlink/?linkid=2140422) |  79c151588de049cc102f61b910d6136e02324dc8d8a14f47772da351b46d9127

    - För Azure Government:

        **Scenario** _ | _ *Hämta** | **SHA256**
        --- | --- | ---
        Hyper-V (85,8 MB) | [Senaste version](https://go.microsoft.com/fwlink/?linkid=2140424) |  cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f

### <a name="3-create-an-appliance"></a>3. skapa en apparat

Importera den nedladdade filen och skapa en-apparat.

1. Extrahera den zippade VHD-filen till en mapp på Hyper-V-värden som är värd för-enheten. Tre mappar extraheras.
2. Öppna Hyper-V Manager. Klicka på **Importera virtuell dator** i **åtgärder**.
2. I guiden Importera virtuell dator > **innan du börjar klickar du** på **Nästa**.
3. I **hitta mapp** anger du den mapp som innehåller den extraherade virtuella hård disken. Klicka på **Nästa**.
1. I **Välj virtuell dator** klickar du på **Nästa**.
2. I **Välj import typ** klickar **du på Kopiera den virtuella datorn (skapa ett nytt unikt ID)**. Klicka på **Nästa**.
3. I **Välj mål** låter du standardvärdet vara kvar. Klicka på **Nästa**.
4. Lämna standardinställningen i **Storage Folders**. Klicka på **Nästa**.
5. I **Välj nätverk** anger du den virtuella växel som ska användas av enheten. Växeln behöver Internet anslutning för att skicka data till Azure.
6. Granska inställningarna i **Sammanfattning**. Klicka sedan på **Slutför**.
7. Starta enheten i Hyper-V Manager > **Virtual Machines**.

### <a name="verify-appliance-access-to-azure"></a>Verifiera åtkomst till enheten till Azure

Kontrol lera att enheten kan ansluta till Azure-URL: er för [offentliga](migrate-appliance.md#public-cloud-urls) och [offentliga](migrate-appliance.md#government-cloud-urls) moln.

### <a name="4-configure-the-appliance"></a>4. Konfigurera enheten

Konfigurera enheten för första gången.

> [!NOTE]
> Om du ställer in installationen med hjälp av ett [PowerShell-skript](deploy-appliance-script.md) i stället för den hämtade virtuella hård disken är de två första stegen i den här proceduren inte relevanta.

1. I Hyper-V Manager > **Virtual Machines** högerklickar du på enheten > **Anslut**.
2. Ange språk, tidszon och lösen ord för enheten.
3. Öppna en webbläsare på vilken dator som helst som kan ansluta till installationen och öppna URL: en för installations programmets webbapp: **https://*-enhetens namn eller IP-adress*: 44368**.

   Alternativt kan du öppna appen från apparatens skriv bord genom att klicka på genvägen till appen.
1. Godkänn **licens villkoren** och Läs informationen från tredje part.
1. I webbappen > **Konfigurera krav** gör du följande:
    - **Anslutning**: appen kontrollerar att enheten har Internet åtkomst. Om enheten använder en proxyserver:
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
4. Om Azure-användarkontot som används för loggning har rätt behörigheter för de Azure-resurser som skapades under den här nyckeln, initieras registrerings enheten.
1. När installationen av enheten har registrerats kan du se registrerings informationen genom att klicka på **Visa information**.

### <a name="delegate-credentials-for-smb-vhds"></a>Delegera autentiseringsuppgifter för SMB-VHD: er

Om du kör virtuella hård diskar på SMB: er måste du aktivera delegering av autentiseringsuppgifter från installationen till Hyper-V-värdarna. Så här gör du detta från enheten:

1. Kör det här kommandot på-enheten. HyperVHost1/HyperVHost2 är exempel värd namn.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
    ```

2. Du kan också göra detta i redigerare för lokalt grupprincipobjekt på enheten:
    - I dator konfiguration för **lokal dator princip**  >  klickar du på **administrativa mallar**  >    >  **delegering av systemautentiseringsuppgifter**.
    - Dubbelklicka på **Tillåt att delegera nya autentiseringsuppgifter** och välj **aktive rad**.
    - I **alternativ** klickar du på **Visa** och lägger till varje Hyper-V-värd som du vill identifiera i listan med **WSMan/** som prefix.
    - I  **delegering av autentiseringsuppgifter** dubbelklickar du på **Tillåt att delegera nya AUTENTISERINGSUPPGIFTER med endast NTLM-** serverautentisering. Lägg återigen till varje Hyper-V-värd som du vill identifiera i listan, med **WSMan/** som prefix.

## <a name="start-continuous-discovery"></a>Starta kontinuerlig identifiering

Anslut från enheten till Hyper-V-värdar eller-kluster och starta Server identifiering.

1. I **steg 1: ange autentiseringsuppgifter för Hyper-v-värden** klickar du på **Lägg till autentiseringsuppgifter** för att ange ett eget namn för autentiseringsuppgifter, Lägg till **användar namn** och **lösen ord** för en Hyper-v-värd/-kluster som installationen ska använda för att identifiera servrar. Klicka på **Spara**.
1. Om du vill lägga till flera autentiseringsuppgifter samtidigt klickar du på **Lägg till fler** för att spara och lägga till fler autentiseringsuppgifter. Flera autentiseringsuppgifter stöds för identifiering av servrar i Hyper-V-miljön.
1. I **steg 2: Ange information om Hyper-v-värdar/-kluster** klickar du på **Lägg till identifierings källa** för att ange **IP-adress/FQDN** för Hyper-v-värd/kluster och det egna namnet för autentiseringsuppgifter för att ansluta till värden/klustret.
1. Du kan antingen **lägga till ett enskilt objekt** i taget eller **lägga till flera objekt** i taget. Det finns också ett alternativ för att tillhandahålla information om Hyper-V-värdar/-kluster via **import-CSV**.

    - Om du väljer **Lägg till enstaka objekt** måste du ange ett eget namn för autentiseringsuppgifter och Hyper-V-värd/kluster **-IP-adress/FQDN** och klicka på **Spara**.
    - Om du väljer **Lägg till flera objekt** _(markerat som standard)_ kan du lägga till flera poster samtidigt genom att ange **IP-adress/FQDN** för Hyper-V-värd/-kluster med det egna namnet för autentiseringsuppgifter i text rutan. **Verifiera** de tillagda posterna och klicka på **Spara**.
    - Om du väljer **importera CSV** kan du ladda ned en CSV-mallfil, fylla i filen med Hyper-V-värd-/kluster **-IP-adress/FQDN** och eget namn för autentiseringsuppgifter. Sedan kan du importera filen till enheten, **Verifiera** posterna i filen och klicka på **Spara**.

1. När du klickar på Spara kommer installations programmet att försöka verifiera anslutningen till Hyper-V-värdarna/-klustren som lagts till och visa **verifierings status** i tabellen mot varje värd/kluster.
    - För verifierade värdar/kluster kan du Visa mer information genom att klicka på deras IP-adress/FQDN.
    - Om verifieringen Miss lyckas för en värd granskar du felet genom att klicka på **verifiering misslyckades** i kolumnen Status i tabellen. Åtgärda problemet och verifiera igen.
    - Klicka på **ta bort** om du vill ta bort värdar eller kluster.
    - Du kan inte ta bort en speciell värd från ett kluster. Du kan bara ta bort hela klustret.
    - Du kan lägga till ett kluster även om det finns problem med specifika värdar i klustret.
1. Du kan **Verifiera** anslutningen till värdarna/klustren när som helst innan du påbörjar identifieringen.
1. Klicka på **Starta identifiering** för att starta Server identifiering från verifierade värdar/kluster. När identifieringen har startats kan du kontrol lera identifierings statusen mot varje värd/kluster i tabellen.

Detta startar identifieringen. Det tar ungefär 2 minuter per värd för metadata för identifierade servrar som visas i Azure Portal.

## <a name="verify-servers-in-the-portal"></a>Verifiera servrarna i portalen

När identifieringen är klar kan du kontrol lera att servrarna visas i portalen.

1. Öppna instrumentpanelen för Azure Migrate.
2. På sidan **Azure Migrate-servrar**  >  **Azure Migrate: identifiering och bedömning** klickar du på ikonen som visar antalet för **identifierade servrar**.

## <a name="next-steps"></a>Nästa steg

- [Utvärdera servrar i Hyper-V-miljön](tutorial-assess-hyper-v.md) för migrering till virtuella Azure-datorer.
- [Granska de data](migrate-appliance.md#collected-data---hyper-v) som enheten samlar in under identifieringen.