# Rastreabilidade de requisitos e evidências

Base: [versões do índice](../README.md), 27/10/2026. "Implementado" significa código presente na base; não significa validação de todos os ambientes. A matriz referencia entregas existentes, sem criar issues duplicadas.

## Funcionalidades

| Requisito | Entrega / issue ou PR | Testes verificáveis no backend | Estado e limites |
| --- | --- | --- | --- |
| RF01 cadastro | `usuario/controller`, `usuario/service` | `UsuarioControllerTest`, `UsuarioServiceTest` | Implementado; nome/email/senha/telefone e email único |
| RF02 login/perfis | [PR #66](https://github.com/Zelar-App/zelar.app-backend/pull/66), [PR #81](https://github.com/Zelar-App/zelar.app-backend/pull/81) | `AutenticacaoIntegrationTest`, `TokenServiceTest`, `AdminProvisioningIntegrationTest` | Implementado; admin por provisionamento, sem refresh/revogação |
| RF03 criação | [PR #68](https://github.com/Zelar-App/zelar.app-backend/pull/68) | `OcorrenciaCriacaoIntegrationTest`, `CriarOcorrenciaRequestTest` | Implementado sem obrigar foto |
| RF03.1 categorias | [PR #84 / DB-07](https://github.com/Zelar-App/zelar.app-backend/pull/84), migration V2 | `OcorrenciaCriacaoIntegrationTest`, testes de constraints do schema | Sete códigos curtos; não usar códigos extensos de PDF antigo |
| RF03.2 localização | [PR #68](https://github.com/Zelar-App/zelar.app-backend/pull/68) | `CriarOcorrenciaRequestTest`, `OcorrenciaCriacaoIntegrationTest` | Coordenadas obrigatórias; endereço opcional; GPS físico é ensaio distinto |
| RF03 fotografia / RNF-S04 | [BE-13 #69](https://github.com/Zelar-App/zelar.app-backend/issues/69), [DB-09 #72](https://github.com/Zelar-App/zelar.app-backend/issues/72), [PR #85](https://github.com/Zelar-App/zelar.app-backend/pull/85), [PR #86](https://github.com/Zelar-App/zelar.app-backend/pull/86) | `ValidadorFotografiaTest`, `FotografiaAcessoServiceTest`, `FotografiaIntegrationTest`, `SupabaseFotografiaStorageTest`, `AnexoMigrationIntegrationTest` | Backend integrado; bucket real e frontend pendentes |
| RF04 listagem/detalhe | [PR #77](https://github.com/Zelar-App/zelar.app-backend/pull/77), [PR #78](https://github.com/Zelar-App/zelar.app-backend/pull/78) | `OcorrenciaListagemIntegrationTest`, `OcorrenciaConsultaIntegrationTest` | Implementado com escopo por autoria/perfil |
| RF05 status / BE-10 | [Issue #8](https://github.com/Zelar-App/zelar.app-backend/issues/8), [PR #79](https://github.com/Zelar-App/zelar.app-backend/pull/79) | `OcorrenciaTest`, `OcorrenciaServiceTest`, `OcorrenciaStatusIntegrationTest` | Implementado; sem reabertura; bloqueio concorrente |
| RF06 prioridade / BE-14 | [Issue #70](https://github.com/Zelar-App/zelar.app-backend/issues/70), [PR #82](https://github.com/Zelar-App/zelar.app-backend/pull/82) | `OcorrenciaPrioridadeIntegrationTest`, testes de domínio/serviço | Implementado; não permite limpar prioridade |
| RF06 indicadores / BE-15 | [Issue #71](https://github.com/Zelar-App/zelar.app-backend/issues/71), [PR #83](https://github.com/Zelar-App/zelar.app-backend/pull/83) | `OcorrenciaIndicadoresIntegrationTest`, `OcorrenciaServiceTest` | Implementado; contagens globais |
| Fluxos de interface | [Frontend PR #47](https://github.com/Zelar-App/zelar.app-frontend/pull/47) | `auth.http.test.ts`, `backend.contract.test.ts` e testes de páginas | HTTP alinhado para fluxos sem foto; 145 testes no ensaio registrado |

Localização dos testes: [backend na versão conferida](https://github.com/Zelar-App/zelar.app-backend/tree/e0d47f21e0439437c031d7610de12dd4dbbb1cd2/src/test/java/app/zelar), [frontend](https://github.com/Zelar-App/zelar.app-frontend/tree/428ebfbc35b7960a7659adfbeeec2a34ad231e7e/src).

## Requisitos não funcionais e trabalho acadêmico

| Requisito / critério | Evidência | Limite ou complemento |
| --- | --- | --- |
| RNF-S01 autenticação/autorização | Testes de login, conta removida, perfil atualizado e acesso alheio nas suítes HTTP | Sem auditoria de segurança completa |
| RNF-S02 hash | `UsuarioService`, `PasswordEncoder`, testes de usuário/login | Não afirmar criptografia reversível |
| RNF-S03 validação/erros | DTOs com Bean Validation, `GlobalExceptionHandlerTest`, testes negativos de endpoints | Resposta de proxy/framework pode ser distinta |
| RNF-S05 minimização | DTOs sem senha, ocorrências sem dados cadastrais, fotos privadas/regravadas | Retenção/exclusão ainda sem política operacional definida |
| RNF-D01 desempenho | Paginação, índice por autor e agregação de indicadores | Sem meta/carga homologada; não declarar SLA |
| RNF-D02 arquitetura | [Arquitetura](../architecture/ARQUITETURA.md) e DTOs/adaptadores | Manter atualização conjunta dos contratos |
| RNF-U01/U02 usabilidade/responsividade | Testes de páginas e [roteiro de integração](../quality/ROTEIRO-INTEGRACAO.md) | Registrar ensaio visual e físico da versão final |
| GitFlow e commits | Branches, PRs integrados e commits semânticos nos links acima | Push manual; revisão por outro integrante |
| TDD e refatoração | [Evidências de qualidade](../quality/QUALIDADE.md) | Teste passando isolado não prova ordem RED/GREEN |
| CI | Workflows de backend e frontend | Consultar resultado do run vinculado ao SHA, não só existência do arquivo |
| Problema real e ODS | [Escopo](ESCOPO-MVP.md) | Confirmar ODS nos slides/Word externos; sem impacto medido |
| Aplicação funcionando / apresentação | [Entrega](../delivery/ENTREGA.md) | Ensaio e materiais externos sob responsabilidade da equipe |

## Pendências acompanhadas

DOC-01 #3: revisão por outro integrante e publicação desta consolidação. INT-01 #4: preencher resultados do roteiro na versão final, incluindo Storage real e cliente. REL-01 #5: fixar versão de entrega e validar apresentação/material externo. Não encerrar estas issues somente por esta matriz existir.
