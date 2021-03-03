---
title: Konfigurera aktiv geo-replikering för Enterprise Azure-cache för Redis-instanser
description: Lär dig hur du replikerar Azure-cachen för Redis Enterprise-instanser i Azure-regioner
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: d52998e477e44f89a35a70f7e2b5d49cbc68e386
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663768"
---
# <a name="configure-active-geo-replication-for-enterprise-azure-cache-for-redis-instances-preview"></a>Konfigurera aktiv geo-replikering för Enterprise Azure-cache för Redis-instanser (för hands version)

I den här artikeln får du lära dig hur du konfigurerar en aktiv geo-replikerad Azure-cache med hjälp av Azure Portal.

Aktiv geo-replikering grupperar två eller flera Enterprise Azure-cache för Redis-instanser i ett enda cacheminne som sträcker sig över Azure-regioner. Alla instanser fungerar som lokala presidentval. Ett program bestämmer vilken eller vilka instanser som ska användas för Läs-och skriv förfrågningar.

## <a name="create-or-join-an-active-geo-replication-group"></a>Skapa eller Anslut en aktiv geo-replikeringsgrupp

> [!IMPORTANT]
> Aktiv geo-replikering måste vara aktiverat när en Azure-cache för Redis skapas.
>
>

1. I den **nya Redis cache** skapa användar gränssnitt klickar du på **Konfigurera** för att konfigurera **aktiv geo-replikering** på fliken **Avancerat** .

    ![Konfigurera aktiv geo-replikering](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-not-configured.png)

1. Skapa en ny replikeringsgrupp, för en första cache-instans eller Välj en befintlig i listan.

    ![Länka cacheminnen](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-new-group.png)

1. Klicka på **Konfigurera** för att avsluta.

    ![Länka cacheminnen](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-configured.png)

1. Upprepa stegen ovan för varje extra cache-instans i Geo-replikeringsgruppen.

## <a name="remove-from-an-active-geo-replication-group"></a>Ta bort från en aktiv geo-replikeringsgrupp

Ta bort instansen genom att ta bort en cache-instans från en aktiv geo-replikeringsgrupp. De återstående instanserna kommer att konfigureras om automatiskt.

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure cache för Redis-funktioner.

* [Azure cache för Redis-tjänst nivåer](cache-overview.md#service-tiers)
* [Hög tillgänglighet för Azure cache för Redis](cache-high-availability.md)
