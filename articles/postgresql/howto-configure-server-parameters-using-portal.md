---
title: Configurar os parâmetros do servidor no Banco de Dados do Azure para PostgreSQL por meio do Portal do Azure
description: Este artigo descreve como configurar os parâmetros de servidor disponíveis no Banco de Dados do Azure para PostgreSQL usando o portal do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: ed19083c6a4245a1b4bf7af166ae965d956c9e37
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067136"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---single-server-via-the-azure-portal"></a>Configurar parâmetros do servidor no banco de dados do Azure para PostgreSQL - único servidor por meio do portal do Azure 
Você pode listar, exibir e atualizar os parâmetros de configuração de um Banco de Dados do Azure para servidor PostgreSQL via portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de instruções, você precisa:
- [Servidor do Banco de Dados do Azure para PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>Exibir e editar parâmetros
1. Abra o [Portal do Azure](https://portal.azure.com).

2. Selecione seu servidor de Banco de Dados do Azure para PostgreSQL.

3. Sob a seção **configurações**, selecione **parâmetros de servidor**. A página mostra uma lista de parâmetros, valores e descrições.
![Página de visão geral de parâmetros](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. Selecione o botão **suspenso** para ver os possíveis valores para parâmetros de tipo enumerado como client_min_messages.
![Enumerar a lista suspensa](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. Selecione ou passe o mouse sobre o botão **i** (informações) para ver o intervalo de valores possíveis para parâmetros numéricos como cpu_index_tuple_cost.
![Botão informação](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. Se necessário, use a **caixa de pesquisa** para restringir rapidamente a um parâmetro específico. A busca está no nome e na descrição dos parâmetros.
![Pesquisar resultados](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. Altere os valores de parâmetro que você deseja ajustar. Todas as alterações feitas nessa sessão são realçadas em roxo. Depois de alterar os valores, você pode selecionar **Salvar**. Ou então, você pode **Descartar** suas alterações.
![Clique em salvar ou descartar mudanças](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. Se você tiver salvo os novos valores para os parâmetros, você sempre pode reverter tudo o que fazer com os valores padrão selecionando **Redefinir tudo para o padrão**.
![Redefinir tudo para padrão](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png)

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre:
- [Visão geral dos parâmetros do servidor no Banco de Dados do Azure para PostgreSQL](concepts-servers.md)
- [Configurando parâmetros usando a CLI do Azure](howto-configure-server-parameters-using-cli.md)
