---
title: "Radmin no Linux"
draft: false
header: true
description: "Guia de como fazer o Radmin funcionar no Linux"
---

{{< toc >}}

Pelo fato do Radmin não estar disponível como um programa nativo pra Linux, o único caminho é rodando ele numa VM de Windows, e redirecionar o tráfego de rede indo pro Radmin rodando na VM, pro Linux. Isso é bem possível de fazer usando o programa de virtualização Virt Manager.

## 0. Observações

1. Esse guia é basicamente uma versão escrita desse vídeo aqui:  

	<https://www.youtube.com/watch?v=XsiRJ62MIy8>

	O vídeo não foi feito por mim, e esse guia explica praticamente o mesmo passo-a-passo do vídeo.

2. Esse guia foi escrito tendo em mente netplay peer-to-peer (um jogador conectar no IP do outro). Na minha experiência, jogos que usam um sistema de escanear a rede local, tipo Minecraft, não funcionam, e exigem que você conecte no IP da pessoa diretamente.

3. Virt Manager não possibilita que você copie e cole textos da VM pro Host e vice-versa sem fazer umas configurações complicadas, então tudo vai ter que ser digitado à mão. O processo pra habilitar essa função vai além do foco desse guia.

4. Meu PC está todo em inglês, então nomes de botões e programas citados aqui podem (e muito provavelmente vão) variar se o seu sistema estiver em outra língua. Estou considerando que você sabe o inglẽs o suficiente pra conseguir traduzir e entender onde fica cada coisa. O programa "Virtual Machine Manager" (Virt Manager) se chama "Gerenciador de Máquinas Virtuais" em português.

5. É *bem* comum algo não funcionar direito no processo de "instalar" o Radmin. Maioria das vezes vai ser porque você não prestou atenção em algo, mas pode ser que seja o Windows dando chilique, ou você só deu azar mesmo. Tenha paciência.

6. Este processo foi feito no Debian 12, com Virt Manager versão 4.1.0, e QEMU versão 7.2. Pode ser que algumas coisas mudem no processo em versões mais recentes.

## 1. Instalando o Virt Manager

O processo de instalação varia de distro pra distro, então infelizmente eu não tenho como dar uma lista de passos que seja universal.

Eu uso Debian, onde o processo, que também se aplica ao Ubuntu e Linux Mint/LMDE, é:

1. Rodar esses comandos:

	```
	sudo apt install virt-manager    # instala o Virt Manager
	sudo usermod -a -G libvirt $USER # adiciona o seu usuário ao grupo "libvirt"
	```

2. Deslogar e logar de volta, ou reiniciar o computador, o que for mais fácil.

## 2. Criando uma VM de Windows

Primeiro, adquira uma ISO de Windows, **preferencialmente Windows Server 2008 R2 ou 2012 R2**, mas qualquer versão que não seja XP serve. Windows Server é preferível às versões desktop devido ao baixo uso de CPU e RAM, e o processo de instalação é *bem* mais rápido.

Configs mínimas pra VM:

- 2 núcleos de CPU
- 1GB de RAM
- 60GB de armazenamento

Depois que você tiver terminado esse guia, você pode baixar as configs para 1 núcleo e 512MB de RAM se quiser, mas durante a instalação é importante ter 2 núcleos e 1GB de RAM.

Se você não souber como cria uma VM no Virt Manager, logo abaixo tem um tutorial de como faz.

{{< details summary="Tutorial de como criar VM no Virt Manager" >}}
Comece clicando nesse botão aqui, no canto superior esquerdo da janela.

![](/img/radmin-no-linux/vm0.png)

Ele irá abrir uma janela como essa:

![](/img/radmin-no-linux/vm1.png)

Selecione "Local install media (ISO image or CDROM)", e clique pra avançar.

![](/img/radmin-no-linux/vm2.png)

Aqui, clique em "Browse" para escolher a sua ISO do Windows.

![](/img/radmin-no-linux/vm3.png)

Aí ele vai abrir essa outra janela. Clique em "Browse Local", e aí sim você seleciona a sua ISO numa janela de selecionar arquivo que vai abrir.

Depois de selecionar, ele vai voltar pra janela principal. No campo de baixo, digite o nome do Windows que você vai instalar, e selecione ele numa caixinha que vai aparecer.

![](/img/radmin-no-linux/vm4.png)

No meu caso eu escolhi "Windows Server 2016", mesmo eu estando instalando o Windows Server 2012 R2, porque a opção pro 2012 não tá disponível, e 2016 é a versão mais próxima do 2012.

Clique pra avançar. Depois, o Virt Manager provavelmente vai abrir uma janela como essa aqui:

![](/img/radmin-no-linux/vm5.png)

Só clicar em "Yes" pra corrigir, que tá tudo certo.

Daí ele vai avançar pra essa janela:

![](/img/radmin-no-linux/vm6.png)

Aqui é onde você vai colocar as configurações recomendadas que eu falei lá no guia: 1GB de RAM (1024MB) e 2 núcleos de CPU. Clique pra avançar.

![](/img/radmin-no-linux/vm7.png)

Aqui o processo vai divergir um pouco, dependendo de como o seu sistema tá particionado. *Provavelmente* ele só vai ter uma partição pro sistema inteiro e outra pra swap. Pra ter certeza, rode o comando `lsblk` e veja se esse é o caso.

**Se o seu sistema tiver só uma partição pra tudo**, copie o que está na imagem acima, e clique pra avançar, e arraste essa página pra baixo até achar uma seção escrito "Finalizando".

**Se o seu sistema tiver uma partição pro `/home` e outra pro `/` (diretório raíz)**, instale da seguinte forma: Selecione o botãozinho de baixo, e clique em "Manage".

![](/img/radmin-no-linux/vm8.png)

Ele vai abrir essa outra janela:

![](/img/radmin-no-linux/vm9.png)

Ali na barra da direita, selecione a opção que estiver escrito o nome do seu usuário ("user" no meu caso). Depois, clique naquele botãozinho de "mais" pra criar um novo volume. Ele vai abrir uma janelinha assim:

![](/img/radmin-no-linux/vm10.png)

Aqui você vai colocar o nome que você quer dar pro volume, o formato pode deixar em qcow2 mesmo, e a capacidade você põe o que eu recomendei no guia, que é 60GB. Selecione também a opção "Allocate entire volume now".

Clique em "Finish", e ele vai voltar pra essa janela:

![](/img/radmin-no-linux/vm11.png)

Você pode mover o arquivo que ele criou ("radmin.qcow2") no diretório do seu usuário, que é o volume da VM em si, pra qualquer pasta que vocẽ quiser, e depois clicar ali em "Manage" de novo e selecionar o arquivo de novo. Mas isso é só por questão de organização, não é nada necessário. Eu coloquei na pasta "Documents", como tá na imagem.

Clique pra avançar aqui.

Aliás, esse processo todo é pra evitar do armazenamento da VM consumir *toda* a partição pro diretório raíz, que é onde o Virt Manager normalmente salva os volumes das suas VMs. Geralmente partições exclusivas pro diretório raíz têm 20 ou 30GB disponíveis somente.

## Finalizando

Finalmente, o Virt Manager vai mostrar uma janela assim:

![](/img/radmin-no-linux/vm12.png)

Se certifique de que as configurações estão de acordo com o que está na imagem, exceto "Storage" que pode variar de sistema pra sistema, e "OS" que varia dependendo de qual versão do Windows vocẽ está instalando. No nome você pode botar qualquer coisa.

Clique pra finalizar. Se essa é a sua primeira vez instalando uma VM, ele provavelmente vai abrir uma janela assim:

![](/img/radmin-no-linux/vm13.png)

Só clicar em "Yes" pra ativar a placa de rede virtual.

Depois disso, ele vai abrir uma janela com a VM em si. Instale o Windows normalmente, e prossiga com o guia.
{{< /details >}}

### 2.1 Instalando o Radmin

Com o Windows Server (ou qualquer versão que você tiver) instalado, instale o Radmin na VM. Você pode baixar o instalador pelo Internet Explorer mesmo, indo no site do Radmin por ele. Quando você abrir o Internet Explorer, ele vai mostrar isso aqui

![](/img/radmin-no-linux/new1.png)

Clique em "Don't use recommended settings", e em "OK".

Outra coisa que vai aparecer é essa mensagem aqui, quando você tentar conectar no site do Radmin:

![](/img/radmin-no-linux/new2.png)

Clique em "Add..." pra adicionar uma exceção ao site, em "Add" de novo na janelinha que abrir depois disso. Essa mensagem vai aparecer mais de uma vez. Aí é só instalar o Radmin normalmente.

Depois de instalado o Radmin, entre em alguma rede qualquer e tente pingar o IP de alguém (botão direito no nome de alguém > Ping), só pra garantir que a VM em si consegue se conectar com outras pessoas.

Ah, e **anote o seu IP do Radmin**.

## 2. Configurando a VM

Desligue a VM, e volte pra janela principal do Virt Manager. Em seguida, vá em Edit > Preferences, no topo da janela do programa. Lá, ative a opção chamada "Enable XML editing". Isso é necessário pra fazer certas configurações mais específicas.

![](/img/radmin-no-linux/new3.png)

Agora é hora da parte chata... Caso você tenha algum erro em algum ponto dessa etapa, recomendo recomeçá-la do zero.

Antes de qualquer coisa, é necessário criar uma placa de rede virtual no modo "isolated". É por meio dessa placa de rede virtual que o redirecionamento do tráfego indo pra VM, pro Linux será feita.

Com o Virt Manager aberto, selecione o item "QEMU/KVM". Depois, vá em Edit > Connection Details. Clicando na aba "Virtual Networks", você verá uma janela parecida com essa:

![](/img/radmin-no-linux/new4.png)

Clique no sinal de mais ("+") no canto inferior esquerdo para criar uma nova placa de rede virtual, e copie essas configurações:

![](/img/radmin-no-linux/new5.png)

(O nome tu pode botar qualquer um. Só tenha em mente qual que é a placa de rede isolada e qual que não é)

Depois de criá-la, ela provavelmente vai estar ativa por padrão. Desative ela clicando no botão com uma placa de parar (⛔), do lado direito do botão com sinal de mais.

### 2.1. Configurando a rede 

Agora, com a VM ainda aberta, volte para aquela janela com as placas de rede virtuais, indo para a janela principal do Virt Manager, depois em Edit > Connection Details (Editar > Detalhes de conexão), depois na aba "Virtual Networks" (Redes Virtuais). Selecione a que criou no passo anterior, e clique na aba "XML". Você verá algo parecido com isso:

![](/img/radmin-no-linux/new6.png)

Onde tá escrito "SEU IP DO RADMIN", você vai colocar o seu IP do Radmin, que será algo parecido com "26.123.456.789". Clique em "Apply", e ative essa placa de rede virtual clicando no botão de play (▶) no canto inferior esquerdo.

Após isso, clique no botão de informação no canto superior direito, na janela da VM em si.

![](/img/radmin-no-linux/new7.png)

Nessa janela, nós vamos adicionar aquela placa de rede virtual isolada que criamos anteriormente, na VM. Clique no botão no canto inferior esquerdo escrito "Add Hardware".

![](/img/radmin-no-linux/new8.png)

Uma outra janela será aberta:

![](/img/radmin-no-linux/new9.png)

Aqui, na lista da esquerda, você vai selecionar "Network". Depois, na parte da direita, no primeiro item, escrito "Network source", você vai selecionar a placa de rede virtual isolada que vocẽ criou.

Agora, só clicar em "Finish", no canto inferior direito, e em "Apply" na janela anterior.

### 2.2. Redirecionando o tráfego da VPN pro Linux

Volte para a janela da VM, clicando neste botão, com um ícone de um monitor:

![](/img/radmin-no-linux/new10.png)

E ligue a VM novamente, clicando no botão de play (▶) no topo da janela.

Depois de logar, abra o Radmin, entre em alguma rede, e pingue o IP de alguém, clicando com o botão direito no nome de alguém e clicando em "Ping". Deixe essa janela com o terminal rodando o ping aberta o tempo todo, para se certificar de que ainda possui conexão depois de fazer cada coisa nesse passo.

Aperte Win + R, e na janela que abrir, digite "ncpa.cpl" e pressione Enter. Uma janela semelhante a essa será aberta:

![](/img/radmin-no-linux/new11.png)

Aqui você verá três dispositivos de rede: 

1. A placa de rede virtual por onde a VM se conecta à internet (vou chamar de "*Placa da VM*");
2. A placa de rede virtual isolada que acabou de adicionar à VM (vou chamar de "*Placa Isolada*");
3. E a placa de rede virtual do Radmin (vou chamar de "*Placa do Radmin*"). 

Muito provavelmente, a Placa da VM e a Placa Isolada vão ter nomes muito parecidos por padrão ("Ethernet" e "Ethernet 2" no meu caso). Para saber de fato qual que é qual, desabilite uma delas. Se depois de você desabilitá-la você perder a conexão com a pessoa que está pingando, a placa que você desabilitou é a Placa da VM. Se não acontecer nada com a conexão, a que você desabilitou é a Placa Isolada. É bom mudar o nome das placas pra ficar mais fácil de diferenciar uma da outra.

Nas imagens a seguir, "Ethernet" é a Placa da VM, "Isolada" é a Placa Isolada, e "Radmin VPN" é a Placa do Radmin.

Agora, é necessário fazer uma ponte entre a Placa do Radmin e a Placa Isolada, para que o Linux consiga pegar o tráfego indo pro Radmin e se conectar à pessoas na rede.

Segurando Ctrl, selecione a Placa Isolada e a Placa do Radmin clicando apenas uma vez em cada uma. Depois, clique com o botão direito **na Placa do Radmin especificamente**, e depois clique em "Bridge Connections".

![](/img/radmin-no-linux/new12.png)

Esse processo demora alguns segundos. É normal que você perca conexão com a pessoa que está pingando após fazer isso.

Com a ponte criada, clique com o botão direito nela, depois clique em "Properties".

![](/img/radmin-no-linux/new13.png)

Uma janela parecida com essa vai abrir:

![](/img/radmin-no-linux/new14.png)

Depois, na lista de baixo, clique duas vezes em "Internet Protocol Version 4 (TCP/IPv4)".

Uma janela de configuração será aberta. Copie estas configurações:

![](/img/radmin-no-linux/new15.png)

Depois clique em "OK", e em "OK" de novo na janela anterior. Certifique-se de que a conexão com a pessoa que está pingando voltou. Caso esse não seja o caso, delete a ponte e faça tudo de novo.

26.0.0.3 é o IP que identificará a ponte pro Linux, de certa forma. Pelo Linux, tente pingar ele pra se certificar de que tudo ocorreu como devia. De novo: se não conseguir pingar a ponte, delete ela na VM e crie ela de novo.

Tente pingar o IP de alguém do Radmin pelo Linux. Você vai ver que não vai funcionar, mas deixe ping rodando. Ele vai ser útil depois.

Na VM, abra um terminal apertando Win + R, digitando "cmd" na janela que abrir, e pressionando Enter. No terminal, rode o seguinte comando:

```
netsh bridge show adapter
```

Você verá algo parecido com isso aqui:

![](/img/radmin-no-linux/new16.png)

A ordem e o nome dos itens não necessariamente será a mesma pra você, mas certifique-se de que:

- Há 2 itens;
- Um dos itens é a Placa do Radmin (provavelmente nomeada como "Radmin VPN");
- Um dos itens é a Placa Isolada.

Nessa lista, cada item possui um ID. Na imagem acima, o ID da Placa do Radmin é 1, e o da Placa Isolada é 2. Pra você, a ordem poderá ser diferente, com a Placa Isolada sendo 1, e a Placa do Radmin sendo 2.

À direita do nome de cada placa, você verá escrito "disabled" (desativado), se referindo ao modo de compatiblidade (compatibilidade com o quê exatamente, eu não sei) estando desativado. Para fazer com que o Linux consiga se conectar à pessoas no Radmin, é necessário habilitar esse modo de compatibilidade pra Placa do Radmin. Você fará isso com o seguinte comando, ainda na VM:

```
netsh bridge set adapter ID forcecompatmode=enable
```

Onde "ID" é o ID da Placa do Radmin.

Logo após rodar esse comando, a VM perderá momentaneamente a conexão com pessoas no Radmin, mas em poucos segundos volta ao normal. No lado do Linux, alguns segundos após aquele comando ser rodado, você poderá pingar o IP de pessoas no Radmin normalmente.

## 3. Configuração no Linux

Volte à janela principal do Virt Manager, com a lista de VMs criadas. Selecione qualquer uma delas, depois vá em Edit > Connection Details. Na janela que abrir, clique na aba escrito "Virtual Networks". Você verá a aquela mesma lista de placas de rede virtuais aqui. Selecione a sua Placa Isolada na lista da esquerda, e na parte da direita, clique na aba "XML". Você deverá ver isso aqui:

![](/img/radmin-no-linux/new17.png)

Na imagem, você verá "virbr1" selecionado. Esse nome em específico pode ser diferente pra você, mas basicamente, anote o valor depois de `bridge name=`. Esse é o nome que o Linux dá pra Placa Isolada que você criou. Geralmente esse nome é no formato "virbrX", X sendo um número qualquer.

Agora, rode o seguinte comando em um terminal no Linux:

```
sudo ip route add 255.255.255.255/32 dev PLACAISOLADA
```

Onde `PLACAISOLADA` é o valor que você acabou de anotar: o nome que o Linux deu pra Placa Isolada.

Esse comando basicamente redireciona qualquer conexão indo para, e vindo da sua Placa Isolada (que está "conectada" à Placa do Radmin na VM), para o Linux.

Pronto! Agora você pode se conectar normalmente com outros usuários do Radmin pelo Linux.

## 4. Usando o Radmin no dia-a-dia

Toda vez que você for ligar a VM pra usar o Radmin, você terá que refazer todo o processo de criar e configurar a ponte entre a Placa Isolada e a Placa do Radmin. Há duas formas de agilizar esse processo: Usando snapshots do Virt Manager, ou usando um batch script. Use o método que for melhor pra você.

### 4.1. Usando snapshots

Pra evitar de ter que fazer a mesma configuração toda hora, criaremos uma snapshot da VM depois da configuração ter sido feita nela. Comece clicando nesse botão com dois monitores:

![](/img/radmin-no-linux/new18.png)

Uma janela vazia deve abrir pra você. Basta agora clicar no botãozinho de "mais" no canto inferior esquerdo.

![](/img/radmin-no-linux/new19.png)

Isso vai abrir um pop-up, perguntando o nome que você quer dar pra snapshot, descrição, e um print da tela da VM.

![](/img/radmin-no-linux/new20.png)

Só clicar em "Finish", e pronto, você criou uma snapshot. Toda vez que você quiser carregar essa snapshot, clique no botão de "play", do lado daquele botãozinho de "mais".

Resumindo, pra ligar o Radmin usando esse método, você vai...

1. Ligar a VM;
2. Carregar a snapshot;
3. Esperar um pouco.

**Obs.: A snapshot não salva a data atual, e sim a data de quando você criou ela (se eu criei ela no dia 20, toda vez que eu carregar a snapshot, a VM vai estar presa no dia 20). Me disseram que isso pode causar uns problemas no Windows depois de um tempo, então é bom de vez em quando refazer a snapshot pra evitar problemas relacionados a data incorreta.**

### 4.2. Usando um batch script

A ideia desse script é fazer toda a configuração da ponte de uma vez só, depois de criar ela.

```
@echo off

netsh interface ipv4 set address name="Network Bridge" static 26.0.0.3 255.0.0.0 26.0.0.1 gwmetric=1500

netsh bridge show adapter

set /p "id=Digite o ID da Placa do Radmin: "

netsh bridge set adapter %id% forcecompatmode=enable
```

Esse script configura o IP e máscara de sub-rede da ponte, e ativa o modo de compatibilidade pra Placa do Radmin. Você pode salvá-lo na Área de Trabalho usando o próprio Bloco de Notas. Certifique-se de que o arquivo termina em ".bat" (Batchfile, ou arquivo em lotes), e não ".txt" (arquivo de texto comum), se não você não vai conseguir rodar ele.

**ADENDO**: No segundo comando (`netsh interface...`),  você verá que `name` está sendo definido como "Network Bridge". Esse é o que o Windows *em inglês* dá pra qualquer ponte criada por padrão. Se estiver usando Windows em outra língua, **mude esse nome pro que o seu Windows dá pras pontes, caso seja diferente de "Network Bridge"**.

Resumidamente, para usar o Radmin no Linux depois desse processo todo, o passo-a-passo é:

1. Abrir o Virt Manager;
2. Ligar a VM e logar;
3. Win + R, digita "ncpa.cpl" e dá Enter;
4. Deletar a ponte *e esperar o Radmin conectar*;
5. Criar a ponte, selecionando a Placa Isolada e a Placa do Radmin, clicando com o botão direito *na Placa do Radmin*, e clicar em "Bridge Connections";
6. Rodar o script acima;
7. Esperar um pouco.

E pronto. Como foi mencionado anteriormente, se quiser, agora você pode abaixar as configs da VM pra 1 núcleo de CPU e 512MB de RAM.

E é isso! Bom netplay a todos!

![](/img/radmin-no-linux/16.gif)
