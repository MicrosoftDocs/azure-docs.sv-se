---
title: Lär dig hur du använder en Apache Hadoop sandbox, emulator – Azure HDInsight
description: 'Om du vill börja lära dig att använda Apache Hadoop eko systemet kan du konfigurera en Hadoop-sandbox från Hortonworks på en virtuell Azure-dator. '
keywords: Hadoop-emulator, Hadoop sandbox
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: how-to
ms.date: 05/29/2019
ms.openlocfilehash: eb286adfd7267a78fcf80bcf5ad34f8f1cc9f493
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946620"
---
# <a name="get-started-with-an-apache-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Kom igång med en Apache Hadoop sandbox, en emulator på en virtuell dator

Lär dig hur du installerar Apache Hadoop sandbox från Hortonworks på en virtuell dator och lär dig mer om Hadoop-eko systemet. Sand boxen tillhandahåller en lokal utvecklings miljö för att lära sig om Hadoop, Hadoop Distributed File System (HDFS) och jobb överföring. När du är bekant med Hadoop kan du börja använda Hadoop på Azure genom att skapa ett HDInsight-kluster. Mer information om hur du kommer igång finns i [Kom igång med Hadoop på HDInsight](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Förutsättningar

* [Oracle-VirtualBox](https://www.virtualbox.org/). Hämta och installera den [här](https://www.virtualbox.org/wiki/Downloads).

## <a name="download-and-install-the-virtual-machine"></a>Hämta och installera den virtuella datorn

1. Bläddra till [Cloudera-filerna](https://www.cloudera.com/downloads/hortonworks-sandbox/hdp.html).

1. Klicka på **VIRTUALBOX** under **Välj Installations typ** för att ladda ned den senaste Hortonworks-sandbox: en på en virtuell dator. Logga in eller Slutför formuläret produkt intresse.

1. Klicka på knappen **HDP SANDBOX (senaste)** för att starta nedladdningen.

Instruktioner för hur du konfigurerar sandbox finns i [sandbox distribution och installations guide](https://hortonworks.com/tutorial/sandbox-deployment-and-install-guide/section/1/).

Om du vill ladda ned en äldre version sandbox av HDP, se länkarna under **äldre versioner**.

## <a name="start-the-virtual-machine"></a>Starta den virtuella datorn

1. Öppna Oracle VM-VirtualBox.
1. I menyn **Arkiv** klickar du på **Importera** installation och anger sedan Hortonworks sandbox-avbildningen.
1. Välj sandbox-Hortonworks, klicka på **Start** och sedan på **Normal start**. När den virtuella datorn har avslutat start processen visas inloggnings anvisningar.

    ![VirtualBox Manager normal start](./media/apache-hadoop-emulator-get-started/virtualbox-normal-start.png)

1. Öppna en webbläsare och gå till den URL som visas (vanligt vis `http://127.0.0.1:8888` ).

## <a name="set-sandbox-passwords"></a>Ange lösen ord för sandbox

1. Från steget **Kom igång** på sidan för Hortonworks-sandbox väljer du **Visa avancerade alternativ**. Använd informationen på den här sidan för att logga in på sand boxen med SSH. Använd det namn och lösen ord som angetts.

   > [!NOTE]
   > Om du inte har en SSH-klient installerad kan du använda den webbaserade SSH som tillhandahölls av den virtuella datorn på **http://localhost:4200/** .

    Första gången du ansluter med SSH uppmanas du att ändra lösen ordet för rot kontot. Ange ett nytt lösen ord som du använder när du loggar in med SSH.

2. När du har loggat in anger du följande kommando:

    ```bash
    ambari-admin-password-reset
    ```

    Ange ett lösen ord för administratörs kontot för Ambari när du uppmanas till det. Detta används när du öppnar Ambari-webbgränssnittet.

## <a name="use-hive-commands"></a>Använda Hive-kommandon

1. Använd följande kommando för att starta Hive-gränssnittet från en SSH-anslutning till sand boxen:

    ```bash
    hive
    ```

2. När gränssnittet har startats använder du följande för att visa de tabeller som ingår i sand boxen:

    ```hiveql
    show tables;
    ```

3. Använd följande för att hämta 10 rader från `sample_07` tabellen:

    ```hiveql
    select * from sample_07 limit 10;
    ```

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du använder Visual Studio med Hortonworks sandbox](./apache-hadoop-visual-studio-tools-get-started.md)

* [Lär dig mer om Hortonworks sand Box](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)

* [Hadoop-självstudie – komma igång med HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)