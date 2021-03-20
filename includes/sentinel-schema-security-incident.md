---
title: ta med fil
description: ta med fil
services: azure-sentinel
author: yelevin
ms.service: azure-sentinel
ms.topic: include
ms.date: 06/28/2020
ms.author: yelevin
ms.custom: include file
ms.openlocfilehash: 63cb53dc60a718892d4bf86140e7fd51303bd61c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88761735"
---
### <a name="the-data-model-of-the-schema"></a>Data modellen för schemat

| Fält | Datatyp | Beskrivning |
| ---- | ---- | ---- |
| **AdditionalData** | dynamisk | Antal varningar, antal bok märken, antal kommentarer, namn på aviserings produkter och taktiker |
| **AlertIds** | dynamisk | Aviseringar från vilka incidenten skapades |
| **BookmarkIds** | dynamisk | Entiteter med bok märken |
| **Klassificering** | sträng | Klassificering av incident stängning |
| **ClassificationComment** | sträng | Klassificerings kommentar för incident stängning |
| **ClassificationReason** | sträng | Orsak till stängning av incident |
| **ClosedTime** | datetime | Tidsstämpel (UTC) för när incidenten senast stängdes |
| **Kommentarer** | dynamisk | Incident kommentarer |
| **CreatedTime** | datetime | Tidsstämpel (UTC) för när incidenten skapades |
| **Beskrivning** | sträng | Incidentbeskrivning |
| **FirstActivityTime** | datetime | Första händelse tid |
| **FirstModifiedTime** | datetime | Tidsstämpel (UTC) för när incidenten ändrades första gången |
| **Incidentnamn** | sträng | Internt GUID |
| **IncidentNumber** | int |  |
| **IncidentUrl** | sträng | Länk till incident |
| **Etiketter** | dynamisk | Taggar |
| **LastActivityTime** | datetime | Senaste händelse tid |
| **LastModifiedTime** | datetime | Tidsstämpel (UTC) för när incidenten senast ändrades <br>(ändringen som beskrivs av den aktuella posten) |
| **ModifiedBy** | sträng | Användare eller system som ändrade incidenten |
| **Ägare** | dynamisk |  |
| **RelatedAnalyticRuleIds** | dynamisk | Regler som incidentens aviseringar utlöstes från |
| **Allvarlighetsgrad** | sträng | Incidentens allvarlighets grad (hög/medel/låg/information) |
| **SourceSystem** | sträng | Konstant ("Azure") |
| **Status** | sträng |  |
| **TenantId** | sträng |  |
| **TimeGenerated** | datetime | Tidsstämpel (UTC) för när den aktuella posten skapades <br>(vid ändring av incidenten) |
| **Rubrik** | sträng | 
| **Typ** | sträng | Konstant (' säkerhets incident ') |
|
