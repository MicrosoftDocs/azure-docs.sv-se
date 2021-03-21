---
title: Konfigurera ett labb för att lära data vetenskap med python-och Jupyter-anteckningsböcker | Microsoft Docs
description: Lär dig hur du konfigurerar ett labb för att lära data vetenskap med python-och Jupyter-anteckningsböcker.
author: emaher
ms.topic: article
ms.date: 09/29/2020
ms.author: enewman
ms.openlocfilehash: d4034f889334bcf1e4eaa3710a32db60b6a9936b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94648029"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>Konfigurera ett labb för att lära data vetenskap med python-och Jupyter-anteckningsböcker
Den här artikeln beskriver hur du konfigurerar en virtuell mall för virtuella datorer i labb tjänster med de verktyg som behövs för att lära studenter hur man använder [Jupyter-anteckningsböcker](http://jupyter-notebook.readthedocs.io/)och hur studenter kan ansluta till sina antecknings böcker på sina virtuella datorer (VM: ar).

Jupyter Notebooks är ett projekt med öppen källkod som gör att du enkelt kan kombinera text och körbara python-källkod på en enda arbets yta som kallas för en bärbar dator. Att köra en bärbar dator resulterar i en linjär inspelning av indata och utdata. Dessa utdata kan innehålla text, tabeller med information, punkt diagram med mera.

## <a name="set-up-the-lab"></a>Konfigurera labbet

### <a name="lab-configuration"></a>Labb konfiguration
Om du vill ställa in det här labbet måste du ha åtkomst till en Azure-prenumeration och ett labb konto. Diskutera med din organisations administratör för att se om du kan få åtkomst till en befintlig Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

När du har en Azure-prenumeration kan du skapa ett nytt labb konto i Azure Lab Services genom att följa anvisningarna i självstudien: [Konfigurera ett labb konto](tutorial-setup-lab-account.md). Du kan också använda ett befintligt labb konto.

### <a name="lab-account-settings"></a>Labb konto inställningar
Aktivera inställningarna som beskrivs i tabellen nedan för labb kontot. Mer information om hur du aktiverar Marketplace-avbildningar finns i [Ange Marketplace-avbildningar som är tillgängliga för labb skapare](specify-marketplace-images.md).

| Inställning för labb konto | Instruktioner |
| ------------------- | ------------ |
| Marketplace-avbildning | I ditt labb konto aktiverar du en av Azure Marketplace-avbildningarna baserat på operativ systemets behov: <br/><ul><li>Data Science Virtual Machine – Windows Server 2019</li><li>Data Science Virtual Machine – Ubuntu 18,04</li></ul> |

> [!NOTE]
> I den här artikeln används avbildningarna för virtuella datorer för data vetenskap som är tillgängliga på Azure Marketplace eftersom de är förkonfigurerade med Jupyter Notebook. Dessa avbildningar innehåller dock även många andra utvecklings-och modellerings verktyg för data vetenskap. Om du inte vill ha de extra verktygen och vill ha en förenklad installation med bara Jupyter notebook-datorer skapar du en anpassad avbildning av virtuella datorer. Ett exempel är att [Installera JupyperHub på Azure](http://tljh.jupyter.org/en/latest/install/azure.html). När den anpassade avbildningen har skapats kan du ladda upp den till ett delat bild galleri för att använda avbildningen i Azure Lab Services. Lär dig mer om att [använda delade avbildnings galleri i Azure Lab Services](how-to-attach-detach-shared-image-gallery.md). 

### <a name="lab-settings"></a>Labb inställningar
Konfigurera inställningar för **virtuell dator** och **avbildning av virtuell dator** enligt följande tabell när du konfigurerar ett klass rums labb. Anvisningar om hur du skapar ett klass rums labb finns i [Konfigurera ett klass rums labb](tutorial-setup-classroom-lab.md).

| Labb inställningar | Värde/anvisningar |
| ------------ | ------------------ | 
| Storlek för virtuell dator | <p>Vilken storlek du väljer beror på vilken arbets belastning du vill köra:</p><ul><li>Liten eller medel – passar för en grundläggande installation av åtkomst till Jupyter-anteckningsböcker</li><li>Liten GPU (Compute) – bäst lämpad för beräknings intensiva och nätverks intensiva program som artificiell intelligens och djup inlärning</li></ul> | 
| Avbildning av virtuell dator | <p>Välj någon av följande avbildningar baserat på operativ systemets behov:</p><ul><li>[Data Science Virtual Machine – Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019)</li><li>[Data Science Virtual Machine – Ubuntu 18,04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview)</li></ul> |


### <a name="template-virtual-machine"></a>Mall för virtuell dator
När du har skapat ett labb skapas en mall för virtuella datorer baserat på den virtuella datorns storlek och avbildning som du har valt. Du konfigurerar mallen VM med allt du vill ge dina studenter för den här klassen. Mer information finns i [hantera den virtuella datorns mall](how-to-create-manage-template.md). 

Data Science VM avbildningar som standard levereras med många av data vetenskaps ramverk och verktyg som krävs för den här typen av klass. Till exempel är avbildningarna:

- [Jupyter Notebooks](http://jupyter-notebook.readthedocs.io/): ett webb program som gör det möjligt för data experter att ta rå data, köra beräkningar och se alla resultat i samma miljö. Den kommer att köras lokalt i mallen VM.  
- [Visual Studio Code](https://code.visualstudio.com/): en Integrated Development Environment (IDE) som ger en omfattande interaktiv upplevelse när du skriver och testar en bärbar dator. Mer information finns i [arbeta med antecknings böcker i Jupyter i Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support).

### <a name="provide-notebooks-for-the-class"></a>Tillhandahålla antecknings böcker för klassen
Nästa uppgift är att ge studenter med antecknings böcker som du vill att de ska använda. Om du vill tillhandahålla egna antecknings böcker kan du spara antecknings böcker lokalt på mallen VM. 

Om du vill använda exempel antecknings böcker från Azure Machine Learning, se så [här konfigurerar du en miljö med Jupyter-anteckningsböcker](../machine-learning/how-to-configure-environment.md#jupyter). 

### <a name="optional-enable-graphical-desktop-for-linux"></a>Valfritt: Aktivera grafiskt skriv bord för Linux 
Avbildningen **data science Virtual Machine – Ubuntu** har redan tillhandahållits med X2GO-servern och är redo att ta emot klient anslutningar. Inga ytterligare steg krävs när du konfigurerar mallen för virtuella datorer. 

### <a name="publish-the-template-machine"></a>Publicera mallen Machine
När du publicerar mallen får varje student som är registrerad i labbet en kopia av mallen VM med alla lokala verktyg och antecknings böcker som du har konfigurerat på den.

## <a name="how-students-connect-to-jupyter-notebooks"></a>Hur ansluter eleverna till Jupyter Notebooks?
När du har publicerat mallen har varje elev åtkomst till en virtuell dator som medföljer allt du har förkonfigurerat för klassen, inklusive Jupyter notebook-datorer. I följande avsnitt visas olika sätt för studenter att ansluta till Jupyter-anteckningsböcker. 

### <a name="for-windows-vms"></a>För virtuella Windows-datorer
Om du har fått studenter med virtuella Windows-datorer måste de ansluta till sina virtuella datorer och använda Jupyter-anteckningsböcker som är tillgängliga lokalt på dem. 

För att ansluta till en virtuell Windows-dator kan en student använda en anslutning till fjärr skrivbord (RDP). Detaljerade anvisningar finns i [så här får du åtkomst till ett klass rums labb](how-to-use-classroom-lab.md). 

En elev som använder en Mac-eller Chromebook kan följa anvisningarna i följande artiklar för att ansluta till den virtuella Windows-datorn för data vetenskap. 

- [Ansluta till en virtuell dator med RDP på en Mac](connect-virtual-machine-mac-remote-desktop.md)
- [Ansluta till en virtuell dator med RDP på en Chromebook](connect-virtual-machine-chromebook-remote-desktop.md)

### <a name="for-linux-vms"></a>För virtuella Linux-datorer
Om du har fått studenter med virtuella Linux-datorer, finns det flera alternativ som studenter kan använda för att ansluta till sina Jupyter-anteckningsböcker i de virtuella datorerna:

- Åtkomst till Jupyter-anteckningsböcker lokalt efter anslutning till den virtuella datorn
    - SSH till den virtuella datorn för terminalserversessioner
     - X2Go anslutning till den virtuella datorn för grafiska sessioner
- Använd SSH-tunnlar för att ansluta från Students lokala dator direkt till Jupyter-servern på den virtuella datorn. 

I följande avsnitt finns information om de här sätten att ansluta till Jupyter-anteckningsböcker. 

#### <a name="ssh-to-virtual-machine"></a>SSH till virtuell dator
Studenter kan ansluta via SSH till sina virtuella Linux-datorer från en terminalsession. Detaljerade anvisningar finns i [så här får du åtkomst till ett klass rums labb](how-to-use-classroom-lab.md). Om de använder en Windows-klientdator måste de aktivera en SSH-klient genom att hämta [SparaTillFil](https://www.putty.org/) eller aktivera [openssh i Windows](/windows-server/administration/openssh/openssh_install_firstuse) till SSH från kommando tolken. 

1.  Starta den virtuella datorn.
2.  När den virtuella datorn är igång klickar du på **Anslut**, som visar en dialog ruta som tillhandahåller SSH-kommandoraden, som ser ut som i följande exempel:
    
     ```shell
    ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```
3.  Gå till kommando tolken eller terminalen och klistra in det här kommandot och tryck sedan på **RETUR**.
4.  Ange lösen ordet för att logga in på den virtuella datorn. 

När eleverna är anslutna till virtuella datorer kan de komma åt och köra Jupyter-anteckningsböcker lokalt.

#### <a name="x2go-to-virtual-machine"></a>X2Go till virtuell dator
Avbildningen **data science Virtual Machine – Ubuntu** har redan tillhandahållits med X2GO-servern och är redo att ta emot klient anslutningar. För att ansluta till den grafiska datorn på Linux-datorn, behöver eleverna följa dessa steg för att konfigurera X2Go på klient datorerna:

1.  Hämta och installera [X2Go-klienten](https://wiki.x2go.org/doku.php/doc:installation:x2goclient) för klient plattformen.
2.  Kontrol lera att den virtuella Linux-datorn som du vill ansluta till har startats i [Azure Lab Services-portalen](https://labs.azure.com).
3.  När den virtuella datorn är igång klickar du på **Anslut**, som visar en dialog ruta som tillhandahåller SSH-kommandoraden, som ser ut som i följande exempel:

    ```
     ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```    
4. När du har den här informationen öppnar du X2Go-klientens app och skapar en ny session. 
5.  Fyll i följande värden i fönstret **Inställningar för session** :
    - **Sessionsnamn**: det kan vara vad du vill, men vi rekommenderar att du använder namnet på din virtuella labb dator.
     - **Värd**: `ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com`
     - **Inloggning**: student
     - **SSH-port**: 12345
     - **Typ av session**: xfce
6. Välj **OK**. 

    > [!NOTE]
     > När du skapar en ny X2Go-session ska du se till att använda SSH-porten, **inte** RDP-porten.

Följ de här stegen för att ansluta till den virtuella datorn:    

1.  I X2Go-klienten dubbelklickar du på den virtuella dator som du vill ansluta till. 

    ![X2Go-klient](./media/class-type-jupyter-notebook/x2go-client.png)
2. Ange lösen ordet för att ansluta till den virtuella datorn. (Du kan behöva ge X2Go-behörighet för att kringgå brand väggen för att slutföra anslutningen.)
3.  Nu bör du se det grafiska gränssnittet för din Ubuntu-Data Science VM.


#### <a name="ssh-tunnel-to-jupyter-server-on-the-vm"></a>SSH-tunnel till Jupyter-server på den virtuella datorn
Vissa studenter kanske vill ansluta direkt från den lokala datorn direkt till Jupyter-servern i sina virtuella datorer. SSH-protokollet möjliggör vidarebefordran av portar mellan den lokala datorn och en fjärrserver (i vårt exempel den virtuella datorns labb dator) så att ett program som körs på en viss port på servern **tunnlar** till mappnings porten på den lokala datorn. Eleverna bör följa de här stegen för SSH-tunnel till Jupyter-servern på sina virtuella labb datorer:

1.  I [Azure Lab Services Portal](https://labs.azure.com)kontrollerar du att den virtuella Linux-datorn som du vill ansluta har startats.
2.  När den virtuella datorn är igång klickar du på **Anslut**, som visar en dialog ruta som tillhandahåller SSH-kommandoraden, som ser ut ungefär som följande sträng:

    ```bash
     ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```
3. Starta en terminal eller kommando tolk på den lokala datorn och kopiera SSH-anslutningssträngen till den. Lägg sedan till i `-L 8888:localhost:8888` kommando strängen som skapar **tunneln** mellan portarna. Den slutgiltiga strängen bör se ut så här:

    ```bash
     ssh –L 8888:localhost:8888 -p 12345 student@ml-lab-b720853e-570f-49ac-9cb2-bd0bd2aeec35.eastus.cloudapp.azure.com
     ```
4. Kör kommandot genom att trycka på **RETUR** . 
5.  När du uppmanas till det anger du lösen ordet för att ansluta till den virtuella labb datorn. 
6.  När du är ansluten till den virtuella datorn startar du Jupyter-servern med följande kommando: 

    ```bash
     jupyter notebook
     ```
7. Om du kör kommandot får du en URL i terminalen eller kommando tolken. URL: en bör se ut så här:

    ```bash
     http://localhost:8888/?token=8c09ecfc93e6a8cbedf9c66dffdae19670a64acc1d37
     ```
8. Klistra in den här URL-adressen i en webbläsare på din lokala dator för att ansluta och arbeta med din Jupyter Notebook. 

    > [!NOTE]
    > Visual Studio Code ger också en bra [Jupyter Notebook redigerings upplevelse](https://code.visualstudio.com/docs/python/jupyter-support). Du kan följa anvisningarna för [hur du ansluter till en fjärran sluten Jupyter-Server](https://code.visualstudio.com/docs/python/jupyter-support#_connect-to-a-remote-jupyter-server) och använder samma URL från föregående steg för att ansluta från vs Code i stället för från webbläsaren. 


## <a name="cost-estimate"></a>Kostnads uppskattning
Vi ska se en möjlig kostnads uppskattning för den här klassen. Vi använder en klass av 25 studenter. Det finns 20 timmar med den schemalagda klass tiden. Dessutom får varje student en kvot på 10 timmar för läxor eller tilldelningar utanför schemalagda klass tider. Den VM-storlek som vi valde var liten GPU (Compute), vilket är 139 lab-enheter. Om du vill använda små (20 lab-enheter) eller medium storlek (42 lab-enheter) kan du ersätta labb enhets delen i ekvationen nedan med rätt nummer.  

Här är ett exempel på en möjlig kostnads uppskattning för den här klassen: 25 studenter * (20 schemalagda timmar + 10 kvot timmar) * 139 labb enheter * 0,01 USD per timme = 1042,5 USD

Mer detaljerad information om priser finns [Azure Lab Services prissättning](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Slutsats
I den här artikeln gick vi igenom stegen för att skapa ett labb för en Jupyter Notebooks-klass. Du kan använda en liknande installation för andra maskin inlärnings klasser.

## <a name="next-steps"></a>Nästa steg

Nästa steg är vanliga för att ställa in alla labb.

- [Skapa och hantera en mall](how-to-create-manage-template.md)
- [Lägg till användare](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ange kvot](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ange ett schema](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-postregistrering länkar till studenter](how-to-configure-student-usage.md#send-invitations-to-users)