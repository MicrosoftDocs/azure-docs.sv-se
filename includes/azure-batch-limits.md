---
title: ta med fil
description: ta med fil
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 12/16/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 0a94b122f1cdd598eeac553c8cc784d2a0a5369f
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97614527"
---
| **Resurs** | **Standardgräns** | **Maximal gräns** |
| --- | --- | --- |
| Azure Batch konton per region per prenumeration | 1-3 |50 |
| Dedikerade kärnor per batch-konto | 90-900 | Kontakta supporten |
| Låg prioritets kärnor per batch-konto | 10-100 | Kontakta supporten |
| **[Aktiva](/rest/api/batchservice/job/get#jobstate)** jobb och jobb scheman per batch-konto (**slutförda** jobb har ingen gräns) | 100-300 | 1 000<sup>1</sup> |
| Pooler per Batch-konto | 20-100 | 500<sup>1</sup> |

<sup>1</sup> Kontakta Azure-supporten om du vill begära en ökning utöver den här gränsen.

> [!NOTE]
> Standard gränserna varierar beroende på vilken typ av prenumeration du använder för att skapa ett batch-konto. Kärnor som visas är för batch-konton i batch-tjänstens läge. [Visa kvoterna i batch-kontot](../articles/batch/batch-quota-limit.md#view-batch-quotas).

> [!IMPORTANT]
> För att hjälpa oss att hantera kapaciteten under den globala hälso Pandemic är standard kvoterna för nya batch-konton i vissa regioner och för vissa typer av prenumeration sänkta från ovanstående värde intervall, i vissa fall till noll kärnor. När du skapar ett nytt batch-konto [kontrollerar du kärn kvoten](../articles/batch/batch-quota-limit.md#view-batch-quotas) och [begär en ökad kärn kvot](../articles/batch/batch-quota-limit.md#increase-a-quota), om det behövs. Du kan också överväga att återanvända batch-konton som redan har tillräcklig kvot.