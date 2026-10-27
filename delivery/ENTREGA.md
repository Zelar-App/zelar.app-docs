# Entrega e demonstração do MVP

Atualizado em 27/10/2026. O prazo ajustado informado pela equipe foi **26/10**, alinhado com o professor. Este documento não presume entrega ou aprovação dos materiais externos.

## Versão e estado

| Item | Referência | Estado verificável |
| --- | --- | --- |
| Backend | `e0d47f21e0439437c031d7610de12dd4dbbb1cd2`, develop | PRs #85/#86 integrados, incluindo fotografias |
| Frontend | `428ebfbc35b7960a7659adfbeeec2a34ad231e7e`, develop | Fluxos HTTP sem foto integrados; adaptação de foto pendente |
| Schema | Flyway V1–V5 | Contrato documentado; conferir histórico no ambiente de demonstração |
| Docs | Base `660043b` + branch desta consolidação | Push/revisão/publicação pendentes |
| Storage real | Bucket privado informado como existente | Validação real ainda não comprovada nas evidências |
| Versão estável/tag de entrega | A definir pela equipe | Não foi inventada tag ou publicação em produção |

Após aprovação desta documentação, registrar o SHA final dela e os SHAs efetivamente demonstrados. Não promover `main`, criar release ou alterar ambiente apenas para preencher tabela.

## Responsabilidades e critérios de conclusão

| Frente | Responsável de referência | Conferência necessária |
| --- | --- | --- |
| API e configuração | Equipe backend / Kauan | Build, env local sem segredos publicados e endpoints reais |
| Schema e histórico | Equipe de banco | V1–V5, constraints e banco correto |
| Interface e fotos | Equipe frontend | Adaptador BE-13, estados de falha/retry e ensaio visual |
| Documentação | Equipes técnicas + revisor de outro integrante | Coerência código/contratos e DOC-01 |
| Integração | Equipes frontend/backend/banco | Resultados da INT-01 com versão e evidências |
| Materiais acadêmicos | Equipe acadêmica já organizando fora do GitHub | Word, slides, Way Hub e pitch |

Não foram atribuídos novos prazos ou responsáveis individuais sem confirmação. Usar as atribuições das issues existentes para acompanhamento.

## Materiais acadêmicos externos

O enunciado fornecido solicita aplicação/código no GitHub, Word, apresentação, relatório no Way Hub e pitch de até 5 minutos; apresentação de 10–15 minutos. Esses arquivos são tratados fora do GitHub, conforme informação da equipe. A falta deles neste repositório não comprova falta de entrega.

Antes de concluir REL-01, confirmar nomes/RAs e composição do grupo autorizada pelo professor, problema/ODS coerentes entre materiais, links/acesso às entregas, duração e possibilidade de executar a aplicação. A identificação do grupo fica nos materiais acadêmicos, sem exigir publicar RAs no repositório.

## Roteiro sugerido: 12 minutos

| Tempo | Conteúdo | Evidência |
| --- | --- | --- |
| 0–2 min | Problema, público, proposta e associação com ODS 11 | [Escopo](../requirements/ESCOPO-MVP.md) |
| 2–4 min | Arquitetura e modelo de dados | [Arquitetura](../architecture/ARQUITETURA.md), [DER](../database/DER.md) |
| 4–8 min | Cadastro/login, abertura sem foto, histórico e administração | Aplicação executando com API/banco reais |
| 8–11 min | TDD, refatoração, erros, branches/PR e CI | [Qualidade](../quality/QUALIDADE.md) e código |
| 11–12 min | Limitações, próximos passos e perguntas | Estado de fotos/Storage e pendências registradas |

Demonstrar foto somente se o fluxo real tiver sido validado. Se mostrar mock, identificá-lo como simulação. Não apresentar upload API isolado como integração completa da interface.

## Antes da demonstração

- [ ] Fixar versões, executar checks e preencher [roteiro de integração](../quality/ROTEIRO-INTEGRACAO.md).
- [ ] Preparar dados fictícios, cidadão e administrador sem senhas publicadas.
- [ ] Testar rede, mapa, permissões e credenciais no computador da apresentação.
- [ ] Confirmar comportamento após reiniciar API e navegador.
- [ ] Revisar slides contra o código atual; números de testes históricos devem indicar versão.
- [ ] Confirmar Word/slides/Way Hub/pitch e duração com seus responsáveis.
- [ ] Obter revisão de outro integrante e registrar pendências aceitas.

Vinculação: [REL-01 #5](https://github.com/Zelar-App/zelar.app-docs/issues/5). Arquivo criado não significa checklist concluído.
