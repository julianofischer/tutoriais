---
layout: post
title: Fazendo um aplicativo de rolar dado
---

Neste projeto aprenderemos a fazer uma app que simula o comportamento de um dado. Ao clicar em um botão rolar, uma face do dado será aleatoriamente exibida para o usuário do app. Vamos lá!

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
    
    O layout deve ficar assim:
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

    ![_config.yml]({{ site.baseurl }}/images/dice-1.png)

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
