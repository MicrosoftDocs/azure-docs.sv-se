---
title: Konfigurera Micro Focus CICS BankDemo för Micro Focus Enterprise Developer 4,0 på Azure Virtual Machines
description: Kör Micro Focus BankDemo-programmet på Azure Virtual Machines (VM) för att lära dig använda Micro Focus Enterprise Server och Enterprise Developer.
author: sread
ms.author: sread
ms.date: 03/30/2020
ms.topic: article
ms.service: multiple
ms.openlocfilehash: b2cac7a8310482305467a1cb3ed3127eb18ae416
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97915784"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>Konfigurera Micro Focus CICS BankDemo för Micro Focus Enterprise Developer 4,0 på Azure

När du ställer in Micro Focus Enterprise Server 4,0 och Enterprise Developer 4,0 på Azure, kan du testa distributioner av IBM z/OS-arbetsbelastningar. Den här artikeln visar hur du konfigurerar CICS BankDemo, ett exempel program som medföljer företags utvecklare.

CICs står för system för kontroll av kund information, den transaktions plattform som används av många av online-stordator programmen. BankDemo-programmet är perfekt för att lära dig hur Enterprise Server och Enterprise Developer fungerar och hur du hanterar och distribuerar ett faktiskt program som kompletterar med gröna skärms terminaler.

> [!NOTE]
> Kommer snart: anvisningar för att konfigurera [Micro Focus Enterprise Server 5,0](https://techcommunity.microsoft.com/t5/azurecat/micro-focus-enterprise-server-5-0-quick-start-template-on-azure/ba-p/1160110) på virtuella Azure-datorer.

## <a name="prerequisites"></a>Krav

- En virtuell dator med [företags utvecklare](set-up-micro-focus-azure.md). Tänk på att företags utvecklaren har en fullständig instans av Enterprise Server i syfte att utveckla och testa. Den här instansen är den instans av företags servern som används för demonstrationen.

- [SQL Server 2017 Express Edition](https://www.microsoft.com/sql-server/sql-server-editions-express). Ladda ned och installera det på den virtuella datorn för Enterprise Developer. Enterprise Server kräver en databas för hantering av CICS-regioner och BankDemo-programmet använder också en SQL Server databas som kallas BANKDEMO. Den här demon förutsätter att du använder SQL Server Express för båda databaserna. När du installerar väljer du den grundläggande installationen.

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017&preserve-view=true) (SSMS). SSMS används för att hantera databaser och köra ett T-SQL-skript. Ladda ned och installera det på den virtuella datorn för Enterprise Developer.

- [Visual studio 2019](https://azure.microsoft.com/downloads/) med den senaste Service Pack-eller [Visual Studio-communityn](https://visualstudio.microsoft.com/vs/community/), som du kan ladda ned kostnads fritt.

- Rumba Desktop eller en annan 3270-emulator.

## <a name="configure-the-windows-environment"></a>Konfigurera Windows-miljön

När du har installerat Enterprise Developer 4,0 på den virtuella datorn måste du konfigurera den instans av företags servern som medföljer den. Om du vill göra det måste du installera några ytterligare Windows-funktioner på följande sätt.

1. Använd RDP för att logga in på den virtuella datorn för Enterprise Server 4,0 som du skapade.

2. Klicka på **Sök** ikonen bredvid **Start** -knappen och skriv **Windows-funktioner**. Guiden Serverhanteraren Lägg till roller och funktioner öppnas.

3. Välj **webb Server rollen (IIS)** och kontrol lera sedan följande alternativ:

    - Webb hanterings verktyg
    - IIS 6-hanterings kompatibilitet (Välj alla tillgängliga funktioner)
    - IIS-hanteringskonsol
    - Skript och verktyg för IIS-hantering
    - Hanterings tjänst för IIS

4. Välj **World Wide Web tjänster** och kontrol lera följande alternativ:

     Program utvecklings funktioner:
    - .NET-utökningsbarhet
    - ASP.NET
    - Vanliga HTTP-funktioner: Lägg till alla tillgängliga funktioner
    - Hälso tillstånd och diagnostik: Lägg till alla tillgängliga funktioner
    - Bullet
        - Grundläggande autentisering
        - Windows-autentisering

5. Välj **Windows Process Activation Service** och alla dess underordnade.

6. För **funktioner** kontrollerar du **Microsoft .NET Framework 3.5.1** och kontrollerar följande alternativ:

    - Windows Communication Foundation HTTP-aktivering
    - Windows Communication Foundation icke-HTTP-aktivering

7. För **funktioner** kontrollerar du **Microsoft .NET Framework 4,6** och kontrollerar följande alternativ:

   - Namngiven pipe-aktivering
   - TCP-aktivering
   - Delning av TCP-port

     ![Guiden Lägg till roller och funktioner: roll tjänster](media/01-demo-roles.png)

8. När du har valt alla alternativ klickar du på **Nästa** för att installera.

9. Efter Windows-funktionerna går du till **kontroll panelen \> system och \> administrations verktyg för säkerhet** och väljer **tjänster**. Rulla nedåt och kontrol lera att följande tjänster körs och är inställda på **Automatisk**:

    - **NetTcpPortSharing**
    - **Net. pipe Listener adapter**
    - **Net. TCP-Lyssnaradapter**

10. Om du vill konfigurera IIS och har stöd, går du till menyn hitta **Micro Focus Enterprise Developer kommando tolk (64-bitars)** och kör som **administratör**.

11. Skriv **wassetup – i** och tryck på **RETUR**.

12. När skriptet har körts kan du stänga fönstret.

## <a name="configure-the-local-system-account-for-sql-server"></a>Konfigurera det lokala system kontot för SQL Server

Vissa företags server processer måste kunna logga in SQL Server och skapa databaser och andra objekt. Dessa processer använder det lokala system kontot, så du måste ge sysadmin-behörighet till det kontot.

1. Starta **SSMS** och klicka på **Anslut** för att ansluta till den lokala SQLExpress-servern med Windows-autentisering. Den bör vara tillgänglig i listan **Server namn** .

2. Expandera mappen **säkerhet** till vänster och välj **inloggningar**.

3. Välj **NT auktoritets \\ system** och välj **Egenskaper**.

4. Välj **Server roller** och kontrol lera **sysadmin**.

     ![SSMS Object Explorer fönster: inloggnings egenskaper](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>Skapa BankDemo-databasen och alla dess objekt

1. Öppna **Utforskaren** och gå till **C: \\ användare \\ Public \\ Documents \\ Micro Focus \\ Enterprise Developer \\ samples \\ stordator \\ CICS \\ dotNet \\ BankDemo \\ SQL**.

2. Kopiera innehållet i **BankDemoCreateAll. SQL** -filen till Urklipp.

3. Öppna **SSMS**. Till höger klickar du på **Server** och väljer **ny fråga**.

4. Klistra in innehållet i Urklipp i rutan **ny fråga** .

5. Kör SQL genom att klicka på **Kör** från **kommando** -fliken ovanför frågan.

Frågan ska köras utan fel. När den är klar har du exempel databasen för BankDemo-programmet.

![SQLQuery1. SQL-utdata](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>Kontrol lera att databas tabellerna och-objekten har skapats

1. Högerklicka på **BANKDEMO** -databasen och välj **Uppdatera**.

2. Expandera **databasen** och välj **tabeller**. Du bör se något som liknar följande.

     ![BANKDEMO-tabellen har utökats i Object Explorer](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>Bygg programmet i företags utvecklare

1. Öppna Visual Studio och logga in.

2. Under meny alternativet **Arkiv** väljer du **öppna projekt/lösning**, navigera till **C: \\ användare \\ publika \\ dokument \\ Micro fokusering \\ Enterprise Developer- \\ exempel \\ stordator \\ CICS \\ dotNet \\ BankDemo** och väljer **SLN** -filen.

3. Ta lite tid för att undersöka objekten. COBOL-program visas i Solution Explorer med CBL-tillägget tillsammans med CopyBooks (CPY) och JCL.

4. Högerklicka på projektet **BankDemo2** och välj **Set as Startup Project (ange som start projekt**).

    > [!NOTE]
    > BankDemo-projektet använder HCOSS (värdens kompatibilitetsalternativ för SQL Server), som inte används för den här demon.

5. I **Solution Explorer** högerklickar du på projektet **BankDemo2** och väljer **build**.

    > [!NOTE]
    > När du skapar en lösnings nivå resulterar det i fel eftersom HCOSS inte har kon figurer ATS.

6. När projektet har skapats granskar du fönstret **utdata** . Det bör se ut som på bilden nedan.

     ![Fönstret utdata visar lyckad version](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>Distribuera BankDemo-programmet till region databasen

1. Öppna en Enterprise Developer-kommandotolk (64-bitars) som administratör.

2. Gå till mappen **% Public% \\ Documents \\ Micro Focus \\ Enterprise Developer \\ samples \\ stordatorer \\ CICS \\ dotNet \\ BankDemo**.

3. I kommando tolken kör du **bankdemodbdeploy** och inkluderar parametern för databasen att distribuera till, till exempel:

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> Se till att använda ett snedstreck (/) som inte är ett omvänt snedstreck ( \\ ). Det här skriptet körs ett tag.

![Administration: kommando tolks fönstret för Enterprise-utvecklare](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>Skapa regionen BankDemo i Enterprise Administrator för .NET

1. Öppna **administrations gränssnittet för Enterprise Server för .net** .

2. Starta MMC-snapin-modulen från **Start** -menyn i Windows och välj **Micro Focus Enterprise-utvecklare \> konfiguration \> Enterprise Server för .net-administratör**. (För Windows Server väljer du **Micro Focus Enterprise-utvecklare \> Enterprise Server för .net-administratör**).

3. Expandera behållaren **regioner** i den vänstra rutan och högerklicka sedan på **CICS**.

4. Välj **definiera region** för att skapa en ny CICS-region som heter **BANKDEMO**, som finns i den lokala databasen (lokal).

5. Ange databas Server instansen, klicka på **Nästa** och ange sedan region namnet **BANKDEMO**.

     ![Dialog rutan definiera region](media/07-demo-cics.png)

6. Om du vill välja region definitions filen för databasen över flera regioner hittar du **regionen \_ bankdemo \_db.config** i **C: \\ användare \\ offentliga \\ dokument \\ Micro Focus \\ Enterprise Developer- \\ exempel \\ stordator \\ CICS \\ dotNet \\ bankdemo**.

     ![Definiera namn på region region: BANKDEMO](media/08-demo-cics.png)

7. Klicka på **Finish**.

## <a name="create-xa-resource-definitions"></a>Skapa resurs definitioner för XA

1. I den vänstra rutan i **Enterprise Server för .net administrations** gränssnitt expanderar du **system** och sedan **XA-resursposter**. Den här inställningen definierar hur regionen samverkar med företags servern och program databaserna.

2. Högerklicka på **resurs definitioner för XA** och välj **Lägg till Server instans**.

3. I list rutan väljer du **databas tjänst instans**. Det är den lokala datorn SQLEXPRESS.

4. Välj instansen under behållaren **resurs definitioner för XA (MachineName \\ )** och klicka på **Lägg till**.

5. Välj **Database XA resurs definition** och skriv sedan **BANKDEMO** som **namn** och **region**.

     ![Skärm bild definition för den nya databasens XA-resurs](media/09-demo-xa.png)

6. Klicka på ellipserna (**...**) för att öppna guiden anslutnings sträng. För **Server namn** skriver du **(lokal) \\ SQLExpress**. För **inloggning** väljer du **Windows-autentisering**. För databas namn skriver du **BANKDEMO**

     ![Skärmen Redigera anslutnings sträng](media/10-demo-string.png)

7. Testa anslutningen.

## <a name="start-the-bankdemo-region"></a>Starta regionen BANKDEMO

> [!NOTE]
> Det första steget är viktigt: du måste ange region för att använda den resurs definition för XA som du nyss skapade.

1. Navigera till **BANDEMO CICS-regionen** under **behållaren regioner** och välj sedan **Redigera region start fil** från fönstret **åtgärder** . Rulla ned till SQL-egenskaperna och ange **bankdemo** som **resurs namn för XA**, eller Använd ellipsen för att välja den.

2. Spara ändringarna genom att klicka på ikonen **Spara** .

3. Högerklicka på **BANKDEMO CICS region** i **konsol** fönstret och välj **Start-/stopp region**.

4. Längst ned i rutan **Starta/stoppa region** som visas i mitten av fönstret väljer du **Start**. Efter några sekunder börjar regionen.

     ![Rutan SQL-start/stopp](media/11-demo-sql.png)

     ![CICS region BANKDEMO – startad skärm](media/12-demo-cics.png)

## <a name="create-a-listener"></a>Skapa en lyssnare

Skapa en lyssnare för de TN3270-sessioner som har åtkomst till BankDemo-programmet.

1. I den vänstra rutan expanderar du **konfigurations redigerare** och väljer **lyssnare**.

2. Klicka på ikonen **Öppna fil** och välj filen **seelistener.exe.config** . Den här filen kommer att redige ras och läses in varje gång Enterprise Server startas.

3. Lägg märke till de två regioner som definierats tidigare (ESDEMO och JCLDEMO).

4. Om du vill skapa en ny region för BANKDEMO högerklickar du på **regioner** och väljer **Lägg till region**.

5. Välj **BANKDEMO region**.

6. Lägg till en TN3270-kanal genom att högerklicka på **BANKDEMO region** och välja **Lägg till kanal**.

7. Som **namn** anger du **TN3270**. För **port** anger du **9024**. ESDEMO-programmet använder port 9230 så du måste använda en annan port.

8. Spara filen genom att klicka på ikonen **Spara** eller välja **Arkiv** \> **Spara**.

9. Starta lyssnaren genom att klicka på ikonen **Starta lyssnare** eller välja **alternativ** \> **Starta lyssnare**.

     ![Fönster för lyssnare i konfigurations redigeraren](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>Konfigurera rumba för att få åtkomst till BankDemo-programmet

Det sista du behöver göra är att konfigurera en 3270-session med Rumba, en 3270-emulator. Det här steget ger dig åtkomst till BankDemo-programmet via den lyssnare som du har skapat.

1. Från **Start** -menyn i Windows startar du rumba Desktop.

2. Under meny alternativet **anslutningar** väljer du **TN3270**.

3. Klicka på **Infoga** och skriv **127.0.0.1** för IP-adressen och **9024** för den användardefinierade porten.

4. Klicka på **Anslut** längst ned i dialog rutan. En svart CICS-skärm visas.

5. Skriv **Bank** för att visa första 3270-skärmen för BankDemo-programmet.

6. För användar-ID skriver du **B0001** och lösen ordet genom att skriva vad som helst. Den första skärmens BANK20 öppnas.

![Skärm bilds visning av stordator skärm ](media/14-demo.png)
 ![ – rumba – under system demonstration](media/15-demo.png)

Grattis! Nu kör du ett CICS-program i Azure med Micro Focus Enterprise Server.

## <a name="next-steps"></a>Nästa steg

- [Kör Enterprise Server i Docker-behållare på Azure](run-enterprise-server-container.md)
- [Stordator-migrering – Portal](/archive/blogs/azurecat/mainframe-migration-to-azure-portal)
- [Virtual Machines](../../../linux/overview.md)
- [Felsökning](../../../troubleshooting/index.yml)
- [Avmystifiera-stordator till Azure-migrering](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
