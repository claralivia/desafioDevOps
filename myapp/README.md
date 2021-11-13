# Criando a aplicação React 

**Você precisará ter o Node 14.0.0 ou uma versão posterior em sua máquina de desenvolvimento local** (mas não é obrigatório no servidor). Recomendo usar a versão mais recente do LTS. Você pode usar nvm (macOS / Linux) ou nvm-windows para alternar as versões do Node entre diferentes projetos.

Para criar um novo aplicativo, você pode escolher um dos seguintes métodos:

### npx

```sh
npx create-react-app myapp
```

_([npx](https://medium.com/@maybekatz/introducing-npx-an-npm-package-runner-55f7d4bd282b) é uma ferramenta de execução de pacote que vem com o npm 5.2+ e superior, consulte [instruções para versões anteriores do npm](https://gist.github.com/gaearon/4064d3c23a77c74a3614c498a8bb1c5f))_

### npm

```sh
npm init react-app myapp
```

_`npm init <initializer>` está disponível no npm 6+_

### Yarn

```sh
yarn create react-app myapp
```

_[`yarn create <starter-kit-package>`](https://yarnpkg.com/lang/en/docs/cli/create/) está disponível no Yarn 0.25+_

Ele criará um diretório chamado `myapp` dentro da pasta atual. <br>
Dentro desse diretório, ele irá gerar a estrutura inicial do projeto e instalar as dependências transitivas:

```
myapp
├── README.md
├── node_modules
├── package.json
├── .gitignore
├── public
│   ├── favicon.ico
│   ├── index.html
│   └── manifest.json
└── src
    ├── App.css
    ├── App.js
    ├── App.test.js
    ├── index.css
    ├── index.js
    ├── logo.svg
    └── serviceWorker.js
    └── setupTests.js
```
Nenhuma configuração ou estruturas de pastas que sejam complicadas, apenas os arquivos que você precisará para criar o seu aplicativo. <br>
Assim que a instalação for concluída, você pode abrir a pasta do seu projeto dando o segunte comando:

```sh
cd myapp
```

# Dockerizando a aplicação React

### Dockerfile
Crie um arquivo `Dockerfile` na raiz do projeto com as seguintes instruções dentro dele:

```
FROM node:13-alpine as build

WORKDIR /app
ENV PATH /app/node_modules/.bin:$PATH

COPY package.json ./

RUN npm install --silent
RUN npm install react-scripts@3.4.1 -g --silent

COPY . ./
EXPOSE 4000
RUN npm run build

# production environment
FROM nginx:1.16.0-alpine

COPY --from=build /app/build/ /usr/share/nginx/html
COPY nginx/nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80
CMD [“nginx”, “-g”, “daemon off;”]
```

Certifique-se de usar o NGINX  para permitir o acesso através da porta 80 direcionando para a porta do container.

### .env
Crie um arquivo de configuração `.env` na raiz do projeto e expecifique, dentro dele, a porta que desejar:

```
PORT=4000
```

### .dockerignore
Crie um arquivo `.dockerignore` na raiz do projeto e adicione nele:

```
node_modules
build
.dockerignore
Dockerfile
```

### nginx
Crie uma pasta `nginx` na raiz do projeto e adicione o arquivo `nginx.conf`, ficará assim:

```
myapp
└── nginx
    └── nginx.conf
```

Coloque as seguintes instruções nele:
```
server {

  listen 80;

  location / {
    root   /usr/share/nginx/html;
    index  index.html index.htm;
    try_files $uri $uri/ /index.html;
  }

  error_page   500 502 503 504  /50x.html;

  location = /50x.html {
    root   /usr/share/nginx/html;
  }

}
```

### docker-compose
Crie um arquivo `docker-compose.yml` na raiz do projeto e dê as seguintes expecificações, de acordo com a porta usada por você e o nome do container escolhido:

```
version: '3.7'
services:
    app:
        container_name: meu-app
        build:
            context: .
            dockerfile: Dockerfile
        ports:
            - '4000:80'
```

### Comando `build`
Execute o seguinte comando para construir a imagem Docker a partir do docker-compose:

```
docker-compose up -d --build
```

# Iniciando
Dentro do projeto recém-criado, você dá um dos seguntes comando:

### npm

```
npm start
```

### Yarn

```
yarn start
```

A partir desse ponto, você poderá acessar a aplicação em [http://localhost:4000](http://localhost:4000) no seu navegador. <br>
Lembrando que a porta indicada deve ser a exposta no projeto.


# Saiba mais
Você pode saber mais em [Create React App documentation](https://facebook.github.io/create-react-app/docs/getting-started).




