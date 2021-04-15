---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 53d0352448ca6ad231ecc285cc7bf795bf74d9e7
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497327"
---
## <a name="azure-security-benchmark"></a>Benchmark för Azure-säkerhet

[Azure Security Benchmark ger](../../../../articles/security/benchmarks/overview.md) rekommendationer om hur du kan skydda dina molnlösningar i Azure. Om du vill se hur den här tjänsten helt mappar till Azure Security Benchmark kan du gå till [mappningsfilerna för Azure Security Benchmark.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Information om hur tillgängliga Azure Policy för alla Azure-tjänster mappar till den här efterlevnadsstandarden finns i [Azure Policy regelefterlevnad – Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Nätverkssäkerhet |NS-2 |Koppla samman privata nätverk |[Azure Cache for Redis ska finnas i ett virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|Dataskydd |DP-4 |Kryptera känslig information under överföring |[Endast säkra anslutningar till Azure Cache for Redis bör vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="azure-security-benchmark-v1"></a>Prestandatest för Azure-säkerhet v1

[Azure Security Benchmark ger](../../../../articles/security/benchmarks/overview.md) rekommendationer om hur du kan skydda dina molnlösningar i Azure. Om du vill se hur den här tjänsten helt mappar till Azure Security Benchmark kan du gå till [mappningsfilerna för Azure Security Benchmark.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Information om hur tillgängliga Azure Policy för alla Azure-tjänster mappar till den här efterlevnadsstandarden finns i [Azure Policy regelefterlevnad – Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Dataskydd |4.4 |Kryptera all känslig information under överföring |[Endast säkra anslutningar till Azure Cache for Redis bör vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="cmmc-level-3"></a>CMMC Nivå 3

Information om hur tillgängliga Azure Policy för alla Azure-tjänster mappar till den här efterlevnadsstandarden finns i [Azure Policy regelefterlevnad – CMMC Nivå 3.](../../../../articles/governance/policy/samples/cmmc-l3.md)
Mer information om den här efterlevnadsstandarden finns [i Cybersecurity Maturity Model Certification (CMMC).](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf)

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Åtkomstkontroll |AC.1.002 |Begränsa informationssystemets åtkomst till de typer av transaktioner och funktioner som behöriga användare tillåts att köra. |[Endast säkra anslutningar till Azure Cache for Redis bör vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|System- och kommunikationsskydd |SC.1.175 |Övervaka, kontrollera och skydda kommunikation (dvs. information som överförs eller tas emot av organisationssystem) vid de externa gränserna och viktiga interna gränser i organisationssystem. |[Endast säkra anslutningar till Azure Cache for Redis bör vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|System- och kommunikationsskydd |SC.3.185 |Implementera kryptografiska mekanismer för att förhindra obehörigt avslöjande av CUI under överföring om inget annat skyddas av alternativa fysiska skydd. |[Endast säkra anslutningar till Azure Cache for Redis bör vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Information om hur de tillgängliga Azure Policy för alla Azure-tjänster mappas till den här efterlevnadsstandarden finns [i Azure Policy-regelefterlevnad – HIPAA HITRUST 9.2.](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md)
Mer information om den här efterlevnadsstandarden finns [i HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Kontroll av nätverksanslutning |0809.01n2Organizational.1234 - 01.n |Nätverkstrafiken styrs i enlighet med organisationens princip för åtkomstkontroll via brandväggen och andra nätverksrelaterade begränsningar för varje nätverksåtkomstpunkt eller externa telekommunikationstjänsts hanterade gränssnitt. |[Endast säkra anslutningar till Azure Cache for Redis bör vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Kontroll av nätverksanslutning |0810.01n2Organizational.5 – 01.n |Överförd information skyddas och krypteras som minst över öppna offentliga nätverk. |[Endast säkra anslutningar till Azure Cache for Redis bör vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Kontroll av nätverksanslutning |0811.01n2Organizational.6 – 01.n |Undantag till trafikflödesprincipen dokumenteras med stöd för verksamhets-/verksamhets behov, varaktighet för undantaget och granskas minst en gång per år. undantag för trafikflödesprincipen tas bort när de inte längre stöds av ett uttryckligt uppdrags-/affärs behov. |[Endast säkra anslutningar till Azure Cache for Redis bör vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Anslutningskontroll för nätverk |0812.01n2Organizational.8 – 01.n |Fjärranslutna enheter som upprättar en icke-fjärranslutning tillåts inte att kommunicera med externa (fjärranslutna) resurser. |[Endast säkra anslutningar till Azure Cache for Redis bör vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Anslutningskontroll för nätverk |0814.01n1Organizational.12 - 01.n |Möjligheten för användare att ansluta till det interna nätverket är begränsad med hjälp av en princip för att neka som standard och "tillåt som undantag" i hanterade gränssnitt enligt principen för åtkomstkontroll och kraven för sjukdoms- och affärsprogram. |[Endast säkra anslutningar till Azure Cache for Redis bör vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Identifiering av risker relaterade till externa parter |1451.05iCSPOrganizational.2 – 05.i |Molntjänstleverantörer utformar och implementerar kontroller för att minimera och begränsa datasäkerhetsrisker genom korrekt uppdelning av uppgifter, rollbaserad åtkomst och åtkomst med minsta behörighet för all personal i leveranskedjan. |[Endast säkra anslutningar till Azure Cache for Redis bör vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Onlinetransaktioner |0946.09y2Organizational.14 - 09.y |Organisationen kräver kryptering mellan och användning av elektroniska signaturer av var och en av de parter som är inblandade i transaktionen. |[Endast säkra anslutningar till Azure Cache for Redis bör vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="iso-270012013"></a>ISO 27001:2013

Information om hur de tillgängliga Azure Policy för alla Azure-tjänster mappar till den här efterlevnadsstandarden finns [i Azure Policy regelefterlevnad – ISO 27001:2013.](../../../../articles/governance/policy/samples/iso-27001.md)
Mer information om den här efterlevnadsstandarden [finns i ISO 27001:2013.](https://www.iso.org/isoiec-27001-information-security.html)

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Kryptografi |10.1.1 |Princip för användning av kryptografiska kontroller |[Endast säkra anslutningar till Azure Cache for Redis bör vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Kommunikationssäkerhet |13.2.1 |Principer och procedurer för informationsöverföring |[Endast säkra anslutningar till Azure Cache for Redis bör vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="new-zealand-ism-restricted"></a>Nya Zeeland ISM Restricted

Information om hur de tillgängliga Azure Policy för alla Azure-tjänster mappas till den här efterlevnadsstandarden finns i Azure Policy regelefterlevnad [– Nya Zeeland ISM Restricted](../../../../articles/governance/policy/samples/new-zealand-ism.md).
Mer information om den här efterlevnadsstandarden finns [i Nya Zeeland ISM Restricted](https://www.nzism.gcsb.govt.nz/).

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Programvarusäkerhet |SS-8 |14.5.8 Webbprogram |[Endast säkra anslutningar till Azure Cache for Redis bör vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Datahantering |DM-6 |20.4.4 Databasfiler |[Endast säkra anslutningar till Azure Cache for Redis bör vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="nist-sp-800-171-r2"></a>NIST SP 800-171 R2

Information om hur tillgängliga Azure Policy för alla Azure-tjänster mappar till den här efterlevnadsstandarden finns [i Azure Policy Regulatory Compliance - NIST SP 800-171 R2](../../../../articles/governance/policy/samples/nist-sp-800-171-r2.md).
Mer information om den här efterlevnadsstandarden finns [i NIST SP 800-171 R2.](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final)

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|System- och kommunikationsskydd |3.13.1 |Övervaka, kontrollera och skydda kommunikation (dvs. information som överförs eller tas emot av organisationssystem) vid de externa gränserna och viktiga interna gränser i organisationssystem. |[Endast säkra anslutningar till Azure Cache for Redis bör vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|System- och kommunikationsskydd |3.13.8 |Implementera kryptografiska mekanismer för att förhindra obehörigt avslöjande av CUI under överföring om inget annat skyddas av alternativa fysiska skydd. |[Endast säkra anslutningar till Azure Cache for Redis bör vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="nist-sp-800-53-r4"></a>NIST SP 800-53 R4

Information om hur tillgängliga Azure Policy för alla Azure-tjänster mappar till den här efterlevnadsstandarden finns [i Azure Policy Regelefterlevnad – NIST SP 800-53 R4.](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md)
Mer information om den här efterlevnadsstandarden finns [i NIST SP 800-53 R4.](https://nvd.nist.gov/800-53)

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|System- och kommunikationsskydd |SC-8 (1) |Kryptografiskt eller alternativt \| fysiskt skydd för överföring av konfidentialitet och integritet |[Endast säkra anslutningar till Azure Cache for Redis bör vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

