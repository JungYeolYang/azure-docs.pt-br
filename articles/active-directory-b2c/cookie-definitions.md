---
title: Definições de cookie – Azure Active Directory B2C | Microsoft Docs
description: Fornece definições para os cookies usados no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7864320b71416d1b06661b8ae96c6113962250dd
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703970"
---
# <a name="cookies-definitions-for-azure-active-directory-b2c"></a>Definições de cookies para o Azure Active Directory B2C

A tabela a seguir lista os cookies usados no Azure Active Directory B2C.

| NOME | Domínio | Expiração | Finalidade |
| ----------- | ------ | -------------------------- | --------- |
| x-ms-cpim-admin | main.b2cadmin.ext.azure.com | Fim do [sessão do navegador](active-directory-b2c-token-session-sso.md) | Mantém os dados de associação do usuário entre locatários. Os locatários um usuário é um membro da e nível de associação (administrador ou usuário). |
| x-ms-cpim-slice | login.microsoftonline.com, b2clogin.com, domínio de marca | Fim do [sessão do navegador](active-directory-b2c-token-session-sso.md) | Usado para rotear solicitações para a instância de produção apropriados. |
| x-ms-cpim-trans | login.microsoftonline.com, b2clogin.com, domínio de marca | Fim do [sessão do navegador](active-directory-b2c-token-session-sso.md) | Usado para acompanhar as transações (número de solicitações de autenticação para o Azure AD B2C) e a transação atual. |
| x-ms-cpim-sso:{Id} | login.microsoftonline.com, b2clogin.com, domínio de marca | Fim do [sessão do navegador](active-directory-b2c-token-session-sso.md) | Usado para manter a sessão SSO. |
| x-ms-cpim-cache:{id}_n | login.microsoftonline.com, b2clogin.com, domínio de marca | Fim do [sessão de navegador](active-directory-b2c-token-session-sso.md), autenticação bem-sucedida | Usado para manter o estado de solicitação. |
| x-ms-cpim-csrf | login.microsoftonline.com, b2clogin.com, domínio de marca | Fim do [sessão do navegador](active-directory-b2c-token-session-sso.md) | Token de falsificação de solicitação entre sites usado para a proteção de CRSF. |
| x-ms-cpim-dc | login.microsoftonline.com, b2clogin.com, domínio de marca | Fim do [sessão do navegador](active-directory-b2c-token-session-sso.md) | Usado para roteamento de rede do Azure AD B2C. |
| x-ms-cpim-ctx | login.microsoftonline.com, b2clogin.com, domínio de marca | Fim do [sessão do navegador](active-directory-b2c-token-session-sso.md) | Contexto |
| x-ms-cpim-rp | login.microsoftonline.com, b2clogin.com, domínio de marca | Fim do [sessão do navegador](active-directory-b2c-token-session-sso.md) | Usado para armazenar dados de associação para o locatário do provedor de recursos. |
| x-ms-cpim-rc | login.microsoftonline.com, b2clogin.com, domínio de marca | Fim do [sessão do navegador](active-directory-b2c-token-session-sso.md) | Usado para armazenar o cookie de retransmissão. |

