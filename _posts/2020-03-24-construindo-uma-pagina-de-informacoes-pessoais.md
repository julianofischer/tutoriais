---
layout: post
title: Construindo uma página de informações pessoais (Android)
---

Neste projeto aprenderemos a fazer uma app que nada mais é que uma página de informações pessoais (About Me). 

Ao final do tutorial, seu app deverá se parecer com este:

![_config.yml]({{ site.baseurl }}/images/dice-2.png)

Vamos lá!

1. ## Crie o projeto
    1. Primeiramente, abra o Android Studio. Crie um novo projeto (File > New > New Project).
    2. Na aba "Phone and Tablet" selecione Empty Activity
    3. Use a seguinte configuração

        ```
        Name: Dice Roller
        Package: ads.vilhena.ifro.edu.diceroller
        Project location: escolha onde vai salvar o projeto no seu computador
        Language: Java
        Minimum API: selecione a API 24

Com isso encerramos o tutorial. Espero que tenham gostado. Valeu!

---
Este tutorial foi baseado na lição _Layouts_ do curso _Developing Android Apps with Kotlin_, disponibilizado pelo Google Inc. em Udacity.com

1. # Crie o projeto About me
    1. Primeiramente, abra o Android Studio. Crie um novo projeto (File > New > New Project).
    2. Na aba "Phone and Tablet" selecione Empty Activity
    3. Use a seguinte configuração

        ```
        Name: AboutMe
        Package: ads.vilhena.ifro.edu.aboutme
        Project location: escolha onde vai salvar o projeto no seu computador
        Language: Java
        Minimum API: selecione a API 24
        ```
    4. Exclua o arquivo activity_main.xml (/res/layout/). Aprenderemos a criar um arquivo para layout do zero.
        1. Botão direito no arquivo > delete > ok > delete anyway. Se aparecer uma mensagem na parte de baixo avisando que o arquivo está sendo utilizado, clique em "Do refactor".

2. # Criando o arquivo de layout
    1. Botão direito na pasta res/layout, selecione new > layout resource file

    ```
    Resource name: activity_main.xml
    Root element: androidx.constraintlayout.widget.LinearLayout
    Source set: main
    Directory name: layout
    ```
    2. Clique em Ok

3. # Adicionando um TextView ao layout
    1. Na visão de design, arraste um TextView da paleta para o layout
    2. Mude o id para name_text
    3. Mude o texto para o seu nome
    4. Mude o textSize para 20sp
        1. SP é a abreviação de Scale Independent Pixel
            1. Corresponde a 1px para cada 160 pixels de densidade
            2. Em uma densidade de 480px, cada sp corresponde a 3px
    5. Mude textColor para android:color/black
    6. Mude textAlignment para center
    7. Haverá um warning "**Hardcoded Text**". Isso porque estamos usando uma string hardcoded para o nome ao invés o resource @string. Uma solução é sugerida ao final da mensagem. Clique em fix. Na tela que se abre, clique em OK.
    8. Clique em executar para ver o resultado. A sua app deve se parecer com a da imagem abaixo.

    ![_config.yml]({{ site.baseurl }}/images/aboutme-1.png)

    4. # Estilizando o TextView
        1. Adicione paddingTop 8dp
        2. Adicione layout_marginTop para 16dp
        3. Clique no atributo Font Family. Clique em more fonts. Procure por Roboto. Selecione Roboto > Regular.
        4. Você pode criar um estilo através do TextView que formatou para economizar tempo e criar elementos com visual regular.
            1. Na árvore de componentes, clique com o botão direito em text_name > refactor > extract style
            2. Desmarque layout_width, layout_height e text_alignment
            3. Nomeie styles
            4. Clique em Ok
            5. Um arquivo res/values/styles.xml foi criado. activity_main.xml passa a utilizá-lo no estilo do TextView

4. # Adicionando outros componentes
    1. Arraste um ImageView da paleta para baixo do TextView na árvore de componentes
    2. Na tela que se abre, na aba Drawable, procure por Android e selecione **btn_star_big_on**
    3. No xml, clique com o botão direito em cima do nome da figura (imageView), selecione refactor > rename, altere o nome para star_image e em seguida clique no botão refactor. Isso alterará o nome da componente em todo o projeto.
    4. Para resolver a mensagem de warning "**Image without contentDescription**", adicione uma descrição ao campo contentDescription. Essa string será utilizada por leitores de tela para descrever a imagem.
    5. Para separar a imagem do nome, altere layoutMarginTop para 16dp

5. # Adicionando um ScrollView
    1. Selecione um ScrollView na paleta e adicione abaixo da ImageView
    2. Adicionar o id bio_scroll
    3. Abra strings.xml e crie uma string bio. Coloque a sua bio no conteúdo.
    4. Adicione um TextView ao ScrollView e mude o id para bio_text
    5. Selecion bio_text, procure pelo campo text, clique no botão e uma tela para selecionar um recurso abrirá. Procure por bio e selecione o recurso que criou no passo 5.3
    6. É melhor que o texto não ocupe toda a tela, de borda a borda. Uma forma de solucionar este problema é adicionar padding ao LinearLayout ou ao ScrollView. Adicione o padding que achar necessário.
    7. Adicione algum espaçamento entre as linhas no texto: ```android:lineSpacingMultiplier="1.2"```

6. # Adicionando um EditText
