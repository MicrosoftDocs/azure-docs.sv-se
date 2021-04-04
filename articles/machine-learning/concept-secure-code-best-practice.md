---
title: Metod tips för säker kod
titleSuffix: Azure Machine Learning
description: Lär dig om potentiella säkerhetshot som kan uppstå när du utvecklar för Azure Machine Learning, åtgärder och bästa praxis.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: cjgronlund
ms.date: 11/12/2019
ms.openlocfilehash: 37cb70bdbd1e3c87eeb994e0959c6214822d22ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93322975"
---
# <a name="secure-code-best-practices-with-azure-machine-learning"></a>Metod tips för säker kod med Azure Machine Learning

I Azure Machine Learning kan du ladda upp filer och innehåll från valfri källa till Azure. Innehåll i Jupyter-anteckningsböcker eller skript som du läser in kan eventuellt läsa data från dina sessioner, komma åt data i din organisation i Azure eller köra skadliga processer för din räkning.

> [!IMPORTANT]
> Kör bara antecknings böcker eller skript från betrodda källor. Till exempel där du eller ditt säkerhets team har granskat antecknings boken eller skriptet.

## <a name="potential-threats"></a>Potentiella hot

Utveckling med Azure Machine Learning ofta involverar webbaserade utvecklings miljöer (Notebooks & Azure ML Studio). När du använder webbaserade utvecklings miljöer är de potentiella hoten:

* [Cross Site Scripting (XSS)](https://owasp.org/www-community/attacks/xss/)

    * __Dom-inmatning__: den här typen av attack kan ändra gränssnittet som visas i webbläsaren. Till exempel genom att ändra hur körnings knappen fungerar i en Jupyter Notebook.
    * Åtkomsttoken __/cookies__: XSS-attacker kan också komma åt lokala lagrings enheter och webb läsar cookies. Din Azure Active Directory-autentiseringstoken (AAD) lagras i lokal lagring. Ett XSS-angrepp kan använda denna token för att skapa API-anrop för din räkning och sedan skicka data till ett externt system eller API.

* [CSRF (Cross Site request förfalskning)](https://owasp.org/www-community/attacks/csrf): det här angreppet kan ersätta URL: en för en bild eller länk med URL: en för ett skadligt skript eller API. När bilden läses in eller när du klickar på länken, görs ett anrop till URL: en.

## <a name="azure-ml-studio-notebooks"></a>Bärbara Azure ML Studio-datorer

Azure Machine Learning Studio tillhandahåller en miljö för en värdbaserad bärbar dator i webbläsaren. Celler i en bärbar dator kan skriva ut HTML-dokument eller fragment som innehåller skadlig kod.  När utdata återges kan koden köras.

__Möjliga hot__:
* Cross Site Scripting (XSS)
* Förfalskning av begäran mellan webbplatser (CSRF)

Åtgärder __som tillhandahålls av Azure Machine Learning__:
* __Kod cellens utdata__ är i begränsat läge i en iframe. Iframe hindrar skriptet från att komma åt den överordnade DOM, cookies eller session Storage.
* __Markdown cell__ innehåll rensas med hjälp av dompurify-biblioteket. Detta blockerar skadliga skript från att köras med markdown-celler återges.
* __Bild-URL__ och __markdown-länkar__ skickas till en Microsoft-ägda slut punkt, som söker efter skadliga värden. Om ett skadligt värde identifieras avvisar slut punkten begäran.

__Rekommenderade åtgärder__:
* Kontrol lera att innehållet i filerna är tillförlitligt innan du laddar upp till Studio. När du laddar upp måste du bekräfta att du överför betrodda filer.
* När du väljer en länk för att öppna ett externt program uppmanas du att lita på programmet.

## <a name="azure-ml-compute-instance"></a>Instans av Azure ML-beräkning

Azure Machine Learning Compute instance är värd för __Jupyter__ och __Jupyter Lab__. När du använder någon av cellerna i en bärbar dator eller kod i kan du skriva HTML-dokument eller fragment som innehåller skadlig kod. När utdata återges kan koden köras. Samma hot gäller även när du använder __RStudio__ som finns på en beräknings instans.

__Möjliga hot__:
* Cross Site Scripting (XSS)
* Förfalskning av begäran mellan webbplatser (CSRF)

Åtgärder __som tillhandahålls av Azure Machine Learning__:
* Inga. Jupyter-och Jupyter-labb är program med öppen källkod som finns på Azure Machine Learning Compute-instansen.

__Rekommenderade åtgärder__:
* Kontrol lera att innehållet i filerna är tillförlitligt innan du laddar upp till Studio. När du laddar upp måste du bekräfta att du överför betrodda filer.

## <a name="report-security-issues-or-concerns"></a>Rapportera säkerhets problem eller problem 

Azure Machine Learning är berättigad enligt Microsoft Azure Bounty-programmet. Mer information finns på  [https://www.microsoft.com/msrc/bounty-microsoft-azure](https://www.microsoft.com/msrc/bounty-microsoft-azure) .

## <a name="next-steps"></a>Nästa steg

* [Företags säkerhet för Azure Machine Learning](concept-enterprise-security.md)