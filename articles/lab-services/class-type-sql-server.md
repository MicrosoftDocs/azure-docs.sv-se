---
title: Konfigurera ett labb för att hantera och utveckla med Azure SQL Database | Azure Lab Services
description: Lär dig hur du konfigurerar ett labb för att hantera och utveckla med Azure SQL Database.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: fb1b9e3458d08b8387c7f3978ff83c097fad2375
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644026"
---
# <a name="set-up-a-lab-to-manage-and-develop-with-sql-server"></a>Konfigurera ett labb för att hantera och utveckla med SQL Server

Den här artikeln beskriver hur du konfigurerar ett labb för en grundläggande SQL Server hanterings-och utvecklings klass i Azure Lab Services.  Databas koncept är en av de inledande kurserna som finns i de flesta av dator vetenskaps avdelningarna på hög skola. Structured Query Language (SQL) är en internationell standard.  SQL är standard språket för Relations databas hantering, inklusive tillägg, åtkomst och hantering av innehåll i en databas.  De är mest noterade för snabb bearbetning, beprövad tillförlitlighet, enkel och flexibel användning.

I den här artikeln visar vi hur du konfigurerar en mall för virtuella datorer i ett labb med [Visual Studio 2019](https://visualstudio.microsoft.com/vs/), [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)och [Azure Data Studio](https://github.com/microsoft/azuredatastudio).  I det här labbet ska vi använda en delad [SQL Server databas](../azure-sql/database/sql-database-paas-overview.md) för hela labbet. [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md) är ett PaaS-databasmotor (Platform as a Service) från Azure.

## <a name="lab-configuration"></a>Labb konfiguration

För att kunna konfigurera det här labbet behöver du ett Azure-prenumerations-och labb konto för att komma igång. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar. När du har skaffat en Azure-prenumeration kan du skapa ett nytt labb konto i Azure Lab Services. Mer information om hur du skapar ett nytt labb konto finns i [självstudier för att konfigurera ett labb konto](./tutorial-setup-lab-account.md). Du kan också använda ett befintligt labb konto.

### <a name="lab-account-settings"></a>Labb konto inställningar

Aktivera inställningarna som beskrivs i tabellen nedan för labb kontot. Mer information om hur du aktiverar Marketplace-avbildningar finns i [Ange Marketplace-avbildningar som är tillgängliga för labb skapare](specify-marketplace-images.md).

| Inställning för labb konto | Instruktioner |
| ------------------- | ------------ |
| Marketplace-avbildning | Aktivera "Visual Studio 2019 community (senaste versionen) på Windows 10 Enterprise N (x64)"-avbildningen för användning i ditt labb konto. |

### <a name="shared-resource-configuration"></a>Konfiguration av delad resurs

Om du vill använda en delad resurs i labb tjänster måste du först skapa det virtuella nätverket och resurserna.  Om du vill skapa det virtuella nätverket och ansluta det till labbet följer [du hur du skapar ett labb med en delad resurs i Azure Lab Services](how-to-create-a-lab-with-shared-resource.md).  Kom ihåg att alla resurser som är externa till labb tjänster debiteras separat och inte tas med i uppskattningar av labb kostnader.

>[!WARNING]
>Delade resurser för ett labb bör installeras innan labbet skapas.  Om VNet inte är peer-kopplat [till labb kontot](how-to-connect-peer-virtual-network.md) *innan* labbet skapas, kommer labb labbet inte att ha åtkomst till den delade resursen.

Nu när nätverks sidan av saker hanteras, kan du skapa en SQL Server databas.  Vi ska skapa en [enda databas](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal) eftersom det är det snabbaste distributions alternativet för Azure SQL Database.  För andra distributions alternativ skapar du en [elastisk pool](../azure-sql/database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal), en [hanterad instans](../azure-sql/managed-instance/instance-create-quickstart.md)eller en [virtuell SQL-dator](../azure-sql/virtual-machines/windows/sql-vm-create-portal-quickstart.md).

1. Välj **Skapa ny resurs** på Azure Portal-menyn.
2. Välj **SQL Database** och klicka på knappen **skapa** .
3. På fliken **grundläggande** i formuläret **skapa SQL-databas** väljer du resurs gruppen för databasen.  Vi kommer att använda *SQLDB-RG*.
4. För **databas namn** anger du *classlabdb*.
5. Under inställningen **Server** , klickar du på **Skapa ny** för att skapa en ny server för att lagra databasen.
6. På den **nya** utfällda servern anger du Server namnet.  Vi kommer att använda *classlabdbserver*.  Server namnet måste vara globalt unikt.
7. Ange *azureuser* som **inloggning för Server administratör**.
8. Ange ett minnes minnes minnes lösen ord.  Lösen ordet måste innehålla minst åtta tecken och innehålla specialtecken.
9. Välj region för **platsen**.  Ange om möjligt samma plats som labb kontot och peer-kopplat VNet för att minimera svars tiden.
10. Klicka på **OK** för att återgå till formuläret **skapa SQL Database** .
11. Klicka på **Konfigurera databas** länk under **beräknings-och lagrings** inställningen.
12. Ändra databas inställningarna efter behov för klassen.  Du kan välja mellan etablerade och Server lös alternativ.  I det här exemplet ska vi använda alternativet för autoskalad utan server med max virtuella kärnor på 4, min virtuella kärnor 1. Vi behåller inställningen AutoPause på minst 1 timme. Klicka på **Applicera**.
13. Klicka på **Nästa: knappen nätverk** .
14. På fliken nätverk väljer du privat slut punkt för **anslutnings metoden**.
15. Under avsnittet **privata slut punkter** klickar du på **Lägg till privat slut punkt**.
16. På sidan **skapa privat slut punkt** väljer du samma resurs grupp som ditt virtuella nätverk som peer-kontot för labbet.
17. För **plats** väljer du samma plats som det virtuella nätverket.
18. Som **namn** anger du *labsql-endpt*.
19. Lämna mål under resurs uppsättningen till SqlServer.
20. För **virtuellt nätverk** väljer du samma virtuella nätverk som peer-kontot i labb kontot.
21. För **undernät** väljer du det undernät som du vill att slut punkten finns i.  IP-adressen som tilldelats slut punkten kommer från intervallet som tilldelats till det under nätet.
22. Ange **integrera med privat DNS** till **Nej**. För enkelhetens skull använder vi Azures DNS över egna privata DNS-zoner eller våra egna DNS-servrar.
23. Klicka på **OK**.
24. Klicka på **Nästa: ytterligare inställningar**.
25. För inställningen **Använd befintlig data** väljer du **exempel**.  Data från AdventureWorksLT-databasen kommer att användas när databasen skapas.
26. Klicka på **Granska + skapa**.
27. Klicka på **Skapa**.

När SQL Database distributionen har slutförts kan vi skapa labbet och installera program varan på labb mal len dator.

### <a name="lab-settings"></a>Labb inställningar

Använd inställningarna i tabellen nedan när du konfigurerar ett klass rums labb. Mer information om hur du skapar ett klass rums labb finns i [Konfigurera en labb Guide för klass rummet](tutorial-setup-classroom-lab.md).

| Labb inställningar | Värde/anvisningar |
| ------------ | ------------------ |
| Storlek på virtuell dator | Medium. Den här storleken passar bäst för Relations databaser, minnes intern cachelagring och analys. |
| Avbildning av virtuell dator | Visual Studio 2019-community (senaste versionen) i Windows 10 Enterprise N (x64) |

Nu när vi har skapat vårt labb ska vi ändra mallen Machine med den program vara vi behöver.

## <a name="visual-studio"></a>Visual Studio

Bilden som du väljer ovan omfattar [Visual Studio 2019-community](https://visualstudio.microsoft.com/vs/community/).  Alla arbets belastningar och verktygs uppsättningar är redan installerade på avbildningen.  Använd Visual Studio-installationsprogrammet för att [Installera valfria verktyg](/visualstudio/install/modify-visual-studio?view=vs-2019&preserve-view=true) som du kanske vill använda.  [Logga in i Visual Studio](/visualstudio/ide/signing-in-to-visual-studio?view=vs-2019&preserve-view=true#how-to-sign-in-to-visual-studio) för att låsa upp community-utgåvan.

Visual Studio innehåller uppsättningen **data lagring och bearbetnings** verktyg, som innehåller SQL Server Data Tools (SSDT).  Mer information om SSDT-funktionerna finns i [SQL Server Data Tools översikt](/sql/ssdt/sql-server-data-tools).  Om du vill kontrol lera att anslutningen till den delade SQL Server för klassen kommer att lyckas, se [Anslut till en databas och bläddra bland befintliga objekt](/sql/ssdt/how-to-connect-to-a-database-and-browse-existing-objects). Om du uppmanas att lägga till mallens dator-IP i [listan över tillåtna datorer](../azure-sql/database/firewall-configure.md) som kan ansluta till din SQL Server-instans.

Visual Studio stöder flera arbets belastningar, inklusive **webb & moln-** och **skriv bords & mobila** arbets belastningar.  Båda dessa arbets belastningar stöder SQL Server som en data källa. Mer information om hur du använder ASP.NET Core för att SQL Server finns i avsnittet [bygga en ASP.net Core och SQL Database app i Azure App Service](../app-service/tutorial-dotnetcore-sqldb-app.md) själv studie kurs.  Använd [system. data. SqlClient](/dotnet/api/system.data.sqlclient) -biblioteket för att ansluta till en SQL Database från en [Xamarin](/xamarin) -app.

## <a name="install-azure-data-studio"></a>Installera Azure Data Studio

[Azure Data Studio](https://github.com/microsoft/azuredatastudio) är en plattforms oberoende Skriv bords miljö med flera databaser för data experter som använder familjen lokala och molnbaserade data plattformar på Windows, MacOS och Linux.

1. Hämta [installations programmet för Azure Data Studio *system* för Windows](https://go.microsoft.com/fwlink/?linkid=2127432). Om du vill hitta installations program för andra operativ system som stöds går du till sidan [Azure Data Studio](/sql/azure-data-studio/download) hämtning.
2. På sidan **licens avtal** väljer **du jag accepterar avtalet**. Klicka på **Nästa**.
3. På sidan **Välj målplats** klickar du på **Nästa**.
4. På sidan **Select Start Menu Folder** (Välj mapp för Start-menyn) klickar du på **Nästa**.
5. På sidan **Välj ytterligare aktiviteter** markerar du **skapa en Skriv bords ikon** om du vill ha en Skriv bords ikon.  Klicka på **Nästa**.
6. Klicka på **Nästa** på **redo att installera**.
7. Vänta tills installations programmet har körts.  Klicka på **Finish**.

Nu när vi har Azure Data Studio installerat är det dags att konfigurera anslutningen till Azure SQL Database.

1. Klicka på länken **ny anslutning** på **välkomst** sidan för Azure Data Studio.
2. I rutan **anslutnings information** fyller du i nödvändig information.
    - Ange **Server** till *classlabdbserver.Database.Windows.net*
    - Ange **användar** namnet till *azureuser*
    - Ange **lösen ord** för lösen ord som används för att skapa databasen.
    - Kontrol lera **kom ihåg lösen ordet**.
    - För **databas** väljer du *classlabdb*.
3. Klicka på **Anslut**.

## <a name="install-sql-server-management-studio"></a>Installera SQL Server Management Studio

[SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) är en integrerad miljö för att hantera alla SQL-infrastrukturer.  SSMS är ett verktyg som används av databas administratörer för att distribuera, övervaka och uppgradera data infrastruktur.

1. [Ladda ned SQL Server Management Studio](https://aka.ms/ssmsfullsetup). Starta installations programmet när du har laddat ned.
2. På **välkomst** sidan klickar du på **Installera**.
3. På sidan **installationen har slutförts** klickar du på **Stäng**.
4. Starta SQL Server-Management Studio.  
5. Klicka på **Stäng** på sidan **beroende konfigurations process** .

SSMS är inte installerat, du kan [ansluta och fråga en SQL Server](/sql/ssms/tutorials/connect-query-sql-server). När du ställer in anslutningen använder du följande värden:

- Server Typ: databas motor
- Server Namn: *classlabdbserver.Database.Windows.net*
- Autentisering: SQL Server autentisering
- Inloggning: *azureuser*
- Lösen ord: lösen ordet som användes för att skapa databasen.

## <a name="cost-estimate"></a>Kostnads uppskattning

Vi ska se en möjlig kostnads uppskattning för den här klassen. Beräkningen omfattar inte kostnaden för att köra SQL Server.  Se [SQL Database priser](https://azure.microsoft.com/pricing/details/sql-database) för aktuell information om databas priser.

Vi använder en klass av 25 studenter. Det finns 20 timmar med den schemalagda klass tiden. Dessutom får varje student en kvot på 10 timmar för läxor eller tilldelningar utanför schemalagda klass tider. Storleken på den virtuella datorn som vi valde var medel, vilket är 42 lab-enheter.

Här är ett exempel på en möjlig kostnads uppskattning för den här klassen:

25 studenter \* (20 schemalagda timmar \+ 10 kvot timmar) * 0,42 USD per timme = 315,00 USD

>[!IMPORTANT]
>Kostnads uppskattning är till exempel endast syfte. Aktuell information om priser finns i [Azure Lab Services priser](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Nästa steg

Nästa steg är vanliga för att ställa in alla labb.

- [Skapa, hantera och publicera en mall](how-to-create-manage-template.md)
- [Lägg till användare](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ange kvot](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ange ett schema](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-postregistrering länkar till studenter](how-to-configure-student-usage.md#send-invitations-to-users)