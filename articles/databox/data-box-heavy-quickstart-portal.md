---
title: Snabbstart för Microsoft Azure Data Box Heavy | Microsoft Docs
description: I den här snabbstarten lär du dig hur du distribuerar Azure Data Box Heavy med hjälp av Azure-portalen, inklusive hur du ansluter kablar, konfigurerar och kopierar data som ska laddas upp till Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: quickstart
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 5a0a040d80911b086561213bd0884ed67545c618
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2020
ms.locfileid: "87920797"
---
::: zone target = "docs"

# <a name="quickstart-deploy-azure-data-box-heavy-using-the-azure-portal"></a>Snabbstart: Distribuera Azure Data Box Heavy med hjälp av Azure-portalen

Den här snabbstarten beskriver hur du distribuerar Azure Data Box Heavy med hjälp av Azure-portalen. Stegen omfattar hur du kopplar in, konfigurerar och kopierar data till Data Box Heavy så att de laddas upp till Azure. Snabbstarten utförs i Azure-portalen och på enhetens lokala webbgränssnitt.

Detaljerade stegvisa distributions- och spårningsinstruktioner finns i [Självstudie: Beställa Azure Data Box Heavy](data-box-heavy-deploy-ordered.md)

## <a name="prerequisites"></a>Krav

Slutför följande konfigurationskrav för installationsplatsen, Data Box-tjänsten och enheten innan du distribuerar enheten.

### <a name="for-installation-site"></a>För installationsplatsen

Innan du börjar ska du kontrollera att:

- Enheten går in genom alla dörrar. Enhetens mått är: bredd: 26” längd: 48” höjd: 28”.
- Du har åtkomst till enheten via en hiss eller ramp om du planerar att installera på en annan våning än nedervåningen.
- Det finns två personer som kan hantera enheten. Enheten väger cirka 500 kg och står på hjul.
- Du har tillgång till en plan yta i datacentret med närhet till en nätverksanslutning som kan hantera en enhet med det här fotavtrycket.

### <a name="for-service"></a>För tjänsten

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

### <a name="for-device"></a>För enheten

Innan du börjar ska du kontrollera att:

- Du har gått igenom [säkerhetsriktlinjerna för Data Box Heavy](data-box-safety.md).
- Du har en värddator som är ansluten till datacenternätverket. Data Box Heavy kommer att kopiera data från den här datorn. Värddatorn måste köra ett [operativsystem som stöds](data-box-heavy-system-requirements.md).
- Du har en bärbar dator med RJ-45-kabel för att kunna ansluta till det lokala användargränssnittet och konfigurera enheten. Använd den bärbara datorn för att konfigurera varje nod av enheten en gång.
- Ditt datacenter har ett höghastighetsnätverk och du har minst en 10 GbE-anslutning.
- Du behöver en 40 Gbit/s-kabel eller 10 Gbit/s-kabel per enhetsnod. Välj kablar som är kompatibla med Mellanox MCX314A-BCCT-nätverksgränssnittet:
    - För 40 Gbit/s-kabeln måste enhetens kabelände vara QSFP+.
    - För 10 Gbit/s-kabeln behöver du en SFP+-kabel som ansluts till en 10-G-växel i ena änden, med en QSFP+ till SFP+-adapter (eller QSA-adaptern) för änden som ansluts till enheten.
- Strömkablarna finns i ett fack på baksidan av enheten.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="order"></a>Beställa

Det här steget tar ungefär 5 minuter.

1. Skapa en ny Azure Data Box-resurs på Azure-portalen.
2. Välj en befintlig prenumeration som har aktiverats för den här tjänsten och välj överföringstypen **Importera**. Ange **ursprungslandet** där dina data finns och **Azure-målregionen** för dataöverföringen.
3. Välj **Data Box Heavy**. Den högsta användbara kapaciteten är 770 TB och du kan skapa flera order för större datastorlekar.
4. Ange order- och leveransinformationen. Om tjänsten är tillgänglig i din region anger du e-postadresser för aviseringsmeddelanden, läser sammanfattningen och skapar sedan ordern.

När ordern har skapats förbereds enheten för leverans.

::: zone-end

::: zone target = "chromeless"

## <a name="cable-and-connect-to-your-device"></a>Koppla in och ansluta till enheten

När du har gått igenom förhandskraven är det dags att koppla in och ansluta enheten.

::: zone-end

## <a name="cable-for-power"></a>Strömkabel

Det här steget tar ungefär 5 minuter.

När du får Data Box Heavy följder du stegen nedan för att koppla in och sätta igång enheten.

1. Fortsätt inte om det finns tecken på att enheten har manipulerats eller skadats. Kontakta Microsoft Support så att de kan skicka en ersättningsenhet.
2. Flytta enheten till installationsplatsen och lås de bakre hjulen.
3. Anslut alla fyra strömkablar till strömförsörjningen på baksidan av enheten.
4. Aktivera enhetens noder med hjälp av strömknapparna på den främre panelen.

## <a name="cable-first-node-for-network"></a>Koppla in den första noden för nätverket

Det här steget tar 10–15 minuter.

1. Använd RJ-45 CAT 6-nätverkskabeln till att ansluta värddatorn till hanteringsporten (MGMT) på enheten.
2. Använd Twinax QSFP+-kopparkabeln för att ansluta minst ett 40 Gbit/s-nätverksgränssnitt (rekommenderas framför 1 Gbit/s), DATA 1 eller DATA 2 för data. Om du använder en 10 Gbit/s-växel använder du en Twinax SFP+-kopparkabel med en QSFP+ till SFP+-adapter (QSA-adaptern) för att ansluta 40 Gbit/s-nätverksgränssnittet för data.
3. Kabelanslut enheten enligt nedan.  

    ![Inkopplad Data Box Heavy](media/data-box-heavy-quickstart-portal/data-box-heavy-ports-cabled.png)  

## <a name="configure-first-node"></a>Konfigurera den första noden

Det här steget tar cirka 5–7 minuter att slutföra.

1. Hämta lösenordet för enheten på **Allmänt > Enhetsinformation** på [Azure-portalen](https://portal.azure.com). Samma lösenord används för båda noderna på enheten.
2. Tilldela den statiska IP-adressen 192.168.100.5 och undernätet 255.255.255.0 till Ethernet-adaptern på den dator som du använder för att ansluta till Data Box Heavy. Öppna det lokala webbgränssnittet på enheten på `https://192.168.100.10`. Anslutningen kan upp till 5 minuter att slutföra efter att du slagit på enheten.
3. Logga in med lösenordet från Azure-portalen. Ett felmeddelande visas som anger ett problem med webbplatsens säkerhetscertifikat. Följ de webbläsarspecifika instruktionerna för att fortsätta till webbsidan.
4. Som standard konfigureras nätverksinställningarna för gränssnitten (förutom MGMT) som DHCP. Om det behövs kan du konfigurera det här nätverket som statiskt och ange en IP-adress.

## <a name="cable-and-configure-the-second-node"></a>Koppla in och konfigurera den andra noden

Det här steget tar 15–20 minuter.

Följ samma steg som för den första noden för att koppla in och konfigurera den andra noden på enheten.  


::: zone target = "docs"

## <a name="copy-data"></a>Kopiera data

Hur lång tid åtgärden tar beror på datastorleken och hastigheten i nätverket som data kopieras i.
 
1. Kopiera data till båda enhetsnoderna med hjälp av båda 40 Gbit/s-datagränssnitten parallellt.

    - Om du använder en Windows-värd använder du ett SMB-kompatibelt filkopieringsverktyg, till exempel [Robocopy](https://technet.microsoft.com/library/ee851678.aspx).
    - För en NFS-värd använder du kommandot `cp` eller `rsync` för att kopiera data.
2. Anslut till resurserna på enheten med hjälp av sökvägen:`\\<IP address of your device>\ShareName`. Du kan hämta autentiseringsuppgifterna för åtkomst till resursen på sidan **Connect & copy** (Anslut och kopiera) i det lokala webbgränssnittet i Data Box Heavy.
3. Kontrollera att resurs- och mappnamnen och data följer riktlinjerna som beskrivs i [tjänstbegränsningarna för Azure Storage och Data Box Heavy](data-box-heavy-limits.md).

## <a name="prepare-to-ship"></a>Förbereda för att skicka

Hur lång tid det tar att slutföra den här åtgärden beror på datastorleken.

1. När datakopieringen har slutförts utan fel går du till sidan **Förbered för att skicka** i det lokala webbgränssnittet och startar leveransförberedelsen.
2. När **Förbered för att skicka** har slutförts på båda noderna stänger du av enheten från det lokala webbgränssnittet.

## <a name="ship-to-azure"></a>Skicka till Azure

Åtgärden tar 15–20 minuter.

1. Avlägsna kablarna och placera dem i facket på baksidan av enheten.
2. Schemalägg en upphämtning med ditt regionala transportföretag.
3. Kontakta [Data Box Operations](mailto:DataBoxOps@microsoft.com) för att informera om upphämtningen och för att få returfraktsedeln.
4. Sätt fast returfraktsedeln på enhetens frontpanel.

## <a name="verify-data"></a>Verifiera data

Hur lång tid det tar att slutföra den här åtgärden beror på datastorleken.

1. När Data Box Heavy-enheten ansluts till Azure-datacentrets nätverk laddas dina data automatiskt upp till Azure.
2. Data Box-tjänsten meddelar dig via Azure-portalen när datakopieringen är klar.

    1. Kontrollera felloggarna för eventuella fel och vidta lämpliga åtgärder.
    2. Kontrollera att alla data finns på lagringskontot innan du tar bort dem från källan.

## <a name="clean-up-resources"></a>Rensa resurser

Det här steget tar 2–3 minuter att slutföra.

- Du kan annullera Data Box Heavy-ordern på Azure-portalen innan orderbehandlingen har påbörjats. När orderbehandlingen har påbörjats kan du inte avbryta ordern. Ordern behandlas tills den når slutskedet i processen. Om du vill avbryta beställningen går du till **Översikt** och klickar på **Avbryt** i kommandofältet.

- Du kan ta bort ordern när statusen visas som **slutförd** eller **avbruten** på Azure Portal. Om du vill ta bort ordern går du till **Översikt** och klickar på **Ta bort** i kommandofältet.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat en Data Box Heavy-enhet för att importera dina data till Azure. Gå vidare till nästa självstudie om du vill veta mer om Azure Data Box Heavy-hantering:

> [!div class="nextstepaction"]
> [Administrera Data Box Disk Heavy via Azure-portalen](data-box-portal-admin.md)

::: zone-end
