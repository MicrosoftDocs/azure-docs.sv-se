---
title: Konfiguration av iSCSI-Server Microsoft Azure StorSimple virtuell matris | Microsoft Docs
description: Beskriver hur du utför den första installationen, registrerar din StorSimple iSCSI-server och slutför enhets konfigurationen.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 4db116d1-978b-48e8-b572-a719a8425dbc
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 6289f335234c9b7efec02a18e12b40a1140c1b3f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94956559"
---
# <a name="deploy-storsimple-virtual-array--set-up-as-an-iscsi-server-via-azure-portal"></a>Distribuera virtuell StorSimple-matris – konfigureras som en iSCSI-server via Azure Portal

![process flöde för iSCSI-installation](./media/storsimple-virtual-array-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Översikt

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Den här distributions guiden gäller för den Microsoft Azure StorSimple virtuella matrisen. I den här självstudien beskrivs hur du utför den första installationen, registrerar din StorSimple iSCSI-server, slutför enhets konfigurationen och sedan skapar, monterar, initierar och formaterar volymer på din StorSimple virtuella matris som kon figurer ATS som en iSCSI-server. 

De procedurer som beskrivs här tar cirka 30 minuter till 1 timme att slutföra. Informationen som publiceras i den här artikeln gäller endast virtuella StorSimple-matriser.

## <a name="setup-prerequisites"></a>Nödvändiga nödvändiga komponenter

Innan du konfigurerar och konfigurerar din virtuella StorSimple-matris måste du kontrol lera att:

* Du har etablerat en virtuell matris och anslutit till den enligt beskrivningen i [distribuera StorSimple Virtual Array-etablera en virtuell matris i Hyper-V](./storsimple-virtual-array-deploy2-provision-hyperv.md) eller [distribuera StorSimple Virtual Array-etablera en virtuell matris i VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Du har tjänst registrerings nyckeln från StorSimple Enhetshanteraren-tjänsten som du skapade för att hantera dina StorSimple virtuella matriser. Mer information finns i **steg 2: Hämta tjänst registrerings nyckeln** i [distribuera StorSimple Virtual Array – förbereda portalen](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
* Om det här är den andra eller efterföljande virtuella matris som du registrerar med en befintlig StorSimple-Enhetshanteraren tjänst bör du ha krypterings nyckeln för tjänst data. Den här nyckeln genererades när den första enheten har registrerats med den här tjänsten. Om du har förlorat den här nyckeln läser du **Hämta krypterings nyckeln för tjänst data** i [Använd webb gränssnittet för att administrera din virtuella StorSimple-matris](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## <a name="step-by-step-setup"></a>Steg för steg-konfiguration

Använd följande steg-för-steg-instruktioner för att konfigurera och konfigurera din virtuella StorSimple-matris:

* [Steg 1: Slutför installations programmet för lokalt webb gränssnitt och registrera din enhet](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* Steg 2: Slutför den nödvändiga enhets konfigurationen
* [Steg 3: Lägg till en volym](#step-3-add-a-volume)
* [Steg 4: montera, initiera och formatera en volym](#step-4-mount-initialize-and-format-a-volume)

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Steg 1: Slutför installations programmet för lokalt webb gränssnitt och registrera din enhet

#### <a name="to-complete-the-setup-and-register-the-device"></a>För att slutföra installationen och registrera enheten

1. Öppna ett webbläsarfönster. Så här ansluter du till webb GRÄNSSNITTs typen:
   
    `https://<ip-address of network interface>`
   
    Använd anslutnings-URL: en som anges i föregående steg. Ett fel meddelande visas om att det är problem med webbplatsens säkerhetscertifikat. Klicka på **Fortsätt till den här webb sidan**.
   
    ![säkerhets certifikat fel](./media/storsimple-virtual-array-deploy3-iscsi-setup/image3.png)
2. Logga in på webb gränssnittet för den virtuella enheten som **StorSimpleAdmin**. Ange det lösen ord för enhets administratör som du ändrade i steg 3: starta den virtuella enheten i [distribuera StorSimple virtuell matris – etablera en virtuell enhet i Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) eller [distribuera StorSimple Virtual Array-etablera en virtuell enhet i VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
    ![Inloggnings sida](./media/storsimple-virtual-array-deploy3-iscsi-setup/image4.png)
3. Du kommer till **Start** sidan. Den här sidan beskriver de olika inställningar som krävs för att konfigurera och registrera den virtuella enheten med tjänsten StorSimple Enhetshanteraren. Observera att **nätverks inställningarna**, **webbproxy-inställningarna** och **tids inställningarna** är valfria. De enda nödvändiga inställningarna är **enhets inställningar** och **moln inställningar**.
   
    ![Startsida](./media/storsimple-virtual-array-deploy3-iscsi-setup/image5.png)
4. På sidan **nätverks inställningar** under **nätverks gränssnitt** kommer data 0 att konfigureras automatiskt åt dig. Varje nätverks gränssnitt anges som standard för att hämta en IP-adress automatiskt (DHCP). Därför tilldelas en IP-adress, ett undernät och en gateway automatiskt (för både IPv4 och IPv6).
   
    När du planerar att distribuera din enhet som en iSCSI-server (för att etablera block lagring) rekommenderar vi att du inaktiverar alternativet **Hämta IP-adress automatiskt** och konfigurerar statiska IP-adresser.
   
    ![Sidan nätverks inställningar](./media/storsimple-virtual-array-deploy3-iscsi-setup/image6.png)
   
    Om du har lagt till fler än ett nätverks gränssnitt under etableringen av enheten kan du konfigurera dem här. Obs! Du kan bara konfigurera nätverks gränssnittet som IPv4 eller både IPv4 och IPv6. Endast IPv6-konfigurationer stöds.
5. DNS-servrar krävs eftersom de används när enheten försöker kommunicera med moln lagrings tjänst leverantörer eller för att lösa din enhet efter namn om den är konfigurerad som en fil server. På sidan **nätverks inställningar** under **DNS-servrarna**:
   
   1. En primär och en sekundär DNS-server kommer att konfigureras automatiskt. Om du väljer att konfigurera statiska IP-adresser kan du ange DNS-servrar. För hög tillgänglighet rekommenderar vi att du konfigurerar en primär och en sekundär DNS-server.
   2. Klicka på **Applicera**. Nätverks inställningarna tillämpas och kontrol leras.
6. På sidan **enhets inställningar** :
   
   1. Tilldela enheten ett unikt **namn** . Namnet kan innehålla 1-15 tecken och får innehålla bokstäver, siffror och bindestreck.
   2. Klicka på ikonen iSCSI-server ikon för **iSCSI-server** ![ ](./media/storsimple-virtual-array-deploy3-iscsi-setup/image7.png) för den **typ** av enhet som du skapar. Med en iSCSI-server kan du etablera block lagring.
   3. Ange om du vill att den här enheten ska vara domänansluten. Om enheten är en iSCSI-server är det valfritt att ansluta till domänen. Om du inte vill ansluta din iSCSI-server till en domän klickar du på **tillämpa**, väntar på att inställningarna ska tillämpas och går sedan vidare till nästa steg.
      
       Om du vill ansluta enheten till en domän. Ange ett **domän namn** och klicka sedan på **Använd**.
      
      > [!NOTE]
      > Om du ansluter din iSCSI-server till en domän måste du se till att den virtuella matrisen finns i en egen organisationsenhet (OU) för Microsoft Azure Active Directory och inga grup princip objekt (GPO) tillämpas på den.
      > 
      > 
   4. En dialog ruta visas. Ange autentiseringsuppgifterna för domänen i det angivna formatet. Klicka på kryssikonen ![kryssikon](./media/storsimple-virtual-array-deploy3-iscsi-setup/image15.png). Domänautentiseringsuppgifter kommer att verifieras. Ett fel meddelande visas om autentiseringsuppgifterna är felaktiga.
      
       ![autentiseringsuppgifter](./media/storsimple-virtual-array-deploy3-iscsi-setup/image8.png)
   5. Klicka på **Applicera**. Detta gäller och validerar enhets inställningarna.
7. (Valfritt) Konfigurera webbproxyservern. Även om webbproxykonfigurationen är valfri, var medveten om att om du använder en webbproxy så kan du bara konfigurera den här.
   
    ![Konfigurera webbproxy](./media/storsimple-virtual-array-deploy3-iscsi-setup/image9.png)
   
    På **webbproxy-** sidan:
   
   1. Ange **webbproxy-URL** i följande format: *http: \/ /host-IP-adress* eller *fullständigt domän namn: port nummer*. Observera att HTTPS-URL: er inte stöds.
   2. Ange **autentisering** som **Basic** eller **none**.
   3. Om du använder autentisering måste du också ange ett **användar namn** och **lösen ord**.
   4. Klicka på **Applicera**. Detta validerar och tillämpar de konfigurerade webbproxyinställningarna.
8. (Valfritt) Konfigurera tid inställningarna för enheten, till exempel tidszon och primära och sekundära NTP-servrar. NTP-servrar krävs eftersom din enhet måste synkronisera tid så att den kan autentiseras med dina moln tjänst leverantörer.
   
    ![Tids inställningar](./media/storsimple-virtual-array-deploy3-iscsi-setup/image10.png)
   
    På sidan **tids inställningar** :
   
   1. I list rutan väljer du **tidszon baserat på den geografiska** plats där enheten distribueras. Standard tids zonen för enheten är PST. Enheten använder den här tidszonen för alla schemalagda åtgärder.
   2. Ange en **primär NTP-server** för din enhet eller acceptera standardvärdet för Time.Windows.com. Kontrollera att ditt nätverk tillåter att NTP-trafik skickas från ditt datacenter till Internet.
   3. Alternativt kan du ange en **sekundär NTP-server** för enheten.
   4. Klicka på **Applicera**. Detta validerar och tillämpar de konfigurerade tids inställningarna.
9. Konfigurera moln inställningarna för enheten. I det här steget ska du slutföra konfigurationen av den lokala enheten och sedan registrera enheten med StorSimple Enhetshanteraren-tjänsten.
   
   1. Ange **tjänst registrerings nyckeln** som du fick i **steg 2: Hämta tjänst registrerings nyckeln** i [distribuera StorSimple Virtual Array – Förbered portalen](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
   2. Om detta inte är den första enheten som du registrerar med tjänsten måste du ange **krypterings nyckeln för tjänst data**. Den här nyckeln krävs med tjänst registrerings nyckeln för att registrera ytterligare enheter med StorSimple Enhetshanteraren-tjänsten. Mer information finns i [Hämta krypterings nyckeln för tjänst data](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) i det lokala webb gränssnittet.
   3. Klicka på **Registrera**. Då startas enheten om. Du kan behöva vänta i 2-3 minuter innan enheten har registrerats. När enheten har startats om kommer du till inloggnings sidan.
      
      ![Registrera enhet](./media/storsimple-virtual-array-deploy3-iscsi-setup/image11.png)
10. Gå tillbaka till Azure-portalen.
11. Gå till bladet **enheter** i din tjänst. Om du har många resurser klickar du på **alla resurser**, klickar på tjänst namnet (Sök efter det om det behövs) och klickar sedan på **enheter**.
12. På bladet **enheter** kontrollerar du att enheten har lyckats ansluta till tjänsten genom att leta upp statusen. Enhetens status bör vara **Redo för installation**.
    
    ![Distribuera enhet](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png)

## <a name="step-2-configure-the-device-as-iscsi-server"></a>Steg 2: Konfigurera enheten som iSCSI-server

Utför följande steg i Azure Portal för att slutföra den nödvändiga enhets konfigurationen.

#### <a name="to-configure-the-device-as-iscsi-server"></a>Så här konfigurerar du enheten som iSCSI-server

1. Gå till StorSimple Enhetshanteraren-tjänsten och gå sedan till **hanterings > enheter**. På bladet **enheter** väljer du den enhet som du nyss skapade. Den här enheten skulle visas som **klar att konfigurera**.
   
    ![Konfigurera enhet som iSCSI-server](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png) 
2. Klicka på enheten så visas ett informations meddelande som anger att enheten är redo att konfigureras.
   
    ![Konfigurera enhet som iSCSI-server 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis2m.png)  
3. Klicka på **Konfigurera** i enhetens kommando fält. Då öppnas bladet **Konfigurera** . Gör följande på bladet **Konfigurera** :
   
   * ISCSI-servernamnet fylls i automatiskt.
   * Kontrol lera att moln lagrings kryptering är **aktiverat**. Detta säkerställer att de data som skickas från enheten till molnet krypteras.
   * Ange en krypterings nyckel på 32-bokstav och registrera den i en nyckel hanterings app för framtida bruk.
   * Välj ett lagrings konto som ska användas med din enhet. I den här prenumerationen kan du välja ett befintligt lagrings konto, eller så kan du klicka på **Lägg** till för att välja ett konto från en annan prenumeration.
     
     ![Konfigurera enheten som iSCSI-server 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis4m.png)
4. Klicka på **Konfigurera** för att slutföra konfigurationen av iSCSI-servern.
   
    ![Konfigurera enhet som iSCSI-Server 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis5m.png) 
5. Du får ett meddelande om att iSCSI-servern håller på att skapas. När iSCSI-servern har skapats uppdateras bladet **enheter** och motsvarande enhets status är **online**.
   
    ![Konfigurera enheten som iSCSI-Server 5](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis9m.png)

## <a name="step-3-add-a-volume"></a>Steg 3: Lägg till en volym

1. På bladet **enheter** väljer du den enhet som du precis har konfigurerat som en iSCSI-server. Klicka på **...** (du kan också högerklicka på den här raden) och välj **Lägg till volym** på snabb menyn. Du kan också klicka på **+ Lägg till volym** från kommando fältet. Då öppnas bladet **Lägg till volym** .
   
    ![Lägg till en volym](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis10m.png)
2. Gör följande på bladet **Lägg till volym** :
   
   * Ange ett unikt namn för din volym i fältet **volym namn** . Namnet måste vara en sträng som innehåller mellan 3 och 127 tecken.
   * I list rutan **typ** anger du om du vill skapa en **nivåAD** eller **lokalt fäst** volym. För arbets belastningar som kräver lokala garantier, låg latens och högre prestanda, väljer du **lokalt fästa** **volym**. För alla andra data väljer du **nivå** **volym**.
   * I fältet **kapacitet** anger du storleken på volymen. En nivå volym måste vara mellan 500 GB och 5 TB och en lokalt fäst volym måste vara mellan 50 GB och 500 GB.
     
     En lokalt fäst volym är tjockt etablerad och säkerställer att primär data i volymen finns kvar på enheten och inte spiller i molnet.
     
     En nivå volym på den andra handen är tunt etablerad. När du skapar en nivå volym etablerades cirka 10% av utrymmet på den lokala nivån och 90% av utrymmet i molnet. Om du till exempel har etablerad en volym på 1 TB skulle 100 GB finnas i det lokala utrymmet och 900 GB används i molnet när data nivåerna. Detta innebär att om du tar bort allt lokalt utrymme på enheten kan du inte etablera en nivå resurs (eftersom 10% inte kommer att vara tillgänglig).
     
     ![Lägg till en volym 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis12.png)
   * Klicka på **anslutna värdar**, Välj en åtkomst kontroll post (ACR) som motsvarar den iSCSI-initierare som du vill ansluta till den här volymen och klicka sedan på **Välj**. <br><br> 
3. Om du vill lägga till en ny ansluten värd klickar du på **Lägg till ny**, anger ett namn för värden och dess iSCSI-kvalificerade namn (IQN) och klickar sedan på **Lägg till**. Om du inte har det kvalificerade iSCSI-resultatet går du till [bilaga A: Hämta IQN för en Windows Server-värd](#appendix-a-get-the-iqn-of-a-windows-server-host).
   
      ![Lägg till en volym 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis15m.png)
4. Klicka på **OK** när du är klar med konfigurationen av volymen. En volym skapas med de angivna inställningarna och ett meddelande visas. Som standard aktive ras övervakning och säkerhets kopiering för volymen.
   
     ![Lägg till en volym 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis18m.png)
5. Du kan kontrol lera att volymen har skapats genom att gå till bladet **volymer** . Du bör se volymen som visas.
   
   ![Lägg till en volym 5](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis20m.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>Steg 4: montera, initiera och formatera en volym

Utför följande steg för att montera, initiera och formatera dina StorSimple-volymer på en Windows Server-värd.

#### <a name="to-mount-initialize-and-format-a-volume"></a>Montera, initiera och formatera en volym

1. Öppna **iSCSI Initiator** -appen på lämplig server.
2. I fönstret **Egenskaper för iSCSI-initieraren**, på fliken **Identifiering**, klickar du på **Identifiera portal**.
   
    ![identifiera Portal](./media/storsimple-virtual-array-deploy3-iscsi-setup/image22.png)
3. I dialogrutan **Identifiera målportal**, anger du IP-adressen för ditt iSCSI-aktiverade nätverksgränssnitt och klickar på **OK**.
   
    ![IP-adress](./media/storsimple-virtual-array-deploy3-iscsi-setup/image23.png)
4. I fönstret **Egenskaper för iSCSI-initieraren**, på fliken **Mål**, letar du upp **Upptäckta mål**. (Varje volym är ett identifierat mål.) Enhetens status bör visas som **inaktiv**.
   
    ![identifierade mål](./media/storsimple-virtual-array-deploy3-iscsi-setup/image24.png)
5. Välj en målenhet och klicka sedan på **Anslut**. När enheten är ansluten, bör statusen ändras till **Ansluten**. (Mer information om hur du använder Microsoft iSCSI-initieraren finns i [Installera och konfigurera Microsoft iSCSI Initiator][1].
   
    ![Välj mål enhet](./media/storsimple-virtual-array-deploy3-iscsi-setup/image25.png)
6. På din Windows-värd trycker du på Windows-tangenten + X och klickar sedan på **Kör**.
7. I dialogrutan **Kör**, skriver du in **diskmgmt.msc**. Klicka på **OK** och dialogrutan **Diskhantering** visas. Den högra panelen visar volymerna som finns på din värd.
8. I fönstret **Diskhantering** visas de monterade volymerna enligt följande bild. Högerklicka på den identifierade volymen (klicka på diskens namn) och klicka sedan på **Online**.
   
    ![disk hantering](./media/storsimple-virtual-array-deploy3-iscsi-setup/image26.png)
9. Högerklicka och välj **initiera disk**.
   
    ![initiera disk 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image27.png)
10. I dialog rutan väljer du de diskar som ska initieras och klickar sedan på **OK**.
    
    ![initiera disk 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image28.png)
11. Guiden Ny enkel volym startar. Välj en disk storlek och klicka sedan på **Nästa**.
    
    ![ny volym, guide 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image29.png)
12. Tilldela volymen en enhets beteckning och klicka sedan på **Nästa**.
    
    ![ny volym Guide 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image30.png)
13. Ange parametrarna för att formatera volymen. **På Windows Server stöds endast NTFS.** Ställ in storleken på allokeringsenheten på 64 kB. Ange en etikett för volymen. Vi rekommenderar att det här namnet är identiskt med volym namnet du angav på din virtuella StorSimple-matris. Klicka på **Nästa**.
    
    ![ny volym Guide 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/image31.png)
14. Kontrol lera värdena för volymen och klicka sedan på **Slutför**.
    
    ![ny volym, guide 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/image32.png)
    
    Volymerna visas som **online** på sidan **disk hantering** .
    
    ![volymer online](./media/storsimple-virtual-array-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder det lokala webb gränssnittet för att [administrera din virtuella StorSimple-matris](storsimple-ova-web-ui-admin.md).

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>Bilaga A: Hämta IQN för en Windows Server-värd

Utför följande steg för att få det kvalificerade iSCSI-namnet (IQN) för en Windows-värd som kör Windows Server 2012.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Hämta IQN för en Windows-värd

1. Starta Microsoft iSCSI-initieraren på din Windows-värd.
2. I fönstret för **iSCSI-initieraregenskaper**, fliken **Konfiguration**, markerar och kopierar du strängen från fältet **Namn på initieraren**.
   
    ![iSCSI-initieraregenskaper](./media/storsimple-virtual-array-deploy3-iscsi-setup/image34.png)
3. Spara den här strängen.

<!--Reference link-->
[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee338480(v=ws.10)