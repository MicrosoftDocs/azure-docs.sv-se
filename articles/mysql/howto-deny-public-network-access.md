---
title: Neka offentlig nätverks åtkomst – Azure Portal-Azure Database for MySQL
description: Lär dig hur du konfigurerar neka offentlig nätverks åtkomst med Azure Portal för din Azure Database for MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: b6fd5b5f70eb813792be003836790752db1d071f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101732827"
---
# <a name="deny-public-network-access-in-azure-database-for-mysql-using-azure-portal"></a>Neka offentlig nätverks åtkomst i Azure Database for MySQL att använda Azure Portal

I den här artikeln beskrivs hur du kan konfigurera en Azure Database for MySQL-server för att neka alla offentliga konfigurationer och bara tillåta anslutningar via privata slut punkter för att ytterligare förbättra nätverks säkerheten.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här instruktions guiden behöver du:

* En [Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md) med generell användning eller minnesoptimerade pris nivå

## <a name="set-deny-public-network-access"></a>Ange neka offentlig nätverks åtkomst

Följ dessa steg om du vill ställa in MySQL server neka offentlig nätverks åtkomst:

1. I [Azure Portal](https://portal.azure.com/)väljer du din befintliga Azure Database for MySQL-server.

1. På sidan MySQL server, under **Inställningar**, klickar du på **anslutnings säkerhet** för att öppna sidan anslutnings säkerhets konfiguration.

1. I **neka offentlig nätverks åtkomst** väljer du **Ja** för att aktivera neka offentlig åtkomst för MySQL-servern.

    :::image type="content" source="./media/howto-deny-public-network-access/setting-deny-public-network-access.PNG" alt-text="Azure Database for MySQL neka nätverks åtkomst":::

1. Klicka på **Spara** för att spara ändringarna.

1. Ett meddelande bekräftar att anslutnings säkerhets inställningen har Aktiver ATS.

    :::image type="content" source="./media/howto-deny-public-network-access/setting-deny-public-network-access-success.png" alt-text="Azure Database for MySQL nekad nätverks åtkomst":::

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [hur du skapar aviseringar för mått](howto-alert-on-metric.md).
