---
title: Använd & migrera ASP.NET-program till Azure Kubernetes
description: 'Självstudie: Använd & migrera ASP.NET-program till Azure Kubernetes service.'
services: ''
author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 3/2/2021
ms.author: rahugup
ms.openlocfilehash: 6be6db2048ac5e671d8ab988ac2e15c08e900193
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103233675"
---
# <a name="containerize-aspnet-applications-and-migrate-to-azure-kubernetes-service"></a>Använd ASP.NET-program och migrera till Azure Kubernetes service

I den här artikeln får du lära dig hur du Använd ASP.NET-program och migrerar dem till [Azure Kubernetes service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) med hjälp av verktyget Azure Migrate: app skapa behållare. Skapa behållare-processen kräver inte åtkomst till kodbasen och ger ett enkelt sätt att Använd befintliga program. Verktyget fungerar med körnings status för program på en server för att fastställa program komponenterna och hjälper dig att paketera dem i en behållar avbildning. Det containerbaserade programmet kan sedan distribueras i Azure Kubernetes service (AKS).

Verktyget Azure Migrate: app skapa behållare stöder för närvarande-

- Containerrar ASP.NET-appar och distribuerar dem i Windows-behållare på AKS.
- Behållare för Java-Web Apps på Apache Tomcat (på Linux-servrar) och distribuera dem på Linux-behållare på AKS. [Läs mer](./tutorial-containerize-java-kubernetes.md)


Verktyget Azure Migrate: app skapa behållare hjälper dig att

- **Identifiera ditt program**: verktyget fjärransluter till program servrar som kör ditt ASP.NET-program och identifierar program komponenterna. Verktyget skapar en Dockerfile som kan användas för att skapa en behållar avbildning för programmet.
- **Bygg behållar avbildningen**: du kan kontrol lera och ytterligare anpassa Dockerfile enligt dina program krav och använda det för att bygga program behållar avbildningen. Program behållar avbildningen skickas till en Azure Container Registry som du anger.
- **Distribuera till Azure Kubernetes service**: verktyget genererar sedan de Kubernetes resurs DEFINITIONs yaml-filer som behövs för att distribuera program varan till Azure Kubernetes-tjänsteklustret. Du kan anpassa YAML-filerna och använda dem för att distribuera programmet på AKS.

> [!NOTE]
> Verktyget Azure Migrate: app skapa behållare hjälper dig att identifiera specifika program typer (ASP.NET-och Java-webbappar på Apache Tomcat) och deras komponenter på en program Server. Om du vill identifiera servrar och inventering av appar, roller och funktioner som körs på lokala datorer använder du Azure Migrate: identifierings-och utvärderings funktioner. [Läs mer](./tutorial-discover-vmware.md)

Även om alla program inte drar nytta av ett rakt Skift till behållare utan betydande omdelning, är några av fördelarna med att flytta befintliga appar till behållare utan att skriva om:

- **Förbättrad infrastruktur användning:** Med behållare kan flera program dela resurser och vara värdbaserade på samma infrastruktur. Detta kan hjälpa dig att konsolidera infrastrukturen och förbättra användningen.
- **Förenklad hantering:** Genom att vara värd för dina program på en modern hanterad infrastruktur plattform som AKS kan du förenkla dina hanterings metoder samtidigt som du behåller kontrollen över din infrastruktur. Du kan åstadkomma detta genom att ta bort eller minska de underhålls-och hanterings processer för infrastrukturen som du traditionellt utför med den ägda infrastrukturen.
- **Program portabilitet:** Med ökad antagande och standardisering av behållar Specifikations format och Orchestration-plattformar, är program portabiliteten inte längre något problem.
- **Inför modern hantering med DevOps:** Hjälper dig att införa och standardisera moderna metoder för hantering och säkerhet med infrastruktur som kod och över gång till DevOps.


I den här självstudien får du lära dig att:

> [!div class="checklist"]
> * Konfigurera ett Azure-konto.
> * Installera verktyget Azure Migrate: app skapa behållare.
> * Identifiera ditt ASP.NET-program.
> * Bygg behållar avbildningen.
> * Distribuera programmet i behållare på AKS.

> [!NOTE]
> Självstudier visar dig den enklaste distributions Sök vägen för ett scenario så att du snabbt kan konfigurera ett koncept för koncept bevis. Självstudierna använder standardalternativ där så är möjligt och visar inte alla möjliga inställningar och sökvägar.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar de här självstudierna bör du:

**Krav** | **Information**
--- | ---
**Identifiera en dator för att installera verktyget** | En Windows-dator för att installera och köra verktyget Azure Migrate: app skapa behållare. Windows-datorn kan vara en server (Windows Server 2016 eller senare) eller ett klient (Windows 10)-operativ system, vilket innebär att verktyget kan köras på Skriv bordet också. <br/><br/> Windows-datorn som kör verktyget bör ha nätverks anslutning till de servrar/virtuella datorer som är värd för de ASP.NET-program som ska behållas.<br/><br/> Se till att det finns 6 GB ledigt utrymme på Windows-datorn som kör verktyget Azure Migrate: app skapa behållare för att lagra program artefakter. <br/><br/> Windows-datorn ska ha Internet åtkomst, direkt eller via en proxyserver. <br/> <br/>Installera Microsoft Web Deploy-verktyget på datorn som kör appen skapa behållare Helper Tool och program server om det inte redan är installerat. Du kan hämta [verktyget härifrån](https://aka.ms/webdeploy3.6)
**Programservrar** | Aktivera PowerShell-fjärrkommunikation på program servrarna: Logga in på program servern och följ [dessa](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/enable-psremoting) instruktioner för att aktivera PowerShell-fjärrkommunikation. <br/><br/> Om program servern kör Windows Server 2008 R2 kontrollerar du att PowerShell 5,1 är installerat på program servern. Följ anvisningarna [här](https://docs.microsoft.com/powershell/scripting/windows-powershell/wmf/setup/install-configure) för att ladda ned och installera PowerShell 5,1 på program servern. <br/><br/> Installera Microsoft Web Deploy-verktyget på datorn som kör appen skapa behållare Helper Tool och program server om det inte redan är installerat. Du kan hämta [verktyget härifrån](https://aka.ms/webdeploy3.6)
**ASP.NET-program** | Verktyget stöder för närvarande <br/><br/> -ASP.NET program som använder Microsoft .NET Framework 3,5 eller senare.<br/> – Program servrar som kör Windows Server 2008 R2 eller senare (program servrar ska köra PowerShell-version 5,1). <br/> – Program som körs på Internet Information Services (IIS) 7,5 eller senare. <br/><br/> Verktyget stöder för närvarande inte <br/><br/> – Program som kräver Windows-autentisering (AKS stöder inte gMSA för närvarande). <br/> – Program som är beroende av andra Windows-tjänster som finns utanför IIS.


## <a name="prepare-an-azure-user-account"></a>Förbereda ett Azure-användarkonto

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.

När din prenumeration har kon figurer ATS behöver du ett Azure-användar konto med:
- Ägar behörigheter för Azure-prenumerationen
- Behörigheter för att registrera Azure Active Directory appar

Om du nyligen skapade ett kostnadsfritt Azure-konto är du ägare av prenumerationen. Om du inte är prenumerations ägare kan du arbeta med ägaren för att tilldela behörigheterna på följande sätt:

1. I Azure Portal söker du efter "prenumerationer" och under **tjänster** väljer du **prenumerationer**.

    ![Sök i rutan för att söka efter Azure-prenumerationen.](./media/tutorial-discover-vmware/search-subscription.png)

2. På sidan **prenumerationer** väljer du den prenumeration där du vill skapa ett Azure Migrate-projekt.
3. I prenumerationen väljer du **åtkomst kontroll (IAM)**  >  **kontrol lera åtkomst**.
4. I **kontrol lera åtkomst** söker du efter det relevanta användar kontot.
5. I **Lägg till en roll tilldelning** klickar du på **Lägg till**.

    ![Sök efter ett användar konto för att kontrol lera åtkomst och tilldela en roll.](./media/tutorial-discover-vmware/azure-account-access.png)

6. I **Lägg till roll tilldelning** väljer du ägar rollen och väljer kontot (azmigrateuser i vårt exempel). Klicka sedan på **Spara**.

    ![Öppnar sidan Lägg till roll tilldelning för att tilldela kontot en roll.](./media/tutorial-discover-vmware/assign-role.png)

7. Ditt Azure-konto måste också ha **behörighet att registrera Azure Active Directory appar.**
8. I Azure Portal navigerar du till **Azure Active Directory**  >  **användares**  >  **användar inställningar**.
9. I **användar inställningar** kontrollerar du att Azure AD-användare kan registrera program (anges till **Ja** som standard).

      ![Verifiera i användar inställningar som användarna kan registrera Active Directory appar.](./media/tutorial-discover-vmware/register-apps.png)

10. Om inställningen "Appregistreringar" är inställd på "nej", ber du klienten/den globala administratören att tilldela den behörighet som krävs. Alternativt kan klient organisationen/den globala administratören tilldela rollen **programutvecklare** till ett konto för att tillåta registrering av Azure Active Directory-appen. [Läs mer](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).


## <a name="download-and-install-azure-migrate-app-containerization-tool"></a>Hämta och installera Azure Migrate: app skapa behållare-verktyg

1. [Hämta](https://go.microsoft.com/fwlink/?linkid=2134571) installations programmet Azure Migrate: app skapa behållare Installer på en Windows-dator.
2. Starta PowerShell i administratörs läge och ändra PowerShell-katalogen till den mapp som innehåller installations programmet.
3. Kör installations skriptet med kommandot

   ```powershell
   .\App ContainerizationInstaller.ps1
   ```

## <a name="launch-the-app-containerization-tool"></a>Starta verktyget app skapa behållare

1. Öppna en webbläsare på vilken dator som helst som kan ansluta till Windows-datorn som kör app skapa behållare-verktyget och öppna verktygs-URL: **https://*dator namn eller IP-adress*: 44368**.

   Alternativt kan du öppna appen från Skriv bordet genom att välja genvägen till appen.

2. Om du ser en varning om att din anslutning inte är privat klickar du på Avancerat och väljer att fortsätta till webbplatsen. Den här varningen visas när webb gränssnittet använder ett självsignerat TLS/SSL-certifikat.
3. På inloggnings skärmen använder du det lokala administratörs kontot på datorn för att logga in.
4. För ange program typ väljer du **ASP.net-webbappar** som den typ av program som du vill Använd.

    ![Standard inläsning för app skapa behållare-verktyg.](./media/tutorial-containerize-apps-aks/tool-home.png)


### <a name="complete-tool-pre-requisites"></a>Fullständiga verktyg för krav
1. Godkänn **licens villkoren** och Läs informationen från tredje part.
6. I verktygs-webbappen > **konfigurerar du krav**, utför följande steg:
   - **Anslutning**: verktyget kontrollerar att Windows-datorn har Internet åtkomst. Om datorn använder en proxyserver:
     - Klicka på **Konfigurera proxy** för att ange proxyadress (i formatet IP-adress eller fullständigt domän namn) och lyssnings port.
     - Ange autentiseringsuppgifter om proxyn kräver autentisering.
     - Endast HTTP-proxy stöds.
     - Om du har lagt till proxyinformation eller inaktiverat proxyn och/eller autentiseringen, klickar du på **Spara** för att utlösa anslutnings kontrollen igen.
   - **Installera uppdateringar**: verktyget söker automatiskt efter senaste uppdateringar och installerar dem. Du kan också installera den senaste versionen av verktyget [manuellt.](https://go.microsoft.com/fwlink/?linkid=2134571)
   - **Installera Microsoft webb distributions verktyg**: verktyget kontrollerar att Microsoft Web Deploy-verktyget är installerat på Windows-datorn som kör verktyget Azure Migrate: app skapa behållare.
   - **Aktivera PowerShell-fjärrkommunikation**: verktyget meddelar dig att se till att PowerShell-fjärrkommunikation är aktiverat på de program servrar som kör ASP.NET-programmen som ska behållas.


## <a name="log-in-to-azure"></a>Logga in på Azure

Klicka på **Logga** in för att logga in på ditt Azure-konto.

1. Du behöver en enhets kod för att autentisera med Azure. När du klickar på Logga in öppnas en modal enhets kod.
2. Klicka på **Kopiera kod & inloggning** för att kopiera enhets koden och öppna en Azure-inloggnings tolk i en ny webbläsare-flik. Om den inte visas kontrollerar du att du har inaktiverat blockering av popup-fönster i webbläsaren.

    ![Modal visar enhets kod.](./media/tutorial-containerize-apps-aks/login-modal.png)

3. På fliken nytt klistrar du in enhets koden och slutför inloggningen med dina autentiseringsuppgifter för Azure-kontot. Du kan stänga fliken webbläsare när inloggningen är klar och återgå till app skapa behållare-verktygets webb gränssnitt.
4. Välj den **Azure-klient** som du vill använda.
5. Ange den **Azure-prenumeration** som du vill använda.

## <a name="discover-aspnet-applications"></a>Identifiera ASP.NET-program

Verktyget app skapa behållare Helper ansluter via fjärr anslutning till program servrarna med de angivna autentiseringsuppgifterna och försöker identifiera ASP.NET program som finns på program servrarna.

1. Ange **IP-adressen/FQDN och autentiseringsuppgifterna** för servern som kör ASP.net-programmet som ska användas för att fjärrans luta till servern för program identifiering.
    - De autentiseringsuppgifter som anges måste vara för en lokal administratör (Windows) på program servern.
    - För domän konton (användaren måste vara administratör på program servern) ska du använda prefixet username med domän namnet i formatet *<domän \ användar namn>*.
    - Du kan köra program identifieringen för upp till fem servrar i taget.

2. Klicka på **Verifiera** för att kontrol lera att program servern kan anslutas från datorn som kör verktyget och att autentiseringsuppgifterna är giltiga. När verifieringen är klar visar status kolumnen status som **mappad**.  

    ![Skärm bild för Server-IP och autentiseringsuppgifter.](./media/tutorial-containerize-apps-aks/discovery-credentials.png)

3. Klicka på **Fortsätt** för att starta program identifiering på valda program servrar.

4. När program identifieringen har slutförts kan du välja en lista över program som ska Använd.

    ![Skärm bild för identifierat ASP.NET-program.](./media/tutorial-containerize-apps-aks/discovered-app.png)


4. Använd kryss rutan för att välja de program som ska Använd.
5. **Ange behållar namn**: Ange ett namn för mål behållaren för varje valt program. Behållar namnet ska anges som <*Namn: tag*> där taggen används för behållar avbildningen. Du kan till exempel ange namnet på mål behållaren som *APPNAME: v1*.   

### <a name="parameterize-application-configurations"></a>Parameterisera program konfiguration
Genom att parameterstyrda konfigurationen blir den tillgänglig som en distributions tids parameter. På så sätt kan du konfigurera den här inställningen när du distribuerar programmet i stället för att få det hårdkodat till ett särskilt värde i behållar avbildningen. Det här alternativet är till exempel användbart för parametrar som databas anslutnings strängar.
1. Granska identifierade konfigurationer genom att klicka på **AppData** .
2. Markera kryss rutan för att Parameterisera identifierade programkonfigurationer.
3. Klicka på **Använd** när du har valt konfigurationerna till Parameterisera.

   ![Skärm bild för program konfiguration Parameterisering ASP.NET Application.](./media/tutorial-containerize-apps-aks/discovered-app-configs.png)

### <a name="externalize-file-system-dependencies"></a>Externalize fil system beroenden

 Du kan lägga till andra mappar som används i programmet. Ange om de ska vara en del av behållar avbildningen eller ska vara expanderade genom permanenta volymer på Azure-filresursen. Användning av beständiga volymer fungerar utmärkt för tillstånds känsliga program som lagrar tillstånd utanför behållaren eller har annat statiskt innehåll som lagras i fil systemet. [Läs mer](https://docs.microsoft.com/azure/aks/concepts-storage)

1. Klicka på **redigera** under app-mappar för att granska de identifierade programmapparna. De identifierade programmapparna har identifierats som obligatoriska artefakter som krävs av programmet och kommer att kopieras till behållar avbildningen.

2. Klicka på **Lägg till mappar** och ange de mappsökvägar som ska läggas till.
3. Om du vill lägga till flera mappar på samma volym anger du kommaavgränsade `,` värden ().
4. Välj **beständig volym** som lagrings alternativ om du vill att mapparna ska lagras utanför behållaren på en beständig volym.
5. Klicka på **Spara** när du har granskat programmapparna.
   ![Skärm bild för lagrings val av app-volymer.](./media/tutorial-containerize-apps-aks/discovered-app-volumes.png)

6. Fortsätt till bygg fasen behållar avbildningen genom att klicka på **Fortsätt** .

## <a name="build-container-image"></a>Skapa containeravbildning


1. **Välj Azure Container Registry**: Använd List rutan för att välja en [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) som ska användas för att skapa och lagra behållar avbildningarna för apparna. Du kan använda en befintlig Azure Container Registry eller välja att skapa en ny med hjälp av alternativet för att skapa ett nytt register.

    ![Skärm bild för val av ACR app.](./media/tutorial-containerize-apps-aks/build-aspnet-app.png)


2. **Granska Dockerfile**: Dockerfile som behövs för att skapa behållar avbildningarna för varje valt program genereras i början av build-steget. Klicka på **Granska** för att granska Dockerfile. Du kan också lägga till alla nödvändiga anpassningar i Dockerfile i gransknings steget och spara ändringarna innan du startar Bygg processen.

3. **Utlös versions process**: Välj de program som avbildningarna ska skapas för och klicka på **build**. Om du klickar på Skapa startas behållar avbildningen som skapas för varje program. Verktyget fortsätter att övervaka Bygg status kontinuerligt och gör att du fortsätter till nästa steg när du har slutfört skapandet.

4. **Spåra Bygg status**: du kan också övervaka förloppet för build-steget genom att klicka på länken för att **bygga** pågår i kolumnen Status. Det tar några minuter innan länken är aktiv när du har utlöst build-processen.  

5. När bygget är klart klickar du på **Fortsätt** för att ange distributions inställningar.

    ![Skärm bild för slut för ande av program behållare avbildning.](./media/tutorial-containerize-apps-aks/build-aspnet-app-completed.png)

## <a name="deploy-the-containerized-app-on-aks"></a>Distribuera appen med behållare på AKS

När behållar avbildningen har skapats är nästa steg att distribuera programmet som en behållare i [Azure Kubernetes service (AKS)](https://azure.microsoft.com/services/kubernetes-service/).

1. **Välj Azure Kubernetes service-kluster**: Ange det AKS-kluster som programmet ska distribueras till.

     - Det valda AKS-klustret måste ha en Windows Node-pool.
     - Klustret måste konfigureras för att tillåta hämtning av avbildningar från den Azure Container Registry som valdes för att lagra avbildningarna.
         - Kör följande kommando i Azure CLI för att koppla AKS-klustret till ACR.
           ``` Azure CLI
           az aks update -n <cluster-name> -g <cluster-resource-group> --attach-acr <acr-name>
           ```  
     - Om du inte har ett AKS-kluster eller om du vill skapa ett nytt AKS-kluster för att distribuera programmet till, kan du välja att skapa det från verktyget genom att klicka på **Skapa nytt AKS-kluster**.      
          - Det AKS-kluster som skapats med verktyget skapas med en Windows-Node-pool. Klustret konfigureras så att det kan hämta avbildningar från Azure Container Registry som skapades tidigare (om alternativet Skapa nytt register har valts).
     - Klicka på **Fortsätt** när du har valt AKS-klustret.

2. **Ange Azure-fil resurs**: om du har lagt till fler mappar och valt alternativet beständig volym anger du den Azure-filresurs som ska användas av Azure Migrate: app skapa behållare-verktyget under distributions processen. Verktyget skapar nya kataloger i den här Azure-filresursen för att kopiera över de programmappar som har kon figurer ATS för permanent volym lagring. När program distributionen är klar rensar verktyget Azure-filresursen genom att ta bort de kataloger som har skapats.

     - Om du inte har någon Azure-filresurs eller om du vill skapa en ny Azure-filresurs kan du välja att skapa på från verktyget genom att klicka på **Skapa nytt lagrings konto och fil resurs**.  

3. **Konfiguration av program distribution**: när du har slutfört stegen ovan måste du ange distributions konfigurationen för programmet. Klicka på **Konfigurera** för att anpassa distributionen av programmet. I konfigurations steget kan du ange följande anpassningar:
     - **Prefixlängd**: Ange en prefixlängd som ska användas i namnet för alla resurser som skapas för det containerbaserade programmet i AKS-klustret.
     - **SSL-certifikat**: om ditt program kräver en https-webbplats bindning, anger du den PFX-fil som innehåller det certifikat som ska användas för bindningen. PFX-filen bör inte vara lösenordsskyddad och den ursprungliga platsen får inte ha flera bindningar.
     - **Replik uppsättningar**: Ange antalet program instanser (poddar) som ska köras inuti behållarna.
     - **Belastnings Utjämnings typ**: Välj *extern* om det containerbaserade programmet ska kunna kommas åt från offentliga nätverk.
     - **Program konfiguration**: för alla programkonfigurationer som har parametrar, anger du de värden som ska användas för den aktuella distributionen.
     - **Lagring**: för alla programmappar som har kon figurer ATS för permanent volym lagring anger du om volymen ska delas mellan program instanser eller ska initieras separat med varje instans i behållaren. Som standard konfigureras alla programmappar på permanenta volymer som delade.  
     - Klicka på **Använd** för att spara distributions konfigurationen.
     - Klicka på **Fortsätt** för att distribuera programmet.

    ![Skärm bild för konfiguration av distributions program.](./media/tutorial-containerize-apps-aks/deploy-aspnet-app-config.png)

4. **Distribuera programmet**: när distributions konfigurationen för programmet har sparats genererar verktyget KUBERNETES Deployment yaml för programmet.
     - Klicka på **Redigera** för att granska och anpassa yaml för Kubernetes-distribution för programmen.
     - Välj det program som ska distribueras.
     - Klicka på **distribuera** för att starta distributioner för de valda programmen

         ![Skärm bild för konfiguration av app-distribution.](./media/tutorial-containerize-apps-aks/deploy-aspnet-app-deploy.png)

     - När programmet har distribuerats kan du klicka på kolumnen *distributions status* för att spåra de resurser som har distribuerats för programmet.

## <a name="download-generated-artifacts"></a>Hämta genererade artefakter

Alla artefakter som används för att bygga och distribuera programmet i AKS, inklusive Dockerfile-och Kubernetes YAML-Specification, lagras på den dator där verktyget körs. Artefakterna finns på *C:\ProgramData\Microsoft Azure Migrate app skapa behållare*.

En enda mapp skapas för varje program Server. Du kan visa och hämta alla mellanliggande artefakter som används i Skapa behållare-processen genom att gå till den här mappen. Mappen som motsvarar program servern kommer att rensas i början av varje körning av verktyget för en viss server.

## <a name="troubleshoot-issues"></a>Felsöka problem

Om du vill felsöka eventuella problem med verktyget kan du titta på loggfilerna på Windows-datorn som kör app skapa behållare-verktyget. Verktyg för loggfiler finns i mappen *C:\ProgramData\Microsoft Azure Migrate app Containerization\Logs* .

## <a name="next-steps"></a>Nästa steg

- Behållare för Java-Web Apps på Apache Tomcat (på Linux-servrar) och distribuera dem på Linux-behållare på AKS. [Läs mer](./tutorial-containerize-java-kubernetes.md)
