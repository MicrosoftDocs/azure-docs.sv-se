---
title: Självstudie för att beställa Azure Data Box Heavy | Microsoft Docs
description: I den här självstudien får du lära dig mer om Azure Data Box Heavy, en hybridlösning som gör att du kan importera lokala data till Azure. Du lär dig också hur du beställer Data Box Heavy.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 9b2b97f11c1493deca9b79907e894efbb7b9c456
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2020
ms.locfileid: "87921001"
---
# <a name="tutorial-order-azure-data-box-heavy"></a>Självstudier: Beställa Azure Data Box Heavy


Azure Data Box Heavy är en hybridmolnlösning som gör att du kan importera lokala data till Azure på ett snabbt, enkelt och tillförlitligt sätt. Du överför data till en lagringsenhet med 770 TB (ungefärlig användbar kapacitet) från Microsoft och skickar sedan tillbaka enheten. Dessa data överförs sedan till Azure.

I den här självstudien beskriver vi hur du kan beställa en Azure Data Box Heavy-enhet. I den här självstudien lär du dig:

> [!div class="checklist"]
> * Förhandskrav för Data Box Heavy
> * Beställa en Data Box Heavy
> * Spåra beställningen
> * Avbryta beställningen

## <a name="prerequisites"></a>Krav

Slutför följande konfigurationskrav för Data Box-tjänsten och enheten innan du distribuerar enheten.

### <a name="for-installation-site"></a>För installationsplatsen

Innan du börjar ska du kontrollera att:

- Enheten går in genom dörrar av standardstorlek. Kontrollera att enheten går in genom alla dörrar på installationsplatsen. Enhetens mått är: bredd: 65 cm längd: 120 cm höjd: 70 cm.
- Om den ska installeras på en annan våning än nedervåningen behöver du en hiss eller ramp. Enheten väger cirka 250 kg.
- Se till att du har tillgång till en plan yta i datacentret med närhet till en nätverksanslutning som kan hantera en enhet med det här fotavtrycket.

### <a name="for-service"></a>För tjänsten

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

### <a name="for-device"></a>För enheten

Innan du börjar ska du kontrollera att:
- Enheten är uppackad.
- Du bör ha en värddator som är ansluten till datacenternätverket. Data Box Heavy kommer att kopiera data från den här datorn. Värddatorn måste köra ett operativsystem som stöds enligt beskrivningen i [Systemkrav för Azure Data Box Heavy](data-box-system-requirements.md).
- Du måste ha en bärbar dator med RJ-45-kabel för att kunna ansluta till det lokala användargränssnittet och konfigurera enheten. Använd den bärbara datorn för att konfigurera varje nod av enheten en gång.
- Datacentret måste ha höghastighetsnätverk. Vi rekommenderar starkt att du har en anslutning på minst 10 GbE.
- Du behöver en 40 Gbit/s- eller 10 Gbit/s-kabel per enhetsnod. Välj kablar som är kompatibla med [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html)-nätverksgränssnittet:

    - För 40 Gbit/s-kabeln måste enhetens kabelände vara QSFP+.
    - För 10 Gbit/s-kabeln behöver du en SFP+-kabel som ansluts till en 10 G-växel i ena änden, med en QSFP+ till SFP+-adapter (eller QSA-adaptern) för änden som ansluts till enheten.
    - Strömkablarna medföljer enheten.

## <a name="order-data-box-heavy"></a>Beställa Data Box Heavy

Utför följande steg på Azure-portalen för att beställa en enhet.

1. Använd dina Microsoft Azure-autentiseringsuppgifter för att logga in på denna URL: [https://portal.azure.com](https://portal.azure.com).
2. Välj **+ Skapa en resurs** och sök efter *Azure Data Box*. Välj **Azure Data Box**.
    
   [![Söka efter Azure Data Box 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Välj **Skapa**.

4. Kontrollera om Data Box-tjänsten är tillgänglig i din region. Ange eller välj följande information och välj **Tillämpa**.

    |Inställning  |Värde  |
    |---------|---------|
    |Prenumeration     | Välj en prenumeration för EA, CSP eller Azure-sponsring för Data Box-tjänsten. <br> Prenumerationen är kopplad till ditt faktureringskonto.       |
    |Överföringstyp     | Välj **Importera till Azure**.        |
    |Källans land/region     | Välj landet/regionen där dina data finns.         |
    |Azure-målregion     | Välj den Azure-region dit du vill överföra data.        |

    [![Välj en tillgänglig Data Box-serie](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

5. Välj **Data Box Heavy**. Den högsta användbara kapaciteten för en enskild order är 770 TB.

    [![Välj Data Box Heavy](media/data-box-heavy-deploy-ordered/select-data-box-heavy.png)

6. I **Order** (Beställa) anger du **Order details** (Beställningsinformation). Ange eller välj följande information och välj **Nästa**.
    
    |Inställning  |Värde  |
    |---------|---------|
    |Namn     | Välj ett smeknamn så att du kan spåra beställningen. <br> Namnet kan innehålla mellan 3 och 24 tecken som kan vara bokstäver, siffror och bindestreck. <br> Namnet måste börja och sluta med en bokstav eller en siffra.      |
    |Resursgrupp     | Använd ett befintligt eller skapa ett nytt. <br> En resursgrupp är en logisk container för de resurser som kan hanteras eller distribueras tillsammans.         |
    |Azure-målregion     | Välj en region för lagringskontot. <br> Mer information finns i [regional tillgänglighet](https://azure.microsoft.com/global-infrastructure/services/?products=databox).        |
    |Lagringsmål     | Välj mellanlagringskonto eller hanterade diskar eller båda. <br> Baserat på den angivna Azure-regionen väljer du ett eller flera lagringskonton från den filtrerade listan med befintliga lagringskonton. <br>Data Box Heavy kan länkas med upp till 10 lagringskonton. <br> Du kan också skapa ett nytt konto för **Generell användning v1**, **Generell användning v2** eller **bloblagring**. <br>Mer information finns i [Lagringskonton som stöds med din enhet](data-box-heavy-system-requirements.md#supported-storage-accounts). <br>Lagringskonton med virtuella nätverk stöds. För att Data Box-tjänsten ska fungera med skyddade lagringskonton aktiverar du de betrodda tjänsterna i inställningarna för nätverksbrandväggen för lagringskontot. Mer information finns i [Lägga till Azure Data Box-tjänsten som en betrodd tjänst](../storage/common/storage-network-security.md#exceptions).|

    Om du använder lagringskontot som lagringsmål visas följande skärmbild:

    ![Data Box Heavy-order för lagringskonto](media/data-box-heavy-deploy-ordered/order-storage-account.png)

    Om du förutom att lägga till lagringskontot som lagringsmål använder Data Box Heavy för att skapa hanterade diskar från lokala virtuella hårddiskar, måste du ange följande information:

    |Inställning  |Värde  |
    |---------|---------|
    |Resursgrupper     | Skapa nya resursgrupper om du planerar att skapa hanterade diskar från lokala virtuella hårddiskar. Du kan bara använda en befintlig resursgrupp om resursgruppen skapades tidigare när du skapade en Data Box Heavy-beställning för hanterade diskar med Data Box-tjänsten. <br> Ange flera resursgrupper avgränsade med semikolon. Högst 10 resursgrupper stöds.|

    ![Data Box Heavy-beställning för hanterad disk](media/data-box-heavy-deploy-ordered/order-managed-disks.png)

    Det angivna lagringskontot för hanterade diskar används som ett mellanlagringskonto. Data Box-tjänsten laddar upp de virtuella hårddiskarna som sidblobar till mellanlagringskontot innan de konverteras till hanterade diskar och flyttas till resursgrupperna. Mer information finns i [Verifiera dataöverföring till Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. I **Leveransadress** uppger du för- och efternamn, företagets postadress och ett giltigt telefonnummer. Välj **Verifiera adress**. 

    Tjänsten verifierar leveransadressen och tjänstens tillgänglighet. Om tjänsten är tillgänglig för den angivna leveransadressen får du ett meddelande om det. Välj **Nästa**.

8. Ange e-postadresser i **Notification details** (Information om meddelande). Tjänsten skickar e-postmeddelanden om alla uppdateringar rörande orderstatus.

    Vi rekommenderar att du använder en grupp-e-postadress, så att du kan fortsätta att ta emot meddelanden även om en gruppadministratör lämnar företaget.

9. Granska informationen **Summary** (Sammanfattning) som rör beställningen, kontakt, meddelanden och sekretess. Markera rutan för avtalet till sekretesspolicyn.

10. Välj **Beställ**. Det tar några minuter att skapa beställningen.


## <a name="track-the-order"></a>Spåra beställningen

När du har skapat beställningen kan du spåra statusen för ordern via Azure-portalen. Öppna Data Box Heavy-beställningen och gå till **Översikt** för att visa statusen. Portalen visar beställningen i tillståndet **Ordered** (beställd).

Om enheten inte är tillgänglig får du ett meddelande. Om enheten är tillgänglig identifierar Microsoft enheten för leverans och förbereder försändelsen. Vid enhetsförberedelsen utförs följande åtgärder:

- SMB-resurser skapas för varje lagringskonto som är associerat med enheten.
- För varje resurs genereras autentiseringsuppgifter för åtkomst såsom användarnamn och lösenord.
- Det enhetslösenord som hjälper till att låsa upp enheten skapas också.
- Data Box Heavy låses för att förhindra obehörig åtkomst till enheten.

När enhetsförberedelserna är klara visar portalen beställningen i tillståndet **Processed** (Behandlad).

![Bearbetad Data Box Heavy-order](media/data-box-overview/data-box-order-status-processed.png)

Microsoft förbereder sedan enheten och skickar den via en regional transportör. Du får ett spårningsnummer när enheten har skickats. Portalen visar ordningen för statusen **Dispatched** (Skickad).

![Data Box Heavy-ordern har skickats](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>Avbryta beställningen

Om du vill avbryta beställningen öppnar du Azure-portalen, navigerar till **Översikt** och klickar på **Avbryt** i kommandofältet.

När du har gjort en beställning kan du avbryta den när som helst innan orderstatusen markeras som Processed (Behandlad).
 
Om du vill ta bort en avbruten beställning navigerar du till **Översikt** och klickar på **Ta bort** i kommandofältet.

## <a name="next-steps"></a>Nästa steg

I den här självstudien om Azure Data Box Heavy har du bland annat lärt dig att:

> [!div class="checklist"]
> * Krav
> * Beställa Data Box Heavy
> * Spåra beställningen
> * Avbryta beställningen

Gå vidare till nästa självstudie och lär dig hur du ställer in Data Box Heavy-disken.

> [!div class="nextstepaction"]
> [Konfigurera Azure Data Box Heavy](./data-box-heavy-deploy-set-up.md)
