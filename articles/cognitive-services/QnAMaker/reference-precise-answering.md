---
title: Exakt svar med identifiering av svars intervall – QnA Maker
description: Förstå exakt besvars funktion i QnA Maker hanterade.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 9c7b265118e395dde2b4b5f3959ba397d75ddac5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103232196"
---
# <a name="precise-answering"></a>Exakta svar

Funktionen exakt svars funktion som introducerades i QnA Maker hanterad (för hands version), gör det möjligt att få det exakta korta svaret från den bästa kandidat svars passage som finns i kunskaps basen för alla användar frågor. Den här funktionen använder en djup inlärnings modell som på körning, som förstår avsikten med användar frågan och identifierar det exakta korta svaret från svars passage, om det finns ett kort svar som är ett faktum i svars passagen. 

Den här funktionen är aktive ras som standard i test fönstret så att du kan testa funktionaliteten som är speciell för ditt scenario. Den här funktionen är mycket bra för både innehålls utvecklare och slutanvändare. Innehålls utvecklare behöver nu inte manuellt se specifika QnA-par för varje faktum i kunskaps basen och slutanvändaren behöver inte gå igenom hela svars resultatet som returneras från tjänsten för att hitta det faktiska faktum som svarar på användarens fråga. Du kan hämta [exakta svar via generera svars-API](How-To/metadata-generateanswer-usage.md#get-precise-answers-with-generateanswer-api).

## <a name="precise-answering-on-qna-maker-portal"></a>Exakt svar på QnA Maker Portal

När du öppnar test-fönstret i QnA Maker Portal visas ett alternativ för att **Visa ett kort svar** överst. Det här alternativet är valt som standard. När du anger en fråga i test fönstret visas ett kort svar tillsammans med svaren, om det finns ett kort svar i svars resultatet.
 
![Hanterat aktiverat test fönster](../QnAMaker/media/conversational-context/test-pane-with-managed.png)

Du kan avmarkera alternativet **Visa kort svar** om du bara vill se svars passagerna i test fönstret. 

Tjänsten returnerar också säkerhets poängen för det exakta svaret som ett **svars intervall** som du kan kontrol lera genom att välja alternativet **Granska** som finns under frågan i test fönstret.

![Resultat av hanterade svars omfång](../QnAMaker/media/conversational-context/managed-answer-span-score.png)

## <a name="publishing-a-qna-maker-bot"></a>Publicera en QnA Maker robot

När du publicerar en robot får du den exakta svars upplevelsen som är aktive rad som standard i ditt program, där du kommer att se ett kort svar tillsammans med svarets passage. Se API-referensen för att [generera svar](/rest/api/cognitiveservices/qnamakerv5.0-preview.1/knowledgebase/generateanswer#answerspan) för att se hur du använder det exakta svaret (kallas AnswerSpan) i svaret. Användaren har möjlighet att välja andra upplevelser genom att uppdatera mallen via robot app service. 

## <a name="language-support"></a>Stöd för språk

Den exakta svars funktionen stöds för närvarande bara på engelska.
