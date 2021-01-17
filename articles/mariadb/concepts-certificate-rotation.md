---
title: Certifikat rotation för Azure Database for MariaDB
description: Lär dig mer om kommande ändringar av rot certifikat ändringar som påverkar Azure Database for MariaDB
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: ac7019abab1aefaee95c155e34fbc0cb551b4d94
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2021
ms.locfileid: "98538433"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mariadb"></a>Förstå ändringarna i rot certifikat utfärdarens ändring för Azure Database for MariaDB

Azure Database for MariaDB kommer att ändra rot certifikatet för klient programmet/driv rutinen som är aktive rad med SSL, använda för att [ansluta till databas servern](concepts-connectivity-architecture.md). Rot certifikatet som för närvarande är tillgängligt upphör att gälla den 15 februari 2021 (02/15/2021) som en del av standard underhåll och rekommenderade säkerhets metoder. Den här artikeln innehåller mer information om kommande ändringar, vilka resurser som kommer att påverkas och de steg som krävs för att säkerställa att programmet upprätthåller anslutningen till databas servern.

>[!NOTE]
> Baserat på feedback från kunder har vi utökat rot certifikatets utfasning för vår befintliga Baltimore rot certifikat utfärdare från oktober 26, 2020 till 15 februari 2021. Vi hoppas att det här tillägget ger tillräckligt med ledtid för att våra användare ska kunna implementera klient ändringarna om de påverkas.

> [!NOTE]
> Den här artikeln innehåller referenser till termen _slav_, en term som Microsoft inte längre använder. När termen tas bort från program varan tar vi bort det från den här artikeln.

## <a name="what-update-is-going-to-happen"></a>Vilken uppdatering ska ske?

I vissa fall använder program en lokal certifikat fil som genererats från en betrodd certifikat utfärdare (CA) certifikat fil för att ansluta på ett säkert sätt. För närvarande kan kunder bara använda det fördefinierade certifikatet för att ansluta till en Azure Database for MariaDB-server som finns [här](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem). [Certifikat utfärdare (ca) webbläsarens forum](https://cabforum.org/)   publicerade nyligen rapporter om flera certifikat som utfärdats av ca-leverantörer som icke-kompatibla.

Som enligt branschens krav på efterlevnad började CA-leverantörer återkallade CA-certifikat för icke-kompatibla ca: er, vilket kräver att servrar använder certifikat som utfärdats av kompatibla ca: er och signeras av CA-certifikat från dessa kompatibla certifikat utfärdare. Eftersom Azure Database for MariaDB för närvarande använder ett av dessa icke-kompatibla certifikat, som klient program använder för att verifiera sina SSL-anslutningar, måste vi se till att lämpliga åtgärder vidtas (beskrivs nedan) för att minimera den potentiella påverkan på dina MariaDB-servrar.

Det nya certifikatet kommer att användas från och med 15 februari 2021 (02/15/2021). Om du använder antingen CA-validering eller fullständig verifiering av Server certifikatet vid anslutning från en MySQL-klient (sslmode = verify-ca eller sslmode = verify-full) måste du uppdatera program konfigurationen före 15 februari 2021 (02/15/2021).

## <a name="how-do-i-know-if-my-database-is-going-to-be-affected"></a>Hur gör jag för att veta om min databas kommer att påverkas?

Alla program som använder SSL/TLS och kontrollerar att rot certifikatet måste uppdatera rot certifikatet. Du kan identifiera om dina anslutningar verifierar rot certifikatet genom att granska anslutnings strängen.

- Om din anslutnings sträng innehåller `sslmode=verify-ca` eller `sslmode=verify-identity` måste du uppdatera certifikatet.
- Om din anslutnings sträng innehåller `sslmode=disable` , `sslmode=allow` , `sslmode=prefer` eller `sslmode=require` , behöver du inte uppdatera certifikat.
- Om anslutnings strängen inte anger sslmode behöver du inte uppdatera certifikat.

Om du använder en-klient som sammanfattar anslutnings strängen går du igenom klientens dokumentation för att ta reda på om den verifierar certifikat.
Mer information om Azure Database for MariaDB sslmode finns i [beskrivningarna för SSL-läge](concepts-ssl-connection-security.md#default-settings).

För att undvika att programmets tillgänglighet avbryts på grund av att certifikaten har återkallats, eller om du vill uppdatera ett certifikat som har återkallats, kan du läsa avsnittet [**Vad behöver jag göra för att underhålla anslutningen**](concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity) .

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>Vad behöver jag för att underhålla anslutningen

Om du vill undvika att programmets tillgänglighet avbryts på grund av att certifikaten har återkallats, eller om du vill uppdatera ett certifikat som har återkallats, följer du stegen nedan. Tanken är att skapa en ny *. pem* -fil som kombinerar det aktuella certifikatet och det nya och under verifieringen av SSL-certifikat efter de tillåtna värdena som ska användas. Se stegen nedan:

- Hämta **BaltimoreCyberTrustRoot**  &  **DigiCertGlobalRootG2** ca från länkarna nedan:

  - [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)
  - [https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)

- Generera ett kombinerat CA-certifikatarkiv med både **BaltimoreCyberTrustRoot** -och **DigiCertGlobalRootG2** -certifikat ingår.

  - För Java-användare (MariaDB Connector/J) kör du:

    ```console
    keytool -importcert -alias MariaDBServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
    ```

    ```console
    keytool -importcert -alias MariaDBServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
    ```

    Ersätt sedan den ursprungliga nyckel lagrings filen med den nya som genererade:

    - System. setProperty ("javax. net. SSL. trustStore", "path_to_truststore_file");
    - System. setProperty ("javax. net. SSL. trustStorePassword", "lösen ord");

  - För .NET-användare (MariaDB Connector/NET, MariaDBConnector) ser du till att **BaltimoreCyberTrustRoot** och **DigiCertGlobalRootG2** båda finns i Windows certifikat Arkiv, betrodda rot certifikat utfärdare. Importera certifikatet som saknas om det inte finns några certifikat.

    [![Azure Database for MariaDB .net-certifikat](media/overview/netconnecter-cert.png)](media/overview/netconnecter-cert.png#lightbox)

  - För .NET-användare på Linux med hjälp av SSL_CERT_DIR kontrollerar du att **BaltimoreCyberTrustRoot** och **DigiCertGlobalRootG2** båda finns i katalogen som anges av SSL_CERT_DIR. Om något certifikat inte finns skapar du den saknade certifikat filen.

  - För andra (MariaDB-klient/MariaDB Workbench/C/C++/Go/Python/Ruby/PHP/NodeJS/Perl/Swift)-användare kan du slå samman två CA-certifikatfiler som detta format nedan

   ```
   -----BEGIN CERTIFICATE-----
   (Root CA1: BaltimoreCyberTrustRoot.crt.pem)
   -----END CERTIFICATE-----
   -----BEGIN CERTIFICATE-----
   (Root CA2: DigiCertGlobalRootG2.crt.pem)
   -----END CERTIFICATE-----
   ```

- Ersätt den ursprungliga PEM-filen för rot certifikat utfärdaren med den kombinerade rot-CA-filen och starta om programmet/klienten.
- I framtiden kan du efter det nya certifikatet som distribuerats på Server sidan Ändra CA PEM-filen till DigiCertGlobalRootG2. CRT. pem.

## <a name="what-can-be-the-impact-of-not-updating-the-certificate"></a>Vad kan vara effekten av att inte uppdatera certifikatet?

Om du använder det Azure Database for MariaDB utfärdade certifikatet som dokumenteras här kan programmets tillgänglighet avbrytas eftersom databasen inte går att komma åt. Beroende på ditt program kan du få olika fel meddelanden, inklusive men inte begränsat till:

- Ogiltigt certifikat/återkallat certifikat
- Anslutningens tidsgräns uppnåddes

> [!NOTE]
> Ta inte bort eller ändra **Baltimore-certifikatet** förrän certifikat ändringen har gjorts. Vi kommer att skicka en kommunikation när ändringen har gjorts, och sedan är det säkert att släppa Baltimore-certifikatet.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="1-if-im-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. om jag inte använder SSL/TLS måste jag fortfarande uppdatera rot certifikat utfärdaren?

Inga åtgärder krävs om du inte använder SSL/TLS.

### <a name="2-if-im-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. om jag använder SSL/TLS måste jag starta om min databas server för att uppdatera rot certifikat utfärdaren?

Nej, du behöver inte starta om databas servern för att börja använda det nya certifikatet. Certifikat uppdateringen är en ändring på klient sidan och inkommande klient anslutningar måste använda det nya certifikatet för att säkerställa att de kan ansluta till databas servern.

### <a name="3-what-will-happen-if-i-dont-update-the-root-certificate-before-february-15-2021-02152021"></a>3. Vad händer om jag inte uppdaterar rot certifikatet före den 15 februari 2021 (02/15/2021)?

Om du inte uppdaterar rot certifikatet före den 15 februari 2021 (02/15/2021) kommer dina program som ansluter via SSL/TLS och verifiering för rot certifikatet inte att kunna kommunicera med MariaDB-databas servern och programmet kommer att uppleva anslutnings problem till MariaDB-databasservern.

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-mariadb"></a>4. Vad är effekten om du använder App Service med Azure Database for MariaDB?

För Azure App Services, anslutning till Azure Database for MariaDB, finns det två möjliga scenarier beroende på hur du använder SSL med ditt program.

- Det nya certifikatet har lagts till App Service på plattforms nivå. Om du använder SSL-certifikat som finns på App Service plattform i ditt program krävs ingen åtgärd.
- Om du uttryckligen inkluderar sökvägen till SSL-cert-filen i din kod måste du hämta det nya certifikatet och uppdatera koden för att använda det nya certifikatet. Ett användbart exempel på det här scenariot är när du använder anpassade behållare i App Service som delas i [App Service-dokumentationen](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress)

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-mariadb"></a>5. Vad är effekten om du använder Azure Kubernetes Services (AKS) med Azure Database for MariaDB?

Om du försöker ansluta till Azure Database for MariaDB med Azure Kubernetes Services (AKS), är det ungefär som att komma åt från en dedikerad kund värd miljö. Se stegen [här](../aks/ingress-own-tls.md).

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-mariadb"></a>6. Vad är effekten om du använder Azure Data Factory för att ansluta till Azure Database for MariaDB?

För Connector som använder Azure Integration Runtime använder anslutningen certifikat i Windows certifikat Arkiv i Azure-värd miljön. Dessa certifikat är redan kompatibla med de nyligen tillämpade certifikaten och ingen åtgärd krävs.

För koppling med egen värd Integration Runtime där du uttryckligen inkluderar sökvägen till SSL-cert-filen i anslutnings strängen måste du hämta det [nya certifikatet](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) och uppdatera anslutnings strängen för att använda den.

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7. behöver jag planera ett avbrott för databas server underhåll för den här ändringen?

Nej. Eftersom ändringen bara finns på klient sidan för att ansluta till databas servern, finns det inga underhålls avbrott som krävs för databas servern för den här ändringen.

### <a name="8--what-if-i-cant-get-a-scheduled-downtime-for-this-change-before-february-15-2021-02152021"></a>8. Vad händer om jag inte kan få en schemalagd stillestånds tid för den här ändringen före den 15 februari 2021 (02/15/2021)?

Eftersom klienterna som används för att ansluta till servern måste uppdatera certifikat informationen enligt beskrivningen i avsnittet åtgärda [här](./concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity), behöver vi inte några avbrott för-servern i det här fallet.

### <a name="9-if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>9. om jag skapar en ny server efter den 15 februari 2021 (02/15/2021) kommer jag att påverkas?

För servrar som skapats efter den 15 februari 2021 (02/15/2021) kan du använda det nyligen utfärdade certifikatet för dina program för att ansluta med SSL.

### <a name="10-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>10. hur ofta uppdaterar Microsoft sina certifikat eller vad är förfallo principen?

Dessa certifikat som används av Azure Database for MariaDB tillhandahålls av betrodda certifikat utfärdare (CA). Stöd för dessa certifikat på Azure Database for MariaDB är knutet till stöd för dessa certifikat av CA. Men i så fall kan det vara oförutsedda buggar i dessa fördefinierade certifikat, vilket måste åtgärdas tidigast.

### <a name="11-if-im-using-read-replicas-do-i-need-to-perform-this-update-only-on-source-server-or-the-read-replicas"></a>11. om jag använder Läs repliker behöver jag bara utföra den här uppdateringen på käll servern eller Läs replikerna?

Eftersom den här uppdateringen är en ändring på klient sidan, om klienten som används för att läsa data från replik servern, måste du även tillämpa ändringarna för dessa klienter.

### <a name="12-if-im-using-data-in-replication-do-i-need-to-perform-any-action"></a>12. behöver jag utföra någon åtgärd om jag använder datareplikering?

- Om datareplikeringen kommer från en virtuell dator (lokal eller virtuell Azure-dator) till Azure Database for MySQL måste du kontrol lera om SSL används för att skapa repliken. Kör **Visa slav status** och kontrol lera följande inställning.

    ```azurecli-interactive
    Master_SSL_Allowed            : Yes
    Master_SSL_CA_File            : ~\azure_mysqlservice.pem
    Master_SSL_CA_Path            :
    Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
    Master_SSL_Cipher             :
    Master_SSL_Key                : ~\azure_mysqlclient_key.pem
    ```

Om du använder [data i replikering](concepts-data-in-replication.md) för att ansluta till Azure Database for MySQL, finns det två saker att tänka på:

- Om datareplikeringen kommer från en virtuell dator (lokal eller virtuell Azure-dator) till Azure Database for MySQL måste du kontrol lera om SSL används för att skapa repliken. Kör **Visa slav status** och kontrol lera följande inställning. 

  ```azurecli-interactive
  Master_SSL_Allowed            : Yes
  Master_SSL_CA_File            : ~\azure_mysqlservice.pem
  Master_SSL_CA_Path            :
  Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
  Master_SSL_Cipher             :
  Master_SSL_Key                : ~\azure_mysqlclient_key.pem
  ```

  Om du ser certifikatet som tillhandahålls för CA_file, SSL_Cert och SSL_Key måste du uppdatera filen genom att lägga till det [nya certifikatet](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem).

- Om datareplikeringen är mellan två Azure Database for MySQL måste du återställa repliken genom att köra **anrop MySQL.az_replication_change_master** och ange det nya dubbla rot certifikatet som den sista parametern [master_ssl_ca](howto-data-in-replication.md#link-the-source-and-replica-servers-to-start-data-in-replication).

### <a name="13-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>13. har vi en fråga på Server sidan för att kontrol lera om SSL används?

För att kontrol lera om du använder SSL-anslutning för att ansluta till servern, se [SSL-verifiering](howto-configure-ssl.md#verify-the-ssl-connection).

### <a name="14-is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>14. finns det en åtgärd som behövs om jag redan har DigiCertGlobalRootG2 i min certifikat fil?

Nej. Det krävs ingen åtgärd om certifikat filen redan har **DigiCertGlobalRootG2**.

### <a name="15-what-if-i-have-further-questions"></a>15. Vad händer om jag har fler frågor?

Om du har frågor kan du få svar från community-experter i [Microsoft Q&A](mailto:AzureDatabaseformariadb@service.microsoft.com). [Kontakta oss](mailto:AzureDatabaseformariadb@service.microsoft.com)om du har en Support plan och behöver teknisk hjälp.
