# Cult Meeting
Esse write-up será sobre sobre o desafio Cult Meeting do CTF Hack The Boo feito pelo Hack the Box para o Halloween.

## Sobre

Nesse desafio havia um arquivo zip para ser baixado e um docker que poderia ser ligado. O zip possuia apenas um executável (ELF) e no docker estava sendo executado esse mesmo programa, para se conectar bastava utilizar o netcat.

O programa ao ser executado imprimia uma mensagem requisitando por uma senha e aguardava o usuário inserí-la, como pode ser visto na Imagem 01. Se não fosse inserido a senha certa o programa imprimia uma mensagem e era interrompido.

![Imagem 01 - Execução do programa](pics/1.png)

**Imagem 01 - Execução do programa** 

## Resolução

Para identificar a senha utiliza-se o comando ltrace que intercepta e imprime todas as chamadas a bibliotecas feitas por um executável. Observando a Imagem 02, podemos ver que o programa lê a entrada do usuário utilizando a função fgets e depois chama a função strcmp para comparar a entrada com uma string. Essa string é a senha que deve ser digitada.

![Imagem 02 - Uso do ltrace](pics/2.png)

**Imagem 02 - ltrace**

Então ao executar o programa novamente mas dessa vez inserindo a senha correta é invocada uma shell, demonstrado na Imagem 03, porém o programa está rodando local então 

![Imagem 03 - Senha correta](pics/3.png)

**Imagem 03 - Senha correta**

