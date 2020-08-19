---
title: 'Snabbstart: Exportera data för Microsoft Azure Data Box'
description: Lär dig hur du snabbt exporterar dina Azure Data Box-data på Azure-portalen
services: databox
author: priestlg
ms.service: databox
ms.subservice: pod
ms.topic: quickstart
ms.date: 07/17/2020
ms.author: v-grpr
ms.localizationpriority: high
ms.openlocfilehash: fbe14151845d19dd1d0f2a4069afb2badb125077
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2020
ms.locfileid: "88122257"
---
# <a name="quickstart-get-started-with-azure-data-box-to-export-data-from-azure"></a>Snabbstart: Kom igång med Azure Data Box och exportera data från Azure

Den här snabbstarten beskriver hur du exporterar data från Azure till din plats med hjälp av Azure-portalen. Stegen förklarar bland annat hur du ansluter kablar, konfigurerar och kopierar data från Azure. Snabbstarten utförs i Azure-portalen och på enhetens lokala webbgränssnitt.

Detaljerade stegvisa distributions- och spårningsinstruktioner finns i [Självstudie: Skapa en exportorder för Azure Data Box](data-box-deploy-export-ordered.md)

## <a name="prerequisites"></a>Krav

Innan du börjar:

* Kontrollera att den prenumeration du använder för Data Box-tjänsten är någon av följande typer:
  * Microsoft Enterprise-avtal (EA). Läs mer om [EA-prenumerationer](https://azure.microsoft.com/pricing/enterprise-agreement/).
  * Leverantör av molnlösningar (CSP). Läs mer om [Azure CSP-program](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
  * Microsoft Azure-sponsring. Läs mer om [Azure-sponsringsprogrammet](https://azure.microsoft.com/offers/ms-azr-0036p/).

* Kontrollera att du har ägar- eller deltagaråtkomst till prenumerationen för att skapa en Data Box-beställning.
* Granska [säkerhetsföreskrifterna för din Data Box](data-box-safety.md).
* Du har en Azure Data Box-enhet som du ska använda för att flytta data till din plats. Värddatorn måste
  * Köra ett [operativsystem som stöds](data-box-system-requirements.md).
  * Vara ansluten till höghastighetsnätverk. Vi rekommenderar starkt att du har en anslutning på minst 10 GbE. Om en 10 GbE anslutning inte är tillgänglig kan en 1 GbE datalänk användas, men då påverkas kopieringshastigheten.
* Du måste ha tillgång till en plan yta där du kan placera Data Box. Om du vill placera enheten på en standardrackhylla måste du ha en 7U-plats i datacenterracket. Du kan placera enheten liggande eller stående i racket.
* Du har skaffat följande kablar för att ansluta Data Box till värddatorn.
  * Två 10 GbE SFP+ Twinax-kopparkablar (använd med DATA 1-, DATA 2-nätverksgränssnitt)
  * En RJ-45 CAT 6-nätverkskabel (använd med hanteringsnätverksgränssnittet (MGMT))
  * En RJ-45 CAT 6A- eller en RJ-45 CAT 6-nätverkskabel (använd med DATA 3-nätverksgränssnitt konfigurerat som 10 Gbps respektive 1 Gbps)

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="order"></a>Beställa

Det här steget tar ungefär 10 minuter.

1. Skapa en ny Azure Data Box-resurs på Azure-portalen.
2. Välj en befintlig prenumeration som har aktiverats för den här tjänsten och välj överföringstypen **Exportera till Azure**. Ange den **Azure-källregion** där dina data finns och **mållandet** för dataöverföringen.
3. Välj **Data Box**. Den högsta användbara kapaciteten är 80 TB och du kan skapa flera order för större datastorlekar.
4. Välj alternativet för att **lägga till lagringskonto och exporttyp** och **välj exportalternativet**.
5. Ange order- och leveransinformationen. Om tjänsten är tillgänglig i din region anger du e-postadresser för aviseringsmeddelanden, läser sammanfattningen och skapar sedan ordern.

När ordern har skapats förbereds enheten för leverans.

## <a name="cable"></a>Kabel

Det här steget tar ungefär 10 minuter.

När du får Data Box-enheten gör du följande för att kabelansluta, ansluta till och slå på enheten. Det här steget tar ungefär 10 minuter.

1. Fortsätt inte om det finns tecken på att enheten har manipulerats eller skadats. Kontakta Microsoft Support så att de kan skicka en ersättningsenhet.
2. Se till att du har följande kablar innan du kabelansluter enheten:

   * Jordad strömkabel (medföljer) graderad 10 A eller högre med en IEC60320 C-13-kontakt i ena änden av enheten.
   * En RJ-45 CAT 6-nätverkskabel (använd med hanteringsnätverksgränssnittet (MGMT))
   * Två 10 GbE SFP+ Twinax-kopparkablar (använd med 10 Gbps DATA 1-, DATA 2-nätverksgränssnitt)
   * En RJ-45 CAT 6A- eller en RJ-45 CAT 6-nätverkskabel (använd med DATA 3-nätverksgränssnitt konfigurerat som 10 Gbps respektive 1 Gbps)

3. Ta bort och placera enheten på en plan yta.

4. Kabelanslut enheten enligt nedan.  

    ![Bakplan på kabelansluten Data Box-enhet](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)  

    1. Anslut strömkabeln till enheten.
    2. Använd RJ-45 CAT 6-nätverkskabeln för att ansluta värddatorn till MGMT-porten på enheten.
    3. Använd SFP+ Twinax-kopparkabeln för att ansluta minst ett 10 Gbps-nätverksgränssnitt (rekommenderas över 1 Gbps), DATA 1 eller DATA 2 för data.
    4. Slå på enheten. Strömknappen finns på enhetens frontpanel.

## <a name="connect"></a>Anslut

Det här steget tar cirka 5–7 minuter att slutföra.

1. Hämta lösenordet för enheten genom att gå till **Allmänt** > **Enhetsinformation** på [Azure-portalen](https://portal.azure.com).
2. Tilldela en statisk IP-adress, 192.168.100.5, och undernätet 255.255.255.0 till Ethernet-adaptern på den dator som du använder för att ansluta till Data Box. Öppna det lokala webbgränssnittet på enheten på `https://192.168.100.10`. Anslutningen kan upp till 5 minuter att slutföra efter att du slagit på enheten.
3. Logga in med lösenordet från Azure-portalen. Ett felmeddelande visas som anger ett problem med webbplatsens säkerhetscertifikat. Följ de webbläsarspecifika instruktionerna för att fortsätta till webbsidan.
4. Som standard konfigureras nätverksinställningarna för 10 Gbps-datagränssnittet (eller 1 Gbps) som DHCP. Om det behövs kan du konfigurera det här nätverket som statiskt och ange en IP-adress.

## <a name="copy-data"></a>Kopiera data

Hur lång tid det tar att slutföra den här åtgärden beror på datastorleken och nätverkshastigheten.

1. Om du använder en Windows-klient använder du ett SMB-kompatibelt filkopieringsverktyg, till exempel Robocopy. För en NFS-värd använder du kommandot `cp` eller `rsync` för att kopiera data. Om du vill ha mer information om hur du använder Robocopy för att kopiera data går du till [Robocopy](https://technet.microsoft.com/library/ee851678.aspx).
2. Anslut till enhetsresurserna och börja kopiera data till värddatorn.
<!-- 1. Connect to the device shares using the path:`\\<IP address of your device>\ShareName`. To get the share access credentials, go to the **Connect & copy** page in the local web UI of the Data Box. --> 

## <a name="ship-to-azure"></a>Skicka till Azure

Åtgärden tar cirka 10–15 minuter att slutföra.

1. Gå till sidan **Prepare to ship** (Förbered för att skicka)i det lokala webbgränssnittet och börja förbereda frakten.
2. Stäng av enheten via det lokala webbgränssnittet. Ta bort kablarna för enheten.
3. Rulla upp strömkabeln som levererades med enheten och placera kabeln säkert på baksidan av enheten.
4. Returetiketten ska vara synlig på E-ink-skärmen. Om etiketten inte visas på e-ink-skärmen går du till **Översikt** > **Ladda ned adressetikett** på Azure-portalen. Ladda ned en adressetikett och fäst den på enheten.
5. Schemalägg en upphämtning med ditt regionala transportföretag om du returnerar enheten.
6. Nära Data Box-enheten hämtas och skannas av transportföretaget uppdateras orderstatusen i portalen till **hämtad**. Du får också ett spårnings-ID.

## <a name="clean-up-resources"></a>Rensa resurser

Det här steget tar 2–3 minuter att slutföra.

* Du kan annullera Data Box-beställningen på Azure Portal innan orderbehandlingen har påbörjats. När orderbehandlingen har påbörjats kan du inte avbryta ordern. Ordern behandlas tills den når slutskedet i processen. Om du vill avbryta beställningen går du till **Översikt** och väljer **Avbryt** i kommandofältet.

* Du kan ta bort ordern när statusen visas som **slutförd** eller **avbruten** på Azure Portal. Om du vill ta bort ordern går du till **Översikt** och väljer **Ta bort** i kommandofältet.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat en exportorder från Azure till en Azure Data Box. Gå vidare till nästa självstudie om du vill veta mer om Azure Data Box-hantering:

> [!div class="nextstepaction"]
> [Administrera Data Box via Azure-portalen](data-box-portal-admin.md)
