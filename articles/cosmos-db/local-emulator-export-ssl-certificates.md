---
title: Exportera Azure Cosmos DB emulatorcertifikat
description: Lär dig hur du exporterar Azure Cosmos DB emulator-certifikatet för användning med Java, python och Node.js appar. Certifikaten bör exporteras och användas för språk-och körnings miljöer som inte använder Windows certifikat arkiv.
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/17/2020
author: deborahc
ms.author: dech
ms.custom: devx-track-python, devx-track-java, contperf-fy21q1
ms.openlocfilehash: 952be09662c2c74f883d63de72bba2b9cb58d0e0
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554011"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs-apps"></a>Exportera Azure Cosmos DB emulator-certifikat för användning med Java-, python-och Node.js-appar
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB-emulatorn ger en lokal miljö som emulerar Azure Cosmos DB-tjänsten för utveckling. Azure Cosmos DB emulatorn stöder endast säker kommunikation via TLS-anslutningar.

Certifikaten i Azure Cosmos DB lokala emulatorn skapas första gången du kör emulatorn. Det finns två certifikat. En av dem används för att ansluta till den lokala emulatorn och den andra används för att hantera standard kryptering av emulatorns data i emulatorn. Det certifikat som du ska exportera är anslutningscertifikatet med namnet "DocumentDBEmulatorCertificate".

När du använder emulatorn för att utveckla appar på olika språk, till exempel Java, python eller Node.js, måste du exportera emulatorns certifikat och importera det till det begärda certifikat arkivet.

.NET-språket och körnings miljön använder Windows certifikat Arkiv för att på ett säkert sätt ansluta till den Azure Cosmos DB lokala emulatorn när programmet körs på en Windows OS-värd. Andra språk har sin egen metod för att hantera och använda certifikat. Till exempel använder Java det egna [certifikat arkivet](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), python använder [socket-omslag](https://docs.python.org/2/library/ssl.html)och Node.js använder [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

Den här artikeln visar hur du exporterar TLS/SSL-certifikat för användning i olika språk och körnings miljöer som inte integreras med Windows certifikat arkiv. Du kan läsa mer om emulatorn i [Använda Azure Cosmos DB-emulatorn för utveckling och testning](./local-emulator.md).

## <a name="export-the-azure-cosmos-db-tlsssl-certificate"></a><a id="export-emulator-certificate"></a>Exportera Azure Cosmos DB TLS/SSL-certifikat

Du måste exportera emulator-certifikatet för att kunna använda emulatorns slut punkt från språk och körnings miljöer som inte integreras med Windows certifikat arkiv. Du kan exportera certifikatet med hjälp av certifikat hanteraren i Windows. Använd följande steg-för-steg-instruktioner för att exportera "DocumentDBEmulatorCertificate"-certifikatet som en BASE-64-kodad X. 509-fil (. cer):

1. Starta certifikathanteraren i Windows genom att köra certlm.msc och gå till mappen Personligt -> Certifikat. Öppna certifikatet med namnet **DocumentDbEmulatorCertificate**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png" alt-text="Steg 1 för att exportera den lokala Azure Cosmos DB-emulatorn":::

1. Klicka på **Detaljer** och sedan **OK**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png" alt-text="Steg 2 för att exportera den lokala Azure Cosmos DB-emulatorn":::

1. Klicka på **Kopiera till fil...**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png" alt-text="Steg 3 för att exportera den lokala Azure Cosmos DB-emulatorn":::

1. Klicka på **Nästa**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png" alt-text="Steg 4 för att exportera den lokala Azure Cosmos DB-emulatorn":::

1. Klicka på **Nej, exportera inte den privata nyckeln** och sedan på **Nästa**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png" alt-text="Steg 5 för att exportera den lokala Azure Cosmos DB-emulatorn":::

1. Klicka på **Base-64 encoded X.509 (.CER)** och sedan på **Nästa**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png" alt-text="Steg 6 för att exportera den lokala Azure DB Cosmos DB-emulatorn":::

1. Ge certifikatet ett namn. I det här fallet är det **documentdbemulatorcert**. Klicka sedan på **Nästa**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png" alt-text="Steg 7 för att exportera den lokala Azure DB Cosmos DB-emulatorn":::

1. Klicka på **Finish**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png" alt-text="Steg 8 för att exportera den lokala Azure DB Cosmos DB-emulatorn":::

## <a name="use-the-certificate-with-java-apps"></a>Använda certifikatet med Java-appar

När du kör Java-program eller MongoDB-program som använder en Java-baserad klient är det enklare att installera certifikatet i Java standard certifikat arkivet än att skicka `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"` flaggorna. Till exempel är det inkluderade java demo programmet ( `https://localhost:8081/_explorer/index.html` ) beroende av standard certifikat arkivet.

Följ instruktionerna i arkivet för att [lägga till ett certifikat i Java-certifikaten](https://docs.oracle.com/cd/E54932_01/doc.705/e54936/cssg_create_ssl_cert.htm) för att importera X. 509-certifikatet till standard-Java-certifikatarkivet. Kom ihåg att du arbetar i katalogen *% JAVA_HOME%* när du kör-verktyget. När certifikatet har importer ATS till certifikat arkivet kommer klienter för SQL och Azure Cosmos DBs API för MongoDB att kunna ansluta till Azure Cosmos DB-emulatorn.

Du kan också köra följande bash-skript för att importera certifikatet:

```bash
#!/bin/bash

# If emulator was started with /AllowNetworkAccess, replace the below with the actual IP address of it:
EMULATOR_HOST=localhost
EMULATOR_PORT=8081
EMULATOR_CERT_PATH=/tmp/cosmos_emulator.cert
openssl s_client -connect ${EMULATOR_HOST}:${EMULATOR_PORT} </dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > $EMULATOR_CERT_PATH
# delete the cert if already exists
sudo $JAVA_HOME/bin/keytool -cacerts -delete -alias cosmos_emulator
# import the cert
sudo $JAVA_HOME/bin/keytool -cacerts -importcert -alias cosmos_emulator -file $EMULATOR_CERT_PATH
```

När TLS/SSL-certifikatet "CosmosDBEmulatorCertificate" har installerats ska programmet kunna ansluta och använda den lokala Azure Cosmos DB-emulatorn. Om du har problem kan du följa artikeln [FELSÖKA SSL/TLS-anslutningar](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) . I de flesta fall kanske certifikatet inte har installerats i *% JAVA_HOME%/jre/lib/security/cacerts* -butiken. Om du till exempel har flera installerade versioner av Java-programmet kanske du använder ett annat cacerts-Arkiv än det som du har uppdaterat.

## <a name="use-the-certificate-with-python-apps"></a>Använd certifikatet med python-appar

När du ansluter till emulatorn från python-appar är TLS-verifiering inaktiverat. Som standard försöker [python SDK (version 2.0.0 eller högre)](sql-api-sdk-python.md) för SQL API inte att använda TLS/SSL-certifikatet vid anslutning till den lokala emulatorn. Om du vill använda TLS-verifiering kan du följa exemplen i dokumentationen för [python socket-omslag](https://docs.python.org/2/library/ssl.html) .

## <a name="how-to-use-the-certificate-in-nodejs"></a>Så här använder du certifikat i Node.js

När du ansluter till emulatorn från Node.js SDK: er är TLS-verifiering inaktiverat. Som standard försöker [Node.js SDK (version 1.10.1 eller högre)](sql-api-sdk-node.md) för SQL-API: t inte använda TLS/SSL-certifikatet vid anslutning till den lokala emulatorn. Om du däremot vill använda TLS-verifiering kan du följa exemplen iNode.js- [ dokumentationen](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="rotate-emulator-certificates"></a>Rotera emulator-certifikat

Du kan framtvinga återskapande av emulatorns certifikat genom att välja **Återställ data** från Azure Cosmos DB-emulatorn som körs i Windows-fältet. Observera att den här åtgärden också raderar alla data som lagras lokalt av emulatorn.

:::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png" alt-text="Återställa data för den lokala Azure Cosmos DB-emulatorn":::

Om du har installerat certifikatet i Java-certifikatarkivet eller använt dem någon annan stans måste du importera dem igen med hjälp av aktuella certifikat. Programmet kan inte ansluta till den lokala emulatorn förrän du har uppdaterat certifikaten.

## <a name="next-steps"></a>Nästa steg

* [Använd kommando rads parametrar och PowerShell-kommandon för att styra emulatorn](emulator-command-line-parameters.md)
* [Felsöka problem med emulatorn](troubleshoot-local-emulator.md)
