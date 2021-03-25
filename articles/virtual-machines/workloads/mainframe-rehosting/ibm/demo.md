---
title: Konfigurera en programutvecklare styrd distribution (ADCD) i IBM zD&T v1 | Microsoft Docs
description: Kör en miljö för IBM Z-utveckling och test miljö (zD&T) i Azure Virtual Machines (VM).
services: virtual-machines
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
documentationcenter: ''
author: njray
manager: edprice
ms.author: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: b1391f81c571879534258c289436d2d086b84bef
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045951"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>Konfigurera en programutvecklare styrd distribution (ADCD) i IBM zD&T v1

Du kan köra en miljö för IBM Z-utveckling och test miljö (zD&T) i Azure Virtual Machines (VM). Den här miljön emulerar arkitekturen i IBM Z-serien. Det kan vara värd för en rad olika operativ system eller installationer i Z-serien (kallas även Z-instanser eller-paket), som görs tillgängliga via anpassade paket som kallas IBM-programutvecklare kontrollerade distributioner (ADCDs).

Den här artikeln visar hur du konfigurerar en ADCD-instans i en zD&T-miljö på Azure. ADCDs skapa fullständiga operativ Systems implementeringar i Z-serien för utvecklings-och test miljöer som körs i zD&T.

Precis som zD&T är ADCDs bara tillgängliga för IBM-kunder och-partner och är endast avsedda för utvecklings-och testnings ändamål. De ska inte användas i produktions miljöer. Det finns flera IBM-installations paket tillgängliga för hämtning via [Passport-förmån](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) eller [IBM PartnerWorld](https://www.ibm.com/partnerworld/public).

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- [ZD&T-miljö][ibm-install-z] tidigare konfigurerat på Azure. Den här artikeln förutsätter att du använder samma Ubuntu 16,04 VM-avbildning som skapades tidigare.

- Åtkomst till ADCD media via IBM PartnerWorld eller Passport-fördel.

- En [licensierings Server](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Detta krävs för att köra IBM zD&T. Hur du skapar det beror på hur du licensierar program varan från IBM:

  - **Maskinvarubaserad licens Server** kräver en USB-maskinvara som innehåller de rationella tokens som krävs för att få åtkomst till alla delar av program varan. Du måste hämta detta från IBM.

  - Med **programvarubaserad licens Server** måste du konfigurera en central server för hantering av licens nycklarna. Den här metoden är önskad och kräver att du konfigurerar de nycklar du får från IBM i hanterings servern.

## <a name="download-the-installation-packages-from-passport-advantage"></a>Hämta installations paketen från Passport-fördel

Åtkomst till ADCD Media krävs. Stegen nedan förutsätter att du är IBM-kunder och kan använda Passport-fördel. IBM-partner kan använda [IBM PartnerWorld](https://www.ibm.com/partnerworld/public).

> [!NOTE]
> Den här artikeln förutsätter att en Windows-dator används för att komma åt Azure Portal och för att ladda ned IBM-mediet. Om du använder en Mac-eller Ubuntu Desktop kan kommandona och processen för att hämta IBM-mediet skilja sig något.

1. Logga in på [Passport-fördel](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer).

2. Välj **hämtning av program vara** och **medie åtkomst**.

3. Välj **program erbjudande och avtals nummer** och klicka på **Fortsätt**.

4. Ange del beskrivningen eller del numret och klicka på **Finder**.

5. Du kan också klicka på listan alfabetisk ordning om du vill visa och Visa produkten efter namn.

6. Välj **alla operativ system** i **fältet operativ system** och **alla språk** i **fältet språk**. Klicka sedan på **gå**.

7. Klicka på **Välj enskilda filer** för att expandera listan och Visa de enskilda media som ska laddas ned.

8. Verifiera de paket som du vill ladda ned, Välj **Ladda ned** och ladda ned filerna till den katalog som du vill använda.

## <a name="upload-the-adcd-packages"></a>Ladda upp ADCD-paketen

Nu när du har paket måste du ladda upp dem till den virtuella datorn i Azure.

1. I Azure Portal initierar du en **SSH** -session med den virtuella datorn Ubuntu som du skapade. Gå till den virtuella datorn, Välj bladet **Översikt** och välj sedan **Anslut**.

2. Välj fliken **SSH** och kopiera sedan SSH-kommandot till Urklipp.

3. Logga in på den virtuella datorn med dina autentiseringsuppgifter och valfri [SSH-klient](../../../linux/use-remote-desktop.md) . Den här demon använder Linux-tilläggen för Windows 10, som lägger till ett bash-gränssnitt i kommando tolken i Windows. SparaTillFil fungerar lika bra.

4. När du är inloggad skapar du en katalog för att ladda upp IBM-paketen. Kom ihåg att Linux är Skift läges känsligt. Den här demon förutsätter till exempel att paketen laddas upp till:

    `/home/MyUserID/ZDT/adcd/nov2017/volumes`

5. Ladda upp filerna med en SSH-klient, till exempel[WinSCP](https://winscp.net/eng/index.php). Eftersom SCP är en del av SSH använder den port 22, som är vad SSH använder. Om den lokala datorn inte är Windows kan du ange SCP- [kommandot](http://man7.org/linux/man-pages/man1/scp.1.html) i SSH-sessionen.

6. Initiera överföringen till Azure VM-katalogen som du skapade, vilket blir avbildnings lagringen för zD&T.

    > [!NOTE]
    > Se till att **ADCDTOOLS.XML** ingår i överföringen till katalogen **Home/ZDT//adcd/nov2017** . Du behöver den senare.

7. Vänta tills filerna har laddats upp, vilket kan ta lite tid beroende på din anslutning till Azure.

8. När uppladdningarna är klara navigerar du till katalogen volymer och dekomprimerar alla **gz** -volymer:

    ```console
    gunzip \*.gz
    ```
    
![Utforskaren visar dekomprimerade gz-volymer](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>Konfigurera avbildnings lagringen

Nästa steg är att konfigurera zD&T för att använda de uppladdade paketen. Avbildnings lagrings processen i zD&T kan du montera och använda avbildningarna. Den kan använda SSH eller FTP.

1. Starta **zDTServer**. För att göra detta måste du vara på rotnivå. Ange följande två kommandon i ordning:
    ```console
    sudo su -
    /opt/ibm/zDT/bin/startServer
    ```
2. Observera URL-utdata med kommandot och Använd denna URL för att få åtkomst till webb servern. Det ser ut ungefär så här:
     > https://(din VM-namn eller IP-adress): 9443/ZDTMC/index.html
     >
     > Kom ihåg att din webb åtkomst använder port 9443. Använd detta för att logga in på webb servern. Användar-ID: t för ZD&T är **zdtadmin** och lösen ordet är ett **lösen** ord.

    ![Välkomst skärmen för IBM zD&T Enterprise Edition](media/02-welcome.png)

3. På sidan **Snabbstart** under **Konfigurera** väljer du **bild lagring**.

     ![IBM zD&T Enterprise Edition Snabbstart skärm](media/03-quickstart.png)

4. På sidan **Konfigurera avbildnings lagring** väljer du **SSH-File Transfer Protocol**.

5. För **värd namn** skriver du **localhost** och anger sökvägen till den plats där du laddade upp avbildningarna. Till exempel/home/MyUserID/ZDT/adcd/nov2017/volumes.

6. Ange **användar-ID** och **lösen ord** för den virtuella datorn. Använd inte ZD&T. ex. användar-ID och lösen ord.

7. Testa anslutningen för att kontrol lera att du har åtkomst och välj sedan **Spara** för att spara konfigurationen.

## <a name="configure-the-target-environments"></a>Konfigurera mål miljöerna

Nästa steg är att konfigurera zD&T-mål miljön. Den här emulerade värd miljön är den plats där dina avbildningar körs.

1. På sidan **Snabbstart** under **Konfigurera** väljer du **mål miljöer**.

2. På sidan **Konfigurera mål miljöer** väljer du **Lägg till mål**.

3. Välj **Linux**. IBM stöder två typer av miljöer, Linux och moln (OpenStack), men den här demon körs på Linux.

4. På sidan **Lägg till mål miljö** anger du **localhost** som **värd namn**. Behåll **SSH-porten** inställd på **22**.

5. I rutan **mål miljö etikett** anger du en etikett som **MyCICS.**

     ![Fönstret Lägg till mål miljö](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>Konfigurera ADCD och distribuera

När du har slutfört de tidigare konfigurations stegen måste du konfigurera zD&T för att använda paketen och mål miljön. Återigen använder du avbildnings lagrings processen i zD&T, som gör att du kan montera och använda avbildningarna. Den kan använda SSH eller FTP.

1. På sidan **Snabbstart** under **Konfigurera** väljer du **ADCD**. En uppsättning instruktioner visas som anger vilka steg som måste utföras innan ett ADCD-paket kan monteras. Detta förklarar varför vi har namngett mål katalogen på det sätt som vi gjorde tidigare.

2. Förutsatt att alla avbildningar har laddats upp till rätt kataloger, klickar du på **avbildningen från ADCD** -länken som visas längst ned till höger (visas i steg 7 i följande skärm bild).

     ![IBM zD&T Enterprise Edition – konfigurera ADCD-skärmen](media/05-adcd.png)

## <a name="create-the-image"></a>Skapa avbildningen

När föregående konfigurations steg har slutförts visas sidan **skapa en avbildning med ADCD-komponenter** .

1. Välj volymen (nov 2017 i det här fallet) om du vill visa de olika paket som finns på volymen.

2. I den här demon väljer du **Customer information Control system (CICS)-5,3**.

3. I rutan **avbildnings namn** skriver du ett namn på bilden, till exempel **MyCICS-avbildning**.

4. Välj knappen **Skapa avbildning** längst ned till höger.

     ![IBM zD&T Enterprise Edition – skapa en avbildning med ADCD Components-skärmen](media/06-adcd.png)

5. I fönstret som visas och meddelar dig att avbildningen har distribuerats, väljer du **distribuera avbildningar**.

6. På sidan **distribuera en avbildning till en mål miljö** väljer du den avbildning som du skapade på föregående sida (**MyCICS-avbildning**) och mål miljön som skapades tidigare (**MyCICS**).

7. På nästa skärm anger du dina autentiseringsuppgifter för den virtuella datorn (det vill säga inte ztadmin-autentiseringsuppgiften).

8. I rutan egenskaper anger du antalet **centrala processorer (CPS)**, mängden **system minne (GB)** och **distributions katalogen** för den pågående avbildningen. Eftersom det är en demonstration bör du hålla den liten.

9. Kontrol lera att rutan är markerad för **att automatiskt utfärda IPL-kommandot till z/OS efter distribution**.

     ![Egenskaps skärm](media/07-properties.png)

10. Välj **Slutför**.

11. Välj **distribuera avbildning** från sidan **distribuera en avbildning till en mål miljö** .

Din avbildning kan nu distribueras och är redo att monteras av en 3270-termin Ale mula Tor.

> [!NOTE]
> Om du får ett fel meddelande om att det inte finns tillräckligt med disk utrymme, Tänk på att regionen kräver 151 GB.

Grattis! Nu kör du en IBM stordator miljö på Azure.

## <a name="learn-more"></a>Läs mer

- [Stordator-migrering: myths och fakta](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [IBM DB2-pureScale på Azure](ibm-db2-purescale-azure.md)
- [Felsökning](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)
- [Avmystifiera-stordator till Azure-migrering](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md