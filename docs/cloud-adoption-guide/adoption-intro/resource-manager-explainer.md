---
title: 'Spiegazione: Informazioni su Azure Resource Manager'
description: Illustra il funzionamento interno di Azure Resource Manager
author: petertay
ms.openlocfilehash: 60f09901bdc4b292abd73335b78c7d56a76f27a6
ms.sourcegitcommit: 2e8b06e9c07875d65b91d5431bfd4bc465a7a242
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
ms.locfileid: "29062029"
---
# <a name="explainer-what-is-azure-resource-manager"></a>Spiegazione: Informazioni su Azure Resource Manager

In [Spiegazione: Funzionamento di Azure](azure-explainer.md) è stata illustrata l'architettura interna di Azure. Questa architettura include un sistema front-end che ospita le applicazioni distribuite che gestiscono i servizi interni di Azure.

Il sistema front-end di Azure include un servizio denominato Azure Resource Manager, che è responsabile del ciclo di vita delle risorse ospitate in Azure dal momento della creazione all'eliminazione. Esistono molti modi per interagire con Azure Resource Manager (tramite Powershell, l'interfaccia della riga di comando di Azure, gli SDK), ma ognuno di questi strumenti è un semplice wrapper su un'API RESTful ospitato da Azure Resource Manager.

L'API RESTful fornita da Azure Resource Manager è un'interfaccia coerente su un set di **provider di risorse**. I provider di risorse sono semplicemente servizi di Azure che consentono di creare, leggere, aggiornare ed eliminare le risorse di Azure. In realtà, l'API RESTful include metodi per ognuna di queste funzioni. 

L'API RESTful richiede un token di accesso per l'utente, un **ID sottoscrizione**, e un nuovo elemento: un **ID gruppo di risorse**. I gruppi di risorse verranno illustrati in [Spiegazione: Informazioni sul gruppo di risorse di Azure](resource-group-explainer.md). Azure Resource Manager richiede anche l'**ID tenant**, che viene codificato come parte del token di accesso. 

Quando si riceve una chiamata API valida per creare una risorsa, Azure Resource Manager trova capacità nell'area specificata e copia i file necessari in una posizione di gestione temporanea. La richiesta viene quindi inviata al controller di infrastruttura nel rack e il controller alloca le risorse. Il controller di infrastruttura risponde alla richiesta con una notifica di esito positivo o di errore, nonché con un **ID risorsa** per la risorsa appena creata. Questi quattro ID vengono archiviati internamente in Azure e insieme costituiscono l'identificatore univoco di una risorsa distribuita.

## <a name="next-steps"></a>Passaggi successivi

* Dopo aver compreso il funzionamento interno di Azure Resource Manager, consultare le informazioni [sui gruppi di risorse](resource-group-explainer.md) per creare il primo gruppo di risorse.
