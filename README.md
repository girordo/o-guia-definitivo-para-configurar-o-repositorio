<div align="center">
<img src="https://media.giphy.com/media/O5R3B0uGvCUBITMXjt/giphy.gif" loading=lazy width="20%" alt="French Bulldog" />
<h6>Troquei por esse gif, ficou melhor</h6>
</div>
<h1 align="center">O GUIA DEFINITIVO PARA CONFIGURAR O REPOSITÓRIO</h1>
<p align="center">Todas as instruções que indico aqui são o que uso no meu dia como desenvolvedor</p>

# Tabela de conteúdos

<!--ts-->

- [Abstract](#abstract)
- [Tecnologias](#Tecnologias)
- [Requisitos mínimos](#abstract)
- [O guia](#o-guia)
<!--te-->

## Personal Note

- Ainda não acabei de configurar o path mapping
- Configurar um com Typescript também
- Ainda não sei se o lint-staged e o husky estão funcionando

## Abstract

Resolvi fazer esse guia pois tô cansado de toda vez que vou começar um projeto(não uso boilerplate) ter que ficar
garimpando todos os guias que conheço para deixar pronto, então farei tudo num único, espero que seja de ajuda a alguém!
Eu sei que o CRA não é o melhor dos cenários, era melhor explicar usar o webpack e babel mas o negócio é ser rápido aqui

### Tecnologias

- <img src="https://github.com/girordo/geticon/blob/master/logos/react.svg" alt="React" width="28px" height="28px"/> React
- <img src="https://github.com/girordo/geticon/blob/master/logos/eslint.svg" alt="ESLint" width="28px" height="28px"/> ESLint
- <img src="https://github.com/girordo/geticon/blob/master/logos/prettier.svg" alt="Prettier" width="28px" height="28px"/> Prettier
- <img src="https://editorconfig.org/logo.png" alt="EditorConfig" width="28px" height="28px"> EditorConfig
- <img src="https://www.pinclipart.com/picdir/big/565-5651740_wolf-emoji-clipart-wolf-emoji-transparent-background-png.png" alt="Husky" width="28px" height="28px"/> Husky
- <img src="https://github.com/girordo/geticon/blob/master/logos/docker-icon.svg" alt="Docker" width="28px" height="28px"/> Docker

### Requisitos mínimos

Primeiramente segundamente, você deve ter o Node instalado de preferência com [NVM](https://github.com/nvm-sh/nvm)
Você deve ter instalado o [CRA](https://create-react-app.dev/docs/getting-started/) e o
[Docker](https://docs.docker.com/)(essa parte não vou explicar pois a documentação de ambos é bem completa).

## O guia

Depois que você possuir tudo instalado que eu citei acima vamos as configurações:

```bash

# Rode o CRA
$ create react-app meu-repo

# Entre na pasta criada pelo CRA
$ cd meu-repo

# Para rodar o projeto
$ yarn start

```

### Instalando as depedências necessárias

Com o projeto rodando vamos configurar agora o ESLint, Prettier, EditorConfig e Husky:

```bash

# Instalando o eslint, prettier e plugins
$  yarn add -D eslint prettier eslint-plugin-react eslint-plugin-react-hooks \
    eslint-config-prettier eslint-plugin-prettier
    eslint-plugin-jsx-a11y husky lint-staged react-app-rewired

# Criando arquivos ignore
$ touch .eslintignore .prettierignore

```

### Populando os arquivos ignore

Dentro de ambos arquivos <kbd>.eslintignore</kbd> e <kbd>.prettierignore</kbd> insira:

```bash

node_modules/
build/

```

### Criando os arquivos de configuração do ESLint, Prettiere e EditorConfig

Crie os arquivos .eslintrc, .prettierrc e .editorconfig

```bash
touch .eslintrc .prettierrc .editorconfig
```

### Populando o arquivo .prettierrc

Dentro do arquivo .prettierrc insira:

```js

{
  "semi": true,
  "singleQuote": true,
  "trailingComma": "all",
  "jsxBracketSameLine": true
}

```

### Populando o .eslintrc

Dentro do arquivo .eslintrc insira:

```js
module.exports = {
  root: true,
  parserOptions: {
    ecmaVersion: 2020,
    sourceType: "module",
    ecmaFeatures: {
      jsx: true,
    },
  },
  settings: {
    react: {
      version: "detect",
    },
  },
  env: {
    browser: true,
    amd: true,
    node: true,
  },
  extends: [
    "eslint:recommended",
    "plugin:react/recommended",
    "plugin:jsx-a11y/recommended",
    "plugin:prettier/recommended",
  ],
  rules: {
    "prettier/prettier": ["error", {}, { usePrettierrc: true }],
  },
};
```

### Populando o arquivo .editorconfig

Dentro do arquivo .editorconfig insira:

```bash
root = true

[*]
indent_style = space
indent_size = 2
end_of_line = lf
charset = utf-8
trim_trailing_whitespace = false
insert_final_newline = false
```

### Configurando os scripts no package.json

E agora vamos inserir dentro de scripts no package.json:

```js
    "lint": "prettier --check . && eslint --ignore-path .gitignore .",
    "lint-fix": "eslint --fix .",
    "format": "prettier --write './**/*.{js,jsx,ts,tsx,css,md,json}' --config ./.prettierrc"
```

E muito provavelmente ele ficará assim:

```js
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject",
    "lint": "prettier --check . && eslint --ignore-path .gitignore .",
    "lint-fix": "eslint --fix .",
    "format": "prettier --write './**/*.{js,jsx,ts,tsx,css,md,json}' --config ./.prettierrc"
  },
```

### Configurando Husky

Para configurar o Husky basta inserir o seguinte comando:

```sh
yarn husky install
```

Agora insira isto no seu package.json

```js
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged",
    }
  }

  "lint-staged": {
    "src/**/*.{js,jsx,ts,tsx,json,css,scss,md}": ["yarn lint", "yarn format"]
  }
```

### Configurando path mapping

### Configurando dockerfile e .dockerignore

Por último vamos configurar o Docker partindo do pressuposto que você já saiba o que é Docker, se não sabe não tem
problema, volte duas casas.

```bash

# Criando arquivo Dockerfile e .dockerignore
$ touch Dockerfile .dockerignore

```

No arquivo .dockerignore, insira:

```.dockerignore
node_modules/
build/
```

Após criar o Dockerfile vamos configura-lo:

```dockerfile

FROM node:15-alpine AS development

WORKDIR /app

ENV PORT=3000

ENV PATH /app/node_modules/.bin:$PATH

COPY package.json ./
COPY yarn.lock ./
RUN yarn install --silent
RUN yarn add global react-scripts@4.0.3 --silent

COPY . ./

FROM development AS builder

RUN yarn run build

FROM nginx:stable

COPY --from=builder /app/build /usr/share/nginx/html

```

### "Buildando" e rodando o projeto

Basicamente na configuração acima você está criando uma imagem do seu projeto para o container e servindo ela
na porta 3000 com o nginx.
É isso que você precisa saber até aqui, sem muitas firulas.

Depois disso você precisará gerar a build e depois rodar o projeto:

```bash

# Docker build
$ docker build -t meu-repo .

# Docker run
$ docker run -p 80:80 meu-repo

```
