---
title: 'API: er för åtkomst till kommersiella data för Marketplace-analys'
description: 'Använd dessa API: er för att komma åt Analytics-data via programmering i Partner Center.'
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: ed6d658155267ab21fd4cdd28dd50fcbb258ee90
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102584254"
---
# <a name="apis-for-accessing-commercial-marketplace-analytics-data"></a>API: er för åtkomst till kommersiella data för Marketplace-analys

Nedan visas en lista över API: er för åtkomst till kommersiella Marketplace-analys data och deras associerade funktioner.

- [Hämta API: er för data uppsättning](#dataset-pull-apis)
- [API: er för fråge hantering](#query-management-apis)
- [API: er för rapport hantering](#report-management-apis)
- [Hämta pull-API: er för rapport körning](#report-execution-pull-apis)

## <a name="dataset-pull-apis"></a>Hämta API: er för data uppsättning

***Tabell 1: hämtnings-API: er för data uppsättning***

| **API** | **Funktioner** |
| --- | --- |
| [Hämta alla data uppsättningar](analytics-api-get-all-datasets.md) | Hämtar alla tillgängliga data uppsättningar. Data uppsättningar visar tabeller, kolumner, mått och tidsintervall. |
|||

## <a name="query-management-apis"></a>API: er för fråge hantering

***Tabell 2: API för fråge hantering***

| **API** | **Funktioner** |
| --- | --- |
| [Skapa rapport fråga](analytics-programmatic-access.md#create-report-query-api) | Skapar anpassade frågor som definierar data uppsättningen som kolumner och mått måste exporteras från. |
| [Hämta rapport frågor](analytics-api-get-report-queries.md) | Hämtar alla frågor som är tillgängliga för användning i rapporter. Hämtar alla system-och användardefinierade frågor som standard. |
| [TA bort rapport frågor](analytics-api-delete-report-queries.md) | Tar bort användardefinierade frågor. |
|||

## <a name="report-management-apis"></a>API: er för rapport hantering

***Tabell 3: API: er för rapport hantering***

| **API** | **Funktioner** |
| --- | --- |
| [Skapa rapport](analytics-programmatic-access.md#create-report-api) | Schemalägger en fråga som ska köras med jämna mellanrum. |
| [Prova rapport frågor](analytics-api-try-report-queries.md) | Kör en rapports frågeuttryck. Returnerar bara 10 poster som en partner kan använda för att kontrol lera om data är som förväntat. |
| [Hämta rapport](analytics-api-get-report.md) | Hämta alla rapporter som har schemalagts. |
| [Uppdatera rapport](analytics-api-update-report.md) | Ändra en rapport parameter. |
| [Ta bort rapport](analytics-api-delete-report.md) | Tar bort alla rapport-och rapport körnings poster. |
| [Pausa rapport körningar](analytics-api-pause-report-executions.md) | Pausar schemalagd körning av rapporter. |
| [Återuppta rapport körningar](analytics-api-resume-report-executions.md) | Återupptar den schemalagda körningen av en pausad rapport. |
|||

## <a name="report-execution-pull-apis"></a>Hämta pull-API: er för rapport körning

***Tabell 4: Hämta hämtnings-API: er för rapport körning***

| **API** | **Funktioner** |
| --- | --- |
| [Hämta rapport körningar](analytics-programmatic-access.md#get-report-executions-api) | Hämta alla körningar som har hänt för en specifik rapport. |
|||

## <a name="next-steps"></a>Nästa steg

- Du kan prova API: erna via [URL: en för Swagger-API](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html).
