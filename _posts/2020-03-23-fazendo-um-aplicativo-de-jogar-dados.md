---
layout: post
title: Fazendo um aplicativo de rolar dado (Android)
---

Neste projeto aprenderemos a fazer uma app que simula o comportamento de um dado. Ao clicar em um botão rolar, uma face do dado será aleatoriamente exibida para o usuário do app. 

Ao final do tutorial, seu app deverá se parecer com este:

<p align="center">![_config.yml]({{ site.baseurl }}/images/dice-2.png)</p>

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
        ```

2. ## Modificando o Layout

    1. Substitua o conteúdo de **activity_main.xml** para:

    ```xml
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    tools:context=".MainActivity">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World!" />
    </LinearLayout>
    ```
    2. Adicione o atributo ```android:orientation="vertical"``` a LinearLayout. Este atributo muda a orientação do posicionamento para vertical, ao invés de horizontal.
    3. Altere o texto do TextView para 1. Altere o tamanho da fonte com o atributo ```android:textSize="30sp"``` e centralize-o verticalmente e horizontalmente com o atributo ```android:layout_gravity="center_horizontal```"

    ```xml
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="1"
            android:textSize="30sp"
            android:layout_gravity="center_horizontal"
        />
    ```
    4. Adicione um Button
    ```xml
        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text:="Rolar"
            android:layout_gravity="center_horizontal"
        />
    ```
    5. Aceite a sugestão (Extract string resource) do Android Studio para o atributo ```android:text="Rolar"```. Esta sugestão extrairá o texto e o salvará em um xml com o nome **strings.xml** (res/values/strings.xml)
    
    6. O layout deve ficar assim:

    ```xml
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:context=".MainActivity"
        android:layout_gravity="center_vertical"
        android:orientation="vertical"
    >

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="1"
        android:textSize="30sp"
        android:layout_gravity="center_horizontal"
    />

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/rolar"
        android:layout_gravity="center_horizontal"
     />
    </LinearLayout>
    ```

    7. Abaixo está uma imagem de como deve ficar o layout do app até o momento:

    <p align="center">![_config.yml]({{ site.baseurl }}/images/dice-1.png)</p>

3. ## Conectando o botão
    
    1. Mude o id do botão para "**rolar_button**" 
    2. Adicione o atributo Button a classe **MainActivity**
        * ```private Button rollButton;```
    3. Vincule o layout com a classe, colocando a seguinte linha no método **onCreate**
        * ```rollButton =  (Button) findViewById(R.id.rolar_button);```
    4. Configure um **OnClickListener** para o botão. Este código exibirá a mensagem "Clicked" quando o botão for clicado. 
        * ```java
            rollButton.setOnClickListener(
                new View.OnClickListener() {
                    @Override
                    public void onClick(View v) {
                        Toast.makeText(getApplicationContext(), "Clicked",
                                Toast.LENGTH_LONG).show();
                    }
                }
            );
          ```
4. ## Trocar o texto do TextView
    
    1. Crie o atributo TextView na classe MainActivity e faça a vinculação
      ```java
      //declarando atributo
      private TextView textView;
      ...
      //dentro do método onClick
      textView = (TextView) findViewById(R.id.text_view);
      ```
    2. Implemente o método **rolarDado** na classe **MainActivity**
      ```java
        private String rolar_dado(){
           Random r = new Random();
           return String.valueOf(r.nextInt(6)+1);
        }
      ```
    3. Altere o método **onClick**
      ```java
        public void onClick(View v) {
            textView.setText(rolar_dado());
        }
      ```
5. ## Substituir por imagens de dados

    1. Faça o download das [imagens do dado](https://github.com/udacity/andfun-kotlin-dice-roller/raw/master/DiceImages.zip)
    2. Adicione as imagens no diretório drawable
    3. Substitua o **TextView** por um **ImageView** e configure-o com o _empty_dice_
      ```xml
      <ImageView
        android:id="@+id/dice_image"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center_horizontal"
        android:src="@drawable/empty_dice" />
      ```
    4. Modifique o método rolar_dado para retornar o id do image resource apropriado, conforme o número aleatório obtido

      ```java
      private int rolar_dado(){
        Random r = new Random();
        switch (r.nextInt(6)+1){
            case 1:
                return R.drawable.dice_1;
            case 2:
                return R.drawable.dice_2;
            case 3:
                return R.drawable.dice_3;
            case 4:
                return R.drawable.dice_4;
            case 5:
                return R.drawable.dice_5;
            case 6:
                return R.drawable.dice_6;
            default:
                return R.drawable.empty_dice;
        }
    }
    ```
    5. Modifique o método **onClick**

    ```java
      imageView.setImageResource(rolar_dado());
    ```
    6. Espero que tenham chegado ao objetivo:
    
    <p align="center">![_config.yml]({{ site.baseurl }}/images/dice-2.png)</p>

6. ## Habilitando o uso de imagens vetoriais
    1. Os arquivos de imagem do dado que você baixou são imagens vetoriais. Uma vantagem do uso de imagens vetoriais é que a mesma imagem pode ser aumentada/diminuída enquanto o tamanho e a qualidade da imagem é mantida. No entanto, até o momento, o Android converte as imagens vetoriais para bitmap, não aproveitando as vantagens desse tipo de arquivo. Mais imagens no seu app implica em um APK maior. APKs maiores estão mais suscetíveis a desinstação e cancelamento de download. Sem contar que usuários com dispositivos limitados em memória (e também plano de dados) podem ficar insatisfeitos. Abaixo aprenderemos a habilitar o uso de imagens vetoriais no Android. 
    2. Abra o arquivo build.gradle (Module:app) e adicionem ```vectorDrawables.useSupportLibrary = true``` ao bloco **defaultConfig**
    ```xml
       ...
        defaultConfig {
          applicationId "ads.vilhena.ifro.edu.diceroller"
          minSdkVersion 19
          targetSdkVersion 29
          versionCode 1
          versionName "1.0"
          testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
          vectorDrawables.useSupportLibrary = true
        }
      ...
    ```
     3. Clique em **Sync now** no canto superior direito e aguardem a sincronização.
     4. Adicione o namespace ```xmlns:app="http://schemas.android.com/apk/res-auto"``` a raíz do layout.
    ```xml
    ...
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:tools="http://schemas.android.com/tools"
      xmlns:app="http://schemas.android.com/apk/res-auto"
      android:layout_width="match_parent"         
      ...
     ```

     5. Use **app:srcCompat** na tag da imagem no arquivo do layout
     ```xml
     <ImageView
        android:id="@+id/dice_image"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center_horizontal"
        app:srcCompat="@drawable/empty_dice" />
     ```
     6. Pronto! Agora sua aplicação está fazendo uso apropriado das imagens vetoriais.

    ---
     **Atenção**    
     Imagens vetoriais são suportadas a partir da **versão 21** da API.

    ---

Com isso encerramos o tutorial. Espero que tenham gostado. Valeu!

---
Este tutorial foi baseado na lição _Build your first app_ do curso _Developing Android Apps with Kotlin_, disponibilizado pelo Google Inc. em Udacity.com