# Fluxos da interface

Base frontend `428ebfb`, backend `e0d47f2`, 27/10/2026. Descreve comportamento e limites para orientar manutenção; não substitui ensaio visual em dispositivos.

## Cidadão

1. Cadastro: informar nome, email, senha e telefone opcional. Mostrar erro de validação ou email duplicado; não sugerir que cadastro público concede perfil administrativo.
2. Login: autenticar na API, manter sessão e revalidar usuário/perfil ao restaurá-la. Token ausente/inválido exige novo login; logout limpa sessão local.
3. Abertura: escolher categoria, descrever e informar localização; foto opcional. Falha/permissão negada de geolocalização precisa permitir informar localização pela alternativa da interface.
4. Histórico/detalhe: apresentar somente ocorrências autorizadas; estados ENVIADA, EM_ANALISE e RESOLVIDA. Não mostrar email/telefone de autores nas ocorrências.
5. Mapa: auxilia localização/visualização conforme o conjunto autorizado; não cria acesso público a dados que a API restringe.

Categorias da interface podem ter rótulos descritivos, mantendo os códigos técnicos definidos no [contrato](../api/README.md). Filtros de categoria/autoria do cliente não são novos parâmetros HTTP do backend.

## Administrador

Painel mostra conjunto autorizado, filtros e indicadores globais. Status e prioridade usam PATCHs separados; se prioridade salvar e status falhar, informar sucesso parcial e recarregar dados, sem afirmar que tudo foi revertido.

Ocorrência nova pode aparecer sem prioridade; isso não significa que a API aceite removê-la depois. Não oferecer "Sem prioridade" como alteração persistível pelo contrato atual. Status não pode saltar, voltar ou repetir. A autorização deve ser aplicada no backend mesmo quando ações estão ocultas na tela.

## Fotografias: estado e adaptação necessária

O frontend de referência prepara imagem e bloqueia envio durante preparação. No modo HTTP, ainda lança a mensagem de que o servidor não aceita fotos. Essa mensagem ficou desatualizada após a integração da BE-13; o backend já possui upload/consulta, mas a adaptação do cliente está pendente.

Fluxo a implementar no cliente: criar ocorrência JSON → guardar ID → enviar foto multipart → apresentar resultado. Falha da foto deve preservar ocorrência e permitir retry somente da foto; nunca repetir automaticamente criação. Após resultado incerto/409, consultar `possuiFotografia` e explicar pendência quando ainda falso.

Para mostrar foto privada, buscar bytes com Bearer, criar object URL e revogá-la quando não usada ou ao mudar sessão. Não construir URL pública do Supabase nem passar token pela query string. Modo mock pode mostrar foto local, sem comprovar Storage real.

## Critérios para revisão de interface

- Desabilitar envio durante operações pendentes e explicar progresso.
- Mensagens por contexto para 400/401/403/404/409/413/503, sem credenciais ou detalhes internos.
- Permissão negada de câmera/GPS não deve apagar os demais campos preenchidos.
- Conferir fluxo de teclado, foco, labels e tamanho/contraste dos controles em celular e desktop.
- Responsividade e acessibilidade devem ser verificadas no [roteiro](../quality/ROTEIRO-INTEGRACAO.md); não há certificação de acessibilidade documentada.

Rotas e componentes: [fonte frontend](https://github.com/Zelar-App/zelar.app-frontend/tree/428ebfbc35b7960a7659adfbeeec2a34ad231e7e/src). Não usar antigos diagramas de casos de uso como evidência de que visibilidade ainda está indefinida.
