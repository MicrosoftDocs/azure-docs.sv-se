---
title: Stoppa/starta – Azure Portal-Azure Database for PostgreSQL flexibel Server
description: I den här artikeln beskrivs hur du stoppar/startar åtgärder i Azure Database for PostgreSQL via Azure Portal.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 4c393e0048a0058ebe0fbf2b0ee65f6ae2e184c5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90941456"
---
# <a name="stopstart-an-azure-database-for-postgresql---flexible-server-preview"></a>Stoppa/starta en Azure Database for PostgreSQL – flexibel Server (för hands version)

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är för närvarande en för hands version.

Den här artikeln innehåller steg-för-steg-instruktioner för att stoppa och starta en flexibel Server.

## <a name="pre-requisites"></a>Förutsättningar

För att slutföra den här instruktions guiden behöver du:

-   Du måste ha en Azure Database for PostgreSQL flexibel Server.

## <a name="stop-a-running-server"></a>Stoppa en server som körs

1.  I [Azure Portal](https://portal.azure.com/)väljer du den flexibla server som du vill stoppa.

2.  På sidan **Översikt** klickar du på knappen **stopp** i verktygsfältet.

> [!NOTE]
> När servern har stoppats är andra hanterings åtgärder inte tillgängliga för den flexibla servern.

Observera att stoppade servrar automatiskt startar om efter sju dagar. Alla väntande underhålls uppdateringar kommer att tillämpas när servern startas nästa gång.

## <a name="start-a-stopped-server"></a>Starta en stoppad Server

1.  I [Azure Portal](https://portal.azure.com/)väljer du den flexibla server som du vill starta.

2.  På sidan **Översikt** klickar du på knappen **Start** i verktygsfältet.

> [!NOTE]
> När servern har startats är alla hanterings åtgärder nu tillgängliga för den flexibla servern.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [beräknings-och lagrings alternativ i Azure Database for PostgreSQL flexibel Server](./concepts-compute-storage.md).
