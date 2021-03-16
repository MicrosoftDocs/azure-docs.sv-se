---
title: Migrations guide för Azure Stack Edge Pro-FPGA till fysisk GPU-enhet
description: Den här guiden innehåller anvisningar för att migrera arbets belastningar från en Azure Stack Edge Pro FPGA-enhet till en Azure Stack Edge Pro GPU-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/11/2021
ms.author: alkohli
ms.openlocfilehash: 24d6528a105d593d1cb4c9c66d981c8787f85633
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103573292"
---
# <a name="migrate-workloads-from-an-azure-stack-edge-pro-fpga-to-an-azure-stack-edge-pro-gpu"></a>Migrera arbets belastningar från en Azure Stack Edge Pro-FPGA till en Azure Stack Edge Pro GPU

Den här artikeln beskriver hur du migrerar arbets belastningar och data från en Azure Stack Edge Pro FPGA-enhet till en Azure Stack Edge Pro GPU-enhet. Migreringsprocessen börjar med en jämförelse av de två enheterna, en migrations plan och en granskning av migrerings överväganden. Migreringsprocessen innehåller detaljerade steg som avslutar verifieringen och rensningen av enheten.

[!INCLUDE [Azure Stack Edge Pro FPGA end-of-life](../../includes/azure-stack-edge-fpga-eol.md)]

## <a name="about-migration"></a>Om migrering

Migrering är en process för att flytta arbets belastningar och program data från en lagrings plats till en annan. Detta gör att du kan skapa en exakt kopia av en organisations aktuella data från en lagrings enhet till en annan lagrings enhet – helst utan att störa eller inaktivera aktiva program, och sedan dirigera om alla aktiviteter för indata/utdata (I/O) till den nya enheten. 

Den här migreringsguiden innehåller en stegvis genom gång av de steg som krävs för att migrera data från en Azure Stack Edge Pro FPGA-enhet till en Azure Stack Edge Pro GPU-enhet. Det här dokumentet är avsett för IT-proffs och kunskaps arbetare som ansvarar för drift, distribution och hantering av Azure Stack gräns enheter i data centret.

I den här artikeln kallas den Azure Stack Edge Pro FPGA-enheten som *käll* enheten och Azure Stack Edge Pro GPU-enhet är *mål* enheten. 

## <a name="comparison-summary"></a>Jämförelse Sammanfattning

Det här avsnittet innehåller en jämför ande Sammanfattning av funktionerna mellan Azure Stack Edge Pro GPU jämfört med Azure Stack Edge Pro FPGA-enheter. Maskin varan i både käll-och mål enheten är i stort sett identisk. Det är bara maskin varu accelerations kortet och lagrings kapaciteten som kan skilja sig.<!--Please verify: These components MAY, but need not necessarily, differ?-->

|    Funktion  | Azure Stack Edge Pro GPU (mål enhet)  | Azure Stack Edge Pro-FPGA (käll enhet)|
|----------------|-----------------------|------------------------|
| Maskinvara       | Maskin varu acceleration: 1 eller 2 NVIDIA T4-GPU: er <br> Beräknings-, minnes-, nätverks gränssnitt, strömförsörjnings enhet och ström sladds specifikationer är identiska med enheten med FPGA.  | Maskin varu acceleration: Intel Arria 10 FPGA <br> Beräknings-, minnes-, nätverks gränssnitt, strömförsörjnings enhet och ström sladds specifikationer är identiska med enheten med GPU.          |
| Användbar lagring | 4,19 TB <br> Efter reserverat utrymme för paritets återhämtning och intern användning | 12,5 TB <br> Efter reservering av utrymme för intern användning |
| Säkerhet       | Certifikat |                                                     |
| Arbetsbelastningar      | IoT Edge arbets belastningar <br> Arbetsbelastningar för virtuella datorer <br> Kubernetes-arbetsbelastningar| IoT Edge arbets belastningar |
| Prissättning        | [Prissättning](https://azure.microsoft.com/pricing/details/azure-stack/edge/) | [Prissättning](https://azure.microsoft.com/pricing/details/azure-stack/edge/)|

## <a name="migration-plan"></a>Migrations plan

Tänk på följande om du vill skapa en migrerings plan:

- Utveckla ett schema för migrering. 
- När du migrerar data kan det uppstå avbrott. Vi rekommenderar att du schemalägger migrering under ett underhålls fönster för drift stopp när processen är störande. Du kommer att konfigurera och återställa konfigurationer i denna nedtid enligt beskrivningen senare i det här dokumentet.
- Förstå den totala längden på nedtid och kommunicera med alla intressenter.
- Identifiera de lokala data som behöver migreras från käll enheten. Som en försiktighets åtgärd bör du se till att alla data i den befintliga lagringen har en aktuell säkerhets kopia. 


## <a name="migration-considerations"></a>Överväganden vid migrering 

Överväg följande information innan du fortsätter med migreringen: 

- Det går inte att aktivera en GPU-enhet för Azure Stack Edge Pro mot en Azure Stack Edge Pro FPGA-resurs. Du bör skapa en ny resurs för Azure Stack Edge Pro GPU-enhet enligt beskrivningen i [skapa en Azure Stack Edge Pro GPU-ordning](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).
- Machine Learning modeller som distribuerats på käll enheten som använde FPGA måste ändras för mål enheten med GPU. Om du behöver hjälp med modellerna kan du kontakta Microsoft Support. Anpassade modeller som distribuerats på käll enheten som inte använde FPGA (endast Använd processor) bör fungera som de är på mål enheten (med hjälp av CPU).
- De IoT Edge moduler som distribueras på käll enheten kan kräva ändringar innan modulerna kan distribueras på mål enheten. 
- Käll enheten har stöd för NFS 3,0-och 4,1-protokoll. Mål enheten stöder bara NFS 3,0-protokollet.
- Käll enheten stöder SMB-och NFS-protokoll. Mål enheten stöder lagring via REST-protokollet med hjälp av lagrings konton utöver SMB-och NFS-protokollen för resurser.
- Resurs åtkomsten på käll enheten är via IP-adressen medan resurs åtkomsten på mål enheten är via enhets namnet.

## <a name="migration-steps-at-a-glance"></a>Stegvisa migreringar

I den här tabellen sammanfattas det övergripande flödet för migrering, som beskriver de steg som krävs för migrering och var de här stegen ska utföras.

| I den här fasen | Gör det här steget| På den här enheten |
|---------------|-------------|----------------|
| Förbered käll enhet *       | 1. registrera konfigurations data <br>2. Säkerhetskopiera delnings data <br>3. Förbered IoT Edge arbets belastningar| Käll enhet  |
| Förbered mål enhet *       |1. skapa en ny order <br>2. Konfigurera och aktivera| Mål enhet  |
| Migrera data       | 1. Migrera data från resurser <br>2. distribuera om IoT Edge-arbetsbelastningar| Mål enhet  |
| Verifiera data            |Verifiera migrerade data |Mål enhet  |
| Rensa, returnera              |Radera data och returnera| Käll enhet  |

**Käll-och mål enheterna kan förberedas parallellt.*

## <a name="prepare-source-device"></a>Förbered käll enhet

Förberedelsen omfattar att du identifierar gräns moln resurser, Edge-lokala resurser och de IoT Edge moduler som distribuerats på enheten. 

### <a name="1-record-configuration-data"></a>1. registrera konfigurations data

Utför de här stegen på käll enheten via det lokala användar gränssnittet.

Registrera konfigurations data på *käll* enheten. Använd [distributions check listan](azure-stack-edge-gpu-deploy-checklist.md) för att hjälpa dig att registrera enhets konfigurationen. Under migreringen använder du den här konfigurations informationen för att konfigurera den nya mål enheten. 

### <a name="2-back-up-share-data"></a>2. Säkerhetskopiera delnings data

Enhets data kan vara av någon av följande typer:

- Data i gräns moln resurser
- Data i lokala resurser

#### <a name="data-in-edge-cloud-shares"></a>Data i gräns moln resurser

Gräns moln resurs nivå data från din enhet till Azure. Utför de här stegen på *käll* enheten via Azure Portal. 

- Skapa en lista över alla gräns moln resurser och användare som du har på käll enheten.
- Skapa en lista över alla bandbredds scheman som du har. Du kommer att återskapa dessa bandbredds scheman på mål enheten.
- Beroende på tillgänglig nätverks bandbredd konfigurerar du bandbredds scheman på enheten för att maximera data skiktet i molnet. Det minimerar lokala data på enheten.
- Se till att resurserna är helt på nivå av molnet. Du kan bekräfta nivån genom att kontrol lera resursens status i Azure Portal.  

#### <a name="data-in-edge-local-shares"></a>Data i lokala Edge-resurser

Data i Edge lokala resurser ligger kvar på enheten. Utför de här stegen på *käll* enheten via Azure Portal. 

- Gör en lista över de Edge-lokala resurserna på enheten.
- Eftersom du kommer att utföra migreringen av data i taget, skapar du en kopia av de lokala data resurserna till en annan lokal server. Du kan använda kopierings verktyg som `robocopy` (SMB) eller `rsync` (NFS) för att kopiera data. Alternativt kanske du redan har distribuerat en data skydds lösning från tredje part för att säkerhetskopiera data i dina lokala resurser. Följande lösningar från tredje part stöds för användning med Azure Stack Edge Pro FPGA-enheter:

    | Tredjepartsprogram           | Referens till lösningen                               |
    |--------------------------------|---------------------------------------------------------|
    | Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Kontakta Cohesity om du vill ha mer information.          |
    | CommVault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Kontakta CommVault om du vill ha mer information.          |
    | Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Kontakta Veritas om du vill ha mer information.   |
    | Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Kontakta Veeam om du vill ha mer information. |


### <a name="3-prepare-iot-edge-workloads"></a>3. Förbered IoT Edge arbets belastningar

- Om du har distribuerat IoT Edge moduler och använder FPGA-acceleration kan du behöva ändra modulerna innan de körs på GPU-enheten. Följ instruktionerna i [modifiera IoT Edge-moduler](azure-stack-edge-gpu-modify-fpga-modules-gpu.md). 

<!--- If you have deployed IoT Edge workloads, the configuration data is shared on a share on the device. Back up the data in these shares.-->


## <a name="prepare-target-device"></a>Förbered mål enhet

### <a name="1-create-new-order"></a>1. skapa en ny order

Du måste skapa en ny order (och en ny resurs) för *mål* enheten. Mål enheten måste aktive ras mot GPU-resursen och inte mot FPGA-resursen.

Om du vill placera en order [skapar du en ny Azure Stack Edge-resurs](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource) i Azure Portal.


### <a name="2-set-up-activate"></a>2. Konfigurera, aktivera

Du måste konfigurera och aktivera *mål* enheten mot den nya resurs som du skapade tidigare. 

Följ de här stegen för att konfigurera *mål* enheten via Azure Portal:

1. Samla in den information som krävs i [Check listan för distribution](azure-stack-edge-gpu-deploy-checklist.md). Du kan använda den information som du sparade från käll enhets konfigurationen. 
1. [Packa upp](azure-stack-edge-gpu-deploy-install.md#unpack-the-device), [rack montering](azure-stack-edge-gpu-deploy-install.md#rack-the-device) och [kabel enheten](azure-stack-edge-gpu-deploy-install.md#cable-the-device). 
1. [Anslut till enhetens lokala användar gränssnitt](azure-stack-edge-gpu-deploy-connect.md).
1. Konfigurera nätverket med en annan uppsättning IP-adresser (om du använder statiska IP-adresser) än de som du använde för din gamla enhet. Se [Konfigurera nätverks inställningar](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
1. Tilldela samma enhets namn som din gamla enhet och ange en DNS-domän. Se [Konfigurera enhets inställningar](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).
1. Konfigurera certifikat på den nya enheten. Se så här [konfigurerar du certifikat](azure-stack-edge-gpu-deploy-configure-certificates.md).
1. Hämta aktiverings nyckeln från Azure Portal och aktivera den nya enheten. Se hur du [aktiverar enheten](azure-stack-edge-gpu-deploy-activate.md).

Du är nu redo att återställa delnings data och distribuera de arbets belastningar som du körde på den gamla enheten.

## <a name="migrate-data"></a>Migrera data

Du kommer nu att kopiera data från käll enheten till gräns moln resurser och lokala resurser på *mål* enheten.

### <a name="1-from-edge-cloud-shares"></a>1. från gräns moln resurser

Följ dessa steg för att synkronisera data på gräns moln resurser på mål enheten:

1. [Lägg till resurser](azure-stack-edge-j-series-manage-shares.md#add-a-share) som motsvarar resurs namnen som skapats på käll enheten. När du skapar resurserna ser du till att **Välj BLOB-behållare** är inställt på **Använd befintlig**, och väljer sedan den behållare som användes med den tidigare enheten.
1. [Lägg till användare](azure-stack-edge-j-series-manage-users.md#add-a-user) som hade åtkomst till den tidigare enheten.
1. [Uppdatera dela](azure-stack-edge-j-series-manage-shares.md#refresh-shares) data från Azure. Om du uppdaterar resursen hämtas alla moln data från den befintliga behållaren till resurserna.
1. Återskapa de bandbredds scheman som ska associeras med dina resurser. Se [Lägg till ett bandbredds schema](azure-stack-edge-j-series-manage-bandwidth-schedules.md#add-a-schedule) för detaljerade steg.


### <a name="2-from-edge-local-shares"></a>2. från lokala Edge-resurser

Du kan ha distribuerat en lösning för säkerhets kopiering från tredje part för att skydda lokala resurs data för dina IoT-arbetsbelastningar. Du kommer nu att behöva återställa dessa data.

När ersättnings enheten är helt konfigurerad aktiverar du enheten för lokal lagring. 

Följ dessa steg om du vill återställa data från lokala resurser:

1. [Konfigurera beräkning på enheten](azure-stack-edge-gpu-deploy-configure-compute.md).
1. Lägg till alla lokala resurser på mål enheten. Se de detaljerade stegen i [Lägg till en lokal resurs](azure-stack-edge-gpu-manage-shares.md#add-a-local-share).
1. Att komma åt SMB-resurserna på käll enheten använder IP-adresserna på mål enheten, du använder enhets namnet. Se [ansluta till en SMB-resurs på Azure Stack Edge Pro GPU](azure-stack-edge-j-series-deploy-add-shares.md#connect-to-an-smb-share). Om du vill ansluta till NFS-resurser på mål enheten måste du använda de nya IP-adresserna som är kopplade till enheten. Se [ansluta till en NFS-resurs på Azure Stack Edge Pro GPU](azure-stack-edge-j-series-deploy-add-shares.md#connect-to-an-nfs-share). 

    Om du har kopierat dina delnings data till en mellanliggande server över SMB eller NFS, kan du kopiera data från den mellanliggande servern till resurser på mål enheten. Om både käll-och mål enheten är *online* kan du också kopiera data direkt från käll enheten.

    Om du har använt program vara från tredje part för att säkerhetskopiera data i de lokala resurserna måste du köra återställnings proceduren som tillhandahålls av den data skydds lösning som du väljer. Se referenser i följande tabell.

    | Tredjepartsprogram           | Referens till lösningen                               |
    |--------------------------------|---------------------------------------------------------|
    | Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Kontakta Cohesity om du vill ha mer information.          |
    | CommVault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Kontakta CommVault om du vill ha mer information. |
    | Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Kontakta Veritas om du vill ha mer information.   |
    | Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Kontakta Veeam om du vill ha mer information. |

### <a name="3-redeploy-iot-edge-workloads"></a>3. distribuera om IoT Edge-arbetsbelastningar

När IoT Edge modulerna har för beretts måste du distribuera IoT Edge arbets belastningar på mål enheten. Om du möter eventuella fel i distributionen IoT Edge-moduler, se:

- [Vanliga problem och lösningar för Azure IoT Edge](../iot-edge/troubleshoot-common-errors.md). 
- [IoT Edge körnings fel](azure-stack-edge-gpu-troubleshoot.md#troubleshoot-iot-edge-errors).

## <a name="verify-data"></a>Verifiera data

Efter migreringen kontrollerar du att alla data har migrerats och att arbets belastningarna har distribuerats på mål enheten.

## <a name="erase-data-return"></a>Radera data, returnera

När datamigreringen är klar tar du bort lokala data och returnerar käll enheten. Följ stegen i [returnera din Azure Stack Edge Pro-enhet](azure-stack-edge-return-device.md).


## <a name="next-steps"></a>Nästa steg

[Lär dig hur du distribuerar IoT Edge-arbetsbelastningar på Azure Stack Edge Pro GPU-enhet](azure-stack-edge-gpu-deploy-compute-module-simple.md)
