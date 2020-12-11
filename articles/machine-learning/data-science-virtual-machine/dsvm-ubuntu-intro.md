---
title: 'Snabb start: skapa en Ubuntu-Data Science Virtual Machine'
titleSuffix: Azure Data Science Virtual Machine
description: Konfigurera och skapa en Data Science Virtual Machine för Linux (Ubuntu) för att göra analyser och maskin inlärning.
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: quickstart
ms.date: 03/10/2020
ms.openlocfilehash: 4a414b706dffae76eaa9841ee7b1fe6bcc1ac0d3
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109852"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-linux-ubuntu"></a>Snabb start: Konfigurera Data Science Virtual Machine för Linux (Ubuntu)

Kom igång med Ubuntu 18,04 Data Science Virtual Machine.

## <a name="prerequisites"></a>Förutsättningar

Om du vill skapa en Ubuntu 18,04-Data Science Virtual Machine måste du ha en Azure-prenumeration. [Prova Azure kostnads fritt](https://azure.com/free).

>[!NOTE]
>Kostnads fria Azure-konton har inte stöd för GPU-aktiverade virtuella datorer SKU: er.

## <a name="create-your-data-science-virtual-machine-for-linux"></a>Skapa din Data Science Virtual Machine för Linux

Här följer stegen för att skapa en instans av Data Science Virtual Machine Ubuntu 18,04:

1. Öppna [Azure-portalen](https://portal.azure.com). Du kan uppmanas att logga in på ditt Azure-konto om du inte redan har loggat in.
1. Hitta listan med virtuella datorer genom att skriva in "data science Virtual Machine" och välja "Data Science Virtual Machine-Ubuntu 18,04"

1. I nästa fönster väljer du **skapa**.

1. Du bör omdirigeras till bladet "skapa en virtuell dator".
   
1. Ange följande information för att konfigurera varje steg i guiden:

    1. **Grunder**:
    
       * **Prenumeration**: om du har mer än en prenumeration väljer du den som datorn ska skapas i och faktureras. Du måste ha behörighet att skapa resurser för prenumerationen.
       * **Resurs grupp**: skapa en ny grupp eller Använd en befintlig grupp.
       * **Namn på virtuell dator**: Ange namnet på den virtuella datorn. Det här namnet kommer att användas i Azure Portal.
       * **Region**: Välj det data Center som är lämpligast. För snabbast nätverks åtkomst är det data Center som har de flesta data eller som är närmast din fysiska plats. Lär dig mer om [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/).
       * **Bild**: låt standardvärdet vara kvar.
       * **Storlek**: det här alternativet ska fyllas i automatiskt med en storlek som passar för allmänna arbets belastningar. Läs mer om [storlekar för virtuella Linux-datorer i Azure](../../virtual-machines/sizes.md).
       * **Autentiseringstyp**: Välj "lösen ord" för snabbare installation. 
         
         > [!NOTE]
         > Om du tänker använda JupyterHub väljer du "Password", eftersom JupyterHub *inte* har kon figurer ATS för att använda offentliga SSH-nycklar.

       * **Användar namn**: Ange administratörens användar namn. Du använder det här användar namnet för att logga in på den virtuella datorn. Det här användar namnet behöver inte vara detsamma som ditt Azure-användarnamn. Använd *inte* versala bokstäver.
         
         > [!IMPORTANT]
         > Om du använder versaler i ditt användar namn kommer JupyterHub inte att fungera och det uppstår ett internt 500-server fel.

       * **Lösen ord**: Ange det lösen ord som du använder för att logga in på den virtuella datorn.    
    
   1. Välj **Granska + skapa**.
   1. **Granska + skapa**
      * Kontrol lera att all information som du har angett är korrekt. 
      * Välj **Skapa**.
    
    Etableringen bör ta cirka 5 minuter. Statusen visas i Azure Portal.

## <a name="how-to-access-the-ubuntu-data-science-virtual-machine"></a>Så här kommer du åt Ubuntu-Data Science Virtual Machine

Du kan komma åt Ubuntu-DSVM på ett av tre sätt:

  * SSH för terminalsessioner
  * X2Go för grafiska sessioner
  * JupyterHub och JupyterLab för Jupyter Notebook

### <a name="ssh"></a>SSH

Om du har konfigurerat den virtuella datorn med SSH-autentisering kan du logga in med de kontoautentiseringsuppgifter som du skapade i avsnittet **grundläggande** i steg 3 för gränssnittet text gränssnitt. I Windows kan du ladda ned ett SSH client-verktyg som [SparaTillFil](https://www.putty.org). Om du föredrar ett grafiskt skriv bord (X Window-System) kan du använda begäran om x11-vidarebefordran på SparaTillFil.

> [!NOTE]
> X2Go-klienten utförde bättre än begäran om x11-vidarebefordran vid testning. Vi rekommenderar att du använder X2Go-klienten för ett grafiskt Skriv bords gränssnitt.

### <a name="x2go"></a>X2Go

Den virtuella Linux-datorn är redan etablerad med X2Go-servern och redo att ta emot klient anslutningar. För att ansluta till den virtuella Linux-datorns grafiska skrivbord, slutför du följande procedur på klienten:

1. Hämta och installera X2Go-klienten för klientplattformen från [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).
1. Anteckna den virtuella datorns offentliga IP-adress, som du hittar i Azure Portal genom att öppna den virtuella dator som du har skapat.

   ![Ubuntu dator-IP-adress](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

1. Kör X2Go-klienten. Om fönstret "ny session" inte visas automatiskt går du till session-> ny session.

1. I konfigurationsfönstret anger du följande konfigurationsparametrar:
   * **Fliken Session**:
     * **Värd**: Ange IP-adressen för din virtuella dator, som du antecknade tidigare.
     * **Logga in**: Ange användarnamnet på den virtuella Linux-datorn.
     * **SSH Port**: Lämna det på 22, standardvärdet.
     * **Sessionstyp**: Ändra värdet till **XFCE**. Den virtuella Linux-datorn har för närvarande endast stöd för XFCE Desktop.
   * **Fliken Media**: Du kan stänga av ljudsupporten och klientutskrift om du inte behöver använda dem.
   * **Delade mappar**: Använd den här fliken för att lägga till den klient dator katalog som du vill montera på den virtuella datorn. 

   ![X2go-konfiguration](./media/dsvm-ubuntu-intro/x2go-ubuntu.png)
1. Välj **OK**.
1. Klicka på rutan i den högra rutan i fönstret X2Go för att öppna inloggnings skärmen för den virtuella datorn.
1. Ange lösenordet för den virtuella datorn.
1. Välj **OK**.
1. Du kan behöva ge X2Go-behörighet för att kringgå brandväggen för att slutföra anslutningen.
1. Nu bör du se det grafiska gränssnittet för din Ubuntu-DSVM. 


### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub och JupyterLab

Ubuntu-DSVM kör [JupyterHub](https://github.com/jupyterhub/jupyterhub), en Jupyter-Server. Följ stegen nedan för att ansluta:

   1. Anteckna den offentliga IP-adressen för den virtuella datorn genom att söka efter och välja den virtuella datorn i Azure Portal.
      ![Ubuntu dator-IP-adress](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

   1. Från din lokala dator öppnar du en webbläsare och navigerar till https: \/ /Your-VM-IP: 8000, ersätter "Your-VM-IP" med IP-adressen som du antecknade tidigare.
   1. Webbläsaren hindrar förmodligen dig från att öppna sidan direkt, vilket innebär att det finns ett certifikat fel. DSVM tillhandahåller säkerhet via ett självsignerat certifikat. I de flesta webbläsare kan du klicka dig igenom efter den här varningen. Många webbläsare kommer även fortsättnings vis att tillhandahålla en viss typ av visuell varning om certifikatet i en webbsession.

      >[!NOTE]
      > Om du ser `ERR_EMPTY_RESPONSE` fel meddelandet i webbläsaren, se till att du har åtkomst till datorn genom att uttryckligen använda *https* -protokollet och inte med *http* eller bara webb adressen. Om du skriver in webb adressen utan `https://` i adress raden, kommer de flesta webbläsare att standardvärdet `http` och du ser det här felet.

   1. Ange det användar namn och lösen ord som du använde för att skapa den virtuella datorn och logga in. 

      ![Ange Jupyter-inloggning](./media/dsvm-ubuntu-intro/jupyter-login.png)

      >[!NOTE]
      > Om du får ett 500-fel i det här skedet är det troligt att du har använt versaler i ditt användar namn. Detta är en känd interaktion mellan Jupyter Hub och den PAMAuthenticator som används. Om du får fel meddelandet "det går inte att komma åt den här sidan" är det troligt att dina behörigheter för nätverks säkerhets gruppen måste justeras. I Azure Portal letar du reda på nätverks säkerhets grupp resursen i din resurs grupp. För att få åtkomst till JupyterHub från det offentliga Internet måste du ha port 8000 öppen. (Avbildningen visar att den virtuella datorn har kon figurer ATS för just-in-Time-åtkomst, vilket är starkt rekommenderat. Se [skydda dina hanterings portar med just-in-Time-åtkomst](../../security-center/security-center-just-in-time.md).) ![Konfiguration av nätverks säkerhets grupp](./media/dsvm-ubuntu-intro/nsg-permissions.png)

   1. Bläddra bland de många exempel antecknings böcker som är tillgängliga.

JupyterLab, nästa generation av Jupyter-anteckningsböcker och JupyterHub, är också tillgänglig. För att få åtkomst till den loggar du in på JupyterHub och bläddrar sedan till URL: en https: \/ /Your-VM-IP: 8000/User/your-username/Lab och ersätter "ditt-username" med det användar namn som du valde när du konfigurerade den virtuella datorn. Igen kan du från början blockeras från att komma åt webbplatsen på grund av ett certifikat fel.

Du kan ange JupyterLab som standard server för bärbara datorer genom att lägga till den här raden till `/etc/jupyterhub/jupyterhub_config.py` :

```python
c.Spawner.default_url = '/lab'
```

## <a name="next-steps"></a>Nästa steg

Så här kan du fortsätta din utbildning och utforskning:

* [Data vetenskap i data science Virtual Machine för Linux](linux-dsvm-walkthrough.md) -genom gången visar hur du utför flera vanliga data vetenskaps uppgifter med Linux-DSVM som tillhandahålls här. 
* Utforska de olika data vetenskaps verktygen på DSVM genom att prova de verktyg som beskrivs i den här artikeln. Du kan också köra `dsvm-more-info` på gränssnittet i den virtuella datorn för grundläggande introduktion och pekare till mer information om de verktyg som är installerade på den virtuella datorn.  
* Lär dig hur du systematiskt skapar analytiska lösningar med hjälp av [team data science-processen](../team-data-science-process/index.yml).
* Besök [Azure AI Gallery](https://gallery.azure.ai/) för Machine Learning-och data analys-exempel som använder Azure AI-tjänsterna.
* Se lämplig [referens dokumentation](./reference-ubuntu-vm.md) för den här virtuella datorn.