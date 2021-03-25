---
title: Registrera en Teradata-källa och installations genomsökningar (för hands version) i Azure avdelningens kontroll
description: Den här artikeln beskriver hur du registrerar en Teradata-källa i Azure avdelningens kontroll och konfigurerar en genomsökning.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 8f300f214ed36b7a5257b7276364027b91edc746
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048110"
---
# <a name="register-and-scan-teradata-source-preview"></a>Registrera och Genomsök Teradata-källa (för hands version)

Den här artikeln beskriver hur du registrerar en Teradata-källa i avdelningens kontroll och konfigurerar en genomsökning.

## <a name="supported-capabilities"></a>Funktioner som stöds

Teradata-källan stöder **fullständig sökning** för att extrahera metadata från en Teradata-databas och hämtar **härkomst** mellan data till gångar.

## <a name="prerequisites"></a>Förutsättningar

1.  Konfigurera den senaste [integrerings körningen med egen värd](https://www.microsoft.com/download/details.aspx?id=39717).
    Mer information finns i [skapa och konfigurera en integration runtime med egen värd](../data-factory/create-self-hosted-integration-runtime.md).

2.  Kontrol lera att [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) är installerat på den virtuella datorn där integration runtime med egen värd är installerat.

3.  Se till att \" Visual C++ redistributable 2012 uppdatering 4 \" är installerat på den lokala datorn för integration Runtime. Om du \' ännu inte har installerat det kan du ladda ned det [här](https://www.microsoft.com/download/details.aspx?id=30679).

4.  Du måste hämta Teradata: s JDBC-drivrutin manuellt på den virtuella datorn där integration runtime med egen värd körs.
    Den körbara JAR-filen kan hämtas från Teradata- [webbplatsen](https://downloads.teradata.com/).

    > [!Note]
    > Driv rutinen bör vara tillgänglig för alla konton på den virtuella datorn. Installera inte i ett användar konto.

5.  De Teradata-databas versioner som stöds är 12. x till 16. x. Se till att ha Läs behörighet till den Teradata-källa som genomsöks.

## <a name="setting-up-authentication-for-a-scan"></a>Konfigurera autentisering för en sökning

Den enda autentisering som stöds för en Teradata-källa är **grundläggande autentisering**.

## <a name="register-a-teradata-source"></a>Registrera en Teradata-källa

Gör så här för att registrera en ny Teradata-källa i data katalogen:

1.  Navigera till ditt avdelningens kontroll-konto.
2.  Välj **källor** i det vänstra navigerings fältet.
3.  Välj **register**
4.  Välj **Teradata** på register källor. Välj **Fortsätt**

    :::image type="content" source="media/register-scan-teradata-source/register-sources.png" alt-text="registrera Teradata-alternativ" border="true":::

På sidan **Registrera källor (Teradata)** gör du följande:

1.  Ange ett **namn** som data källan ska visas med i katalogen.

2.  Ange **värd** namnet för att ansluta till en Teradata-källa. Det kan också vara en IP-adress eller en fullständigt kvalificerad anslutnings sträng till servern.

3.  Välj en samling eller skapa en ny (valfritt)

4.  Slutför för att registrera data källan.

    :::image type="content" source="media/register-scan-teradata-source/register-sources-2.png" alt-text="registrera Teradata" border="true":::

## <a name="creating-and-running-a-scan"></a>Skapa och köra en sökning

Om du vill skapa och köra en ny genomsökning gör du följande:

1.  Klicka på **integration runtime** i hanterings centret. Kontrol lera att en lokal integration Runtime har kon figurer ATS. Om den inte har kon figurer ATS kan du använda stegen som beskrivs [här](./manage-integration-runtimes.md) för att konfigurera en integration runtime med egen värd

2.  Navigera till **källorna**

3.  Välj den registrerade Teradata-källan.

4.  Välj **+ Ny skanning**

5.  Ange informationen nedan:

    a.  **Namn**: namnet på genomsökningen

    b.  **Anslut via integration runtime**: Välj den konfigurerade integrerings körningen med egen värd.

    c.  **Autentiseringsuppgift**: Välj autentiseringsuppgifter för att ansluta till data källan. Se till att:

    -   Välj grundläggande autentisering när du skapar en autentiseringsuppgift.
    -   Ange ett användar namn för att ansluta till databas servern i fältet användar namn indata
    -   Lagra lösen ordet för databas servern i den hemliga nyckeln.

        Om du vill veta mer om autentiseringsuppgifter läser du länken [här](./manage-credentials.md)

6.  **Schema**: lista över en delmängd av scheman som ska importeras uttrycks som en semikolonavgränsad lista. t. ex. Schema1; schema2. Alla användar scheman importeras om listan är tom. Alla system scheman (till exempel SysAdmin) och objekt ignoreras som standard. När listan är tom importeras alla tillgängliga scheman.

    Acceptabla schema namn mönster med SQL LIKE-uttryck inkluderar att använda%, t. ex. en%; T % C%; Styr
    - börja med en eller    
    - sluta med B eller    
    - innehåller C eller    
    - lika med D

    Användning av icke-och specialtecken är inte acceptabelt

7.  **Driv rutins plats**: Ange sökvägen till JDBC-drivrutinen på den virtuella datorn där integration runtime med egen värd körs. Detta bör vara sökvägen till giltig plats för JAR-mappen.

8.  **Maximalt tillgängligt minne:** Högsta mängd minne (i GB) som är tillgängligt på kundens virtuella dator som ska användas genom genomsökning av processer. Detta beror på vilken storlek på Teradata-källan som ska genomsökas.

    > [!Note] 
    > Ange 2 GB minne för varje 1000 tabeller som en tumregel-regel

    :::image type="content" source="media/register-scan-teradata-source/setup-scan.png" alt-text="installations genomsökning" border="true":::

6.  Klicka på **Fortsätt**.

7.  Välj din **genomsöknings utlösare**. Du kan ställa in ett schema eller köra genomsökningen en gång.

8.  Granska din genomsökning och klicka på **Spara och kör**.

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