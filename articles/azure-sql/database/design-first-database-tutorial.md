---
title: 'Självstudie: utforma din första Relations databas med SSMS'
description: Lär dig hur du utformar din första Relations databas i Azure SQL Database att använda SQL Server Management Studio.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 07/29/2019
ms.custom: sqldbrb=1
ms.openlocfilehash: ae7baeac6cee2a692928642e3e38ce0adad17d1c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92674885"
---
# <a name="tutorial-design-a-relational-database-in-azure-sql-database-using-ssms"></a>Självstudie: utforma en Relations databas i Azure SQL Database med SSMS
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]


Azure SQL Database är en relationsdatabas-som-tjänst (DBaaS) som bygger på Microsoft Cloud (Azure). I de här självstudierna får du lära dig att använda Azure-portalen och [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS) för att:

> [!div class="checklist"]
>
> - Skapa en databas med hjälp av Azure Portal *
> - Konfigurera en IP-brandväggsregel på servernivå med hjälp av Azure-portalen
> - Ansluta till databasen med SSMS
> - Skapa tabeller med SSMS
> - Massinläsa data med BCP
> - Fråga efter data med SSMS

* Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.

> [!TIP]
> Följande Microsoft Learn-modul hjälper dig att lära dig kostnads fritt hur du [utvecklar och konfigurerar ett ASP.NET-program som skickar frågor till en Azure SQL Database](/learn/modules/develop-app-that-queries-azure-sql/), inklusive skapandet av en enkel databas.
> [!NOTE]
> I den här självstudien använder vi Azure SQL Database. Du kan också använda en pool databas i en elastisk pool eller en SQL-hanterad instans. För anslutning till en SQL-hanterad instans, se följande snabb starter för SQL-hanterad instans: [snabb start: Konfigurera den virtuella Azure-datorn för att ansluta till en Azure SQL-hanterad instans](../managed-instance/connect-vm-instance-configure.md) och [snabb start: Konfigurera en punkt-till-plats-anslutning till en Azure SQL-hanterad instans](../managed-instance/point-to-site-p2s-configure.md)

## <a name="prerequisites"></a>Förutsättningar

För att kunna följa de här självstudierna ska du kontrollera att du har installerat:

- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (senaste versionen)
- [BCP och SQLCMD](https://www.microsoft.com/download/details.aspx?id=36433) (senaste versionen)

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-blank-database-in-azure-sql-database"></a>Skapa en tom databas i Azure SQL Database

En databas i Azure SQL Database skapas med en definierad uppsättning beräknings-och lagrings resurser. Databasen skapas i en Azure- [resurs grupp](../../active-directory-b2c/overview.md) och hanteras med hjälp av en [logisk SQL-Server](logical-servers.md).

Följ de här stegen för att skapa en tom databas.

1. Välj **Skapa en resurs** på menyn i Microsoft Azure-portalen eller från **startsidan**.
2. Välj **Databaser** i avsnittet Azure Marketplace på sidan **Nytt** och klicka sedan på **SQL Database** i avsnittet **Aktuellt**.

   ![skapa tom databas](./media/design-first-database-tutorial/create-empty-database.png)

3. Fyll i **SQL Database** formuläret med följande information, som du ser på föregående bild:

    | Inställning       | Föreslaget värde | Beskrivning |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Databasnamn** | *yourDatabase* | För giltiga databasnamn, se [databasidentifierare](/sql/relational-databases/databases/database-identifiers). |
    | **Prenumeration** | *yourSubscription*  | Mer information om dina prenumerationer finns i [Prenumerationer](https://account.windowsazure.com/Subscriptions). |
    | **Resursgrupp** | *yourResourceGroup* | Giltiga resursgruppnamn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming). |
    | **Välj källa** | Tom databas | Anger att en tom databas ska skapas. |

4. Klicka på **Server** för att använda en befintlig server eller skapa och konfigurera en ny server. Välj antingen en befintlig server eller klicka på **Skapa en ny server** och fyll i följande information i formuläret **Ny server**:

    | Inställning       | Föreslaget värde | Beskrivning |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Servernamn** | Valfritt globalt unikt namn | Giltiga servernamn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming). |
    | **Inloggning för serveradministratör** | Valfritt giltigt namn | För giltiga inloggningsnamn, se [Databasidentifierare](/sql/relational-databases/databases/database-identifiers). |
    | **Lösenord** | Valfritt giltigt lösenord | Lösenordet måste innehålla minst åtta tecken och måste innehålla tecken från tre av följande kategorier: versaler, gemener, siffror och icke-alfanumeriska tecken. |
    | **Plats** | Valfri giltig plats | För information om regioner, se [Azure-regioner](https://azure.microsoft.com/regions/). |

    ![skapa databas-server](./media/design-first-database-tutorial/create-database-server.png)

5. Klicka på **Välj**.
6. Klicka på **Prisnivå** för att ange tjänstnivå, antalet DTU:er eller V-kärnor och mängden lagring. Du kan undersöka alternativen för antalet DTU:er/V-kärnor och lagringsutrymme som du har tillgång till på varje tjänstnivå.

    När du har valt tjänstnivå, antalet DTU:er eller virtuella kärnor samt mängden lagring klickar du på **Använd**.

7. Ange en **sortering** för den tomma databasen (använd standardvärdet för de här självstudierna). Mer information om sorteringar finns i [Sorteringar](/sql/t-sql/statements/collations).

8. Nu när du har fyllt i **SQL Database**-formuläret klickar du på **Skapa** så att databasen etableras. Det här steget kan ta några minuter.

9. Klicka på **Aviseringar** i verktygsfältet för att övervaka distributionsprocessen.

   ![Skärm bild som visar menyn meddelanden med pågående distribution.](./media/design-first-database-tutorial/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Skapa en IP-brandväggsregel på servernivå

Azure SQL Database skapar en IP-brandvägg på server nivå. Den här brandväggen förhindrar att externa program och verktyg ansluter till servern eller databaser på servern såvida inte en brandväggsregel tillåter deras IP-adresser genom brandväggen. Om du vill aktivera extern anslutning till databasen måste du först lägga till en IP-brandväggsregel för din IP-adress (eller IP-adressintervall). Följ de här stegen för att skapa en [regel för IP-brandvägg på server nivå](firewall-configure.md).

> [!IMPORTANT]
> Azure SQL Database kommunicerar via port 1433. Om du försöker ansluta till den här tjänsten från ett företagsnätverk kan utgående trafik via port 1433 bli nekad av nätverkets brandvägg. I så fall kan du inte ansluta till databasen om administratören öppnar port 1433.

1. När distributionen är klar väljer du **SQL-databaser** på Azure Portal-menyn eller söker efter och väljer *SQL-databaser* från vilken sida som helst.  

1. Välj *yourDatabase* på sidan **SQL-databaser** . Översikts sidan för databasen öppnas och visar det fullständigt kvalificerade **Server namnet** (till exempel `contosodatabaseserver01.database.windows.net` ) och innehåller alternativ för ytterligare konfiguration.

   ![servernamn](./media/design-first-database-tutorial/server-name.png)

1. Kopiera det här fullständigt kvalificerade servernamnet. Du behöver det när du ansluter till servern och dess databaser från SQL Server Management Studio.

1. Klicka på **Konfigurera serverns brandvägg** i verktygsfältet. Sidan **brand Väggs inställningar** för servern öppnas.

   ![IP-brandväggsregel på servernivå](./media/design-first-database-tutorial/server-firewall-rule.png)

1. Klicka på **Lägg till klient-IP** i verktygsfältet och lägg till din aktuella IP-adress i en ny IP-brandväggsregel. Med en IP-brandväggsregel kan du öppna port 1433 för en enskild IP-adress eller för IP-adressintervall.

1. Klicka på **Spara**. En regel för IP-brandvägg på server nivå skapas för den aktuella IP-adressen som öppnar port 1433 på servern.

1. Klicka på **OK** och stäng sedan sidan **Brandväggsinställningar**.

Din IP-adress kan nu passera genom IP-brandväggen. Nu kan du ansluta till din databas med SQL Server Management Studio eller något annat verktyg som du själv väljer. Se till att använda serveradmin-kontot som du skapade tidigare.

> [!IMPORTANT]
> Som standard är åtkomst via IP-brandväggen för SQL Database aktiverad för alla Azure-tjänster. Klicka på **AV** på den här sidan om du vill inaktivera åtkomsten för alla Azure-tjänster.

## <a name="connect-to-the-database"></a>Ansluta till databasen

Använd [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) för att upprätta en anslutning till databasen.

1. Öppna SQL Server Management Studio.
2. I dialogrutan **Anslut till server** anger du följande information:

   | Inställning       | Föreslaget värde | Beskrivning |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Servertyp** | Databasmotor | Det här värdet är obligatoriskt. |
   | **Servernamn** | Fullständigt kvalificerat servernamn | Till exempel *yourserver.database.windows.net*. |
   | **Autentisering** | SQL Server-autentisering | SQL-autentisering är den enda autentiseringstypen som vi har konfigurerat i den här självstudien. |
   | **Inloggning** | Serveradministratörskontot | Kontot som du angav när du skapade servern. |
   | **Lösenord** | Lösenordet för serveradministratörskontot | Detta är det lösenord som du angav när du skapade servern. |

   ![Anslut till server](./media/design-first-database-tutorial/connect.png)

3. Klicka på **Alternativ** i dialogrutan **Anslut till server**. I avsnittet **Anslut till databas** anger du *yourDatabase* så att du ansluter till den här databasen.

    ![ansluta till databas på server](./media/design-first-database-tutorial/options-connect-to-db.png)  

4. Klicka på **Anslut**. Fönstret **Object Explorer** öppnas i SSMS.

5. I **Object Explorer** expanderar du **Databaser** och sedan *yourDatabase* för att visa objekten i exempeldatabasen.

   ![databasobjekt](./media/design-first-database-tutorial/connected.png)  

## <a name="create-tables-in-your-database"></a>Skapa tabeller i databasen

Skapa ett databasschema med fyra tabeller som visar ett studenthanteringssystem för universitet med [Transact-SQL](/sql/t-sql/language-reference):

- Person
- Kurs
- Student
- Kredit

Följande diagram visar hur tabellerna är relaterade till varandra. Vissa av tabellerna hänvisar till kolumner i andra tabeller. Till exempel hänvisar *studenttabellen* till kolumnen *PersonId* i tabellen *Person*. Studera diagrammet för att förstå hur tabellerna i de här självstudierna är relaterade till varandra. Mer information om hur du skapar effektiva databastabeller finns i [Skapa effektiva databastabeller](/previous-versions/tn-archive/cc505842(v=technet.10)). Information om hur du väljer datatyper finns i [Datatyper](/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> Du kan också använda [tabelldesignern i SQL Server Management Studio](/sql/ssms/visual-db-tools/design-database-diagrams-visual-database-tools) för att skapa och utforma tabeller.

![Relationer mellan tabellerna](./media/design-first-database-tutorial/tutorial-database-tables.png)

1. I **Object Explorer** högerklickar du på *yourDatabase* och väljer sedan **Ny fråga**. Ett tomt frågefönster öppnas som är anslutet till databasen.

2. I frågefönstret skriver du följande fråga om du vill skapa fyra tabeller i databasen:

   ```sql
   -- Create Person table
   CREATE TABLE Person
   (
       PersonId INT IDENTITY PRIMARY KEY,
       FirstName NVARCHAR(128) NOT NULL,
       MiddelInitial NVARCHAR(10),
       LastName NVARCHAR(128) NOT NULL,
       DateOfBirth DATE NOT NULL
   )

   -- Create Student table
   CREATE TABLE Student
   (
       StudentId INT IDENTITY PRIMARY KEY,
       PersonId INT REFERENCES Person (PersonId),
       Email NVARCHAR(256)
   )

   -- Create Course table
   CREATE TABLE Course
   (
       CourseId INT IDENTITY PRIMARY KEY,
       Name NVARCHAR(50) NOT NULL,
       Teacher NVARCHAR(256) NOT NULL
   )

   -- Create Credit table
   CREATE TABLE Credit
   (
       StudentId INT REFERENCES Student (StudentId),
       CourseId INT REFERENCES Course (CourseId),
       Grade DECIMAL(5,2) CHECK (Grade <= 100.00),
       Attempt TINYINT,
       CONSTRAINT [UQ_studentgrades] UNIQUE CLUSTERED
       (
           StudentId, CourseId, Grade, Attempt
       )
   )
   ```

   ![Skapa tabeller](./media/design-first-database-tutorial/create-tables.png)

3. Expandera noden **Tabeller** under *yourDatabase* i **Object Explorer** för att se de tabeller som du skapade.

   ![skapade ssms-tabeller](./media/design-first-database-tutorial/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Läs in data i tabellerna

1. Skapa en mapp med namnet *sampleData* i din mapp för nedladdningar för att lagra exempeldata till din databas.

2. Högerklicka på följande länkar och spara dem i mappen *sampleData*.

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. Öppna en kommandotolk och navigera till mappen *sampleData*.

4. Kör följande kommandon för att infoga exempeldata i tabellerna där du ersätter värdena för *server*, *databas*, *användare* och *lösenord* med värdena för din miljö.

   ```cmd
   bcp Course in SampleCourseData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   ```

Du har nu läst in exempeldata i de tabeller som du skapade tidigare.

## <a name="query-data"></a>Söka i data

Kör följande frågor för att hämta information från databastabellerna. I [Skriva SQL-frågor](/previous-versions/sql/sql-server-2005/express-administrator/bb264565(v=sql.90)) kan du läsa mer om hur du skriver SQL-frågor. Den första frågan kopplar ihop alla fyra tabellerna och söker efter alla elever som undervisats av ”Dominick Pope” som har ett betyg som är högre än 75 %. Den andra frågan kopplar ihop alla fyra tabeller och söker efter kurserna som ”Noe Coleman” någonsin har registrerat sig på.

1. Kör följande fråga i frågefönstret i SQL Server Management Studio:

   ```sql
   -- Find the students taught by Dominick Pope who have a grade higher than 75%
   SELECT  person.FirstName, person.LastName, course.Name, credit.Grade
   FROM  Person AS person
       INNER JOIN Student AS student ON person.PersonId = student.PersonId
       INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
       INNER JOIN Course AS course ON credit.CourseId = course.courseId
   WHERE course.Teacher = 'Dominick Pope'
       AND Grade > 75
   ```

2. I ett frågefönster kör du följande fråga:

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled
   SELECT  course.Name, course.Teacher, credit.Grade
   FROM  Course AS course
       INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
       INNER JOIN Student AS student ON student.StudentId = credit.StudentId
       INNER JOIN Person AS person ON person.PersonId = student.PersonId
   WHERE person.FirstName = 'Noe'
       AND person.LastName = 'Coleman'
   ```

## <a name="next-steps"></a>Nästa steg

I den här självstudien beskrivs många grundläggande databasuppgifter. Du har lärt dig att:

> [!div class="checklist"]
>
> - Skapa en databas med hjälp av Azure Portal *
> - Konfigurera en IP-brandväggsregel på servernivå med hjälp av Azure-portalen
> - Ansluta till databasen med SSMS
> - Skapa tabeller med SSMS
> - Massinläsa data med BCP
> - Fråga efter data med SSMS

Gå vidare till fler självstudier för att lära dig att utforma en databas med Visual Studio och C#.

> [!div class="nextstepaction"]
> [Utforma en Relations databas inom Azure SQL Database C# och ADO.NET](design-first-database-csharp-tutorial.md)