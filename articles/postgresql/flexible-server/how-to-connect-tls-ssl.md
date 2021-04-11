---
title: Krypterad anslutning med TLS/SSL i Azure Database for PostgreSQL-flexibel Server
description: Instruktioner och information om hur du ansluter med TLS/SSL i Azure Database for PostgreSQL-flexibel Server.
author: rothja
ms.author: jroth
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: d47c7c6b980df46ee44a44758313571506e79e44
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551211"
---
# <a name="encrypted-connectivity-using-transport-layer-security-in-azure-database-for-postgresql---flexible-server"></a>Krypterad anslutning med hjälp av Transport Layer Security i Azure Database for PostgreSQL-flexibel Server

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

Azure Database for PostgreSQL-flexibel Server stöder anslutning av klient program till PostgreSQL-tjänsten med hjälp av Transport Layer Security (TLS), tidigare kallat Secure Sockets Layer (SSL). TLS är ett bransch standard protokoll som garanterar krypterade nätverks anslutningar mellan din databas server och klient program, så att du kan följa kraven för efterlevnad.

Azure Database for PostgreSQL-flexibel Server stöder krypterade anslutningar med Transport Layer Security (TLS 1.2 +) och alla inkommande anslutningar med TLS 1,0 och TLS 1,1 kommer att nekas. För alla flexibla servrar är tvingande av TLS-anslutningar aktiverat och du kan inte inaktivera TLS/SSL för anslutning till flexibel Server.

## <a name="applications-that-require-certificate-verification-for-tlsssl-connectivity"></a>Program som kräver certifikat verifiering för TLS/SSL-anslutning
I vissa fall kräver program en lokal certifikat fil som genereras från en certifikat utfärdare för betrodda certifikat utfärdare för att ansluta säkert. Azure Database for PostgreSQL-flexibel Server använder *DigiCert global rot certifikat utfärdare*. Hämta det här certifikatet som krävs för att kommunicera via SSL från [DigiCert global rot certifikat utfärdare](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) och spara certifikat filen på önskad plats. I den här självstudien används till exempel `c:\ssl` .


### <a name="connect-using-psql"></a>Anslut med psql
Om du har skapat din flexibla server med *privat åtkomst (VNet-integrering)* måste du ansluta till servern från en resurs i samma VNet som servern. Du kan skapa en virtuell dator och lägga till den i VNet som skapats med din flexibla Server.

Om du har skapat din flexibla server med *offentlig åtkomst (tillåtna IP-adresser)* kan du lägga till din lokala IP-adress i listan över brand Väggs regler på servern.

I följande exempel visas hur du ansluter till servern med hjälp av kommando rads gränssnittet psql. Använd `sslmode=verify-full` inställningen för anslutnings strängen för att framtvinga verifiering av TLS/SSL-certifikat. Överför sökvägen till den lokala certifikat filen till- `sslrootcert` parametern.

```bash
 psql "sslmode=verify-full sslrootcert=c:\ssl\DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=postgres user=myadmin"
```
> [!Note]
> Bekräfta att värdet som skickas till `sslrootcert` matchar fil Sök vägen för det certifikat som du har sparat.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Se till att ditt program eller ramverk stöder TLS-anslutningar

Vissa program ramverk som använder PostgreSQL för sina databas tjänster aktiverar inte TLS som standard under installationen. PostgreSQL-servern framtvingar TLS-anslutningar, men om programmet inte har kon figurer ATS för TLS kan det hända att programmet inte kan ansluta till din databas server. Mer information om hur du aktiverar TLS-anslutningar finns i programmets dokumentation.

## <a name="next-steps"></a>Nästa steg
- [Skapa och hantera Azure Database for PostgreSQL-flexibla Server nätverk med Azure CLI](./how-to-manage-virtual-network-cli.md).
- Lär dig mer om [nätverk i Azure Database for PostgreSQL-flexibel Server](./concepts-networking.md)
- Lär dig mer om regler för att [Azure Database for PostgreSQL flexibla Server brand vägg](./concepts-networking.md#public-access-allowed-ip-addresses)
