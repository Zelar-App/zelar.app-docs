# Decisões técnicas e limites

Registro consolidado em 27/10/2026 a partir do código e das decisões da equipe. "Adotada" significa refletida na implementação de referência, sem substituir revisão documental de outro integrante.

## D-01 — API em camadas e contratos explícitos — adotada

Java/Spring Boot, PostgreSQL, DTOs e organização por domínio. Controllers cuidam de HTTP; serviços de casos de uso e autorização; entidades de invariantes; repositórios de persistência. Efeito: mudanças no schema não devem expor automaticamente atributos no JSON. Custo: manter os mapeamentos e testes de contrato.

## D-02 — JWT próprio e perfil atual — adotada

HS256, chave Base64 de pelo menos 32 bytes, emissor e duração configuráveis. Token assinado, não criptografado; sem dados pessoais nas claims. Conferir conta/perfil atual nas operações evita manter privilégio antigo só porque o token ainda vale. Não há refresh/revogação; encerrar sessão no cliente não invalida token já emitido. Banco continua necessário para autorização.

## D-03 — Migrações versionadas — adotada

Flyway V1–V5 e Hibernate validate. Domínios persistidos em VARCHAR + CHECK. V4 não distribui administrador/senha padrão. Evolução incremental, sem editar migration aplicada. H2 é ambiente rápido de teste; PostgreSQL real complementa compatibilidade e constraints.

## D-04 — Visibilidade e administração — adotada

Cidadão vê somente suas ocorrências; administrador vê todas. Status avança em duas etapas sem reabertura. Prioridade é opcional inicialmente e atribuída por administrador, sem remoção por PATCH. Bloqueio de registro protege alterações concorrentes. Não há painel público de ocorrências, nem SLA garantido.

## D-05 — Fotografias privadas no Supabase — adotada no backend

Uma foto opcional por ocorrência; conteúdo JPEG/PNG validado e regravado; bucket privado, chave UUID e acesso por Bearer na API. Sem nome original, URL pública ou chave secreta no frontend. O contrato substitui o modelo anterior de múltiplos anexos e exclui a alternativa S3 da branch `feat/foto-resolver` desta integração. Custos: tráfego de leitura passa pela API e o cliente precisa fazer busca autenticada.

## D-06 — Recuperação eventual — adotada no backend

Não há transação única entre banco e Storage. Reservar antes da chamada externa, publicar só após confirmação e recuperar operações interrompidas. Estados duráveis e exclusão de objeto antes do metadado permitem nova tentativa. Não prometer exatamente uma execução de rede, disponibilidade imediata após falha ou proteção contra edição manual dos sistemas.

## D-07 — Desenvolvimento por proxy local — adotada

Frontend em modo HTTP usa `/api` na mesma origem, com proxy Vite para backend. Mock continua disponível como demonstração. Publicação exige configurar roteamento da API e HTTPS; `npm run preview` não substitui o proxy de desenvolvimento. A política CORS de produção não está configurada como solução universal no código conferido.

## Dados pessoais e retenção

**D-08 — medidas adotadas:** senha somente como hash; JWT sem nome/email/telefone; ocorrências sem dados cadastrais do autor na resposta; acesso por autoria/perfil; foto privada e regravada para retirar metadados. Ainda existe `usuarioId` e a própria descrição/foto pode conter informação pessoal. Isso não equivale a anonimização nem comprova conformidade legal integral.

**Pendência de produto/equipe:** definir prazo e finalidade de retenção, procedimento autorizado de exclusão/anonimização, cópias de segurança e tratamento de solicitações. Não foi criado prazo fictício nem endpoint de exclusão nesta documentação. Dados fictícios de ensaio devem ficar em banco/bucket isolados; limpeza deve ser aprovada para esse ambiente e executada sem afetar o compartilhado.

## Pendências técnicas explicitadas

- Validar upload/consulta/recuperação com o bucket real e credenciais locais.
- Integrar fotos no frontend; hoje o adaptador HTTP impede envio com anexo.
- Definir metas de volume/latência antes de afirmar atendimento a desempenho.
- Definir versão estável/publicação e ensaiar ambiente de apresentação.
- Datas de DTO sem offset são comportamento atual; normalização global exige mudança planejada.

Responsabilidade de consolidação: [DOC-01](https://github.com/Zelar-App/zelar.app-docs/issues/3). A documentação registra pendências; não as apresenta como implementações.
