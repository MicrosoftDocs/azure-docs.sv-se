---
title: Apache Kafka TLS-kryptering & autentisering – Azure HDInsight
description: Konfigurera TLS-kryptering för kommunikation mellan Kafka-klienter och Kafka-utjämnare samt mellan Kafka-utjämnare. Konfigurera SSL-autentisering av klienter.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 05/01/2019
ms.openlocfilehash: 6c020153d5c5cb5aad593c5b15e60e67951b89d4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945191"
---
# <a name="set-up-tls-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Konfigurera TLS-kryptering och autentisering för Apache Kafka i Azure HDInsight

Den här artikeln visar hur du ställer in Transport Layer Security kryptering (TLS), som tidigare kallades Secure Sockets Layer (SSL)-kryptering, mellan Apache Kafka-klienter och Apache Kafka-utjämnare. Det visar också hur du konfigurerar autentisering av klienter (kallas ibland tvåvägs TLS).

> [!Important]
> Det finns två klienter som du kan använda för Kafka-program: en Java-klient och en konsol klient. Endast Java-klienten `ProducerConsumer.java` kan använda TLS för både produktion och användning. Konsolens producerande klient `console-producer.sh` fungerar inte med TLS.

> [!Note]
> HDInsight Kafka-konsolens tillverkare med version 1,1 stöder inte SSL.

## <a name="apache-kafka-broker-setup"></a>Installation av Apache Kafka Broker

Installations programmet för Kafka TLS Broker kommer att använda fyra virtuella HDInsight-kluster med virtuella datorer på följande sätt:

* huvudnoden 0 – certifikat utfärdare (CA)
* Worker-nod 0, 1 och 2-mäklare

> [!Note] 
> Den här guiden använder självsignerade certifikat, men den säkraste lösningen är att använda certifikat som utfärdats av betrodda certifikat utfärdare.

Sammanfattningen av installations processen för Broker är följande:

1. Följande steg upprepas på var och en av de tre arbetsnoderna:

    1. Generera ett certifikat.
    1. Skapa en begäran om certifikat signering.
    1. Skicka certifikat signerings förfrågan till certifikat utfärdaren (CA).
    1. Logga in på CA: n och signera begäran.
    1. SCP det signerade certifikatet tillbaka till arbetsnoden.
    1. SCP-certifikatets offentliga certifikat till arbets noden.

1. När du har alla certifikaten ska du placera certifikaten i certifikat arkivet.
1. Gå till Ambari och ändra konfigurationerna.

Använd följande detaljerade anvisningar för att slutföra konfigurationen av Service Broker:

> [!Important]
> I följande kodfragment är wnX en förkortning för en av de tre arbetsnoderna och bör ersättas med `wn0` `wn1` eller `wn2` efter behov. `WorkerNode0_Name` och `HeadNode0_Name` bör ersättas med namnen på respektive datorer.

1. Utför den första installationen på Head-noden 0, som för HDInsight ska fylla rollen för certifikat utfärdaren (CA).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Utför samma inledande installation på var och en av utjämnarna (arbetsnoderna 0, 1 och 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Utför följande steg på varje arbetsnoder med hjälp av kodfragmentet nedan.
    1. Skapa ett nyckel lager och fyll i det med ett nytt privat certifikat.
    1. Skapa en begäran om certifikat signering.
    1. SCP-begäran om certifikat signering till CA: n (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. Kör följande kommando på CA-datorn för att skapa ca-cert-och ca-nyckel filer:

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. Ändra till CA-datorn och signera alla mottagna signerings begär Anden för certifikat:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Skicka de signerade certifikaten tillbaka till arbetsnoderna från CA: n (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. Skicka certifikat utfärdarens offentliga certifikat till varje arbets nod.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. På varje arbetsnoden lägger du till det offentliga certifikat utfärdaren i trustStore och nyckel arkivet. Lägg sedan till arbetsnodens egna signerade certifikat i nyckel lagret

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-tls-and-restart-brokers"></a>Uppdatera Kafka-konfigurationen för att använda TLS och restart-utjämnare

Du har nu konfigurerat varje Kafka-koordinator med ett nyckel lager och trustStore och importerat rätt certifikat. Ändra sedan de relaterade konfigurations egenskaperna för Kafka med hjälp av Ambari och starta sedan om Kafka-utjämnare.

Slutför konfigurations ändringen genom att utföra följande steg:

1. Logga in på Azure Portal och välj ditt Azure HDInsight Apache Kafka-kluster.
1. Gå till Ambari-ANVÄNDARGRÄNSSNITTET genom att klicka på **Ambari start** under **kluster instrument paneler**.
1. Under **Kafka Broker** anger du egenskapen **Listeners** till `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. Under **Advanced Kafka-Broker** ställer du in egenskapen **Security. Interservice Broker. Protocol** till `SSL`

    ![Redigera Kafka egenskaper för SSL-konfiguration i Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. Under **anpassad Kafka-Broker** anger du egenskapen **SSL. client. auth** till `required` . Det här steget krävs bara om du konfigurerar autentisering och kryptering.

    ![Redigera Kafka egenskaper för SSL-konfiguration i Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. För HDI version 3,6 går du till Ambari UI och lägger till följande konfigurationer under **Avancerad Kafka-kuvert** och **mallen Kafka-miljö** .

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Här är skärm bilden som visar Ambari konfigurations gränssnitt med de här ändringarna.

    För HDI version 3,6:

    ![Redigera Kafka-miljö-mallegenskap i Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png)

    För HDI version 4,0:

     ![Redigera Kafka-miljö mal len i Ambari fyra](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env-four.png)

1. Starta om alla Kafka-utjämnare.

## <a name="client-setup-without-authentication"></a>Klient installation (utan autentisering)

Om du inte behöver autentisering är sammanfattningen av stegen för att ställa in endast TLS-kryptering:

1. Logga in på CA: n (aktiva Head-noden).
1. Kopiera CA-certifikatet till klient datorn från CA-datorn (wn0).
1. Logga in på klient datorn (HN1) och navigera till `~/ssl` mappen.
1. Importera CA-certifikatet till trustStore.
1. Importera certifikat utfärdarens certifikat till nyckel arkivet.

De här stegen beskrivs i följande kodfragment.

1. Logga in på CA-noden.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    ```

1. Kopiera CA-certifikatet till klient datorn

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. Logga in på klient datorn (standby-Head-noden).

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. Importera CA-certifikatet till trustStore.

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Importera certifikat utfärdarens certifikat till nyckel arkivet.
    
    ```bash
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Skapa filen `client-ssl-auth.properties` på klient datorn (HN1). Den bör ha följande rader:

    ```config
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ```

1. Starta administratörs klienten med producent-och konsument alternativ för att kontrol lera att både tillverkare och konsumenter arbetar på port 9093. Se avsnittet [verifiering](apache-kafka-ssl-encryption-authentication.md#verification) nedan för steg som behövs för att verifiera installationen med hjälp av konsolens tillverkare/konsument.

## <a name="client-setup-with-authentication"></a>Klient installation (med autentisering)

> [!Note]
> Följande steg krävs endast om du konfigurerar både TLS-kryptering **och** autentisering. Om du bara konfigurerar kryptering kan du se [klient konfiguration utan autentisering](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication).

Följande fyra steg sammanfattar de uppgifter som krävs för att slutföra klient installationen:

1. Logga in på klient datorn (standby-Head-noden).
1. Skapa ett Java-nyckel lager och hämta ett signerat certifikat för koordinatorn. Kopiera sedan certifikatet till den virtuella dator där CA: n körs.
1. Växla till CA-datorn (den aktiva Head-noden) för att signera klient certifikatet.
1. Gå till klient datorn (standby-Head-noden) och navigera till `~/ssl` mappen. Kopiera det signerade certifikatet till klient datorn.

Information om varje steg anges nedan.

1. Logga in på klient datorn (standby-Head-noden).

    ```bash
    ssh sshuser@HeadNode1_Name
    ```

1. Ta bort alla befintliga SSL-kataloger.

    ```bash
    rm -R ~/ssl
    mkdir ssl
    cd ssl
    ```

1. Skapa en Java-nyckel lagring och skapa en begäran om certifikat signering. 

    ```bash
    keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=HEADNODE1_FQDN" -storetype pkcs12
    
    keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -storepass "MyClientPassword123" -keypass "MyClientPassword123"
    ```

1. Kopiera certifikat signerings förfrågan till CA: n

    ```bash
    scp client-cert-sign-request sshuser@HeadNode0_Name:~/ssl/client-cert-sign-request
    ```

1. Växla till CA-datorn (aktiva Head-noden) och signera klient certifikatet.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    openssl x509 -req -CA ca-cert -CAkey ca-key -in ~/ssl/client-cert-sign-request -out ~/ssl/client-cert-signed -days 365 -CAcreateserial -passin pass:MyClientPassword123
    ```

1. Kopiera signerat klient certifikat från certifikat utfärdaren (Active Head Node) till klient datorn.

    ```bash
    scp client-cert-signed sshuser@HeadNode1_Name:~/ssl/client-signed-cert
    ```

1. Kopiera CA-certifikatet till klient datorn

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

    1. Logga in på klient datorn (standby-Head-noden) och gå till SSL-katalogen.

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. Skapa klient Arkiv med signerat certifikat och importera certifikat utfärdarens certifikat till nyckel lagrings platsen och trustStore på klient datorn (HN1):

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Skapa en fil `client-ssl-auth.properties` på klient datorn (HN1). Den bör ha följande rader:

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=MyClientPassword123
    ssl.key.password=MyClientPassword123
    ```

## <a name="verification"></a>Verifiering

Kör de här stegen på klient datorn.

> [!Note]
> Om HDInsight 4,0 och Kafka 2,1 har installerats kan du använda konsolens tillverkare/användare för att verifiera installationen. Om inte, kör du Kafka-producenten på port 9092 och skickar meddelanden till ämnet och använder sedan Kafka-konsumenten på port 9093 som använder TLS.

### <a name="kafka-21-or-above"></a>Kafka 2,1 eller senare

1. Skapa ett ämne om det inte redan finns.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. Starta konsolens tillverkare och ange sökvägen till `client-ssl-auth.properties` som en konfigurations fil för producenten.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9093 --topic topic1 --producer.config ~/ssl/client-ssl-auth.properties
    ```

1. Öppna en annan ssh-anslutning till klient datorn och starta konsolens konsument och ange sökvägen till `client-ssl-auth.properties` som en konfigurations fil för konsumenten.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

### <a name="kafka-11"></a>Kafka 1,1

1. Skapa ett ämne om det inte redan finns.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE_0>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. Starta konsolens tillverkare och ange sökvägen till klienten-SSL-auth. Properties som en konfigurations fil för producenten.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9092 --topic topic1 
    ```

1. Öppna en annan ssh-anslutning till klient datorn och starta konsolens konsument och ange sökvägen till `client-ssl-auth.properties` som en konfigurations fil för konsumenten.

    ```bash
    $ /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

## <a name="next-steps"></a>Nästa steg

* [Vad är Apache Kafka på HDInsight?](apache-kafka-introduction.md)
