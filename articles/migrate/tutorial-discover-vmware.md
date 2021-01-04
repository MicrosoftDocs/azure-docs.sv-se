---
title: Identifiera virtuella VMware-datorer med utvärdering av Azure Migrate Server
description: Lär dig att identifiera lokala virtuella VMware-datorer med verktyget för Azure Migrate Server bedömning
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: e11c3277ffa07fe0a8d5fc7495e2c09152ce585f
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704303"
---
# <a name="tutorial-discover-vmware-vms-with-server-assessment"></a>Självstudie: identifiera virtuella VMware-datorer med Server utvärdering

Som en del av migreringen till Azure identifierar du din lokala inventering och dina arbets belastningar. 

Den här självstudien visar hur du identifierar lokala virtuella VMware-datorer (VM: ar) med verktyget Azure Migrate: Server bedömning med en förenklad Azure Migrate-apparat. Du distribuerar installationen som en virtuell VMware-dator för att kontinuerligt identifiera VM-och prestanda-metadata, appar som körs på virtuella datorer och virtuella dator beroenden.

I de här självstudierna får du lära dig att

> [!div class="checklist"]
> * Konfigurera ett Azure-konto.
> * Förbered VMware-miljön för identifiering.
> * Skapa ett Azure Migrate-projekt.
> * Konfigurera Azure Migrate-enheten.
> * Starta kontinuerlig identifiering.

> [!NOTE]
> Självstudier visar den snabbaste sökvägen för att testa ett scenario och använda standard alternativ där det är möjligt.  

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.


## <a name="prerequisites"></a>Förutsättningar

Innan du påbörjar den här självstudien måste du kontrol lera att du har dessa krav på plats.


**Krav** | **Information**
--- | ---
**vCenter Server/ESXi-värd** | Du behöver ett vCenter Server som kör version 5,5, 6,0, 6,5 eller 6,7.<br/><br/> Virtuella datorer måste finnas på en ESXi-värd som kör version 5,5 eller senare.<br/><br/> På vCenter Server tillåter du inkommande anslutningar på TCP-port 443, så att du kan samla in utvärderings data på enheten.<br/><br/> Enheten ansluter som standard till vCenter på port 443. Om vCenter-servern lyssnar på en annan port kan du ändra porten när du ansluter från installations programmet till servern för att starta identifieringen.<br/><br/> På den EXSi-server som är värd för de virtuella datorerna kontrollerar du att inkommande åtkomst är tillåten på TCP-port 443 för identifiering av appar.
**Enhet** | vCenter Server behöver resurser för att allokera en virtuell dator för Azure Migrates apparaten:<br/><br/> - Windows Server 2016<br/><br/> – 32 GB RAM, åtta virtuella processorer och cirka 80 GB disk lagring.<br/><br/> – En extern virtuell växel och Internet åtkomst på för den virtuella datorn, direkt eller via en proxyserver.
**Virtuella datorer** | För att kunna använda den här självstudien måste virtuella Windows-datorer köra Windows Server 2016, 2012 R2, 2012 eller 2008 R2.<br/><br/> Virtuella Linux-datorer måste köra Red Hat Enterprise Linux 7/6/5, Ubuntu Linux 14.04/16.04, Debian 7/8, Oracle Linux 6/7 eller CentOS 5/6/7.<br/><br/> Virtuella datorer behöver VMware-verktyg (en senare version än 10.2.0) installerade och körs.<br/><br/> Windows PowerShell 2,0 eller senare bör installeras på virtuella Windows-datorer.


## <a name="prepare-an-azure-user-account"></a>Förbereda ett Azure-användarkonto

Om du vill skapa ett Azure Migrate-projekt och registrera Azure Migrate-enheten måste du ha ett konto med:
- Deltagar-eller ägar behörigheter för en Azure-prenumeration.
- Behörighet att registrera Azure Active Directory appar.

Om du nyligen skapade ett kostnadsfritt Azure-konto är du ägare av prenumerationen. Om du inte är prenumerations ägare kan du arbeta med ägaren för att tilldela behörigheterna på följande sätt:

1. I Azure Portal söker du efter "prenumerationer" och under **tjänster** väljer du **prenumerationer**.

    ![Sök i rutan för att söka efter Azure-prenumerationen](./media/tutorial-discover-vmware/search-subscription.png)

2. På sidan **prenumerationer** väljer du den prenumeration där du vill skapa ett Azure Migrate-projekt. 
3. I prenumerationen väljer du **åtkomst kontroll (IAM)**  >  **kontrol lera åtkomst**.
4. I **kontrol lera åtkomst** söker du efter det relevanta användar kontot.
5. I **Lägg till en roll tilldelning** klickar du på **Lägg till**.

    ![Sök efter ett användar konto för att kontrol lera åtkomst och tilldela en roll](./media/tutorial-discover-vmware/azure-account-access.png)

6. I **Lägg till roll tilldelning** väljer du rollen deltagare eller ägare och väljer kontot (azmigrateuser i vårt exempel). Klicka sedan på **Spara**.

    ![Öppnar sidan Lägg till roll tilldelning för att tilldela kontot en roll](./media/tutorial-discover-vmware/assign-role.png)

7. I portalen söker du efter användare och under **tjänster** väljer **du användare**.
8. I **användar inställningar** kontrollerar du att Azure AD-användare kan registrera program (anges till **Ja** som standard).

    ![Verifiera i användar inställningar som användare kan registrera Active Directory appar](./media/tutorial-discover-vmware/register-apps.png)

9. Alternativt kan klient organisationen/den globala administratören tilldela rollen **programutvecklare** till ett konto för att tillåta registrering av AAD-appar. [Läs mer](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-vmware"></a>Förbereda VMware

På vCenter Server skapar du ett konto som enheten kan använda för att få åtkomst till vCenter Server och kontrollerar att de portar som krävs är öppna. Du måste också ha ett konto som kan användas av enheten för att komma åt virtuella datorer. 

### <a name="create-an-account-to-access-vcenter"></a>Skapa ett konto för åtkomst till vCenter

I vSphere-webbklient skapar du ett konto på följande sätt:

1. Med hjälp av ett konto med administratörs behörighet går du till vSphere-webbklienten > väljer **Administration**.
2. **Åtkomst** väljer du **SSO-användare och-grupper**.
3. I **användare lägger du** till en ny användare.
4. I **ny användare**, anger du konto informationen. Klicka sedan på **OK**.
5. I **globala behörigheter** väljer du användar kontot och tilldelar kontot den **skrivskyddade** rollen. Klicka sedan på **OK**.
6. I **roller** > väljer du **skrivskyddad** roll och i **privilegier** väljer du **gäst åtgärder**. De här behörigheterna krävs för att identifiera appar som körs på virtuella datorer och analysera VM-beroenden.
 
    ![Kryss ruta för att tillåta gäst åtgärder på den skrivskyddade rollen](./media/tutorial-discover-vmware/guest-operations.png)


### <a name="create-an-account-to-access-vms"></a>Skapa ett konto för åtkomst till virtuella datorer

Enheten får åtkomst till virtuella datorer för att identifiera appar och analysera virtuella dator beroenden. Installations programmet installerar inte några agenter på virtuella datorer.

1. Skapa ett lokalt administratörs konto som kan användas av enheten för att identifiera appar och beroenden på virtuella Windows-datorer.
2. För Linux-datorer skapar du ett användar konto med rot privilegier eller alternativt ett användar konto med dessa behörigheter för/bin/netstat-och/bin/ls-filer: CAP_DAC_READ_SEARCH och CAP_SYS_PTRACE.

> [!NOTE]
> Azure Migrate stöder en autentiseringsuppgift för app-Discovery på alla Windows-servrar och en autentiseringsuppgift för app-Discovery på alla Linux-datorer.


## <a name="set-up-a-project"></a>Konfigurera ett projekt

Skapa ett nytt Azure Migrate-projekt.

1. I Azure-portalen > **Alla tjänster** söker du efter **Azure Migrate**.
2. Under **Tjänster** väljer du **Azure Migrate**.
3. I **Översikt** väljer du **skapa projekt**.
5. I **skapa projekt** väljer du din Azure-prenumeration och resurs grupp. Skapa en resurs grupp om du inte har någon.
6. I **projekt information** anger du projekt namnet och geografin som du vill skapa projektet i. Granska stödda geografiska områden för [offentliga](migrate-support-matrix.md#supported-geographies-public-cloud) och [offentliga moln](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Rutor för projekt namn och region](./media/tutorial-discover-vmware/new-project.png)

7. Välj **Skapa**.
8. Vänta några minuter tills Azure Migrate-projektet har distribuerats.

Verktyget **Azure Migrate: Server bedömning** läggs till som standard i det nya projektet.

![Sida som visar verktyget för Server bedömning som har lagts till som standard](./media/tutorial-discover-vmware/added-tool.png)


## <a name="set-up-the-appliance"></a>Konfigurera installationen

Så här konfigurerar du installationen av en tjänstmall:
- Ange ett namn på apparaten och generera en Azure Migrate projekt nyckel i portalen
- Hämta en mall för en embryo-fil och importera den till vCenter Server.
- Skapa installationen och kontrol lera att den kan ansluta till Azure Migrate Server utvärdering.
- Konfigurera enheten för första gången och registrera den med det Azure Migrate projektet med hjälp av Azure Migrate projekt nyckeln.

> [!NOTE]
> Om du av någon anledning inte kan konfigurera installationen med hjälp av mallen kan du konfigurera den med hjälp av ett PowerShell-skript. [Läs mer](deploy-appliance-script.md#set-up-the-appliance-for-vmware).


### <a name="deploy-with-ova"></a>Distribuera med ägg

Så här konfigurerar du installationen av en tjänstmall:
- Ange ett namn på apparaten och generera en Azure Migrate projekt nyckel i portalen
- Hämta en mall för en embryo-fil och importera den till vCenter Server.
- Skapa installationen och kontrol lera att den kan ansluta till Azure Migrate Server utvärdering.
- Konfigurera enheten för första gången och registrera den med det Azure Migrate projektet med hjälp av Azure Migrate projekt nyckeln.

### <a name="generate-the-azure-migrate-project-key"></a>Generera Azure Migrate projekt nyckel

1. I **Migreringsmål** > **Servrar** > **Azure Migrate: Serverutvärdering** väljer du **Identifiera**.
2. I **identifiera datorer**  >  **är dina datorer virtualiserade?** väljer du **Ja, med VMware vSphere hypervisor**.
3. I **1: generera Azure Migrate projekt nyckel** anger du ett namn för Azure Migrate-installationen som ska konfigureras för identifiering av virtuella VMware-datorer. namnet måste vara alfanumeriskt med 14 tecken eller färre.
1. Klicka på **generera nyckel** för att starta skapandet av de nödvändiga Azure-resurserna. Stäng inte sidan identifiera datorer när du skapar resurser.
1. När Azure-resurserna har skapats skapas en **Azure Migrate projekt nyckel** .
1. Kopiera nyckeln på samma sätt som du behöver den för att slutföra registreringen av enheten under konfigurationen.

### <a name="download-the-ova-template"></a>Ladda ned mallen för ägg

I **2: Ladda ned Azure Migrate-enheten** väljer du. ÄGG filen och klicka på **Hämta**. 


### <a name="verify-security"></a>Verifiera säkerhet

Kontrol lera att ägg filen är säker innan du distribuerar den:

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
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




### <a name="create-the-appliance-vm"></a>Skapa VM-enheten

Importera den nedladdade filen och skapa en virtuell dator.

1. I klient konsolen för vSphere klickar du på **fil**  >  **distribution OVF mall**.
2. I guiden Distribuera OVF-mall > **källa** anger du platsen för ägg filen.
3. I **namn** och **plats** anger du ett eget namn för den virtuella datorn. Välj det lager objekt som den virtuella datorn ska vara värd för.
5. I **värd/kluster** anger du den värd eller det kluster som den virtuella datorn ska köras på.
6. I **lagring** anger du lagrings målet för den virtuella datorn.
7. I **Diskformat** anger du disktyp och storlek.
8. I **nätverks mappning** anger du det nätverk som den virtuella datorn ska ansluta till. Nätverket måste ha Internet anslutning för att kunna skicka metadata till Azure Migrate Server bedömning.
9. Granska och bekräfta inställningarna. Klicka sedan på **Slutför**.


### <a name="verify-appliance-access-to-azure"></a>Verifiera åtkomst till enheten till Azure

Se till att den virtuella datorns virtuella datorer kan ansluta till Azure-URL: er för [offentliga](migrate-appliance.md#public-cloud-urls) och [offentliga](migrate-appliance.md#government-cloud-urls) moln.


### <a name="configure-the-appliance"></a>Konfigurera installationen

Konfigurera enheten för första gången.

> [!NOTE]
> Om du ställer in installationen med hjälp av ett [PowerShell-skript](deploy-appliance-script.md) i stället för hämtade ägg, är de två första stegen i den här proceduren inte relevanta.

1. I klient konsolen för vSphere högerklickar du på den virtuella datorn och väljer sedan **Öppna konsol**.
2. Ange språk, tidszon och lösen ord för enheten.
3. Öppna en webbläsare på vilken dator som helst som kan ansluta till den virtuella datorn och öppna URL: en för installations programmets webbapp: **https://-enhetens *namn eller IP-adress*: 44368**.

   Alternativt kan du öppna appen från apparatens skriv bord genom att välja genvägen till appen.
1. Godkänn **licens villkoren** och Läs informationen från tredje part.
1. I webbappen > **Konfigurera krav** gör du följande:
   - **Anslutning**: appen kontrollerar att den virtuella datorn har Internet åtkomst. Om den virtuella datorn använder en proxyserver:
     - Klicka på **Konfigurera proxy** för att ange proxyadress (i formuläret http://ProxyIPAddress eller http://ProxyFQDN) lyssnande port.
     - Ange autentiseringsuppgifter om proxyn kräver autentisering.
     - Endast HTTP-proxy stöds.
     - Om du har lagt till proxyinformation eller inaktiverat proxyn och/eller autentiseringen, klickar du på **Spara** för att utlösa anslutnings kontrollen igen.
   - **Tidssynkronisering**: tiden för installationen bör vara synkroniserad med Internet-tid för att identifieringen ska fungera korrekt.
   - **Installera uppdateringar: installationen** säkerställer att de senaste uppdateringarna är installerade. När kontrollen är klar kan du klicka på **Visa apparat-tjänster** för att se status och versioner för komponenterna som körs på produkten.
   - **Installera vddk: installations** programmet kontrollerar att VMware vSphere Virtual Disk Development Kit (vddk) har installerats. Om den inte är installerad laddar du ned VDDK 6,7 från VMware och extraherar det hämtade ZIP-innehållet till den angivna platsen på enheten, enligt **anvisningarna i installations anvisningarna**.

     Azure Migrate Server-migrering använder VDDK för att replikera datorer under migreringen till Azure. 
1. Om du vill kan du **köra nödvändiga komponenter** när som helst under installationen av konfigurationen för att kontrol lera om installationen uppfyller alla krav.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrera enheten med Azure Migrate

1. Klistra in **Azure Migrate projekt nyckeln** som har kopierats från portalen. Om du inte har nyckeln går du till **Server utvärdering> identifiera> hantera befintliga apparater**, väljer det installations namn som du angav vid tidpunkten för att generera nyckeln och kopierar motsvarande nyckel.
1. Du behöver en enhets kod för att autentisera med Azure. När du klickar på **Logga in** öppnas en modal enhets kod som visas nedan.

    ![Modal visar enhets koden](./media/tutorial-discover-vmware/device-code.png)

1. Klicka på **Kopiera kod & inloggning** för att kopiera enhets koden och öppna en Azure-inloggnings tolk i en ny webbläsare-flik. Om den inte visas kontrollerar du att du har inaktiverat blockering av popup-fönster i webbläsaren.
1. På fliken nytt klistrar du in enhets koden och loggar in med ditt användar namn och lösen ord för Azure.
   
   Inloggning med en PIN-kod stöds inte.
3. Om du stänger fliken inloggning oavsiktligt utan att logga in, måste du uppdatera fliken webbläsare i Konfigurations hanteraren för att aktivera inloggnings knappen igen.
1. När du har loggat in går du tillbaka till föregående flik med installationen av Konfigurations hanteraren.
1. Om Azure-användarkontot som används för loggning har rätt behörigheter för de Azure-resurser som skapades under den här nyckeln, initieras registrerings enheten.
1. När installationen av enheten har registrerats kan du se registrerings informationen genom att klicka på **Visa information**.



## <a name="start-continuous-discovery"></a>Starta kontinuerlig identifiering

Installations programmet måste ansluta till vCenter Server för att identifiera konfigurations-och prestanda data för de virtuella datorerna.

1. I **steg 1: ange vCenter Server autentiseringsuppgifter** klickar du på **Lägg till autentiseringsuppgifter** för att ange ett eget namn för autentiseringsuppgifter, Lägg till **användar namn** och **lösen ord** för det vCenter servers konto som ska användas för att identifiera virtuella datorer på vCenter Server-instansen.
    - Du bör ha skapat ett konto med de behörigheter som krävs i föregående självstudie.
    - Om du vill begränsa identifieringen till specifika VMware-objekt (vCenter Server Data Center, kluster, en mapp med kluster, värdar, en mapp med värdar eller enskilda virtuella datorer) läser du anvisningarna i [den här artikeln](set-discovery-scope.md) för att begränsa det konto som används av Azure Migrate.
1. I **steg 2: ange vCenter Server information** klickar du på **Lägg till identifierings källa** och väljer det egna namnet för autentiseringsuppgifter i list rutan. Ange **IP-adressen/FQDN** för vCenter Server-instansen. Du kan lämna **porten** till standard (443) eller ange en anpassad Port där vCenter Server lyssnar och klicka på **Spara**.
1. När du klickar på Spara kommer installations programmet att försöka verifiera anslutningen till vCenter Server med de angivna autentiseringsuppgifterna och visa **verifierings status** i tabellen mot vCenter Server IP-adress/FQDN.
1. Du kan **omverifiera** anslutningen till vCenter Server när som helst innan du påbörjar identifieringen.
1. I **steg 3: ange autentiseringsuppgifter för virtuella datorer för att identifiera installerade program och utföra en agent lös beroende mappning**, klickar du på **Lägg till autentiseringsuppgifter** och anger det operativ system för vilket autentiseringsuppgifterna anges, eget namn för autentiseringsuppgifter och **användar namn** och **lösen ord**. Klicka sedan på **Spara**.

    - Du kan också lägga till autentiseringsuppgifter här om du har skapat ett konto som ska användas för [program identifierings funktionen](how-to-discover-applications.md)eller [funktionen för beroende analys av agent](how-to-create-group-machine-dependencies-agentless.md).
    - Om du inte vill använda dessa funktioner kan du klicka på skjutreglaget för att hoppa över steget. Du kan ändra avsikten när som helst senare.
    - Granska de autentiseringsuppgifter som krävs för [identifiering av program](migrate-support-matrix-vmware.md#application-discovery-requirements)eller för analys av [beroenden för agenter](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless).

5. Klicka på **Starta identifiering** för att starta identifiering av virtuell dator. När identifieringen har startats kan du kontrol lera identifierings statusen mot vCenter Server IP-adress/FQDN i tabellen.

Identifiering fungerar på följande sätt:
- Det tar ungefär 15 minuter för identifierade VM-metadata som visas i portalen.
- Det tar lite tid att identifiera installerade program, roller och funktioner. Varaktigheten beror på antalet virtuella datorer som identifieras. För 500 virtuella datorer tar det cirka en timme för program inventeringen att visas i Azure Migrate portalen.


## <a name="next-steps"></a>Nästa steg

- [Utvärdera virtuella VMware-datorer](./tutorial-assess-vmware-azure-vm.md) för migrering till virtuella Azure-datorer.
- [Granska de data](migrate-appliance.md#collected-data---vmware) som enheten samlar in under identifieringen.