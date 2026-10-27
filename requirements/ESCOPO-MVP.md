# Escopo e requisitos atuais

Consolidação em 27/10/2026. Mantém os identificadores RF01–RF06 do levantamento original, com decisões agora implementadas. PDFs anteriores permanecem históricos.

## Problema, público e resultado esperado

Relatos de buracos, iluminação, lixo, alagamentos, calçadas, sinalização e problemas ambientais precisam de registro com localização e acompanhamento. Zelar.app oferece uma interface ao cidadão e um painel ao responsável administrativo para organizar esses relatos. O MVP apoia registro e acompanhamento; não comprova encaminhamento a um órgão público, execução de reparo ou parceria institucional.

Público: cidadãos autenticados e equipe administrativa. Cada cidadão acompanha seus próprios registros; administradores gerenciam o conjunto. A localização apoia identificação do problema; a fotografia é opcional.

## Relação com ODS

**Associação proposta para a apresentação: ODS 11 — Cidades e comunidades sustentáveis.** O registro localizado de problemas de infraestrutura e ambiente urbano pode apoiar identificação de demandas e acompanhamento administrativo. Essa ligação explica a finalidade do produto; não representa certificação, medição de impacto ou garantia de que a prefeitura solucionará ocorrências. A equipe deve confirmar a mesma escolha nos materiais acadêmicos externos.

Referência: [ONU — Objetivo 11](https://sdgs.un.org/goals/goal11).

## Requisitos funcionais

| ID | Comportamento na versão documentada |
| --- | --- |
| RF01 | Cadastrar cidadão com nome, email único, senha e telefone opcional |
| RF02 | Login JWT; consultar usuário autenticado; diferenciar cidadão e administrador |
| RF03 | Criar ocorrência autenticada com categoria, descrição e localização; uma fotografia opcional em etapa separada |
| RF03.1 | Categorias HTTP: BURACO, ILUMINACAO, LIXO, ALAGAMENTO, CALCADA, SINALIZACAO, AMBIENTAL |
| RF03.2 | Latitude/longitude obrigatórias; endereço aproximado opcional |
| RF04 | Listar com paginação e consultar detalhe; cidadão vê próprias e administrador todas |
| RF05 | Administrador avança status ENVIADA → EM_ANALISE → RESOLVIDA; cidadão acompanha pelas consultas |
| RF06 | Administrador lista, filtra status/prioridade, atribui prioridade e consulta contagens globais por status |

Backend de fotografia integrado nos PRs #85/#86. Integração da interface e validação do bucket real seguem pendentes; não considerar RF03 com foto validado de ponta a ponta.

## Requisitos não funcionais

| ID original | Tratamento e limite |
| --- | --- |
| RNF-S01 | JWT assinado/expiração, conta e perfil atual, autorização por recurso |
| RNF-S02 | Hash BCrypt com salt; sem senha em respostas |
| RNF-S03 | Validação de DTO, consultas via JPA/parâmetros e mensagens de erro; não há alegação de auditoria completa contra toda classe de ataque |
| RNF-S04 | Conteúdo real JPEG/PNG, MIME/tamanho/dimensões, uma foto, acesso privado; validação real do provedor pendente |
| RNF-S05 | Minimização de dados expostos e metadados de imagem removidos; retenção/exclusão ainda requer decisão |
| RNF-D01 | Paginação, índice por usuário e indicadores agregados; metas de carga/latência não definidas nem homologadas |
| RNF-D02 | Separação frontend/API/banco/Storage e contratos documentados |
| RNF-U01 | Fluxos simples e erros úteis; ensaio visual completo da versão final pendente |
| RNF-U02 | Interface responsiva; ensaio físico de GPS/câmera e dispositivos deve ser registrado |

## Fora do MVP e evolução

IA para classificação/análise, notificações, SLA garantido, refresh token, recuperação de senha, reabertura, edição/exclusão de ocorrência, exclusão/substituição de foto e gestão completa de retenção não estão implementados no contrato conferido. Não prometer funcionalidades futuras na demonstração.

O levantamento inicial tratava mapas como evolução; a interface atual já utiliza mapas/geocodificação. Registrar essa evolução sem confundir com uma API pública de ocorrências ou IA. Não existe medição demonstrada de impacto urbano real.

Cada requisito e evidência está relacionado na [matriz de rastreabilidade](RASTREABILIDADE.md).
