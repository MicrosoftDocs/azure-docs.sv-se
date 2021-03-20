---
title: Fråga efter krav
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/30/2020
ms.author: mbullwin
ms.openlocfilehash: 777e3261bd6f842861ed6622b78589eedaaf65d7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92043225"
---
I frågan använder du `@StartTime` parametern för att hämta mått data för en enskild tidsstämpel. Metrics Advisor ersätter parametern med en `yyyy-MM-ddTHH:mm:ss` format sträng när frågan körs.

> [!IMPORTANT]
> Frågan ska returnera högst en post för varje dimensions kombination vid varje tidsstämpel. Och alla poster som returneras av frågan måste ha samma tidsstämplar. Metrics Advisor kör den här frågan för varje tidsstämpel för att mata in dina data. Mer information och exempel finns i [avsnittet Vanliga frågor och svar om frågor](../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data) . 