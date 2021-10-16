---
layout: post
title: latinoware 2021 - analyze (web challenge)
slug: latinoware_web
---

O 18º Congresso Latino-americano de Sofware Livre e Tecnologias Abertas aconteceu do dia 13 até 15 de outubro, contando com palestras sobre segurança da informação, desenvolvimento, etc. Além do evento, foi realizado um Capture the Flag com desafios de web, crypto e engenharia reversa.

---
### Analyze

O desafio **Analyze** era um problema da categoria de web que consistia em um *code review* de um sistema escrito em Node JS.

Acessando o desafio, apenas era mostrado uma imagem e dois links.

<p align="center">
    <img src="/assets/latino-ware-1.png">
</p>

O *endpoint* **/code** mostra o código da aplicação, para que fosse possível realizar uma análise e encontrar possíveis erros.

---
##### Código da aplicação

```
const express = require('express')
const path = require('path')
const fs = require('fs')
const { exit } = require('process')
const app = express()

require('dotenv').config()

const ANaLYzEANaLYzE = process.env.ANaLYzEANaLYzE

app.listen(9000, () => {
    console.log('[+] Analyze Up!')
});

app.get('/', (req, res) =>{
    res.sendFile(path.join(__dirname+'/html/index.html'))
});

app.get('/code', (req, res) => {
    fs.readFile('index.js', 'utf8', (err, content) => {
        res.setHeader('Content-Type', 'text/plain')
        res.send(content)
    });
});

app.get('/flag', (rQrrQr, rRrrRr) => {
    const sTrsTr = "AnAlYzE" 
    
    if(rQrrQr.query['xXxXxXx'] === undefined || rQrrQr.query['yYyYyYy'] === undefined){
        return rRrrRr.send('something wrong is not right with your analysis.')
    }   

    var xXxxXx = rQrrQr.query['xXxXxXx'].trim().substring(0,14)
    var yYyyYy = rQrrQr.query['yYyYyYy'].trim().substring(0,14)

    if(xXxxXx.length !== yYyyYy.length || xXxxXx === rQrrQr.query['yYyYyYy']){
        return rRrrRr.send('something wrong is not right with your analysis.')
    }

    const oBjObJx = JSON.parse(xXxxXx)
    const oBjObJy = JSON.parse(yYyyYy)

    if(oBjObJx['xXxxXx'] == oBjObJy['yYyYy']){
        return rRrrRr.send(ANaLYzEANaLYzE)
    } else {
        return rRrrRr.send('something wrong is not right with your analysis.')
    }
});
```
---
##### Análise do código


De cara já é possível notar que o *endpoint* **/flag** espera dois parâmetros: xXxXxXx e yYyYyYy. 

Existem três condições iniciais que podem causar o retorno de um erro:
- Algum dos parâmetros não forem passados;
- Conteúdo do parâmetro xXxXxXx ter tamanho diferente do conteúdo do parâmetro yYyYyYy;
- Conteúdo do parâmetro xXxXxXx for igual ao parâmetro yYyYyYy

Pelo uso da chamada de *JSON.parse*, é entendível que o conteúdo esperado para o parâmetro é um JSON. Por fim, é comparado o **valor** dos campos **xXxxXx** e **yYyYy**, se forem iguais, a flag será retornada para gente.

---
##### Exploit

Precisamos então enviar dois JSONs para a aplicação através dos parâmetros **xXxXxXx** e **yYyYyYy**. Sabemos que o código espera dois campos nos JSON passados, que são, **xXxxXx** e **yYyYy**.

Montando um *payload* inicial, chegamos na seguinte requisição:

```
/flag?xXxXxXx={"xXxxXx":1}&yYyYyYy={"yYyYy":1}
```

Porém, neste caso, o segundo JSON possui um caracter a menos que o primeiro, fazendo com que a mensagem de erro seja retornada, devido a segunda condição comentada acima. Sabendo disso, é necessário compensar esse caracter faltando, ficando com a seguinte requisição:

```
/flag?xXxXxXx={"xXxxXx":1}&yYyYyYy={"yYyYy":10}
```

Sabendo que apenas **os valores** são comparados no final, é possível encontrar dois valores de tipos diferentes, mas que irão retornar **true** no final. Lembrando que um dos valores deve possuir um caracter a mais que o outro, para passar no filtro comentado anteriormente.

Fazendo testes, é possível encontrar uma comparação que satisfaz nossas duas condições, que é comparar o inteiro **0** com a string vazia **""**.


<p align="center">
    <img src="/assets/latino-ware-2.png">
</p>

Dessa forma, o *payload* final ficou dessa forma:

```
/flag?xXxXxXx={"xXxxXx":0}&yYyYyYy={"yYyYy":""}
```

Realizando essa requisição, a *flag* **LW2021{m3m3_5ucc3s5fully_an4lyz3d}** é retornada.

---
### m0r3m3m35

Existia também uma v2 deste desafio, que era chamado de **m0r3m3m35**. O código dessa vez era o seguite: 

```
const express = require('express')
const path = require('path')
const fs = require('fs')
const { exit } = require('process')
const app = express()

require('dotenv').config()

const ANaLYzEANaLYzE = process.env.ANaLYzEANaLYzE

app.listen(9000, () => {
    console.log('[+] Analyze Up!')
});

app.get('/', (req, res) =>{
    res.sendFile(path.join(__dirname+'/html/index.html'))
});

app.get('/code', (req, res) => {
    fs.readFile('index.js', 'utf8', (err, content) => {
        res.setHeader('Content-Type', 'text/plain')
        res.send(content)
    });
});

app.get('/flag', (rQrrQr, rRrrRr) => {
    const sTrsTr = undefined
    
    if(rQrrQr.query['xXxXxXx'] === undefined || rQrrQr.query['yYyYyYy'] === undefined){
        return rRrrRr.send('something wrong is not right with your analysis.')
    }   

    var xXxxXx = rQrrQr.query['xXxXxXx'].trim().substring(0,14)
    var yYyyYy = rQrrQr.query['yYyYyYy'].trim().substring(0,14)

    if(xXxxXx.length !== yYyyYy.length || xXxxXx === yYyyYy){
        return rRrrRr.send('something wrong is not right with your analysis.')
    }

    const oBjObJx = JSON.parse(xXxxXx)
    const oBjObJy = JSON.parse(yYyyYy)

    if(oBjObJx['xXxxXx'] === sTrsTr){
        return rRrrRr.send('something wrong is not right with your analysis.')
    }

    if(oBjObJx['xXxxXx'] == oBjObJy['yYyYy']){
        return rRrrRr.send(ANaLYzEANaLYzE)
    } else {
        return rRrrRr.send('something wrong is not right with your analysis.')
    }
});
```

Basicamente era o mesmo, com uma comparação a mais, o campo **xXxxXx** do primeiro JSON **não pode ser do tipo undefined**. Porém, o *payload* criado no primeiro desafio é genérico e já serve para esse novo desafio.

Então, enviando o mesmo *payload* de antes, a *flag* **LW2021{wr0ng_m3m3_an4lyz3d}** é retornada. 
