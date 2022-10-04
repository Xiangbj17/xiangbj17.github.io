---
title: Eventually I Started Up My Personal Website
date: 2022-10-04 21:39:00 +0800
categories: [Blogging, Tutorial]
tags: [Blog]
render_with_liquid: false
---

For my first post in my personal website, I decided to <u>record the process of why and how I built it.</u>

If you're just interested in how to build a personal website, you can jump to the third part.

# (I) About me

I'm **xiangbj17**.

This is my email prefix when I am an undergraduate student, having very special meanings for me, so I use it  here as my nickname.

- I enrolled at Tsinghua Univ at the year 2017, major in **Material Science and Engineering** and monor in **Music Engineering and Technology**.

- Now I'm doing researches about **Deep Learning** at the Institute Of Semiconductors,CAS, mostly about <u>Dereverbeation, Music Generating and Speech Enhancement.</u>

- I love all the beautiful things, enjoy composing music and coding.

- I want to create something novel and helpful to people.

I'll keep on updating my story, my thoughts, and sharing the skills I have attained with a genuine heart.

# (II) Why a personal website / blog

> I started to do things with always the same reason --- **cool and challengeable**.
{: .prompt-tip }

I'm fascinated with that.

This is also why at the end of freshman year I decided to get a degree about music.

Actually the **thought of doing something to share my life** has haunted me for long,  but lazy as me, I never put that into action. Also, I have the fear of "what if my friends see my post and laugh at me beacause they think that's stupid", or "what if what I share is wrong and misleading". 

Having these doubts in mind, I hesitated each time when the thought of starting up a personal channel came to me.

This year, after reading the book  [*Show Your Work*](https://www.amazon.com/Show-Your-Work-Austin-Kleon/dp/076117897X)  by Austin Kleon (By the way I highly recommend this book to everybody who has the same thought like me. It's really encouraging.), I found all the problem I previously had is not problem at all. In fact, **the real issue is just taking action as fast as possible.**

<img src="https://m.media-amazon.com/images/P/076117897X.01._SCLZZZZZZZ_SX500_.jpg" alt="Show your work" style="zoom:50%;" />

<center>The Cover Of Show Your Work</center>



**<font size=4>So, this is WHY I am here, typing for my first post.</font>**



# (III) How to build a personal website

## 1. Get started with Jekyll

[Jekyll](https://jekyllrb.com/)   is a tool for translating simple markdown file into beautiful websites. With Jekyll themes already posted on Github, one can easily build a personal website. Mastering basic coding ability, using Jekyll and Github to host the personal website for free must be the best choise.

- In this step, you need to install Jekyll. Just type to your terminal:

  ```console
  gem install bundler jekyll
  ```

- If you failed and you are using **macOS**:

  - Never mind! I met with the same problem when I tried to install Jekyll.

  - Just follow the instruction in the [link](https://www.moncefbelyamani.com/the-definitive-guide-to-installing-ruby-gems-on-a-mac/) step by step, you will succeed installing.



## 2. Choose a theme you like

Jekyll themes are available [here](https://jekyllthemes.io/), and you can pick the one you like.

I'm using **Jekyll Chirpy Theme**, and I'll tell you how I used it. 

1. Get into the [link](https://github.com/cotes2020/jekyll-theme-chirpy) and fork this repository on Github, with a name <u>xxx.github.io</u> and make it public.

2. Clone codes to your computer, by 

   ```console
   cd <The place you want to put the codes>
   git init
   git remote add origin <http link>
   git pull
   git checkout master
   ```



## 3. Change the defaults

Just open the files on your computer, change the defaults to your own infomation.

- The things you need to change are most  in `_config.yml`.
- To add a new post, just create an .md file in `_posts` with a title of `yyyy-mm-dd-your-title`.



## 4. Host it on Github

1. Push your updated code to remote repository, by

   ```console
   git status
   git add .
   git commit -m "your change"
   git pull
   ```

2. Go back to the github repository, click  "Actions" on the upper row. 

   - You will see the workflows running.
   - Wait till it finish and give you a green arrow.

3. click "Settings" on the upper row and then choose "Pages" on the left column,

   - Changing the "Source" as Github Actions.
   - Then your website will be live at <u>xxx.github.io</u>. Enjoy it!

















