# Desafio Técnico Automação de Matrículas e Boas vindas 🚀

Este repositório contém a minha solução para o desafio de automação de matrículas utilizando o n8n. O objetivo principal deste fluxo é cumprir todos os requisitos obrigatórios solicitados, garantindo a captura, o tratamento seguro dos dados e a comunicação automatizada com os novos alunos.

## Como o fluxo funciona ⚙️

O processo foi dividido em etapas claras e separadas por contexto para facilitar a visualização e a manutenção.

No recebimento da requisição, o fluxo é iniciado por um Webhook configurado no método POST, que aguarda o payload com os dados do novo aluno.

Para o tratamento de dados, utilizei um nó de definição de variáveis com funções JavaScript para padronizar as informações. O nome recebe a primeira letra de cada palavra maiúscula, a data é convertida do padrão brasileiro para o americano (YYYY-MM-DD) e utilizei Regex para extrair e manter apenas os números do telefone.

No roteamento, um nó de Switch atua como a regra de negócio principal, separando o caminho lógico entre os cursos Tech e Business.

Na etapa de armazenamento, para garantir total aderência às instruções, implementei uma dupla inserção. Primeiro, os dados passam por um nó de PostgreSQL, conectado a uma instância do Supabase que criei especificamente para suprir a falta de credenciais do teste. Em seguida, os dados também são salvos nas Data Tables nativas do n8n, cobrindo assim tanto o requisito em texto quanto o tutorial em vídeo fornecido.

Por fim, o fluxo é finalizado com o disparo de um e-mail transacional de boas vindas, contendo as variáveis dinâmicas mapeadas diretamente do payload já processado.

## O Desafio Oculto 🕵️‍♂️

Durante a análise dos requisitos, identifiquei um risco operacional grave na documentação em relação a uma inconsistência nas chaves do JSON. O escopo em texto indicava o recebimento das chaves como email_aluno e curso_escolhido, enquanto o payload de exemplo enviava apenas email e curso.

Se o fluxo fosse montado cegamente apenas com base em uma das fontes, o mapeamento falharia na vida real, inserindo campos vazios no banco de dados e quebrando o disparo de e-mails. Construí um fluxo Sender de testes simulando a entrada real dos dados para homologar o funcionamento exato do payload, garantindo a integridade da automação de ponta a ponta.

## Apresentação da Solução 🎥

Gravei um vídeo rápido demonstrando o fluxo em execução, explicando a lógica por trás do tratamento de strings e como configurei a arquitetura do banco de dados.

Link do vídeo Principal: https://www.loom.com/share/d7ce3964991b404f94bf2419db68dcf2


## O Nível Extra: Bot do Telegram e Arquitetura Escalável 🚀

Para demonstrar domínio avançado com a ferramenta e apresentar uma solução mais robusta, desenvolvi uma versão extra do projeto com uma arquitetura de múltiplas integrações e coleta de dados conversacional.

Criei um segundo fluxo que atua como o front-end dessa operação utilizando um Bot do Telegram. Esse fluxo é acionado sempre que um usuário manda uma mensagem. O n8n faz uma consulta ao banco de dados Supabase para analisar se o ID daquele usuário já existe. Se for um usuário novo, o bot inicia um fluxo de atendimento passo a passo, guardando o estado da conversa e salvando cada resposta progressivamente no banco de dados.

O bot interage com o aluno pedindo, na ordem, o nome, o e-mail, a data de nascimento, o telefone e o curso. Na última etapa da conversa, o fluxo faz uma consulta final para resgatar todos os dados que foram preenchidos e os passa pelo nó de tratamento, aplicando funções de Title Case, alteração de formato de data e as expressões regulares.

Após finalizar o tratamento, o bot envia uma mensagem de confirmação no chat do Telegram e faz uma requisição POST automática (funcionando como um Sender) enviando o payload finalizado direto para o Webhook do fluxo principal.

O fluxo principal também recebeu um upgrade de segurança e escalabilidade. No nó de decisão, implementei uma rota Default além das rotas Tech e Business. Isso atua como um fallback para garantir que, se o aluno digitar um curso diferente no Telegram, o sistema não descarte o cadastro e salve o registro em uma área de contingência.

Por fim, as funções de armazenamento foram expandidas. Agora, o fluxo salva os dados do aluno simultaneamente em três locais diferentes: em tabelas do banco PostgreSQL, em planilhas do Google Sheets para acesso rápido da equipe e nas Data Tables nativas do n8n. No final do processo de triplo armazenamento, o e-mail de boas vindas é disparado com sucesso para o aluno.

Link do vídeo parte Extra: https://youtu.be/XcOQJnpLRzo
