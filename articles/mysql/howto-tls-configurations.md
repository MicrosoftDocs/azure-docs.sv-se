---
title: TLS-konfiguration – Azure Portal-Azure Database for MySQL
description: Lär dig hur du ställer in TLS-konfiguration med Azure Portal för din Azure Database for MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: 5ecf2992fa9ea56f73748a9f1f98c75f9076c68f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104656897"
---
# <a name="configuring-tls-settings-in-azure-database-for-mysql-using-azure-portal"></a>Konfigurera TLS-inställningar i Azure Database for MySQL att använda Azure Portal

Den här artikeln beskriver hur du kan konfigurera en Azure Database for MySQL-server för att genomdriva den lägsta TLS-version som tillåts för anslutningar att gå igenom och neka alla anslutningar med lägre TLS-version än den minst konfigurerade TLS-versionen och därigenom förbättra nätverks säkerheten.

Du kan använda TLS-version för att ansluta till sina Azure Database for MySQL. Kunder har nu ett alternativ för att ställa in den lägsta TLS-versionen för sin databas server. Om du till exempel ställer in denna minsta TLS-version på 1,0 innebär det att du tillåter klienter att ansluta med TLS 1.0, 1.1 och 1,2. Du kan också ställa in detta på 1,2 innebär att du bara tillåter klienter som ansluter med TLS 1.2 + och alla inkommande anslutningar med TLS 1,0 och TLS 1,1 att avvisas.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här instruktions guiden behöver du:

* En [Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mysql"></a>Ange TLS-konfigurationer för Azure Database for MySQL

Följ de här stegen om du vill ange lägsta TLS-server version:

1. I [Azure Portal](https://portal.azure.com/)väljer du din befintliga Azure Database for MySQL-server.

1. På sidan MySQL server, under **Inställningar**, klickar du på **anslutnings säkerhet** för att öppna sidan anslutnings säkerhets konfiguration.

1. I **lägsta TLS-version** väljer du **1,2** för att neka anslutningar med en TLS-version som är mindre än TLS 1,2 för MySQL-servern.

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value.png" alt-text="Azure Database for MySQL TLS-konfiguration":::

1. Klicka på **Spara** för att spara ändringarna. 

1. Ett meddelande bekräftar att anslutnings säkerhets inställningarna har Aktiver ATS och att de körs direkt. Det finns **ingen omstart** av servern som krävs eller utförs. När ändringarna har sparats godkänns alla nya anslutningar till servern endast om TLS-versionen är större än eller lika med den lägsta TLS-versionen som har angetts på portalen.

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value-success.png" alt-text="Azure Database for MySQL TLS-konfiguration lyckades":::

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [att skapa aviseringar för mått](howto-alert-on-metric.md)
