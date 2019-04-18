---
title: 'CAF: Azienda di grandi dimensioni – più livelli di governance nelle grandi imprese'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Azienda di grandi dimensioni – più livelli di governance nelle grandi imprese
author: BrianBlanchard
ms.openlocfilehash: 1a90f8007077df0ecefa8ec5d8c0dd6bfca9ccc7
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59641213"
---
# <a name="multiple-layers-of-governance-in-large-enterprises"></a>Più livelli di governance nelle aziende di grandi dimensioni

Quando le aziende di grandi dimensioni richiedono più livelli di governance, devono essere presi in considerazione maggiori livelli di complessità all'interno dell'MVP per la governance e le successive evoluzioni della stessa.

Esempi comuni di tali complessità includono:

- Funzioni di governance distribuita.
- IT aziendale a supporto delle organizzazioni IT della Business Unit.
- IT aziendale a supporto delle organizzazioni IT distribuite geograficamente.

Questo articolo illustra alcuni modi per esplorare questo tipo di complessità.

## <a name="large-enterprise-governance-is-a-team-sport"></a>Governance per aziende di grandi dimensioni come sport di squadra

Le aziende affermate di grandi dimensioni dispongono spesso di team o di dipendenti che si concentrano sulle discipline indicate in questo percorso. Questo passaggio illustra uno degli approcci per fare della governance uno sport di squadra.

In molte aziende di grandi dimensioni, le cinque discipline di Governance Cloud può essere blockers all'adozione. Lo sviluppo di competenze cloud in identità, sicurezza, operazioni, distribuzioni e configurazione all'interno di un'azienda richiede tempo. L'implementazione in modo completo dei criteri di governance IT e di sicurezza IT può contribuire a rallentare l'innovazione per mesi o addirittura anni. L'equilibrio tra la necessità di innovazione delle imprese e la necessità della governance di proteggere le risorse esistenti è delicato.

Le funzionalità intrinseche del cloud possono rimuovere i blocchi per l'innovazione, ma anche aumentare i rischi. Questo percorso di governance, illustra come l'azienda di esempio abbia creato misure cautelative per attenuare il rischio. Piuttosto che affrontare tutte le discipline necessarie per proteggere l'ambiente, il team di governance del cloud adotta un approccio basato sul rischio per controllare ciò che potrebbe essere distribuito, mentre gli altri team compilano le necessarie maturità del cloud. Soprattutto, mentre ogni team raggiunge la maturità cloud, la governance applica in modo completo le proprie soluzioni. Man mano che ogni team matura e si aggiunge alla soluzione complessiva, il team di governance del cloud può iniziare la fase di stage gate, il che consente l'affermazione di ulteriore innovazione e adozione.

Questo modello illustra la crescita di una relazione tra il team di governance del cloud e team aziendali esistenti (sicurezza, governance IT, rete, identità e altri). Il percorso inizia con l'MVP per la governance e raggiunge uno stato finale completo tramite le evoluzioni della governance.

## <a name="requirements-to-supporting-such-a-team-sport"></a>Requisiti per il supporto di questo sport di squadra

Il primo requisito di un modello di governance a più livelli consiste nel comprendere la gerarchia di governance. Rispondere alle domande seguenti consentono di comprendere la gerarchia di governance generali:

- Dove si posiziona la contabilità del cloud (fatturazione per i servizi cloud) tra le unità aziendali?
- Dove si posizionano le responsabilità di governance nel panorama IT aziendale e tra ogni business unit?
- Quali tipi di ambiente vengono gestiti da ciascuna di queste unità IT?

## <a name="central-governance-of-a-distributed-governance-hierarchy"></a>Governance centrale di una gerarchia di governance distribuita

Strumenti come i gruppi di gestione consentono all'IT aziendale di creare una struttura gerarchica che corrisponde alla gerarchia di governance. Strumenti come Azure Blueprints possono applicare le risorse a diversi livelli di quella gerarchia. Azure Blueprints può disporre di versioni e varie di queste possono essere applicate a gruppi di gestione, abbonamenti o gruppi di risorse. Ognuno di questi concetti viene descritto più nei dettagli nell'MVP della governance.

L'aspetto importante di ogni strumento è la capacità di applicare più progetti a una gerarchia. Questo permette alla governance di diventare un processo a più livelli. L'esempio seguente mostra questa applicazione gerarchica di governance:

- IT aziendale: l'IT aziendale crea un insieme di standard e politiche che si applicano a tutta l'adozione del cloud. Ciò si concretizza in un progetto "Baseline". L'IT aziendale è quindi proprietaria della gerarchia del gruppo dirigente, assicurando che una versione della baseline sia applicata a tutte le sottoscrizioni della gerarchia.
- IT a livello di area o di Business unit: diversi team IT possono applicare un ulteriore livello di governance creando il loro progetto. Questi progetti creerebbero politiche e standard cumulativi. Una volta sviluppato, l'IT aziendale potrebbe applicare questi progetti ai nodi applicabili all'interno della gerarchia dei gruppi di gestione.
- Team per l'adozione del cloud: le decisioni dettagliate e l'implementazione di applicazioni o carichi di lavoro possono essere prese dai team di adozione del cloud, nel contesto dei requisiti di governance. A volte il team può anche richiedere ulteriori modelli di coerenza di Azure Resource per accelerare le attività di adozione.

I dettagli relativi all'attuazione della governance a ogni livello richiederanno un coordinamento tra i vari gruppi di lavoro. L'MVP della governance e le evoluzioni della governance delineate in questo percorso possono contribuire ad allineare tale coordinamento.
