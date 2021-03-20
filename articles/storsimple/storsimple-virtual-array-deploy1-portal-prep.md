---
title: Portalen prep för StorSimple Virtual Array
description: Den första självstudien för att distribuera StorSimple virtuell matris innebär att förbereda Azure Portal
author: alkohli
ms.assetid: 68a4cfd3-94c9-46cb-805c-46217290ce02
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c1a3d0b05f04ad8c182d428843479160666b96ee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87070328"
---
# <a name="deploy-storsimple-virtual-array---prepare-the-azure-portal"></a>Distribuera StorSimple Virtual Array – förbereda Azure Portal

![Diagram som visar de steg som behövs för att distribuera en virtuell matris. Det första steget är märkt kom igång och är markerat.](./media/storsimple-virtual-array-deploy1-portal-prep/getstarted4.png)


## <a name="overview"></a>Översikt

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Det här är den första artikeln i serien med distributions kurser som krävs för att fullständigt distribuera den virtuella matrisen som en fil server eller en iSCSI-server med hjälp av Resource Manager-modellen. Den här artikeln beskriver den förberedelse som krävs för att skapa och konfigurera din StorSimple-Enhetshanteraren tjänst innan en virtuell matris skapas. Den här artikeln länkar också till en check lista för distributions konfiguration och konfigurations krav.

Du måste ha administratörsbehörighet för att utföra installationen och konfigurationen. Vi rekommenderar att du läser igenom check listan för distributions konfigurationen innan du börjar. Portalförberedelserna tar mindre än tio minuter.

Informationen som publiceras i den här artikeln gäller distributionen av virtuella StorSimple-matriser i Azure Portal och Microsoft Azure Government molnet.

### <a name="get-started"></a>Kom igång
Arbets flödet för distribution består av att förbereda portalen, etablering av en virtuell matris i din virtualiserade miljö och slutföra installationen. För att komma igång med StorSimple-distributionen av virtuella matriser som en fil server eller en iSCSI-server, måste du referera till följande tabellen-resurser.

#### <a name="deployment-articles"></a>Distributions artiklar

Om du vill distribuera din virtuella StorSimple-matris läser du följande artiklar i den angivna ordningen.

| **#** | **I det här steget** | **Det gör du...** | **Och Använd dessa dokument.** |
| --- | --- | --- | --- |
| 1. |**Konfigurera Azure Portal** |Skapa och konfigurera din StorSimple-Enhetshanteraren tjänst innan du allokerar en StorSimple-virtuell matris. |[Förbered portalen](storsimple-virtual-array-deploy1-portal-prep.md) |
| 2. |**Etablera den virtuella matrisen** |För Hyper-V etablerar och ansluter du till en virtuell StorSimple-matris på ett värd system som kör Hyper-V på Windows Server 2012 R2, Windows Server 2012 eller Windows Server 2008 R2. <br></br> <br></br> För VMware, etablera och Anslut till en virtuell StorSimple-matris på ett värd system som kör VMware ESXi 5,0, 5,5, 6,0 eller 6,5.<br></br> |[Etablera en virtuell matris i Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) <br></br> <br></br> [Etablera en virtuell matris i VMware](storsimple-virtual-array-deploy2-provision-vmware.md) |
| 3. |**Konfigurera den virtuella matrisen** |För fil servern, utför inledande installation, registrera din StorSimple-filserver och slutför enhets konfigurationen. Sedan kan du etablera SMB-resurser. <br></br> <br></br> För iSCSI-servern utför du en inledande installation, registrerar din StorSimple iSCSI-server och slutför enhets konfigurationen. Du kan sedan etablera iSCSI-volymer. |[Konfigurera virtuell matris som fil Server](storsimple-virtual-array-deploy3-fs-setup.md)<br></br> <br></br>[Konfigurera virtuell matris som iSCSI-server](storsimple-virtual-array-deploy3-iscsi-setup.md) |

Nu kan du börja konfigurera Azure-portalen.

## <a name="configuration-checklist"></a>Konfigurations check lista

I konfigurations check listan beskrivs den information som du behöver samla in innan du konfigurerar program varan på din virtuella StorSimple-matris. Genom att förbereda den här informationen i förväg kan du effektivisera processen med att distribuera StorSimple-enheten i din miljö. Beroende på om din virtuella StorSimple-matris distribueras som en fil server eller en iSCSI-server, behöver du någon av följande check listor.

* Ladda ned [Check lista för StorSimple virtuell array File Server Configuration](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).
* Ladda ned [Check lista för konfiguration av iSCSI-StorSimple för virtuell matris](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## <a name="prerequisites"></a>Förutsättningar

Här hittar du nödvändiga konfigurations krav för din StorSimple Enhetshanteraren-tjänst, din StorSimple virtuella matris och data Center nätverket.

### <a name="for-the-storsimple-device-manager-service"></a>För StorSimple Device Manager-tjänsten

Innan du börjar ska du kontrollera att:

* Du har ditt Microsoft-konto med autentiseringsuppgifter.
* Du har ditt Microsoft Azure lagringskonto med autentiseringsuppgifter.
* Din Microsoft Azure prenumeration ska vara aktive rad för StorSimple Enhetshanteraren-tjänsten.

### <a name="for-the-storsimple-virtual-array"></a>För den virtuella StorSimple-matrisen

Innan du distribuerar en virtuell matris måste du kontrol lera att:

* Du har åtkomst till ett värd system som kör Hyper-V på Windows Server 2008 R2 eller senare eller VMware (ESXi 5,0, 5,5, 6,0 eller 6,5) som kan användas för att etablera en enhet.
* Värd systemet kan dedikera följande resurser för att etablera den virtuella matrisen:
  
  * Minst 4 kärnor.
  * Minst 8 GB RAM. Om du planerar att konfigurera den virtuella matrisen som fil Server stöder 8 GB 2 000 000 filer. Du behöver 16 GB RAM-minne för att kunna hantera 2-4 miljoner filer.
  * Ett nätverksgränssnitt.
  * En virtuell disk på 500 GB för system data.

### <a name="for-the-datacenter-network"></a>För datacenternätverket

Innan du börjar ska du kontrollera att:

* Nätverket i data centret konfigureras enligt nätverks kraven för din StorSimple-enhet. Mer information finns i [system krav för StorSimple Virtual Array](storsimple-ova-system-requirements.md).
* Din virtuella StorSimple-matris har en dedikerad 5 Mbit/s Internet bandbredd (eller mer) tillgänglig hela tiden. Den här bandbredden ska inte delas med andra program.

## <a name="step-by-step-preparation"></a>Steg för steg-förberedelse

Använd följande steg-för-steg-instruktioner för att förbereda portalen för tjänsten StorSimple Enhetshanteraren.

## <a name="step-1-create-a-new-service"></a>Steg 1: Skapa en ny tjänst

En enskild instans av tjänsten StorSimple Enhetshanteraren kan hantera flera StorSimple-virtuella matriser. Skapa en instans av StorSimple Device Manager-tjänsten genom att utföra stegen nedan. Om du har en befintlig StorSimple Enhetshanteraren-tjänst för att hantera dina virtuella matriser kan du hoppa över det här steget och gå till [steg 2: Hämta tjänst registrerings nyckeln](#step-2-get-the-service-registration-key).

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

> [!IMPORTANT]
> Om du inte har aktiverat automatiskt skapande av lagringskonton med din tjänst måste du skapa minst ett lagringskonto efter att du har skapat en tjänst.
> 
> * Om du inte har skapat ett lagringskonto automatiskt går du till [Konfigurera ett nytt lagringskonto för tjänsten](#optional-step-configure-a-new-storage-account-for-the-service) för detaljerade anvisningar.
> * Om du har aktiverat automatiskt skapande av ett lagringskonto går du till [steg 2: hämta nyckel för tjänstregistrering](#step-2-get-the-service-registration-key).
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>Steg 2: Hämta nyckel för tjänstregistrering

När StorSimple Device Manager-tjänsten är igång måste du hämta tjänstregistreringsnyckeln. Den här nyckeln används för att registrera och ansluta din StorSimple-enhet till tjänsten.

Utför följande steg på [Azure-portalen](https://portal.azure.com/).

[!INCLUDE [storsimple-virtual-array-get-service-registration-key](../../includes/storsimple-virtual-array-get-service-registration-key.md)]

> [!NOTE]
> Tjänst registrerings nyckeln används för att registrera alla StorSimple-Enhetshanteraren enheter som måste registreras med din StorSimple Enhetshanteraren-tjänst.
> 
> 

## <a name="step-3-download-the-virtual-array-image"></a>Steg 3: Ladda ned den virtuella mat ris avbildningen

När du har registrerings nyckeln för tjänsten måste du ladda ned lämplig avbildning av den virtuella matrisen för att etablera en virtuell matris på värd systemet. De virtuella mat ris avbildningarna är ett särskilt operativ system och kan hämtas från Snabbstart sidan i Azure Portal.

> [!IMPORTANT]
> Den program vara som körs på den virtuella StorSimple-matrisen kan bara användas med StorSimple Enhetshanteraren-tjänsten.
> 
> 

Utför följande steg på [Azure-portalen](https://portal.azure.com/).

#### <a name="to-get-the-virtual-array-image"></a>Så här hämtar du den virtuella mat ris avbildningen

1. Logga in på [Azure-portalen](https://portal.azure.com/). 
2. Klicka på **bläddra > StorSimple Device Managers** i Azure Portal.
3. Välj en befintlig StorSimple Enhetshanteraren-tjänst. Klicka på **Snabbstart** i bladet **StorSimple Enhetshanteraren** . 
4. Klicka på länken som motsvarar den avbildning som du vill ladda ned från Microsoft Download Center. Avbildningsfilerna är cirka 4,8 GB.
   
   * VHDX för Hyper-V på Windows Server 2012 och senare
   * VHD för Hyper-V på Windows Server 2008 R2 och senare
   * VMDK för VMWare ESXi 5,0, 5,5, 6,0 eller 6,5
5. Ladda ned och packa upp filen till en lokal enhet och skriv ned sökvägen till de uppackade filerna.

## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Valfritt steg: Konfigurera ett nytt lagrings konto för tjänsten

Det här steget är valfritt och ska endast utföras om du inte har aktiverat automatisk generering av ett lagrings konto med tjänsten.

Om du behöver skapa ett Azure Storage-konto i en annan region, se [så här skapar du ett lagrings konto](../storage/common/storage-account-create.md) för steg-för-steg-instruktioner.

Utför följande steg i [Azure Portal](https://ms.portal.azure.com/) på StorSimple Enhetshanteraren service-sidan för att lägga till ett befintligt Microsoft Azure lagrings konto.

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Så här lägger du till autentiseringsuppgifter för ett lagrings konto som har samma Azure-prenumeration som den Enhetshanteraren tjänsten

1. Navigera till Enhetshanteraren tjänsten, markera och dubbelklicka på den. **Översikts** bladet öppnas.
2. Välj **autentiseringsuppgifter för lagrings konto** i **konfigurations** avsnittet.
3. Klicka på **Lägg till**.
4. På bladet **Lägg till ett lagrings konto** gör du följande:
   
   1. För **prenumeration** väljer du **aktuell**.
   
   2. Ange namnet på ditt Azure Storage-konto.
   
   3. Välj **Aktivera** för att skapa en säker kanal för nätverkskommunikation mellan din StorSimple-enhet och molnet. Välj **inaktivera** endast om du arbetar i ett privat moln.
   
   4. Klicka på **Lägg till**. Du får ett meddelande när lagrings kontot har skapats.<br></br>
   
      ![Lägg till en befintlig autentiseringsuppgift för lagrings konto](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

## <a name="next-step"></a>Nästa steg

Nästa steg är att etablera en virtuell dator för din virtuella StorSimple-matris. Läs de detaljerade instruktionerna i följande avsnitt, beroende på ditt värdoperativsystem:

* [Etablera en virtuell StorSimple-matris i Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md)
* [Etablera en virtuell StorSimple-matris i VMware](storsimple-virtual-array-deploy2-provision-vmware.md)

