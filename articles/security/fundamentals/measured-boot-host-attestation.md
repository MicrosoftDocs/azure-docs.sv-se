---
title: Uppmätta start-och värd attestering i fasta enheter – Azure-säkerhet
description: Teknisk översikt över uppmätta Azure firmware-start-och värd attestering.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: f4906d4e0590df047bac4ee15cb0e996a59c3d5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96498871"
---
# <a name="measured-boot-and-host-attestation"></a>Uppmätt start-och värd attestering
Den här artikeln beskriver hur Microsoft garanterar integritet och säkerhet för värdar genom uppmätt start och värd attestering.

## <a name="measured-boot"></a>Uppmätt start

[Trusted Platform Module](/windows/security/information-protection/tpm/trusted-platform-module-top-node) (TPM) är en manipulerad, kryptografiskt, säker gransknings komponent med inbyggd program vara som tillhandahålls av en betrodd tredje part. Start konfigurations loggen innehåller hash-länkade mätningar som registrerats i den plattforms konfigurations register (PCR) när värden senast genomgick start ordningen. Följande bild visar den här inspelnings processen. Genom att lägga till en tidigare hashad mätning i nästa måttets hash och köra hash-algoritmen i unionen uppnår du hash-länkning.

![Diagram som visar hash-kedje för värd attesterings tjänsten.](./media/measured-boot-host-attestation/hash-chaining.png)

Attestering utförs när en värd ger bevis på sitt konfigurations tillstånd med hjälp av start konfigurations loggen (TCGLog). Förfalskning av en start logg är svår eftersom TPM inte exponerar sina PCR-värden förutom Läs-och utöknings åtgärderna. Dessutom förseglas de autentiseringsuppgifter som angetts av tjänsten för värd attestering till vissa PCR-värden. Användningen av hash-länkning gör det omöjligt att använda falska autentiseringsuppgifter eller att avförsegla autentiseringsuppgifterna för out-of-band.

## <a name="host-attestation-service"></a>Tjänsten värd attestering

Tjänsten för värd attestering är ett förebyggande mått som kontrollerar om värd datorer är pålitliga innan de tillåts interagera med kunddata eller arbets belastningar. Kontroll av värd attesterings tjänsten genom att verifiera en Compliance (verifierbara bevis på värdens efterlevnad) som skickas av varje värd mot en attesterings princip (definition av säkert tillstånd). Integriteten för det här systemet säkerställs av en rot som är [betrodd](https://www.uefi.org/sites/default/files/resources/UEFI%20RoT%20white%20paper_Final%208%208%2016%20%28003%29.pdf) av en TPM.

Tjänsten för värd attestering finns i varje Azure-kluster i en specialiserad låsnings miljö. Den låsta miljön innehåller andra Gatekeeper-tjänster som ingår i värddatorns start protokoll. En PKI (Public Key Infrastructure) fungerar som en mellanhand för att verifiera härkomsten för begäran om attestering och som en identitets utfärdare (beroende på lyckad värd attestering). Autentiseringsuppgifterna för efter attestering som utfärdats till bestyrkande värden är förseglade till sin identitet. Endast den begär ande värden kan avförsegla autentiseringsuppgifterna och utnyttja dem för att få stegvisa behörigheter. Detta förhindrar angrepp i mitten och förfalskning.

Om en Azure-värd kommer från fabrik med en felaktig säkerhets konfiguration eller manipuleras i data centret, innehåller dess TCGLog indikatorer för kompromisser som har flaggats av tjänsten för värd attestering vid nästa attestering, vilket orsakar ett attesterings fel. Attesterings problem förhindrar att Azure-flottan litar på den felande värden. Detta förhindrar effektivt all kommunikation till och från värden och utlöser ett incident arbets flöde. Undersökning och en detaljerad analys efter slakt utförs för att fastställa Rotors Aker och eventuella potentiella angrepps indikationer. Det är bara när analysen är klar och har möjlighet att ansluta till Azure-flottan och ta del av kund arbets belastningar.

Följande är en övergripande arkitektur för tjänsten för värd attestering:

![Diagram som visar arkitekturen för värd attesterings tjänsten.](./media/measured-boot-host-attestation/host-attestation-arch.png)

## <a name="attestation-measurements"></a>Mätningar för attestering

Följande är exempel på många mätningar som registrerats idag.

### <a name="secure-boot-and-secure-boot-keys"></a>Säker start och säkra start nycklar
Genom att verifiera att signatur databasen och de återkallade signaturerna är korrekta, ser tjänsten för värd attestering att klient agenten anser att rätt program vara är betrodd. Genom att validera signaturerna för registrerings nyckel databasen för offentliga nycklar och den offentliga plattforms nyckeln bekräftar tjänsten värd attestering att endast betrodda parter har behörighet att ändra definitionerna för vilken program vara som anses vara betrodd. Slutligen, genom att säkerställa att säker start är aktivt, verifierar tjänsten för värd attestering att dessa definitioner tillämpas.

### <a name="debug-controls"></a>Felsöka kontroller
Fel sökare är kraftfulla verktyg för utvecklare. Men oinskränkt-åtkomsten till minne och andra fel söknings kommandon kan minska data skyddet och systemets integritet om detta beviljas till en icke-betrodd part. Tjänsten värd attestering garanterar att alla typer av fel sökning har inaktiverats vid start på produktions datorer.

### <a name="code-integrity"></a>Kod integritet
UEFI ( [säker start](secure-boot.md) ) garanterar att endast betrodda lågnivå program kan köras under startsekvensen. Samma kontroller måste även tillämpas i efter start miljö för driv rutiner och andra körbara filer med åtkomst till kernelläge. I detta fall används en kod integritets princip (CI) för att definiera vilka driv rutiner, binärfiler och andra körbara filer som betraktas som betrodda genom att ange giltiga och ogiltiga signaturer. Dessa principer tillämpas. Överträdelser av principen genererar aviseringar till teamet för säkerhets incident hantering för undersökning.

## <a name="next-steps"></a>Nästa steg
Mer information om vad vi gör för att driva plattforms integritet och säkerhet finns i:

- [Säkerhet för inbyggd programvara](firmware.md)
- [Säker start](secure-boot.md)
- [Project Cerberus](project-cerberus.md)
- [Kryptering i vila](encryption-atrest.md)
- [Hypervisor-säkerhet](hypervisor.md)