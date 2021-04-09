---
title: Exempel på flera klient organisationer – Wingtip SaaS
description: Innehåller steg och rikt linjer för att installera och köra ett exempel på ett program för flera innehavare som använder Azure SQL Database, Wingtip Ticket SaaS-exempel.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: aa9215fa001fb117000eb6a68867ddd46fac9b92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92780333"
---
# <a name="general-guidance-for-working-with-wingtip-tickets-sample-saas-apps"></a>Allmänna rikt linjer för att arbeta med Wingtip ticks-exempel SaaS-appar
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Den här artikeln innehåller allmän vägledning för att köra Wingtip ticks-SaaS program som använder Azure SQL Database.

## <a name="download-and-unblock-the-wingtip-tickets-saas-scripts"></a>Hämta och avblockera Wingtip Ticket SaaS-skript

Körbart innehåll (skript, DLL-filer) kan blockeras av Windows när zip-filer laddas ned från en extern källa och extraheras. När du extraherar skripten från en zip-fil **följer du stegen nedan för att avblockera zip-filen innan du extraherar**. Detta säkerställer att skripten kan köras.

1. Bläddra till Wingtip Ticket SaaS GitHub-lagrings platsen för databasens hyres mönster som du vill utforska:
    - [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
    - [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)
    - [WingtipTicketsSaaS-MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)
2. Klicka på **klona eller hämta**.
3. Klicka på **Hämta zip** och spara filen.
4. Högerklicka på zip-filen och välj **Egenskaper**. Zip-filnamn motsvarar namnet på lagrings platsen. priset. _WingtipTicketsSaaS-DbPerTenant-master.zip_)
5. På fliken **Allmänt** väljer du **avblockera**.
6. Klicka på **OK**.
7. Extrahera filerna.

Skript finns på *... \\ Mappen inlärnings moduler* .


## <a name="working-with-the-wingtip-tickets-powershell-scripts"></a>Arbeta med ett Wingtip-biljetter PowerShell-skript

För att få ut det mesta av exemplet måste du komma in i de medföljande skripten. Använd Bryt punkter och gå igenom skripten när de körs och kontrol lera hur de olika SaaS-mönstren implementeras. För att enkelt gå igenom de tillhandahållna skripten och modulerna för bästa förståelse rekommenderar vi att du använder [POWERSHELL ISE](/powershell/scripting/components/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>Uppdatera konfigurations filen för distributionen

Redigera filen **userconfig. psm1** med den resurs grupp och det användar värde som du angav under distributionen:

1. Öppna *POWERSHELL ISE* och Läs in... \\ Learning-moduler \\ *userconfig. psm1*
2. Uppdatera *ResourceGroupName* och *namn* med de angivna värdena för din distribution (endast på raderna 10 och 11).
3. Spara ändringarna!

Genom att ange dessa värden behöver du bara uppdatera de här distributions-/regionsspecifika värdena i alla skript.

### <a name="execute-the-scripts-by-pressing-f5"></a>Kör skripten genom att trycka på F5

Flera skript använder *$PSScriptRoot* för att navigera i mappar och *$PSScriptRoot* utvärderas bara när skript körs genom att trycka på **F5**.  Att markera och köra en markering (**F8**) kan resultera i fel, så tryck på **F5** när du kör skript.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Stega igenom skripten för att undersöka implementeringen

Det bästa sättet att förstå skripten är genom att stega igenom dem för att se vad de gör. Kolla in **demo-** scripts som ingår och som visar ett enkelt att följa hög arbets flöde. **Demonstrationen –** skripten visar de steg som krävs för att utföra varje uppgift, så ange Bryt punkter och granska djupare i de enskilda anropen för att se implementerings information för de olika SaaS-mönstren.

Tips för att utforska och stega igenom PowerShell-skript:

- Öppna **demo –** skript i PowerShell ISE.
- Kör eller Fortsätt med **F5** (Använd **F8** rekommenderas inte eftersom *$PSScriptRoot* inte utvärderas när du kör markeringar i ett skript).
- Placera brytpunkter genom att klicka, eller välja en rad och trycka på **F9**.
- Stega över en funktion eller skriptanrop med **F10**.
- Stega in i en funktion eller skriptanrop med **F11**.
- Stega ut ur den aktuella funktionen eller skriptanropet med **Skift + F11**.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Utforska databasschemat och kör SQL-frågor med SSMS

Använd [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) för att ansluta och bläddra bland program servrar och databaser.

Distributionen har ursprungligen klient organisationer och katalog servrar som ska anslutas till. Namngivningen av servrarna beror på databasens hyres mönster (se nedan för mer information).

   - **Fristående program:** servrar för varje klient (t. ex. *contosoconcerthall – &lt; Användar &gt;* Server) och *katalog – sa- &lt; användare &gt;*
   - **Databas per klient:** *tenants1-DPT- &lt; User &gt;* och *Catalog-DPT- &lt; User &gt;* servers
   - **Databas för flera innehavare:** *tenants1-MT- &lt; User &gt;* och *Catalog-MT- &lt; User &gt;* servers

För att säkerställa en lyckad demo anslutning har alla servrar en [brand Väggs regel](firewall-configure.md) som tillåter alla IP-adresser.


1. Öppna *SSMS* och Anslut till klienterna. Server namnet beror på databasens hyres mönster som du har valt (se nedan för information):
    - **Fristående program:** servrar för enskilda klienter (t. ex. *contosoconcerthall – &lt; User &gt; . Database.Windows.net*)
    - **Databas per klient:** *tenants1-DPT- &lt; User &gt; . Database.Windows.net*
    - **Databas för flera klient organisationer:** *tenants1-MT- &lt; User &gt; . Database.Windows.net*
2. Klicka på **Anslut**  >  **databas motor...**:

   ![katalogserver](./media/saas-tenancy-wingtip-app-guidance-tips/connect.png)

3. Autentiseringsuppgifterna för demon är: login = *Developer*, Password = *P \@ ssword1*

    Bilden nedan visar inloggningen för *databasen per klient* mönster.
    ![anslutningen](./media/saas-tenancy-wingtip-app-guidance-tips/tenants1-connect.png)



4. Upprepa steg 2-3 och Anslut till katalog servern (se nedan för angivna Server namn baserat på databasens hyres mönster har valts)
    - **Fristående program:** *katalog-sa- &lt; User &gt; . Database.Windows.net*
    - **Databas per klient:** *katalog-DPT- &lt; User &gt; . Database.Windows.net*
    - **Databas för flera innehavare:** *katalog-MT- &lt; User &gt; . Database.Windows.net*


När du har anslutit bör du se alla servrar. Din lista över databaser kan vara olika beroende på vilka innehavare du har etablerad.

Bilden nedan visar inloggningen för *databasen per klient* mönster.

![objektutforskaren](./media/saas-tenancy-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>Nästa steg
- [Distribuera Wingtip Ticket SaaS fristående program](./saas-standaloneapp-get-started-deploy.md)
- [Distribuera Wingtip-biljetterna SaaS-databas per klient program](./saas-dbpertenant-get-started-deploy.md)
- [Distribuera Wingtip Ticket SaaS-program för flera klient databaser](./saas-multitenantdb-get-started-deploy.md)