---
title: Arbets flöde för Azure-attestering
description: Arbets flödet för Azure-attestering.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 27a97ceb2ca9a7b58df7200930e4e47d89c9ae89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98762209"
---
# <a name="workflow"></a>Arbetsflöde

Microsoft Azure attesteringen tar emot bevis från enclaves och utvärderar bevis mot Azures säkerhets bas linje och konfigurerbara principer. När verifieringen är klar genererar Azure-attestering en token för attestering för att bekräfta trovärdigheten för enklaven.

Följande aktörer ingår i ett arbets flöde för Azure-attestering:

- **Förlitande part**: komponenten som förlitar sig på Azure-attestering för att verifiera enklaven-giltighet. 
- **Klient**: komponenten som samlar in information från en enklaven och skickar förfrågningar till Azure-attestering. 
- **Azure-attestering**: komponenten som godkänner enklaven-bevis från klienten, validerar den och returnerar attesterings-token till klienten


## <a name="intel-software-guard-extensions-sgx-enclave-validation-work-flow"></a>Intel® Software Guard-tillägg (SGX) enklaven-validering av arbets flöde

Här följer allmänna steg i ett typiskt SGX-enklaven för attestering av arbets flöde (med Azure-attestering):

1. Klienten samlar in bevis från en enklaven. Bevis är information om enklaven-miljön och klient biblioteket som körs i enklaven.
1. Klienten har en URI som refererar till en instans av Azure-attestering. Klienten skickar bevis till Azure-attestering. Exakt den information som skickas till providern beror på typen av enklaven.
1. Azure-attesteringen verifierar den inskickade informationen och utvärderar den mot en konfigurerad princip. Om verifieringen lyckas utfärdar Azure-attestering en attesterings-token och returnerar den till klienten. Om det här steget Miss lyckas rapporterar Azure-attestering ett fel till klienten. 
1. Klienten skickar attesterings-token till förlitande part. Den förlitande parten anropar den offentliga nyckelns metadata-slutpunkt för Azure-attestering för att hämta signerings certifikat. Den förlitande parten verifierar sedan signaturen för attesterings-token och säkerställer enklaven-trovärdigheten. 

![Validerings flöde för SGX-enklaven](./media/sgx-validation-flow.png)

> [!Note]
> När du skickar begäran om attestering i [2018-09-01-Preview API-](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/attestation/data-plane/Microsoft.Attestation/stable/2018-09-01-preview) versionen måste klienten skicka bevis till Azure-attestering tillsammans med Azure AD-åtkomsttoken.

## <a name="trusted-platform-module-tpm-enclave-validation-work-flow"></a>Trusted Platform Module (TPM) enklaven för validering av arbets flöde

Här följer de allmänna stegen i ett typiskt arbets flöde för TPM-enklaven attestering (med Azure-attestering):

1.  Vid start av enhet/plattform, olika start belastningar och start tjänster mäter händelser som backas upp av TPM och lagras på ett säkert sätt (TCG-logg).
2.  Klienten samlar in TCG-loggarna från enhets-och TPM-offerten, som agerar som bevis för attestering.
3.  Klienten har en URI som refererar till en instans av Azure-attestering. Klienten skickar bevis till Azure-attestering. Exakt den information som skickas till providern beror på plattformen.
4.  Azure-attesteringen verifierar den inskickade informationen och utvärderar den mot en konfigurerad princip. Om verifieringen lyckas utfärdar Azure-attestering en attesterings-token och returnerar den till klienten. Om det här steget Miss lyckas rapporterar Azure-attestering ett fel till klienten. Kommunikationen mellan klienten och attesterings tjänsten styrs av TPM-protokollet för Azure-attestering.
5.  Klienten skickar sedan attesterings-token till förlitande part. Den förlitande parten anropar den offentliga nyckelns metadata-slutpunkt för Azure-attestering för att hämta signerings certifikat. Den förlitande parten verifierar sedan signaturen för attesterings-token och säkerställer plattformarna pålitlighet.

![Flöde för TPM-validering](./media/tpm-validation-flow.png)

## <a name="next-steps"></a>Nästa steg
- [Så här skapar och signerar du en policy för attestering](author-sign-policy.md)
- [Konfigurera Azure-attestering med PowerShell](quickstart-powershell.md)
