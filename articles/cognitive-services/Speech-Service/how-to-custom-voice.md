---
title: Förbättra syntesen med anpassade röst tal tjänster
titleSuffix: Azure Cognitive Services
description: Anpassad röst är en uppsättning online-verktyg som du kan använda för att skapa en igenkännings bara röst för ditt varumärke. Allt det krävs för att komma igång är en fåtal av ljudfiler och tillhör ande avskrifter. Följ länkarna nedan för att börja skapa en anpassad tal-till-text-upplevelse.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: trbye
ms.openlocfilehash: 756c40be52f2739d3a7682752b038bfe3958d47d
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99507718"
---
# <a name="get-started-with-custom-voice"></a>Komma igång med Custom Voice

[Anpassad röst](https://aka.ms/customvoice) är en uppsättning online-verktyg som du kan använda för att skapa en igenkännings bara röst för ditt varumärke. Allt det krävs för att komma igång är en fåtal av ljudfiler och tillhör ande avskrifter. Följ länkarna nedan för att börja skapa en anpassad text till tal-upplevelse.

## <a name="whats-in-custom-voice"></a>Vad är en anpassad röst?

Innan du börjar med anpassad röst behöver du ett Azure-konto och en röst tjänst prenumeration. När du har skapat ett konto kan du förbereda dina data, träna och testa dina modeller, utvärdera röst kvalitet och slutligen distribuera din anpassade röst modell.

Diagrammet nedan visar stegen för att skapa en anpassad röst modell med hjälp av den [anpassade röst portalen](https://aka.ms/customvoice). Använd länkarna för att lära dig mer.

![Diagram över anpassad röst arkitektur](media/custom-voice/custom-voice-diagram.png)

1. [Prenumerera och skapa ett projekt](#set-up-your-azure-account) – skapa ett Azure-konto och skapa en prenumeration på tal tjänsten. Den här enhetliga prenumerationen ger dig åtkomst till tal översättning av tal till text, text till tal, tal översättning och den anpassade röst portalen. Sedan kan du skapa ditt första anpassade röst projekt med hjälp av din röst tjänst prenumeration.

2. [Ladda upp data](how-to-custom-voice-create-voice.md#upload-your-datasets) överförings data (ljud och text) med hjälp av den anpassade röst portalen eller det anpassade röst-API: et. Från portalen kan du undersöka och utvärdera uttal-resultat och signal-till-brus-förhållande. Mer information finns i [så här förbereder du data för anpassad röst](how-to-custom-voice-prepare-data.md).

3. [Träna din modell](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) – Använd dina data för att skapa en anpassad röst modell för text till tal. Du kan träna en modell på olika språk. Efter utbildning, testa din modell och om du är nöjd med resultatet kan du distribuera modellen.

4. [Distribuera din modell](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) – skapa en anpassad slut punkt för din text till tal-röst modell och Använd den för tal syntes i dina produkter, verktyg och program.

## <a name="custom-neural-voices"></a>Anpassade neurala-röster

Neurala röst anpassnings funktion finns för närvarande i offentlig för hands version, begränsad till utvalda kunder. Fyll i det här [program formuläret](https://go.microsoft.com/fwlink/?linkid=2108737) för att komma igång.

## <a name="set-up-your-azure-account"></a>Konfigurera ditt Azure-konto

En röst tjänst prenumeration krävs innan du kan använda Custom Speech Portal för att skapa en anpassad modell. Följ de här anvisningarna för att skapa en prenumeration på en röst tjänst i Azure. Om du inte har ett Azure-konto kan du registrera dig för ett nytt.  

När du har skapat ett Azure-konto och en prenumeration på en röst tjänst måste du logga in på den anpassade röst portalen och ansluta din prenumeration.

1. Hämta din prenumerations nyckel för röst tjänsten från Azure Portal.
2. Logga in på den [anpassade röst portalen](https://aka.ms/custom-voice).
3. Välj din prenumeration och skapa ett tal projekt.
4. Om du vill växla till en annan tal prenumeration använder du kugg hjuls-ikonen som finns i det övre navigerings fönstret.

> [!NOTE]
> Du måste ha en F0-eller S0-nyckel som skapats i Azure innan du kan använda tjänsten.

## <a name="how-to-create-a-project"></a>Så här skapar du ett projekt

Innehåll som data, modeller, tester och slut punkter organiseras i **projekt** i den anpassade röst portalen. Varje projekt är specifika för ett land/språk och köns kön för den röst du vill skapa. Du kan till exempel skapa ett projekt för en hona röst för samtals centrets chatt-robotar som använder engelska i USA (en-US).

Skapa ditt första projekt genom att välja fliken **text-till-tal/anpassad röst** och klicka på **nytt projekt**. Följ anvisningarna i guiden för att skapa projektet. När du har skapat ett projekt visas fyra flikar: **data**, **utbildning**, **testning** och **distribution**. Använd länkarna som beskrivs i [Nästa steg](#next-steps) för att lära dig hur du använder varje flik.

> [!IMPORTANT]
> Den [anpassade röst portalen](https://aka.ms/custom-voice) har nyligen uppdaterats! Om du har skapat tidigare data, modeller, tester och publicerade slut punkter i CRIS.ai-portalen eller med API: er, måste du skapa ett nytt projekt i den nya portalen för att ansluta till dessa gamla entiteter.

## <a name="next-steps"></a>Nästa steg

- [Förbered anpassade röst data](how-to-custom-voice-prepare-data.md)
- [Skapa en anpassad röst](how-to-custom-voice-create-voice.md)
- [Guide: spela in dina röst exempel](record-custom-voice-samples.md)