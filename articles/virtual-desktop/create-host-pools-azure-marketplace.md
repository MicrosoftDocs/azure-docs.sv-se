---
title: Windows-pool för virtuella skriv bord Azure Portal – Azure
description: Så här skapar du en Windows-pool för virtuella skriv bord med hjälp av Azure Portal.
author: Heidilohr
ms.topic: tutorial
ms.custom: references_regions
ms.date: 02/17/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 46a029a3b803428d6250b74059190f66183be452
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100651467"
---
# <a name="tutorial-create-a-host-pool-with-the-azure-portal"></a>Självstudie: skapa en värdbaserad pool med Azure Portal

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder Windows Virtual Desktop (klassisk) utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/create-host-pools-azure-marketplace-2019.md). Objekt som du skapar med Windows Virtual Desktop (klassisk) kan inte hanteras med Azure Portal.

Lagringspooler är en samling av en eller flera identiska virtuella datorer (VM) i Windows-miljöer för virtuella skriv bord. Varje adresspool kan innehålla en app-grupp som användare kan interagera med på samma sätt som på ett fysiskt skriv bord.

Den här artikeln vägleder dig genom installations processen för att skapa en adresspool för en Windows Virtual Desktop-miljö via Azure Portal. Den här metoden ger ett webbläsarbaserat användar gränssnitt för att skapa en adresspool i Windows Virtual Desktop, skapa en resurs grupp med virtuella datorer i en Azure-prenumeration, ansluta de virtuella datorerna till den Azure Active Directory AD-domänen och registrera de virtuella datorerna med Windows Virtual Desktop.

## <a name="prerequisites"></a>Förutsättningar

Du måste ange följande parametrar för att skapa en värdbaserad pool:

- Namnet på den virtuella dator avbildningen
- Konfiguration av virtuell dator
- Egenskaper för domän och nätverk
- Egenskaper för pool för virtuella Windows-värdar

Du måste också känna till följande saker:

- Var källan till den avbildning du vill använda finns. Är det från Azure Gallery eller är det en anpassad avbildning?
- Autentiseringsuppgifterna för din domän anslutning.

Kontrol lera också att du har registrerat resurs leverantören för Microsoft. DesktopVirtualization. Om du inte redan har gjort det går du till **prenumerationer**, väljer namnet på din prenumeration och väljer sedan **resurs leverantörer**. Sök efter DesktopVirtualization, Välj Microsoft. DesktopVirtualization och välj sedan registrera.

När du skapar en Windows-pool för virtuella Skriv bords värdar med Azure Resource Manager-mallen kan du skapa en virtuell dator från Azure-galleriet, en hanterad avbildning eller en ohanterad avbildning. Mer information om hur du skapar VM-avbildningar finns i [förbereda en Windows VHD-eller VHDX-överföring till Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) och [skapa en hanterad avbildning av en generaliserad virtuell dator i Azure](../virtual-machines/windows/capture-image-resource.md).

Om du inte redan har en Azure-prenumeration kan du se till att [skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar följa dessa anvisningar.

## <a name="begin-the-host-pool-setup-process"></a>Starta installationen av poolen för värdar

För att börja skapa den nya poolen:

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com/).
   
   >[!NOTE]
   > Om du loggar in på US Gov portal går du till [https://portal.azure.us/](https://portal.azure.us/) i stället.

2. Ange det **virtuella Windows-skrivbordet** i Sök fältet och Sök sedan efter och välj **Windows Virtual Desktop** under tjänster.

3. På sidan Översikt för **virtuella Windows-datorer** väljer du **skapa en adresspool**.

4. På fliken **grundläggande** väljer du rätt prenumeration under projekt information.

5. Välj antingen **Skapa nytt** för att skapa en ny resurs grupp eller Välj en befintlig resurs grupp på den nedrullningsbara menyn.

6. Ange ett unikt namn för din värd bassäng.

7. I fältet plats väljer du den region där du vill skapa fakturapoolen från den nedrullningsbara menyn.

   Azure-geografin som är associerad med de regioner som du har valt är där metadata för den här poolen och dess relaterade objekt kommer att lagras. Se till att du väljer de regioner i geografin som du vill att tjänstens metadata ska lagras i.

     > [!div class="mx-imgBorder"]
     > ![En skärm bild av Azure Portal som visar fältet plats med platsen östra USA vald. Bredvid fältet är texten "metadata kommer att lagras i USA, östra".](media/portal-location-field.png)
  
   >[!NOTE]
   > Om du vill skapa en adresspool i [en region som stöds](data-locations.md) utanför USA måste du registrera resurs leverantören igen. När du har registrerat igen bör du se de andra regionerna i list rutan för att välja plats. Lär dig hur du registrerar på nytt med hjälp av den här artikeln för att [skapa en Fakturapool](troubleshoot-set-up-issues.md#i-only-see-us-when-setting-the-location-for-my-service-objects) .

8. Under typ av värdstat väljer du om din värddator ska vara **personlig** eller **pool**.

    - Om du väljer **privat** väljer du antingen **Automatisk** eller **direkt** i fältet tilldelnings typ.

      > [!div class="mx-imgBorder"]
      > ![En skärm bild av list rutan tilldelnings typ fält. Användaren har valt automatisk.](media/assignment-type-field.png)

9.  Om du väljer **pool**, anger du följande information:

     - För **högsta antal sessioner** anger du det maximala antalet användare som du vill belastningsutjämna till en värd för en enda session.
     - För **belastnings Utjämnings algoritm** väljer du antingen bredd-första eller djup-först, baserat på ditt användnings mönster.

       > [!div class="mx-imgBorder"]
       > ![En skärm bild av tilldelnings typ fältet med "pooled" valt. Användaren håller mus pekaren över bredden – först på den nedrullningsbara menyn för belastnings utjämning.](media/pooled-assignment-type.png)

10. Välj **Nästa: Virtual Machines >**.

11. Om du redan har skapat virtuella datorer och vill använda dem med den nya poolen väljer du **Nej**, Välj **Nästa: arbets ytans >** och hoppar till avsnittet [information om arbets yta](#workspace-information) . Välj **Ja** om du vill skapa nya virtuella datorer och registrera dem i den nya poolen.

Nu när du har slutfört den första delen kan vi gå vidare till nästa del av installations processen där vi skapar den virtuella datorn.

## <a name="virtual-machine-details"></a>Information om virtuell dator

Nu när vi går igenom den första delen måste du konfigurera din virtuella dator.

Så här konfigurerar du den virtuella datorn i konfigurationen av poolen för värdar:

1. Under **resurs grupp** väljer du den resurs grupp där du vill skapa de virtuella datorerna. Detta kan vara en annan resurs grupp än den som du använde för poolen.

2. Välj den **virtuella dator plats** där du vill skapa de virtuella datorerna. De kan vara samma eller olika från den region som du har valt för poolen.

3. Välj sedan den **virtuella dator storlek** som du vill använda. Du kan antingen behålla standard storleken som är eller välja **ändra storlek** om du vill ändra storleken. Om du väljer **ändra storlek** i fönstret som visas väljer du storleken på den virtuella dator som passar din arbets belastning.

4. Under **antal virtuella datorer** anger du det antal virtuella datorer som du vill skapa för din värd pool.

    >[!NOTE]
    >Installations processen kan skapa upp till 400 virtuella datorer när du konfigurerar din adresspool, och varje process för VM-installation skapar fyra objekt i resurs gruppen. Eftersom processen för att skapa inte kontrollerar din prenumerations kvot kontrollerar du att antalet virtuella datorer som du anger ligger inom Azure VM och API-gränserna för resurs gruppen och prenumerationen. Du kan lägga till fler virtuella datorer när du är klar med att skapa din värd-pool.

5. Efter det anger du ett **namn prefix** som namnger de virtuella datorer som installations processen skapar. Suffixet kommer att vara `-` med siffror från 0.

6. Välj sedan den avbildning som ska användas för att skapa den virtuella datorn. Du kan välja antingen **Galleri** eller **Storage-BLOB**.

    - Om du väljer **Galleri** väljer du en av de rekommenderade bilderna på den nedrullningsbara menyn:

      - Windows 10 Enterprise multi-session, version 1909
      - Windows 10 Enterprise multi-session, version 1909 + Microsoft 365-appar
      - Windows Server 2019 Datacenter
      - Windows 10 Enterprise multi-session, version 2004
      - Windows 10 Enterprise multi-session, version 2004 + Microsoft 365-appar

     Om du inte ser den avbildning som du vill använda väljer du **Bläddra bland alla avbildningar och diskar**, vilket gör att du kan välja antingen en annan bild i galleriet eller en avbildning som tillhandahålls av Microsoft och andra utgivare. Se till att den avbildning du väljer är en av de [operativ system avbildningar som stöds](overview.md#supported-virtual-machine-os-images).

     > [!div class="mx-imgBorder"]
     > ![En skärm bild av Marketplace med en lista över bilder från Microsoft som visas.](media/marketplace-images.png)

     Du kan också gå till **Mina objekt** och välja en anpassad avbildning som du redan har laddat upp.

     > [!div class="mx-imgBorder"]
     > ![En skärm bild av fliken Mina objekt.](media/my-items.png)

    - Om du väljer **Storage BLOB** kan du använda en egen avbildning som bygger på Hyper-V eller på en virtuell Azure-dator. Allt du behöver göra är att ange platsen för avbildningen i Storage-blobben som en URI.

7. Välj vilken typ av OS-diskar du vill att de virtuella datorerna ska använda: Standard SSD, Premium SSD eller Standard HDD.

8. Under nätverk och säkerhet väljer du det **virtuella nätverk** och **undernät** där du vill placera de virtuella datorer som du skapar. Se till att det virtuella nätverket kan ansluta till domänkontrollanten eftersom du måste ansluta de virtuella datorerna i det virtuella nätverket till domänen. DNS-servrarna för det virtuella nätverk som du har valt ska konfigureras att använda IP-adressen för domänkontrollanten.

9. Välj sedan om du vill ha en offentlig IP-adress för de virtuella datorerna. Vi rekommenderar att du väljer **Nej** eftersom en privat IP-adress är säkrare.

10. Välj vilken typ av säkerhets grupp du vill använda: **Basic**, **Advanced** eller **none**.

    Om du väljer **Basic** måste du välja om du vill att en inkommande port ska vara öppen. Om du väljer **Ja** väljer du i listan med standard portar att tillåta inkommande anslutningar till.

    >[!NOTE]
    >För större säkerhet rekommenderar vi att du inte öppnar offentliga inkommande portar.

    > [!div class="mx-imgBorder"]
    > ![En skärm bild av sidan säkerhets grupp som visar en lista över tillgängliga portar i en nedrullningsbar meny.](media/available-ports.png)

    Om du väljer **Avancerat** väljer du en befintlig nätverks säkerhets grupp som du redan har konfigurerat.

11. Efter det väljer du om du vill att de virtuella datorerna ska vara anslutna till en speciell domän och organisationsenhet. Om du väljer **Ja** anger du domänen som ska anslutas. Du kan också lägga till en speciell organisationsenhet som du vill att de virtuella datorerna ska finnas i. Om du väljer **Nej** kommer de virtuella datorerna att anslutas till domänen som matchar suffixet för **AD-domänens anslutningens UPN**.

  - När du anger en ORGANISATIONSENHET måste du kontrol lera att du använder den fullständiga sökvägen (unikt namn) och utan citat tecken.

12. Under administratörs konto anger du autentiseringsuppgifterna för Active Directory-domän admin för det virtuella nätverk som du har valt. Det här kontot kan inte ha Multi-Factor Authentication (MFA) aktiverat. När du ansluter till en Azure Active Directory Domain Services (Azure AD DS)-domän måste kontot vara en del av administratörs gruppen för Azure AD DC och konto lösen ordet måste fungera i Azure AD DS.

13. Välj **Nästa: >arbets yta**.

Vi är nu redo att starta nästa steg i konfigurationen av din värddator: registrera din app-grupp på en arbets yta.

## <a name="workspace-information"></a>Arbetsyteinformation

Installations processen för fakturapoolen skapar en program grupp för skriv bord som standard. För att värddatorn ska fungera som avsett måste du publicera den här program gruppen till användare eller användar grupper och du måste registrera app-gruppen på en arbets yta.

Registrera Skriv bords gruppen på en arbets yta:

1. Välj **Ja**.

   Om du väljer **Nej** kan du registrera app-gruppen senare, men vi rekommenderar att du får registrering av arbets ytorna så snart du kan så att din värd pool fungerar som den ska.

2. Välj sedan om du vill skapa en ny arbets yta eller Välj från befintliga arbets ytor. Endast arbets ytor som har skapats på samma plats som poolen kommer att kunna registrera app-gruppen på.

3. Alternativt kan du välja **Nästa: taggar >**.

    Här kan du lägga till taggar så att du kan gruppera objekten med metadata för att göra det enklare för dina administratörer.

4. När du är klar väljer du **Granska + skapa**.

     >[!NOTE]
     >Verifierings processen för att granska och skapa kontrollerar inte om ditt lösen ord uppfyller säkerhets standarderna eller om arkitekturen är korrekt, så du måste kontrol lera eventuella problem med någon av dessa saker.

5. Granska informationen om distributionen för att kontrol lera att allting ser korrekt ut. När du är färdig väljer du **Skapa**. Detta startar distributions processen, som skapar följande objekt:

     - Den nya poolen för värdar.
     - En Skriv bords grupp.
     - En arbets yta, om du väljer att skapa den.
     - Registreringen slutförs om du väljer att registrera Skriv bords gruppen.
     - Virtuella datorer, om du väljer att skapa dem, som är anslutna till domänen och registrerade med den nya poolen.
     - En nedladdnings länk för en Azure-resurs hanterings mall baserat på din konfiguration.

Därefter är du klar!

## <a name="run-the-azure-resource-manager-template-to-provision-a-new-host-pool"></a>Kör Azure Resource Manager-mallen för att etablera en ny värdbaserad pool

Om du hellre vill använda en automatiserad process [laddar du ned vår Azure Resource Manager-mall](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates) för att etablera den nya poolen i stället.

>[!NOTE]
>Om du använder en automatiserad process för att bygga din miljö behöver du den senaste versionen av konfigurations-JSON-filen. Du kan hitta JSON-filen [här](https://wvdportalstorageblob.blob.core.windows.net/galleryartifacts?restype=container&comp=list).

## <a name="next-steps"></a>Nästa steg

Nu när du har gjort din värd pool kan du fylla den med RemoteApp-program. Om du vill veta mer om hur du hanterar appar i Windows Virtual Desktop, kan du gå till nästa självstudie:

> [!div class="nextstepaction"]
> [Själv studie kurs för hantering av app-grupper](./manage-app-groups.md)
