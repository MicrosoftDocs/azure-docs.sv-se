---
title: Använd Azure Data Studio för att hantera din PostgreSQL-instans
description: Använd Azure Data Studio för att hantera din PostgreSQL-instans
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7dcc0f916a15598060e034dcf62536ee13e2672e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92320241"
---
# <a name="use-azure-data-studio-to-manage-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Använd Azure Data Studio för att hantera Azure Arc-aktiverade PostgreSQL-Server gruppen


Den här artikeln beskriver hur du:
- hantera dina PostgreSQL-instanser med instrument panels vyer som översikt, anslutnings strängar, egenskaper Resource Health...
- arbeta med dina data och ditt schema

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Förutsättningar

- [Installera azdata, Azure Data Studio och Azure CLI](install-client-tools.md)
- Installera i Azure Data Studio- **[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]** och **Azure-båg** -och **postgresql** -tillägg
- Skapa [data styrenheten för Azure-bågen](create-data-controller-using-azdata.md)
- Starta Azure Data Studio

## <a name="connect-to-the-azure-arc-data-controller"></a>Anslut till data styrenheten för Azure-bågen

I Azure Data Studio expanderar du noden **Azure Arc-styrenheter** och väljer knappen **Anslut kontrollant** :

Ange anslutnings informationen till din Azure-datakontrollant:

- **Kontrollant-URL:**

    URL: en för att ansluta till din styrenhet i Kubernetes. Anges i form av till `https://<IP_address_of_the_controller>:<Kubernetes_port.` exempel:

    ```console
    https://12.345.67.890:30080
    ```
- **Användarnamn:**

    Namnet på det användar konto som du använder för att ansluta till kontrollanten. Använd det namn som du vanligt vis använder när du kör `azdata login` . Det är inte namnet på den PostgreSQL-användare som du använder för att ansluta till PostgreSQL-databasmotorn normalt från psql.
- **Lösen ord:** Lösen ordet för det användar konto som du använder för att ansluta till kontrollanten


Azure Data Studio visar din ARC-datakontrollant. Expandera den och visa en lista över de PostgreSQL-instanser som hanteras.

## <a name="manage-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Hantera Azure Arc-aktiverade PostgreSQL-servergrupper för Server grupper

Högerklicka på den PostgreSQL-instans som du vill hantera och välj [hantera]

Vyn instrument panel för PostgreSQL:

Det innehåller flera instrument paneler som visas på vänster sida i fönstret:

- **Översikt:** Visar sammanfattnings information om din instans, t. ex. namn, PostgreSQL admin-användarnamn, Azure-prenumerations-ID, konfiguration, version av databas motorn, slut punkter för Grafana och Kibana...
- **Anslutnings strängar:** Visar olika anslutnings strängar som du kan behöva ansluta till PostgreSQL-instansen som psql, Node.js, PHP, Ruby...
- **Diagnostisera och lösa problem:** Är landnings sidan där du hittar olika resurser som hjälper dig att felsöka din instans när vi utökar fel söknings antecknings böckerna
- **Ny supportbegäran:** Är landnings sidan från vilken du kommer att kunna begära hjälp från våra Support tjänster som startar allmänt för hands versions meddelande.

## <a name="work-with-your-data-and-schema"></a>Arbeta med dina data och ditt schema

Expandera noden **servrar** till vänster i Azure Data Studios fönstret:

Och välj [Lägg till anslutning] och fyll i anslutnings informationen till din PostgreSQL-instans:
- **Anslutnings typ:** PostgreSQL
- **Server Namn:** ange namnet på din postgresql-instans. Till exempel: postgres01
- **Autentiseringstyp:** Ords
- **Användar namn:** till exempel kan du använda standard-postgresql administratörs användar namn. Obs! det här fältet är Skift läges känsligt.
- **Lösen ord:** du hittar lösen ordet för postgresql-användarnamnet i psql-anslutningssträngen i `azdata postgres server endpoint -n postgres01` kommandots utdata
- **Databas namn:** ange namnet på den databas som du vill ansluta till. Du kan låta den ställas in på __standard__
- **Server grupp:** du kan låta den ställas in på __standard__
- **Namn (valfritt):** du kan låta detta vara tomt
- **Erfar**
    - **Värd-IP-adress:** är den offentliga IP-adressen för Kubernetes-klustret
    - **Port:** är porten som postgresql-instansen lyssnar på. Du hittar den här porten i slutet av psql-anslutningssträngen i `azdata postgres server endpoint -n postgres01` kommandots utdata. Inte port 30080 där Kubernetes lyssnar och du angav när du anslöt till Azure-datakontrollanten i Azure Data Studio.
    - **Andra parametrar:** De bör vara självständiga, du kan leva med de standardvärden/tomma värdena de visas med.

Välj **[OK] och [Anslut]** för att ansluta till servern.

När du är ansluten är flera upplevelser tillgängliga:
- **Ny fråga**
- **Ny antecknings bok**
- **Expandera visningen av servern och bläddra/arbeta med objekten i databasen**
- **...**

## <a name="next-step"></a>Nästa steg
[Övervaka Server gruppen](monitor-grafana-kibana.md)
