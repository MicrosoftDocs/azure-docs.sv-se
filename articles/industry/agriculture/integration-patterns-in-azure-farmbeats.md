---
title: Azure FarmBeats-arkitektur
description: Beskriver arkitekturen i Azure FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: ebc1cdc7c762117851072037624f11e9fa98b6f1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182612"
---
# <a name="integration-patterns"></a>Integreringsmönster

Azure FarmBeats är ett erbjudande för affärs till företag som finns på Azure Marketplace. FarmBeats möjliggör agg regering av jordbruks data uppsättningar mellan leverantörer och skapandet av användbara insikter genom att bygga artificiell intelligens (AI) eller Machine Learning (ML) genom att fusing data uppsättningarna.

![Taktslag i projekt grupp](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

I följande avsnitt beskrivs integrerings mönstret för Azure FarmBeats.

## <a name="why-integrate-with-azure-farmbeats"></a>Varför integreras med Azure FarmBeats?

Det här avsnittet fokuserar på partners som vill integrera sina data system (t. ex. sensorer, drönare, väder stationer) i Azure FarmBeats.

Azure FarmBeats är ett utöknings Bart erbjudande som gör det möjligt för jordbruks företag att lägga till sina olika historiska och real tids data uppsättningar i en enda plattform. Azure FarmBeats hjälper en jord handel att normalisera, sätta och samla in sina data i en server grupps kontext.

Genom att bli en data partner med Azure FarmBeats kan du öppna dina system för att komma igång och kontakta fler kunder med dina data erbjudanden. Azure FarmBeats tillhandahåller ett utöknings Bart API-lager som kallas Datahub, som hjälper dig att mata in data från dina enheter systematiskt och till ett standardiserat schema.

När data är tillgängliga i dina kunders Azure FarmBeats-instans kan dina kunder bygga bättre analyser och verktyg ovanpå dina data.

## <a name="next-steps"></a>Nästa steg

Mer information om integrering av sensor data finns i integrering av [sensor data](sensor-partner-integration-in-azure-farmbeats.md) och för integrering av bilder-partner, se [bilder partner integration](imagery-partner-integration-in-azure-farmbeats.md).
