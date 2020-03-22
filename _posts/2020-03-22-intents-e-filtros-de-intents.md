---
layout: post
title: Intents e filtros de intents (Android)
---

O **Intent** é um objeto de mensagem que pode ser usado para solicitar uma ação de outro componente do aplicativo. Embora os intents facilitem a comunicação entre os componentes de diversas formas, há três casos fundamentais de uso:

- Como iniciar uma atividade
Uma Activity representa uma única tela em um aplicativo. É possível iniciar uma nova instância de uma Activity passando um **Intent** para startActivity(). O **Intent** descreve a atividade a iniciar e carrega todos os dados necessários.

Se você quiser receber um resultado da atividade quando ela finalizar, chame startActivityForResult(). Sua atividade recebe o resultado como um objeto **Intent** separado no callback de onActivityResult() da atividade. Para saber mais, consulte o guia Activities.

- Como iniciar um serviço
O Service é um componente que realiza operações em segundo plano sem interface do usuário. Com o Android 5.0 (API nível 21) e posteriores, é possível iniciar um serviço JobScheduler. Para saber mais sobre JobScheduler, consulte a API-reference documentation.

Para versões anteriores ao Android 5.0 (API nível 21), é possível iniciar um serviço usando os métodos da classe Service. É possível iniciar um serviço para realizar uma operação que acontece uma vez (como fazer o download de um arquivo) passando um **Intent** a startService(). O **Intent** descreve o serviço a iniciar e carrega todos os dados necessários.

Se o serviço for projetado com uma interface servidor-cliente, é possível vincular ao serviço em outro componente passando um **Intent** a bindService(). Para mais informações, consulte o guia Serviços.

- Como fornecer uma transmissão
Transmissão é uma mensagem que qualquer aplicativo pode receber. O sistema fornece diversas transmissões para eventos do sistema, como quando o sistema inicializa ou o dispositivo inicia o carregamento. Você pode fornecer uma transmissão a outros aplicativos passando um **Intent** ao sendBroadcast() ou ao sendOrderedBroadcast().

O restante desta página explica como os intents funcionam e como usá-los. Para informações relacionadas, consulte Interação com outros aplicativos e Compartilhamento de conteúdo.

## Tipos de Intents
---
Há dois tipos de intents:

- Os **intents explícitos** especificam qual aplicativo atenderá ao intent, fornecendo o nome do pacote do aplicativo de destino ou o nome da classe de um componente totalmente qualificado. Normalmente, usa-se um intent explícito para iniciar um componente no próprio aplicativo porque se sabe o nome de classe da atividade ou do serviço que se quer iniciar. Por exemplo, iniciar uma nova atividade em resposta a uma ação do usuário ou iniciar um serviço para fazer o download de um arquivo em segundo plano.

- Os **intents implícitos** não nomeiam nenhum componente específico, mas declaram uma ação geral a realizar, o que permite que um componente de outro aplicativo a processe. Por exemplo, se você quiser exibir ao usuário uma localização em um mapa, pode usar um intent implícito para solicitar que outro aplicativo capaz exiba uma localização especificada no mapa.

A **figura 1** mostra como um intent é usado ao iniciar uma atividade. Quando o objeto **Intent** nomeia um componente específico da atividade de forma explícita, o sistema inicia o componente de imediato.

![_config.yml]({{ site.baseurl }}/images/intent-filters_2x.png)
**Figura 1**. Ilustração de como um intent implícito é fornecido pelo sistema para iniciar outra atividade: [1] A atividade A cria um **Intent** com uma descrição de ação e a passa para startActivity(). [2] O sistema Android busca, em todos os aplicativos, um filtro de intents que corresponda ao intent. Ao encontrar uma correspondência, [3] o sistema inicia a atividade correspondente (Atividade B) chamando seu método onCreate() e passando-lhe o **Intent**.

Ao criar um intent implícito, o sistema Android encontra o componente adequado para iniciar, comparando o conteúdo do intent aos filtros de intents declarados no arquivo de manifesto de outros aplicativos no dispositivo. Se o intent corresponder a um filtro de intents, o sistema iniciará esse componente e entregará o objeto **Intent**. Se diversos filtros de intents corresponderem, o sistema exibirá uma caixa de diálogo para que o usuário selecione o aplicativo que deseja usar.

O filtro de intents é uma expressão em um arquivo de manifesto do aplicativo que especifica o tipo de intents que o componente gostaria de receber. Por exemplo, ao declarar um filtro de intents para uma atividade, outros aplicativos se tornam capazes de iniciar diretamente sua atividade com o determinado tipo de intent. Do mesmo modo, se você não declarar nenhum filtro de intents para uma atividade, ela poderá ser iniciada somente com um intent explícito.

---

**ATENÇÃO**

Para garantir a segurança do seu aplicativo, sempre use um intent explícito ao iniciar um Service e não declare filtros de intents para os serviços. O uso de um intent implícito para iniciar um serviço representa um risco de segurança, porque não é possível determinar qual serviço responderá ao intent e o usuário não poderá ver que serviço é iniciado. A partir do Android 5.0 (API de nível 21), o sistema lança uma exceção ao chamar bindService() com um intent implícito.

---

## Criação de um intent
---
Um objeto **Intent** carrega informações que o sistema Android usa para determinar o componente a iniciar (como o nome exato do componente ou categoria do componente que deve receber o intent), além de informações que o componente receptor usa para realizar a ação adequadamente (como a ação a tomar e os dados a usar).

As informações principais contidas em um **Intent** são as seguintes:

### Nome do componente
É o nome do componente a iniciar.
É opcional, mas é a informação fundamental que torna um intent explícito, o que significa que o intent deve ser entregue somente ao componente do aplicativo definido pelo nome do componente. Sem nome de componente, o intent será implícito, e o sistema decidirá qual componente deve receber o intent com base nas informações de outro intent (como a ação, os dados e a categoria — descritos abaixo). Portanto, se for necessário iniciar um componente específico no seu aplicativo, você deverá especificar o nome do componente.

---

**Observação**
Ao iniciar um Service, deve-se sempre especificar o nome do componente. Caso contrário, não será possível determinar qual serviço responderá ao intent e o usuário não poderá ver que serviço é iniciado.

---

Esse campo do **Intent** é um objeto ComponentName que pode ser especificado usando um nome de classe totalmente qualificado do componente-alvo, inclusive o nome do pacote do aplicativo. Por exemplo, com.example.ExampleActivity. É possível definir o nome do componente com setComponent(), setClass(), setClassName() ou com o construtor Intent.

### Ação
String que especifica a ação genérica a realizar (como exibir ou selecionar).
No caso de um intent de transmissão, essa é a ação que entrou em vigor e que está sendo relatada. A ação determina amplamente como o resto do intent é estruturado — especificamente, o que está contido nos dados e em extras.

É possível especificar as ações para uso por intents dentro do aplicativo (ou para uso por outros aplicativos para chamar componentes no seu aplicativo), mas normalmente são usadas constantes de ação definidas pela classe **Intent** ou por outras classes de biblioteca. Veja algumas ações comuns para iniciar uma atividade:

#### ACTION_VIEW
Use essa ação em um intent com startActivity() quando houver informações de que uma atividade possa ser exibida ao usuário, como uma foto para exibição em um aplicativo de galeria ou um endereço para exibição em um aplicativo de mapa.
#### ACTION_SEND
Também conhecida como o intent de share, deve ser usada em um intent com startActivity() quando houver alguns dados que o usuário possa compartilhar por meio de outro aplicativo, como um app de e-mails ou de compartilhamento social.
Consulte a referência da classe **Intent** para saber mais constantes que definem ações genéricas. Outras ações são definidas em outros locais na biblioteca do Android, como nas Settings para ações que abrem telas específicas no aplicativo de Configurações do sistema.

É possível especificar a ação para um intent com setAction() ou com um construtor **Intent**.

Se você definir as próprias ações, verifique se incluiu o nome do pacote do seu aplicativo como prefixo, conforme o exemplo a seguir:

```java
static final String ACTION_TIMETRAVEL = "com.example.action.TIMETRAVEL";
```

### Dados
É o URI (um objeto Uri) que referencia os dados a serem aproveitados e/ou o tipo MIME desses dados. O tipo dos dados fornecidos geralmente é determinado pela ação do intent. Por exemplo, se a ação for ACTION_EDIT, os dados deverão conter o URI do documento a editar.
Ao criar um intent, em geral, é importante especificar o tipo de dados (seu tipo MIME) em adição ao URI. Por exemplo: uma atividade capaz de exibir imagens provavelmente não será capaz de reproduzir um arquivo de áudio, mesmo que os formatos do URI sejam similares. Portanto, especificar o tipo MIME dos dados ajuda o sistema Android a encontrar o melhor componente para receber o intent. Contudo, o tipo MIME, às vezes, pode ser inferido a partir do URI — especificamente quando os dados são um URI de content:. A URI de content: indica que os dados se localizam no dispositivo e são controlados por um ContentProvider, que torna o tipo MIME dos dados visível para o sistema.

Para definir só um URI de dados, chame setData(). Para definir só o tipo MIME, chame setType(). Se necessário, é possível definir ambos explicitamente com setDataAndType().

---

**Atenção** 

Se você desejar definir o URI e o tipo MIME, não chame setData() e setType(), pois um anula o outro. Sempre use setDataAndType() para definir o URI e o tipo MIME juntos.

---

### Categoria
É uma string que contém informações adicionais sobre o tipo de componente que deve processar o intent. Qualquer número de descrições de categoria pode ser inserido em um intent, mas a maioria dos intents não requer nenhuma categoria. Veja algumas categorias comuns:
#### CATEGORY_BROWSABLE
A atividade-alvo permite ser iniciada por um navegador da Web para exibir dados referenciados por um link, como uma imagem ou uma mensagem de e-mail.
#### CATEGORY_LAUNCHER
A atividade é a atividade inicial de uma tarefa e é listada no inicializador do aplicativo do sistema.
Consulte a descrição da classe **Intent** para ver a lista completa de categorias.

É possível especificar uma categoria com addCategory().

As propriedades listadas abaixo (nome do componente, ação, dados e categoria) representam as características de definição de um intent. Ao ler estas propriedades, o sistema Android será capaz de definir o componente de aplicativo que ele deve iniciar. Contudo, um intent pode carregar informações adicionais que não afetam o modo com que é tratado para um componente do aplicativo. Os intents também podem fornecer o seguinte:

### Extras
São pares de chave-valor que carregam informações adicionais necessárias para realizar a ação solicitada. Assim como algumas ações usam determinados tipos de URIs de dados, outras também usam determinados extras.
É possível adicionar dados extras com diversos métodos putExtra(), cada um aceitando dois parâmetros: o nome principal e o valor. Também é possível criar um objeto Bundle com todos os dados extras e, em seguida, inserir o Bundle no elemento **Intent** com putExtras().

Por exemplo, ao criar um intent para enviar um e-mail com ACTION_SEND, é possível especificar o recipiente to com a chave EXTRA_EMAIL e especificar subject com a chave EXTRA_SUBJECT.

A classe **Intent** especifica diversas constantes EXTRA_* para tipos de dados padronizados. Se for necessário declarar chaves extras (para intents que seu aplicativo receba), certifique-se de incluir o nome do pacote do aplicativo como prefixo, conforme o exemplo abaixo:

```java
static final String EXTRA_GIGAWATTS = "com.example.EXTRA_GIGAWATTS";
```

### Sinalizadores
Sinalizadores são definidos na classe **Intent** e funcionam como metadados para o intent. Os sinalizadores podem instruir o sistema Android a inicializar uma atividade (por exemplo, a qual tarefa a atividade deve pertencer) e como tratá-la após a inicialização (por exemplo, se ela pertence a uma lista de atividades recentes).
Para mais informações, consulte o método setFlags().

## Exemplo de intent explícito
O intent explícito é usado para inicializar um componente específico de um aplicativo, como uma atividade ou serviço em particular no seu aplicativo. Para criar um intent explícito, defina o nome do componente para o objeto **Intent** — todas as outras propriedades do intent são opcionais.

Por exemplo, se você criar um serviço no aplicativo chamado DownloadService, projetado para fazer o download de um arquivo da Web, poderá iniciá-lo com o código a seguir:

```java
// Executed in an Activity, so 'this' is the Context
// The fileUrl is a string URL, such as "http://www.example.com/image.png"
Intent downloadIntent = new Intent(this, DownloadService.class);
downloadIntent.setData(Uri.parse(fileUrl));
startService(downloadIntent);
```

O construtor **Intent(Context, Class)** fornece o Context do aplicativo e o componente fornece um objeto Class. Assim, esse intent inicia explicitamente a classe DownloadService no aplicativo.

Para mais informações sobre a criação e inicialização de um serviço, consulte o guia Serviços.

## Exemplo de intent implícito
O intent implícito especifica uma ação que possa chamar qualquer aplicativo no dispositivo capaz de realizar a ação. O intent implícito é útil quando o aplicativo não pode realizar a ação, mas outros aplicativos provavelmente podem, e o usuário seleciona que aplicativo usar.

Por exemplo, se você tiver conteúdo que quer que o usuário compartilhe com outras pessoas, crie um intent com a ação ACTION_SEND e adicione extras que especifiquem o conteúdo a compartilhar. Ao chamar startActivity() com esse intent, o usuário poderá selecionar um aplicativo para compartilhar o conteúdo.

---

**ATENÇÃO**

É possível que um usuário não tenha nenhum aplicativo que processe o intent implícito enviado a startActivity(). Ou um aplicativo pode estar inacessível por causa das configurações ou restrições do perfil definidas pelo administrador. Se isso ocorrer, há uma falha de chamada e o aplicativo será interrompido. Para verificar se uma atividade receberá o intent, chame resolveActivity() no seu objeto **Intent**. Se o resultado for diferente de nulo, há pelo menos um aplicativo que pode processar o intent e é seguro chamar startActivity(). Se o resultado for nulo, você não deve usar o intent e, se possível, deve desativar o recurso que o emite. O exemplo abaixo mostra como verificar se o intent resulta em uma atividade. Nesse caso, não será usado nenhum URI, mas o tipo de dados do intent será declarado para especificar o conteúdo carregado pelos extras.

---

```java
// Create the text message with a string
Intent sendIntent = new Intent();
sendIntent.setAction(Intent.ACTION_SEND);
sendIntent.putExtra(Intent.EXTRA_TEXT, textMessage);
sendIntent.setType("text/plain");

// Verify that the intent will resolve to an activity
if (sendIntent.resolveActivity(getPackageManager()) != null) {
    startActivity(sendIntent);
}
```

Quando startActivity() é chamada, o sistema avalia todos os aplicativos instalados para determinar quais deles podem processar esse tipo de intent (um intent com a ação ACTION_SEND e que carrega dados de “texto/simples”. Se houver somente um aplicativo que possa tratá-lo, o aplicativo se abrirá imediatamente e receberá o intent. Se diversas atividades aceitarem o intent, o sistema exibirá uma caixa de diálogo, conforme exibido na Figura 2, para que o usuário selecione que aplicativo usar.

Mais informações sobre iniciar outros aplicativos são fornecidas também em Como enviar o usuário para outro aplicativo.

![_config.yml]({{ site.baseurl }}/images/intent-choosers.png)
**Figura 2**. Caixa de diálogo seletora.

### Como forçar um seletor de aplicativo
Quando há mais de um aplicativo que responde ao intent implícito, o usuário pode selecionar o aplicativo que quer usar e tornar esse aplicativo a escolha padrão para a ação. Isso é positivo ao executar uma ação em que o usuário quer usar o mesmo aplicativo todas as vezes, como quando abre uma página da Web (os usuários geralmente usam somente um navegador).

Contudo, se diversos aplicativos puderem responder ao intent e o usuário tiver que ficar livre para usar um aplicativo diferente a cada vez, é preciso exibir uma caixa de diálogo seletora explicitamente. A caixa de diálogo seletora pede que o usuário selecione o aplicativo desejado para a ação todas as vezes (o usuário não pode selecionar um aplicativo padrão para a ação). Por exemplo: quando o aplicativo realiza “compartilhar” com a ação ACTION_SEND, os usuários podem querer compartilhar usando um aplicativo diferente conforme a situação, portanto, deve-se sempre usar a caixa de diálogo seletora, como ilustrado na figura 2.

Para mostrar o seletor, crie um **Intent** usando createChooser() e passe-o para startActivity(), conforme exibido no exemplo a seguir: Isso exibirá uma caixa de diálogo com uma lista de aplicativos que respondem ao intent passado ao método createChooser() e usará o texto fornecido como título da caixa de diálogo.

```java
Intent sendIntent = new Intent(Intent.ACTION_SEND);
...

// Always use string resources for UI text.
// This says something like "Share this photo with"
String title = getResources().getString(R.string.chooser_title);
// Create intent to show the chooser dialog
Intent chooser = Intent.createChooser(sendIntent, title);

// Verify the original intent will resolve to at least one activity
if (sendIntent.resolveActivity(getPackageManager()) != null) {
    startActivity(chooser);
}
```

## Como receber um intent implícito
---
Para anunciar quais intents implícitos o aplicativo pode receber, declare um ou mais filtros de intents para cada um dos componentes do aplicativo com um elemento <intent-filter> no seu arquivo de manifesto. Cada filtro de intents especifica o tipo de intents aceito com base na ação, nos dados e na categoria do intent. O sistema fornecerá um intent implícito ao componente do seu aplicativo somente se ele puder passar por um dos filtros de intents.

---

**Observação**

O intent explícito é sempre entregue ao alvo independentemente dos filtros de intents que o componente declare.

---
Os componentes de um aplicativo devem declarar filtros separados para cada job exclusivo que podem fazer. Por exemplo, uma atividade em um aplicativo de galeria de imagens pode ter dois filtros: um filtro para visualizar uma imagem e outro para editar uma imagem. Quando a atividade se inicia, ela inspeciona o **Intent** e decide como se comportar com base nas informações no **Intent** (como para exibir ou não os controles do editor).

Cada filtro de intents é definido por um elemento <intent-filter> no arquivo de manifesto do aplicativo, aninhado no componente correspondente do aplicativo (como um elemento <activity>). Dentro de <intent-filter>, é possível especificar o tipo de intents aceitos usando um ou mais dos três elementos a seguir:

<action>
Declara a ação do intent aceito, no atributo name. O valor deve ser o valor literal da string de uma ação, e não a constante da classe.
<data>
Declara o tipo de dados aceitos usando um ou mais atributos que especificam diversos aspectos do URI de dados (scheme, host, port, path) e o tipo MIME.
<category>
Declara a categoria do intent aceito, no atributo name. O valor deve ser o valor literal da string de uma ação, e não a constante da classe.

---
**Observação**

Para receber intents implícitos, você precisa incluir a categoria CATEGORY_DEFAULT no filtro de intents. Os métodos startActivity() e startActivityForResult() tratam todos os intents como se eles declarassem a categoria CATEGORY_DEFAULT. Se você não a declarar no filtro de intents, nenhum intent implícito retomará sua atividade.

---

Por exemplo, abaixo há uma declaração de atividade com um filtro de intents para receber um intent ACTION_SEND quando o tipo de dados for texto:

```xml
<activity android:name="ShareActivity">
    <intent-filter>
        <action android:name="android.intent.action.SEND"/>
        <category android:name="android.intent.category.DEFAULT"/>
        <data android:mimeType="text/plain"/>
    </intent-filter>
</activity>
```

Você pode criar um filtro que inclua mais de uma instância de <action>, <data> ou <category>. Se fizer isso, será necessário verificar se o componente pode processar todas as combinações dos elementos de filtro.

Para processar diversos tipos de intents, mas somente em combinações específicas de ações, dados e tipos de categoria, será necessário criar diversos filtros de intents.

Os intents implícitos são testados em relação a um filtro por meio da comparação do intent com cada um dos três elementos. Para ser entregue ao componente, o intent deve passar por todos os três testes. Se ele falhar em algum deles, o sistema Android não entregará o intent ao componente. No entanto, como um componente pode ter diversos filtros de intents, um intent que não passe por um dos filtros de um componente pode passar por outro filtro. Veja mais informações sobre como o sistema resolve intents na seção abaixo sobre Resolução de intents.

---

**Atenção**

O uso de filtros de intent não é um modo seguro de evitar que outros aplicativos iniciem componentes. Embora os filtros de intents restrinjam um componente a responder somente a determinados tipos de intents implícitos, outro aplicativo pode iniciar o componente do seu aplicativo usando um intent explícito se o desenvolvedor determinar os nomes dos componentes. Se for importante que somente seu próprio aplicativo inicie um dos seus componentes, não declare filtros de intents no manifesto. Em vez disso, defina o atributo exported como "false" para esse componente.

---

De modo semelhante, para evitar a execução involuntária de um Service diferente do aplicativo, sempre use um intent explícito para iniciar o próprio serviço.

---

**Observação**

Para todas as atividades, é necessário declarar os filtros de intents no arquivo de manifesto. No entanto, filtros para broadcast receivers podem ser registrados dinamicamente ao chamar registerReceiver(). Assim, será possível cancelar o registro do receptor com unregisterReceiver(). Isso permitirá que o aplicativo receba transmissões específicas durante um período de tempo especificado somente quando o aplicativo estiver em execução.

---

### Exemplos de filtros
Para demonstrar alguns comportamentos do filtro de intents, veja um exemplo do arquivo de manifesto de um aplicativo de compartilhamento social.

```xml
<activity android:name="MainActivity">
    <!-- This activity is the main entry, should appear in app launcher -->
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>

<activity android:name="ShareActivity">
    <!-- This activity handles "SEND" actions with text data -->
    <intent-filter>
        <action android:name="android.intent.action.SEND"/>
        <category android:name="android.intent.category.DEFAULT"/>
        <data android:mimeType="text/plain"/>
    </intent-filter>
    <!-- This activity also handles "SEND" and "SEND_MULTIPLE" with media data -->
    <intent-filter>
        <action android:name="android.intent.action.SEND"/>
        <action android:name="android.intent.action.SEND_MULTIPLE"/>
        <category android:name="android.intent.category.DEFAULT"/>
        <data android:mimeType="application/vnd.google.panorama360+jpg"/>
        <data android:mimeType="image/*"/>
        <data android:mimeType="video/*"/>
    </intent-filter>
</activity>
```

A primeira atividade, MainActivity, é o ponto de entrada principal do aplicativo — a atividade que se abre quando o usuário inicializa o aplicativo pela primeira vez com o ícone na tela de início:

A ação ACTION_MAIN indica que esse é o ponto de entrada principal e não espera nenhum dado de intent.
A categoria CATEGORY_LAUNCHER indica que esse ícone da atividade deve ser colocado no inicializador de aplicativo do sistema. Se o elemento <activity> não especificar nenhum ícone com icon, o sistema usará o ícone do elemento <application>.
Esses dois devem ser pareados para que a atividade apareça no inicializador do aplicativo.

A segunda atividade, ShareActivity, destina-se a facilitar o compartilhamento de conteúdo de texto e mídia. Apesar de os usuários poderem acessar essa atividade pela MainActivity, eles também podem acessar ShareActivity diretamente de outro aplicativo que emita um intent implícito que corresponda a um dos dois filtros de intents.

---

**Observação**

O tipo MIME application/vnd.google.panorama360+jpg é um tipo de dados especial que especifica fotos panorâmicas que podem ser processadas com as APIs do Google Panorama.

---

## Uso de um intent pendente
---
Um objeto **PendingIntent** é um agrupador em torno de um objeto **Intent**. A principal finalidade de um **PendingIntent** é conceder permissão a um aplicativo externo para usar o **Intent** contido como se ele fosse executado a partir do processo do próprio aplicativo.

Os principais casos de uso de um intent pendente são os seguintes:

Declarar um intent a ser executado quando o usuário realiza uma ação com a Notificação (o NotificationManager do sistema Android executa o **Intent**).
Declarar um intent a ser executado quando o usuário realiza uma ação com o Widget do aplicativo (o aplicativo de tela inicial executa o **Intent**).
Declarar um intent a ser executado em um momento específico no futuro (o AlarmManager do sistema Android executa o **Intent**).
Assim como cada objeto **Intent** é projetado para ser processado por um tipo específico de componente do aplicativo (uma Activity, um Service ou um BroadcastReceiver), um **PendingIntent** deve ser criado com a mesma consideração. Ao usar um intent pendente, o aplicativo não executa o intent com uma chamada como startActivity(). Em vez disso, é necessário declarar o tipo do componente pretendido ao criar o **PendingIntent** chamando o respectivo método criador:

**PendingIntent.getActivity()** para um **Intent** que inicia uma Activity.
**PendingIntent.getService()** para um **Intent** que inicia um Service.
**PendingIntent.getBroadcast()** para um **Intent** que inicia um BroadcastReceiver.
A menos que o aplicativo esteja recebendo intents pendentes de outros aplicativos, os métodos acima para criar um **PendingIntent** são provavelmente os únicos método de **PendingIntent** necessários.

Cada método toma o Context do aplicativo atual, o **Intent** que você deseja agrupar e um ou mais sinalizadores que especificam como o intent deve ser usado (como se o intent pode ser usado mais de uma vez).

Veja mais informações sobre o uso de intents pendentes na documentação dos respectivos casos de uso, como nos guias das APIs de Notificações e Widget do aplicativo.

## Resolução de intents
---
Quando o sistema recebe um intent implícito para começar uma atividade, ele procura a melhor atividade para o intent ao compará-lo aos filtros de intent com base em três aspectos:

- Ação
- Dados (URI e tipo de dados)
- Categoria

As seções a seguir descrevem como os intents correspondem aos componentes apropriados de acordo com a declaração do filtro de intents em um arquivo de manifesto do aplicativo.

### Teste de ação
Para especificar ações de intents aceitos, um filtro de intents pode declarar zero ou mais elementos <action>, conforme o exemplo a seguir:

```xml
<intent-filter>
    <action android:name="android.intent.action.EDIT" />
    <action android:name="android.intent.action.VIEW" />
    ...
</intent-filter>
```

Para passar por esse filtro, a ação especificada no **Intent** deve corresponder a uma das ações listadas no filtro.

Se o filtro não listar nenhuma ação, não haverá nada a que um intent corresponda, portanto, todos os intents falharão no teste. No entanto, se um **Intent** não especificar uma ação, ele passará no teste, desde que o filtro contenha ao menos uma ação.

### Teste de categoria
Para especificar ações de intents aceitos, um filtro de intents pode declarar zero ou mais elementos <category>, conforme o exemplo a seguir:

```xml
<intent-filter>
    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />
    ...
</intent-filter>
```

Para que um intent passe no teste de categoria, cada categoria no **Intent** precisa corresponder a uma categoria no filtro. O inverso não é necessário — o filtro de intents pode declarar mais categorias que as especificadas no **Intent** e o **Intent** ainda passará no teste. Portanto, um intent sem categorias sempre passará nesse teste independentemente das categorias declaradas no filtro.

---

**Observação**

O Android aplica automaticamente a categoria CATEGORY_DEFAULT a todos os intents implícitos passados a startActivity() e a startActivityForResult(). Para que a atividade receba intents implícitos, ela precisa conter uma categoria de "android.intent.category.DEFAULT" nos filtros de intents (como é exibido no exemplo de <intent-filter> anterior).

---

### Teste de dados
Para especificar ações de intents aceitos, um filtro de intents pode declarar zero ou mais elementos <data>, conforme o exemplo a seguir:

```xml
<intent-filter>
    <data android:mimeType="video/mpeg" android:scheme="http" ... />
    <data android:mimeType="audio/mpeg" android:scheme="http" ... />
    ...
</intent-filter>
```

Cada elemento <data> pode especificar uma estrutura de URI e um tipo de dados (tipo de mídia MIME). Cada parte do URI é um atributo separado: scheme, host, port e path:

```xml
<scheme>://<host>:<port>/<path>
```

O exemplo abaixo mostra possíveis valores para esses atributos:

```
content://com.example.project:200/folder/subfolder/etc
``` 

Nesse URI, o esquema é content, o host é com.example.project, a porta é 200 e o caminho é folder/subfolder/etc.

Cada um desses atributos é opcional em um elemento <data>, mas há dependências lineares:

- Se não houver esquema especificado, o host será ignorado.
- Se não houver host especificado, a porta será ignorada.
- Se não houver esquema nem host especificado, o caminho será ignorado.

Quando o URI em um intent é comparado a uma especificação de URI em um filtro, a comparação é feita somente com as partes do URI incluídas no filtro. Por exemplo:

- Se um filtro especificar somente um esquema, todos os URIs com esse esquema atenderão ao filtro.
- Se um filtro especificar um esquema e uma autoridade, mas não um caminho, todos os URIs com o mesmo esquema e autoridade passarão pelo filtro independentemente dos caminhos.
- Se um filtro especificar um esquema, uma autoridade e um caminho, somente URIs com o mesmo esquema, autoridade e caminho passarão pelo filtro.

---
**Observação**

A especificação de caminho pode conter um asterisco de caractere curinga (*) para exigir somente uma correspondência parcial do nome do caminho.

---

O teste de dados compara o URI e o tipo MIME do intent com um URI e um tipo MIME especificados no filtro. As regras são as seguintes:

1. O intent que não contiver URI nem tipo MIME passará no teste somente se o filtro não especificar nenhum URI nem tipo MIME.
2. O intent que contiver um URI, mas nenhum tipo MIME (nem explícito, nem inferível a partir do URI), passará no teste somente se o URI corresponder ao formato de URI do filtro e se o filtro, igualmente, não especificar um tipo MIME.
3. O intent que contiver um tipo MIME, mas nenhum URI, passará no teste somente se o filtro listar o mesmo tipo MIME e não especificar nenhum formato de URI.
4. O intent que contiver URI e tipo MIME (explícito ou inferível a partir do URI) passará na parte do tipo MIME do teste somente se esse tipo corresponder a um tipo listado no filtro. Ele passará na parte do URI do teste se corresponder a um URI no filtro ou se tiver um URI de content: ou file: e se o filtro não especificar nenhum URI. Em outras palavras, presume-se que um componente seja compatível com dados de content: e de file: se o filtro listar somente um tipo MIME.

---

**Observação**

Se um intent especificar um URI ou um tipo MIME, haverá falha no teste de dados caso não haja elementos <data> no <intent-filter>.

---

Essa última regra (d) reflete a expectativa de que os componentes sejam capazes de receber dados de local de um arquivo ou provedor de conteúdo. Portanto, os filtros podem listar somente um tipo de dados e não precisam nomear explicitamente os esquemas content: e file:. Um elemento <data> como o seguinte, por exemplo, informa ao Android que o componente pode receber dados de imagem de um provedor de conteúdo e exibi-los:

```xml
<intent-filter>
    <data android:mimeType="image/*" />
    ...
</intent-filter>
```

Como a maioria dos dados disponíveis é dispensada pelos provedores de conteúdo, os filtros que especificam um tipo de dados, mas não um URI, são, talvez, os mais comuns.

Outra configuração comum é de filtros com um esquema e um tipo de dados. Por exemplo, um elemento <data>, como o seguinte, informa ao Android que o componente pode recuperar dados de vídeo da rede para realizar a ação:

```xml
<intent-filter>
    <data android:scheme="http" android:mimeType="video/*" />
    ...
</intent-filter>
```

### Correspondência de intents
Intents são correspondidos a filtros de intents não somente para descobrir um componente-alvo a ativar, mas também para descobrir algo sobre o conjunto de componentes do dispositivo. Por exemplo, o aplicativo Home preenche o inicializador do aplicativo encontrando todas as atividades com filtros de intents que especifiquem a ação ACTION_MAIN e a categoria CATEGORY_LAUNCHER. Uma correspondência só é bem-sucedida se as ações e categorias no intent estiverem de acordo com o filtro, como descrito na documentação para a classe IntentFilter.

O aplicativo pode usar a correspondência de intents de modo semelhante ao feito pelo aplicativo Home. O PackageManager tem um conjunto de métodos query...() que retornam todos os componentes que podem aceitar um determinado intent e uma série de métodos resolve...() similares, que determinam o melhor componente para responder a um intent. Por exemplo, queryIntentActivities() retorna uma lista de atividades que podem realizar o intent passado como argumento, e queryIntentServices() retorna uma lista semelhante de serviços. Nenhum dos métodos ativa os componentes, eles só listam os que podem responder. Há um método semelhante, queryBroadcastReceivers(), para os broadcast receivers.

[Publicação Original Android Developers](https://developer.android.com/guide/components/intents-filters?hl=pt_br)

---

Content and code samples on this page are subject to the licenses described in the [Content License](https://developer.android.com/license?hl=pt_br). Java is a registered trademark of Oracle and/or its affiliates.

---
