---
title: Konfigurera en Azure Migrate-apparat för VMware
description: Lär dig hur du konfigurerar en Azure Migrate-apparat för att utvärdera och migrera servrar i VMware-miljön.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: 1217b51ea91758d25b76394b27d3b21b2e9808b3
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780879"
---
# <a name="set-up-an-appliance-for-servers-in-vmware-environment"></a>Konfigurera en installation för servrar i VMware-miljön

Följ den här artikeln för att konfigurera Azure Migrate-installationen för utvärdering med verktyget [Azure Migrate: identifiering och bedömning](migrate-services-overview.md#azure-migrate-server-assessment-tool) , och för att migrera utan agent med hjälp av [Azure Migrate: Migreringsverktyg för Server](migrate-services-overview.md#azure-migrate-server-migration-tool) .

[Azure Migrate](migrate-appliance.md) -installationen är en förenklad utrustning som används av Azure Migrate: identifiering och utvärdering och Server migrering för att identifiera servrar som kör i vCenter Server, skicka Server konfiguration och prestanda-metadata till Azure och för replikering av servrar med hjälp av en agent lös migrering.

Du kan distribuera installationen på ett par olika sätt:

- Skapa en server på vCenter Server med en Hämtad områdesmall-mall. Detta är den metod som beskrivs i den här artikeln.
- Konfigurera installationen på en befintlig server med hjälp av ett PowerShell-skript. [Den här metoden](deploy-appliance-script.md) ska användas om du inte kan använda en mall för ägg eller om du är i Azure Government.

När du har skapat installationen kontrollerar du att den kan ansluta till Azure Migrate: identifiering och utvärdering, registrera den med Azure Migrate-projektet och konfigurera installationen till att initiera identifiering.

## <a name="deploy-with-ova"></a>Distribuera med ägg

Så här konfigurerar du installationen av en tjänstmall:
1. Ange ett namn på apparaten och generera en Azure Migrate projekt nyckel i portalen.
1. Hämta en mall för en embryo-fil och importera den till vCenter Server. Kontrol lera att EMBRYOna är säkra.
1. Skapa den virtuella dator enheten från den ägg filen och kontrol lera att den kan ansluta till Azure Migrate.
1. Konfigurera enheten för första gången och registrera den med projektet med hjälp av Azure Migrate projekt nyckel.

### <a name="1-generate-the-azure-migrate-project-key"></a>1. generera Azure Migrate projekt nyckeln

1. I **mål**  >  **servrar** för migrering  >  **Azure Migrate: identifiering och bedömning**, Välj **identifiera**.
2. I **Discover-servrar**  >  **är servrarna virtualiserade?** väljer du **Ja, med VMware vSphere hypervisor**.
3. I **1: generera Azure Migrate projekt nyckel** anger du ett namn för Azure Migrate-installationen som ska konfigureras för identifiering av servrar i VMware-miljön. Namnet måste vara alfanumeriskt med 14 tecken eller färre.
1. Klicka på **generera nyckel** för att starta skapandet av de nödvändiga Azure-resurserna. Stäng inte sidan identifiera när du skapar resurser.
1. När Azure-resurserna har skapats skapas en **Azure Migrate projekt nyckel** .
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
3. Öppna en webbläsare på en server som kan ansluta till installations servern och öppna URL: en för installations hanteraren för installationen: `https://appliance name or IP address: 44368` .

   Alternativt kan du öppna Configuration Manager från apparatens server Skriv bord genom att välja genvägen för Configuration Manager.
1. Godkänn **licens villkoren** och Läs informationen från tredje part.
1. I Configuration Manager > **Konfigurera krav**, gör du följande:
   - **Anslutning**: installationen kontrollerar att servern är ansluten till Internet. Om servern använder en proxyserver:
     - Klicka på **Konfigurera proxy** för att ange proxyadress i formuläret `http://ProxyIPAddress` eller `http://ProxyFQDN` lyssnande port.
     - Ange autentiseringsuppgifter om proxyn kräver autentisering.
     - Endast HTTP-proxy stöds.
     - Om du har lagt till proxyinformation eller inaktiverat proxyn och/eller autentiseringen, klickar du på **Spara** för att utlösa anslutnings kontrollen igen.
   - **Tidssynkronisering**: tiden för installationen bör vara synkroniserad med Internet-tid för att identifieringen ska fungera korrekt.
   - **Installera uppdateringar: installationen** säkerställer att de senaste uppdateringarna är installerade. När kontrollen är klar kan du klicka på **Visa apparat-tjänster** för att se status och versioner för de tjänster som körs på installations servern.
   - **Installera vddk: installations** programmet kontrollerar att VMware vSphere Virtual Disk Development Kit (vddk) har installerats. Om den inte är installerad laddar du ned VDDK 6,7 från VMware och extraherar det hämtade ZIP-innehållet till den angivna platsen på enheten, enligt **anvisningarna i installations anvisningarna**.

     Azure Migrate Server-migrering använder VDDK för att replikera servrar under migreringen till Azure. 
1. Om du vill kan du **köra nödvändiga komponenter** när som helst under installationen av konfigurationen för att kontrol lera om installationen uppfyller alla krav.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="Panel 1 på installationens konfigurations hanterare":::


## <a name="register-the-appliance-with-azure-migrate"></a>Registrera enheten med Azure Migrate

1. Klistra in **Azure Migrate projekt nyckeln** som har kopierats från portalen. Om du inte har nyckeln går du till **identifierings-och utvärderings> identifiera> hantera befintliga apparater**, väljer det installations namn du angav vid tidpunkten för att generera nyckeln och kopierar motsvarande nyckel.
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
1. Du kan **omverifiera** anslutningen till vCenter Server när som helst innan du påbörjar identifieringen.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="Panel 3 på installationens konfigurations hanterare för vCenter Server information":::

### <a name="provide-server-credentials"></a>Ange autentiseringsuppgifter för servern

I **steg 3: ange autentiseringsuppgifter för servern för att utföra program varu inventering, agent lös beroende analys och identifiering av SQL Server instanser och databaser**, kan du antingen välja att ange flera autentiseringsuppgifter för Server eller om du inte vill använda dessa funktioner kan du välja att hoppa över steget och fortsätta med vCenter Server identifiering. Du kan ändra avsikten när som helst senare.

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="Panel 3 på installation Configuration Manager för Server information":::

> [!Note]
> Identifiering och utvärdering av SQL Server instanser och databaser som körs i din VMware-miljö är nu i för hands version. Om du vill testa funktionen använder du [**den här länken**](https://aka.ms/AzureMigrate/SQL) till att skapa ett projekt i regionen **Australien, östra**. Om du redan har ett projekt i Australien, östra och vill prova den här funktionen måste du se till att du har slutfört dessa [**krav**](how-to-discover-sql-existing-project.md) på portalen.

Om du vill utnyttja dessa funktioner kan du ange autentiseringsuppgifter för servern genom att följa stegen nedan. Installations programmet försöker automatiskt mappa autentiseringsuppgifterna till servrarna för att utföra identifierings funktionerna.

- Du kan lägga till autentiseringsuppgifter för servern genom att klicka på knappen **Lägg till autentiseringsuppgifter** . Då öppnas en spärr ande där du kan välja **typ av autentiseringsuppgifter** i list rutan.
- Du kan ange domän-/Windows-(icke-domän)/Linux-eller SQL Server autentiseringsuppgifter för autentisering. [Lär dig mer](add-server-credentials.md) om hur du anger autentiseringsuppgifter och hur vi hanterar dem.
- För varje typ av autentiseringsuppgifter måste du ange ett eget namn för autentiseringsuppgifterna, lägga till **användar namn** och **lösen ord** och klicka på **Spara**.
- Om du väljer domänautentiseringsuppgifter måste du också ange FQDN för domänen. FQDN krävs för att verifiera äktheten hos autentiseringsuppgifterna med Active Directory i domänen.
- Granska de [behörigheter som krävs](add-server-credentials.md#required-permissions) för kontot för identifiering av installerade program, övervakning utan agent eller för identifiering av SQL Server instanser och databaser.
- Om du vill lägga till flera autentiseringsuppgifter samtidigt klickar du på **Lägg till fler** för att spara och lägga till fler autentiseringsuppgifter.
- När du klickar på **Spara** eller **Lägg till fler**, verifierar installations programmet domänautentiseringsuppgifter med domänens Active Directory för deras äkthet. Detta görs för att undvika eventuella konto utelåsningar när installationen gör att flera iterationer mappar autentiseringsuppgifter till respektive-servrar.
- Du kan se **verifierings status** för alla domänautentiseringsuppgifter i tabellen autentiseringsuppgifter. Endast domänautentiseringsuppgifter kommer att val IDE ras.
- Om verifieringen Miss lyckas kan du klicka på **misslyckad** status för att se felet som påträffades och klicka på reparera **autentiseringsuppgifter** igen när du har åtgärdat problemet för att validera de misslyckade domänautentiseringsuppgifter igen.


### <a name="start-discovery"></a>Starta identifiering

1. Klicka på **Starta identifiering** för att starta vCenter Server identifiering. När identifieringen har startats kan du kontrol lera identifierings statusen mot vCenter Server IP-adress/FQDN i tabellen sources.
1. Om du har angett autentiseringsuppgifter för servern startas program varu inventeringen (identifiering av installerade program) automatiskt när identifieringen av vCenter Server har slutförts. Program varu inventeringen utförs en gång var 12: e timme.
1. [Program varu inventeringen](how-to-discover-applications.md) identifierar de SQL Server instanser som körs på servrarna och använder informationen, som försöker ansluta till instanserna via Windows-autentiseringen eller SQL Server autentiseringsuppgifter som anges på enheten och samla in data på SQL Server databaser och deras egenskaper. SQL-identifieringen utförs en gång var 24: e timme.
1. Under program varu inventeringen upprepas de tillagda servrarnas autentiseringsuppgifter mot servrar och val IDE ras för agent avsöknings analys. Du kan aktivera agent avsöknings analys för servrar från portalen. Det går bara att välja de servrar där verifieringen lyckas, för att aktivera en agent lös beroende analys.

Identifiering fungerar på följande sätt:
- Det tar ungefär 15 minuter för identifierade Server lager att visas i portalen.
- Det kan ta en stund att identifiera installerade program. Varaktigheten beror på antalet identifierade servrar. För 500-servrar tar det cirka en timme innan den identifierade inventeringen visas i Azure Migrate portalen.
- När identifieringen av-servrarna är klar kan du aktivera agentens beroende analys på servrarna från portalen.
- SQL Server instanser och databas data börjar visas i portalen inom 24 timmar från identifierings initieringen.

## <a name="next-steps"></a>Nästa steg

Gå igenom självstudierna för [VMware-utvärdering](./tutorial-assess-vmware-azure-vm.md) och [migrering utan agent](tutorial-migrate-vmware.md).