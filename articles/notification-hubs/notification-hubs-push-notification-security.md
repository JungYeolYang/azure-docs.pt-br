---
title: Segurança dos Hubs de Notificação
description: Este tópico explica a segurança para hubs de notificação do Azure.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 6506177c-e25c-4af7-8508-a3ddca9dc07c
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 22494984ca45cde7255fb5e1a30548c859bfad68
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65826504"
---
# <a name="security-model-of-azure-notification-hubs"></a>Modelo de segurança dos Hubs de Notificação do Azure

## <a name="overview"></a>Visão geral

Este tópico descreve o modelo de segurança dos Hubs de notificação do Azure. Como os Hubs de notificação são uma entidade do barramento de serviço, eles implementam o mesmo modelo de segurança que o barramento de serviço. Para mais informações, consulte os tópicos de [Autenticação do barramento de serviço](https://msdn.microsoft.com/library/azure/dn155925.aspx) .

## <a name="shared-access-signature-security-sas"></a>Segurança de assinatura de acesso compartilhado (SAS)

Os hubs de notificação implementam um esquema de segurança de nível de entidade chamado SAS (assinatura de acesso compartilhado). Esse esquema permite que as entidades de mensagens declarem até 12 regras de autorização nas suas descrições que concedem direitos naquela entidade.

Cada regra contém um nome, um valor de chave (segredo compartilhado) e um conjunto de direitos, conforme explicado na seção “Declarações de segurança”. Ao criar um Hub de notificação, duas regras são automaticamente criadas: uma com direitos de escuta (que usa o aplicativo cliente) e outra com todos os direitos (que usa o back-end).

Ao realizar o gerenciamento de registro dos aplicativos clientes, se as informações enviadas por meio de notificações não forem confidenciais (por exemplo, atualizações de clima), uma maneira comum de acessar um Hub de notificação é fornecer o valor da chave da regra de acesso de somente escuta para o aplicativo cliente e fornecer o valor de chave da regra de acesso completo para o back-end do aplicativo.

Não é recomendável que você insira o valor da chave em aplicativos cliente da Windows Store. Uma maneira de evitar a inserção do valor chave é recuperar o aplicativo cliente do back-end do aplicativo na inicialização.

É importante entender que a chave de acesso de escuta permite que um aplicativo cliente seja registrado para qualquer marca. Se seu aplicativo tiver que restringir registros de marcas específicas para clientes específicos (por exemplo, quando as marcas representam IDs de usuário), o back-end do aplicativo deverá executar os registros. Para mais informações, consulte a seção Gerenciamento de registro. Observe que, dessa maneira, o aplicativo cliente não terá acesso direto aos Hubs de notificação.

## <a name="security-claims"></a>Declarações de segurança

Semelhantes a outras entidades, as operações de Hub de notificação são permitidas para três declarações de segurança: Ouvir, Enviar e Gerenciar.

| Declaração   | Descrição                                          | Operações permitidas |
| ------- | ---------------------------------------------------- | ------------------ |
| Escutar  | Criar/atualizar, ler e excluir registros simples | Criar/Atualizar o registro<br><br>Ler registro<br><br>Ler todos os registros para um identificador<br><br>Excluir registro |
| Enviar    | Enviar mensagens ao hub de notificação                | Enviar mensagem |
| Gerenciar  | CRUDs nos Hubs de notificação (incluindo atualização de credenciais PNS e chaves de segurança) e ler registros baseados em marcas |Criar/Atualizar/Ler/Excluir hubs de notificação<br><br>Ler registros por marca |

Os hubs de notificação aceitam declarações concedidas pelos tokens de controle de acesso do Microsoft Azure e por tokens de assinatura gerados com chaves compartilhadas configuradas diretamente no hub de notificação.

Não é possível enviar uma notificação para mais de um namespace. Namespaces são um contêiner lógico para os hubs de notificação e não estão envolvidos com o envio de notificações.
As políticas de acesso de nível de namespace (credenciais) podem ser usadas para operações de nível de namespace, por exemplo: listando os hubs de notificação, criação ou exclusão de hubs de notificação, etc. Somente as políticas de acesso de nível de hub seriam permitem que você envie notificações.
