# Candidate Validator

Skill para Claude que valida candidatos a uma vaga a partir do perfil do LinkedIn, comparando as informações do perfil com os requisitos da vaga informados no momento do uso.

## O que essa skill faz

Dado um ou mais perfis de candidatos (via URL do LinkedIn ou texto colado) e os requisitos de uma vaga, a skill gera um parecer estruturado de aderência, incluindo:

- Extração do perfil do LinkedIn via navegador (Claude in Chrome) ou a partir de texto colado pelo usuário
- Identificação automática dos requisitos obrigatórios e desejáveis a partir da descrição da vaga (ou classificação manual, quando informados soltos)
- Ranking dos diferenciais por relevância para a vaga, com base no texto da própria descrição
- Cruzamento requisito a requisito contra o perfil, classificando cada item como Atende, Atende parcialmente, Não atende ou Não é possível avaliar
- Análise de coerência de trajetória (pulos de cargo, gaps, trocas frequentes de emprego) e aderência ao nível de senioridade da vaga
- Parecer final em camadas — do resumo (resultado e ranking entre candidatos) até o detalhado (tabelas completas por requisito), geradas somente sob demanda

## Regra principal

Um único requisito obrigatório marcado como "Não atende" já reprova o candidato automaticamente, independentemente de quantos diferenciais ele cubra. Diferenciais nunca compensam a ausência de um requisito obrigatório.

## Quando usar

- Quando você tem uma ou mais URLs de perfil do LinkedIn (ou o texto do perfil) e os requisitos de uma vaga
- Quando quiser comparar vários candidatos entre si para a mesma vaga e obter um ranking
- Quando precisar de um parecer estruturado como apoio à decisão de contratação (a decisão final é sempre humana)

## Como usar

1. Envie o(s) perfil(is) do candidato — via URL do LinkedIn (a skill usa o navegador para extrair o conteúdo) ou colando o texto do perfil diretamente.
2. Informe os requisitos da vaga — pode ser a descrição completa da vaga (a skill identifica automaticamente as seções de requisitos obrigatórios e diferenciais) ou uma lista de requisitos soltos.
3. A skill responde primeiro com o resultado resumido (aprovado / aprovado com ressalvas / não aprovado) e o ranking entre candidatos, se houver mais de um.
4. Ao final, ela pergunta se você quer ver as tabelas completas de requisito por requisito — geradas apenas quando solicitado.

## Instalação

Copie o arquivo `SKILL.md` para a pasta de skills do seu projeto Claude:

```bash
mkdir -p .claude/skills/candidate-validator
cp SKILL.md .claude/skills/candidate-validator/SKILL.md
```

## Requisitos

- Claude Code ou Claude com suporte a skills
- Para validar perfis via URL do LinkedIn: navegador conectado (Claude in Chrome). Sem navegador, o texto do perfil pode ser colado manualmente.

## Aviso

O parecer gerado é um apoio à decisão humana de contratação, não uma aprovação ou reprovação automática de candidatos.
