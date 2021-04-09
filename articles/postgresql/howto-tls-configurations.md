---
title: TLS-konfiguration – Azure Portal-Azure Database for PostgreSQL-enskild server
description: Lär dig hur du ställer in TLS-konfiguration med Azure Portal för din Azure Database for PostgreSQL enskild server
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: 25be6b3c4e3172fc8ee14b97fd890b5948c284ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93242372"
---
# <a name="configuring-tls-settings-in-azure-database-for-postgresql-single---server-using-azure-portal"></a>Konfigurera TLS-inställningar i Azure Database for PostgreSQL enskild server med Azure Portal

Den här artikeln beskriver hur du kan konfigurera en Azure Database for PostgreSQL för att genomdriva den lägsta TLS-versionen som tillåts för anslutningar och neka alla anslutningar med lägre TLS-version än den konfigurerade lägsta TLS-versionen och därigenom förbättra nätverks säkerheten.

Du kan använda TLS-version för att ansluta till sina Azure Database for PostgreSQL. Kunder har nu ett alternativ för att ställa in den lägsta TLS-versionen för sin databas server. Om du till exempel anger den lägsta TLS-inställningen version till TLS 1,0 betyder det att servern tillåter anslutningar från klienter som använder TLS 1,0, 1,1 och 1.2 +. I stället för att ange den lägsta TLS-versionen till 1.2 + innebär det att du bara tillåter anslutningar från klienter som använder TLS 1,2 och alla anslutningar med TLS 1,0 och TLS 1,1 kommer att avvisas.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här instruktions guiden behöver du:

* En [Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-postgresql---single-server"></a>Ange TLS-konfigurationer för Azure Database for PostgreSQL-enskild server

Följ de här stegen för att ange PostgreSQL lägsta TLS-version:

1. I [Azure Portal](https://portal.azure.com/)väljer du din befintliga Azure Database for PostgreSQL.

1.  På sidan Azure Database for PostgreSQL-enskild server, under **Inställningar**, klickar du på **anslutnings säkerhet** för att öppna sidan anslutnings säkerhets konfiguration.

1. I **lägsta TLS-version** väljer du **1,2** för att neka anslutningar med en TLS-version som är mindre än TLS 1,2 för din postgresql-server.

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value.png" alt-text="Azure Database for PostgreSQL TLS-konfiguration för enskild server":::

1. Klicka på **Spara** för att spara ändringarna.

1. Ett meddelande bekräftar att anslutnings säkerhets inställningen har Aktiver ATS.

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value-success.png" alt-text="Azure Database for PostgreSQL-konfiguration av en enskild server-TLS lyckades":::

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [att skapa aviseringar för mått](howto-alert-on-metric.md)