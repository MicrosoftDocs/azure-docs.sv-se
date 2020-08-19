---
title: Snabbstart för Microsoft Azure Data Box| Microsoft Docs
description: I den här snabbstarten lär du dig hur du distribuerar Azure Data Box från Azure-portalen för en importorder. Konfigurera Azure Data Box och kopiera data för uppladdning till Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: quickstart
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 2ee9d1edefb0e248d0eba13e2d0b9f1c651fdd42
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2020
ms.locfileid: "87924520"
---
# <a name="get-started-with-azure-data-box-to-import-data-into-azure"></a>Kom igång med Azure Data Box för att importera data till Azure

::: zone target="docs"

Den här snabbstarten beskriver hur du distribuerar Azure Data Box med hjälp av Azure-portalen för en importbeställning. Stegen omfattar hur du kabelansluter, konfigurerar och kopierar data till Data Box så att den laddas upp till Azure. Stegen i snabbstarten utförs på Azure-portalen och i enhetens lokala webbgränssnitt.

Detaljerade stegvisa distributions- och spårningsinstruktioner finns i [Självstudie: Beställa Azure Data Box](data-box-deploy-ordered.md)

::: zone-end 

::: zone target="chromeless"

Den här guiden beskriver hur du distribuerar Azure Data Box för en import med hjälp av Azure-portalen. Stegen beskriver förhandskraven, hur du kopplar in och ansluter enheten och hur du kopierar data till enheten så att de laddas upp till Azure.

::: zone-end

::: zone target="docs"
 
## <a name="prerequisites"></a>Krav

Innan du börjar:

- Kontrollera att den prenumeration du använder för Data Box-tjänsten är någon av följande typer:
    - Microsoft Enterprise-avtal (EA). Läs mer om [EA-prenumerationer](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Leverantör av molnlösningar (CSP). Läs mer om [Azure CSP-program](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
    - Microsoft Azure-sponsring. Läs mer om [Azure-sponsringsprogrammet](https://azure.microsoft.com/offers/ms-azr-0036p/). 

- Kontrollera att du har ägar- eller deltagaråtkomst till prenumerationen för att skapa en Data Box-beställning.
- Granska [säkerhetsföreskrifterna för din Data Box](data-box-safety.md).
- Du har en värddator som har de data du vill kopiera över till Data Box. Värddatorn måste
    - Köra ett [operativsystem som stöds](data-box-system-requirements.md).
    - Vara ansluten till höghastighetsnätverk. Vi rekommenderar starkt att du har minst en 10 GbE anslutning. Om en 10 GbE anslutning inte är tillgänglig kan en 1 GbE datalänk användas med kopieringshastigheten påverkas. 
- Du måste ha tillgång till en plan yta där du kan placera Data Box. Om du vill placera enheten på en standardrackhylla måste du ha en 7U-plats i datacenterracket. Du kan placera enheten liggande eller stående i racket.
- Du har skaffat följande kablar för att ansluta Data Box till värddatorn.
    - Två 10 GbE SFP+ Twinax-kopparkablar (använd med DATA 1-, DATA 2-nätverksgränssnitt)
    - En RJ-45 CAT 6-nätverkskabel (använd med MGMT-nätverksgränssnitt)
    - En RJ-45 CAT 6A- eller en RJ-45 CAT 6-nätverkskabel (använd med DATA 3-nätverksgränssnitt konfigurerat som 10 Gbps respektive 1 Gbps)

::: zone-end 

::: zone target="chromeless"

## <a name="prerequisites"></a>Krav

Kontrollera följande innan du börjar:

1. Du har gått [självstudien: Beställ Azure Data Box](data-box-deploy-ordered.md).
2. Du har tagit emot Data Box-enheten och orderstatusen på portalen är **Levererad**. 
3. Du har läst [säkerhetsriktlinjerna för Data Box](data-box-safety.md).
4. Du har fått en jordad strömkabel som ska användas med 100 TB-lagringsenheten.
5. Du har åtkomst till värddatorn där de data som ska kopieras till Data Box finns. Värddatorn måste
    - Köra ett [operativsystem som stöds](data-box-system-requirements.md).
    - Vara ansluten till höghastighetsnätverk. Vi rekommenderar starkt att du har en anslutning på minst 10 GbE. Om en 10 GbE anslutning inte är tillgänglig kan en 1 GbE datalänk användas, men då påverkas kopieringshastigheten. 
6. Du har tillgång till en plan yta där du kan ställa Data Box-enheten. För att kunna placera enheten plant eller upprätt på en hylla i ett standardrack måste du ha en 7U-plats i racket.

::: zone-end

::: zone target="docs"

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="order"></a>Beställa

Det här steget tar ungefär 5 minuter.

1. Skapa en ny Azure Data Box-resurs på Azure-portalen.
2. Välj en befintlig prenumeration som har aktiverats för den här tjänsten och välj överföringstypen **Importera**. Ange **ursprungslandet** där dina data finns och **Azure-målregionen** för dataöverföringen.
3. Välj **Data Box**. Den högsta användbara kapaciteten är 80 TB och du kan skapa flera order för större datastorlekar.
4. Ange order- och leveransinformationen. Om tjänsten är tillgänglig i din region anger du e-postadresser för aviseringsmeddelanden, läser sammanfattningen och skapar sedan ordern.

När ordern har skapats förbereds enheten för leverans.



## <a name="cable"></a>Kabel 

Det här steget tar ungefär 10 minuter.

När du får Data Box gör du följande för att kabelansluta, ansluta och slå på enheten. Det här steget tar ungefär 10 minuter.

1. Fortsätt inte om det finns tecken på att enheten har manipulerats eller skadats. Kontakta Microsoft Support så att de kan skicka en ersättningsenhet.
2. Se till att du har följande kablar innan du kabelansluter enheten:
    
    - (Medföljer) jordad strömkabel graderad 10 A eller högre med en IEC60320 C-13-kontakt i ena änden av enheten.
    - En RJ-45 CAT 6-nätverkskabel (använd med MGMT-nätverksgränssnitt)
    - Två 10 GbE SFP+ Twinax-kopparkablar (använd med 10 Gbps DATA 1-, DATA 2-nätverksgränssnitt)
    - En RJ-45 CAT 6A- eller en RJ-45 CAT 6-nätverkskabel (använd med DATA 3-nätverksgränssnitt konfigurerat som 10 Gbps respektive 1 Gbps)

3. Ta bort och placera enheten på en plan yta. 
    
4. Kabelanslut enheten enligt nedan.  

    ![Bakplan på kabelansluten Data Box-enhet](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)  

    1. Anslut strömkabeln till enheten.
    2. Använd RJ-45 CAT 6-nätverkskabeln till att ansluta värddatorn till hanteringsporten (MGMT) på enheten. 
    3. Använd SFP+ Twinax-kopparkabeln till att ansluta minst ett 10 Gbps-nätverksgränssnitt (rekommenderas över 1 Gbps), DATA 1 eller DATA 2 för data. 
    4. Slå på enheten. Strömknappen finns på enhetens frontpanel.


## <a name="connect"></a>Anslut

Det här steget tar cirka 5–7 minuter att slutföra.

1. Hämta lösenordet för enheten på **Allmänt > Enhetsinformation** på [Azure-portalen](https://portal.azure.com).
2. Tilldela en statisk IP-adress, 192.168.100.5, och undernät 255.255.255.0 till Ethernet-adaptern på den dator du använder för att ansluta till Data Box. Öppna det lokala webbgränssnittet på enheten på `https://192.168.100.10`. Anslutningen kan upp till 5 minuter att slutföra efter att du slagit på enheten. 
3. Logga in med lösenordet från Azure-portalen. Ett felmeddelande visas som anger ett problem med webbplatsens säkerhetscertifikat. Följ de webbläsarspecifika instruktionerna för att fortsätta till webbsidan.
4. Som standard konfigureras nätverksinställningarna för 10 Gbps-datagränssnitt (eller 1 Gbps) som DHCP. Om det behövs kan du konfigurera det här nätverket som statiskt och ange en IP-adress. 

## <a name="copy-data"></a>Kopiera data

Hur lång tid det tar att slutföra den här åtgärden beror på datastorleken och nätverkshastigheten.
 
1. Om du använder en Windows-värd använder du ett SMB-kompatibelt filkopieringsverktyg, till exempel Robocopy. För en NFS-värd använder du kommandot `cp` eller `rsync` för att kopiera data. Anslut verktyget till din enhet och börja kopiera data till resurserna. Om du vill ha mer information om hur du använder Robocopy för att kopiera data går du till [Robocopy](https://technet.microsoft.com/library/ee851678.aspx).
2. Anslut till resurserna med hjälp av sökvägen `\\<IP address of your device>\ShareName`. Du kan hämta autentiseringsuppgifterna för åtkomst till resursen på sidan **Connect & copy** (Anslut och kopiera) i det lokala webbgränssnittet i Data Box.
3. Kontrollera att resursen och mappnamnen, och data följer riktlinjerna som beskrivs i [tjänstbegränsningarna för Azure Storage och Data Box](data-box-limits.md).

## <a name="ship-to-azure"></a>Skicka till Azure 

Åtgärden tar cirka 10–15 minuter att slutföra.

1. Gå till sidan **Prepare to ship** (Förbered för att skicka)i det lokala webbgränssnittet och börja förbereda frakten. 
2. Stäng av enheten via det lokala webbgränssnittet. Ta bort kablarna för enheten. 
3. Returetiketten ska vara synlig på E-ink-skärmen. Om etiketten inte visas på E-ink-skärmen laddar du ned adressetiketten från Azure-portalen och sätter in den i den genomskinliga fickan på enheten.
4. Lås höljet och skicka till Microsoft. 

## <a name="verify-data"></a>Verifiera data

Hur lång tid det tar att slutföra den här åtgärden beror på datastorleken.

1. När Data Box-enheten har anslutits till nätverket på Azures datacenter startar datauppladdningen till Azure automatiskt. 
2. Azure Data Box-tjänsten meddelar dig via Azure Portal när datakopieringen är klar. 

    1. Kontrollera felloggarna för eventuella fel och vidta lämpliga åtgärder.
    2. Kontrollera att alla data finns på lagringskontot innan du tar bort dem från källan.

## <a name="clean-up-resources"></a>Rensa resurser

Det här steget tar 2–3 minuter att slutföra.

- Du kan annullera Data Box-beställningen på Azure Portal innan orderbehandlingen har påbörjats. När orderbehandlingen har påbörjats kan du inte avbryta ordern. Ordern behandlas tills den når slutskedet i processen. Om du vill avbryta beställningen går du till **Översikt** och klickar på **Avbryt** i kommandofältet.

- Du kan ta bort ordern när statusen visas som **slutförd** eller **avbruten** på Azure Portal. Om du vill ta bort ordern går du till **Översikt** och klickar på **Ta bort** i kommandofältet.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat en Azure Data Box-enhet för att importera dina data till Azure. Gå vidare till nästa självstudie om du vill veta mer om Azure Data Box-hantering: 

> [!div class="nextstepaction"]
> [Administrera Data Box via Azure-portalen](data-box-portal-admin.md)

::: zone-end


