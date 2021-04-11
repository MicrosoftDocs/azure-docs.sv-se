---
title: Vanliga frågor och svar
description: Svar på vanliga frågor om Microsoft Azure attestering
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 9b15a336e97cad1fb03a63ec34f563a4453755ff
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504200"
---
# <a name="frequently-asked-questions-for-microsoft-azure-attestation"></a>Vanliga frågor och svar om Microsoft Azure attestering

Den här artikeln innehåller svar på några av de vanligaste frågorna om [Azure-attestering](overview.md).

Om ditt Azure-problem inte åtgärdas i den här artikeln kan du även skicka en support förfrågan för Azure på [sidan för Azure-supporten](https://azure.microsoft.com/support/options/).

## <a name="what-is-azure-pck-caching-service-and-its-role-in-enclave-attestation"></a>Vad är Azure PCK caching service och dess roll i enklaven-attestering

Tjänsten Azure PCK caching definierar Azures säkerhets bas linje för [ACC-noderna (Azure konfidentiell data behandling)](../confidential-computing/overview.md) från Intel och cachelagrar data. Den cachelagrade informationen används ytterligare av Azure-attestering i verifiera betrodda körnings miljöer (TEEs).  

Azure PCK Caching Service:
   - Erbjuder hög tillgänglighet 
   - Minskar beroenden för externt värdbaserade tjänster och Internet anslutning.
   - Hämtar de senaste versionerna av Intel-certifikat, listor över återkallade certifikat, TCB-information (Trusted Computing Base) och citerar enklaven identitet för ACC-noderna från Intel. Tjänsten bekräftar därför att Azures säkerhets bas linje refereras till av Azure-attestering vid validering av TEEs, vilket avsevärt minskar attesterings felen på grund av invaliditet eller återkallande av Intel-certifikat  

## <a name="is-sgx-attestation-supported-by-azure-attestation-in-non-azure-environments"></a>Stöds SGX-attestering av Azure-attestering i andra miljöer än Azure

Nej. Azure-attestering är beroende av den säkerhets bas linje som anges av tjänsten Azure PCK caching för att verifiera TEEs. Tjänsten Azure PCK caching är för närvarande utformad för att endast stödja Azures data behandlings-noder. 

## <a name="what-validations-does-azure-attestation-perform-for-attesting-sgx-enclaves"></a>Vilka valideringar utför Azure-attestering för att intyga SGX-enclaves

Azure-attestering är ett enhetligt ramverk för fjärrattestering av olika typer av TEEs. Azure-attestering:

   - Verifierar om den betrodda roten för en signerad enklaven-offert tillhör Intel.
   - Validerar om enklaven-offerten uppfyller Azures säkerhets bas linje enligt definitionen i Azure PCK caching service.
   - Verifierar om SHA256-hashen för enklaven inneha data (EHD) i objektet attestering begär ande matchar de första 32 byten i reportData-fältet i enklaven-offerten.
   - Gör det möjligt för kunder att skapa en attesterings leverantör och konfigurera en anpassad princip. Utöver ovanstående validering utvärderar Azure-attestering enklaven-offerten mot principen. Principer definierar auktoriseringsregler för enklaven och avgör även utfärdande regler för generering av attesterings-token. För att bekräfta om avsedd program vara körs i en enklaven kan kunder lägga till auktoriseringsregler för att kontrol lera om **mrsigner** och **mrenclave** fält i enklaven-offerten matchar värdena för kundernas binärfiler.

## <a name="how-can-a-verifier-obtain-the-collateral-for-sgx-attestation-supported-by-azure-attestation"></a>Hur kan en kontrollant få säkerhet för SGX-attestering som stöds av Azure-attestering

I allmänhet för bestyrkande modeller med Intel som förtroende roten, pratar klienten med enklaven-API: er för att hämta enklaven-beviset. Enklaven API: er internt anropar Intel PCK caching service för att hämta Intel-certifikat för noden som ska bestyrkas. Certifikaten används för att signera enklaven-beviset och på så sätt skapa en fjärrkontrollerad säkerhet.  

Samma process kan implementeras för Azure-attestering. Men för att kunna utnyttja fördelarna som erbjuds av tjänsten Azure PCK caching, när du har installerat ACC Virtual Machine, rekommenderar vi att du installerar [Azure DCAP-biblioteket](https://www.nuget.org/packages/Microsoft.Azure.DCAP). Baserat på avtalet med Intel, när Azure DCAP-biblioteket är installerat, omdirigeras förfrågningar om att generera enklaven-bevis från Intel PCK caching service till Azure PCK caching service. Azure DCAP-biblioteket stöds i Windows-och Linux-baserade miljöer.

## <a name="how-to-shift-to-azure-attestation-from-other-attestation-models"></a>Så här växlar du till Azure-attestering från andra attesterings modeller

- När du har installerat den virtuella Azure-datorn för konfidentiella data behandling installerar du Azure DCAP library ([Windows/](https://www.nuget.org/packages/Microsoft.Azure.DCAP/) [Linux](https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/a/az-dcap-client/)) för att utnyttja fördelarna som erbjuds av Azure PCK caching service.
- Klienten för fjärrattestering måste ha skapats som kan hämta enklaven-beviset och skicka förfrågningar till Azure-attestering. Se [kod exempel](/samples/browse/?expanded=azure&terms=attestation) för referens 
- Attesterings begär Anden kan skickas till REST API slut punkten för standard leverantörer eller anpassade providers för attestering 
- API: er för Azure-attestering skyddas av Azure AD-autentisering. Därför måste klienten som anropar attesterings-API: er kunna hämta och skicka en giltig Azure AD-åtkomsttoken i begäran om attestering 

## <a name="how-can-the-relying-party-verify-the-integrity-of-attestation-token"></a>Hur kan den förlitande parten verifiera integriteten för attesterings-token

Attesterings-token som genereras av Azure-attestering signeras med ett självsignerat certifikat. Certifikaten exponeras via en [OpenID-slutpunkt för metadata](/rest/api/attestation/metadataconfiguration/get). Förlitande part kan hämta signerings certifikaten från den här slut punkten och utföra signatur verifiering av attesterings-token. Giltighets tiden för attesterings-token är 8 timmar. 

## <a name="how-to-identify-the-certificate-to-be-used-for-signature-verification-from-the-openid-metadata-endpoint"></a>Så här identifierar du det certifikat som ska användas för signaturverifieringen från OpenID metadata-slutpunkt

Flera certifikat som exponeras i slut punkten för OpenID-metadata motsvarar olika användnings fall (t. ex. SGX-attestering) som stöds av Azure-attestering. Enligt de standarder som anges i [RFC 7515](https://tools.ietf.org/html/rfc7515)kommer certifikatet med nyckel-ID (barn) som matchar *subparameter i* token för attestering att användas för signaturverifiering. Om inget matchande **barn** hittas förväntas det att testa alla certifikat som exponeras av OpenID metadata-slutpunkt.

## <a name="is-it-possible-for-the-relying-party-to-share-secrets-with-the-validated-trusted-execution-environments-tees"></a>Är det möjligt för den förlitande parten att dela hemligheter med de verifierade betrodda körnings miljöerna (TEEs)

En offentlig nyckel som genereras inom en enklaven kan uttryckas i EHD-egenskapen (enklaven hålls data) för begäran om attestering av begäran som skickas av klienten till Azure-attestering. När du har bekräftat om SHA256-hashvärdet för EHD uttrycks i reportData-fältet i offerten innehåller Azure-attestering EHD i attesterings-token. Förlitande part kan använda EHD från svaret för verifierat attestering för att kryptera hemligheterna och dela dem med enklaven. Mer information finns i [grundläggande begrepp för Azure-attestering](basic-concepts.md) .
