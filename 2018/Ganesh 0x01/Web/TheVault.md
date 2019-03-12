# The Vault

Logo ao abrir a página dada na descrição do challenge, encontramos: <br>
`Somente o DUNGEON_MASTER pode entrar no cofre` <br>

Então, ao tentar utilizar o login DUNGEON_MASTER e uma senha qualquer, não obtemos sucesso. <br>
Porém, se fizermos isso observando, no console do navegador (Hotkey F12 no Firefox), na aba de Network/Redes, podemos ver o método POST enviado pelo site. Na resposta dele vemos:
```
test_hash [f823475459e4f567bb4a3f6dc44e40c98602a9c33824e7bf5c7c66f5ecf36265] does not match real_hash[40f5d109272941b79fdf078a0e41477227a9b4047ca068fff6566104302169ce]
```
Perceba que o "test_hash" é uma criptografia da hash que você enviou, enquanto real_hash é a hash que da senha correta. <br>
Utilizando uma ferramenta de descriptografia online (por exemplo [reverse-hash](https://hashtoolkit.com/reverse-hash/)) e testando manualmente, ou percebendo que essa hash é um SHA256, obtemos a senha do site. <br>
Logando nele, uma janela aparece com a flag.
