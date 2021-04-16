---
title: Registrera käll- och installationsgenomsökningar för Oracle (förhandsversion) i Azure Purview
description: Den här artikeln beskriver hur du registrerar Oracle-källa i Azure Purview och ställer in en genomsökning.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 40c5e0ff2c2301607f5a548ff05c742c5c5a948d
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517070"
---
# <a name="register-and-scan-oracle-source-preview"></a>Registrera och skanna Oracle-källa (förhandsversion)

Den här artikeln beskriver hur du registrerar en Oracle-databas i Purview och ställer in en genomsökning.

## <a name="supported-capabilities"></a>Funktioner som stöds

Oracle-källan stöder **fullständig genomsökning** för att extrahera metadata från en Oracle-databas **och hämtar ursprung** mellan datatillgångar.

## <a name="prerequisites"></a>Förutsättningar

1.  Konfigurera den senaste [integrationskörningen med egen värd.](https://www.microsoft.com/download/details.aspx?id=39717)
    Mer information finns i [Skapa och konfigurera en integrationskörning med egen värd.](../data-factory/create-self-hosted-integration-runtime.md)

2.  Kontrollera att [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) är installerat på den virtuella datorn där integration runtime med egen värd är installerat.

3.  Kontrollera att \" Visual C++ Redistributable 2012 Uppdatering 4 är installerat på den lokala \" integrationskörningsdatorn. Om du inte \' har installerat det än laddar du ned det [härifrån](https://www.microsoft.com/download/details.aspx?id=30679).

4.  Du måste manuellt ladda ned en Oracle [](https://www.oracle.com/database/technologies/appdev/jdbc-downloads.html) JDBC-drivrutin härifrån på den virtuella datorn där integrationskörning med egen värd körs.

    > [!Note] 
    > Drivrutinen ska vara tillgänglig för alla konton på den virtuella datorn. Installera den inte på ett användarkonto.

5.  Oracle-databasversioner som stöds är från 6i till 19c.

6.  Användarbehörighet: En skrivskyddade åtkomst till systemtabeller krävs. Användaren ska ha behörighet att skapa en session samt rollen SELECT \_ CATALOG \_ ROLE assigned (VÄLJ KATALOGROLL). Alternativt kan användaren ha SELECT-behörighet beviljad för varje enskild systemtabell som den här anslutningsappen frågar metadata från:
       > grant create session to \[ user \] ;\
        grant select on all \_ users to \[ user \] ;\
        grant select on dba \_ objects to \[ user \] ;\
        grant select on dba \_ tab comments to user \_ \[ \] ;\
        grant select on dba \_ external \_ locations to \[ user \] ;\
        grant select on dba \_ directories to \[ user \] ;\
        grant select on dba \_ mviews to \[ user \] ;\
        grant select on dba \_ clu \_ columns to user \[ \] ;\
        grant select on dba \_ tab columns to user \_ \[ \] ;\
        grant select on dba \_ col comments to user \_ \[ \] ;\
        grant select on dba \_ constraints to \[ user \] ;\
        grant select on dba \_ cons columns to user \_ \[ \] ;\
        grant select on dba \_ indexes to \[ user \] ;\
        grant select on dba \_ ind \_ columns to user \[ \] ;\
        grant select on dba \_ procedures to \[ user \] ;\
        grant select on dba \_ synonyms to \[ user \] ;\
        grant select on dba \_ views to \[ user \] ;\
        grant select on dba \_ source to \[ user \] ;\
        grant select on dba \_ triggers to \[ user \] ;\
        grant select on dba \_ arguments to \[ user \] ;\
        grant select on dba \_ sequences to \[ user \] ;\
        grant select on dba \_ dependencies to \[ user \] ;\
        grant select on V \_ \$ INSTANCE to user \[ \] ;\
        grant select on v \_ \$ database to user \[ \] ;
    
## <a name="setting-up-authentication-for-a-scan"></a>Konfigurera autentisering för en genomsökning

Den enda autentisering som stöds för en Oracle-källa är **Grundläggande autentisering**.

## <a name="register-an-oracle-source"></a>Registrera en Oracle-källa

Så här registrerar du en ny Oracle-källa i datakatalogen:

1.  Gå till ditt Purview-konto.
2.  Välj **Källor** i det vänstra navigeringsfönstret.
3.  Välj **Registrera**
4.  På Registrera källor väljer du **Oracle**. Välj **Fortsätt**.

    :::image type="content" source="media/register-scan-oracle-source/register-sources.png" alt-text="registrera källor" border="true":::

På skärmen **Registrera källor (Oracle)** gör du följande:

1.  Ange ett **Namn** som datakällan ska visas i katalogen.

2.  Ange **värdnamnet för** att ansluta till en Oracle-källa. Detta kan antingen vara:
    - Ett värdnamn som används av JDBC för att ansluta till databasservern. Till exempel, MyDatabaseServer.com eller
    - IP-adress. Till exempel 192.169.1.2 eller
    - Dess fullständigt kvalificerade JDBC-anslutningssträng. Till exempel,\
        jdbc:oracle:thin:@(DESCRIPTION=(LOAD \_ BALANCE=on)(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver1)(PORT=1521))(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver2)(PORT=1521))(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver3)(PORT=1521))(CONNECT \_ DATA=(SERVICE \_ NAME=orcl))

3.  Ange det **portnummer som** används av JDBC för att ansluta till databasservern (1521 som standard för Oracle).

4.  Ange **Oracle-tjänstnamnet som** används av JDBC för att ansluta till databasservern.

5.  Välj en samling eller skapa en ny (valfritt)

6.  Slutför registreringen av datakällan.

    :::image type="content" source="media/register-scan-oracle-source/register-sources-2.png" alt-text="alternativ för att registrera källor" border="true":::

## <a name="creating-and-running-a-scan"></a>Skapa och köra en genomsökning

Gör följande för att skapa och köra en ny genomsökning:

1.  I Hanteringscenter klickar du på Integreringskörningar. Kontrollera att en integrationskörning med egen värd har ställts in. Om den inte har ställts in använder du stegen som anges [här](./manage-integration-runtimes.md) för att skapa en integration runtime med egen värd.

2.  Gå till Sources ( **Källor).**

3.  Välj den registrerade Oracle-källan.

4.  Välj **+ Ny genomsökning.**

5.  Ange informationen nedan:

    a.  **Namn:** Namnet på genomsökningen

    b.  **Ansluta via Integration Runtime:** Välj den konfigurerade integrationskörningen med egen värd

    c.  **Autentiseringsuppgifter:** Välj autentiseringsuppgifterna för att ansluta till datakällan. Se till att:
    - Välj Grundläggande autentisering när du skapar en autentiseringsautentisering.        
    - Ange användarnamnet som används av JDBC för att ansluta till databasservern i fältet Användarnamn        
    - Lagra användarlösenordet som används av JDBC för att ansluta till databasservern i den hemliga nyckeln.

    d.  **Schema:** Lista delmängd av scheman som ska importeras uttryckt som en semikolonavgränsad lista. Till exempel schema1; schema2. Alla användarscheman importeras om listan är tom. Alla systemscheman (till exempel SysAdmin) och objekt ignoreras som standard. När listan är tom importeras alla tillgängliga scheman.
        Godkända schemanamnmönster med SQL LIKE-uttryck syntax inkluderar användning av %, till exempel. A %; %B; %C%; D
       -   börja med A eller        
       -   avsluta med B eller        
       -   innehålla C eller        
       -   lika med D

    Användning av NOT och specialtecken är inte acceptabelt.

6.  **Drivrutinsplats:** Ange sökvägen till JDBC-drivrutinsplatsen på den virtuella dator där integrationskörning med egen värd körs. Detta bör vara sökvägen till en giltig JAR-mappplats.

7.  **Maximalt tillgängligt minne:** Maximalt minne (i GB) som är tillgängligt på kundens virtuella dator som ska användas av genomsökningsprocesser. Detta beror på storleken på SAP S/4HANA-källan som ska genomsökas.

    :::image type="content" source="media/register-scan-oracle-source/scan.png" alt-text="genomsöka orakel" border="true":::

8.  Klicka på **Fortsätt.**

9.  Välj **sökutlösaren**. Du kan konfigurera ett schema eller genomsöka en gång.

10.  Granska genomsökningen och klicka på **Spara och kör**.

## <a name="viewing-your-scans-and-scan-runs"></a>Visa dina genomsökningar och genomsökningskörningar

1. Gå till hanteringscentret. Välj **Datakällor** under avsnittet **Källor och genomsökning.**

2. Välj önskad datakälla. Du ser en lista över befintliga genomsökningar på den datakällan.

3. Välj den genomsökning vars resultat du är intresserad av att visa.

4. Den här sidan visar alla tidigare genomsökningskörningar tillsammans med mått och status för varje genomsökningskörning. Den visar också om genomsökningen har schemalagts eller manuellt, hur många tillgångar som hade tillämpade klassificeringar, hur många totala tillgångar som identifierades, start- och sluttiden för genomsökningen samt den totala genomsökningstiden.

## <a name="manage-your-scans"></a>Hantera dina genomsökningar

Om du vill hantera eller ta bort en genomsökning gör du följande:

1. Gå till hanteringscentret. Välj **Datakällor** under **avsnittet Källor och genomsökning** och välj sedan önskad datakälla.

2. Välj den genomsökning som du vill hantera. Du kan redigera genomsökningen genom att välja **Redigera**.

3. Du kan ta bort genomsökningen genom att välja **Ta bort**.

## <a name="next-steps"></a>Nästa steg

- [Bläddra i Azure Purview Data-katalogen](how-to-browse-catalog.md)
- [Sök i Azure Purview-Data Catalog](how-to-search-catalog.md)