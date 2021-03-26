---
title: Registrera Oracle-källa och installations genomsökningar (för hands version) i Azure avdelningens kontroll
description: Den här artikeln beskriver hur du registrerar Oracle-källor i Azure avdelningens kontroll och konfigurerar en genomsökning.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 76aadd667691e12c61e0e5e13c13ca0241a9f0ce
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045509"
---
# <a name="register-and-scan-oracle-source-preview"></a>Registrera och skanna Oracle-källa (för hands version)

Den här artikeln beskriver hur du registrerar en Oracle-databas i avdelningens kontroll och konfigurerar en genomsökning.

## <a name="supported-capabilities"></a>Funktioner som stöds

Oracle-källan stöder **fullständig sökning** för att extrahera metadata från en Oracle-databas och hämtar **härkomst** mellan data till gångar.

## <a name="prerequisites"></a>Förutsättningar

1.  Konfigurera den senaste [integrerings körningen med egen värd](https://www.microsoft.com/download/details.aspx?id=39717).
    Mer information finns i [skapa och konfigurera en integration runtime med egen värd](../data-factory/create-self-hosted-integration-runtime.md).

2.  Kontrol lera att [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) är installerat på den virtuella datorn där integration runtime med egen värd är installerat.

3.  Se till att \" Visual C++ redistributable 2012 uppdatering 4 \" är installerat på den lokala datorn för integration Runtime. Om du \' ännu inte har installerat det kan du ladda ned det [här](https://www.microsoft.com/download/details.aspx?id=30679).

4.  Du måste manuellt hämta en Oracle JDBC-drivrutin [manuellt från den](https://www.oracle.com/database/technologies/appdev/jdbc-downloads.html) virtuella datorn där integration runtime med egen värd körs.

    > [!Note] 
    > Driv rutinen bör vara tillgänglig för alla konton på den virtuella datorn. Installera det inte i ett användar konto.

5.  De Oracle Database-versioner som stöds är 6i till 19c.

6.  Användar behörighet: för att säkerställa en lyckad sökning för första gången krävs en fullständig sys-administratörs typ behörighet.

    För efterföljande genomsökningar krävs en skrivskyddad åtkomst till system tabeller. Användaren måste ha behörighet att skapa en session och roll rollen Välj katalog som \_ har \_ tilldelats. Alternativt kan användaren ha SELECT-behörighet beviljad för varje enskild system tabell som den här anslutningen efterfrågar metadata från:
       > bevilja skapa session till \[ användare \] ; \
        bevilja Välj alla \_ användare till \[ användare \] ; \
        bevilja Välj på DBA- \_ objekt till \[ användare \] ; \
        Tillåt Välj på DBA- \_ fliken \_ kommentarer till \[ användare \] ; \
        bevilja Välj på DBA- \_ externa \_ platser till \[ användare \] ; \
        bevilja Välj på DBA- \_ kataloger till \[ användare \] ; \
        bevilja Välj på DBA- \_ mviews till \[ användare \] ; \
        bevilja Välj på DBA- \_ CLU \_ kolumner till \[ användare \] ; \
        bevilja Välj på fliken DBA- \_ \_ kolumner till \[ användare \] ; \
        bevilja Välj på DBA \_ - \_ textkommentarer till \[ användare \] ; \
        Tillåt val av dBA- \_ begränsningar till \[ användare \] ; \
        bevilja Välj på DBA \_ - \_ kolumner till \[ användare \] ; \
        bevilja Välj på DBA- \_ index till \[ användare \] ; \
        bevilja Välj på DBA- \_ IND \_ kolumner till \[ användare \] ; \
        bevilja urval av dBA- \_ procedurer till \[ användare \] ; \
        bevilja Välj på DBA- \_ synonymer till \[ användare \] ; \
        bevilja Välj i DBA- \_ vyer till \[ användare \] ; \
        bevilja Välj på DBA- \_ källa till \[ användare \] ; \
        bevilja Välj på DBA- \_ utlösare till \[ användare \] ; \
        Tillåt val av dBA- \_ argument till \[ användare \] ; \
        bevilja val på DBA- \_ sekvenser till \[ användare \] ; \
        bevilja Select on DBA- \_ beroenden till \[ användare \] ; \
        bevilja Select on V- \_ \$ instans till \[ användare \] ; \
        bevilja användaren Select-v- \_ \$ databas \[ \]
    
## <a name="setting-up-authentication-for-a-scan"></a>Konfigurera autentisering för en sökning

Den enda autentisering som stöds för en Oracle-källa är **grundläggande autentisering**.

## <a name="register-an-oracle-source"></a>Registrera en Oracle-källa

Registrera en ny Oracle-källa i din data katalog genom att göra följande:

1.  Navigera till ditt avdelningens kontroll-konto.
2.  Välj **källor** i det vänstra navigerings fältet.
3.  Välj **register**
4.  Välj **Oracle** på register källor. Välj **Fortsätt**.

    :::image type="content" source="media/register-scan-oracle-source/register-sources.png" alt-text="registrera källor" border="true":::

På sidan **Registrera källor (Oracle)** gör du följande:

1.  Ange ett **namn** som data källan ska visas i katalogen.

2.  Ange **värd** namnet för att ansluta till en Oracle-källa. Detta kan antingen vara:
    - Ett värdnamn som används av JDBC för att ansluta till databas servern. Till exempel MyDatabaseServer.com eller
    - IP-adress. Till exempel 192.169.1.2 eller
    - Den fullständigt kvalificerade JDBC-anslutningssträngen. Till exempel \
        JDBC: Oracle: tunn: @ (beskrivning = (BELASTNINGs \_ utjämning = på) (adress = (protokoll = TCP) (värd = oracleserver1) (port = 1521)) (adress = (protokoll = TCP) (värd = oracleserver2) (port = 1521)) (adress = (protokoll = TCP) (värd = oracleserver3) (port = 1521)) (Anslut \_ data = (tjänst \_ namn = ORCL)))

3.  Ange det **port nummer** som används av JDBC för att ansluta till databas servern (1521 som standard för Oracle).

4.  Ange det **Oracle-servernamn** som används av JDBC för att ansluta till databas servern.

5.  Välj en samling eller skapa en ny (valfritt)

6.  Slutför för att registrera data källan.

    :::image type="content" source="media/register-scan-oracle-source/register-sources-2.png" alt-text="alternativ för registrering av källor" border="true":::

## <a name="creating-and-running-a-scan"></a>Skapa och köra en sökning

Om du vill skapa och köra en ny genomsökning gör du följande:

1.  Klicka på integration runtime i hanterings centret. Kontrol lera att en lokal integration Runtime har kon figurer ATS. Om den inte har kon figurer ATS använder du stegen som beskrivs [här](./manage-integration-runtimes.md) för att skapa en egen värd för integration Runtime.

2.  Navigera till **källor**.

3.  Välj den registrerade Oracle-källan.

4.  Välj **+ Ny skanning**.

5.  Ange informationen nedan:

    a.  **Namn**: namnet på genomsökningen

    b.  **Anslut via integration runtime**: Välj den konfigurerade integrerings körningen med egen värd

    c.  **Autentiseringsuppgift**: Välj autentiseringsuppgifter för att ansluta till data källan. Se till att:
    - Välj grundläggande autentisering när du skapar en autentiseringsuppgift.        
    - Ange det användar namn som används av JDBC för att ansluta till databas servern i fältet användar namn indata        
    - Lagra det användar lösen ord som används av JDBC för att ansluta till databas servern i den hemliga nyckeln.

    d.  **Schema**: lista över en delmängd av scheman som ska importeras uttrycks som en semikolonavgränsad lista. Till exempel Schema1; schema2. Alla användar scheman importeras om listan är tom. Alla system scheman (till exempel SysAdmin) och objekt ignoreras som standard. När listan är tom importeras alla tillgängliga scheman.
        Acceptabla schema namn mönster med SQL LIKE-uttryck inkluderar användning av%, till exempel. En%; T % C%; Styr
       -   börja med en eller        
       -   sluta med B eller        
       -   innehåller C eller        
       -   lika med D

    Användning av icke-och specialtecken är inte acceptabelt.

6.  **Driv rutins plats**: Ange sökvägen till JDBC-drivrutinen på den virtuella datorn där integration runtime med egen värd körs. Detta bör vara sökvägen till giltig plats för JAR-mappen.

7.  **Maximalt tillgängligt minne**: högsta mängd minne (i GB) som är tillgängligt på kundens virtuella dator som ska användas genom genomsökning av processer. Detta beror på storleken på SAP S/4HANA-källan som ska genomsökas.

    :::image type="content" source="media/register-scan-oracle-source/scan.png" alt-text="Sök igenom Oracle" border="true":::

8.  Klicka på **Fortsätt**.

9.  Välj din **genomsöknings utlösare**. Du kan ställa in ett schema eller köra genomsökningen en gång.

10.  Granska din genomsökning och klicka på **Spara och kör**.

## <a name="viewing-your-scans-and-scan-runs"></a>Visa genomsökningar och skannings körningar

1. Gå till hanterings centret. Välj **data källor** under avsnittet **källor och sökning** .

2. Välj önskad data källa. En lista över befintliga genomsökningar visas på den data källan.

3. Välj den genomsökning vars resultat du är intresse rad av.

4. På den här sidan visas alla tidigare skannings körningar tillsammans med mått och status för varje genomsöknings körning. Den visar även om din genomsökning har schemalagts eller manuell, hur många till gångar som har klassificeringar tillämpade, hur många totala till gångar som har identifierats, start-och slut tid för genomsökningen och den totala genomsökningens varaktighet.

## <a name="manage-your-scans"></a>Hantera dina sökningar

Gör följande för att hantera eller ta bort en genomsökning:

1. Gå till hanterings centret. Välj **data källor** under avsnittet **källor och sökning** och välj sedan på önskad data källa.

2. Välj den genomsökning som du vill hantera. Du kan redigera sökningen genom att välja **Redigera**.

3. Du kan ta bort din genomsökning genom att välja **ta bort**.

## <a name="next-steps"></a>Nästa steg

- [Bläddra i Azure avdelningens kontroll Data Catalog](how-to-browse-catalog.md)
- [Sök i Azure avdelningens kontroll-Data Catalog](how-to-search-catalog.md)