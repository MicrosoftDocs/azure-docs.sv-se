---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c925eb4e9cbd9fa5df472844d7e3bcae278c2102
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98860023"
---
## <a name="azure-security-benchmark"></a>Benchmark för Azure-säkerhet

[Azures säkerhets benchmark](../../../../articles/security/benchmarks/overview.md) ger rekommendationer om hur du kan skydda dina moln lösningar på Azure. Om du vill se hur den här tjänsten är fullständigt mappad till Azures säkerhets benchmark kan du läsa mer i [Azure-mappningen för säkerhets benchmark](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Om du vill se hur tillgängliga Azure Policy inbyggda program för alla Azure-tjänster mappar till den här standarden för efterlevnad, se [Azure policy regelefterlevnad – säkerhet i Azure](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domain |Kontroll-ID |Kontroll rubrik |Policy<br /><sub>(Azure Portal)</sub> |Princip version<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Nätverkssäkerhet |NS-2 |Ansluta privata nätverk tillsammans |[Azure cache för Redis bör finnas i ett virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|Dataskydd |DP-4 |Kryptera känslig information i överföring |[Endast säkra anslutningar till Azure-cachen för Redis ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="azure-security-benchmark-v1"></a>Azure-säkerhet för prestandatest v1

[Azures säkerhets benchmark](../../../../articles/security/benchmarks/overview.md) ger rekommendationer om hur du kan skydda dina moln lösningar på Azure. Om du vill se hur den här tjänsten är fullständigt mappad till Azures säkerhets benchmark kan du läsa mer i [Azure-mappningen för säkerhets benchmark](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Om du vill se hur tillgängliga Azure Policy inbyggda program för alla Azure-tjänster mappar till den här standarden för efterlevnad, se [Azure policy regelefterlevnad – säkerhet i Azure](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domain |Kontroll-ID |Kontroll rubrik |Policy<br /><sub>(Azure Portal)</sub> |Princip version<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Dataskydd |4.4 |Kryptera all känslig information i överföring |[Endast säkra anslutningar till Azure-cachen för Redis ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="cmmc-level-3"></a>CMMC nivå 3

Om du vill se hur tillgängliga Azure Policy inbyggda program för alla Azure-tjänster mappar till den här standarden för efterlevnad, se [Azure policy regelefterlevnad-CMMC nivå 3](../../../../articles/governance/policy/samples/cmmc-l3.md).
Mer information om den här standarden för efterlevnad finns i [cybersäkerhet förfallo modell certifiering (CMMC)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf).

|Domain |Kontroll-ID |Kontroll rubrik |Policy<br /><sub>(Azure Portal)</sub> |Princip version<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Åtkomstkontroll |AC. 1.002 |Begränsa informations systemets åtkomst till de typer av transaktioner och funktioner som behöriga användare tillåts att köra. |[Endast säkra anslutningar till Azure-cachen för Redis ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|System-och kommunikations skydd |SC. 1.175 |Övervaka, kontrol lera och skydda kommunikation (t. ex. information som överförs eller tas emot av organisations system) vid de yttre gränserna och viktiga interna gränser för organisations system. |[Endast säkra anslutningar till Azure-cachen för Redis ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|System-och kommunikations skydd |SC. 3.185 |Implementera kryptografiska mekanismer för att förhindra obehörig utlämnande av CUI under överföringen, om inte annat skyddas av alternativa fysiska skydds nivåer. |[Endast säkra anslutningar till Azure-cachen för Redis ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Om du vill se hur tillgängliga Azure Policy inbyggda program för alla Azure-tjänster mappar till den här standarden för efterlevnad, se [Azure policy regler för regelefterlevnad-HIPAA HITRUST 9,2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md).
Mer information om den här standarden för efterlevnad finns i [HIPAA HITRUST 9,2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

|Domain |Kontroll-ID |Kontroll rubrik |Policy<br /><sub>(Azure Portal)</sub> |Princip version<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Nätverks anslutnings kontroll |0809.01 n2Organizational. 1234-01. n |Nätverks trafiken kontrol leras i enlighet med organisationens åtkomst kontroll princip via brand vägg och andra nätverksrelaterade begränsningar för varje nätverks åtkomst punkt eller extern telekommunikations tjänsts hanterade gränssnitt. |[Endast säkra anslutningar till Azure-cachen för Redis ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Nätverks anslutnings kontroll |0810.01 n2Organizational. 5-01. n |Information som överförs är skyddad och minst krypterad över öppna, offentliga nätverk. |[Endast säkra anslutningar till Azure-cachen för Redis ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Nätverks anslutnings kontroll |0811.01 n2Organizational. 6-01. n |Undantag till trafikflödes principen dokumenteras med ett support uppdrag/affärs behov, varaktigheten för undantag och granskas minst årligen. undantag för trafik flödes princip tas bort när det inte längre stöds av ett explicit uppdrag/affärs behov. |[Endast säkra anslutningar till Azure-cachen för Redis ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Nätverks anslutnings kontroll |0812.01 n2Organizational. 8-01. n |Fjärranslutna enheter som upprättar en icke-fjärr anslutning tillåts inte kommunicera med externa resurser (fjär). |[Endast säkra anslutningar till Azure-cachen för Redis ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Nätverks anslutnings kontroll |0814.01 n1Organizational. 12-01. n |Möjligheten för användare att ansluta till det interna nätverket är begränsad med hjälp av en princip för nekad-till-standard och Tillåt-vid-undantag på hanterade gränssnitt enligt principen för åtkomst kontroll och kraven för kliniska och affärs program. |[Endast säkra anslutningar till Azure-cachen för Redis ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Identifiering av risker relaterade till externa parter |1451.05 iCSPOrganizational. 2 – 05. i |Moln tjänst leverantörer utformar och implementerar kontroller för att minimera och innehålla data säkerhets risker genom korrekt uppdelning av uppgifter, rollbaserad åtkomst och åtkomst med minst privilegier för all personal i sin leverans kedja. |[Endast säkra anslutningar till Azure-cachen för Redis ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Online-transaktioner |0946.09 y2Organizational. 14-09. y |Organisationen kräver att kryptering används mellan, och användningen av elektroniska signaturer av, var och en av de parter som ingår i transaktionen. |[Endast säkra anslutningar till Azure-cachen för Redis ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="nist-sp-800-171-r2"></a>NIST SP 800-171 R2

Om du vill se hur tillgängliga Azure Policy inbyggda program för alla Azure-tjänster mappar till den här standarden för efterlevnad, se [Azure policy regler för efterlevnad-NIST SP 800-171 R2](../../../../articles/governance/policy/samples/nist-sp-800-171-r2.md).
Mer information om den här standarden för efterlevnad finns i [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).

|Domain |Kontroll-ID |Kontroll rubrik |Policy<br /><sub>(Azure Portal)</sub> |Princip version<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|System-och kommunikations skydd |3.13.1 |Övervaka, kontrol lera och skydda kommunikation (t. ex. information som överförs eller tas emot av organisations system) vid de yttre gränserna och viktiga interna gränser för organisations system. |[Endast säkra anslutningar till Azure-cachen för Redis ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|System-och kommunikations skydd |3.13.8 |Implementera kryptografiska mekanismer för att förhindra obehörig utlämnande av CUI under överföringen, om inte annat skyddas av alternativa fysiska skydds nivåer. |[Endast säkra anslutningar till Azure-cachen för Redis ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="nist-sp-800-53-r4"></a>NIST SP 800-53 R4

Om du vill se hur tillgängliga Azure Policy inbyggda program för alla Azure-tjänster mappar till den här standarden för efterlevnad, se [Azure policy regler för efterlevnad-NIST SP 800-53 R4](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md).
Mer information om den här standarden för efterlevnad finns i [NIST SP 800-53 R4](https://nvd.nist.gov/800-53).

|Domain |Kontroll-ID |Kontroll rubrik |Policy<br /><sub>(Azure Portal)</sub> |Princip version<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|System-och kommunikations skydd |SC-8 (1) |Överförings konfidentialitet och integritets \| kryptering eller alternativt fysiskt skydd |[Endast säkra anslutningar till Azure-cachen för Redis ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

