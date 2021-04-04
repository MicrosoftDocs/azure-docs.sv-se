---
title: Hantera StorSimple för lagrings konton för virtuella matris | Microsoft Docs
description: Förklarar hur du kan använda StorSimple-Enhetshanteraren Konfigurera sidan för att lägga till, redigera, ta bort eller rotera säkerhets nycklar för lagrings konto uppgifter som är associerade med den virtuella StorSimple-matrisen.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 234bf8bb-d5fe-40be-9d25-721d7482bc3b
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.openlocfilehash: 70b6231829173059ffee675105bacbe1bd1532f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023899"
---
# <a name="use-storsimple-device-manager-to-manage-storage-account-credentials-for-storsimple-virtual-array"></a>Använd StorSimple Enhetshanteraren för att hantera autentiseringsuppgifter för lagrings kontot för StorSimple virtuella matrisen

## <a name="overview"></a>Översikt
I **konfigurations** avsnittet på bladet StorSimple Enhetshanteraren tjänst för din StorSimple-virtuella matris visas de globala tjänst parametrarna som kan skapas i StorSimple Manager-tjänsten. Dessa parametrar kan tillämpas på alla enheter som är anslutna till tjänsten och inkluderar:

* Autentiseringsuppgifter för lagringskonto
* Åtkomst kontroll poster
  
  ![Instrument panel för Enhetshanterarens tjänsten](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccts-dashboard.png)  

I den här självstudien beskrivs hur du kan lägga till, redigera eller ta bort autentiseringsuppgifter för lagrings kontot för din virtuella StorSimple-matris. Informationen i den här själv studie kursen gäller endast för den virtuella StorSimple-matrisen. Information om hur du hanterar lagrings konton i 8000-serien finns i [använda tjänsten StorSimple Manager för att hantera ditt lagrings konto](./storsimple-8000-manage-storage-accounts.md).

Autentiseringsuppgifterna för lagrings kontot innehåller de autentiseringsuppgifter som enheten använder för att komma åt ditt lagrings konto hos din moln tjänst leverantör. För Microsoft Azure lagrings konton är dessa autentiseringsuppgifter, till exempel konto namnet och den primära åtkomst nyckeln.

På bladet med **autentiseringsuppgifter för lagrings kontot** visas alla lagrings konto uppgifter som skapas för fakturerings prenumerationen i ett tabell format med följande information:

* **Namn** – det unika namn som tilldelats kontot när det skapades.
* **SSL aktiverat** – huruvida TLS är aktiverat och kommunikation från enhet till molnet är över den säkra kanalen.
  
  ![Konfigurations avsnitt](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccountcredentials-blade.png)

De vanligaste uppgifterna som rör autentiseringsuppgifter för lagrings kontot som kan utföras på bladet med **autentiseringsuppgifter för lagrings kontot** är:

* Lägg till autentiseringsuppgift för lagringskonto
* Redigera autentiseringsuppgifter för ett lagrings konto
* Ta bort autentiseringsuppgifter för lagrings konto

## <a name="types-of-storage-account-credentials"></a>Typer av autentiseringsuppgifter för lagrings konto
Det finns tre typer av lagrings konto uppgifter som kan användas med din StorSimple-enhet.

* **Automatiskt genererade autentiseringsuppgifter för lagrings konto** – som namn antyder skapas den här typen av autentiseringsuppgifter för lagrings kontot automatiskt när tjänsten skapas. Mer information om hur det här lagrings kontots autentiseringsuppgift skapas finns i [skapa en ny tjänst](storsimple-virtual-array-manage-service.md#create-a-service).
* **autentiseringsuppgifter för lagrings konto i tjänst prenumerationen** – det här är de autentiseringsuppgifter för Azure Storage-kontot som är associerat med samma prenumeration som tjänsten. Mer information om hur dessa lagrings konto uppgifter skapas finns i [om Azure Storage-konton](../storage/common/storage-account-create.md).
* **autentiseringsuppgifterna för lagrings kontot utanför tjänst prenumerationen** – det här är de autentiseringsuppgifter för Azure Storage-kontot som inte är associerade med din tjänst och troligen fanns innan tjänsten skapades.

## <a name="add-a-storage-account-credential"></a>Lägg till autentiseringsuppgift för lagringskonto
Du kan lägga till en autentiseringsuppgift för lagrings kontot i StorSimple Enhetshanteraren tjänst konfigurationen genom att ange ett unikt namn och autentiseringsuppgifter för åtkomst som är länkade till lagrings kontot. Du kan också välja att aktivera Transport Layer Security (TLS)-läge, som tidigare kallades Secure Sockets Layer (SSL), för att skapa en säker kanal för nätverkskommunikation mellan enheten och molnet.

Du kan skapa flera konton för en specifik moln tjänst leverantör. När autentiseringsuppgiften för lagrings kontot sparas försöker tjänsten kommunicera med moln tjänst leverantören. De autentiseringsuppgifter och det åtkomst material som du har angett autentiseras just nu. Autentiseringsuppgifter för ett lagrings konto skapas endast om autentiseringen lyckas. Om autentiseringen Miss lyckas visas ett lämpligt fel meddelande.

Använd följande procedurer för att lägga till autentiseringsuppgifter för Azure Storage-kontot:

* Så här lägger du till autentiseringsuppgifter för ett lagrings konto som har samma Azure-prenumeration som den Enhetshanteraren tjänsten
* Så här lägger du till autentiseringsuppgifter för Azure Storage-kontot som ligger utanför Enhetshanteraren tjänst prenumerationen

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

#### <a name="to-add-an-azure-storage-account-credential-that-is-outside-of-the-device-manager-service-subscription"></a>Så här lägger du till autentiseringsuppgifter för Azure Storage-kontot som ligger utanför Enhetshanteraren tjänst prenumerationen

1. Navigera till Enhetshanteraren tjänsten, markera och dubbelklicka på den. **Översikts** bladet öppnas.
2. Välj **autentiseringsuppgifter för lagrings konto** i **konfigurations** avsnittet. Här visas alla befintliga autentiseringsuppgifter för lagrings kontot som är associerade med tjänsten StorSimple Enhetshanteraren.
3. Klicka på **Lägg till**.
4. På bladet **Lägg till ett lagrings konto** gör du följande:
   
    1. För **prenumeration** väljer du **annat**.
   
    2. Ange namnet på ditt Azure Storage-kontos autentiseringsuppgifter.
   
    3. I text rutan **lagrings kontots åtkomst nyckel** anger du den primära åtkomst nyckeln för ditt Azure Storage-kontos autentiseringsuppgifter. Om du vill hämta den här nyckeln går du till Azure Storage tjänsten, väljer ditt lagrings kontos autentiseringsuppgifter och klickar på **Hantera konto nycklar**. Nu kan du kopiera den primära åtkomst nyckeln.
   
    4. Aktivera TLS genom att klicka på knappen **Aktivera** för att skapa en säker kanal för nätverkskommunikation mellan StorSimple-Enhetshanteraren tjänsten och molnet. Klicka bara på knappen **inaktivera** om du arbetar i ett privat moln.
   
    5. Klicka på **Lägg till**. Du får ett meddelande när autentiseringsuppgifterna för lagrings kontot har skapats.

5. Den nyligen skapade autentiseringsuppgiften för lagrings kontot visas på bladet StorSimple konfigurera Enhetshanteraren tjänst under **autentiseringsuppgifter för lagrings kontot**.
   
    ![Lägg till en autentiseringsuppgift för lagrings konto utanför Enhetshanteraren tjänst prenumerationen](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-outside-storageacct.png)

## <a name="edit-a-storage-account-credential"></a>Redigera autentiseringsuppgifter för ett lagrings konto
Du kan redigera autentiseringsuppgifter för ett lagrings konto som används av din enhet. Om du redigerar autentiseringsuppgifter för ett lagrings konto som används för närvarande, är fälten som är tillgängliga för att ändra åtkomst nyckeln och TLS-läget för lagrings kontots autentiseringsuppgift. Du kan ange den nya lagrings åtkomst nyckeln eller ändra inställningen **Aktivera SSL-läge** och spara de uppdaterade inställningarna.

#### <a name="to-edit-a-storage-account-credential"></a>Redigera autentiseringsuppgifter för ett lagrings konto
1. Navigera till Enhetshanteraren tjänsten, markera och dubbelklicka på den. **Översikts** bladet öppnas.
2. Välj **autentiseringsuppgifter för lagrings konto** i **konfigurations** avsnittet. Här visas alla befintliga autentiseringsuppgifter för lagrings kontot som är associerade med tjänsten StorSimple Enhetshanteraren.
3. I Tabellista i tabell listan över autentiseringsuppgifter för lagrings konto väljer du och dubbelklickar på det konto som du vill ändra.
4. Gör följande i bladet **Egenskaper** för autentiseringsuppgifter för lagrings konto:
   
   1. Om det behövs kan du ändra valet **Aktivera SSL** -läge.
   2. Du kan välja att återskapa dina åtkomst nycklar för lagrings kontots autentiseringsuppgifter. Mer information finns i [Hantera åtkomst nycklar för lagrings konton](../storage/common/storage-account-keys-manage.md). Ange den nya nyckeln för lagrings kontots autentiseringsuppgift. Detta är den primära åtkomst nyckeln för ett Azure Storage-konto.
   3. Spara inställningarna genom att klicka på **Spara** överst på **egenskaps** bladet. Inställningarna uppdateras på bladet med **autentiseringsuppgifter för lagrings kontot** .
      
      ![Redigera autentiseringsuppgifter för ett lagrings konto](./media/storsimple-virtual-array-manage-storage-accounts/ova-edit-storageacct.png)

## <a name="delete-a-storage-account-credential"></a>Ta bort autentiseringsuppgifter för lagrings konto
> [!IMPORTANT]
> Du kan bara ta bort autentiseringsuppgifter för lagrings kontot om den inte används. Om autentiseringsuppgifter för ett lagrings konto används meddelas du.
> 
> 

#### <a name="to-delete-a-storage-account-credential"></a>Ta bort autentiseringsuppgifter för ett lagrings konto
1. Navigera till Enhetshanteraren tjänsten, markera och dubbelklicka på den. **Översikts** bladet öppnas.
2. Välj **autentiseringsuppgifter för lagrings konto** i **konfigurations** avsnittet. Här visas alla befintliga autentiseringsuppgifter för lagrings kontot som är associerade med tjänsten StorSimple Enhetshanteraren.
3. I tabellistan i Tabellista väljer du och dubbelklickar på det konto som du vill ta bort.
4. Gör följande i bladet **Egenskaper** för autentiseringsuppgifter för lagrings konto:
   
   1. Klicka på **ta bort** för att ta bort autentiseringsuppgifterna.
   2. När du uppmanas att bekräfta klickar du på **Ja** för att fortsätta med borttagningen. Tabell listan uppdateras för att återspegla ändringarna.
      
      ![Ta bort autentiseringsuppgifter för lagrings konto](./media/storsimple-virtual-array-manage-storage-accounts/ova-del-storageacct.png)

## <a name="synchronizing-storage-account-credential-keys"></a>Synkroniserar autentiseringsuppgifter för lagrings konto
Av säkerhets skäl är nyckel rotation ofta ett krav i Data Center. En Microsoft Azure administratör kan återskapa eller ändra den primära eller sekundära nyckeln genom att direkt komma åt autentiseringsuppgifterna för lagrings kontot (via tjänsten Microsoft Azure Storage). StorSimple-Enhetshanteraren tjänsten ser inte den här ändringen automatiskt.

Om du vill informera StorSimple Enhetshanteraren tjänsten om ändringen måste du komma åt StorSimple Enhetshanteraren-tjänsten, komma åt autentiseringsuppgifterna för lagrings kontot och sedan synkronisera den primära eller sekundära nyckeln (beroende på vilken som har ändrats). Tjänsten hämtar sedan den senaste nyckeln, krypterar nycklarna och skickar den krypterade nyckeln till enheten.

#### <a name="to-synchronize-keys-for-storage-account-credentials-in-the-same-subscription-as-the-service-azure-only"></a>Synkronisera nycklar för lagrings kontots autentiseringsuppgifter i samma prenumeration som tjänsten (endast Azure)
1. På bladet tjänst landning väljer du din tjänst, dubbelklickar på tjänstens namn och klickar sedan på **autentiseringsuppgifter för lagrings konto** i avsnittet **konfiguration** .
2. På bladet **autentiseringsuppgifter för lagrings konto** , i listan över autentiseringsuppgifter för lagrings konto, väljer du den autentiseringsuppgifter för lagrings konto vars nycklar du vill synkronisera.
3. Gör följande på bladet **Egenskaper** för den valda autentiseringsuppgiften för lagrings kontot:
   
    1. Klicka på **mer** och klicka sedan på **Synkronisera åtkomst nyckel**.
   
    2. När du uppmanas att bekräfta klickar du på **Sync-nyckel** för att slutföra synkroniseringen.
    
4. I StorSimple-Enhetshanteraren tjänsten måste du uppdatera den nyckel som tidigare har ändrats i Microsoft Azure Storage-tjänsten. I bladet **Synkronisera lagrings konto nyckel** , om den primära åtkomst nyckeln ändrades (regenererad), klickar du på primär och sedan på **Synkronisera nyckel**. Om den sekundära nyckeln har ändrats klickar du på **sekundär** och sedan på **Synkronisera nyckel**.
   
    ![Synkronisera åtkomst nyckel](./media/storsimple-virtual-array-manage-storage-accounts/ova-sync-access-key.png)

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [administrerar din virtuella StorSimple-matris](storsimple-ova-web-ui-admin.md).