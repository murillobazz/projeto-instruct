# 🌎 [CountryFinder](https://countryfinder-instruct.vercel.app/)

<small><i>(Este projeto foi feito por Murillo Bazilio, para participação no processo seletivo da Instruct.)</i></small>

## 📘 Descrição

  O 🌎 [CountryFinder](https://countryfinder-instruct.vercel.app/) foi uma solução pensada para o problema proposto no teste do processo seletivo da Instruct. O problema girava em torno de uma empresa que precisaria de uma aplicação para visualizar dados de diversos países que seriam captados à partir da [Countries GraphQL API](https://countries.trevorblades.com/graphql).

Por se tratar de uma aplicação de consulta, a mesma foi feita com baixo uso de dependências/pacotes externos, tentando alcançar um alto nível de performance.

<br>
  O projeto deveria contar com os seguintes requisitos:
  
- [x] Um filtro de continente, obrigatório, que determina quais países são listados;
- [x] Um filtro de países com mais de um idioma, opcional;
- [x] Um filtro por idioma, também opcional.

<br>

  Os requisitos foram alcançados, e pensando, também, em outros requisitos reais que uma aplicação como essa poderia ter, foram implementadas algumas funcionalidades extras, visando a aplicabilidade dessa implementação no mundo real:

- [x] Responsividade Mobile: essa opção foi adicionada por se tratar de um uso comum da maioria das aplicações web. No Brasil, mais de 60% da população utiliza algum dispositivo móvel primariamente para utilizar a internet;
- [x] Exportação de Dados: pensando na problemática apresentada no desafio, é bem possível que o usuário final optasse por salvar os dados filtrados para utilizá-los posteriormente, sem ter que precisar acessar a aplicação e adicionar os filtros toda vez. Pensando nisso, foi adicionado um botão de exportação dos dados (sob a filtragem aplicada pelo usuário), que faz o download de uma planilha desses mesmos dados no formato .csv. (Atualmente, o filtro de países com mais de uma língua não é aplicado nos dados exportados, por se tratar, atualmente, de um filtro meramente visual, porém essa correção está no roadmap da aplicação.)

<hr>

## 💾 Tecnologias

  A aplicação foi feita utilizando [Vue 3](https://vuejs.org/), que teve como ponto de partida o CLI do [Vite](https://vitejs.dev/). É executada sobre [Node.js](https://nodejs.org/en/). O único pacote importado durante o desenvolvimento foi o [Axios](https://axios-http.com/), para fazer as chamadas para a [Countries GraphQL API](https://countries.trevorblades.com/graphql).

  A aplicação está disponível através de deploy no [Vercel](https://vercel.com/), você pode acessar clicando aqui -> [CountryFinder](https://countryfinder-instruct.vercel.app/)
<br>

### Para rodar essa aplicação localmente (considerando que você tenha permissão de acesso):
```bash
# Clone este repositório
$ git clone <https://github.com/murillobazz/projeto-instruct>

# Acesse a pasta do projeto no terminal/cmd
$ cd projeto-instruct

# Instale as dependências
$ yarn install

# Execute a aplicação em modo de desenvolvimento
$ yarn dev

# O servidor inciará na porta:3000 - acesse <http://localhost:3000>
```
<hr>

## 👨‍💻 Como usar

  Ao acessar o site da aplicação você verá cards de todos países disponíveis através da API. Você pode utilizar o filtro de continentes para visualizar países de continentes específicos, o filtro de idiomas, para visualizar países que utilizam idiomas específicos e, também, poderá utilizar o filtro visual de países que falam mais de um idioma. Ao final de sua filtragem, você pode utilizar o botão "Download query" para fazer o download de uma planilha (.csv) contendo os dados dos países filtrados.
