# Zelar.app — Documentação

Plataforma para cidadãos registrarem problemas urbanos e administradores acompanharem seu atendimento. Este repositório reúne os contratos, regras e instruções de manutenção do MVP.

## Versão documentada

Conferência em **27/10/2026**, com referências fixadas para reprodução:

| Repositório | Base conferida | Situação |
| --- | --- | --- |
| [Backend](https://github.com/Zelar-App/zelar.app-backend) | [`e0d47f2`](https://github.com/Zelar-App/zelar.app-backend/commit/e0d47f21e0439437c031d7610de12dd4dbbb1cd2) | `develop`, incluindo DB-09 e BE-13, PRs #85 e #86 integrados |
| [Frontend](https://github.com/Zelar-App/zelar.app-frontend) | [`428ebfb`](https://github.com/Zelar-App/zelar.app-frontend/commit/428ebfbc35b7960a7659adfbeeec2a34ad231e7e) | `develop`, PR #47; fotos ainda bloqueadas no adaptador HTTP |
| Documentação | Base `660043b`, acrescida desta consolidação | Depende de push e revisão |

**Backend implementado não significa integração completa das fotografias.** A validação do bucket real e a adaptação do frontend continuam pendentes nas evidências disponíveis. O mock não comprova persistência na API.

## Índice

| Documento | Conteúdo |
| --- | --- |
| [Escopo e requisitos](requirements/ESCOPO-MVP.md) | Problema, público, ODS e limites |
| [Rastreabilidade](requirements/RASTREABILIDADE.md) | Requisitos, PRs, testes e pendências |
| [Contrato HTTP](api/README.md) | Endpoints, DTOs, autorização e exemplos |
| [OpenAPI 3.0.3](api/openapi.json) | Contrato importável |
| [Regras de negócio](business-rules/REGRAS-MVP.md) | Visibilidade, status, prioridade e fotos |
| [Arquitetura e fluxos](architecture/ARQUITETURA.md) | Componentes, camadas e sequências |
| [Modelo de dados](database/DER.md) | Dicionário físico e migrations V1–V5 |
| [Classes de domínio](database/Diagrama_de_Classe.md) | Modelo conceitual atual |
| [Decisões técnicas](decisions/DECISOES-MVP.md) | Escolhas e assuntos em aberto |
| [Execução e configuração](operations/EXECUCAO.md) | Banco, JWT, frontend e Storage |
| [Qualidade de software](quality/QUALIDADE.md) | TDD, revisão, CI e evidências |
| [Roteiro de integração](quality/ROTEIRO-INTEGRACAO.md) | Verificações e registro de resultados |
| [Fluxos da interface](ux-ui/FLUXOS-MVP.md) | Telas, permissões e erros |
| [Entrega e demonstração](delivery/ENTREGA.md) | Versões, roteiro e pendências |

## Como manter

Código e migrations determinam o comportamento implementado. Ao alterar um endpoint, atualizar OpenAPI, exemplos, regras e rastreabilidade no mesmo ciclo. Evidências históricas devem indicar commit, ambiente e uso de simulação.

Fluxo: `feature/*`, `fix/*` ou `docs/*` → PR para `develop` → revisão → integração. `stage` representa homologação nos repositórios que a utilizam; `main`, versão estável. Pushes desta consolidação são manuais pelo responsável. Não publicar segredos ou dados reais de demonstração.

As issues [DOC-01 #3](https://github.com/Zelar-App/zelar.app-docs/issues/3), [INT-01 #4](https://github.com/Zelar-App/zelar.app-docs/issues/4) e [REL-01 #5](https://github.com/Zelar-App/zelar.app-docs/issues/5) acompanham consolidação, integração e entrega. Criar os arquivos não substitui revisão por outro integrante nem execução dos roteiros.

## PDFs históricos

Os PDFs de requisitos, classes, sequência, casos de uso e entidades preservam versões anteriores. **Não são o contrato vigente.** Alguns usam categorias extensas, visibilidade indefinida, múltiplos anexos ou envio de foto junto ao cadastro. Os documentos editáveis do índice substituem essas informações para a versão acima; os PDFs são mantidos para rastreabilidade. Novas exportações devem usar as fontes atuais e identificar a versão.

Licença: [LICENSE](LICENSE).
