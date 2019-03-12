# Show Me You Code!
Abrindo o challenge, percebemos um código que testa se a sua string é igual a uma determinada string que foi criptografada. <br>
Para isso, ele recebe a sua string e depois descriptografa a utilizada para ver se é igual. <br>
Então, vendo o código com mais calma, percebemos que ele usa a string `xx` e depois a descriptografa em base64. <br>
Podemos então apagar quase todo o código, restando as linhas:

```python
xx = ''
z = [89, 86, 69, 121, 100, 82,69, 103, 98, 47, 48, 103, 80, 71, 77, 121]
for zz in z:
    xx += chr(zz + 275 * 100001 - 275 * 1000 - 27225274)
```

Colocando `print xx` ao final dessas linhas e então descriptografando, em base 64, o obtido no print, temos a flag. Coloque no formato ganesh{} e submeta.
