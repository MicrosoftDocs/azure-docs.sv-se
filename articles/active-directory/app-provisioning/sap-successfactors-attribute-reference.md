---
title: Referens för SAP SuccessFactors-attribut
description: Lär dig vilka attribut från SuccessFactors som stöds av SuccessFactors-HR driven-etablering
services: active-directory
author: cmmdesai
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 08/05/2020
ms.author: chmutali
ms.reviewer: celested
ms.openlocfilehash: 0d1a25f67d3550e79b2339ee060d37acaea0c1c4
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99255550"
---
# <a name="sap-successfactors-attribute-reference"></a>Referens för SAP SuccessFactors-attribut

I den här artikeln hittar du information om:

- [SuccessFactors entiteter och attribut](#supported-successfactors-entities-and-attributes)
- [Mappning av standardattribut](#default-attribute-mapping)

## <a name="supported-successfactors-entities-and-attributes"></a>SuccessFactors entiteter och attribut som stöds

I tabellen nedan samlas listan över SuccessFactors-attribut som ingår som standard i följande två etablerings appar:

- [SuccessFactors för att Active Directory användar etablering](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
- [SuccessFactors till användar etablering i Azure AD](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)

I [integrerings referensen för SAP SuccessFactors](./sap-successfactors-integration-reference.md#retrieving-additional-attributes) finns mer information om hur du utökar schemat för ytterligare attribut. 

| \# | SuccessFactors-entitet                  | SuccessFactors-attribut     | Åtgärdstyp |
|----|----------------------------------------|------------------------------|----------------|
| 1  | PerPerson                              | personIdExternal             | Läs           |
| 2  | PerPerson                              | personId                     | Läs           |
| 3  | PerPerson                              | perPersonUuid                | Läs           |
| 4  | PerPersonal                            | displayName                  | Läs           |
| 5  | PerPersonal                            | firstName                    | Läs           |
| 6  | PerPersonal                            | kön                       | Läs           |
| 7  | PerPersonal                            | lastName                     | Läs           |
| 8  | PerPersonal                            | middleName                   | Läs           |
| 9  | PerPersonal                            | preferredName                | Läs           |
| 10 | User                                   | addressLine1                 | Läs           |
| 11 | User                                   | addressLine2                 | Läs           |
| 12 | User                                   | addressLIne3                 | Läs           |
| 13 | User                                   | businessPhone                | Läs           |
| 14 | User                                   | cellPhone                    | Läs           |
| 15 | User                                   | city                         | Läs           |
| 16 | User                                   | land                      | Läs           |
| 17 | User                                   | custom01                     | Läs           |
| 18 | User                                   | custom02                     | Läs           |
| 19 | User                                   | custom03                     | Läs           |
| 20 | User                                   | custom04                     | Läs           |
| 21 | User                                   | custom05                     | Läs           |
| 22 | User                                   | custom06                     | Läs           |
| 23 | User                                   | custom07                     | Läs           |
| 24 | User                                   | custom08                     | Läs           |
| 25 | User                                   | custom09                     | Läs           |
| 26 | User                                   | anpassad10                     | Läs           |
| 27 | User                                   | custom11                     | Läs           |
| 28 | User                                   | custom12                     | Läs           |
| 29 | User                                   | custom13                     | Läs           |
| 30 | User                                   | custom14                     | Läs           |
| 31 | User                                   | empId                        | Läs           |
| 32 | User                                   | homePhone                    | Läs           |
| 33 | User                                   | jobFamily                    | Läs           |
| 34 | User                                   | smek namn                     | Läs           |
| 35 | User                                   | state                        | Läs           |
| 36 | User                                   | Tidszon                     | Läs           |
| 37 | User                                   | användarnamn                     | Läs           |
| 38 | User                                   | Postummer                      | Läs           |
| 39 | PerPhone                               | areaCode                     | Läs           |
| 40 | PerPhone                               | countryCode                  | Läs           |
| 41 | PerPhone                               | utöka                    | Läs           |
| 42 | PerPhone                               | phoneNumber                  | Läs           |
| 43 | PerPhone                               | phoneType                    | Läs           |
| 44 | PerEmail                               | emailAddress                 | Läsa, skriva    |
| 45 | PerEmail                               | emailType                    | Läs           |
| 46 | EmpEmployment                          | firstDateWorked              | Läs           |
| 47 | EmpEmployment                          | lastDateWorked               | Läs           |
| 48 | EmpEmployment                          | userId                       | Läs           |
| 49 | EmpEmployment                          | isContingentWorker           | Läs           |
| 50 | EmpJob                                 | countryOfCompany             | Läs           |
| 51 | EmpJob                                 | emplStatus                   | Läs           |
| 52 | EmpJob                                 | endDate                      | Läs           |
| 53 | EmpJob                                 | /SD                    | Läs           |
| 54 | EmpJob                                 | jobTitle                     | Läs           |
| 55 | EmpJob                                 | position                     | Läs           |
| 65 | EmpJob                                 | customString13               | Läs           |
| 56 | EmpJob                                 | managerId                    | Läs           |
| 57 | EmpJob \. BusinessUnit                   | businessUnit                 | Läs           |
| 58 | EmpJob \. BusinessUnit                   | businessUnitId               | Läs           |
| 59 | EmpJob- \. företag                        | company                      | Läs           |
| 60 | EmpJob- \. företag                        | companyId                    | Läs           |
| 61 | EmpJob \. Company \. CountryOfRegistration | twoCharCountryCode           | Läs           |
| 62 | EmpJob \. CostCenter                     | costCenter                   | Läs           |
| 63 | EmpJob \. CostCenter                     | costCenterId                 | Läs           |
| 64 | EmpJob \. CostCenter                     | costCenterDescription        | Läs           |
| 65 | EmpJob- \. avdelning                     | avdelning                   | Läs           |
| 66 | EmpJob- \. avdelning                     | departmentId                 | Läs           |
| 67 | EmpJob- \. avdelning                       | vändning                     | Läs           |
| 68 | EmpJob- \. avdelning                       | divisionId                   | Läs           |
| 69 | EmpJob \. JobCode                        | jobCode                      | Läs           |
| 70 | EmpJob \. JobCode                        | jobCodeId                    | Läs           |
| 71 | EmpJob- \. plats                       | LocationName                 | Läs           |
| 72 | EmpJob- \. plats                       | officeLocationAddress        | Läs           |
| 73 | EmpJob- \. plats                       | officeLocationCity           | Läs           |
| 74 | EmpJob- \. plats                       | officeLocationCustomString4  | Läs           |
| 75 | EmpJob- \. plats                       | officeLocationZipCode        | Läs           |
| 76 | EmpJob \. PayGrade                       | payGrade                     | Läs           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | Läs           |
| 78 | EmpEmploymentTermination               | latestTerminationDate        | Läs           |

## <a name="default-attribute-mapping"></a>Mappning av standardattribut

Tabellen nedan innehåller standardattributets mappning mellan SuccessFactors-attribut som anges ovan och AD/Azure AD-attribut. I bladet för Azure AD Provisioning-appen kan du ändra standard mappningen för att inkludera attribut från listan ovan. 

| \# | SuccessFactors-entitet                  | SuccessFactors-attribut | AD/Azure AD-attributmappning som är standard   | Bearbetar kommentar                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | PerPerson                              | personIdExternal         | Anställnings                              | Används som matchande attribut                                                                   |
| 2  | PerPerson                              | perPersonUuid            | \[Inte mappad \- används som käll ankare\] | Under den inledande synkroniseringen länkar etablerings tjänsten personUuid till befintlig objectGuid\.  |
| 3  | PerPersonal                            | displayName              | displayName                             | NA                                                                                           |
| 4  | PerPersonal                            | firstName                | förnamn                               | NA                                                                                           |
| 5  | PerPersonal                            | lastName                 | sn                                      | NA                                                                                           |
| 6  | User                                   | addressLine1             | streetAddress                           | NA                                                                                           |
| 7  | User                                   | city                     | l                                       | NA                                                                                           |
| 8  | User                                   | land                  | co                                      | NA                                                                                           |
| 9  | User                                   | state                    | st                                      | NA                                                                                           |
| 10 | User                                   | användarnamn                 | samAccountName                          | NA                                                                                           |
| 11 | User                                   | Postummer                  | postalCode                              | NA                                                                                           |
| 12 | PerEmail                               | emailAddress             | e-post                                    | NA                                                                                           |
| 13 | EmpJob                                 | jobTitle                 | title                                   | NA                                                                                           |
| 14 | EmpJob                                 | managerId                | manager                                 | NA                                                                                           |
| 15 | EmpJob \. Company \. CountryOfRegistration | twoCharCountryCode       | c                                       | NA                                                                                           |
| 16 | EmpJob- \. avdelning                     | avdelning               | avdelning                              | NA                                                                                           |
| 17 | EmpJob- \. avdelning                       | vändning                 | company                                 | NA                                                                                           |
| 18 | EmpJob- \. plats                       | officeLocationAddress    | streetAddress                           | NA                                                                                           |
| 19 | EmpJob- \. plats                       | officeLocationZipCode    | postalCode                              | NA                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsCount   | accountEnabled                          | om activeEmploymentsCount = 0 inaktiverar du account\.                                           |
