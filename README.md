# github dev stats dashboard

> dashboard minimalista em tempo real que transforma dados públicos do GitHub em um perfil de dev limpo e elegante para o portfólio — sem autenticação.

![api](https://img.shields.io/badge/api-github%20público-black?style=flat-square) ![auth](https://img.shields.io/badge/auth-nenhuma-black?style=flat-square) ![estilo](https://img.shields.io/badge/estilo-minimal%20clean-black?style=flat-square)

---

## sobre o projeto

dashboard de página única construído como um artefato interativo que busca dados ao vivo da [GitHub Public API](https://docs.github.com/en/rest) e os apresenta como um perfil de dev limpo e legível. projetado para viver dentro de um portfólio sem nenhuma configuração de backend.

digite qualquer username do github → pressione enter → métricas reais, instantaneamente.

---

## funcionalidades

| seção | o que exibe |
|---|---|
| **perfil** | avatar, nome, bio, localização, empresa, site |
| **métricas** | repos, total de stars, seguidores, membro desde, gists, forks |
| **top linguagens** | barra proporcional + percentual calculado a partir dos repos |
| **top repositórios** | ordenados por stars — nome, descrição, linguagem, forks |
| **grade de atividade** | visual estilo contribution graph das últimas 52 semanas |

---

## como funciona

```
usuário digita o username
         ↓
GitHub API /users/{username}          → perfil, contagem de seguidores, metadados
GitHub API /users/{username}/repos    → top 100 repos ordenados por stars
         ↓
agregação no lado do cliente
  · total de stars    = soma de stargazers_count em todos os repos
  · top linguagens    = contagem de repos por linguagem, normalizada em %
  · top repos         = ordenados por stars, top 6 exibidos
         ↓
renderizado em < 1s — sem servidor, sem token, sem build
```

---

## fontes de dados

todos os dados vêm da **GitHub REST API v3** — endpoints públicos, sem autenticação necessária.

```
https://api.github.com/users/{username}
https://api.github.com/users/{username}/repos?sort=stars&per_page=100
```

**rate limit:** requisições sem autenticação são limitadas a 60/hora por IP. para uso em portfólio isso é mais do que suficiente.

> nota: a grade de atividade é uma visualização simulada. a API do GitHub não expõe o histórico de contribuições por semana sem autenticação.

---

## stack

- **vanilla html/css/js** — sem framework, sem dependências, sem build
- **github public api** — zero autenticação necessária
- **css variables** — suporte automático a modo claro/escuro
- **tabler icons** — ícones outline carregados via cdn
- **fonte:** stack monospace para aquela sensação nativa de dev

---

## decisões de design

**por que monospace?** dashboards de dev vivem no mundo de terminais, editores e código. uma fonte mono faz números e labels parecerem nativos, não enfeitados.

**por que sem token de auth?** simplicidade para embed em portfólio. a API pública cobre tudo que é exibido — perfil, repos, stars, linguagens, forks.

**por que simular a grade de atividade?** o gráfico de contribuições requer acesso GraphQL autenticado. uma grade simulada preserva a intenção visual sem precisar de token ou proxy backend.

**por que top 6 repos?** sinal suficiente para mostrar variedade, sem virar ruído.

---

## uso

### embed em portfólio (html)

```html
<!-- cole dentro de um iframe ou renderize diretamente na sua página -->
<iframe
  src="url-do-seu-dashboard"
  width="100%"
  height="700"
  frameborder="0"
></iframe>
```

### rodar localmente

sem build — abra o arquivo html direto no navegador:

```bash
# clone ou baixe o arquivo
open index.html
# ou
python3 -m http.server 3000
```

### usuário padrão

o dashboard carrega `torvalds` por padrão. para mudar, edite o valor inicial do input no html:

```html
<input type="text" id="username-input" value="seu-username" />
```

---

## personalização

### alterar número de repos exibidos

```js
const topRepos = repos
  .sort((a, b) => b.stargazers_count - a.stargazers_count)
  .slice(0, 6);  // ← altere 6 para qualquer número
```

### adicionar ou remover cards de métricas

edite o array `metrics` no script:

```js
const metrics = [
  { label: 'repositórios', value: fmtNum(user.public_repos), sub: `${nonForkRepos.length} originais` },
  { label: 'total de stars', value: fmtNum(totalStars), sub: 'em todos os repos' },
  // adicione ou remova objetos aqui
];
```

### adicionar cores de linguagens

```js
const LANG_COLORS = {
  JavaScript: '#f1e05a',
  TypeScript: '#3178c6',
  // adicione qualquer linguagem que não esteja na lista
  Zig: '#ec915c',
};
```

---

## referência da api

### `GET /users/{username}`

retorna o perfil do usuário. campos utilizados:

```
avatar_url · name · login · bio · location · company · blog
public_repos · public_gists · followers · following · created_at
```

### `GET /users/{username}/repos`

retorna até 100 repos. campos utilizados:

```
name · description · language · stargazers_count · forks_count
fork · topics
```

---

## limitações

- **sem histórico de commits** — requer autenticação
- **sem repos privados** — apenas API pública
- **limite de 100 repos** — máximo por página da API do GitHub (cobre a maioria dos usuários)
- **60 req/hora** — rate limit sem autenticação
- **grade de atividade simulada** — dados reais requerem GraphQL autenticado

---

## licença

mit — use livremente no seu portfólio, faça fork, modifique, publique.

---

*construído com a github public api · sem backend · sem auth · sem framework*
