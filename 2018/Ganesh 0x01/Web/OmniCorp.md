Write up SHX 3 (PHP Security) - Omni Corp
=========================================

**Retirado de [Write up SHX 3 (PHP Security) - Omni Corp](https://hiagohubert.github.io/blog/php-write-up-shx-3-1/) por [Hiago Hubert](https://hiagohubert.github.io/contact/)**  
15 de Fevereiro de 2017 | 15 Minutos de leitura

Para quem não está familiarizado com competições de segurança da informação no estilo CTF (Capture The Flag), um “Write Up” é uma descrição de uma solução para um desafio de um CTF especifico. Caso nunca tenha ouvido falar de CTF, você pode começar por esse [link][6].

Este Write Up é de um challenge do [Shellter Labs][7] que é uma rede social focada no aprendizado de segurança da informação e que periodicamente lança um conjunto de challenges com um assunto especifico, e nesta semana lançaram com o assunto de Segurança com PHP.

Descrição do problema:

> “We were able to find a piece of this website code on the internet. We hope it help us to find a vulnerability that show us the secret code.” (Fomos capazes de encontrar um pedaço deste código do site na internet. Esperamos que nos ajude a encontrar uma vulnerabilidade que nos mostre o código secreto)

Junto ao problema, é fornecido o seguinte código-fonte:
```PHP
<?php
	include('includes/flag.php');

	session_start();	
?>
```
```HTML
<!DOCTYPE html>
<html >
<head>
  <meta charset="UTF-8">
  <title>Omni Cosimer Corp.</title>
  
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/normalize/5.0.0/normalize.min.css">

  
      <style>
      /* NOTE: The styles were added inline because Prefixfree needs access to your styles and they must be inlined if they are on local disk! */
      @import url(http://fonts.googleapis.com/css?family=Open+Sans);
.btn { display: inline-block; *display: inline; *zoom: 1; padding: 4px 10px 4px; margin-bottom: 0; font-size: 13px; line-height: 18px; color: #333333; text-align: center;text-shadow: 0 1px 1px rgba(255, 255, 255, 0.75); vertical-align: middle; background-color: #f5f5f5; background-image: -moz-linear-gradient(top, #ffffff, #e6e6e6); background-image: -ms-linear-gradient(top, #ffffff, #e6e6e6); background-image: -webkit-gradient(linear, 0 0, 0 100%, from(#ffffff), to(#e6e6e6)); background-image: -webkit-linear-gradient(top, #ffffff, #e6e6e6); background-image: -o-linear-gradient(top, #ffffff, #e6e6e6); background-image: linear-gradient(top, #ffffff, #e6e6e6); background-repeat: repeat-x; filter: progid:dximagetransform.microsoft.gradient(startColorstr=#ffffff, endColorstr=#e6e6e6, GradientType=0); border-color: #e6e6e6 #e6e6e6 #e6e6e6; border-color: rgba(0, 0, 0, 0.1) rgba(0, 0, 0, 0.1) rgba(0, 0, 0, 0.25); border: 1px solid #e6e6e6; -webkit-border-radius: 4px; -moz-border-radius: 4px; border-radius: 4px; -webkit-box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.2), 0 1px 2px rgba(0, 0, 0, 0.05); -moz-box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.2), 0 1px 2px rgba(0, 0, 0, 0.05); box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.2), 0 1px 2px rgba(0, 0, 0, 0.05); cursor: pointer; *margin-left: .3em; }
.btn:hover, .btn:active, .btn.active, .btn.disabled, .btn[disabled] { background-color: #e6e6e6; }
.btn-large { padding: 9px 14px; font-size: 15px; line-height: normal; -webkit-border-radius: 5px; -moz-border-radius: 5px; border-radius: 5px; }
.btn:hover { color: #333333; text-decoration: none; background-color: #e6e6e6; background-position: 0 -15px; -webkit-transition: background-position 0.1s linear; -moz-transition: background-position 0.1s linear; -ms-transition: background-position 0.1s linear; -o-transition: background-position 0.1s linear; transition: background-position 0.1s linear; }
.btn-primary, .btn-primary:hover { text-shadow: 0 -1px 0 rgba(0, 0, 0, 0.25); color: #ffffff; }
.btn-primary.active { color: rgba(255, 255, 255, 0.75); }
.btn-primary { background-color: #4a77d4; background-image: -moz-linear-gradient(top, #6eb6de, #4a77d4); background-image: -ms-linear-gradient(top, #6eb6de, #4a77d4); background-image: -webkit-gradient(linear, 0 0, 0 100%, from(#6eb6de), to(#4a77d4)); background-image: -webkit-linear-gradient(top, #6eb6de, #4a77d4); background-image: -o-linear-gradient(top, #6eb6de, #4a77d4); background-image: linear-gradient(top, #6eb6de, #4a77d4); background-repeat: repeat-x; filter: progid:dximagetransform.microsoft.gradient(startColorstr=#6eb6de, endColorstr=#4a77d4, GradientType=0);  border: 1px solid #3762bc; text-shadow: 1px 1px 1px rgba(0,0,0,0.4); box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.2), 0 1px 2px rgba(0, 0, 0, 0.5); }
.btn-primary:hover, .btn-primary:active, .btn-primary.active, .btn-primary.disabled, .btn-primary[disabled] { filter: none; background-color: #4a77d4; }
.btn-block { width: 100%; display:block; }

* { -webkit-box-sizing:border-box; -moz-box-sizing:border-box; -ms-box-sizing:border-box; -o-box-sizing:border-box; box-sizing:border-box; }

html { width: 100%; height:100%; overflow:hidden; }

body { 
	width: 100%;
	height:100%;
	font-family: 'Open Sans', sans-serif;
	background: #092756;
	background: -moz-radial-gradient(0% 100%, ellipse cover, rgba(104,128,138,.4) 10%,rgba(138,114,76,0) 40%),-moz-linear-gradient(top,  rgba(57,173,219,.25) 0%, rgba(42,60,87,.4) 100%), -moz-linear-gradient(-45deg,  #670d10 0%, #092756 100%);
	background: -webkit-radial-gradient(0% 100%, ellipse cover, rgba(104,128,138,.4) 10%,rgba(138,114,76,0) 40%), -webkit-linear-gradient(top,  rgba(57,173,219,.25) 0%,rgba(42,60,87,.4) 100%), -webkit-linear-gradient(-45deg,  #670d10 0%,#092756 100%);
	background: -o-radial-gradient(0% 100%, ellipse cover, rgba(104,128,138,.4) 10%,rgba(138,114,76,0) 40%), -o-linear-gradient(top,  rgba(57,173,219,.25) 0%,rgba(42,60,87,.4) 100%), -o-linear-gradient(-45deg,  #670d10 0%,#092756 100%);
	background: -ms-radial-gradient(0% 100%, ellipse cover, rgba(104,128,138,.4) 10%,rgba(138,114,76,0) 40%), -ms-linear-gradient(top,  rgba(57,173,219,.25) 0%,rgba(42,60,87,.4) 100%), -ms-linear-gradient(-45deg,  #670d10 0%,#092756 100%);
	background: -webkit-radial-gradient(0% 100%, ellipse cover, rgba(104,128,138,.4) 10%,rgba(138,114,76,0) 40%), linear-gradient(to bottom,  rgba(57,173,219,.25) 0%,rgba(42,60,87,.4) 100%), linear-gradient(135deg,  #670d10 0%,#092756 100%);
	filter: progid:DXImageTransform.Microsoft.gradient( startColorstr='#3E1D6D', endColorstr='#092756',GradientType=1 );
}
.login { 
	position: absolute;
	top: 50%;
	left: 50%;
	margin: -150px 0 0 -150px;
	width:300px;
	height:300px;
}
.login h1 { color: #fff; text-shadow: 0 0 10px rgba(0,0,0,0.3); letter-spacing:1px; text-align:center; }
.login h3 { color: #fff; text-shadow: 0 0 10px rgba(1,0,0,0.3); letter-spacing:1px; text-align:center; }
.login a { color: #fff; font-size: 10px} 

.head {
	position: absolute;
	top: 30%;
	left: 43%;
	margin: -150px 0 0 -150px;
	width:500px;
	height:500px;
}
.head h1 { color: #f8a; text-shadow: 0 0 10px rgba(0,0,0,0.3); letter-spacing:1px; text-align:center; }

input { 
	width: 100%; 
	margin-bottom: 10px; 
	background: rgba(0,0,0,0.3);
	border: none;
	outline: none;
	padding: 10px;
	font-size: 13px;
	color: #fff;
	text-shadow: 1px 1px 1px rgba(0,0,0,0.3);
	border: 1px solid rgba(0,0,0,0.3);
	border-radius: 4px;
	box-shadow: inset 0 -5px 45px rgba(100,100,100,0.2), 0 1px 1px rgba(255,255,255,0.2);
	-webkit-transition: box-shadow .5s ease;
	-moz-transition: box-shadow .5s ease;
	-o-transition: box-shadow .5s ease;
	-ms-transition: box-shadow .5s ease;
	transition: box-shadow .5s ease;
}
input:focus { box-shadow: inset 0 -5px 45px rgba(100,100,100,0.4), 0 1px 1px rgba(255,255,255,0.2); }

    </style>

  <script src="https://cdnjs.cloudflare.com/ajax/libs/prefixfree/1.0.7/prefixfree.min.js"></script>

</head>

<body>
  <div class="head">
  <h1>OMNI COSIMER CORP.</h1>
  </div>
  <?php
	// Check if user is authenticated
	if( (isset($_SESSION['username']) == true)){
		// Get user data
		$id = $_SESSION['id'];

		// Check if user logged user is admin
		if ($id == 0){ 
			echo $FLAG;
			$username = 'Admin';
		}
		else{
			$username = $_SESSION['username']; 
		}

		?>
		  <div class="login">
			<h1>Welcome, <?php echo $username; ?></h1>
			<h3><a href="logout.php">Logout</a></h3>
		  </div>
	<?php
	}
	
	else{
		include 'includes/form.html';
	}
  ?>

</body>
</html>
```    

Também é oferecido um link para o site a ser explorado, e ao acessar o site, vemos isto:

![Fig1. - Pagina inicial](https://hiagohubert.github.io/assets/img/omni_corp_screenshot1.png)
Fig1. - Pagina inicial

### Solução

Como nos CTFs o que interessa são as Flags, é possível ver no código fornecido que a parte que nos interessa para ser executada é essa:

```PHP
 <?php
	// Check if user is authenticated
	if( (isset($_SESSION['username']) == true)){
		// Get user data
		$id = $_SESSION['id'];

		// Check if user logged user is admin
		if ($id == 0){ 
			echo $FLAG;
			$username = 'Admin';
		}
```

Tendo em mente que o código do formulário de login é este:
```HTML
    <form method="post" action="login.php" name="login">
    	
        <input name="username" placeholder="Username" id="username" required="required" type="text">
    	
        <input name="passwd" id="passwd" placeholder="Password" required="required" type="password">
            
        <button type="submit" class="btn btn-primary btn-block btn-large">Let me in.</button>
    	
        <input name="id" id="id" value="1" type="hidden">
    </form>
```

É natural que a primeira tentativa seja alterar o value do input “id” para 0, visto que é essa a comparação feita no código php, entretanto, ao fazer isto é retornado o seguinte erro:

![Fig2. - id = 0](https://hiagohubert.github.io/assets/img/omni_corp_screenshot2.png)  
Fig2. - id = 0

Então, percebi que a vulnerabilidade trata-se de um erro comum cometido por programadores em linguagens de tipagem dinâmica como o PHP, que é não usar o “===” que além de comparar o conteúdo da variavel, compara o tipo. Para resolver explorar a falha, alterei o value do campo id para “false”:

![Fig3. - Alterando value de campo id através de "Ferramentas de desenvolvedor" do Google Chrome](https://hiagohubert.github.io/assets/img/omni_corp_screenshot3.png)  
Fig3. - Alterando value de campo id através de "Ferramentas de desenvolvedor" do Google Chrome

Após submeter o formulário, conseguimos a flag:

![Fig3. - Flag: shellter{lookout\_for\_php\_typ3\_juggling!}](https://hiagohubert.github.io/assets/img/omni_corp_screenshot4.png)
Fig3. - Flag: shellter{lookout\_for\_php\_typ3\_juggling!}

Em PHP, as comparações `false == 0` e `true == 1` retornam `true`, então é preciso muito cuidado ao desenvolver.

[1]: /
[2]: /index.html
[3]: /contact
[4]: /blog/php-write-up-shx-3-1-en/
[5]: /blog/php-write-up-shx-3-1/
[6]: https://ctf-br.org/sobre/
[7]: https://shellterlabs.com/
