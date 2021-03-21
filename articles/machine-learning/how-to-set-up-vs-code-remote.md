---
title: Ansluta till beräknings instans i Visual Studio Code (för hands version)
titleSuffix: Azure Machine Learning
description: Lär dig hur du ansluter till en Azure Machine Learning beräknings instans i Visual Studio Code för att köra interaktiva Jupyter Notebook och arbets belastningar för fjärrutveckling.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: luquinta
author: luisquintanilla
ms.date: 11/16/2020
ms.openlocfilehash: 3f3794ed4748eda44e863ff4627dedc0a34691af
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98070107"
---
# <a name="connect-to-an-azure-machine-learning-compute-instance-in-visual-studio-code-preview"></a>Ansluta till en Azure Machine Learning beräknings instans i Visual Studio Code (för hands version)

I den här artikeln får du lära dig hur du ansluter till en Azure Machine Learning beräknings instans med hjälp av Visual Studio Code.

En [Azure Machine Learning beräknings instans](concept-compute-instance.md) är en fullständigt hanterad molnbaserad arbets station för data forskare och innehåller funktioner för hantering och företags beredskap för IT-administratörer.

Det finns två sätt som du kan ansluta till en beräknings instans från Visual Studio Code:

* Fjärr Jupyter Notebook Server. Med det här alternativet kan du ange en beräknings instans som en fjärr Jupyter Notebook Server.
* [Visual Studio Code fjärr utveckling](https://code.visualstudio.com/docs/remote/remote-overview). Med Visual Studio Code Remote Development kan du använda en behållare, fjärrdator eller Windows-undersystem för Linux (WSL) som en komplett utvecklings miljö.

## <a name="configure-compute-instance-as-remote-notebook-server"></a>Konfigurera beräknings instans som fjärran sluten Notebook Server

För att kunna konfigurera en beräknings instans som en fjärran sluten Jupyter Notebook server behöver du några krav:

* Azure Machine Learning Visual Studio Code-tillägg. Mer information finns i [installations guiden för Azure Machine Learning Visual Studio Code Extension](tutorial-setup-vscode-extension.md).
* Azure Machine Learning arbets yta. [Använd tillägget Azure Machine Learning Visual Studio Code för att skapa en ny arbets yta](how-to-manage-resources-vscode.md#create-a-workspace) om du inte redan har en.

Så här ansluter du till en beräknings instans:

1. Öppna en Jupyter Notebook i Visual Studio Code.
1. När den integrerade Notebook-upplevelsen läses in väljer du **Jupyter-Server**.

    > [!div class="mx-imgBorder"]
    > ![Starta Azure Machine Learning listruta för Jupyter Notebook Server](media/how-to-set-up-vs-code-remote/launch-server-selection-dropdown.png)

    Du kan också använda kommando-paletten:

    1. Öppna paletten kommando genom att välja **visa > kommando paletten** på Meny raden.
    1. Ange i text rutan `Azure ML: Connect to Compute instance Jupyter server` .

1. Välj i `Azure ML Compute Instances` listan över Jupyter-Server alternativ.
1. Välj din prenumeration i listan över prenumerationer. Om du tidigare har konfigurerat standard arbets ytan Azure Machine Learning hoppas det här steget över.
1. Välj din arbets yta.
1. Välj din beräknings instans i listan. Om du inte har en sådan, väljer du **Skapa ny Azure ml-beräkning-instans** och följer anvisningarna för att skapa en.
1. För att ändringarna ska börja gälla måste du läsa in Visual Studio Code igen.
1. Öppna en Jupyter Notebook och kör en cell.

> [!IMPORTANT]
> Du **måste** köra en cell för att kunna upprätta anslutningen.

Nu kan du fortsätta att köra celler i Jupyter Notebook.

> [!TIP]
> Du kan också arbeta med python-skriptfiler (. py) som innehåller Jupyter-liknande kod celler. Mer information finns i den [interaktiva dokumentationen för Visual Studio Code python](https://code.visualstudio.com/docs/python/jupyter-support-py).

## <a name="configure-compute-instance-remote-development"></a>Konfigurera fjärrutveckling av beräknings instanser

För en komplett fjärran sluten utvecklings miljö behöver du några krav:

* [Visual Studio Code Remote SSH-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh).
* SSH-aktiverad beräknings instans. Mer information [finns i guiden skapa en beräknings instans](how-to-create-manage-compute-instance.md).

> [!NOTE]
> På Windows-plattformar måste du [installera en OpenSSH-kompatibel SSH-klient](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) om en sådan inte redan finns. SparaTillFil stöds inte i Windows eftersom SSH-kommandot måste finnas i sökvägen.

### <a name="get-the-ip-and-ssh-port-for-your-compute-instance"></a>Hämta IP-och SSH-porten för beräknings instansen

1. Gå till Azure Machine Learning Studio på https://ml.azure.com/ .
2. Välj din [arbets yta](concept-workspace.md).
1. Klicka på fliken **beräknings instanser** .
1. I kolumnen **program-URI** klickar du på **SSH** -länken för den beräknings instans som du vill använda som fjärrberäkning. 
1. Anteckna IP-adressen och SSH-porten i dialog rutan. 
1. Spara din privata nyckel i katalogen ~/.ssh/på den lokala datorn. öppna exempelvis en redigerare för en ny fil och klistra in nyckeln i: 

   **Linux**:

   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Windows**:

   ```cmd
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa
   ```

   Den privata nyckeln ser ut ungefär så här:

   ```text
   -----BEGIN RSA PRIVATE KEY-----

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw==

   -----END RSA PRIVATE KEY-----
   ```

1. Ändra fil behörigheter för att se till att endast du kan läsa filen.  

   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa
   ```

### <a name="add-instance-as-a-host"></a>Lägg till instans som värd

Öppna filen `~/.ssh/config` (Linux) eller `C:\Users<username>.ssh\config` (Windows) i en redigerare och Lägg till en ny post som liknar innehållet nedan:

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa
```

Här finns information om fälten:

|Fält|Beskrivning|
|----|---------|
|Värd|Använd den stenografiska du gillar för beräknings instansen |
|HostName|Detta är beräknings instansens IP-adress |
|Port|Detta är porten som visas i dialog rutan SSH ovan |
|User|Detta måste vara `azureuser` |
|IdentityFile|Ska peka på filen där du sparade den privata nyckeln |

Nu bör du kunna använda SSH till din beräknings instans med hjälp av den kort skrift som du använde ovan `ssh azmlci1` .

### <a name="connect-vs-code-to-the-instance"></a>Ansluta VS-kod till instansen

1. Klicka på ikonen för fjärr-SSH från aktivitets fältet i Visual Studio Code för att visa dina SSH-konfigurationer.

1. Högerklicka på den SSH-värd konfiguration som du nyss skapade.

1. Välj **Anslut till värd i aktuellt fönster**. 

Härifrån är du helt igång med beräknings instansen och nu kan du redigera, felsöka, använda Git, använda tillägg osv. – precis som du kan med din lokala Visual Studio-kod.

## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat Visual Studio Code-fjärrhantering kan du använda en beräknings instans som fjärrberäkning från Visual Studio Code för att [interaktivt felsöka din kod](how-to-debug-visual-studio-code.md).

[Självstudie: träna din första ml-modell](tutorial-1st-experiment-sdk-train.md) visar hur du använder en beräknings instans med en integrerad antecknings bok.
