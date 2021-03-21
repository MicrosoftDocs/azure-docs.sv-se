---
title: Hantera lagrings konto uppgifter, StorSimple 8000-serien het
description: Förklarar hur du kan använda StorSimple-Enhetshanteraren Konfigurera sidan för att lägga till, redigera, ta bort eller rotera säkerhets nycklar för ett lagrings konto.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 0df0c12d5942179e2492bfdc932fa25d126a2426
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96016886"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-storage-account-credentials"></a>Använd tjänsten StorSimple Enhetshanteraren för att hantera dina autentiseringsuppgifter för lagrings kontot

## <a name="overview"></a>Översikt

I **konfigurations** avsnittet i bladet StorSimple Enhetshanteraren tjänst visas alla globala tjänst parametrar som kan skapas i StorSimple-tjänsten för Enhetshanteraren. Dessa parametrar kan tillämpas på alla enheter som är anslutna till tjänsten och inkluderar:

* Autentiseringsuppgifter för lagringskonto
* Mallar för bandbredd 
* Åtkomst kontroll poster 

I den här självstudien beskrivs hur du lägger till, redigerar eller tar bort autentiseringsuppgifter för lagrings kontot eller roterar säkerhets nycklarna för ett lagrings konto.

 ![Lista över autentiseringsuppgifter för lagrings konto 1](./media/storsimple-8000-manage-storage-accounts/createnewstorageacct6.png)  

Lagrings konton innehåller de autentiseringsuppgifter som StorSimple-enheten använder för att komma åt ditt lagrings konto hos din moln tjänst leverantör. För Microsoft Azure lagrings konton är dessa autentiseringsuppgifter, till exempel konto namnet och den primära åtkomst nyckeln. 

På bladet **autentiseringsuppgifter för lagrings konto** visas alla lagrings konton som skapas för fakturerings prenumerationen i ett tabell format med följande information:

* **Namn** – det unika namn som tilldelats kontot när det skapades.
* **SSL aktiverat** – huruvida TLS är aktiverat och kommunikation från enhet till molnet är över den säkra kanalen.
* **Används av** – antalet volymer som använder lagrings kontot.

De vanligaste uppgifterna som rör lagrings konton som kan utföras är:

* Lägg till ett lagrings konto 
* Redigera ett lagrings konto 
* Ta bort ett lagringskonto 
* Nyckel rotation för lagrings konton 

## <a name="types-of-storage-accounts"></a>Typer av lagringskonton

Det finns tre typer av lagrings konton som kan användas med din StorSimple-enhet.

* **Automatiskt genererade lagrings konton** – som namnet antyder skapas den här typen av lagrings konto automatiskt när tjänsten skapas. Mer information om hur det här lagrings kontot skapas finns i [steg 1: skapa en ny tjänst](storsimple-8000-deployment-walkthrough-u2.md#step-1-create-a-new-service) i [distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md). 
* **Lagrings konton i tjänst prenumerationen** – dessa är de Azure Storage-konton som är associerade med samma prenumeration som tjänsten. Mer information om hur dessa lagrings konton skapas finns i [om Azure Storage-konton](../storage/common/storage-account-create.md). 
* **Lagrings konton utanför tjänst prenumerationen** – dessa är de Azure Storage-konton som inte är associerade med tjänsten och troligen fanns innan tjänsten skapades.

## <a name="add-a-storage-account"></a>Lägg till ett lagrings konto

Du kan lägga till ett lagrings konto genom att ange ett unikt namn och autentiseringsuppgifter för åtkomst som är länkade till lagrings kontot (med den angivna moln tjänst leverantören). Du kan också välja att aktivera Transport Layer Security (TLS)-läge, som tidigare kallades Secure Sockets Layer (SSL), för att skapa en säker kanal för nätverkskommunikation mellan enheten och molnet.

Du kan skapa flera konton för en specifik moln tjänst leverantör. Observera dock att du inte kan ändra moln tjänst leverantören när du har skapat ett lagrings konto.

När lagrings kontot sparas försöker tjänsten kommunicera med moln tjänst leverantören. De autentiseringsuppgifter och det åtkomst material som du tillhandahöll autentiseras just nu. Ett lagrings konto skapas endast om autentiseringen lyckas. Om autentiseringen Miss lyckas visas ett lämpligt fel meddelande.

Använd följande procedurer för att lägga till autentiseringsuppgifter för Azure Storage-kontot:

* Så här lägger du till autentiseringsuppgifter för ett lagrings konto som har samma Azure-prenumeration som den Enhetshanteraren tjänsten
* Så här lägger du till autentiseringsuppgifter för Azure Storage-kontot som ligger utanför Enhetshanteraren tjänst prenumerationen

[!INCLUDE [add-a-storage-account-update2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

#### <a name="to-add-an-azure-storage-account-credential-outside-of-the-storsimple-device-manager-service-subscription"></a>Så här lägger du till autentiseringsuppgifter för Azure Storage-kontot utanför StorSimple Enhetshanteraren service-prenumerationen

1. Navigera till StorSimple Enhetshanteraren-tjänsten, markera och dubbelklicka på den. **Översikts** bladet öppnas.
2. Välj **autentiseringsuppgifter för lagrings konto** i **konfigurations** avsnittet. Här visas alla befintliga autentiseringsuppgifter för lagrings kontot som är associerade med tjänsten StorSimple Enhetshanteraren.
3. Klicka på **Lägg till**.
4. Gör följande på bladet **Lägg till autentiseringsuppgift för lagrings konto** :
   
    1. För **prenumeration** väljer du **annat**.
   
    2. Ange namnet på ditt Azure Storage-kontos autentiseringsuppgifter.
   
    3. I text rutan **lagrings kontots åtkomst nyckel** anger du den primära åtkomst nyckeln för ditt Azure Storage-kontos autentiseringsuppgifter. Om du vill hämta den här nyckeln går du till Azure Storage tjänsten, väljer ditt lagrings kontos autentiseringsuppgifter och klickar på **Hantera konto nycklar**. Nu kan du kopiera den primära åtkomst nyckeln.
   
    4. Aktivera TLS genom att klicka på knappen **Aktivera** för att skapa en säker kanal för nätverkskommunikation mellan StorSimple-Enhetshanteraren tjänsten och molnet. Klicka bara på knappen **inaktivera** om du arbetar i ett privat moln.
   
    5. Klicka på **Lägg till**. Du får ett meddelande när autentiseringsuppgifterna för lagrings kontot har skapats.

5. Den nyligen skapade autentiseringsuppgiften för lagrings kontot visas på bladet StorSimple konfigurera Enhetshanteraren tjänst under **autentiseringsuppgifter för lagrings kontot**.
   


## <a name="edit-a-storage-account"></a>Redigera ett lagrings konto

Du kan redigera ett lagrings konto som används av en volym behållare. Om du redigerar ett lagrings konto som används för närvarande är det enda fältet som är tillgängligt för att ändra åtkomst nyckeln för lagrings kontot. Du kan ange den nya lagrings åtkomst nyckeln och spara de uppdaterade inställningarna.

#### <a name="to-edit-a-storage-account"></a>Redigera ett lagrings konto

1. Gå till StorSimple Device Manager-tjänsten. Klicka på **Autentiseringsuppgifter för lagringskonto** i avsnittet **Konfiguration**.

    ![Autentiseringsuppgifter för lagrings konto 2](./media/storsimple-8000-manage-storage-accounts/editstorageacct1.png)

2. I bladet med **autentiseringsuppgifter för lagrings kontot** , i listan över autentiseringsuppgifter för lagrings konto, väljer du och klickar på det du vill redigera. 

3. Du kan ändra inställningen **Aktivera SSL** . Du kan också klicka på **fler...** och sedan välja **Synkronisera åtkomst nyckel för att rotera** dina åtkomst nycklar för lagrings kontot. Gå till [nyckel rotationen för lagrings konton](#key-rotation-of-storage-accounts) för mer information om hur du utför nyckel rotation. När du har ändrat inställningarna klickar du på **Spara**. 

    ![Spara redigerade autentiseringsuppgifter för lagrings kontot](./media/storsimple-8000-manage-storage-accounts/editstorageacct3.png)

4. Klicka på **Ja** när du uppmanas att bekräfta åtgärden. 

    ![Bekräfta ändringar](./media/storsimple-8000-manage-storage-accounts/editstorageacct4.png)

Inställningarna kommer att uppdateras och sparas för ditt lagrings konto. 

## <a name="delete-a-storage-account"></a>Ta bort ett lagringskonto

> [!IMPORTANT]
> Du kan bara ta bort ett lagrings konto om det inte används av en volym behållare. Om ett lagrings konto används av en volym behållare tar du först bort volym behållaren och tar sedan bort det associerade lagrings kontot.

#### <a name="to-delete-a-storage-account"></a>Ta bort ett lagrings konto

1. Gå till StorSimple Device Manager-tjänsten. Klicka på **Autentiseringsuppgifter för lagringskonto** i avsnittet **Konfiguration**.

2. Hovra över det konto som du vill ta bort i tabell listan över lagrings konton. Högerklicka för att anropa snabb menyn och klicka på **ta bort**.

    ![Ta bort autentiseringsuppgifter för lagrings konto](./media/storsimple-8000-manage-storage-accounts/deletestorageacct1.png)

3. När du uppmanas att bekräfta klickar du på **Ja** för att fortsätta med borttagningen. Tabell listan kommer att uppdateras för att avspegla ändringarna.

    ![Bekräfta borttagning](./media/storsimple-8000-manage-storage-accounts/deletestorageacct2.png)

## <a name="key-rotation-of-storage-accounts"></a>Nyckel rotation för lagrings konton

Av säkerhets skäl är nyckel rotation ofta ett krav i Data Center. Varje Microsoft Azure prenumeration kan ha ett eller flera associerade lagrings konton. Åtkomsten till dessa konton styrs av prenumerations-och åtkomst nycklarna för varje lagrings konto. 

När du skapar ett lagrings konto genererar Microsoft Azure 2 512-bitars lagrings åtkomst nycklar som används för autentisering när lagrings kontot nås. Med två lagrings åtkomst nycklar kan du återskapa nycklarna utan avbrott i lagrings tjänsten eller åtkomst till tjänsten. Den nyckel som används för närvarande är *primär* nyckel och säkerhets kopierings nyckeln kallas *sekundär* nyckel. En av följande två nycklar måste anges när din Microsoft Azure StorSimple-enhet ansluter till din moln lagrings tjänst leverantör.

## <a name="what-is-key-rotation"></a>Vad är nyckel rotation?

Normalt använder program bara en av nycklarna för att komma åt dina data. Efter en viss tids period kan du låta dina program växla över till att använda den andra nyckeln. När du har bytt program till den sekundära nyckeln kan du dra tillbaka den första nyckeln och sedan generera en ny nyckel. Genom att använda de två nycklarna på det här sättet kan dina program få åtkomst till data utan att det uppstår några drift avbrott.

Lagrings konto nycklarna lagras alltid i-tjänsten i ett krypterat format. De kan dock återställas via tjänsten StorSimple Enhetshanteraren. Tjänsten kan hämta primär nyckel och sekundär nyckel för alla lagrings konton i samma prenumeration, inklusive konton som skapats i lagrings tjänsten samt de standard lagrings konton som genereras när StorSimple Enhetshanteraren-tjänsten först skapades. StorSimple-Enhetshanteraren tjänsten får alltid dessa nycklar från den klassiska Azure-portalen och lagrar dem sedan på ett krypterat sätt.

## <a name="rotation-workflow"></a>Rotations arbets flöde

En Microsoft Azure administratör kan återskapa eller ändra den primära eller sekundära nyckeln genom att direkt komma åt lagrings kontot (via tjänsten Microsoft Azure Storage). StorSimple-Enhetshanteraren tjänsten ser inte den här ändringen automatiskt.

För att meddela StorSimple-Enhetshanteraren tjänstens ändring, behöver du åtkomst till StorSimple Enhetshanteraren-tjänsten, åtkomst till lagrings kontot och synkroniserar sedan den primära eller sekundära nyckeln (beroende på vilken som har ändrats). Tjänsten hämtar sedan den senaste nyckeln, krypterar nycklarna och skickar den krypterade nyckeln till enheten.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service"></a>Synkronisera nycklar för lagrings konton i samma prenumeration som tjänsten 
1. Gå till StorSimple Device Manager-tjänsten. Klicka på **Autentiseringsuppgifter för lagringskonto** i avsnittet **Konfiguration**.
2. I listan över lagrings konton klickar du på det du vill ändra. 

    ![Synkronisera nycklar](./media/storsimple-8000-manage-storage-accounts/syncaccesskey1.png)

3. Klicka på **... Mer** och välj sedan **Synkronisera åtkomst nyckel för att rotera**.   

    ![Synkronisera nycklar 2](./media/storsimple-8000-manage-storage-accounts/syncaccesskey2.png)

4. I StorSimple-Enhetshanteraren tjänsten måste du uppdatera den nyckel som tidigare har ändrats i Microsoft Azure Storage-tjänsten. Om den primära åtkomst nyckeln ändrades (återskapades) väljer du **primär** nyckel. Om den sekundära nyckeln ändrades väljer du **sekundär** nyckel. Klicka på **Synkronisera nyckel**.
      
      ![Synkronisera nycklar 3](./media/storsimple-8000-manage-storage-accounts/syncaccesskey3.png)

Du får ett meddelande när nyckeln har synkroniserats.

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>Synkronisera nycklar för lagrings konton utanför tjänst prenumerationen
1. På sidan **tjänster** klickar du på fliken **Konfigurera** .
2. Klicka på **Lägg till/redigera lagrings konton**.
3. Gör följande i dialog rutan:
   
   1. Välj det lagrings konto som har den åtkomst nyckel som du vill uppdatera.
   2. Du måste uppdatera lagrings åtkomst nyckeln i StorSimple Enhetshanteraren-tjänsten. I det här fallet kan du se lagrings åtkomst nyckeln. Ange den nya nyckeln i rutan **lagrings kontots åtkomst nyckel** . 
   3. Spara ändringarna. Din åtkomst nyckel för lagrings kontot bör nu uppdateras.

## <a name="next-steps"></a>Nästa steg
* Läs mer om [StorSimple-säkerhet](storsimple-8000-security.md).
* Lär dig mer om [att använda tjänsten StorSimple Enhetshanteraren för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).