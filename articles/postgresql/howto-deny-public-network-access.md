---
title: Neka offentlig nätverks åtkomst – Azure Portal-Azure Database for PostgreSQL-enskild server
description: Lär dig hur du konfigurerar neka offentlig nätverks åtkomst med hjälp av Azure Portal för din Azure Database for PostgreSQL enskild server
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: e195c005676df27385e5e00736b04bdb689fafc5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727115"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>Neka offentlig nätverks åtkomst i Azure Database for PostgreSQL enskild server med Azure Portal

I den här artikeln beskrivs hur du kan konfigurera en Azure Database for PostgreSQL enskild server för att neka alla offentliga konfigurationer och bara tillåta anslutningar via privata slut punkter för att ytterligare förbättra nätverks säkerheten.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här instruktions guiden behöver du:

* En [Azure Database for PostgreSQL enskild server](quickstart-create-server-database-portal.md) med generell användning eller minnesoptimerade pris nivå.

## <a name="set-deny-public-network-access"></a>Ange neka offentlig nätverks åtkomst

Följ dessa steg för att ställa in PostgreSQL-Server neka offentlig nätverks åtkomst:

1. I [Azure Portal](https://portal.azure.com/)väljer du din befintliga Azure Database for PostgreSQL enskild server.

1. På sidan PostgreSQL enskild server under **Inställningar** klickar du på **anslutnings säkerhet** för att öppna sidan anslutnings säkerhets konfiguration.

1. I **neka offentlig nätverks åtkomst** väljer du **Ja** för att aktivera neka offentlig åtkomst för postgresql-servern.

    :::image type="content" source="./media/howto-deny-public-network-access/deny-public-network-access.PNG" alt-text="Azure Database for PostgreSQL enskild server neka nätverks åtkomst":::

1. Klicka på **Spara** för att spara ändringarna.

1. Ett meddelande bekräftar att anslutnings säkerhets inställningen har Aktiver ATS.

    :::image type="content" source="./media/howto-deny-public-network-access/deny-public-network-access-success.png" alt-text="Azure Database for PostgreSQL enskild server neka nätverks åtkomst":::

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [hur du skapar aviseringar för mått](howto-alert-on-metric.md).
