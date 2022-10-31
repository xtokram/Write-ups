# Capture The Flag - Menina de CyberSec <br>
### Segundo CTF de OSINT - Servidor Menina de CyberSec :dancer:
 
CTF (Capture The Flag) realizado entre os dias 29 e 31 de Outubro, no servidor Menina de CyberSec.
<br>
<br>
Agradecimentos especiais para a Sabrina, idealizadora do servidor e do CTF em questão. <br>
Instagram: @meninadecybersec
<br><br>

** Todos os desafios estão na pasta /pics 

# 2º CTF de OSINT - Menina de CyberSec

### Resumo
Este CTF aborda conceitos como OSINT, Esteganografia e Geolicalização. <br> Ideal para todos que desejam aprender sobre diferentes técnicas de Information Gathering, que são fundamentais nas stacks de um profissional de segurança cibernética.
<br><br>
## Foothold 
<br>
Para começar, acessamos o link (já expirado) para encontrar a página inicial do CTF, onde encontramos os desafios.
<br><br>

# Desafios


![estanografia](pics/dashboard.png) 


O primeiro desafio a ser completado é o de esteganografia, pois nele temos uma imagem disponibilizada que será nosso ponto de partida.
<br><br>

## Desafio: Best Place on Earth - Fácil
    Quem tirou essa foto?
    
### Imagem em anexo:

![estanografia](pics/bestplaceonearth.png) 
<br><br>

### Metados interessantes dentro da imagem

![estanografia](pics/metadados.png) 

Utilizando a ferramenta exif.tools nota-se um nome, um tanto quanto único, dentro dos metadados desta imagem. <br> Nossa abordagem será procurar por este nome em redes sociais para obter maiores informações. <br> Por se tratar de um CTF, é muito provável que o perfil seja novo e de certa forma, genérico. 
<br><br>
Flag Best Place on Earth - Esteganografia: MCS{GiudittaDeRose}

<br><br>

## Desafio: Username - Fácil

    Qual o nome de usuário encontrado?
![estanografia](pics/twitter.png) 

Podemos observar, conforme dito anteriormente, que temos um perfil novo e com o nome que encontramos na imagem, também é possível notar no feed do perfil que a pessoa em questão gosta de jogar golf. <br> O que faz conexão com a imagem anexada no primeiro desafio.

Flag Username - OSINT: MCS{DeroseGiu}
<br><br>

## Desafio: Personal Data - Fácil
    Qual a profissão desse usuário?

Analisando o feed e a bio do Twitter "Nursing for love" (Enfermagem por amor) podemos concluir que a profissão do alvo é enfermagem.

Flag Personal Data - OSINT: MCS{enfermeira} ou MCS{nurse}

<br><br>

## Desafio: Personal Data 2 - Fácil
    De qual país esse usuário é?

Analisando a bio do perfil no Twitter, também notamos que o alvo é nascido na Itália, porém está morando nos Estados Unidos.

Flag Personal Data 2 - OSINT: MCS{United States}

<br><br>




## Desafio: Hobby - Fácil
    Qual o hobby preferido desse usuário?

Ao analisar o feed, bem como a imagem em anexo ao primeiro desafio, notamos que o hobby do alvo é Golf.

![estanografia](pics/hobby.png) 

Flag Hobby - OSINT: MCS{golf}

<br><br>


## Desafio: Cool Website - Fácil
    Qual website foi encontrado durante sua busca?


![estanografia](pics/coolwebsitebro.png) 
<br>


Flag Cool Website - OSINT: MCS{www.bahrainride.com}

<br><br>

## Desafio: Cool Website 2 - Fácil
    Esse site está em funcionamento?

Ao tentar conectar no website, notamos que este está fora do ar, o que seria nossa resposta para esta flag.

![estanografia](pics/cantconnect.png) 

Flag Cool Website 2 - OSINT: MCS{não}

<br><br>

## Desafio: Cool Website 3 - Hard
    Qual o título em destaque nesse website?

Nosso próximo desafio "Cool Website 3" nos pergunta sobre um título em destaque do site, porém este está fora do ar.<br>
Podemos então tentar acessar o site Wayback Machine (archive.org) para tentar resgatar versões antigas deste website. <br>
No Wayback Machine, encontramos uma versão do site de Fevereiro de 2011, com o seguinte título. 

<br><br>
### Print do Wayback Machine:
![estanografia](pics/waybackmachine.png) 
<br><br>
### Print da versão de 2011 do WebSite bahrainride.com
![estanografia](pics/titulo.png) 

Flag Cool Website 3 - OSINT: MCS{free people search engine}

<br><br>

## Desafio: Hobby 2 - Medium

    Qual o nome do local em que esse usuário pratica seu hobby?

Analisando novamente a seguinte imagem:

![estanografia](pics/bestplaceonearth.png)

De forma simples, podemos pesquisar por esta imagem em sites de busca, evitando a necessidade de buscar por campos de golf na região do alvo.
<br>
O website duplichecker.com busca por imagens em diferentes fontes na internet, fazendo o upload de uma foto, ele busca em vários sites e algoritmos diferentes de busca por imagens similares.
<br> 
Selecionando o Bing, obtemos a localização do campo de golf já no primeiro resultado.

### Resultado bing:

![estanografia](pics/pinemeadow.png)

Flag Hobby 2 - OSINT & Geolocation: MCS{pine meadow golf course}

<br><br>

## Desafio: Personal Data 3 - Medium

    Qual o nome da rua em que esse usuário mora ?

Neste desafio podemos acessar a localização do clube de golf através do google maps, e tentar obter mais informações.

<br>

Acessando o Twitter do alvo, temos um post muito interessante.

## Print do post:

![estanografia](pics/postwitter.png)
<br><br>
"Que privilégio, tenho o melhor lugar do mundo á 03 minutos de distância de casa. Posso pedir mais que isso? Acho que não!"
<br><br>
Concluímos que o alvo mora próximo ao campo de golf, onde ela pratica seu hobby favorito.
<br>
Utilizando o google maps para investir as ruas próximas ao campo de golf, encontramos a seguinte rua.
<br><br>
## Print da rua encontrada:

![estanografia](pics/cloverstreet.png)
<br><br>
EUREKA! Exatamente a mesma rua do post do alvo! Clover Dr.
<br><br>
Flag Personal Data 3 - OSINT & Geolocation: MCS{cloverdr}

<br><br><br>

# Agradecimentos

Novamente, agradecemos a Sabrina por criar este CTF e também a todos que auxiliaram neste processo. <br>

<br>
