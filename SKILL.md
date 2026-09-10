---
name: candidate-validator
description: Valida se um candidato atende aos requisitos de uma vaga a partir do perfil do LinkedIn dele. Use quando o usuário pedir para "validar candidato", "analisar perfil do LinkedIn para a vaga", "ver se o candidato é fit", ou fornecer uma URL/texto de perfil do LinkedIn junto com requisitos de uma vaga. Aceita o perfil via URL (extraído por navegador) ou colado como texto, e os requisitos são sempre informados no momento do uso (não há lista fixa).
---

# Candidate Validator

Skill para validar candidatos a uma vaga com base no perfil do LinkedIn, comparando-o contra requisitos fornecidos na hora.

## Quando usar
- Usuário fornece uma ou mais URLs de LinkedIn OU um ou mais textos colados de perfil
- Usuário fornece (ou já forneceu antes na conversa) os requisitos da vaga
- Objetivo: gerar um parecer estruturado de aderência do(s) candidato(s) à vaga

## Passo 1 — Obter o(s) perfil(is)
- O usuário pode enviar **um ou vários** candidatos na mesma mensagem (múltiplas URLs de LinkedIn e/ou múltiplos textos colados). Tratar cada um como um candidato independente.
- **Delimitação entre candidatos**: identificar o fim de cada link/texto com base em quebras de linha, numeração (ex: "1.", "2.") ou marcadores que o usuário usar. Nunca presumir onde um perfil termina e outro começa quando o texto vier colado sem separação clara — nesse caso, parar e pedir ao usuário para reenviar com cada candidato separado por linha (ou numerado), em vez de arriscar misturar dados de pessoas diferentes.
- Se identificar múltiplas URLs de LinkedIn na mensagem, tratar cada URL completa (padrão `linkedin.com/in/...`) como o delimitador de um candidato novo.
- **Se vier uma URL do LinkedIn**: use a ferramenta de navegador (Claude in Chrome, se disponível) para abrir a página e extrair o conteúdo visível (experiências, cargos, tempo em cada função, formação, skills, localização, resumo/"sobre"). Se não houver navegador conectado, informe o usuário e peça que cole o texto do perfil manualmente.
- **Se vier texto colado**: use diretamente, sem necessidade de ferramentas externas.
- Nunca invente informações do perfil que não estejam explícitas no conteúdo obtido, e nunca misturar trechos de candidatos diferentes em um mesmo parecer.

## Passo 2 — Obter os requisitos da vaga
- Os requisitos variam a cada uso — sempre pergunte ou use os que o usuário já passou na mensagem.
- Se o usuário colar uma descrição de vaga completa, identificar automaticamente as seções de requisitos, reconhecendo variações comuns de nomenclatura:
  - **Obrigatório**: seções como "Requisitos Obrigatórios", "Requisitos", "Must have", "Necessário"
  - **Desejável**: seções como "Diferenciais", "Desejável", "Nice to have", "Plus"
- Extrair também, quando presentes na descrição, o **nível/senioridade da vaga** (ex: Pleno, Sênior, Júnior) e o **título da vaga** — serão usados no Passo 4.
- Se o usuário listar requisitos soltos (sem estrutura de seções), classificar cada um em obrigatório ou desejável com base no que o usuário indicar. Se não indicar, perguntar antes de prosseguir — não assumir.

## Passo 3 — Ranquear os diferenciais por relevância para a vaga
Diferenciais não têm todos o mesmo peso — alguns são centrais para a vaga, outros são complementares. Antes de avaliar o candidato, ranquear os diferenciais em ordem de importância, com base exclusivamente no que está escrito na descrição da vaga (nunca em suposição externa):

Critérios para definir importância (nessa ordem de sinal):
1. **Repetição/reforço**: diferenciais que retomam ou aprofundam algo já citado nos requisitos obrigatórios ou no resumo da vaga (ex: se a vaga fala muito de "pipelines de dados em escala" e um diferencial é "streaming de dados", isso é mais relevante do que um diferencial citado isoladamente).
2. **Proximidade com as responsabilidades principais**: diferenciais diretamente ligados às responsabilidades do dia a dia descritas na vaga têm mais peso do que os genéricos.
3. **Especificidade técnica direta**: diferenciais que são extensão natural da stack obrigatória (ex: vaga pede Databricks, diferencial cita "Certificações Databricks") pesam mais do que diferenciais de domínio adjacente (ex: "Power BI/Tableau" numa vaga majoritariamente de engenharia).
4. **Ordem de menção na descrição**: usar apenas como critério de desempate — nunca como critério principal, pois a ordem em que a empresa lista nem sempre reflete prioridade real.

Classificar cada diferencial em 3 faixas (não usar números/notas, para não confundir com o status de atendimento do Passo 4):
- **Alta relevância para a vaga**
- **Relevância moderada**
- **Relevância baixa/complementar**

Sempre que possível, dar uma justificativa curta de por que aquele diferencial caiu naquela faixa (baseada na própria descrição da vaga, nunca em opinião externa sobre o mercado).

## Passo 4 — Cruzar requisito a requisito
Para cada requisito, avaliar com base APENAS no que está no perfil (sem notas ou pesos — apenas o status). O ranking de relevância do Passo 3 não altera o status de atendimento — são análises independentes que se combinam apenas no parecer final:
- **Atende**: evidência clara no perfil
- **Atende parcialmente**: evidência incompleta ou indireta
- **Não atende**: sem evidência ou evidência contrária
- **Não é possível avaliar**: informação não disponível no perfil (não deve ser tratado como "não atende")

O perfil pode estar em português ou inglês (ou misto). Interpretar termos equivalentes nos dois idiomas ao cruzar com os requisitos (ex: "Bacharel em Administração" = "Bachelor's in Business Administration"; "Gerente de Projetos" = "Project Manager"). Os requisitos podem ser enviados em qualquer um dos dois idiomas, independente do idioma do perfil.

**Requisitos de stack técnica/ferramentas** (ex: linguagens, frameworks, plataformas como Databricks, PySpark, SQL, Airflow, dbt, Kafka, ferramentas cloud, etc.) merecem busca ampla no perfil, não só em cargos formais:
- Seção de skills/habilidades do LinkedIn
- Descrições de projetos e responsabilidades dentro de cada experiência
- Certificações listadas
- Seção "Sobre"/resumo
- Recomendações e endossos, se disponíveis
Uma stack mencionada apenas na seção de skills (sem contexto de uso real) deve ser marcada como "atende parcialmente", com observação de que não há evidência de aplicação prática — a menos que apareça também em descrição de projeto/certificação.

## Passo 5 — Avaliar coerência de trajetória
Além do checklist de requisitos, analisar a progressão de carreira do candidato:
- Pulos de cargo muito rápidos ou incomuns (ex: júnior para diretor em curto espaço de tempo, sem explicação aparente)
- Gaps longos sem atividade (períodos sem experiência, estudo ou explicação registrada)
- Trocas frequentes de emprego em curtos períodos (menos de 1 ano), quando isso for um padrão recorrente
- Coerência entre a trajetória e a senioridade exigida pela vaga
Registrar essas observações separadamente do checklist de requisitos — são um ponto de atenção, não um critério eliminatório.

**Aderência ao nível da vaga**: quando a vaga especificar um nível (ex: Pleno, Sênior), avaliar explicitamente se a senioridade do candidato é compatível, considerando:
- Tempo total de experiência na área
- Complexidade e escopo das responsabilidades exercidas (ex: liderança técnica, decisões de arquitetura, mentoria — indicativos de sênior)
- Progressão de cargos até o momento
Classificar como: **abaixo do nível pedido**, **compatível com o nível pedido**, ou **acima do nível pedido**. Isso é um ponto de atenção informativo, não elimina o candidato automaticamente — a decisão final é do avaliador humano.

## Passo 6 — Gerar parecer final
A saída é dividida em camadas — do mais resumido para o mais detalhado — e as tabelas completas só são geradas quando o usuário pedir explicitamente.

**Camada 1 — Resultado, em ordem de ranking (sempre aparece, para todos os candidatos)**
Antes de listar, ranquear os candidatos do mais apto ao menos apto para a vaga (ver critérios de ranking logo abaixo), e usar essa ordem já na Camada 1. Formato:
```
1. Nome do Candidato - Resultado (Aprovado / Aprovado com ressalvas / Não aprovado)
2. Nome do Candidato - Resultado
...
```
- **Regra eliminatória, sem exceção**: se qualquer requisito obrigatório estiver marcado como "Não atende", o resultado é automaticamente **Não aprovado** — independentemente de quantos diferenciais o candidato atenda ou de quão forte seja o restante do perfil. Diferenciais nunca compensam um requisito obrigatório não atendido.
- Um requisito obrigatório marcado como "Não é possível avaliar" (informação ausente no perfil, não uma evidência contrária) não é reprovação automática — nesse caso, considerar **Aprovado com ressalvas**, sinalizando a necessidade de confirmar esse ponto em entrevista.
- "Aprovado com ressalvas" também se aplica quando todos os obrigatórios são atendidos, mas há itens "Atende parcialmente" relevantes.
- "Aprovado" só quando todos os obrigatórios estiverem em "Atende".

**Critérios de ranking (ordem de posição na lista):**
1. Todos os candidatos **Não aprovados** ficam sempre depois dos Aprovados e Aprovados com ressalvas, independentemente de qualquer outro critério — reprovação em obrigatório nunca é superada por diferenciais.
2. Entre candidatos com o mesmo resultado (ex: todos Aprovados), ordenar por quantidade e relevância de diferenciais atendidos: quem atende mais diferenciais de alta relevância (Passo 3) fica em posição melhor.
3. Em caso de empate no critério acima, usar como desempate a qualidade de atendimento dos obrigatórios (mais itens em "Atende" total, versus "Atende parcialmente") e a aderência ao nível da vaga (Passo 5).
4. Entre os Não aprovados, ordenar por proximidade de aprovação (quem tem menos obrigatórios não atendidos vem primeiro entre eles) — isso só afeta a ordem dentro do grupo de reprovados, nunca move um reprovado para cima de um aprovado.

Se houver mais de um candidato, listar todos nessa camada, na ordem de ranking definida acima. Usar o nome do candidato quando disponível no perfil; caso não esteja disponível, usar "Candidato 1", "Candidato 2" etc. (mantendo a referência à ordem de envio original entre parênteses, ex: "Candidato 2 (3º link enviado)", para evitar confusão).

**Camada 2 — Ranking final detalhado (aparece logo abaixo da Camada 1, apenas quando houver mais de um candidato)**
Um bloco separado, com título próprio (ex: "Ranking final"), contendo a lista ordenada de candidatos (mesma ordem da Camada 1) e uma justificativa breve (1-2 linhas) do porquê de cada posição, comparando pontos fortes entre eles. Exemplo de formato:
```
Ranking final:
1º Vicente — Aprovado. Atende todos os obrigatórios e ainda cobre 3 dos 4 diferenciais de alta relevância para a vaga.
2º Bruno — Aprovado. Atende todos os obrigatórios, mas cobre apenas 1 diferencial de alta relevância.
3º Gabriel — Não aprovado. Não atende ao requisito obrigatório de X.
```
Esse ranking é comparativo por natureza (diferente das demais camadas, que são individuais) — é o único ponto da resposta onde os candidatos são explicitamente comparados entre si. Se houver apenas um candidato, pular esta camada inteiramente (não há o que comparar).

**Camada 3 — Resumo do motivo (logo abaixo da Camada 2, um bloco separado por candidato)**
Título próprio (ex: "Resumo por candidato"), e dentro dele, para cada candidato — na mesma ordem de ranking das camadas anteriores — um subtítulo com o nome e um parágrafo de aproximadamente 5 linhas explicando o porquê do resultado: quais requisitos obrigatórios pesaram na decisão (atendidos ou não), e, se houver, o peso qualitativo dos diferenciais de maior relevância. Não usar tabelas nessa camada — é só texto corrido, direto ao ponto. Deixar essa seção visualmente separada das anteriores (ex: com um divisor ou cabeçalho), para não misturar com o ranking.

**Camada 4 — Tabelas completas (só sob demanda, é a última coisa da resposta)**
Depois de entregar as Camadas 1, 2 e 3 de todos os candidatos, perguntar ao usuário se deseja ver as tabelas completas, e para quais candidatos (todos, ou apenas alguns específicos). Não gerar tabelas antes dessa confirmação — essa pergunta é sempre o fechamento da resposta.

Quando o usuário confirmar (todos ou uma lista específica), gerar para cada candidato selecionado:
1. **Tabela de requisitos obrigatórios**: requisito | atende? (Sim/Não/Parcial/Não avaliável) | observação (motivo do status)
2. **Requisitos obrigatórios não atendidos**: lista logo abaixo da tabela acima, só com os itens marcados como "Não" ou "Não avaliável" — se todos forem atendidos, indicar isso em uma linha simples ("Todos os requisitos obrigatórios foram atendidos").
3. **Tabela de diferenciais**: diferencial | atende? (Sim/Não/Parcial/Não avaliável) | observação (motivo do status)

Não incluir observações de trajetória ou gaps para entrevista em nenhuma camada — esses itens continuam sendo considerados internamente (ex: o ranking do Passo 3 pondera as Camadas 2 e 3), mas nunca aparecem na resposta ao usuário.

## Regras importantes
- Não fazer suposições sobre informações que não estão no perfil (ex: não inferir "sabe inglês" apenas porque trabalhou em empresa multinacional, a menos que esteja explícito).
- Ser transparente quando a extração do LinkedIn for parcial ou limitada (perfis podem ocultar informações).
- O ranking de relevância dos diferenciais (Passo 3) é sempre derivado do texto da vaga, nunca de suposições genéricas de mercado — se a vaga não der sinal suficiente para ranquear algum diferencial, tratá-lo como relevância moderada por padrão.
- Nunca confundir o ranking de relevância (Passo 3, interno) com o status de atendimento (Passo 4, exibido) — são eixos independentes.
- O parecer é um apoio à decisão humana, não uma aprovação/reprovação automática — deixar isso explícito na resposta.
- **Requisito obrigatório não atendido = reprovação automática.** Essa é a regra mais importante da skill: um único "Não atende" em requisito obrigatório já define o resultado como Não aprovado, mesmo que o restante do perfil seja excelente. Nunca "compensar" isso com diferenciais fortes ao decidir o resultado final.
- Se o usuário enviar vários candidatos na mesma mensagem, entregar as Camadas 1, 2 e 3 (resultado ordenado, ranking detalhado, resumo por candidato) de todos eles antes de perguntar sobre as tabelas — nunca gerar tabelas de forma antecipada, mesmo que pareça útil. A pergunta sobre tabelas é sempre a última coisa da resposta.
- O ranking (Camadas 1 e 2) é sempre calculado com base nos critérios objetivos definidos no Passo 6 (obrigatórios atendidos, diferenciais de alta relevância atendidos, aderência a nível como desempate) — nunca em impressão subjetiva do perfil.
- Candidatos Não aprovados sempre ficam depois dos Aprovados/Aprovados com ressalvas no ranking, mesmo que tenham diferenciais fortes — a reprovação em obrigatório é sempre o critério dominante.
