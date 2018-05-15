# Forensic_xmas
Once again, Santa needs your help!

Many children sent him pictures. But the good old man ended up deleting all these photos.
What can you do for him?

See if you can identify the gifts in all photos. The more important photo is a toy that is inside a box, and its name is written on it. Can you figure out what is written in the box?

Flag format: shellter {nomenacaixa}

[Chall link](https://shellterlabs.com/en/questions/christmas-challenge-2016/forensic_xmas/)

## Solving it
We first download the file given at the challenge page. <br>
It is a .gz file, unzipping it we obtain a `forensic_xmas` file. <br>
Then, using `strings` we can se that there are lots of images inside the file.

![forensic_xmas](Forensic_xmas/forensic_xmas.png)

Then, extracting the images using `foremost forensic_xmas`. <br>
At the output folder, we can see this messed up image that seems to fit at the given description:

![forensic_xmas](Forensic_xmas/00020170.png)

At the top of it we can see the "Pop Movies" logo, then, searching for it, while it appears to be a Minion from Despicable Me movie we find out the his name is "king bob". <br>
Then, submitting `shellter{kingbob}` we obtain success.
