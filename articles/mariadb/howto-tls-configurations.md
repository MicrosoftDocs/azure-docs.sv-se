---
title: TLS-konfiguration – Azure Portal-Azure Database for MariaDB
description: Lär dig hur du ställer in TLS-konfiguration med Azure Portal för din Azure Database for MariaDB
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: 55a664ceb3524a0c545e037dcba7a2af90034ad4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664774"
---
# <a name="configuring-tls-settings-in-azure-database-for-mariadb-using-azure-portal"></a>Konfigurera TLS-inställningar i Azure Database for MariaDB att använda Azure Portal

Den här artikeln beskriver hur du kan konfigurera en Azure Database for MariaDB-Server för att genomdriva lägsta TLS-version för anslutningar att gå igenom och neka alla anslutningar med lägre TLS-version än den lägsta TLS-versionen och därigenom förbättra nätverks säkerheten.

Du kan använda TLS-versionen för att ansluta till sina Azure Database for MariaDB genom att ange den lägsta TLS-versionen för sin databas server. Om du till exempel anger den lägsta TLS-inställningen version till TLS 1,0 betyder det att servern tillåter anslutningar från klienter som använder TLS 1,0, 1,1 och 1.2 +. Du kan också ställa in detta på 1,2 innebär att du bara tillåter anslutningar från klienter som använder TLS 1.2 + och alla anslutningar med TLS 1,0 och TLS 1,1 kommer att avvisas.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här instruktions guiden behöver du:

* En [Azure Database for MariaDB](quickstart-create-mariaDB-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mariadb"></a>Ange TLS-konfigurationer för Azure Database for MariaDB

Följ dessa steg om du vill ange MariaDB-serverns lägsta TLS-version:

1. I [Azure Portal](https://portal.azure.com/)väljer du din befintliga Azure Database for MariaDB-Server.

1. På sidan MariaDB Server under **Inställningar** klickar du på **anslutnings säkerhet** för att öppna sidan anslutnings säkerhets konfiguration.

1. I **lägsta TLS-version** väljer du **1,2** för att neka anslutningar med en TLS-version som är mindre än TLS 1,2 för MariaDB-servern.

    ![Azure Database for MariaDB TLS-konfiguration](./media/howto-tls-configurations/tls-configurations.png)

1. Klicka på **Spara** för att spara ändringarna.

1. Ett meddelande bekräftar att anslutnings säkerhets inställningen har Aktiver ATS.

    ![Azure Database for MariaDB TLS-konfiguration lyckades](./media/howto-tls-configurations/tls-configurations-success.png)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [att skapa aviseringar för mått](howto-alert-metric.md)