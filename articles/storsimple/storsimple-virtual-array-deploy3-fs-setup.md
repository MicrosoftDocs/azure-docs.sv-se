---
title: Konfigurera virtuell StorSimple-matris som fil Server | Microsoft Docs
description: Den här tredje självstudien om distribution av virtuella StorSimple-matriser gör att du kan konfigurera en virtuell enhet som fil server.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: f609f6ff-0927-48bb-a68a-6d8985d2fe34
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 244fdbf7cb723fe85e0987d176a13242f0bff064
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005936"
---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server-via-azure-portal"></a>Distribuera virtuell StorSimple-matris – Ställ in som fil server via Azure Portal
![Diagram som visar de steg som krävs för att distribuera en virtuell matris. Den första delen av det tredje steget är märkt konfigurerad som fil server och är markerad.](./media/storsimple-virtual-array-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Introduktion

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Den här artikeln beskriver hur du utför den första installationen, registrerar StorSimple-filservern, slutför enhets konfigurationen och skapar och ansluter till SMB-resurser. Det här är den sista artikeln i serien med distributions kurser som krävs för att fullständigt distribuera den virtuella matrisen som en fil server eller en iSCSI-server.

Installations-och konfigurations processen kan ta cirka 10 minuter att slutföra. Informationen i den här artikeln gäller endast distributionen av den virtuella StorSimple-matrisen. För distributionen av StorSimple 8000-serie enheter går du till: [distribuera din StorSimple 8000-serie enhet som kör uppdatering 2](./storsimple-8000-deployment-walkthrough-u2.md).

## <a name="setup-prerequisites"></a>Nödvändiga nödvändiga komponenter
Innan du konfigurerar och konfigurerar din virtuella StorSimple-matris måste du kontrol lera att:

* Du har etablerat en virtuell matris och anslutit till den enligt beskrivningen i [etablera en virtuell StorSimple-matris i Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) eller [etablera en StorSimple virtuell matris i VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Du har tjänst registrerings nyckeln från StorSimple Enhetshanteraren-tjänsten som du skapade för att hantera StorSimple-virtuella matriser. Mer information finns i [steg 2: Hämta tjänst registrerings nyckeln](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) för StorSimple Virtual Array.
* Om det här är den andra eller efterföljande virtuella matris som du registrerar med en befintlig StorSimple-Enhetshanteraren tjänst bör du ha krypterings nyckeln för tjänst data. Den här nyckeln genererades när den första enheten har registrerats med den här tjänsten. Om du har förlorat den här nyckeln läser du [Hämta krypterings nyckeln för tjänst data](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) för din virtuella StorSimple-matris.

## <a name="step-by-step-setup"></a>Steg för steg-konfiguration
Använd följande steg-för-steg-instruktioner för att konfigurera och konfigurera din virtuella StorSimple-matris.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Steg 1: Slutför installations programmet för lokalt webb gränssnitt och registrera din enhet
#### <a name="to-complete-the-setup-and-register-the-device"></a>För att slutföra installationen och registrera enheten
1. Öppna ett webbläsarfönster och Anslut till det lokala webb gränssnittet. Ange:
   
   `https://<ip-address of network interface>`
   
   Använd anslutnings-URL: en som anges i föregående steg. Du ser ett fel som indikerar att det är problem med webbplatsens säkerhetscertifikat. Klicka på **Fortsätt till den här webb sidan**.
   
   ![Skärm bild av ett webbläsarfönster som rapporterar ett säkerhets certifikat problem. Två länkar visas, en för webbplatsen och en för användarens hem sida.](./media/storsimple-virtual-array-deploy3-fs-setup/image2.png)
2. Logga in på webb gränssnittet för den virtuella matrisen som **StorSimpleAdmin**. Ange det lösen ord för enhets administratör som du ändrade i steg 3: starta den virtuella matrisen i [etablera en virtuell StorSimple-matris i Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) eller i [etablera en StorSimple virtuell matris i VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
   ![Skärm bild av inloggnings sidan för StorSimple. Användar namnet StorSimpleAdmin visas och ett lösen ord fylls med obestämda tecken.](./media/storsimple-virtual-array-deploy3-fs-setup/image3.png)
3. Du kommer till **Start** sidan. Den här sidan beskriver de olika inställningar som krävs för att konfigurera och registrera den virtuella matrisen med tjänsten StorSimple Enhetshanteraren. **Nätverks inställningarna**, **webbproxy-inställningarna** och **tids inställningarna** är valfria. De enda nödvändiga inställningarna är **enhets inställningar** och **moln inställningar**.
   
   ![Skärm bild av start sidan. Text anger att enheten inte har kon figurer ATS. Länkar till flera olika typer av inställningar visas.](./media/storsimple-virtual-array-deploy3-fs-setup/image4.png)
4. På sidan **nätverks inställningar** under **nätverks gränssnitt** kommer data 0 att konfigureras automatiskt åt dig. Varje nätverks gränssnitt anges som standard för att hämta IP-adresser automatiskt (DHCP). Därför tilldelas en IP-adress, ett undernät och en gateway automatiskt (för både IPv4 och IPv6).
   
   ![Skärm bild av sidan Nätverks inställningar som visar de IP-adresser som är konfigurerade för olika versioner av Internet Protocol.](./media/storsimple-virtual-array-deploy3-fs-setup/image5.png)
   
   Om du har lagt till fler än ett nätverks gränssnitt under etableringen av enheten kan du konfigurera dem här. Obs! Du kan bara konfigurera nätverks gränssnittet som IPv4 eller både IPv4 och IPv6. Endast IPv6-konfigurationer stöds.
5. DNS-servrar krävs eftersom de används när enheten försöker kommunicera med moln lagrings tjänst leverantörer eller för att matcha din enhet efter namn när den konfigureras som en fil server. På sidan **nätverks inställningar** under **DNS-servrarna**:
   
   1. En primär och en sekundär DNS-server konfigureras automatiskt. Om du väljer att konfigurera statiska IP-adresser kan du ange DNS-servrar. För hög tillgänglighet rekommenderar vi att du konfigurerar en primär och en sekundär DNS-server.
   2. Klicka på **tillämpa** för att tillämpa och verifiera nätverks inställningarna.
6. På sidan **enhets inställningar** :
   
   1. Tilldela enheten ett unikt **namn** . Namnet kan innehålla 1-15 tecken och får innehålla bokstäver, siffror och bindestreck.
   2. Klicka på ikonen **fil Server** :::image type="icon" source="./media/storsimple-virtual-array-deploy3-fs-setup/image6.png"::: för den **typ** av enhet som du skapar. Med en fil Server kan du skapa delade mappar.
   3. Eftersom enheten är en fil server måste du ansluta enheten till en domän. Ange ett **domän namn**.
   4. Klicka på **Applicera**.
7. En dialog ruta visas. Ange autentiseringsuppgifterna för domänen i det angivna formatet. Klicka på kryss ikonen. Domänautentiseringsuppgifter verifieras. Ett fel meddelande visas om autentiseringsuppgifterna är felaktiga.
   
   ![Skärm bild som visar en dialog ruta med användar namn och lösen ord ifyllt.](./media/storsimple-virtual-array-deploy3-fs-setup/image7.png)
8. Klicka på **Applicera**. Detta gäller och validerar enhets inställningarna.
   
   ![Skärm bild av sidan enhets inställningar. Enhets namnet och domän namnet fylls i.](./media/storsimple-virtual-array-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > Se till att den virtuella matrisen finns i en egen organisationsenhet (OU) för Active Directory och att inga grup princip objekt (GPO) tillämpas på den eller ärvs. Grup princip kan installera program som antivirus program på den virtuella StorSimple-matrisen. Det finns inte stöd för att installera ytterligare program vara och det kan leda till skadade data. 
   > 
   > 
9. (Valfritt) Konfigurera webbproxyservern. Även om webbproxykonfigurationen är valfri, var medveten om att om du använder en webbproxy så kan du bara konfigurera den här.
   
   ![Skärm bild av sidan Web Proxy-inställningar. Alternativet Aktivera webbproxy är inaktiverat och autentiseringen är inställd på ingen. Inga andra värden har angetts.](./media/storsimple-virtual-array-deploy3-fs-setup/image9.png)
   
   På **webbproxy-** sidan:
   
   1. Ange **webbproxy-URL** i det här formatet: *http:// &lt; host-IP Address eller FQDN &gt; :P ort nummer*. Observera att HTTPS-URL: er inte stöds.
   2. Ange **autentisering** som **Basic** eller **none**.
   3. Om du använder autentisering måste du också ange ett **användar namn** och **lösen ord**.
   4. Klicka på **Applicera**. Detta validerar och tillämpar de konfigurerade webbproxyinställningarna.
10. (Valfritt) Konfigurera tid inställningarna för enheten, till exempel tidszon och primära och sekundära NTP-servrar. NTP-servrar krävs eftersom din enhet måste synkronisera tid så att den kan autentiseras med dina moln tjänst leverantörer.
    
    ![Skärm bild av sidan med tids inställningar. Tids zonen och den primära N T P-servern fylls i. Den sekundära N T T P-servern är tom.](./media/storsimple-virtual-array-deploy3-fs-setup/image10.png)
    
    På sidan **tids inställningar** :
    
    1. I list rutan väljer du **tidszon baserat på** den geografiska plats där enheten distribueras. Standard tids zonen för enheten är PST. Enheten använder den här tidszonen för alla schemalagda åtgärder.
    2. Ange en **primär NTP-server** för din enhet eller acceptera standardvärdet för Time.Windows.com. Kontrollera att ditt nätverk tillåter att NTP-trafik skickas från ditt datacenter till Internet.
    3. Alternativt kan du ange en **sekundär NTP-server** för enheten.
    4. Klicka på **Applicera**. Detta validerar och tillämpar de konfigurerade tids inställningarna.
11. Konfigurera moln inställningarna för enheten. I det här steget ska du slutföra konfigurationen av den lokala enheten och sedan registrera enheten med StorSimple Enhetshanteraren-tjänsten.
    
    1. Ange **tjänst registrerings nyckeln** som du fick i [steg 2: Hämta tjänst registrerings nyckeln](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) för StorSimple Virtual Array.
    2. Om det här är din första enhet som du registrerar med tjänsten visas **krypterings nyckeln för tjänst data**. Kopiera den här nyckeln och spara den på säker plats. Den här nyckeln krävs med tjänst registrerings nyckeln för att registrera ytterligare enheter med StorSimple Enhetshanteraren-tjänsten. 
       
       Om detta inte är den första enheten som du registrerar med tjänsten måste du ange krypterings nyckeln för tjänst data. Mer information finns i Hämta [krypterings nyckeln för tjänst data](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) i det lokala webb gränssnittet.
    3. Klicka på **Registrera**. Då startas enheten om. Du kan behöva vänta i 2-3 minuter innan enheten har registrerats. När enheten har startats om kommer du till inloggnings sidan.
       
       ![Skärm bild av sidan med moln inställningar. Rutorna registrerings nyckel och krypterings nyckel fylls i, men värdena fördelas.](./media/storsimple-virtual-array-deploy3-fs-setup/image13.png)
12. Gå tillbaka till Azure-portalen. Gå till **alla resurser**, Sök efter din StorSimple Enhetshanteraren-tjänst.
    
    ![Skärm bild av sidan alla resurser i Azure Portal. En Enhetshanteraren tjänst är markerad.](./media/storsimple-virtual-array-deploy3-fs-setup/searchdevicemanagerservice1.png) 
13. I den filtrerade listan väljer du din StorSimple-Enhetshanteraren tjänst och navigera sedan till **hanterings > enheter**. På bladet **enheter** kontrollerar du att enheten har lyckats ansluta till tjänsten och att statusen **redo att konfigureras**.
    
    ![Distribuera](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png)

## <a name="step-2-configure-the-device-as-file-server"></a>Steg 2: Konfigurera enheten som fil Server
Utför följande steg i [Azure Portal](https://portal.azure.com/) för att slutföra den nödvändiga enhets konfigurationen.

#### <a name="to-configure-the-device-as-file-server"></a>Så här konfigurerar du enheten som fil Server
1. Gå till StorSimple Enhetshanteraren-tjänsten och gå sedan till  **hanterings > enheter**. På bladet **enheter** väljer du den enhet som du nyss skapade. Den här enheten skulle visas som **klar att konfigurera**.
   
   ![Konfigurera en fil Server](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png) 
2. Klicka på enheten så visas ett informations meddelande som anger att enheten är redo att konfigureras.
   
    ![Konfigurera en fil Server 2](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs3m.png)
3. Klicka på **Konfigurera** i kommando fältet. Då öppnas bladet **Konfigurera** . Gör följande på bladet **Konfigurera** :
   
   1. Fil Server namnet fylls i automatiskt.
    
   2. Kontrol lera att moln lagrings kryptering är **aktiverat**. Då krypteras alla data som skickas till molnet. 
    
   3. En 256-bitars AES-nyckel används med den användardefinierade nyckeln för kryptering. Ange en bokstav på 32 och ange sedan nyckeln för att bekräfta den. Registrera nyckeln i en nyckel hanterings app för framtida bruk.
    
   4. Klicka på **Konfigurera nödvändiga inställningar** för att ange de autentiseringsuppgifter för lagrings konto som ska användas med din enhet. Klicka på **Lägg till ny** om inga autentiseringsuppgifter för lagrings kontot har kon figurer ATS. **Se till att det lagrings konto som du använder stöder block-blobbar. Page blobbar stöds inte.** Mer information om [block-blobar och Page blobbar](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).
   
      ![Konfigurera en fil Server 3](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs6m.png) 
4. Gör följande på bladet **Lägg till autentiseringsuppgifter för lagrings konto** : 

    1. Välj aktuell prenumeration om lagrings kontot finns i samma prenumeration som tjänsten. Ange andra är lagrings kontot utanför tjänst prenumerationen. 
    
    2. Välj ett befintligt lagrings konto i list rutan. 
    
    3. Platsen fylls i automatiskt baserat på det angivna lagrings kontot. 
    
    4. Aktivera TLS för att säkerställa en säker kanal för nätverks kommunikation mellan enheten och molnet.
    
    5. Klicka på **Lägg** till för att lägga till autentiseringsuppgifterna för lagrings kontot. 
   
        ![Konfigurera en fil Server 4](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs8m.png)

5. När lagrings kontots autentiseringsuppgift har skapats, kommer **Konfigurera** -bladet att uppdateras för att visa de angivna autentiseringsuppgifterna för lagrings kontot. Klicka på **Konfigurera**.
   
   ![Konfigurera en fil Server 5](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs11m.png)
   
   En fil server som skapas visas. När fil servern har skapats får du ett meddelande.
   
   ![Konfigurera en fil Server 5b](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs13m.png)
   
   Enhetens status ändras också till **online**.
   
   ![Konfigurera en fil Server 5c](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs14m.png)
   
   Du kan fortsätta att lägga till en resurs.

## <a name="step-3-add-a-share"></a>Steg 3: Lägg till en resurs
Skapa en resurs genom att utföra stegen nedan på [Azure-portalen](https://portal.azure.com/).

#### <a name="to-create-a-share"></a>Skapa en resurs
1. Välj den fil server enhet som du konfigurerade i föregående steg och klicka på **...** (eller högerklicka). I snabb menyn väljer du **Lägg till resurs**. Du kan också klicka på **+ Lägg till resurs** i enhetens kommando fält.
   
   ![Lägga till en resurs](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs15m.png)
2. Ange följande resurs inställningar:

   1. Ett unikt namn för din resurs. Namnet måste vara en sträng som innehåller mellan 3 och 127 tecken.
    
   2. En valfri **Beskrivning** av resursen. Beskrivningen hjälper till att identifiera resurs ägare.
    
   3. En **typ** för resursen. Typen kan vara på **nivå** av eller **lokalt fäst**, med nivån standard. För arbets belastningar som kräver lokala garantier, låg fördröjning och högre prestanda väljer du en **lokalt fäst** resurs. För alla andra data väljer du en **nivå** resurs.
      En lokalt fäst resurs är tjockt etablerad och säkerställer att de primära data på resursen förblir lokala för enheten och inte spiller i molnet. En nivå resurs på den andra handen är tunt etablerad. När du skapar en nivå resurs har 10% av utrymmet tillhandahållits på den lokala nivån och 90% av utrymmet är etablerad i molnet. Om du till exempel har allokerat en 1 TB-volym skulle 100 GB finnas i det lokala utrymmet och 900 GB används i molnet när data nivåerna. Detta innebär i sin tur att om du tar bort allt lokalt utrymme på enheten kan du inte etablera en nivå resurs.
   
   4. I fältet **ange fullständiga standard behörigheter till** tilldelar du behörigheterna till användaren eller gruppen som har åtkomst till den här resursen. Ange namnet på användaren eller användar gruppen i *john \@ contoso.com* -format. Vi rekommenderar att du använder en användar grupp (i stället för en enskild användare) för att tillåta administratörs behörighet att få åtkomst till dessa resurser. När du har tilldelat behörigheterna här kan du sedan använda Utforskaren till att ändra dessa behörigheter.
   
   5. Klicka på **Lägg till** för att skapa resursen. 
    
       ![Lägg till en resurs 1](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs18m.png)
   
       Du får ett meddelande om att resursen skapas.
   
       ![Lägg till en resurs 2](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs19m.png)
   
      När resursen har skapats med de angivna inställningarna kommer bladet **resurser** att uppdateras för att avspegla den nya resursen. Som standard aktive ras övervakning och säkerhets kopiering för resursen.
   
      ![Lägg till en resurs 3](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs22m.png)

## <a name="step-4-connect-to-the-share"></a>Steg 4: Anslut till resursen
Du måste nu ansluta till en eller flera resurser som du skapade i föregående steg. Utför de här stegen på Windows Server-värden som är ansluten till din virtuella StorSimple-matris.

#### <a name="to-connect-to-the-share"></a>Så här ansluter du till resursen
1. Tryck på :::image type="icon" source="./media/storsimple-virtual-array-deploy3-fs-setup/image22.png"::: + R. I fönstret kör anger du *&lt; namnet &gt; på&#92;&#92;fil servern* som sökväg, ersätter *fil Server namnet* med det enhets namn som du har tilldelat till fil servern. Klicka på **OK**.
   
   ![Skärm bild av dialog rutan Kör. Rutan Öppna fylls i med en sökväg som leder till fil servern.](./media/storsimple-virtual-array-deploy3-fs-setup/image23.png)
2. Då öppnas Utforskaren. Du bör nu kunna se de resurser du har skapat som mappar. Välj och dubbelklicka på en resurs (mapp) för att visa innehållet.
   
   ![Skärm bild av Utforskaren. Flera mappar visas som representerar de nyligen skapade resurserna.](./media/storsimple-virtual-array-deploy3-fs-setup/image24.png)
3. Nu kan du lägga till filer till dessa resurser och göra en säkerhets kopia.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du använder det lokala webb gränssnittet för att [administrera din virtuella StorSimple-matris](storsimple-ova-web-ui-admin.md).