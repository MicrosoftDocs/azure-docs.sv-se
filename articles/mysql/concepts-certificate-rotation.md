---
title: Certifikat rotation för Azure Database for MySQL
description: Lär dig mer om kommande ändringar av rot certifikat ändringar som påverkar Azure Database for MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 4b218a9481fdd5970fd6fc8fa6a1d071161e5b58
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313372"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mysql-single-server"></a>Förstå ändringarna i rot certifikat utfärdarens ändring för Azure Database for MySQL enskild server

Azure Database for MySQL en enda server slutförde rot certifikats ändringen den **15 februari 2021 (02/15/2021)** som en del av standard underhåll och säkerhets metoder. Den här artikeln innehåller mer information om ändringarna, de resurser som påverkas och de steg som krävs för att säkerställa att programmet upprätthåller anslutningen till databas servern.

> [!NOTE]
> Den här artikeln gäller endast för [Azure Database for MySQL-enskild server](single-server-overview.md) . För [Azure Database for MySQL-flexibel Server](flexible-server/overview.md)är det certifikat som krävs för att kommunicera via SSL [DigiCert global rot certifikat utfärdare](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)
>
> Den här artikeln innehåller referenser till termen _slav_, en term som Microsoft inte längre använder. När termen tas bort från program varan tar vi bort det från den här artikeln.
>

#### <a name="why-is-a-root-certificate-update-required"></a>Varför krävs en rot certifikat uppdatering?

Azure Database for MySQL användare kan endast använda det fördefinierade certifikatet för att ansluta till MySQL-servern, som finns [här](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem). [Certifikat utfärdare (ca) webbläsarens forum](https://cabforum.org/)   publicerade nyligen rapporter om flera certifikat som utfärdats av ca-leverantörer som icke-kompatibla.

Enligt branschens krav började CA-leverantörer återkalla CA-certifikat för icke-kompatibla certifikat utfärdare, vilket kräver att servrar använder certifikat som utfärdats av kompatibla ca: er och signerade av CA-certifikat från dessa kompatibla certifikat utfärdare. Eftersom Azure Database for MySQL använde något av dessa icke-kompatibla certifikat behövde vi att rotera certifikatet till den kompatibla versionen för att minimera det potentiella hotet till MySQL-servrarna.

Det nya certifikatet distribueras och gäller från och med den 15 februari 2021 (02/15/2021).

#### <a name="what-change-was-performed-on-february-15-2021-02152021"></a>Vilken ändring utfördes den 15 februari 2021 (02/15/2021)?

Den 15 februari 2021 ersattes [rot certifikatet för BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) med en **kompatibel version** av samma [BaltimoreCyberTrustRoot-rotcertifikat](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) för att se till att befintliga kunder inte behöver ändra något och det inte påverkar sina anslutningar till servern. Under den här ändringen har [BaltimoreCyberTrustRoot-rotcertifikatet](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) **inte ersatts** med [DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) och denna ändring uppskjuts för att tillåta mer tid för kunderna att göra ändringen.

#### <a name="do-i-need-to-make-any-changes-on-my-client-to-maintain-connectivity"></a>Behöver jag göra några ändringar på klienten för att upprätthålla anslutningen?

Ingen ändring krävs på klient sidan. Om du har följt vår tidigare rekommendation nedan kan du fortsätta att ansluta så länge **BaltimoreCyberTrustRoot-certifikatet inte tas bort** från det kombinerade CA-certifikatet. **För att upprätthålla anslutningen rekommenderar vi att du behåller BaltimoreCyberTrustRoot i ditt kombinerade CA-certifikat tills vidare.**

###### <a name="previous-recommendation"></a>Föregående rekommendation

Använd följande steg för att undvika avbrott i programmets tillgänglighet till följd av certifikat som har återkallats eller för att uppdatera ett certifikat som har återkallats. Tanken är att skapa en ny *. pem* -fil, som kombinerar det aktuella certifikatet och det nya och under verifieringen av SSL-certifikat, och ett av de tillåtna värdena kommer att användas. Se följande steg:

1. Hämta BaltimoreCyberTrustRoot & DigiCertGlobalRootG2 rot certifikat utfärdare från följande länkar:

    * [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)
    * [https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)

2. Generera ett kombinerat CA-certifikatarkiv med både **BaltimoreCyberTrustRoot** -och **DigiCertGlobalRootG2** -certifikat ingår.

    * För Java-användare (MySQL Connector/J) kör du:

      ```console
      keytool -importcert -alias MySQLServerCACert -file D:\BaltimoreCyberTrustRoot.crt.pem -keystore truststore -storepass password -noprompt
      ```

      ```console
      keytool -importcert -alias MySQLServerCACert2 -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password -noprompt
      ```

      Ersätt sedan den ursprungliga nyckel lagrings filen med den nya som genererade:

      * System. setProperty ("javax. net. SSL. trustStore", "path_to_truststore_file");
      * System. setProperty ("javax. net. SSL. trustStorePassword", "lösen ord");

    * För .NET-användare (MySQL Connector/NET, MySQLConnector) kontrollerar du att **BaltimoreCyberTrustRoot** och **DigiCertGlobalRootG2** finns både i Windows certifikat Arkiv, betrodda rot certifikat utfärdare. Importera certifikatet som saknas om det inte finns några certifikat.

      :::image type="content" source="media/overview/netconnecter-cert.png" alt-text="Azure Database for MySQL .NET-cert diagram":::

    * För .NET-användare på Linux med hjälp av SSL_CERT_DIR kontrollerar du att **BaltimoreCyberTrustRoot** och **DigiCertGlobalRootG2** båda finns i katalogen som anges av SSL_CERT_DIR. Om något certifikat inte finns skapar du den saknade certifikat filen.

    * För andra (MySQL-klienter/MySQL Workbench/C/C++/Go/Python/Ruby/PHP/NodeJS/Perl/Swift) användare kan du slå samman två CA-certifikatfiler i följande format:

      ```
      -----BEGIN CERTIFICATE-----
      (Root CA1: BaltimoreCyberTrustRoot.crt.pem)
      -----END CERTIFICATE-----
      -----BEGIN CERTIFICATE-----
      (Root CA2: DigiCertGlobalRootG2.crt.pem)
      -----END CERTIFICATE-----
      ```

3. Ersätt den ursprungliga PEM-filen för rot certifikat utfärdaren med den kombinerade rot-CA-filen och starta om programmet/klienten.

   I framtiden kan du när det nya certifikatet har distribuerats på Server sidan Ändra CA PEM-filen till DigiCertGlobalRootG2. CRT. pem.

> [!NOTE]
> Ta inte bort eller ändra **Baltimore-certifikatet** förrän certifikat ändringen har gjorts. Vi skickar en kommunikation när ändringen har gjorts och sedan är det säkert att släppa **Baltimore-certifikatet**.

#### <a name="why-was-baltimorecybertrustroot-certificate-not-replaced-to-digicertglobalrootg2-during-this-change-on-february-15-2021"></a>Varför bytte BaltimoreCyberTrustRoot-certifikatet inte till DigiCertGlobalRootG2 under den här ändringen den 15 februari 2021?

Vi utvärderade kundens beredskap för den här ändringen och påstod att många kunder sökte efter extra ledtid för att hantera den här ändringen. För att ge kunderna mer ledtid för beredskap, beslutade vi att skjuta upp certifikat ändringen till DigiCertGlobalRootG2 under minst ett år, vilket ger tillräckligt med ledtid för kunderna och slutanvändarna.

Vår rekommendation till användare är att använda de ovan nämnda stegen för att skapa ett kombinerat certifikat och ansluta till servern, men ta inte bort BaltimoreCyberTrustRoot-certifikatet förrän vi skickar en kommunikation för att ta bort den.

#### <a name="what-if-we-removed-the-baltimorecybertrustroot-certificate"></a>Vad händer om vi har tagit bort BaltimoreCyberTrustRoot-certifikatet?

Du börjar med att stöta på anslutnings fel när du ansluter till din Azure Database for MySQL-server. Du måste [Konfigurera SSL](howto-configure-ssl.md) med [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) -certifikatet igen för att upprätthålla anslutningen.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

#### <a name="if-im-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>Om jag inte använder SSL/TLS måste jag fortfarande uppdatera rot certifikat utfärdaren?

  Inga åtgärder krävs om du inte använder SSL/TLS.

#### <a name="if-im-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>Om jag använder SSL/TLS måste jag starta om min databas server för att uppdatera rot certifikat utfärdaren?

Nej, du behöver inte starta om databas servern för att börja använda det nya certifikatet. Detta rot certifikat är en ändring på klient sidan och inkommande klient anslutningar måste använda det nya certifikatet för att säkerställa att de kan ansluta till databas servern.

#### <a name="how-do-i-know-if-im-using-ssltls-with-root-certificate-verification"></a>Hur gör jag för att veta om jag använder SSL/TLS med verifiering av rot certifikat?

Du kan identifiera om dina anslutningar verifierar rot certifikatet genom att granska anslutnings strängen.

* Om din anslutnings sträng innehåller `sslmode=verify-ca` eller `sslmode=verify-identity` måste du uppdatera certifikatet.
* Om din anslutnings sträng innehåller `sslmode=disable` , `sslmode=allow` , `sslmode=prefer` eller `sslmode=require` , behöver du inte uppdatera certifikat.
* Om anslutnings strängen inte anger sslmode behöver du inte uppdatera certifikat.

Om du använder en-klient som sammanfattar anslutnings strängen går du igenom klientens dokumentation för att ta reda på om den verifierar certifikat.

#### <a name="what-is-the-impact-of-using-app-service-with-azure-database-for-mysql"></a>Vad är effekten av att använda App Service med Azure Database for MySQL?

När Azure App Services ansluter till Azure Database for MySQL finns det två möjliga scenarier beroende på hur du använder SSL med ditt program.

* Det nya certifikatet har lagts till App Service på plattforms nivå. Om du använder SSL-certifikat som finns på App Service plattform i ditt program krävs ingen åtgärd. Detta är det vanligaste scenariot.
* Om du uttryckligen inkluderar sökvägen till SSL-cert-filen i din kod måste du hämta det nya certifikatet och skapa ett kombinerat certifikat som nämnts ovan och använda certifikat filen. Ett användbart exempel på det här scenariot är när du använder anpassade behållare i App Service som delas i [App Service-dokumentationen](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress). Detta är ett ovanligt scenario men vi har sett vissa användare som använder detta.

#### <a name="what-is-the-impact-of-using-azure-kubernetes-services-aks-with-azure-database-for-mysql"></a>Vad är effekten av att använda Azure Kubernetes Services (AKS) med Azure Database for MySQL?

Om du försöker ansluta till Azure Database for MySQL med Azure Kubernetes Services (AKS), är det ungefär som att komma åt från en dedikerad kund värd miljö. Se stegen [här](../aks/ingress-own-tls.md).

#### <a name="what-is-the-impact-of-using-azure-data-factory-to-connect-to-azure-database-for-mysql"></a>Vad händer om du använder Azure Data Factory för att ansluta till Azure Database for MySQL?

För en anslutning som använder Azure Integration Runtime använder anslutningen certifikat i Windows certifikat Arkiv i Azure-värd miljön. Dessa certifikat är redan kompatibla med de nyligen tillämpade certifikaten och därför behövs ingen åtgärd.

För en anslutning som använder sig av egen värd Integration Runtime där du uttryckligen inkluderar sökvägen till SSL-cert-filen i anslutnings strängen, måste du hämta det [nya certifikatet](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) och uppdatera anslutnings strängen för att använda den.

#### <a name="do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>Måste jag planera ett avbrott för databas server underhåll för den här ändringen?

Nej. Eftersom ändringen bara finns på klient sidan för att ansluta till databas servern finns det inga underhålls avbrott som krävs för databas servern för den här ändringen.

#### <a name="if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>Kommer jag att påverkas om jag skapar en ny server efter den 15 februari 2021 (02/15/2021)?

För servrar som skapats efter den 15 februari 2021 (02/15/2021) kommer du fortsätta att använda [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) för dina program för att ansluta med SSL.

#### <a name="how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>Hur ofta uppdaterar Microsoft sina certifikat eller vad är förfallo principen?

Dessa certifikat som används av Azure Database for MySQL tillhandahålls av betrodda certifikat utfärdare (CA). Detta innebär att stöd för dessa certifikat är knutet till stöd för dessa certifikat av CA. [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) -certifikatet är schemalagt att förfalla i 2025 så att Microsoft måste utföra en ändring av certifikatet innan förfallo datum. Även om det finns oförutsedda buggar i dessa fördefinierade certifikat, måste Microsoft göra certifikats rotationen på det tidigaste sätt som genomfördes den 15 februari 2021 för att säkerställa att tjänsten är säker och kompatibel hela tiden.

#### <a name="if-im-using-read-replicas-do-i-need-to-perform-this-update-only-on-source-server-or-the-read-replicas"></a>Behöver jag bara utföra den här uppdateringen på käll servern eller Läs replikerna om jag använder Läs repliker?

Eftersom den här uppdateringen är en ändring på klient sidan, om klienten som används för att läsa data från replik servern, måste du även tillämpa ändringarna för dessa klienter.

#### <a name="if-im-using-data-in-replication-do-i-need-to-perform-any-action"></a>Behöver jag utföra någon åtgärd om jag använder datareplikering?

Om du använder [data i replikering](concepts-data-in-replication.md) för att ansluta till Azure Database for MySQL, finns det två saker att tänka på:

* Om datareplikeringen kommer från en virtuell dator (lokal eller virtuell Azure-dator) till Azure Database for MySQL måste du kontrol lera om SSL används för att skapa repliken. Kör **Visa slav status** och kontrol lera följande inställning.  

    ```azurecli-interactive
    Master_SSL_Allowed            : Yes
    Master_SSL_CA_File            : ~\azure_mysqlservice.pem
    Master_SSL_CA_Path            :
    Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
    Master_SSL_Cipher             :
    Master_SSL_Key                : ~\azure_mysqlclient_key.pem
    ```

    Om du ser att certifikatet har angetts för CA_file, SSL_Cert och SSL_Key måste du uppdatera filen genom att lägga till det [nya certifikatet](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) och skapa en kombinerad certifikat fil.

* Om datareplikeringen är mellan två Azure Database for MySQL-servrar måste du återställa repliken genom att köra **anrops MySQL.az_replication_change_master** och ange det nya dubbla rot certifikatet som den sista parametern [master_ssl_ca](howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication).

#### <a name="is-there-a-server-side-query-to-determine-whether-ssl-is-being-used"></a>Finns det en fråga på Server sidan för att avgöra om SSL används?

För att kontrol lera om du använder SSL-anslutning för att ansluta till servern, se [SSL-verifiering](howto-configure-ssl.md#step-4-verify-the-ssl-connection).

#### <a name="is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>Är det en åtgärd som behövs om jag redan har DigiCertGlobalRootG2 i min certifikat fil?

Nej. Det krävs ingen åtgärd om certifikat filen redan har **DigiCertGlobalRootG2**.

#### <a name="what-if-i-have-further-questions"></a>Vad händer om jag har fler frågor?

För frågor, få svar från community-experter i [Microsoft Q&A](mailto:AzureDatabaseforMySQL@service.microsoft.com). [Kontakta oss](mailto:AzureDatabaseforMySQL@service.microsoft.com)om du har en Support plan och behöver teknisk hjälp.
