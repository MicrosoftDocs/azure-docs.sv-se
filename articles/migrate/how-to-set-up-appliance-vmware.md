---
title: Konfigurera en Azure Migrate-apparat för VMware
description: Lär dig hur du konfigurerar en Azure Migrate-apparat för att utvärdera och migrera virtuella VMware-datorer.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: 749dd17c6f9b09db1841459da951b44470d8bb92
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704715"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Konfigurera en installation för virtuella VMware-datorer

Följ den här artikeln för att konfigurera Azure Migrate-installationen för utvärdering med verktyget [Azure Migrate: Server utvärdering](migrate-services-overview.md#azure-migrate-server-assessment-tool) och för återställning utan agent med hjälp av [Azure Migrate: Migreringsverktyg för Server](migrate-services-overview.md#azure-migrate-server-migration-tool) .

[Azure Migrate](migrate-appliance.md) -installationen är en förenklad installation som används av Azure Migrate: Server utvärdering och Server migrering för att identifiera lokala virtuella VMware-datorer, skicka VM-metadata/prestanda data till Azure och för replikering av virtuella VMware-datorer under en agent lös migrering.

Du kan distribuera installationen på ett par olika sätt:

- Konfigurera på en virtuell VMware-dator med en Hämtad mall för ägg. Detta är den metod som beskrivs i den här artikeln.
- Konfigurera på en virtuell VMware-dator eller en fysisk dator med ett PowerShell-skript. [Den här metoden](deploy-appliance-script.md) ska användas om du inte kan konfigurera en virtuell dator med en behållar mall eller om du är i Azure-myndigheter.

När du har skapat enheten kontrollerar du att den kan ansluta till Azure Migrate: Server utvärdering, konfigurera den för första gången och registrera den med Azure Migrate projektet.


## <a name="appliance-deployment-ova"></a>Installation av utrustning (ägg)

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


   ![Val för identifiering av datorer](./media/tutorial-assess-vmware/servers-discover.png)


   ![Val för generera nyckel](./media/tutorial-assess-vmware/generate-key-vmware.png)

### <a name="verify-security"></a>Verifiera säkerhet

Kontrol lera att ägg filen är säker innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera hash-värdet för de ägg:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel på användning: ```C:\>C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```
3. För den senaste versionen av produkten ska den genererade hashen matcha de här [inställningarna](./tutorial-discover-vmware.md#verify-security).



## <a name="create-the-appliance-vm"></a>Skapa VM-enheten

Importera den nedladdade filen och skapa en virtuell dator.

1. I klient konsolen för vSphere klickar du på **fil**  >  **distribution OVF mall**.
![Meny kommando för att distribuera en OVF-mall](./media/tutorial-assess-vmware/deploy-ovf.png)

2. I guiden Distribuera OVF-mall > **källa** anger du platsen för ägg filen.
3. I **namn** och **plats** anger du ett eget namn för den virtuella datorn. Välj det lager objekt som den virtuella datorn ska vara värd för.
5. I **värd/kluster** anger du den värd eller det kluster som den virtuella datorn ska köras på.
6. I **lagring** anger du lagrings målet för den virtuella datorn.
7. I **Diskformat** anger du disktyp och storlek.
8. I **nätverks mappning** anger du det nätverk som den virtuella datorn ska ansluta till. Nätverket måste ha Internet anslutning för att kunna skicka metadata till Azure Migrate Server bedömning.
9. Granska och bekräfta inställningarna. Klicka sedan på **Slutför**.


## <a name="verify-appliance-access-to-azure"></a>Verifiera åtkomst till enheten till Azure

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
4. Om Azure-användarkontot som används för loggning har rätt [behörigheter](./tutorial-discover-vmware.md#prepare-an-azure-user-account) för de Azure-resurser som skapades under den här nyckeln, initieras registrerings enheten.
1. När installationen av enheten har registrerats kan du se registrerings informationen genom att klicka på **Visa information**.


## <a name="start-continuous-discovery"></a>Starta kontinuerlig identifiering

Installations programmet måste ansluta till vCenter Server för att identifiera konfigurations-och prestanda data för de virtuella datorerna.

1. I **steg 1: ange vCenter Server autentiseringsuppgifter** klickar du på **Lägg till autentiseringsuppgifter** för att ange ett eget namn för autentiseringsuppgifter, Lägg till **användar namn** och **lösen ord** för det vCenter servers konto som ska användas för att identifiera virtuella datorer på vCenter Server-instansen.
    - Du bör ha skapat ett konto med de behörigheter som krävs i [föregående självstudie](./tutorial-discover-vmware.md#create-an-account-to-access-vcenter).
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

Gå igenom självstudierna för [VMware-utvärdering](./tutorial-assess-vmware-azure-vm.md) och [migrering utan agent](tutorial-migrate-vmware.md).