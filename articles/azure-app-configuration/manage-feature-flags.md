---
title: 'Självstudie: Använd Azure App konfiguration för att hantera funktions flaggor'
titleSuffix: Azure App Configuration
description: I den här självstudien får du lära dig hur du hanterar funktions flaggor separat från ditt program genom att använda Azure App konfiguration.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: lcozzens
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: dd816ebcf2a40e6a0b7febcc3fe5c1006dac20bb
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2020
ms.locfileid: "88209943"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>Självstudie: hantera funktions flaggor i Azure App konfiguration

Du kan lagra alla funktions flaggor i Azure App konfiguration och administrera dem från en enda plats. App-konfigurationen har ett Portal gränssnitt med namnet **funktions hanterare** som är särskilt utformat för funktions flaggor. App-konfigurationen har även stöd för .NET Core-funktionen – flagga data schema.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Definiera och hantera funktions flaggor i app-konfiguration.
> * Få åtkomst till funktions flaggor från ditt program.

## <a name="create-feature-flags"></a>Skapa funktions flaggor

Funktions hanteraren i Azure Portal för app-konfiguration innehåller ett gränssnitt för att skapa och hantera de funktions flaggor som du använder i dina program.

Lägga till en ny funktions flagga:

1. Välj **funktions hanteraren**  >  **+ Lägg** till för att lägga till en funktions flagga.

    ![Funktions flagg lista](./media/azure-app-configuration-feature-flags.png)

1. Ange ett unikt nyckel namn för funktions flaggan. Du behöver det här namnet för att referera till flaggan i din kod.

1. Om du vill kan du ge funktions flagga en beskrivning.

1. Ange funktions flaggans initiala tillstånd. Det här läget är vanligt vis *inaktiverat* eller *på*. *On* -status ändras till *villkorlig* om du lägger till ett filter i funktions flaggan.

    ![Skapa funktions flagga](./media/azure-app-configuration-feature-flag-create.png)

1. När tillståndet är *på*väljer du **+ Lägg till filter** för att ange ytterligare villkor för att kvalificera tillståndet. Ange en inbyggd eller anpassad filter nyckel och välj sedan **+ Lägg till parameter** för att associera en eller flera parametrar med filtret. Inbyggda filter är:

    | Tangent | JSON-parametrar |
    |---|---|
    | Microsoft. procent | {"Värde": 0-100 procent} |
    | Microsoft. TimeWindow | {"Start": UTC-tid, "slut": UTC-tid} |

    ![Funktions flagg filter](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>Uppdatera funktions flagga tillstånd

Ändra en funktions flaggas tillstånds värde:

1. Välj **funktions hanterare**.

1. Till höger om en funktions flagga som du vill ändra, väljer du ellipsen (**...**) och väljer sedan **Redigera**.

1. Ange ett nytt tillstånd för funktions flaggan.

## <a name="access-feature-flags"></a>Åtkomst till funktions flaggor

Funktions flaggor som skapats av funktions hanteraren lagras och hämtas som vanliga nyckel värden. De behålls under ett särskilt namnområdesprefix `.appconfig.featureflag` . Om du vill visa de underliggande nyckel värdena använder du konfigurations Utforskaren. Ditt program kan hämta dessa värden med hjälp av konfigurations leverantörerna för konfiguration av appar, SDK: er, kommando rads tillägg och REST-API: er.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du hanterar funktions flaggor och deras tillstånd med hjälp av app-konfiguration. Mer information om stöd för funktions hantering i app Configuration och ASP.NET Core finns i följande artikel:

* [Använda funktions flaggor i en ASP.NET Core app](./use-feature-flags-dotnet-core.md)
