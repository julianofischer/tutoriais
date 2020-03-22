---
layout: post
title: Introdução a atividades (Android)
---

A classe __Activity__ é um componente crucial de um app para Android, e a maneira como as atividades são lançadas e reunidas é uma parte fundamental do modelo de aplicativo da plataforma. Diferentemente dos paradigmas de programação em que os apps são lançados com um método main(), o sistema Android inicia o código em uma instância __Activity__ invocando métodos de callback que correspondem a estágios específicos do ciclo de vida.

## O conceito de atividade

A experiência em apps para dispositivos móveis é diferente da versão para computador, porque a interação do usuário com o app nem sempre começa no mesmo lugar. Em vez disso, a jornada do usuário geralmente começa de forma não determinista. Por exemplo, se você abrir um app de e-mails na tela inicial, poderá ver uma lista de e-mails. Por outro lado, se você estiver usando um app de mídia social que inicialize seu app de e-mails, poderá ir diretamente para a tela do app de e-mails para escrever uma mensagem.

A classe __Activity__ foi desenvolvida para facilitar esse paradigma. Quando um app invoca outro, o app de chamada invoca uma atividade no outro, em vez do app como um todo. Dessa forma, a atividade serve como ponto de entrada para a interação de um app com o usuário. Você implementa uma atividade como uma subclasse da classe __Activity__.

Uma atividade fornece a janela na qual o app desenha a própria IU. Essa janela normalmente preenche a tela, mas pode ser menor do que a tela e flutuar sobre outras janelas. Geralmente, uma atividade implementa uma tela em um app. Por exemplo, uma das atividades de um app pode implementar uma tela Preferências, enquanto outra atividade implementa uma tela Selecionar foto.

A maioria dos apps contém várias telas, o que significa que elas abrangem várias atividades. Normalmente, uma atividade em um app é especificada como a atividade principal, que é a primeira tela a ser exibida quando o usuário inicia o app. Cada atividade pode iniciar outra para realizar ações diferentes. Por exemplo, a atividade principal em um app de e-mails simples pode fornecer a tela que mostra uma caixa de entrada de e-mail. A partir daí, a atividade principal pode iniciar outras atividades que fornecem telas para tarefas como escrever e-mails e abrir e-mails individuais.

Embora as atividades funcionem juntas para formar uma experiência do usuário coesa em um app, cada uma é vagamente vinculada às outras. Geralmente, há dependências mínimas entre as atividades em um app. Na verdade, com frequência elas iniciam atividades pertencentes a outros apps. Por exemplo, um app de navegação pode iniciar a atividade "Compartilhar" de um app de mídia social.

Para usar as atividades no seu app, é necessário registrar informações sobre elas no manifesto e você precisa gerenciar os ciclos de vida da atividade adequadamente. O restante deste documento introduz esses assuntos.

## Como configurar o Manifesto
Para que seu app possa usar as atividades, você precisa declará-las junto a alguns dos atributos delas no manifesto.

### Declarar atividades
Para declarar sua atividade, abra o arquivo de manifesto e adicione um elemento __<activity>__ como filho do elemento __<application>__. Exemplo:

```xml
    <manifest ... >
      <application ... >
          <activity android:name=".ExampleActivity" />
          ...
      </application ... >
      ...
    </manifest >
```

O único atributo obrigatório para esse elemento é __android:name__, que especifica o nome da classe da atividade. Você também pode adicionar atributos que definem características de atividade, como etiqueta, ícone ou tema da IU. Para mais informações sobre esses e outros atributos, consulte a documentação de referência do elemento __<activity>__.

---
**NOTA**

Observação: depois de publicar o app, não mude os nomes das atividades. Se fizer isso, você poderá corromper alguma funcionalidade, como atalhos de apps. <s>Para mais informações sobre as mudanças a serem evitadas após a publicação, consulte **Itens que não podem ser mudados (em inglês)**.</s>

---

## Declarar filtros de intent
**Filtros de intent** são um recurso muito poderoso da plataforma Android. Eles oferecem a capacidade de iniciar uma atividade com base não apenas em uma solicitação explícita, mas também implícita. Por exemplo, uma solicitação explícita pode dizer ao sistema para "Iniciar a atividade de envio de e-mail no app Gmail". Por outro lado, uma solicitação implícita diz ao sistema para "Iniciar uma tela de envio de e-mail em qualquer atividade que possa fazer o trabalho". Quando a IU do sistema pergunta a um usuário qual app usar na execução de uma tarefa, um filtro de intent está em ação.

Você pode aproveitar esse recurso declarando um atributo **<intent-filter>** no elemento **<activity>**. A definição desse elemento inclui um elemento <action> e, opcionalmente, um **<category>** e/ou um **<data>**. Esses elementos são combinados para especificar o tipo de intent ao qual sua atividade pode responder. Por exemplo, o seguinte snippet de código mostra como configurar uma atividade que envia dados de texto e recebe solicitações de outras atividades para fazer isso:

```xml
    <activity android:name=".ExampleActivity" android:icon="@drawable/app_icon">
        <intent-filter>
            <action android:name="android.intent.action.SEND" />
            <category android:name="android.intent.category.DEFAULT" />
            <data android:mimeType="text/plain" />
        </intent-filter>
    </activity>
```

Nesse exemplo, o elemento **<action>** especifica que essa atividade envia dados. Declarar o elemento **<category>** como **DEFAULT** permite que a atividade receba solicitações de inicialização. O elemento **<data>** especifica o tipo de dado que essa atividade pode enviar. O snippet de código a seguir mostra como chamar a atividade descrita acima:

```java
    // Create the text message with a string
    Intent sendIntent = new Intent();
    sendIntent.setAction(Intent.ACTION_SEND);
    sendIntent.setType("text/plain");
    sendIntent.putExtra(Intent.EXTRA_TEXT, textMessage);
    // Start the activity
    startActivity(sendIntent);
```

Se você pretende que seu app seja autossuficiente e não permita que outros apps ativem as atividades dele, você não precisa de outros filtros de intent. As atividades que você não quiser disponibilizar para outros apps não precisam ter filtros de intent, e você pode iniciá-las usando intents explícitos. <s>Para mais informações sobre como suas atividades podem responder aos intents, consulte Intents e filtros de intents.</s>

## Declarar permissões
Você pode usar a tag **<activity>** do manifesto para controlar quais apps podem iniciar uma atividade específica. Uma atividade mãe não pode iniciar uma atividade filha a menos que as duas tenham as mesmas permissões no manifesto. Se você declarar um elemento <uses-permission> para uma atividade mãe, cada atividade filha precisará ter um elemento **<uses-permission>** correspondente.

Por exemplo, se seu app quiser usar um app hipotético chamado SocialApp para compartilhar uma postagem em mídias sociais, o próprio SocialApp precisa definir a permissão que o app autor da chamada precisa ter:

```xml
    <manifest>
    <activity android:name="...."
       android:permission=”com.google.socialapp.permission.SHARE_POST”

    />
```

Em seguida, para poder chamar o SocialApp, seu app precisa corresponder à permissão definida no manifesto do SocialApp:

```xml
    <manifest>
       <uses-permission android:name="com.google.socialapp.permission.SHARE_POST" />
    </manifest>
```

<s>Para mais informações sobre permissões e segurança em geral, consulte Segurança e permissões</s>

## Como gerenciar o ciclo de vida da atividade

Ao longo da vida útil de uma atividade, ela passa por vários estados. Uma série de callbacks são usados para lidar com transições entre estados. As seções a seguir apresentam esses callbacks.

### onCreate()
Você precisa implementar esse callback, que é acionado quando o sistema cria sua atividade. A implementação inicializará os componentes essenciais da atividade. Por exemplo, aqui, o app criará visualizações e vinculará dados a listas. Mais importante, é nesse local que você precisa chamar **setContentView()** para definir o layout da interface do usuário da atividade.

Quando **onCreate()** termina, o próximo callback sempre é **onStart()**.

### onStart()
Quando **onCreate()** sai, a atividade entra no estado "Iniciado" e se torna visível para o usuário. Esse callback contém o que equivale aos preparativos finais da atividade para ir para o primeiro plano e se tornar interativa.

### onResume()
O sistema invoca esse callback imediatamente antes de a atividade começar a interagir com o usuário. Neste ponto, a atividade fica na parte superior da pilha de atividades e captura toda a entrada do usuário. A maior parte da funcionalidade principal de um app é implementada no método **onResume()**.

O callback **onPause()** sempre segue **onResume()**.

### onPause()
O sistema chama **onPause()** quando a atividade perde o foco e entra em um estado "Pausado". Esse estado ocorre quando, por exemplo, o usuário toca no botão "Voltar" ou "Recentes". Quando o sistema chama **onPause()** para sua atividade, isso significa, tecnicamente, que ela ainda está parcialmente visível. Porém, na maioria das vezes, é uma indicação de que o usuário está deixando a atividade e que logo ela entrará no estado "Interrompido" ou "Retomado".

Uma atividade no estado "Pausado" pode continuar atualizando a IU se o usuário estiver esperando por isso. Exemplos de uma dessas atividades incluem a exibição da tela de um mapa de navegação ou de um player de mídia sendo reproduzido. Mesmo que essas atividades percam o foco, o usuário espera que a IU continue sendo atualizada.

Não use **onPause()** para salvar dados do app ou do usuário, fazer chamadas de rede ou executar transações de banco de dados. Para mais informações sobre como salvar dados, consulte Como salvar e restaurar o estado da atividade.

Quando a execução de **onPause()** termina, o próximo callback é **onStop()** ou **onResume()**, dependendo do que acontece depois que a atividade entra no estado "Pausado".

### onStop()
O sistema chama **onStop()** quando a atividade não está mais visível para o usuário. Isso pode acontecer porque a atividade está sendo destruída, uma nova atividade está sendo iniciada ou uma atividade existente está entrando em um estado "Retomado" e está cobrindo a atividade interrompida. Em todos esses casos, a atividade interrompida não fica mais visível.

O próximo callback que o sistema chamar será **onRestart()**, se a atividade voltar a interagir com o usuário, ou **onDestroy()**, se essa atividade for completamente encerrada.

### onRestart()
O sistema invoca esse callback quando uma atividade no estado "Interrompido" está prestes a ser reiniciada. **onRestart()** restaura o estado da atividade a partir do momento em que ela foi interrompida.

Esse callback é sempre seguido por **onStart()**.

### onDestroy()
O sistema invoca esse callback antes de uma atividade ser destruída.

Esse é o último callback que a atividade recebe. **onDestroy()** normalmente é implementado para garantir que todos os recursos de uma atividade sejam liberados quando ela (ou o processo que a contém) for destruída.

Esta seção é apenas uma introdução a esse tema. Para ver um tratamento mais detalhado do ciclo de vida da atividade e dos callbacks dele, consulte Ciclo de vida da atividade.

[Publicação original Android Developers](https://developer.android.com/guide/components/activities/intro-activities?hl=pt_br#java)

---

Content and code samples on this page are subject to the licenses described in the [Content License](https://developer.android.com/license?hl=pt_br). Java is a registered trademark of Oracle and/or its affiliates

---
