---
title: Transport Layer Security (TLS)-storskalig (citus)-Azure Database for PostgreSQL
description: Instruktioner och information om hur du konfigurerar Azure Database for PostgreSQL-storskalig (citus) och associerade program för att använda TLS-anslutningar på rätt sätt.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: 659f86a107e4b08db4ec5195635ea32d2260d677
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87071463"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>Konfigurera TLS i Azure Database for PostgreSQL-storskalig (citus)
Den storskaliga (citus) koordinator noden kräver att klient program ansluter till Transport Layer Security (TLS). Att framtvinga TLS mellan databas servern och klient programmen hjälper till att hålla data konfidentiella under överföring. Ytterligare verifierings inställningar som beskrivs nedan skyddar också mot "man-in-the-Middle"-attacker.

## <a name="enforcing-tls-connections"></a>Tvinga TLS-anslutningar
Program använder en "anslutnings sträng" för att identifiera mål databasen och inställningar för en anslutning. Olika klienter kräver olika inställningar. Om du vill se en lista över anslutnings strängar som används av vanliga klienter läser du avsnittet **anslutnings strängar** för Server gruppen i Azure Portal.

TLS-parametrarna `ssl` och `sslmode` varierar beroende på funktionerna i anslutningen, till exempel `ssl=true` eller `sslmode=require` `sslmode=required` .

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Se till att ditt program eller ramverk stöder TLS-anslutningar
Vissa program ramverk aktiverar inte TLS som standard för PostgreSQL-anslutningar. Men utan en säker anslutning går det inte att ansluta ett program till en citus-koordinator (storskalig). Mer information om hur du aktiverar TLS-anslutningar finns i programmets dokumentation.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Program som kräver certifikat verifiering för TLS-anslutning
I vissa fall kräver program en lokal certifikat fil som genereras från en betrodd certifikat utfärdare (. cer) för att ansluta på ett säkert sätt. Certifikatet för att ansluta till en Azure Database for PostgreSQL-storskalig (citus) finns på https://cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem . Ladda ned certifikat filen och spara den på önskad plats.

> [!NOTE]
>
> Du kan kontrol lera certifikatets äkthet genom att verifiera dess SHA-256-finger avtryck med kommando rads verktyget OpenSSL:
>
> ```sh
> openssl x509 -in DigiCertGlobalRootCA.crt.pem -noout -sha256 -fingerprint
>
> # should output:
> # 43:48:A0:E9:44:4C:78:CB:26:5E:05:8D:5E:89:44:B4:D8:4F:96:62:BD:26:DB:25:7F:89:34:A4:43:C7:01:61
> ```

### <a name="connect-using-psql"></a>Anslut med psql
I följande exempel visas hur du ansluter till din citus-koordinator-nod med hjälp av kommando rads verktyget psql. Använd `sslmode=verify-full` inställningen anslutnings sträng för att framtvinga verifiering av TLS-certifikat. Överför sökvägen till den lokala certifikat filen till- `sslrootcert` parametern.

Nedan visas ett exempel på anslutnings strängen psql:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Bekräfta att värdet som skickas till `sslrootcert` matchar fil Sök vägen för det certifikat som du har sparat.

## <a name="next-steps"></a>Nästa steg
Öka säkerheten ytterligare med [brand Väggs regler i Azure Database for PostgreSQL-storskalig (citus)](concepts-hyperscale-firewall-rules.md).
