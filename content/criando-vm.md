---
title: "Criando uma VM no Virt Manager"
draft: true
header: true
---

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

Aliás, esse processo todo é pra evitar do armazenamento da VM consumir *toda* a partição pro diretório raíz, que é onde o Virt Manager normalmente salva os volumes das suas VMs. A minha partição pro diretório raíz só tem 30GB, que é só metade do espaço suficiente pra armazenar o volume da VM.

## Finalizando

Finalmente, o Virt Manager vai mostrar uma janela assim:

![](/img/radmin-no-linux/vm12.png)

Se certifique de que as configurações estão de acordo com o que está na imagem, exceto "Storage" que pode variar de sistema pra sistema, e "OS" que varia dependendo de qual versão do Windows vocẽ está instalando. No nome você pode botar qualquer coisa.

Clique pra finalizar. Se essa é a sua primeira vez instalando uma VM, ele provavelmente vai abrir uma janela assim:

![](/img/radmin-no-linux/vm13.png)

Só clicar em "Yes" pra ativar a placa de rede virtual.

Depois disso, ele vai abrir uma janela com a VM em si. Instale o Windows normalmente, e [prossiga com o guia](/radmin-no-linux.html#2-criando-uma-vm-de-windows).
