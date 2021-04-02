---
title: 'Azure AD Connect: Uppgradera från DirSync | Microsoft Docs'
description: Lär dig hur du uppgraderar från DirSync till Azure AD Connect. I den här artikeln lär du dig hur du uppgraderar från DirSync till Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: baf52da7-76a8-44c9-8e72-33245790001c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8aa45294de4ef644c20ef66b7163706dca9759d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996533"
---
# <a name="azure-ad-connect-upgrade-from-dirsync"></a>Azure AD Connect: Uppgradera från DirSync
Azure AD Connect är efterföljaren till DirSync. I det här avsnittet lär du dig hur du kan uppgradera från DirSync på olika sätt. Stegen fungerar inte om du ska uppgradera från en annan version av Azure AD Connect eller från Azure AD Sync.

Innan du börjar installera Azure AD Connect [laddar du ned Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) och kontrollerar att du uppfyller kraven i [Azure AD Connect: Maskinvara och krav](how-to-connect-install-prerequisites.md). Du bör särskilt läsa om följande, eftersom dessa områden skiljer sig från DirSync:

* Den version av .NET och PowerShell som krävs. Nyare versioner krävs på servern än vad DirSync krävde.
* Proxyserverkonfigurationen. Om du använder en proxyserver för att nå Internet måste den här inställningen konfigureras innan du uppgraderar. DirSync använde alltid proxyservern som konfigurerats för användaren som installerar den, men Azure AD Connect använder datorinställningar i stället.
* URL:erna måste vara öppna i proxyservern. För grundläggande scenarier, som också stöds av DirSync, är kraven identiska. Om du vill använda de nya funktionerna som ingår i Azure AD Connect måste några nya URL:er vara öppna.

> [!NOTE]
> När du har aktiverat den nya Azure AD Connect servern för att starta synkroniseringen av ändringar till Azure AD, kan du inte återställa till att använda DirSync eller Azure AD Sync. Nedgradering från Azure AD Connect till äldre klienter, inklusive DirSync och Azure AD Sync, stöds inte och kan leda till problem som data förlust i Azure AD.

Om du inte uppgraderar från DirSync läser du relaterad dokumentation för andra scenarier.

## <a name="upgrade-from-dirsync"></a>Uppgradera från DirSync
Beroende på din aktuella DirSync-distribution finns det olika alternativ för uppgraderingen. Om den förväntade tiden för uppgraderingen är mindre än tre timmar rekommenderar vi att du gör en uppgradering på plats. Om den förväntade tiden för uppgraderingen är mer än tre timmar rekommenderar vi att du gör en parallell distribution på en annan server. Uppskattningsvis tar uppgraderingen mer än tre timmar om du har mer än 50 000 objekt.

| Scenario |
| --- |
| [Uppgradering på plats](#in-place-upgrade) |
| [Parallell distribution](#parallel-deployment) |

> [!NOTE]
> När du planerar att uppgradera från DirSync till Azure AD Connect ska du inte avinstallera DirSync själv före uppgraderingen. Azure AD Connect läser och migrerar konfigurationen från DirSync och avinstallerar efter att ha inspekterat servern.

**Uppgradering på plats**  
Den förväntade tiden för att slutföra uppgraderingen visas i guiden. Den här uppskattningen baseras på antagandet att det tar tre timmar att slutföra en uppgradering för en databas med 50 000 objekt (användare, kontakter och grupper). Azure AD Connect rekommenderar en uppgradering på plats om antalet objekt i databasen är färre än 50 000. Om du väljer att fortsätta tillämpas dina aktuella inställningar automatiskt under uppgraderingen och servern återupptar automatiskt den aktiva synkroniseringen.

Om du vill utföra en konfigurationsmigrering och en parallell distribution kan du åsidosätta rekommendationen om en uppgradering på plats. Du kan till exempel passa på att uppdatera maskinvaran och operativsystemet. Mer information finns i avsnittet om [parallell distribution](#parallel-deployment).

**Parallell distribution**  
En parallell distribution rekommenderas om du har fler än 50 000 objekt. Med den här distributionen undviker du risken för att användarna upplever fördröjningar i processerna. Azure AD Connect-installationen försöker beräkna avbrottstiden för uppgraderingen, men om du har uppgraderat DirSync tidigare är din egen erfarenheter antagligen den bästa riktlinjen.

### <a name="supported-dirsync-configurations-to-be-upgraded"></a>DirSync-konfigurationer som stöds som ska uppgraderas
Följande konfigurationsändringar stöds med uppgraderat DirSync:

* Domän- och organisationsenhetsfiltrering
* Alternativt ID (UPN)
* Lösenordssynkronisering och Exchange-hybridinställningar
* Din skog/domän och dina Azure AD-inställningar
* Filtrering baserat på användarattribut

Följande ändring kan inte uppgraderas. Uppgraderingen blockeras om du har den här konfigurationen:

* DirSync-ändringar som inte stöds, t.ex. borttagna attribut och användningen av ett anpassat tilläggs-DLL

![Uppgraderingen har blockerats](./media/how-to-dirsync-upgrade-get-started/analysisblocked.png)

I dessa fall rekommenderar vi att du installerar en ny Azure AD Connect-server i [mellanlagringsläge](how-to-connect-sync-staging-server.md) och verifierar den gamla DirSync-konfigurationen och den nya Azure AD Connect-konfigurationen. Tillämpa eventuella ändringar med en anpassad konfiguration. Mer information finns i [Anpassad Azure AD Connect Sync-konfiguration](how-to-connect-sync-whatis.md).

Lösenorden som används av DirSync för tjänstkontona kan inte hämtas och migreras inte. Dessa lösenord återställs under uppgraderingen.

### <a name="high-level-steps-for-upgrading-from-dirsync-to-azure-ad-connect"></a>Allmänna steg för att uppgradera från DirSync till Azure AD Connect
1. Välkommen till Azure AD Connect
2. Analys av den aktuella DirSync-konfigurationen
3. Samla in lösenordet för globala Azure AD-administratörer
4. Samla in autentiseringsuppgifterna för ett konto för företagsadministratörer (används endast under installationen av Azure AD Connect)
5. Installation av Azure AD Connect
   * Avinstallera DirSync (eller inaktivera det tillfälligt)
   * Installera Azure AD Connect
   * Starta en synkronisering om det behövs

Ytterligare steg krävs om:

* Du har en fullständig SQL-server – lokal eller fjärransluten
* Du har mer än 50 000 objekt som ska synkroniseras

## <a name="in-place-upgrade"></a>Uppgradering på plats
1. Starta Azure AD Connect-installationsprogrammet (MSI).
2. Läs och godkänn licensvillkoren och sekretesspolicyn.  
   ![Välkommen till Azure AD](./media/how-to-dirsync-upgrade-get-started/Welcome.png)
3. Klicka på Nästa när du vill börja analysera din befintliga DirSync-installation.  
   ![Analysera den befintliga Directory Sync-installationen](./media/how-to-dirsync-upgrade-get-started/Analyze.png)
4. När analysen är klar visas rekommendationer om hur du bör fortsätta.  
   * Om du använder SQL Server Express och har färre än 50 000 objekt visas följande skärm:  
     ![Analysen är klar och du kan uppgradera från DirSync](./media/how-to-dirsync-upgrade-get-started/AnalysisReady.png)
   * Om du använder en fullständig SQL Server för DirSync kan du få se den här sidan i stället:  
     ![skärm bild som visar den befintliga SQL Database-servern som används.](./media/how-to-dirsync-upgrade-get-started/AnalysisReadyFullSQL.png)  
     Informationen om den befintliga SQL Server-databasservern som används av DirSync visas. Gör relevanta justeringar om det behövs. Klicka på **Nästa** när du vill fortsätta med installationen.
   * Om du har fler än 50 000 objekt kan se du få se den här skärmen i stället:  
     ![Skärm bild som visar skärmen som visas när du har fler än 50 000 objekt att uppgradera.](./media/how-to-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)  
     Om du vill fortsätta med en uppgradering på plats klickar du på kryssrutan bredvid det här meddelandet: **Fortsätt att uppgradera DirSync på den här datorn.**
     Om du vill utföra en [parallell distribution](#parallel-deployment) i stället exporterar du DirSync-konfigurationsinställningarna och flyttar konfigurationen till den nya servern.
5. Ange lösenordet för det konto som du för närvarande använder för att ansluta till Azure AD. Det här måste vara det konto som för närvarande används av DirSync.  
   ![Ange dina autentiseringsuppgifter för Azure AD](./media/how-to-dirsync-upgrade-get-started/ConnectToAzureAD.png)  
   Om du får ett fel och har problem med anslutningen läser du [Felsöka anslutningsproblem](tshoot-connect-connectivity.md).
6. Ange ett företagsadministratörskonto för Active Directory.  
   ![Ange dina ADDS-autentiseringsuppgifter](./media/how-to-dirsync-upgrade-get-started/ConnectToADDS.png)
7. Nu är det dags att konfigurera inställningarna. När du klickar på **Uppgradera** avinstalleras DirSync och Azure AD Connect konfigureras och börjar synkronisera.  
   ![Klart att konfigurera](./media/how-to-dirsync-upgrade-get-started/ReadyToConfigure.png)
8. När installationen är klar loggar du ut och loggar sedan in igen i Windows innan du använder Synchronization Service Manager eller Synchronization Rule Editor eller innan du försöker göra andra konfigurationsändringar.

## <a name="parallel-deployment"></a>Parallell distribution
### <a name="export-the-dirsync-configuration"></a>Exportera DirSync-konfigurationen
**Parallell distribution med mer än 50 000 objekt**

Om du har fler än 50 000 objekt rekommenderar Azure AD Connect-installationen en parallell distribution.

En skärm liknande följande visas:  
![Analysen är klar](./media/how-to-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

Om du vill fortsätta med den parallella distributionen måste du utföra följande steg:

* Klicka på knappen **Exportera inställningar**. När du installerar Azure AD Connect på en separat server migreras dessa inställningar från din aktuella DirSync till den nya Azure AD Connect-installationen.

När inställningarna har exporterats kan du avsluta Azure AD Connect-guiden på DirSync-servern. Fortsätt med nästa steg för att installera Azure AD Connect på en separat server

**Parallell distribution med mindre än 50 000 objekt**

Om du har färre än 50 000 objekt, men ändå vill utföra en parallell distribution, gör du följande:

1. Kör Azure AD Connect-installationsprogrammet (MSI).
2. När du ser skärmen **Välkommen till Azure AD Connect** avslutar du installationsguiden genom att klicka på ”X” i det övre högra hörnet i fönstret.
3. Öppna en kommandotolk.
4. Kör följande kommando från installationsplatsen för Azure AD Connect (som standard C:\Program Files\Microsoft Azure Active Directory Connect): `AzureADConnect.exe /ForceExport`.
5. Klicka på knappen **Exportera inställningar**. När du installerar Azure AD Connect på en separat server migreras dessa inställningar från din aktuella DirSync till den nya Azure AD Connect-installationen.

![Skärm bild som visar alternativet Exportera inställningar för att migrera dina inställningar till den nya Azure AD Connect-installationen.](./media/how-to-dirsync-upgrade-get-started/forceexport.png)

När inställningarna har exporterats kan du avsluta Azure AD Connect-guiden på DirSync-servern. Fortsätt med nästa steg om du vill installera Azure AD Connect på en separat server.

### <a name="install-azure-ad-connect-on-separate-server"></a>Installera Azure AD Connect på en separat server
När du installerar Azure AD Connect på en ny server antas det att du vill utföra en ren installation av Azure AD Connect. Eftersom du vill använda DirSync-konfigurationen måste du utföra några extra steg:

1. Kör Azure AD Connect-installationsprogrammet (MSI).
2. När du ser skärmen **Välkommen till Azure AD Connect** avslutar du installationsguiden genom att klicka på ”X” i det övre högra hörnet i fönstret.
3. Öppna en kommandotolk.
4. Kör följande kommando från installationsplatsen för Azure AD Connect (som standard C:\Program Files\Microsoft Azure Active Directory Connect): `AzureADConnect.exe /migrate`.
   Installationsguiden för Azure AD Connect startar och följande skärm visas:  
   ![Skärm bild som visar var du kan importera inställnings filen när du uppgraderar.](./media/how-to-dirsync-upgrade-get-started/ImportSettings.png)
5. Välj inställningsfilen som exporterades från DirSync-installationen.
6. Konfigurera eventuella avancerade alternativ, inklusive:
   * En anpassad installationsplats för Azure AD Connect.
   * En befintlig instans av SQL Server (SQL Server 2012 Express installeras som standard av Azure AD Connect). Använd inte samma databasinstans som DirSync-servern.
   * Ett tjänstkonto som används för att ansluta till SQL Server (om SQL Server-databasen är en fjärrplats måste det här kontot vara ett domäntjänstkonto).
     Följande alternativ visas på skärmen:  
     ![Skärm bild som visar avancerade konfigurations alternativ för att uppgradera från DirSync.](./media/how-to-dirsync-upgrade-get-started/advancedsettings.png)
7. Klicka på **Nästa**.
8. På sidan **Klart att konfigurera** lämnar du kryssrutan **Starta synkroniseringsprocessen så snart som konfigurationen är klar** markerad. Nu är servern i [mellanlagringsläge](how-to-connect-sync-staging-server.md) och ändringarna exporteras inte till Azure AD.
9. Klicka på **Installera**.
10. När installationen är klar loggar du ut och loggar sedan in igen i Windows innan du använder Synchronization Service Manager eller Synchronization Rule Editor eller innan du försöker göra andra konfigurationsändringar.

> [!NOTE]
> Synkroniseringen mellan Windows Server Active Directory och Azure Active Directory börjar, men inga ändringar exporteras till Azure AD. Endast ett synkroniseringsverktyg i taget kan aktivt exportera ändringar. Det här tillståndet kallas för [mellanlagringsläge](how-to-connect-sync-staging-server.md).

### <a name="verify-that-azure-ad-connect-is-ready-to-begin-synchronization"></a>Kontrollera att Azure AD Connect är redo att påbörja synkroniseringen
För att bekräfta att Azure AD Connect är redo att ta över från DirSync måste du öppna **Synchronization Service Manager** i gruppen **Azure AD Connect** från startmenyn.

I programmet går du till fliken **åtgärder** . På den här fliken bekräftar du att följande åtgärder har slutförts:

* Import i AD-anslutningen
* Import i Azure AD Connector
* Fullständig synkronisering i AD-anslutningen
* Fullständig synkronisering i Azure AD Connector

![Importen och synkroniseringen har slutförts](./media/how-to-dirsync-upgrade-get-started/importsynccompleted.png)

Granska resultatet från dessa åtgärder och kontrollera att det inte finns några fel.

Om du vill visa och inspektera de ändringar som är på väg att exporteras till Azure AD läser du hur du verifierar konfigurationen under [mellanlagringsläge](how-to-connect-sync-staging-server.md). Gör nödvändiga konfigurationsändringar tills du inte ser något oväntat.

Du är redo att byta från DirSync till Azure AD när du har utfört dessa åtgärder och är nöjd med resultatet.

### <a name="uninstall-dirsync-old-server"></a>Avinstallera DirSync (den gamla servern)
* Leta upp **Synkroniseringsverktyget för Microsoft Azure Active Directory** i **Program och funktioner**
* Avinstallera **Synkroniseringsverktyget för Microsoft Azure Active Directory**
* Avinstallationen kan ta upp till 15 minuter.

Om du vill avinstallera DirSync senare kan du också tillfälligt stänga av servern eller inaktivera tjänsten. Om något går fel kan du aktivera den igen med hjälp av den här metoden. Dock är det inte troligt att nästa steg misslyckas så detta bör inte vara nödvändigt.

När DirSync har avinstallerats eller inaktiverats finns det ingen aktiv server som exporterar till Azure AD. Nästa steg, då du aktiverar Azure AD Connect, måste slutföras innan ändringar i din lokala Active Directory fortsätter att synkroniseras till Azure AD.

### <a name="enable-azure-ad-connect-new-server"></a>Aktivera Azure AD Connect (den nya servern)
Efter installationen kan du öppna Azure AD Connect igen om du vill göra ytterligare konfigurationsändringar. Starta **Azure AD Connect** från Start-menyn eller genvägen på skrivbordet. Var försiktig så att du inte råkar köra MSI-installationsfilen igen.

Du bör se följande:  
![Ytterligare uppgifter](./media/how-to-dirsync-upgrade-get-started/AdditionalTasks.png)

* Välj **Konfigurera mellanlagringsläge**.
* Inaktivera mellanlagring genom att avmarkera kryssrutan **Aktivera mellanlagringsläge**.

![Skärm bild som visar alternativet för att aktivera mellanlagrings läge.](./media/how-to-dirsync-upgrade-get-started/configurestaging.png)

* Klicka på knappen **Nästa**
* Klicka på knappen **Installera** på bekräftelsesidan.

Nu är Azure AD Connect din aktiva server och du kan inte gå tillbaka till att använda din befintliga DirSync-server.

## <a name="next-steps"></a>Nästa steg
Nu när Azure AD Connect är installerat kan du [verifiera installationen och tilldela licenser](how-to-connect-post-installation.md).

Lär dig mer om dessa nya funktioner, som aktiverades med installationen: [Automatisk uppgradering](how-to-connect-install-automatic-upgrade.md), [Förhindra oavsiktliga borttagningar](how-to-connect-sync-feature-prevent-accidental-deletes.md) och [Azure AD Connect Health](how-to-connect-health-sync.md).

Läs mer om [schemaläggaren och hur du utlöser synkronisering](how-to-connect-sync-feature-scheduler.md).

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
