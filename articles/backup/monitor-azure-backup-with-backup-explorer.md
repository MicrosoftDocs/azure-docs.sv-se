---
title: Övervaka säkerhets kopieringar med Backup Explorer
description: Den här artikeln beskriver hur du använder backup Explorer för att utföra övervakning av säkerhets kopior i valv, prenumerationer, regioner och klienter i real tid.
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 78d3117d363059b6a298740f7b5daa31e10751c8
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294067"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>Övervaka säkerhets kopieringar med Backup Explorer

När organisationer säkerhetskopierar fler och fler datorer till molnet blir det allt viktigare att övervaka dessa säkerhets kopieringar effektivt. Det bästa sättet att börja är att använda en central plats för att Visa drifts information på en stor egendom.

Backup Explorer är en inbyggd Azure Monitor arbets bok som ger Azure Backup kunder denna enda, centrala plats. Backup Explorer hjälper dig att övervaka operativa aktiviteter i hela reserv utrymmet på Azure, för att erbjuda klienter, platser, prenumerationer, resurs grupper och valv. I stort sett har backup Explorer följande funktioner:

* **Perspektiv i skala**: Hämta en sammanställd vy över de säkerhets kopierings objekt, jobb, aviseringar, principer och resurser som ännu inte har kon figurer ATS för säkerhets kopiering över hela egendomen.
* **Analys av detalj nivå**: Visa detaljerad information om var och en av dina jobb, aviseringar, principer och säkerhets kopierings objekt, allt på ett och samma ställe.
* **Åtgärds bara gränssnitt**: när du har identifierat ett problem kan du lösa det genom att gå sömlöst till relevant säkerhets kopierings objekt eller Azure-resurs.

Dessa funktioner tillhandahålls direkt av inbyggd integrering med Azure Resource Graph och Azure Monitor arbets böcker.

> [!NOTE]
>
> * Backup Explorer är för närvarande endast tillgängligt för Azure Virtual Machines (VM)-data.
> * Backup Explorer är avsett att vara en drifts instrument panel för att visa information om dina säkerhets kopior under de senaste 7 dagarna (max).
> * Backup Explorer stöds för närvarande inte i nationella moln.
> * För närvarande stöds inte anpassning av mallen för säkerhets kopierings Utforskaren.
> * Vi rekommenderar inte att du skriver anpassade automations i Azures resurs diagram data.
> * För närvarande kan du övervaka säkerhets kopieringar över högst 1000 prenumerationer (mellan klienter) i Backup Explorer.

## <a name="get-started"></a>Kom igång

Du kan komma åt backup Explorer genom att gå till något av dina Recovery Services-valv och välja länken **Backup Explorer** i fönstret **Översikt** .

![Snabb länk för valvet](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

Om du väljer länken öppnas backup Explorer, som innehåller en sammanställd vy över alla valv och prenumerationer som du har åtkomst till. Om du använder ett Azure Lighthouse-konto kan du visa data över alla klienter som du har åtkomst till. Mer information finns i avsnittet "Cross-Tenant views" i slutet av den här artikeln.

![Start sida för backup Explorer](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>Användnings fall för backup Explorer

Backup Explorer visar flera flikar där du får detaljerad information om en angiven säkerhets kopierings artefakt (till exempel ett säkerhets kopierings objekt, jobb eller en princip). Det här avsnittet innehåller en kort översikt över var och en av flikarna. Videor innehåller exempel på användnings fall för varje säkerhets kopierings artefakt, tillsammans med beskrivningar av tillgängliga kontroller.

### <a name="the-summary-tab"></a>Fliken Sammanfattning

På fliken **Sammanfattning** får du en snabb överblick över det övergripande villkoret för din reserv egendom. Du kan till exempel Visa antalet objekt som skyddas, antalet objekt för vilka skyddet inte har Aktiver ATS eller hur många jobb som lyckades under de senaste 24 timmarna.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="the-backup-items-tab"></a>Fliken säkerhets kopierings objekt

Du kan filtrera och Visa var och en av dina säkerhets kopierings objekt efter prenumeration, valv och andra egenskaper. Genom att välja namnet på ett säkerhets kopierings objekt kan du öppna fönstret Azure för objektet. Från tabellen kan du till exempel Observera att den senaste säkerhets kopieringen misslyckades för objektet *X*. Genom att välja *X* kan du öppna fönstret objekt **säkerhets kopiering** där du kan utlösa en säkerhets kopiering på begäran.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="the-jobs-tab"></a>Fliken jobb

Välj fliken **jobb** om du vill visa information om alla jobb som har utlösts under de senaste 7 dagarna. Här kan du filtrera efter *jobb åtgärd*, *jobb status* och *Felkod* (för misslyckade jobb).

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="the-alerts-tab"></a>Fliken aviseringar

Välj fliken **aviseringar** om du vill visa information om alla aviseringar som har genererats på dina valv under de senaste 7 dagarna. Du kan filtrera aviseringar efter typ *(fel vid säkerhets kopiering* eller *återställning*), aktuell status (*aktiv* eller *löst*) och allvarlighets grad (*kritisk*, *Varning* eller *information*). Du kan också välja en länk för att gå till den virtuella Azure-datorn och vidta nödvändiga åtgärder.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="the-policies-tab"></a>Fliken principer

Du kan välja fliken **principer** om du vill visa viktig information om alla säkerhets kopierings principer som har skapats i din reserv egendom. Du kan visa antalet objekt som är associerade med varje princip, tillsammans med kvarhållningsintervallet och säkerhets kopierings frekvensen som anges i principen.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="the-backup-not-enabled-tab"></a>Fliken säkerhets kopiering är inte aktive rad

Säkerhets kopiering måste vara aktiverat för alla datorer som kräver skydd. Med Backup Explorer kan säkerhets kopierings administratörer snabbt identifiera vilka datorer i en organisation som ännu inte skyddas av säkerhets kopiering. Om du vill visa den här informationen väljer du fliken **säkerhets kopiering inte aktive rad** .

Rutan **säkerhets kopiering är inte aktive rad** visar en tabell med en lista över oskyddade datorer. Din organisation kan tilldela olika taggar till produktions datorer och test datorer, eller till datorer som har en mängd olika funktioner. Eftersom varje dator klass behöver en separat säkerhets kopierings princip hjälper filtrering av taggar dig att visa information som är unik för var och en. Om du väljer namnet på en dator omdirigeras du till den datorns **säkerhets kopierings** fönster, där du kan välja att tillämpa en lämplig säkerhets kopierings princip.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="export-to-excel"></a>Exportera till Excel

Du kan exportera innehållet i en tabell eller ett diagram som ett Excel-kalkylblad. Innehållet exporteras som det är, med befintliga filter tillämpade. Om du vill exportera ytterligare tabell rader kan du öka antalet rader som ska visas på sidan genom att använda List rutan **rader per sida** överst på varje flik.

## <a name="pin-to-the-dashboard"></a>Fäst på instrument panelen

Du kan välja ikonen "fäst" överst i varje tabell eller diagram för att fästa den på din Azure Portal-instrumentpanel. Genom att fästa den här informationen kan du skapa en anpassad instrument panel som är skräddarsydd för att visa den information som är viktigast för dig.

## <a name="cross-tenant-views"></a>Vyer mellan klienter

Om du är en Azure Lighthouse-användare med delegerad åtkomst till prenumerationer i flera klient miljöer kan du använda standard prenumerations filtret. Du visar de prenumerationer som du vill visa data för genom att välja ikonen "filter" längst upp till höger i Azure Portal. När du använder den här funktionen samlar backup Explorer samman information om alla valv i de valda prenumerationerna. Mer information finns i [Vad är Azure Lighthouse?](../lighthouse/overview.md).

## <a name="next-steps"></a>Nästa steg

[Lär dig hur du använder Azure Monitor för att få insikter om dina säkerhets kopierings data](./backup-azure-monitoring-use-azuremonitor.md)
