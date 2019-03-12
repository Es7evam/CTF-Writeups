# Banana Shopping
Abrindo a página, nos deparamos com diversas caixas de texto, onde podemos ver que escolhemos frutas para comprar. <br>
Tentando colocar um número negativo de uma determinada fruta, percebemos que a página muda esse número para positivo antes de enviar a requisição. Isso pode ser verificado no código fonte da página.<br>
Então, utilizando alguma ferramenta, como por exemplo o BurpSuite, podemos interceptar o pacote de rede e modificá-lo, colocando um número negativo de uma determinada fruta, assim podendo por exemplo comprar 72 bananas e -36 uvas, com custo zero. <br>
Logo a flag vai ser disponibilizada na parte interior da página. <br>
Outro modo de fazer é enviando manualmente, através do comando `curl`, por terminal, a página, já com as requisições de frutas, tomando os parâmetros através da análise da aba Network do console do navegador.
```
curl -X POST -d "apple=0&banana=72&grape=-36&lemon=0&mango=0&watermelon=0" http://lab.shellterlabs.com:XXXXX
```
Onde XXXXX é a porta do seu lab do shellterlabs.
No final do arquivo HTML recebido vemos shellter{...}, a flag do desafio.
