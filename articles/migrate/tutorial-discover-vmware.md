---
title: Identifiera servrar som körs i VMware-miljön med Azure Migrate identifiering och utvärdering
description: Lär dig hur du identifierar lokala servrar som körs i VMware-miljön med verktyget för Azure Migrate identifiering och bedömning
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/25/2021
ms.custom: mvc
ms.openlocfilehash: 2e9dafc111478f35263dc9db0cc07d913327c936
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612066"
---
# <a name="tutorial-discover-servers-running-in-vmware-environment-with-azure-migrate-discovery-and-assessment"></a>Självstudie: identifiera servrar som körs i VMware-miljön med Azure Migrate: identifiering och utvärdering

Som en del av migreringen till Azure identifierar du din lokala inventering och dina arbets belastningar.

Den här självstudien visar hur du identifierar servrar som körs i VMware-miljön med Azure Migrate: identifierings-och utvärderings verktyg med hjälp av en förenklad Azure Migrate-enhet. Du distribuerar installationen som en server som körs i vCenter Server, för att kontinuerligt identifiera servrar och deras prestanda-metadata, program som körs på servrar, Server beroenden och SQL Server instanser och databaser.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera ett Azure-konto.
> * Förbered VMware-miljön för identifiering.
> * Skapa ett projekt.
> * Konfigurera Azure Migrate-enheten.
> * Starta kontinuerlig identifiering.

> [!NOTE]
> Självstudier visar den snabbaste sökvägen för att testa ett scenario och använda standard alternativ där det är möjligt.  

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Innan du påbörjar den här självstudien måste du kontrol lera att du har dessa krav på plats.

**Krav** | **Information**
--- | ---
**vCenter Server/ESXi-värd** | Du behöver ett vCenter Server som kör version 5,5, 6,0, 6,5 eller 6,7.<br/><br/> Servrar måste ligga på en ESXi-värd som kör version 5,5 eller senare.<br/><br/> På vCenter Server kan du tillåta inkommande anslutningar på TCP-port 443, så att installationen kan samla in konfigurations-och prestanda-metadata.<br/><br/> Enheten ansluter till vCenter Server på port 443 som standard. Om vCenter Server lyssnar på en annan port kan du ändra porten när du anger vCenter Servers information på installations hanteraren för installationen.<br/><br/> På ESXi-värdarna kontrollerar du att inkommande åtkomst tillåts på TCP-port 443 för att identifiera installerade program och en agent lös beroende analys på servrar.
**Enhet** | vCenter Server behöver resurser för att allokera en server för Azure Migrates enheten:<br/><br/> – 32 GB RAM, 8 virtuella processorer och cirka 80 GB disk lagring.<br/><br/> – En extern virtuell växel och Internet åtkomst på installations servern, direkt eller via en proxyserver.
**Servrar** | Alla Windows-och Linux OS-versioner stöds för identifiering av konfigurations-och prestanda-metadata. <br/><br/> Alla Windows-och Linux OS-versioner stöds för att utföra program identifiering på servrar. Sök efter de OS- [versioner som stöds](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) för analys av beroende analyser.<br/><br/> Om du vill utföra identifiering av installerade program och en agent lös beroende analys, måste VMware-verktyg (senare än 10.2.0) installeras och köras på servrar. Windows-servrar måste ha PowerShell version 2,0 eller senare installerat.<br/><br/> Om du vill identifiera SQL Server instanser och databaser kan [du söka efter](migrate-support-matrix-vmware.md#requirements-for-discovery-of-sql-server-instances-and-databases) de SQL Server versioner och versioner som stöds, de Windows OS-versioner och autentiseringsmekanismer som stöds.

## <a name="prepare-an-azure-user-account"></a>Förbereda ett Azure-användarkonto

Om du vill skapa ett projekt och registrera Azure Migrate-enheten behöver du ett konto med:

- Deltagar-eller ägar behörigheter för Azure-prenumerationen
- Behörigheter för att registrera Azure Active Directory-appar (AAD)
- Ägare eller deltagare plus administratörs behörighet för användar åtkomst på Azure-prenumerationen för att skapa en Key Vault som används vid migrering av en server utan Server

Om du nyligen skapade ett kostnadsfritt Azure-konto är du ägare av prenumerationen. Om du inte är prenumerations ägare kan du arbeta med ägaren för att tilldela behörigheterna på följande sätt:

1. I Azure Portal söker du efter "prenumerationer" och under **tjänster** väljer du **prenumerationer**.

    :::image type="content" source="./media/tutorial-discover-vmware/search-subscription.png" alt-text="Sök i rutan för att söka efter Azure-prenumerationen":::


2. På sidan **prenumerationer** väljer du den prenumeration där du vill skapa ett projekt.
3. I prenumerationen väljer du **åtkomst kontroll (IAM)**  >  **kontrol lera åtkomst**.
4. I **kontrol lera åtkomst** söker du efter det relevanta användar kontot.
5. I **Lägg till en roll tilldelning** klickar du på **Lägg till**.
:::image type="content" source="./media/tutorial-discover-vmware/azure-account-access.png" alt-text="Sök efter ett användar konto för att kontrol lera åtkomst och tilldela en roll":::
    
6. I **Lägg till roll tilldelning** väljer du rollen deltagare eller ägare och väljer kontot (azmigrateuser i vårt exempel). Klicka sedan på **Spara**.

    :::image type="content" source="./media/tutorial-discover-vmware/assign-role.png" alt-text="Öppnar sidan Lägg till roll tilldelning för att tilldela kontot en roll":::

1. För att registrera installationen behöver ditt Azure-konto **behörighet att registrera AAD-appar.**
1. I Azure Portal navigerar du till **Azure Active Directory**  >  **användares**  >  **användar inställningar**.
1. I **användar inställningar** kontrollerar du att Azure AD-användare kan registrera program (anges till **Ja** som standard).

    :::image type="content" source="./media/tutorial-discover-vmware/register-apps.png" alt-text="Verifiera i användar inställningar som användare kan registrera Active Directory appar":::

9. Om inställningen "Appregistreringar" är inställd på "nej", ber du klienten/den globala administratören att tilldela den behörighet som krävs. Alternativt kan klient organisationen/den globala administratören tilldela rollen **programutvecklare** till ett konto för att tillåta registrering av AAD-appen. [Läs mer](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-vmware"></a>Förbereda VMware

På vCenter Server kontrollerar du att ditt konto har behörighet att skapa en virtuell dator med en ägg fil. Detta krävs när du distribuerar Azure Migrate-installationen som en virtuell VMware-dator med hjälp av en ägg fil.

Azure Migrate behöver ett vCenter Server skrivskyddat konto för identifiering och utvärdering av servrar som körs i VMware-miljön. Om du även vill utföra identifiering av installerade program och beroende analyser utan agent måste kontot ha behörighet som är aktiverat för **Virtual Machines > gäst åtgärder**.

### <a name="create-an-account-to-access-vcenter"></a>Skapa ett konto för åtkomst till vCenter

I vSphere-webbklient skapar du ett konto på följande sätt:

1. Med hjälp av ett konto med administratörs behörighet går du till vSphere-webbklienten > väljer **Administration**.
2. **Åtkomst** väljer du **SSO-användare och-grupper**.
3. I **användare lägger du** till en ny användare.
4. I **ny användare**, anger du konto informationen. Klicka sedan på **OK**.
5. I **globala behörigheter** väljer du användar kontot och tilldelar kontot den **skrivskyddade** rollen. Klicka sedan på **OK**.
6. Om du även vill utföra identifiering av installerade program och en agent lös beroende analys går du till **roller** > väljer den **skrivskyddade** rollen och väljer **gäst åtgärder** i **privilegier**. Du kan sprida privilegierna till alla objekt under vCenter Server genom att markera kryss rutan Sprid till underordnade objekt.

    :::image type="content" source="./media/tutorial-discover-vmware/guest-operations.png" alt-text="Kryss ruta för att tillåta gäst åtgärder på den skrivskyddade rollen":::

> [!NOTE]
> Du kan begränsa identifieringen till specifika vCenter Server Data Center, kluster, en mapp med kluster, värdar, en mapp med värdar eller enskilda servrar genom att omfånget vCenter Server-kontot. [**Läs mer**](set-discovery-scope.md) om hur du omfångerar vCenter Server-användarkontot.

### <a name="create-an-account-to-access-servers"></a>Skapa ett konto för åtkomst till servrar

Du behöver ett användar konto med de behörigheter som krävs på servrarna för att kunna identifiera installerade program, agent lös beroende analys och identifiering av SQL Server instanser och databaser. Du kan ange användar kontot för Konfigurations hanteraren för installationen. Installations programmet installerar inte några agenter på servrarna.

1. För Windows-servrar skapar du ett konto (lokal eller domän) med administratörs behörighet på-servrarna. Om du vill identifiera SQL Server instanser och databaser måste du ha Windows-eller SQL Server-kontot som medlem i Server rollen sysadmin. [Läs mer](/sql/relational-databases/security/authentication-access/server-level-roles) om hur du tilldelar användar kontot den roll som krävs.
2. Skapa ett konto med rot privilegier för Linux-servrar. Alternativt kan du skapa ett konto med dessa behörigheter för/bin/netstat-och/bin/ls-filer: CAP_DAC_READ_SEARCH och CAP_SYS_PTRACE.

> [!NOTE]
> Du kan nu lägga till flera autentiseringsuppgifter för servern i Configuration Manager för att utföra identifiering av installerade program, agent lös beroende analys och identifiering av SQL Server instanser och databaser. Du kan lägga till flera domän/Windows (icke-domän)/Linux-(icke-domän) och/eller SQL Server autentiseringsuppgifter för autentisering. [**Läs mer**](add-server-credentials.md)

## <a name="set-up-a-project"></a>Konfigurera ett projekt

Skapa ett nytt projekt.

1. I Azure-portalen > **Alla tjänster** söker du efter **Azure Migrate**.
2. Under **Tjänster** väljer du **Azure Migrate**.
3. I **Översikt** väljer du **skapa projekt**.
5. I **skapa projekt** väljer du din Azure-prenumeration och resurs grupp. Skapa en resurs grupp om du inte har någon.
6. I **projekt information** anger du projekt namnet och geografin som du vill skapa projektet i. Granska stödda geografiska områden för [offentliga](migrate-support-matrix.md#supported-geographies-public-cloud) och [offentliga moln](migrate-support-matrix.md#supported-geographies-azure-government).

    :::image type="content" source="./media/tutorial-discover-vmware/new-project.png" alt-text="Rutor för projekt namn och region":::

7. Välj **Skapa**.
8. Vänta några minuter tills projektet har distribuerats. Verktyget **Azure Migrate: identifiering och bedömning** läggs till som standard i det nya projektet.

> [!NOTE]
> Om du redan har skapat ett projekt kan du använda samma projekt för att registrera ytterligare enheter för att identifiera och utvärdera fler. av servrar. [ **Läs mer**](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Konfigurera installationen

Azure Migrate: identifiering och bedömning använder en förenklad Azure Migrate-apparat. Installationen utför Server identifiering och skickar metadata för Server konfiguration och prestanda till Azure Migrate. Installationen kan konfigureras genom att distribuera en mall för ägg som kan laddas ned från projektet.

> [!NOTE]
> Om du av någon anledning inte kan konfigurera installationen med hjälp av mallen kan du konfigurera den med hjälp av ett PowerShell-skript på en befintlig Windows Server 2016-Server. [**Läs mer**](deploy-appliance-script.md#set-up-the-appliance-for-vmware).

### <a name="deploy-with-ova"></a>Distribuera med ägg

Så här konfigurerar du installationen av en tjänstmall:

1. Ange ett namn på apparaten och generera en projekt nyckel i portalen.
1. Hämta en mall för en embryo-fil och importera den till vCenter Server. Kontrol lera att EMBRYOna är säkra.
1. Skapa installationen från ägg filen och kontrol lera att den kan ansluta till Azure Migrate.
1. Konfigurera enheten för första gången och registrera den med projektet med hjälp av projekt nyckeln.

### <a name="1-generate-the-project-key"></a>1. generera projekt nyckeln

1. I **mål för migrering**  >  **fönster, Linux-och SQL-servrar**  >  **Azure Migrate: identifiering och bedömning**, Välj **identifiera**.
2. I **Discover-servrar**  >  **är servrarna virtualiserade?** väljer du **Ja, med VMware vSphere hypervisor**.
3. I **1: generera en projekt nyckel** anger du ett namn för Azure Migrate-installationen som ska konfigureras för identifiering av servrar i VMware-miljön. Namnet måste vara alfanumeriskt med 14 tecken eller färre.
1. Klicka på **generera nyckel** för att starta skapandet av de nödvändiga Azure-resurserna. Stäng inte sidan identifiera när du skapar resurser.
1. När Azure-resurserna har skapats skapas en **projekt nyckel** .
1. Kopiera nyckeln på samma sätt som du behöver den för att slutföra registreringen av enheten under konfigurationen.

### <a name="2-download-the-ova-template"></a>2. Hämta embryo-mallen

I **2: Ladda ned Azure Migrate-enheten** väljer du. ÄGG filen och klicka på **Hämta**.

### <a name="verify-security"></a>Verifiera säkerhet

Kontrol lera att ägg filen är säker innan du distribuerar den:

1. Öppna ett administratörs kommando fönster på den server som du laddade ned filen till.
2. Kör följande kommando för att generera hashen för den ägg filen:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Exempel på användning: ```C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```

3. Kontrol lera de senaste versions-och hash-värdena för produkten:

    - För det offentliga Azure-molnet:
    
        **Integritetsalgoritm** | **Ladda ned** | **SHA256**
        --- | --- | ---
        VMware (11,9 GB) | [Senaste version](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

    - För Azure Government:
    
        **Integritetsalgoritm** | **Ladda ned** | **SHA256**
        --- | --- | ---
        VMware (85,8 MB) | [Senaste version](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca

### <a name="3-create-the-appliance-server"></a>3. skapa apparatens Server

Importera den nedladdade filen och skapa en server i VMware-miljön

1. I klient konsolen för vSphere klickar du på **fil**  >  **distribution OVF mall**.
2. I guiden Distribuera OVF-mall > **källa** anger du platsen för ägg filen.
3. I **namn** och **plats** anger du ett eget namn för servern. Välj det lager objekt som servern ska finnas i.
5. I **värd/kluster** anger du den värd eller det kluster som servern ska köras på.
6. I **lagring** anger du lagrings målet för-servern.
7. I **Diskformat** anger du disktyp och storlek.
8. I **nätverks mappning** anger du det nätverk som servern ska ansluta till. Nätverket måste ha Internet anslutning för att kunna skicka metadata till Azure Migrate.
9. Granska och bekräfta inställningarna. Klicka sedan på **Slutför**.

### <a name="verify-appliance-access-to-azure"></a>Verifiera åtkomst till enheten till Azure

Se till att installations servern kan ansluta till Azure-URL: er för [offentliga](migrate-appliance.md#public-cloud-urls) och [offentliga](migrate-appliance.md#government-cloud-urls) moln.

### <a name="4-configure-the-appliance"></a>4. Konfigurera enheten

Konfigurera enheten för första gången.

> [!NOTE]
> Om du ställer in installationen med hjälp av ett [**PowerShell-skript**](deploy-appliance-script.md) i stället för hämtade ägg, är de två första stegen i den här proceduren inte relevanta.

1. I klient konsolen för vSphere högerklickar du på servern och väljer sedan **Öppna konsol**.
2. Ange språk, tidszon och lösen ord för enheten.
3. Öppna en webbläsare på vilken dator som helst som kan ansluta till installationen och öppna URL: en för installations hanteraren för installationen: `https://appliance name or IP address: 44368` .

   Alternativt kan du öppna Configuration Manager från apparatens server Skriv bord genom att välja genvägen för Configuration Manager.
1. Godkänn **licens villkoren** och Läs informationen från tredje part.
1. I Configuration Manager > **Konfigurera krav**, gör du följande:
   - **Anslutning**: installationen kontrollerar att servern är ansluten till Internet. Om servern använder en proxyserver:
     - Klicka på **Installera Proxy** för att ange proxyservern `http://ProxyIPAddress` eller `http://ProxyFQDN` och lyssnings porten.
     - Ange autentiseringsuppgifter om proxyn kräver autentisering.
     - Endast HTTP-proxy stöds.
     - Om du har lagt till proxyinformation eller inaktiverat proxyn och/eller autentiseringen, klickar du på **Spara** för att utlösa anslutnings kontrollen igen.
   - **Tidssynkronisering**: tiden för installationen bör vara synkroniserad med Internet-tid för att identifieringen ska fungera korrekt.
   - **Installera uppdateringar: installationen** säkerställer att de senaste uppdateringarna är installerade. När kontrollen är klar kan du klicka på **Visa apparat-tjänster** för att se status och versioner för de tjänster som körs på installations servern.
   - **Installera vddk: installations** programmet kontrollerar att VMware vSphere Virtual Disk Development Kit (vddk) har installerats. Om den inte är installerad laddar du ned VDDK 6,7 från VMware och extraherar det hämtade ZIP-innehållet till den angivna platsen på enheten, enligt **anvisningarna i installations anvisningarna**.

     Azure Migrate Server-migrering använder VDDK för att replikera servrar under migreringen till Azure. 
1. Om du vill kan du **köra nödvändiga komponenter** när som helst under installationen av konfigurationen för att kontrol lera om installationen uppfyller alla krav.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="Panel 1 på installationens konfigurations hanterare":::

### <a name="register-the-appliance-with-azure-migrate"></a>Registrera enheten med Azure Migrate

1. Klistra in **projekt nyckeln** som har kopierats från portalen. Om du inte har nyckeln går du till **Azure Migrate: identifierings-och utvärderings> identifiera> hantera befintliga apparater**, Välj det installations namn som du angav vid tidpunkten för att generera nyckeln och kopiera motsvarande nyckel.
1. Du behöver en enhets kod för att autentisera med Azure. När du klickar på **Logga in** öppnas en modal enhets kod som visas nedan.

    :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="Modal visar enhets koden":::

1. Klicka på **Kopiera kod & inloggning** för att kopiera enhets koden och öppna en Azure-inloggnings tolk i en ny webbläsare-flik. Om den inte visas kontrollerar du att du har inaktiverat blockering av popup-fönster i webbläsaren.
1. På fliken nytt klistrar du in enhets koden och loggar in med ditt användar namn och lösen ord för Azure.
   
   Inloggning med en PIN-kod stöds inte.
3. Om du stänger fliken inloggning oavsiktligt utan att logga in, måste du uppdatera fliken webbläsare i Konfigurations hanteraren för att aktivera inloggnings knappen igen.
1. När du har loggat in går du tillbaka till föregående flik med installationen av Konfigurations hanteraren.
1. Om Azure-användarkontot som används för loggning har rätt behörigheter för de Azure-resurser som skapades under den här nyckeln, initieras registrerings enheten.
1. När installationen av enheten har registrerats kan du se registrerings informationen genom att klicka på **Visa information**.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-registration.png" alt-text="Panel 2 på installationens konfigurations hanterare":::

## <a name="start-continuous-discovery"></a>Starta kontinuerlig identifiering

### <a name="provide-vcenter-server-details"></a>Ange vCenter Server information

Installationen måste ansluta till vCenter Server för att identifiera serverns konfigurations-och prestanda data.

1. I **steg 1: ange vCenter Server autentiseringsuppgifter** klickar du på **Lägg till autentiseringsuppgifter** för att ange ett eget namn för autentiseringsuppgifter, Lägg till **användar namn** och **lösen ord** för det vCenter servers konto som ska användas för att identifiera servrar som körs på vCenter Server.
    - Du bör ha skapat ett konto med de behörigheter som krävs, enligt vad som beskrivs i den här artikeln ovan.
    - Om du vill begränsa identifieringen till specifika VMware-objekt (vCenter Server Data Center, kluster, en mapp med kluster, värdar, en mapp med värdar eller enskilda servrar) läser du anvisningarna i [den här artikeln](set-discovery-scope.md) för att begränsa det konto som används av Azure Migrate.
1. I **steg 2: ange vCenter Server information** klickar du på **Lägg till identifierings källa** och väljer det egna namnet för autentiseringsuppgifter i list rutan. Ange **IP-adressen/FQDN** för vCenter Server. Du kan lämna **porten** till standard (443) eller ange en anpassad Port där vCenter Server lyssnar och klicka på **Spara**.
1. När du klickar på **Spara** kommer installations programmet att försöka verifiera anslutningen till vCenter Server med de angivna autentiseringsuppgifterna och visa **verifierings status** i tabellen mot vCenter Server IP-adress/FQDN.
1. Du kan **Verifiera** anslutningen till vCenter Server när som helst innan du påbörjar identifieringen.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="Panel 3 på installationens konfigurations hanterare för vCenter Server information":::

### <a name="provide-server-credentials"></a>Ange autentiseringsuppgifter för servern

I **steg 3: ange autentiseringsuppgifter för servern för att utföra program varu inventering, agent lös beroende analys och identifiering av SQL Server instanser och databaser**, kan du antingen välja att ange flera autentiseringsuppgifter för Server eller om du inte vill använda dessa funktioner kan du välja att hoppa över steget och fortsätta med vCenter Server identifiering. Du kan ändra avsikten när som helst senare.

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="Panel 3 på installation Configuration Manager för Server information":::

Om du vill använda dessa funktioner kan du ange autentiseringsuppgifter för servern genom att följa stegen nedan. Installations programmet försöker automatiskt mappa autentiseringsuppgifterna till servrarna för att utföra identifierings funktionerna.

- Du kan lägga till autentiseringsuppgifter för servern genom att klicka på knappen **Lägg till autentiseringsuppgifter** . Då öppnas en spärr ande där du kan välja **typ av autentiseringsuppgifter** i list rutan.
- Du kan ange domän-/Windows-(icke-domän)/Linux-eller SQL Server autentiseringsuppgifter för autentisering. [Lär dig mer](add-server-credentials.md) om hur du anger autentiseringsuppgifter och hur vi hanterar dem.
- För varje typ av autentiseringsuppgifter måste du ange ett eget namn för autentiseringsuppgifterna, lägga till **användar namn** och **lösen ord** och klicka på **Spara**.
- Om du väljer domänautentiseringsuppgifter måste du också ange FQDN för domänen. FQDN krävs för att verifiera äktheten hos autentiseringsuppgifterna med Active Directory i domänen.
- Granska de [behörigheter som krävs](add-server-credentials.md#required-permissions) för kontot för identifiering av installerade program, övervakning utan agent eller för identifiering av SQL Server instanser och databaser.
- Om du vill lägga till flera autentiseringsuppgifter samtidigt klickar du på **Lägg till fler** för att spara och lägga till fler autentiseringsuppgifter.
- När du klickar på **Spara** eller **Lägg till fler**, verifierar installations programmet domänautentiseringsuppgifter med domänens Active Directory för deras äkthet. Detta görs för att undvika eventuella konto utelåsningar när installationen gör att flera iterationer mappar autentiseringsuppgifter till respektive-servrar.
- Du kan se **verifierings status** för alla domänautentiseringsuppgifter i tabellen autentiseringsuppgifter. Endast domänautentiseringsuppgifter kommer att val IDE ras.
- Om verifieringen Miss lyckas kan du klicka på **misslyckad** status för att se felet som påträffades och klicka på reparera **autentiseringsuppgifter** igen när du har åtgärdat problemet för att validera de misslyckade domänautentiseringsuppgifter igen.

     :::image type="content" source="./media/tutorial-discover-vmware/add-server-credentials-multiple.png" alt-text="Panel 3 på installationens konfigurations hanterare för att ange flera autentiseringsuppgifter":::

### <a name="start-discovery"></a>Starta identifiering

1. Klicka på **Starta identifiering** för att starta vCenter Server identifiering. När identifieringen har startats kan du kontrol lera identifierings statusen mot vCenter Server IP-adress/FQDN i tabellen sources.
1. Om du har angett autentiseringsuppgifter för servern startas program varu inventeringen (identifiering av installerade program) automatiskt när identifieringen av vCenter Server har slutförts. Program varu inventeringen utförs en gång var 12: e timme.
1. [Program varu inventeringen](how-to-discover-applications.md) identifierar de SQL Server instanser som körs på servrarna och använder informationen, som försöker ansluta till instanserna via Windows-autentiseringen eller SQL Server autentiseringsuppgifter som anges på enheten och samla in data på SQL Server databaser och deras egenskaper. SQL-identifieringen utförs en gång var 24: e timme.
1. Under program varu inventeringen upprepas de tillagda servrarnas autentiseringsuppgifter mot servrar och val IDE ras för agent avsöknings analys. Du kan aktivera agent avsöknings analys för servrar från portalen. Det går bara att välja de servrar där verifieringen lyckas, för att aktivera en agent lös beroende analys.

> [!Note]
>Azure Migrate krypterar kommunikationen mellan Azure Migrates utrustning och källa SQL Server instanser (med egenskapen krypterad anslutning inställd på TRUE). Dessa anslutningar är krypterade med [**TrustServerCertificate**](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) (anges till sant). transport lagret använder SSL för att kryptera kanalen och kringgå certifikat kedjan för att verifiera förtroende. Installations servern måste vara konfigurerad för att [**lita på certifikatets rot utfärdare**](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).<br/>
Om inget certifikat har etablerats på servern när det startas, genererar SQL Server ett självsignerat certifikat som används för att kryptera inloggnings paket. [**Läs mer**](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

Identifiering fungerar på följande sätt:

- Det tar ungefär 15 minuter för identifierade Server lager att visas i portalen.
- Det kan ta en stund att identifiera installerade program. Varaktigheten beror på antalet identifierade servrar. För 500-servrar tar det cirka en timme innan den identifierade inventeringen visas i Azure Migrate portalen.
- När identifieringen av-servrarna är klar kan du aktivera agentens beroende analys på servrarna från portalen.
- SQL Server instanser och databas data börjar visas i portalen inom 24 timmar från identifierings initieringen.

## <a name="next-steps"></a>Nästa steg

- [Utvärdera servrar](./tutorial-assess-vmware-azure-vm.md) för migrering till virtuella Azure-datorer.
- [Utvärdera SQL-servrar](./tutorial-assess-sql.md) för migrering till Azure SQL.
- [Granska de data](migrate-appliance.md#collected-data---vmware) som enheten samlar in under identifieringen.
