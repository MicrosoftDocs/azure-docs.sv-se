---
title: Konfigurera Always Encrypted med hjälp av Windows certifikat Arkiv
description: Den här artikeln visar hur du skyddar känsliga data i Azure SQL Database med databas kryptering med hjälp av guiden Always Encrypted i SQL Server Management Studio (SSMS). Det visar också hur du lagrar dina krypterings nycklar i Windows certifikat arkiv.
keywords: kryptera data, SQL-kryptering, databas kryptering, känsliga data Always Encrypted
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: VanMSFT
ms.author: vanto
ms.reviwer: ''
ms.date: 04/23/2020
ms.openlocfilehash: 60dea826a12ea475806adb6db88faa88e26463a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92674830"
---
# <a name="configure-always-encrypted-by-using-the-windows-certificate-store"></a>Konfigurera Always Encrypted med hjälp av Windows certifikat Arkiv

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Den här artikeln visar hur du skyddar känsliga data i Azure SQL Database eller Azure SQL-hanterad instans med databas kryptering med hjälp av [Always Encrypted-guiden](/sql/relational-databases/security/encryption/always-encrypted-wizard) i [SQL Server Management Studio (SSMS)](/sql/ssms/sql-server-management-studio-ssms). Det visar också hur du lagrar dina krypterings nycklar i Windows certifikat arkiv.

Always Encrypted är en data krypterings teknik som skyddar känsliga data i vila på servern under förflyttning mellan klient och Server, medan data används, vilket säkerställer att känsliga data aldrig visas som klartext i databas systemet. När du har krypterat data kan endast klient program eller App-servrar som har åtkomst till nycklarna komma åt oformaterade data. Detaljerad information finns i [Always Encrypted (databas motor)](/sql/relational-databases/security/encryption/always-encrypted-database-engine).

När du har konfigurerat databasen att använda Always Encrypted skapar du ett klient program i C# med Visual Studio för att arbeta med krypterade data.

Följ stegen i den här artikeln för att lära dig hur du konfigurerar Always Encrypted för SQL Database eller SQL-hanterad instans. I den här artikeln får du lära dig hur du utför följande uppgifter:

* Skapa [Always Encrypted-nycklar](/sql/relational-databases/security/encryption/always-encrypted-database-engine#Anchor_3)med hjälp av guiden Always Encrypted i SSMS.
  * Skapa en [kolumn huvud nyckel (CMK)](/sql/t-sql/statements/create-column-master-key-transact-sql).
  * Skapa en [kolumn krypterings nyckel (CEK)](/sql/t-sql/statements/create-column-encryption-key-transact-sql).
* Skapa en databas tabell och kryptera kolumner.
* Skapa ett program som infogar, väljer och visar data från de krypterade kolumnerna.

## <a name="prerequisites"></a>Förutsättningar

I den här självstudien behöver du:

* Ett Azure-konto och prenumeration. Om du inte har någon kan du registrera dig för en [kostnads fri utvärderings version](https://azure.microsoft.com/pricing/free-trial/).
- En databas i [Azure SQL Database](single-database-create-quickstart.md) eller [Azure SQL-hanterad instans](../managed-instance/instance-create-quickstart.md).
* [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) version 13.0.700.242 eller senare.
* [.NET Framework 4,6](/dotnet/framework/) eller senare (på klient datorn).
* [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).

## <a name="enable-client-application-access"></a>Aktivera åtkomst till klient program

Du måste aktivera klient programmet för att få åtkomst till SQL Database eller SQL-hanterad instans genom att konfigurera ett Azure Active Directory (AAD)-program och kopiera *program-ID: t* och *nyckeln* som du behöver för att autentisera ditt program.

Hämta *program-ID* och *nyckel* genom att följa stegen i [skapa ett Azure Active Directory program och tjänstens huvud namn som kan komma åt resurser](../../active-directory/develop/howto-create-service-principal-portal.md).



## <a name="connect-with-ssms"></a>Anslut med SSMS

Öppna SQL Server Management Studio (SSMS) och Anslut till servern eller hanterad med databasen.

1. Öppna SSMS. (Klicka på **Anslut**  >  **Database Engine** för att öppna fönstret **Anslut till Server** om det inte är öppet).
2. Ange server namn och autentiseringsuppgifter.

    ![Kopiera anslutningssträngen](./media/always-encrypted-certificate-store-configure/ssms-connect.png)

Om fönstret **ny brand Väggs regel** öppnas loggar du in på Azure och låter SSMS skapa en ny brand Väggs regel åt dig.

## <a name="create-a-table"></a>Skapa en tabell

I det här avsnittet ska du skapa en tabell för att lagra patient data. Detta är en normal tabell – du kommer att konfigurera kryptering i nästa avsnitt.

1. Expandera **databaser**.
2. Högerklicka på **klinik** -databasen och klicka på **ny fråga**.
3. Klistra in följande Transact-SQL (T-SQL) i fönstret ny fråga och **Kör** det.
    
    ```tsql
    CREATE TABLE [dbo].[Patients](
    [PatientId] [int] IDENTITY(1,1),
    [SSN] [char](11) NOT NULL,
    [FirstName] [nvarchar](50) NULL,
    [LastName] [nvarchar](50) NULL,
    [MiddleName] [nvarchar](50) NULL,
    [StreetAddress] [nvarchar](50) NULL,
    [City] [nvarchar](50) NULL,
    [ZipCode] [char](5) NULL,
    [State] [char](2) NULL,
    [BirthDate] [date] NOT NULL
    PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
    GO
    ```

## <a name="encrypt-columns-configure-always-encrypted"></a>Kryptera kolumner (Konfigurera Always Encrypted)

SSMS innehåller en guide för att enkelt konfigurera Always Encrypted genom att konfigurera CMK, CEK och krypterade kolumner åt dig.

1. Expandera   >  **klinik**-  >  **tabeller** för databaser.
2. Högerklicka på tabellen **patienter** och välj **kryptera kolumner** för att öppna guiden Always Encrypted:

    ![Skärm bild som visar Colunns för kryptering... meny alternativ i tabellen patienter.](./media/always-encrypted-certificate-store-configure/encrypt-columns.png)

Guiden Always Encrypted innehåller följande avsnitt: **kolumn val**, **huvud nyckel konfiguration** (CMK), **verifiering** och **Sammanfattning**.

### <a name="column-selection"></a>Kolumn val

Klicka på **Nästa** på sidan **Introduktion** för att öppna sidan **kolumn val** . På den här sidan kan du välja vilka kolumner du vill kryptera, [vilken typ av kryptering och vilken kolumn krypterings nyckel (CEK)](/sql/relational-databases/security/encryption/always-encrypted-wizard#Anchor_2) som ska användas.

Kryptera uppgifter om **SSN** och **födelse datum** för varje patient. Kolumnen **SSN** använder deterministisk kryptering, som stöder likhets uppslag, kopplingar och gruppering av. Kolumnen **födelse datum** kommer att använda slumpmässig kryptering, som inte stöder åtgärder.

Ange **krypterings typ** för kolumnen **SSN** till **deterministisk** och kolumnen **födelse datum** till **slumpmässig**. Klicka på **Nästa**.

![Kryptera kolumner](./media/always-encrypted-certificate-store-configure/column-selection.png)

### <a name="master-key-configuration"></a>Huvud nyckel konfiguration

På sidan **konfiguration av huvud nyckel** kan du konfigurera din CMK och välja den nyckel lagrings leverantör där CMK ska lagras. För närvarande kan du lagra en CMK i certifikat arkivet i Windows, Azure Key Vault eller en HSM (Hardware Security Module). I den här självstudien visas hur du lagrar dina nycklar i Windows certifikat arkiv.

Kontrol lera att **Windows certifikat Arkiv** är markerat och klicka på **Nästa**.

![Huvud nyckel konfiguration](./media/always-encrypted-certificate-store-configure/master-key-configuration.png)

### <a name="validation"></a>Validering

Du kan kryptera kolumnerna nu eller spara ett PowerShell-skript för att köra senare. I den här självstudien väljer **du Fortsätt för att slutföra nu** och klickar på **Nästa**.

### <a name="summary"></a>Sammanfattning

Kontrol lera att inställningarna är korrekta och klicka på **Slutför** för att slutföra installationen av Always Encrypted.

![Skärm bild som visar sidan resultat med aktiviteter som har marker ATS som slutförda.](./media/always-encrypted-certificate-store-configure/summary.png)

### <a name="verify-the-wizards-actions"></a>Verifiera guidens åtgärder

När guiden har slutförts konfigureras databasen för Always Encrypted. Följande åtgärder utfördes av guiden:

* Skapade en CMK.
* Skapade en CEK.
* Konfigurerade de markerade kolumnerna för kryptering. Din **patienter** -tabell har för närvarande inga data, men alla befintliga data i de markerade kolumnerna är nu krypterade.

Du kan kontrol lera att nycklarna skapas i SSMS genom att gå till   >    >  **Always Encrypted nycklar** för klinik-säkerhet. Nu kan du se de nya nycklar som skapas av guiden.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Skapa ett klient program som fungerar med krypterade data

Nu när Always Encrypted har kon figurer ATS kan du skapa ett program som utför *infogningar* och *väljer* i de krypterade kolumnerna. För att kunna köra exempel programmet måste du köra det på samma dator där du körde guiden Always Encrypted. Om du vill köra programmet på en annan dator måste du distribuera dina Always Encrypted certifikat till den dator som kör-klient programmet.  

> [!IMPORTANT]
> Ditt program måste använda [SqlParameter](/dotnet/api/system.data.sqlclient.sqlparameter) -objekt när du skickar oformaterade data till servern med Always Encrypted kolumner. Att skicka litterala värden utan att använda SqlParameter-objekt resulterar i ett undantag.

1. Öppna Visual Studio och skapa ett nytt C#-konsol program. Kontrol lera att ditt projekt är inställt på **.NET Framework 4,6** eller senare.
2. Ge projektet namnet **AlwaysEncryptedConsoleApp** och klicka på **OK**.

![Skärm bild som visar det nyligen namngivna AlwaysEncryptedConsoleApp-projektet.](./media/always-encrypted-certificate-store-configure/console-app.png)

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Ändra anslutnings strängen för att aktivera Always Encrypted

I det här avsnittet beskrivs hur du aktiverar Always Encrypted i databas anslutnings strängen. Du ändrar den-konsol app som du nyss skapade i nästa avsnitt, "Always Encrypted exempel konsol program".

Om du vill aktivera Always Encrypted måste du lägga till nyckelordet för **kolumn krypterings inställningen** i anslutnings strängen och ange det som **aktive rad**.

Du kan ställa in detta direkt i anslutnings strängen, eller så kan du ange den med hjälp av en [SqlConnectionStringBuilder](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder). Exempel programmet i nästa avsnitt visar hur du använder **SqlConnectionStringBuilder**.

> [!NOTE]
> Detta är den enda ändringen som krävs i ett klient program som är specifika för Always Encrypted. Om du har ett befintligt program som lagrar anslutnings strängen externt (det vill säga i en konfigurations fil) kanske du kan aktivera Always Encrypted utan att ändra någon kod.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Aktivera Always Encrypted i anslutnings strängen

Lägg till följande nyckelord i anslutnings strängen:

`Column Encryption Setting=Enabled`

### <a name="enable-always-encrypted-with-a-sqlconnectionstringbuilder"></a>Aktivera Always Encrypted med en SqlConnectionStringBuilder

Följande kod visar hur du aktiverar Always Encrypted genom att ställa in funktionen [SqlConnectionStringBuilder. ColumnEncryptionSetting](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting) på [aktive rad](/dotnet/api/system.data.sqlclient.sqlconnectioncolumnencryptionsetting).

```csharp
// Instantiate a SqlConnectionStringBuilder.
SqlConnectionStringBuilder connStringBuilder =
    new SqlConnectionStringBuilder("replace with your connection string");

// Enable Always Encrypted.
connStringBuilder.ColumnEncryptionSetting =
    SqlConnectionColumnEncryptionSetting.Enabled;
```

## <a name="always-encrypted-sample-console-application"></a>Always Encrypted exempel konsol program

Det här exemplet visar hur du:

* Ändra anslutnings strängen för att aktivera Always Encrypted.
* Infoga data i de krypterade kolumnerna.
* Välj en post genom att filtrera efter ett särskilt värde i en krypterad kolumn.

Ersätt innehållet i **program. cs** med följande kod. Ersätt anslutnings strängen för den globala connectionString-variabeln på raden direkt ovanför huvud metoden med din giltiga anslutnings sträng från Azure Portal. Detta är den enda ändringen du behöver göra i den här koden.

Kör appen för att se Always Encrypted i praktiken.

```cs
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.SqlClient;
using System.Globalization;

namespace AlwaysEncryptedConsoleApp
{
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"Data Source = SPE-T640-01.sys-sqlsvr.local; Initial Catalog = Clinic; Integrated Security = true";

        static void Main(string[] args)
        {
            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();

            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;


            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            CultureInfo culture = CultureInfo.CreateSpecificCulture("en-US");
            InsertPatient(new Patient()
            {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993", culture)
            });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now let's locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 123-45-6789):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null)
            {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else
            {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }


        static int InsertPatient(Patient newPatient)
        {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
     VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);


            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex)
                {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients()
        {
            List<Patient> patients = new List<Patient>();


            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));


            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patients.Add(new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }

            return patients;
        }


        static Patient SelectPatientBySSN(string ssn)
        {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);


            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patient = new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else
                    {
                        patient = null;
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }
            return patient;
        }


        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable()
        {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex)
                {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient
    {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
}
```

## <a name="verify-that-the-data-is-encrypted"></a>Verifiera att data är krypterade

Du kan snabbt kontrol lera att faktiska data på servern är krypterade genom att fråga **patienternas** data med SSMS. (Använd din aktuella anslutning där kolumn krypterings inställningen inte har Aktiver ATS än.)

Kör följande fråga i klinik-databasen.

```tsql
SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
```

Du kan se att de krypterade kolumnerna inte innehåller några oformaterade data.

   ![Skärm bild som visar krypterade data i de krypterade kolumnerna.](./media/always-encrypted-certificate-store-configure/ssms-encrypted.png)

Om du vill använda SSMS för att komma åt data i klartext kan du lägga till **kolumn krypterings inställningen = aktive rad** parameter till anslutningen.

1. I SSMS högerklickar du på servern i **Object Explorer** och klickar sedan på **Koppla från**.
2. Klicka på **Anslut**  >  **databas motor** för att öppna fönstret **Anslut till Server** och klicka sedan på **alternativ**.
3. Klicka på **ytterligare anslutnings parametrar** och ange **kolumn krypterings inställning = aktive rad**.

    ![Skärm bild som visar fliken Ytterligare anslutnings parametrar med inställningen för kolumn kryptering som har Aktiver ATS i rutan.](./media/always-encrypted-certificate-store-configure/ssms-connection-parameter.png)
4. Kör följande fråga i **klinik** -databasen.

    ```tsql
    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
    ```

     Nu kan du se data i klartext i de krypterade kolumnerna.

    ![Nytt konsol program](./media/always-encrypted-certificate-store-configure/ssms-plaintext.png)

> [!NOTE]
> Om du ansluter till SSMS (eller någon klient) från en annan dator, kommer den inte att ha åtkomst till krypterings nycklarna och kommer inte att kunna dekryptera data.

## <a name="next-steps"></a>Nästa steg

När du har skapat en databas som använder Always Encrypted kanske du vill göra följande:

* Kör det här exemplet från en annan dator. Den har inte åtkomst till krypterings nycklarna, så den kommer inte att ha åtkomst till data i klartext och kommer inte att kunna köras.
* [Rotera och rensa dina nycklar](/sql/relational-databases/security/encryption/configure-always-encrypted-using-sql-server-management-studio).
* [Migrera data som redan är krypterade med Always Encrypted](/sql/relational-databases/security/encryption/migrate-sensitive-data-protected-by-always-encrypted).
* [Distribuera Always Encrypted certifikat till andra klient datorer](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted#Anchor_1) (se avsnittet "göra certifikat tillgängliga för program och användare").

## <a name="related-information"></a>Relaterad information

* [Always Encrypted (klient utveckling)](/sql/relational-databases/security/encryption/always-encrypted-client-development)
* [Transparent datakryptering](/sql/relational-databases/security/encryption/transparent-data-encryption)
* [SQL Server kryptering](/sql/relational-databases/security/encryption/sql-server-encryption)
* [Always Encrypted guiden](/sql/relational-databases/security/encryption/always-encrypted-wizard)
* [Always Encrypted blogg](/archive/blogs/sqlsecurity/always-encrypted-key-metadata)