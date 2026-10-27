# Qualidade de software e evidências

Consolidação em 27/10/2026. As práticas abaixo têm exemplos no código; nenhuma substitui revisão e ensaio da versão final.

## Código e manutenção

- Responsabilidades separadas por controller, DTO, serviço, entidade, repositório e adaptador de Storage. [Arquitetura](../architecture/ARQUITETURA.md).
- Invariantes de status e prioridade na entidade; autorização no serviço e visibilidade aplicada na consulta. Evita depender somente de bloqueios de tela.
- DTOs sem senha/credenciais, erros tratados e validação de entrada. Regras não são espalhadas por valores livres na interface.
- Dependências por construtor; `Clock` injetado em `TokenService` permite testes determinísticos de emissão/expiração.
- Novas classes de backend foram registradas em commits individuais por orientação acadêmica. Esta entrega documental não adiciona classes Java nem altera a aplicação.

## TDD: exemplo auditável

Na BE-10, seguir o [histórico do PR #79](https://github.com/Zelar-App/zelar.app-backend/pull/79/commits):

1. [`cdcbb25`](https://github.com/Zelar-App/zelar.app-backend/commit/cdcbb25): introduz testes de integração antes do endpoint, etapa RED. A rodada registrada teve 21 falhas pelo endpoint ausente e um caso sem token já aprovado.
2. Testes de domínio/serviço e implementações intermediárias definem transições e autorização.
3. [`fc257d4`](https://github.com/Zelar-App/zelar.app-backend/commit/fc257d4): endpoint/tratamento de erros completam o fluxo GREEN.
4. Suíte completa da branch na ocasião: 169 testes; é um número histórico daquela base, não o total atual.

Para estudar: ler primeiro `OcorrenciaStatusIntegrationTest`, depois `StatusOcorrencia`, `Ocorrencia.atualizarStatus`, serviço e controller. O teste descreve o comportamento esperado e a implementação satisfaz a regra. Não executar commits RED em banco compartilhado; eles existem para demonstrar a evolução.

Exemplo de refatoração: [`2014f39`](https://github.com/Zelar-App/zelar.app-backend/commit/2014f39), nome `emitidoEm` no cálculo temporal do JWT. Capturar `clock.instant()` uma vez mantém emissão e expiração baseadas no mesmo instante; a mudança de nome melhora entendimento sem modificar o contrato. Confrontar código antes/depois e testes de token ao apresentar.

## Verificações reproduzíveis

Backend, raiz do repositório:

```powershell
.\mvnw.cmd clean verify
```

Suíte padrão usa H2 em modo PostgreSQL e Flyway. Para validar migrations/fotografias em PostgreSQL, usar **banco vazio exclusivo de testes**. Configurar `DB09_TEST_URL`, `DB09_TEST_USERNAME`, `DB09_TEST_PASSWORD` e `BE13_TEST_URL`, `BE13_TEST_USERNAME`, `BE13_TEST_PASSWORD` para esse ambiente, com permissão para criar/remover schemas de teste. Não apontar para o banco da aplicação.

```powershell
.\mvnw.cmd '-Dtest=AnexoMigrationIntegrationTest,FotografiaIntegrationTest' test
```

Frontend, raiz:

```powershell
npm ci
npm run lint
npm test
npm run build
```

## CI existente

| Repositório | Workflow | O que executa |
| --- | --- | --- |
| [Backend](https://github.com/Zelar-App/zelar.app-backend/blob/e0d47f21e0439437c031d7610de12dd4dbbb1cd2/.github/workflows/maven.yml) | PRs/pushes para develop, stage e main | Java 21, clean verify, build Docker; job PostgreSQL 17 para migration e ciclo HTTP das fotos |
| [Frontend](https://github.com/Zelar-App/zelar.app-frontend/blob/428ebfbc35b7960a7659adfbeeec2a34ad231e7e/.github/workflows/frontend-ci.yml) | PRs, pushes para main/develop/stage e disparo manual | Node 24, npm ci, lint, testes e build |

Consultar os runs em [Actions backend](https://github.com/Zelar-App/zelar.app-backend/actions) e [Actions frontend](https://github.com/Zelar-App/zelar.app-frontend/actions). O resultado verde deve corresponder ao commit de entrega. Existência do YAML e sucesso local não provam sucesso de um run remoto. Não há alegação de deploy/CD automático.

## Resultados históricos disponíveis

| Escopo | Base / ambiente | Resultado registrado | Limite |
| --- | --- | --- | --- |
| BE-13 completa | HEAD `abf67f9`, depois integrado no PR #86; Java local 24, alvo 21 | 398 testes sem falhas/erros/ignorados | Não foi nova execução nesta consolidação documental |
| DB-09 + ciclo HTTP de fotos | Mesma entrega; PostgreSQL 17.11 isolado, V1–V5 e Hibernate validate | 72 casos: 54 de migration e 18 do ciclo HTTP | Supabase simulado por HTTP; não comprova bucket real |
| Frontend consolidado | Entrega do PR #47, Node 24 | Lint, build e 145 testes aprovados | Não comprova fotos reais ou GPS/câmera físicos |
| Adaptadores HTTP com API/banco | Ensaio de 26/10, Java/PostgreSQL 17 temporários | Cadastro/login, 105 registros paginados, visibilidade/filtros, PATCHs, indicadores, falha parcial e logout | Não foi ensaio visual completo; não incluía fotos |

Os logs detalhados dos ensaios originais foram produzidos no ambiente local de desenvolvimento. Esta consolidação registra os resultados históricos e links dos PRs, sem publicar logs brutos potencialmente sensíveis nem alegar nova execução. Reproduzir pelos comandos e registrar o novo run/resultado no [roteiro](ROTEIRO-INTEGRACAO.md).

## Revisão e aceite

Revisar contrato versus código, verificar erros e permissões, manter commits focados e PR com problema/resultado/validação/limites. Para documentos, verificar links, consistência de exemplos e diagramas. A DOC-01 exige revisão de outro integrante: o autor desta consolidação não registra essa revisão como realizada.

### Conferência desta consolidação — 27/10/2026

O OpenAPI passou em validador da especificação 3.0.3. Foram comparadas as 11 operações com os controllers e os campos de 12 schemas com os DTOs Java; os seis exemplos JSON passaram na conferência dos schemas correspondentes. Os 43 links locais então existentes foram verificados sem destinos ou âncoras ausentes. Não houve nova execução das suítes da aplicação, pois esta entrega altera somente documentação. Isso não substitui revisão humana nem os testes integrados ainda pendentes.
