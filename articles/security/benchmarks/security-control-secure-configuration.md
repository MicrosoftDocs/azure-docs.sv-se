---
title: Azure säkerhets kontroll – säker konfiguration
description: Säker konfiguration av Azure Security Control
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: f945699256b4de9bd87ee4ab7500268302e93089
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880177"
---
# <a name="security-control-secure-configuration"></a>Säkerhets kontroll: säker konfiguration

Upprätta, implementera och aktivt hantera (spåra, rapportera om, korrigera) säkerhets konfigurationen för Azure-resurser för att förhindra att angripare utnyttjar sårbara tjänster och inställningar.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 7.1 | 5,1 | Kund |

Använd Azure Policy alias för att skapa anpassade principer för att granska eller tillämpa konfigurationen för dina Azure-resurser. Du kan också använda inbyggda Azure Policy definitioner.

Azure Resource Manager har också möjlighet att exportera mallen i JavaScript Object Notation (JSON), vilken bör granskas för att säkerställa att konfigurationerna uppfyller/överskrider säkerhets kraven för din organisation.

Du kan också använda rekommendationer från Azure Security Center som en säker konfigurations bas linje för dina Azure-resurser.

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Självstudie: skapa och hantera principer för att genomdriva efterlevnad](../../governance/policy/tutorials/create-and-manage.md)

- [Exportera en och flera resurser till en mall i Azure Portal](../../azure-resource-manager/templates/export-template-portal.md)

- [Säkerhetsrekommendationer – en referensguide](../../security-center/recommendations-reference.md)

## <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 7.2 | 5,1 | Kund |

Använd Azure Security Center rekommendationer för att underhålla säkerhetskonfigurationer för alla beräknings resurser.  Dessutom kan du använda anpassade operativ system avbildningar eller Azure Automation tillstånds konfiguration för att upprätta säkerhets konfigurationen för det operativ system som krävs av din organisation.

- [Så här övervakar du Azure Security Center rekommendationer](../../security-center/security-center-recommendations.md)

- [Säkerhetsrekommendationer – en referensguide](../../security-center/recommendations-reference.md)

- [Översikt över Azure Automation tillstånds konfiguration](../../automation/automation-dsc-overview.md)

- [Ladda upp en virtuell hård disk och Använd den för att skapa nya virtuella Windows-datorer i Azure](../../virtual-machines/windows/upload-generalized-managed.md)

- [Skapa en virtuell Linux-dator från en anpassad disk med Azure CLI](../../virtual-machines/linux/upload-vhd.md)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 7.3 | 5.2 | Kund |

Använd Azure Policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar i dina Azure-resurser.  Dessutom kan du använda Azure Resource Manager mallar för att underhålla säkerhets konfigurationen för dina Azure-resurser som krävs av din organisation. 

- [Förstå Azure Policys effekter](../../governance/policy/concepts/effects.md)

- [Skapa och hantera principer för att använda kompatibilitet](../../governance/policy/tutorials/create-and-manage.md)

- [Översikt över Azure Resource Manager mallar](../../azure-resource-manager/templates/overview.md)

## <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 7,4 | 5.2 | Delad |

Följ rekommendationerna från Azure Security Center om att utföra sårbarhets bedömningar på dina Azure Compute-resurser.  Dessutom kan du använda Azure Resource Manager mallar, anpassade operativ Systems avbildningar eller Azure Automation tillstånds konfiguration för att underhålla säkerhets konfigurationen för det operativ system som krävs av din organisation.   Mallarna för Microsoft Virtual Machines tillsammans med Azure Automation önskad tillstånds konfiguration kan under lätta möten och upprätthålla säkerhets kraven. 

Observera också att avbildningar av virtuella Azure Marketplace-datorer som publiceras av Microsoft hanteras och underhålls av Microsoft. 

- [Så här implementerar du rekommendationer för Azure Security Center sårbarhets bedömning](../../security-center/deploy-vulnerability-assessment-vm.md)

- [Så här skapar du en virtuell Azure-dator från en Azure Resource Manager-mall](../../virtual-machines/windows/ps-template.md)

- [Översikt över Azure Automation tillstånds konfiguration](../../automation/automation-dsc-overview.md)

- [Skapa en virtuell Windows-dator i Azure Portal](../../virtual-machines/windows/quick-create-portal.md)

- [Information om hur du hämtar mallen för virtuella datorer](/previous-versions/azure/virtual-machines/windows/download-template)

- [Exempelskript för att överföra en virtuell hårddisk till Azure och skapa en ny virtuell dator](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 7.5 | 5.3 | Kund |

Använd Azure-DevOps för att lagra och hantera din kod på ett säkert sätt, till exempel anpassade Azure-principer, Azure Resource Manager mallar och önskade tillstånds konfigurations skript. För att få åtkomst till de resurser som du hanterar i Azure DevOps, kan du bevilja eller neka behörigheter till särskilda användare, inbyggda säkerhets grupper eller grupper som definierats i Azure Active Directory (Azure AD) om det är integrerat med Azure DevOps eller Active Directory om det är integrerat med TFS.

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Om behörigheter och grupper i Azure DevOps](/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 7,6 | 5.3 | Kund |

Om du använder anpassade avbildningar använder du rollbaserad åtkomst kontroll i Azure (Azure RBAC) så att endast behöriga användare får åtkomst till avbildningarna. Med hjälp av ett delat bild galleri kan du dela dina avbildningar till olika användare, tjänst huvud namn eller AD-grupper i din organisation.  För behållar avbildningar lagrar du dem i Azure Container Registry och utnyttjar Azure RBAC för att se till att endast behöriga användare kan komma åt avbildningarna.  

- [Förstå Azure RBAC](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Lär dig mer om Azure RBAC för Container Registry](../../container-registry/container-registry-roles.md)

- [Så här konfigurerar du Azure RBAC](../../role-based-access-control/quickstart-assign-role-user-portal.md)

- [Översikt över delade avbildnings Galleri](../../virtual-machines/shared-image-galleries.md)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 7,7 | 5.4 | Kund |

Definiera och implementera standardinställda säkerhetskonfigurationer för Azure-resurser med hjälp av Azure Policy. Använd Azure Policy alias för att skapa anpassade principer för att granska eller tillämpa nätverks konfigurationen för dina Azure-resurser. Du kan också använda inbyggda princip definitioner som är relaterade till dina speciella resurser.  Dessutom kan du använda Azure Automation för att distribuera konfigurations ändringar.

- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Använda alias](../../governance/policy/concepts/definition-structure.md#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Distribuera konfigurations hanterings verktyg för operativ system

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 7,8 | 5.4 | Kund |

Azure Automation tillstånds konfiguration är en konfigurations hanterings tjänst för DSC-noder (Desired State Configuration) i alla moln eller lokala data Center. Du kan enkelt publicera datorer, tilldela dem deklarativ konfigurationer och Visa rapporter som visar varje dators kompatibilitet med önskat tillstånd som du har angett. 

- [Onboarding-datorer för hantering genom Azure Automation tillstånds konfiguration](../../automation/automation-dsc-onboarding.md)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 7,9 | 5,5 | Kund |

Använd Azure Security Center för att utföra bas linje genomsökningar för dina Azure-resurser.  Du kan också använda Azure Policy för att varna och granska Azure-resursfiler.

- [Så här åtgärdar du rekommendationer i Azure Security Center](../../security-center/security-center-remediate-recommendations.md)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 7,10 | 5,5 | Kund |

Använd Azure Security Center för att utföra bas linje genomsökningar för OS-och Docker-inställningar för behållare.

- [Förstå rekommendationer för Azure Security Center-container](../../security-center/container-security.md)

## <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 7,11 | 13,1 | Kund |

Använd Hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda hemlig hantering för dina moln program.

- [Integrera med Azure Managed Identities](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Så här skapar du en Key Vault](../../key-vault/secrets/quick-create-portal.md)

- [Så här autentiserar du till Key Vault](../../key-vault/general/authentication.md)

- [Så här tilldelar du en Key Vault åtkomst princip](../../key-vault/general/assign-access-policy-portal.md)

## <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 7,12 | 4.1 | Kund |

Använd hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure AD. Med hanterade identiteter kan du autentisera till vilken tjänst som helst som stöder Azure AD-autentisering, inklusive Key Vault utan autentiseringsuppgifter i din kod.

- [Konfigurera hanterade identiteter](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

## <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 7,13 | 18,1, 18,7 | Kund |

Implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault. 

- [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>Nästa steg

- Se nästa säkerhets kontroll: skydd  [mot skadlig kod](security-control-malware-defense.md)