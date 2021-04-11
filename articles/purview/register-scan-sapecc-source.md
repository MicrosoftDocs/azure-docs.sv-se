---
title: Registrera SAP ECC-källa och installations genomsökningar (för hands version) i Azure avdelningens kontroll
description: Den här artikeln beskriver hur du registrerar SAP ECC-källor i Azure avdelningens kontroll och konfigurerar en genomsökning.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: af3f54778882df9aaa06297f291c12a0f4b1577c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105046053"
---
# <a name="register-and-scan-sap-ecc-source-preview"></a>Registrera och skanna SAP ECC-källa (för hands version)

Den här artikeln beskriver hur du registrerar en SAP ECC-källa i avdelningens kontroll och konfigurerar en genomsökning.

## <a name="supported-capabilities"></a>Funktioner som stöds

SAP ECC-källan stöder **fullständig sökning** för att extrahera metadata från en SAP ECC-instans och hämtar **härkomst** mellan data till gångar.

## <a name="prerequisites"></a>Förutsättningar

1.  Konfigurera den senaste [integrerings körningen med egen värd](https://www.microsoft.com/download/details.aspx?id=39717).
    Mer information finns i [skapa och konfigurera en integration runtime med egen värd](../data-factory/create-self-hosted-integration-runtime.md).

2.  Kontrol lera att [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) är installerat på den virtuella datorn där integration runtime med egen värd är installerat.

3.  Se till att \" Visual C++ redistributable 2012 uppdatering 4 \" är installerat på den lokala datorn för integration Runtime. Om du \' ännu inte har installerat det kan du ladda ned det [här](https://www.microsoft.com/download/details.aspx?id=30679).

4.  Hämta 64-bitars [SAP-anslutaren för Microsoft .NET 3,0](https://support.sap.com/en/product/connectors/msnet.html) från SAP \' s webbplats och installera den på den lokala integration runtime-datorn. Under installationen ser du till att du väljer alternativet **Installera sammansättningar till GAC** i fönstret **valfria installations steg** .

    :::image type="content" source="media/register-scan-sapecc-source/requirement.png" alt-text="krav" border="true":::

5.  Anslutningen läser metadata från SAP med API: t Java Connector (JCo) 3,0. Se därför till att Java Connector är tillgängligt på den virtuella datorn där integration runtime med egen värd är installerat.
    Kontrol lera att du använder rätt JCo-distribution för din miljö. Se till exempel att sapjco3. jar-och sapjco3.dll-filerna är tillgängliga på en Microsoft Windows-dator.

    > [!Note] 
    > Driv rutinen bör vara tillgänglig för alla konton på den virtuella datorn. Installera det inte i ett användar konto.

6.  Distribuera ABAP-modulen för metadata extrahering på SAP-servern genom att följa anvisningarna i [distributions guiden för ABAP Functions](abap-functions-deployment-guide.md). Du behöver ett ABAP Developer-konto för att skapa modulen RFC-funktion på SAP-servern. Användar kontot måste ha tillräcklig behörighet för att ansluta till SAP-servern och köra följande moduler i RFC-funktionen:
    -   STFC_CONNECTION (kontrol lera anslutning)
    -   RFC_SYSTEM_INFO (kontrol lera system information)


## <a name="setting-up-authentication-for-a-scan"></a>Konfigurera autentisering för en sökning

Den enda autentisering som stöds för SAP ECC-källan är **grundläggande autentisering**.

## <a name="register-sap-ecc-source"></a>Registrera SAP ECC-källa

Gör så här för att registrera en ny SAP ECC-källa i data katalogen:

1.  Navigera till ditt avdelningens kontroll-konto.
2.  Välj **källor** i det vänstra navigerings fältet.
3.  Välj **register**
4.  Välj **SAP ECC** på register källor. Välj **Fortsätt.**

    :::image type="content" source="media/register-scan-sapecc-source/register-sapecc.png" alt-text="registrera SAPECC-alternativ" border="true":::

På skärmen **Registrera källor (SAP ECC)** gör du följande:

1.  Ange ett **namn** som data källan ska visas i katalogen.

2.  Ange namnet på **program servern** för att ansluta till SAP ECC-källan.
    Det kan också vara en IP-adress till SAP Application Server-värden.

3.  Ange SAP- **system numret**. Detta är ett tvåsiffrigt heltal mellan 00 och 99.

4.  Välj en samling eller skapa en ny (valfritt)

5.  Slutför för att registrera data källan.

    :::image type="content" source="media/register-scan-sapecc-source/register-sapecc-2.png" alt-text="registrera SAPECC" border="true":::

## <a name="creating-and-running-a-scan"></a>Skapa och köra en sökning

Om du vill skapa och köra en ny genomsökning gör du följande:

1.  Klicka på integration runtime i hanterings centret. Kontrol lera att en lokal integration Runtime har kon figurer ATS. Om den inte har kon figurer ATS använder du stegen som beskrivs [här](./manage-integration-runtimes.md) för att skapa en egen värd för integration Runtime.

2.  Navigera till **källor**

3.  Välj den registrerade SAP ECC-källan.

4.  Välj **+ Ny skanning**

5.  Ange informationen nedan:

    a.  **Namn**: namnet på genomsökningen

    b.  **Anslut via integration runtime**: Välj den konfigurerade integrerings körningen med egen värd

    c.  **Autentiseringsuppgift**: Välj autentiseringsuppgifter för att ansluta till data källan. Se till att:

    -   Välj grundläggande autentisering när du skapar en autentiseringsuppgift.
    -   Ange ett användar-ID för att ansluta till SAP-servern i fältet användar namn indata.
    -   Lagra användar lösen ordet som används för att ansluta till SAP-servern i den hemliga nyckeln.

    d.  **Klient-ID**: Ange SAP-klient-ID: t. Detta är ett tal med tre siffror från 000 till 999.

    e.  **Biblioteks Sök väg för JCo**: sökvägen till katalogen där JCo-biblioteken finns

    f.  **Maximalt tillgängligt minne:** Högsta mängd minne (i GB) som är tillgängligt på kundens virtuella dator som ska användas genom genomsökning av processer. Detta beror på storleken på SAP ECC-källan som ska genomsökas.

    :::image type="content" source="media/register-scan-sapecc-source/scan-sapecc.png" alt-text="Genomsök SAPECC" border="true":::

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