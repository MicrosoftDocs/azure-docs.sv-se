---
title: Vill du migrera Microsoft Translator Hub-arbetsytan och-projekt? – Anpassad översättare
titleSuffix: Azure Cognitive Services
description: Den här artikeln förklarar hur du migrerar din hubb och dina projekt till Azure Cognitive Services anpassad översättare.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 89658ce88b4f1ac9d5bacac7bd45511b4aa0a1be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895719"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Migrera Hub-arbetsytan och projekt till anpassad översättare

Du kan enkelt migrera [Microsoft Translator Hub](https://hub.microsofttranslator.com/) -arbetsytan och-projekt till anpassad översättare. Migrering initieras från Microsoft Hub genom att välja en arbets yta eller ett projekt och sedan välja en arbets yta i anpassad översättare och sedan välja de utbildningar som du vill överföra. När migreringen startar överförs de valda utbildnings inställningarna till alla relevanta dokument. Distribuerade modeller är utbildade och kan distribueras när de är klara.

De här åtgärderna utförs under migreringen:
* Alla dokument och projekt definitioner får sina namn överförda med tillägget "hub_" som redan har prefixet till namnet. Automatiskt genererade test-och justerings data får namnet hub_systemtune_ \<modelid> eller hub_systemtest_ \<modelid> .
* Alla utbildningar som befann sig i det distribuerade läget när migreringen sker kommer automatiskt att tränas med dokumenten i hubb inlärningen. Den här utbildningen kommer inte att debiteras för din prenumeration. Om automatisk distribution valdes för migreringen distribueras den tränade modellen när den är klar. Vanliga värd avgifter kommer att tillämpas.
* Alla migrerade utbildningar som inte befann sig i det distribuerade läget kommer att placeras i det migrerade utkastet. I det här läget har du möjlighet att träna en modell med den migrerade definitionen, men regelbundna utbildnings avgifter kommer att gälla.
* BLEU-poängen som migreras från hubben i någon punkt finns i TrainingDetails-sidan i modellen i rubriken "Bleu score i MT Hub".

> [!Note] 
> För att en utbildning ska lyckas kräver anpassad översättare minst 10 000 unika extraherade meningar. Anpassad översättare kan inte utföra en utbildning med färre än det [föreslagna minimivärdet](./sentence-alignment.md#suggested-minimum-number-of-sentences).

## <a name="find-custom-translator-workspace-id"></a>Hitta anpassad Translator-arbetsyte-ID

Om du vill migrera [Microsoft Translator Hub](https://hub.microsofttranslator.com/) -arbetsytan måste du ha destinations arbetsyte-ID i anpassad översättare. Mål arbets ytan i den anpassade översättaren är där alla dina nav arbets ytor och projekt ska migreras till.

Du hittar ditt mål arbets ytans ID på den anpassade översättnings sidans inställnings sida:

1. Gå till sidan "inställning" på portalen för anpassad översättare.

2. Du hittar arbetsyte-ID: t i avsnittet grundläggande information.

    ![Så här hittar du mål arbets ytans ID](media/how-to/how-to-find-destination-ws-id.png)

3. Behåll mål arbets ytans ID för att referera till under migreringsprocessen.

## <a name="migrate-a-project"></a>Migrera ett projekt

Om du vill migrera dina projekt selektivt ger Microsoft Translator-hubben möjlighet.

Så här migrerar du ett projekt:

1. Logga in på Microsoft Translator Hub.

2. Gå till sidan projekt.

3. Klicka på migrera länken för lämpligt projekt.

    ![Skärm bild som visar knappen migrera för det valda projektet.](media/how-to/how-to-migrate-from-hub.png)

4. När du trycker på länken migrera visas ett formulär där du kan:
   * Ange den arbets yta som du vill överföra till på en anpassad översättare
   * Ange om du vill överföra alla utbildningar med lyckade utbildningar eller bara de distribuerade träningarna. Som standard kommer alla lyckade utbildningar att överföras.
   * Ange om du vill att din utbildning ska distribueras automatiskt när inlärningen är slutförd. Som standard kommer din utbildning inte att distribueras automatiskt när den har slutförts.

5. Klicka på skicka begäran.

## <a name="migrate-a-workspace"></a>Migrera en arbets yta

Förutom att migrera ett enskilt projekt kan du även migrera alla projekt med lyckade utbildningar i en arbets yta. Detta gör att varje projekt i arbets ytan utvärderas som om migreringen hade tryckts ned. Den här funktionen är lämplig för användare med många projekt som vill migrera alla till en anpassad översättare med samma inställningar. Migreringen av en arbets yta kan initieras från sidan Inställningar i Translator Hub.

Så här migrerar du en arbets yta:

1. Logga in på Microsoft Translator Hub.

2. Gå till sidan Inställningar.

3. På sidan inställningar klickar du på migrera data från arbets ytan till anpassad översättare.

    ![Skärm bild som visar alternativet migrera arbets ytans data till anpassat Translator.](media/how-to/how-to-migrate-workspace-from-hub.png)

4. På nästa sida väljer du något av följande två alternativ:

    a. Endast distribuerade utbildningar: om du väljer det här alternativet migreras bara dina distribuerade system och relaterade dokument.

    b. Alla lyckade utbildningar: om du väljer det här alternativet migreras alla lyckade utbildningar och relaterade dokument.

    c. Ange mål arbets ytans ID i anpassad översättare.

    ![Migrera från hubben](media/how-to/how-to-migrate-from-hub-screen.png)

5. Klicka på skicka begäran.

## <a name="migration-history"></a>Historik för migrering

När du har begärt arbets ytan/projekt-migrering från hubben hittar du din migrerings historik på sidan med inställningar för anpassad översättare.

Följ dessa steg om du vill visa en historik för migreringen:

1. Gå till sidan "inställning" på portalen för anpassad översättare.

2. I avsnittet migrerings historik på sidan inställningar klickar du på migrera historik.

    ![Historik för migrering](media/how-to/how-to-migration-history.png)

Sidan migrera historik visar följande information som Sammanfattning för varje migrering som du har begärt.

1. Migrerad av: namn och e-postadress för användaren som skickade begäran om migrering

2. Migrerad: datum och tidstämpel för migreringen

3. Projekt: antal projekt som begärdes för migrering v/s antal projekt har migrerats.

4. Utbildningar: antalet utbildningar som begärs för migrering v/s antal utbildningar har migrerats.

5. Dokument: antalet dokument som begärdes för migrering v/s antal dokument har migrerats.

    ![Information om historik för migrering](media/how-to/how-to-migration-history-details.png)

Om du vill ha mer detaljerad migrering om dina projekt, utbildningar och dokument, har du alternativ exportera information som CSV.

## <a name="implementation-notes"></a>Implementeringsanteckningar
* System med språk par som ännu inte är tillgängliga i en anpassad översättare är bara tillgängliga för att komma åt data eller avdistribuera via anpassad översättare. Dessa projekt kommer att markeras som "ej tillgängliga" på sidan projekt. När vi aktiverar nya språk par med anpassad översättare blir projekten aktiva för att träna och distribuera. 
* Migrering av ett projekt från hubb till anpassad översättare påverkar inte dina nav eller projekt. Vi tar inte bort projekt eller dokument från hubben under en migrering och vi avinstallerar inte modeller.
* Du får bara migrera en gång per projekt. Om du behöver upprepa en migrering i ett projekt kan du kontakta oss.
* Anpassad översättare stöder NMT språk par till och från engelska. [Visa en fullständig lista över språk som stöds](../language-support.md#customization). Hubben kräver inte bas linje modeller och stöder därför flera tusen språk. Du kan migrera ett språk par som inte stöds, men vi kommer bara att utföra migreringen av dokument och projekt definitioner. Vi kommer inte att kunna träna den nya modellen. Dessa dokument och projekt visas dessutom som inaktiva för att indikera att de inte kan användas för tillfället. Om support läggs till för de här projekten och/eller dokumenten blir de aktiva och kan tränas.
* Anpassad översättare stöder för närvarande inte monolingual inlärnings data. Precis som språk par som inte stöds kan du migrera monolingual-dokument, men de visas som inaktiva tills monolingual-data stöds.
* Anpassad översättare kräver 10 000 parallella meningar för att träna. Microsoft Hub kan träna på en mindre uppsättning data. Om en utbildning migreras som inte uppfyller det här kravet, kommer den inte att tränas.

## <a name="custom-translator-versus-hub"></a>Anpassad översättare jämfört med hubb

I den här tabellen jämförs funktionerna mellan Microsoft Translator Hub och den anpassade översättare.

| Funktion | Hubb | Custom Translator |
| ------- | :-: | :---------------: |
| Status för anpassnings funktion    | Allmän tillgänglighet    | Allmän tillgänglighet |
| Text-API-version    | V2     | V3  |
| SMT-anpassning    | Ja    | Inga |
| Anpassning av NMT    | Inga    | Ja |
| Ny anpassning av enhetligt tal tjänster    | Inga    | Ja |
| Ingen spårning | Ja | Ja |

## <a name="new-languages"></a>Nya språk

Om du är en community eller organisation som arbetar med att skapa ett nytt språk system för Translator kan du kontakta [custommt@microsoft.com](mailto:custommt@microsoft.com) för mer information.

## <a name="next-steps"></a>Nästa steg

- [Träna en modell](how-to-train-model.md).
- Börja använda din distribuerade anpassade översättnings modell via [Translator v3](../reference/v3-0-translate.md?tabs=curl).