---
title: SSL/TLS – Azure Database for PostgreSQL-enskild server
description: Instruktioner och information om hur du konfigurerar TLS-anslutning för Azure Database for PostgreSQL-enskild server.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: c98ee8f747975d4237c2906be2060eddbc7b9990
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000954"
---
# <a name="configure-tls-connectivity-in-azure-database-for-postgresql---single-server"></a>Konfigurera TLS-anslutning i Azure Database for PostgreSQL-enskild server

Azure Database for PostgreSQL föredrar att ansluta dina klient program till PostgreSQL-tjänsten med hjälp av Transport Layer Security (TLS), tidigare kallat Secure Sockets Layer (SSL). Att framtvinga TLS-anslutningar mellan din databas server och dina klient program hjälper till att skydda mot "man-in-the-Middle"-attacker genom att kryptera data strömmen mellan servern och ditt program.

Som standard är PostgreSQL-databas tjänsten konfigurerad att kräva TLS-anslutning. Du kan välja att inaktivera krav på TLS om klient programmet inte stöder TLS-anslutning.

>[!NOTE]
> Baserat på feedback från kunder har vi utökat rot certifikatets utfasning för vår befintliga Baltimore rot certifikat utfärdare till 15 februari 2021 (02/15/2021).

> [!IMPORTANT] 
> SSL-rotcertifikat är inställt på att upphöra att gälla den 15 februari 2021 (02/15/2021). Uppdatera ditt program för att använda det [nya certifikatet](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem). Mer information finns i avsnittet om [planerade certifikat uppdateringar](concepts-certificate-rotation.md)


## <a name="enforcing-tls-connections"></a>Tvinga TLS-anslutningar

För alla Azure Database for PostgreSQL-servrar som tillhandahålls via Azure Portal och CLI är tvingande av TLS-anslutningar aktiverat som standard. 

På samma sätt inkluderar anslutnings strängar som är fördefinierade i inställningarna "anslutnings strängar" under servern i Azure Portal de nödvändiga parametrarna för vanliga språk för att ansluta till din databas server med TLS. TLS-parametern varierar beroende på anslutningen, till exempel "SSL = true" eller "sslmode = Kräv" eller "sslmode = required" och andra variationer.

## <a name="configure-enforcement-of-tls"></a>Konfigurera verk ställande av TLS

Du kan också inaktivera tvingande TLS-anslutning. Microsoft Azure rekommenderar att du alltid aktiverar alternativet **FRAMTVINGA SSL-anslutning** för förbättrad säkerhet.

### <a name="using-the-azure-portal"></a>Använda Azure Portal

Besök Azure Database for PostgreSQL-servern och klicka på **anslutnings säkerhet**. Använd växlings knappen för att aktivera eller inaktivera inställningen **FRAMTVINGA SSL-anslutning** . Klicka sedan på **Spara**.

:::image type="content" source="./media/concepts-ssl-connection-security/1-disable-ssl.png" alt-text="Anslutnings säkerhet – inaktivera framtvinga TLS/SSL":::

Du kan bekräfta inställningen genom att visa **översikts** sidan för att se status indikatorn för **SSL-framtvingande** .

### <a name="using-azure-cli"></a>Använda Azure CLI

Du kan aktivera eller inaktivera **SSL-tvångs** parametern med `Enabled` respektive `Disabled` värden i Azure CLI.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Se till att ditt program eller ramverk stöder TLS-anslutningar

Vissa program ramverk som använder PostgreSQL för sina databas tjänster aktiverar inte TLS som standard under installationen. Om din PostgreSQL-Server framtvingar TLS-anslutningar men programmet inte har kon figurer ATS för TLS, kan det hända att programmet inte kan ansluta till din databas server. Mer information om hur du aktiverar TLS-anslutningar finns i programmets dokumentation.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Program som kräver certifikat verifiering för TLS-anslutning

I vissa fall kräver program en lokal certifikat fil som genereras från en certifikat utfärdare för betrodda certifikat utfärdare för att ansluta säkert. Certifikatet för att ansluta till en Azure Database for PostgreSQL-Server finns på https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem . Ladda ned certifikat filen och spara den på önskad plats. 

Se följande länkar för certifikat för servrar i suveräna moln: [Azure Government](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem), [Azure Kina](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)och [Azure Germany](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt).

### <a name="connect-using-psql"></a>Anslut med psql

I följande exempel visas hur du ansluter till PostgreSQL-servern med hjälp av kommando rads verktyget psql. Använd `sslmode=verify-full` inställningen för anslutnings strängen för att framtvinga verifiering av TLS/SSL-certifikat. Överför sökvägen till den lokala certifikat filen till- `sslrootcert` parametern.

Följande kommando är ett exempel på anslutnings strängen psql:

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Bekräfta att värdet som skickas till `sslrootcert` matchar fil Sök vägen för det certifikat som du har sparat.

## <a name="tls-enforcement-in-azure-database-for-postgresql-single-server"></a>TLS-tvång i Azure Database for PostgreSQL enskild server

Azure Database for PostgreSQL-enskild server stöder kryptering för klienter som ansluter till din databas server med hjälp av Transport Layer Security (TLS). TLS är ett bransch standard protokoll som garanterar säkra nätverks anslutningar mellan din databas server och klient program, så att du kan följa kraven för efterlevnad.

### <a name="tls-settings"></a>TLS-inställningar

Azure Database for PostgreSQL enskild server ger möjlighet att genomdriva TLS-versionen för klient anslutningarna. Om du vill framtvinga TLS-versionen använder du inställningen **minsta TLS-version** . Följande värden är tillåtna för den här alternativ inställningen:

|  Minsta TLS-inställning             | Klientens TLS-version stöds                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (standard) | Ingen TLS krävs                      |
| TLS1_0                           | TLS 1,0, TLS 1,1, TLS 1,2 och högre |
| TLS1_1                           | TLS 1,1, TLS 1,2 och högre          |
| TLS1_2                           | TLS version 1,2 och högre           |


Om du till exempel anger den här lägsta TLS-inställningen version till TLS 1,0 betyder det att servern tillåter anslutningar från klienter som använder TLS 1,0, 1,1 och 1.2 +. Du kan också ställa in detta på 1,2 innebär att du bara tillåter anslutningar från klienter som använder TLS 1.2 + och alla anslutningar med TLS 1,0 och TLS 1,1 kommer att avvisas.

> [!Note] 
> Som standard tillämpar Azure Database for PostgreSQL inte en lägsta TLS-version (inställningen `TLSEnforcementDisabled` ).
>
> När du har tillverkat en lägsta TLS-version kan du inte senare inaktivera den lägsta version som tillämpas.

Information om hur du anger TLS-inställningen för Azure Database for PostgreSQL enskild server finns i [Konfigurera TLS-inställningen](howto-tls-configurations.md).

## <a name="cipher-support-by-azure-database-for-postgresql-single-server"></a>Cipher-stöd av Azure Database for PostgreSQL enskild server

Som en del av SSL/TLS-kommunikationen verifieras chiffersviter och endast stöd för chiffersviter är tillåtna för att kommunicera med databasen serer. Verifieringen av chiffersviter styrs i [Gateway-lagret](concepts-connectivity-architecture.md#connectivity-architecture) och inte uttryckligen på själva noden. Om chiffersviter inte matchar någon av paketen i listan nedan, kommer inkommande klient anslutningar att avvisas.

### <a name="cipher-suite-supported"></a>Chiffrering stöds

*   TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
*   TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="next-steps"></a>Nästa steg

Granska olika program anslutnings alternativ i [anslutnings bibliotek för Azure Database for PostgreSQL](concepts-connection-libraries.md).

- Lär dig hur du [konfigurerar TLS](howto-tls-configurations.md)