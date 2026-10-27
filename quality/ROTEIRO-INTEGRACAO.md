# Roteiro de validação integrada

Vinculado à [INT-01 #4](https://github.com/Zelar-App/zelar.app-docs/issues/4). Este arquivo é um procedimento; caixas abertas **não representam testes executados**. Histórico conhecido na [qualidade](QUALIDADE.md); executar novamente após alterações de integração.

## Preparação e registro

Usar banco e bucket de ensaio identificados, sem dados reais. Registrar sem segredos:

| Campo | Preencher na execução |
| --- | --- |
| Data/hora e responsável | Pendente |
| SHA backend / frontend / docs | Pendente |
| Java / Node / PostgreSQL | Pendente |
| API e origem frontend | Pendente |
| Banco isolado e bucket privado | Identificadores sem credenciais |
| Migrations aplicadas | V1–V5 esperadas |
| Resultado e evidência | Run de CI, relatório revisado ou captura sem token |
| Falhas e correções | Issue/PR existente ou nova somente se não houver cobertura |

Criar cidadão A, cidadão B e administrador em ambiente de ensaio. Não publicar senhas/tokens. Usar [guia de configuração](../operations/EXECUCAO.md).

## Fluxo sem fotografia

- [ ] Cadastrar A e B (201); repetir email (409); campos inválidos (400).
- [ ] Login correto (200), incorreto (401); revalidar sessão em `/auth/me`; acesso protegido sem Bearer (401).
- [ ] Criar ocorrência A com categoria/descrição/coordenadas (201, ENVIADA, prioridade nula, sem foto).
- [ ] Categorias oficiais e limites de coordenadas; inválidos (400), sem gravar ocorrência.
- [ ] A lista/consulta seu registro; B não o lista e recebe 404 no detalhe; administrador vê ambos.
- [ ] Conferir paginação em conjunto maior que uma página, filtros combinados e ordenação estável; tamanho >100/página negativa retornam 400.
- [ ] Administrador define ALTA, consulta reflexo em lista/detalhe; repetir ALTA preserva data; tentar nulo retorna 400.
- [ ] Cidadão tenta prioridade/status e recebe 403; não confiar apenas no botão oculto.
- [ ] ENVIADA → EM_ANALISE → RESOLVIDA; salto/repetição/retorno/reabertura retornam 409.
- [ ] Indicadores globais refletem mudanças; filtros da lista não alteram indicadores; cidadão recebe 403.
- [ ] Em ensaio controlado, duas transições simultâneas para o mesmo próximo status produzem uma mudança e um conflito; prioridade/status não perdem campos.
- [ ] Reiniciar API e verificar persistência; fechar/reabrir navegador e conferir restauração/validação da sessão e logout.

## Fotografias: API, Storage e interface

- [ ] Bucket realmente privado e variáveis do backend configuradas; nenhuma chave de servidor no frontend.
- [ ] Enviar JPEG/PNG real, pequeno, pelo autor: 201, Location protegido, metadados válidos e `possuiFotografia=true`.
- [ ] Consultar bytes como autor/admin; cabeçalhos no-store/private e nosniff; sem token 401; cidadão B 404.
- [ ] Administrador que não é autor tenta enviar e recebe 403; segundo envio recebe 409.
- [ ] Arquivo disfarçado/MIME incompatível ou dimensões inválidas: 400. Arquivo acima do limite multipart: 413. Conteúdo regravado não preserva metadados originais.
- [ ] Objeto não é acessível anonimamente no bucket; não publicar a chave usada para esse teste.
- [ ] Em ambiente isolado, simular indisponibilidade do Storage sem destruir objetos disponíveis: 503, ocorrência preservada, reserva recuperável quando criada.
- [ ] Conferir recuperação após carência, exclusão idempotente e remoção posterior do vínculo; falhas preservam pendência e fotos disponíveis não são removidas.
- [ ] Cliente cria ocorrência uma vez e envia foto em segunda etapa; erro da segunda etapa preserva ID para retry sem duplicação.
- [ ] Cliente trata 409 consultando disponibilidade, busca imagem com Bearer e revoga object URL ao trocar sessão/desmontar.

**Na base frontend `428ebfb`, os dois últimos itens ainda dependem de implementação.** O adaptador bloqueia envio de foto; não marcar fluxo completo como aprovado com teste apenas da API ou mock.

## Interface e demonstração

- [ ] Mensagens de erro claras, sem stack trace/token; atualização parcialmente concluída é informada e painel recarrega dados.
- [ ] Formulário impede envio durante preparação da foto; câmera/GPS com permissão negada têm alternativa e erro compreensível.
- [ ] Verificar celular e desktop, navegação por teclado, foco/labels e contraste em telas principais; registrar limitações encontradas.
- [ ] Testar GPS/câmera em dispositivo físico quando usados na apresentação; mock e ambiente de teste não substituem esse ensaio.
- [ ] Executar lint/testes/build e conferir CI do SHA escolhido; registrar resultado e pendências.

## Conclusão da rodada

Preencher resultado por grupo: aprovado, reprovado ou não executado; responsável; evidência; falha; ação/PR. Fechar INT-01 somente quando critérios estiverem atendidos ou exclusões de escopo tiverem decisão explícita e registrada. Não transformar pendência em sucesso por ausência de erro aparente.
