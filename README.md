<div align="center">

</div>

<h1 align="center">O GUIA DEFINITIVO PARA CONFIGURAR O REPOSITÓRIO</h1>

# Tabela de conteúdos

<!--ts-->

- [Tabela de conteúdo](#tabela-de-conteudo)
- [Abstract](#abstract)
- [Tecnologias](#Tecnologias)
- [Requisitos mínimos](#abstract)
- [O guia](#o-guia)
<!--te-->

## Abstract

Resolvi fazer esse guia pois tô cansado de toda vez que vou começar um projeto(não uso boilerplate) ter que ficar
garimpando todos os guias que conheço para deixar pronto, então farei tudo num único, espero que seja de ajuda a alguém!

### Tecnologias

- <img src="https://github.com/girordo/geticon/blob/master/logos/react.svg" alt="React" width="28px" height="28px"/> React
- [CRA](https://create-react-app.dev/)
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

Com o projeto rodando vamos configurar agora o ESLint, Prettier, EditorConfig e Husky:

```bash

# Instalando o eslint, prettier e plugins
$  yarn add eslint prettier eslint-plugin-react eslint-plugin-react-hooks eslint-config-prettier eslint-plugin-prettier eslint-plugin-jsx-a11y

# Criando arquivos ignore
$ touch .eslintignore .prettierignore

```

Dentro de ambos arquivos ignore insira:

node_modules/
build/

Por último vamos configurar o Docker partindo do pressuposto que você já saiba o que é Docker, se não sabe não tem
problema, volte duas casas.

```bash

# Criando arquivo Dockerfile
$ touch Dockerfile

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
