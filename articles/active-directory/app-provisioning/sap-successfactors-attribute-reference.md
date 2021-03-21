---
title: Informazioni di riferimento sugli attributi di SAP SuccessFactors
description: Informazioni sugli attributi di SuccessFactors supportati dal provisioning basato su SuccessFactors-HR
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
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99255543"
---
# <a name="sap-successfactors-attribute-reference"></a>Informazioni di riferimento sugli attributi di SAP SuccessFactors

In questo articolo sono disponibili informazioni su:

- [Entità e attributi di SuccessFactors](#supported-successfactors-entities-and-attributes)
- [Mapping predefinito degli attributi](#default-attribute-mapping)

## <a name="supported-successfactors-entities-and-attributes"></a>Entità e attributi SuccessFactors supportati

La tabella seguente consente di acquisire l'elenco degli attributi SuccessFactors inclusi per impostazione predefinita nelle due app di provisioning seguenti:

- [SuccessFactors per Active Directory il provisioning utenti](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
- [SuccessFactors per Azure AD il provisioning utenti](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)

Per estendere lo schema per attributi aggiuntivi, vedere informazioni di [riferimento sull'integrazione di SAP SuccessFactors](./sap-successfactors-integration-reference.md#retrieving-additional-attributes) . 

| \# | Entità SuccessFactors                  | Attributo di SuccessFactors     | Tipo di operazione |
|----|----------------------------------------|------------------------------|----------------|
| 1  | PerPerson                              | personIdExternal             | Lettura           |
| 2  | PerPerson                              | personId                     | Lettura           |
| 3  | PerPerson                              | perPersonUuid                | Lettura           |
| 4  | PerPersonal                            | displayName                  | Lettura           |
| 5  | PerPersonal                            | firstName                    | Lettura           |
| 6  | PerPersonal                            | gender                       | Lettura           |
| 7  | PerPersonal                            | lastName                     | Lettura           |
| 8  | PerPersonal                            | middleName                   | Lettura           |
| 9  | PerPersonal                            | preferenzaname                | Lettura           |
| 10 | User                                   | addressLine1                 | Lettura           |
| 11 | User                                   | addressLine2                 | Lettura           |
| 12 | Utente                                   | addressLIne3                 | Lettura           |
| 13 | User                                   | businessPhone                | Lettura           |
| 14 | User                                   | cellPhone                    | Lettura           |
| 15 | Utente                                   | city                         | Lettura           |
| 16 | Utente                                   | country                      | Lettura           |
| 17 | User                                   | custom01                     | Lettura           |
| 18 | User                                   | custom02                     | Lettura           |
| 19 | User                                   | custom03                     | Lettura           |
| 20 | User                                   | custom04                     | Lettura           |
| 21 | User                                   | custom05                     | Lettura           |
| 22 | User                                   | custom06                     | Lettura           |
| 23 | User                                   | custom07                     | Lettura           |
| 24 | User                                   | custom08                     | Lettura           |
| 25 | User                                   | custom09                     | Lettura           |
| 26 | User                                   | personalizzata10                     | Lettura           |
| 27 | User                                   | custom11                     | Lettura           |
| 28 | User                                   | custom12                     | Lettura           |
| 29 | User                                   | custom13                     | Lettura           |
| 30 | User                                   | custom14                     | Lettura           |
| 31 | User                                   | empId                        | Lettura           |
| 32 | User                                   | homePhone                    | Lettura           |
| 33 | User                                   | jobFamily                    | Lettura           |
| 34 | User                                   | nickname                     | Lettura           |
| 35 | Utente                                   | state                        | Lettura           |
| 36 | User                                   | timeZone                     | Lettura           |
| 37 | User                                   | username                     | Lettura           |
| 38 | User                                   | zipCode                      | Lettura           |
| 39 | PerPhone                               | areaCode                     | Lettura           |
| 40 | PerPhone                               | countryCode                  | Lettura           |
| 41 | PerPhone                               | estensione                    | Lettura           |
| 42 | PerPhone                               | phoneNumber                  | Lettura           |
| 43 | PerPhone                               | phoneType                    | Lettura           |
| 44 | PerEmail                               | emailAddress                 | Lettura, Scrittura    |
| 45 | PerEmail                               | emailType                    | Lettura           |
| 46 | EmpEmployment                          | firstDateWorked              | Lettura           |
| 47 | EmpEmployment                          | lastDateWorked               | Lettura           |
| 48 | EmpEmployment                          | userId                       | Lettura           |
| 49 | EmpEmployment                          | isContingentWorker           | Lettura           |
| 50 | EmpJob                                 | countryOfCompany             | Lettura           |
| 51 | EmpJob                                 | emplStatus                   | Lettura           |
| 52 | EmpJob                                 | endDate                      | Lettura           |
| 53 | EmpJob                                 | startDate                    | Lettura           |
| 54 | EmpJob                                 | jobTitle                     | Lettura           |
| 55 | EmpJob                                 | position                     | Lettura           |
| 65 | EmpJob                                 | customString13               | Lettura           |
| 56 | EmpJob                                 | managerId                    | Lettura           |
| 57 | \.BusinessUnit EmpJob                   | businessUnit                 | Lettura           |
| 58 | \.BusinessUnit EmpJob                   | businessUnitId               | Lettura           |
| 59 | \.Società EmpJob                        | company                      | Lettura           |
| 60 | \.Società EmpJob                        | companyId                    | Lettura           |
| 61 | \.CountryOfRegistration società \. EmpJob | twoCharCountryCode           | Lettura           |
| 62 | \.CostCenter EmpJob                     | costCenter                   | Lettura           |
| 63 | \.CostCenter EmpJob                     | costCenterId                 | Lettura           |
| 64 | \.CostCenter EmpJob                     | costCenterDescription        | Lettura           |
| 65 | \.Reparto EmpJob                     | department                   | Lettura           |
| 66 | \.Reparto EmpJob                     | departmentId                 | Lettura           |
| 67 | \.Divisione EmpJob                       | divisione                     | Lettura           |
| 68 | \.Divisione EmpJob                       | divisionId                   | Lettura           |
| 69 | \.JobCode EmpJob                        | jobCode                      | Lettura           |
| 70 | \.JobCode EmpJob                        | jobCodeId                    | Lettura           |
| 71 | \.Percorso EmpJob                       | LocationName                 | Lettura           |
| 72 | \.Percorso EmpJob                       | officeLocationAddress        | Lettura           |
| 73 | \.Percorso EmpJob                       | officeLocationCity           | Lettura           |
| 74 | \.Percorso EmpJob                       | officeLocationCustomString4  | Lettura           |
| 75 | \.Percorso EmpJob                       | officeLocationZipCode        | Lettura           |
| 76 | \.PayGrade EmpJob                       | payGrade                     | Lettura           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | Lettura           |
| 78 | EmpEmploymentTermination               | latestTerminationDate        | Lettura           |

## <a name="default-attribute-mapping"></a>Mapping predefinito degli attributi

La tabella seguente fornisce il mapping predefinito degli attributi tra gli attributi SuccessFactors elencati sopra e gli attributi AD/Azure AD. Nel pannello "mapping" dell'app di provisioning di Azure AD è possibile modificare questo mapping predefinito per includere gli attributi dall'elenco precedente. 

| \# | Entità SuccessFactors                  | Attributo di SuccessFactors | Mapping di attributi AD/Azure AD predefinito   | Elaborazione del contrassegno                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | PerPerson                              | personIdExternal         | employeeId                              | Utilizzato come attributo corrispondente                                                                   |
| 2  | PerPerson                              | perPersonUuid            | \[Non mappato \- usato come ancoraggio di origine\] | Durante la sincronizzazione iniziale, il servizio di provisioning collega personUuid a objectGuid\. esistenti  |
| 3  | PerPersonal                            | displayName              | displayName                             | N/D                                                                                           |
| 4  | PerPersonal                            | firstName                | givenName                               | N/D                                                                                           |
| 5  | PerPersonal                            | lastName                 | sn                                      | N/D                                                                                           |
| 6  | Utente                                   | addressLine1             | streetAddress                           | N/D                                                                                           |
| 7  | Utente                                   | city                     | l                                       | N/D                                                                                           |
| 8  | Utente                                   | country                  | co                                      | N/D                                                                                           |
| 9  | Utente                                   | state                    | st                                      | N/D                                                                                           |
| 10 | User                                   | username                 | samAccountName                          | N/D                                                                                           |
| 11 | User                                   | zipCode                  | postalCode                              | N/D                                                                                           |
| 12 | PerEmail                               | emailAddress             | mail                                    | N/D                                                                                           |
| 13 | EmpJob                                 | jobTitle                 | title                                   | N/D                                                                                           |
| 14 | EmpJob                                 | managerId                | manager                                 | N/D                                                                                           |
| 15 | \.CountryOfRegistration società \. EmpJob | twoCharCountryCode       | c                                       | N/D                                                                                           |
| 16 | \.Reparto EmpJob                     | department               | department                              | N/D                                                                                           |
| 17 | \.Divisione EmpJob                       | divisione                 | company                                 | N/D                                                                                           |
| 18 | \.Percorso EmpJob                       | officeLocationAddress    | streetAddress                           | N/D                                                                                           |
| 19 | \.Percorso EmpJob                       | officeLocationZipCode    | postalCode                              | N/D                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsCount   | accountEnabled                          | Se activeEmploymentsCount = 0, disabilitare il account\.                                           |
