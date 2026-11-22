# Agent Smith — Guia do Usuário (PT-BR)

Firmware **Agent Smith** — um fork do firmware **[Bruce](https://github.com/pr3y/Bruce)** para o
**M5Stack Cardputer v1.1** (dispositivo portátil ESP32-S3 com teclado e tela de 240x135).

Este guia foi escrito a partir do código-fonte real do firmware. Ele documenta apenas as
funções que de fato existem no menu e nos módulos. Onde a tela ou o nome do item no menu está em
inglês, o nome original é mantido entre aspas para você encontrá-lo no dispositivo.

---

## AVISO LEGAL E DE RESPONSABILIDADE

> **Leia antes de usar.** As ferramentas deste firmware (deauth, Evil Portal, captura de
> handshake, beacon flood, BLE spam, jammers, sniffers, BadUSB, etc.) só podem ser usadas em
> **redes e dispositivos de sua propriedade** ou para os quais você tem **permissão explícita e
> por escrito** do responsável.
>
> - Interferir, interceptar, clonar ou negar serviço em redes, rádios ou dispositivos de
>   terceiros **sem autorização é ilegal** na maioria dos países.
> - Transmissores de RF/FM/jammers podem violar regras de telecomunicações (ANATEL no Brasil).
> - Este material é para **testes de segurança autorizados, pesquisa e educação**.
> - **Você é o único responsável** pelo uso que fizer do dispositivo e deste firmware. Os autores
>   do firmware e deste guia não se responsabilizam por uso indevido.

---

## 1. Controles do teclado do Cardputer

O Cardputer tem um teclado pequeno. A navegação usa teclas de atalho no lugar de setas físicas.

| Ação | Tecla | Observação |
|------|-------|------------|
| Cima (item anterior) | `;` | Também funciona com as setas `Fn` do teclado |
| Baixo (próximo item) | `.` | Também funciona com as setas `Fn` do teclado |
| Esquerda | `,` | Usado em telas com valores/laterais |
| Direita | `/` | Usado em telas com valores/laterais |
| Selecionar / Confirmar | `Enter` (OK) | Abre o item destacado / confirma |
| Voltar / Sair (Esc) | `` ` `` (crase, canto superior esquerdo) | Volta um nível no menu / cancela |

### Como navegar nos menus

1. Ligue o dispositivo. Aparece o **menu principal** com ícones das categorias.
2. Use `,` e `/` (ou `;`/`.`) para mover a seleção entre as categorias.
3. Pressione `Enter` para entrar na categoria.
4. Dentro de um submenu, use `;` (cima) e `.` (baixo) para escolher a ferramenta e `Enter` para
   abrir.
5. Pressione `` ` `` (crase) a qualquer momento para voltar ou cancelar.

### Como digitar (teclado virtual / entrada de texto)

Vários itens pedem texto (nome de rede, senha, nome de arquivo). Quando isso acontece:

- Digite normalmente nas teclas físicas do Cardputer.
- `Enter` confirma o texto.
- `` ` `` cancela (em muitas telas isso devolve um valor vazio e o firmware usa o padrão).
- Os dígitos e símbolos seguem o mapa de teclas do teclado (há suporte a troca de idioma do
  teclado em **Config → System Config → Keyboard Language**).

---

## 2. Visão geral do menu principal

A ordem das categorias no menu principal (conforme o firmware) é:

| # | Categoria (tela) | O que faz |
|---|------------------|-----------|
| 1 | **WiFi** | Conexão, ataques Wi-Fi, Evil Portal, sniffer, handshake |
| 2 | **Bluetooth** | Scan BLE, spam de notificações/pareamento, HID BLE |
| 3 | **RF** (Radio Frequency) | Captura/replay Sub-GHz, espectro, jammer (CC1101) |
| 4 | **NRF24** | Spectrum 2.4 GHz, MouseJack, jammer (módulo NRF24) |
| 5 | **LoRa** | Chat LoRa ponto a ponto |
| 6 | **FM** | Transmissão FM, espectro, RDS/TA |
| 7 | **Infrared** (IR) | TV-B-Gone, envio e gravação de códigos IR |
| 8 | **Ethernet** | Ataques em rede cabeada (módulo W5500) |
| 9 | **GPS** | Wardriving e GPS Tracker |
| 10 | **RFID** | Leitura/escrita/emulação de tags 13.56 MHz e 125 kHz |
| 11 | **Files** | Gerenciador de arquivos (SD, LittleFS, WebUI) |
| 12 | **Scripts** | Interpretador JavaScript (.js) |
| 13 | **Clock** (Relógio) | Relógio e temporizador |
| 14 | **Others** | QR Codes, microfone, BadUSB & HID, iButton |
| 15 | **Wire Tools** | UART/I2C/SPI, JTAG scanner, multímetro |
| 16 | **Config** | Configurações do dispositivo |

> Alguns itens só aparecem se o hardware externo correspondente estiver configurado (CC1101,
> NRF24, W5500, PN532, etc.) ou se a versão do firmware não for a "LITE".

---

## 3. WiFi

Categoria mais completa do firmware. Caminho base: **menu principal → WiFi**.

### 3.1 Conexão

| Item | O que faz |
|------|-----------|
| **Connect to Wifi** | Conecta a uma rede existente (modo estação). Lista redes ao redor; escolha e digite a senha. |
| **Start WiFi AP** | Cria um ponto de acesso próprio. Mostra a senha do AP (`bruceConfig.wifiAp.pwd`). |
| **Turn Off WiFi** | Desliga o rádio Wi-Fi. |
| **AP info** | Mostra informações da conexão/AP atual. |

### 3.2 Wifi Atks (ataques Wi-Fi)

Caminho: **WiFi → Wifi Atks**. Abre um submenu com:

#### Target Atks (ataques direcionados)
Faz um scan das redes ao redor e lista cada uma com RSSI, tipo de criptografia e canal. Ao
escolher uma rede, abre o **menu do alvo**:

| Opção do alvo | O que faz |
|---------------|-----------|
| **Information** | Mostra SSID, canal e BSSID (MAC) do alvo. |
| **Deauth** | Envia quadros de desautenticação (deauth) para derrubar clientes da rede alvo. |
| **Capture Handshake** | Captura o handshake WPA/WPA2 (ver seção 3.4). |
| **Clone Portal** | Sobe um Evil Portal com o nome (SSID) clonado do alvo. |
| **Deauth+Clone** | Faz deauth do AP real **e** sobe o portal clonado (incentiva a vítima a reconectar no clone). |
| **Deauth+Clone+Verify** | Igual ao anterior, mas tenta **validar** as credenciais capturadas conectando-se ao AP real. |

#### Karma Attack
Ataque Karma: responde às sondas (probe requests) dos clientes anunciando as redes que eles
procuram, atraindo-os para o dispositivo.

#### Beacon SPAM (beacon flood)
Transmite muitos beacons falsos, enchendo a lista de redes dos aparelhos próximos. Modos:

| Modo | Descrição |
|------|-----------|
| **Funny SSID** | Lista interna de nomes engraçados. |
| **Ricky Roll** | Nomes formando a letra do "Never Gonna Give You Up". |
| **Random SSID** | Nomes aleatórios. |
| **Single SSID** | Um nome base digitado por você, numerado (base1, base2, ...). |
| **Custom SSIDs** | Lê a lista de nomes de um arquivo **.txt** (um SSID por linha) do SD ou LittleFS. |

#### Deauth Flood
Escaneia **todas** as redes ao redor e envia deauth em massa para todas, alternando canais.
Mostra a taxa de quadros/segundo. Pressione `` ` `` para parar.

#### Enhanced Deauth
Deauth mais granular:

| Opção | Descrição |
|-------|-----------|
| **Station Deauth (Single)** | Deauth de um único cliente (estação) selecionado. |
| **Deauth All Clients** | Deauth de todos os clientes detectados. |
| **Deauth Target List** | Deauth a partir de uma lista de alvos. |

### 3.3 Evil Portal / Captive Portal (clonar SSID e capturar credenciais)

Caminho: **WiFi → Evil Portal** (ou pelas opções "Clone Portal" do Target Atks).

O Evil Portal sobe um ponto de acesso **aberto** com um nome (SSID) à sua escolha e um **portal
cativo**: qualquer dispositivo que conecta é redirecionado para uma página de login servida pelo
Cardputer. O que a vítima digita é salvo no cartão.

**Fluxo passo a passo:**

1. Entre em **WiFi → Evil Portal**.
2. Escolha o **template HTML**:
   - **Default** — página de login embutida no firmware.
   - **Custom Html** — carrega um arquivo **.html** do SD/LittleFS. O firmware pode ler o SSID do
     comentário `<!-- AP="NomeDaRede" -->` na primeira linha do HTML.
3. Escolha o **nome (SSID) do AP clonado**:
   - **Custom Wifi** — digita no teclado (padrão: "Free Wifi").
   - Ou escolhe um nome previamente salvo na lista **Evil Wifi** (ver 3.9).
4. Escolha o **IP de gateway** do portal:
   - **Default** (vem da config), **172.0.0.1** ou **192.168.4.1**.
5. O firmware sobe o AP aberto + servidor DNS (redireciona tudo para a página) + servidor web na
   porta 80.
6. Conforme vítimas conectam e enviam o formulário, as credenciais são gravadas no cartão.

**Onde ficam os dados capturados:**

- Pasta: **`/BruceEvilCreds/`** (no SD; se não houver SD, no LittleFS interno).
- Arquivo: **`<nome_do_ap>_creds.csv`** (ou `<nome_do_html>_creds.csv` para templates custom).
- Os dados são anexados (append) em formato CSV a cada envio.

**Endpoints do portal (configuráveis):**

| Endpoint | Função |
|----------|--------|
| `/` | Página do portal (login). |
| `/post` | Recebe o formulário com as credenciais. |
| `/creds` (GET) | Mostra as credenciais capturadas (pode ser desligado). |
| `/ssid` | Permite trocar o SSID do portal remotamente (pode ser desligado). |

As variantes **Clone Portal / Deauth+Clone / Deauth+Clone+Verify** (dentro de Target Atks)
automatizam o passo 2–4 usando o nome do alvo e, opcionalmente, fazem deauth do AP real ao mesmo
tempo e validam a senha.

### 3.4 Captura de handshake (WPA/WPA2)

Caminho: **WiFi → Wifi Atks → Target Atks → (escolher rede) → Capture Handshake**.

- Coloca o rádio em modo promíscuo no canal do alvo e envia rajadas de deauth para forçar
  clientes a reconectarem, capturando o **handshake de 4 vias (EAPOL)**.
- A tela mostra o progresso das mensagens **EAPOL MSG 1/2/3/4** (vermelho = faltando, verde =
  capturada) e o status: *Waiting / Scanning / Monitoring / CAPTURED!*.
- Pressione `Enter` (OK) para disparar um novo burst de deauth manualmente; o firmware também
  dispara automaticamente em intervalos.
- Ao completar, o status fica **CAPTURED!** e o arquivo é salvo.

**Onde ficam as capturas:**

- Pasta: **`/BrucePCAP/handshakes/`**.
- Arquivo: **`HS_<MAC>_<SSID>.pcap`** (ou `HS_<MAC>.pcap` quando o SSID não é resolvido).
- Esses `.pcap` podem ser analisados/quebrados em um PC (ex.: hashcat/aircrack) para testar a
  robustez da sua senha.

### 3.5 Sniffer / PCAP

Caminho: **WiFi → Sniffer**.

- Captura pacotes Wi-Fi em modo promíscuo e grava em arquivo **.pcap**.
- Capturas ficam na pasta **`/BrucePCAP/`** no cartão.
- Útil para auditar o tráfego da sua própria rede com Wireshark depois.

### 3.6 Outras ferramentas de rede WiFi

| Item | O que faz |
|------|-----------|
| **NetCut** | Corta/gerencia o acesso de hosts na rede (ARP). |
| **Listen TCP** | Abre uma porta TCP para escuta. |
| **Client TCP** | Conecta como cliente TCP a um host:porta. |
| **SOCKS4 Proxy** | Sobe um proxy SOCKS4 (porta 1080). |
| **TelNET** | Cliente Telnet. |
| **SSH** | Cliente SSH. |
| **Channel Analyzer** | Analisa ocupação dos canais Wi-Fi. |
| **Jam Detect** | Detecta possíveis jammers/interferência. |
| **SDR** | Função SDR (apenas ESP32-S3, se habilitada no build). |
| **Scan Hosts** | Faz varredura ARP dos hosts da rede conectada. |
| **Wireguard** | Túnel VPN WireGuard. |
| **Responder** | Responder LLMNR/NBNS (captura de credenciais em rede). |
| **Brucegotchi** | Modo tipo "pwnagotchi" (coleta passiva de handshakes). |
| **WiFite** | Fluxo automatizado de ataque Wi-Fi (ESP32-S3). |
| **WiFi Pass Recovery** | Recupera senhas Wi-Fi salvas no dispositivo. |

### 3.7 Configuração do WiFi (WiFi → Config)

| Item | O que faz |
|------|-----------|
| **Change MAC** | Altera o endereço MAC do rádio Wi-Fi. |
| **Add Evil Wifi** / **Remove Evil Wifi** | Gerencia a lista de nomes (SSIDs) prontos para o Evil Portal. |
| **SSH/Telnet Log ON/OFF** | Liga/desliga log de SSH/Telnet. |
| **Evil Wifi Settings** | Gateway IP, modo de senha, renomear/permitir endpoints `/creds` e `/ssid`, exibir endpoints. |
| **Hidden Networks: ON/OFF** | Inclui/ignora redes ocultas nos scans. |

---

## 4. Bluetooth (BLE)

Caminho: **menu principal → Bluetooth**.

| Item | O que faz |
|------|-----------|
| **Disconnect** | Desconecta o HID BLE ativo (aparece só quando conectado). |
| **Media Cmds** | Envia comandos de mídia (play/pause/volume) como dispositivo HID BLE. |
| **BLE Scan** | Escaneia e lista dispositivos BLE ao redor. |
| **iBeacon** | Emite um iBeacon (UUID da Apple). |
| **Bad BLE** | Executa scripts Ducky via HID **Bluetooth** (ver seção BadUSB). |
| **BLE Keyboard** | Usa o dispositivo como teclado BLE. |
| **BLE Spam** | Spam de notificações/pareamento (ver abaixo). |
| **BLE Suite** | Conjunto de ferramentas BLE. |
| **Ninebot** | Interage com patinetes Ninebot via BLE. |
| **Presenter mode** | Modo apresentador (controle de slides via HID BLE). |

### 4.1 BLE Spam (spam de notificações/pareamento)

Caminho: **Bluetooth → BLE Spam**.

Transmite anúncios BLE que disparam pop-ups de pareamento/notificação em aparelhos próximos.
Tipos de ataque disponíveis:

| Tipo | Alvo |
|------|------|
| **Apple Pairing Prompt** | Pop-up de pareamento iOS. |
| **Apple Action Modal** | Modal de ação iOS. |
| **Apple Not Your Device** | Alerta "não é seu dispositivo" iOS. |
| **Android Device Alert** | Alerta de dispositivo Android (FastPair). |
| **Windows Swift Pair** | Pop-up Swift Pair do Windows. |
| **Samsung BLE Spam** | Pop-ups de dispositivos Samsung (EasySetup/Buds). |
| **BLE Beacon Spam** | Spam genérico de beacons. |
| **Random / All** | Alterna entre todos os tipos. |

A interface permite ajustar **timing de advertising/gap**, **potência de transmissão (TX
power)**, **frequência de randomização de MAC** e mostra estatísticas de **pacotes/segundo** ao
vivo. Em dispositivos Android, há detecção automática de Samsung (por MAC OUI) para escolher o
FastPair correto.

---

## 5. RF (Radio Frequency / Sub-GHz)

Caminho: **menu principal → RF**. Requer módulo **CC1101** (ou os pinos TX/RX configurados). O
título do menu mostra o módulo/pinos em uso.

| Item | O que faz |
|------|-----------|
| **Scan/copy** | Escaneia uma frequência, detecta o sinal e permite copiar/salvar (controle de portão, etc.). |
| **ProtoPirate** | Decodificação/exploração de protocolos Sub-GHz. |
| **Record RAW** | Grava o sinal bruto (RAW) recebido. |
| **Custom SubGhz** | Envia um sinal Sub-GHz customizado. |
| **Spectrum** | Analisador de espectro Sub-GHz. |
| **RSSI Spectrum** | Espectro por RSSI. |
| **SquareWave Spec** | Espectro de onda quadrada. |
| **Spectogram** | Espectrograma (waterfall). |
| **Listen** | Escuta contínua de uma frequência. |
| **Bruteforce** | Força bruta de códigos (ex.: controles de garagem com código fixo). |
| **Jammer** | Jammer de RF (interferência). |

**Config (RF → Config):** `RF TX Pin`, `RF RX Pin`, `RF Module`, `RF Frequency`.

**Onde ficam as capturas:** arquivos **`.sub`** (formato compatível com Flipper) na pasta
**`/BruceRF/`** (capturas automáticas em `/BruceRF/autoSaved/`).

---

## 6. NRF24

Caminho: **menu principal → NRF24**. Requer módulo NRF24L01+.

| Item | O que faz |
|------|-----------|
| **Information** | Mostra info do módulo NRF24. |
| **Spectrum** | Analisador de espectro 2.4 GHz. |
| **MouseJack** | Explora teclados/mouses sem fio vulneráveis (injeção MouseJack). |
| **NRF Jammer** | Jammer 2.4 GHz. |
| **Config pins** | Seleciona o preset de pinos do módulo. |

---

## 7. Infrared (IR)

Caminho: **menu principal → Infrared**. O título mostra os pinos TX/Rx e o número de repetições.

| Item | O que faz |
|------|-----------|
| **TV-B-Gone** | Envia uma sequência de códigos de desligar TVs. Pergunta a região: **Region NA** (Américas/Ásia/resto) ou **Region EU** (Europa/Oriente Médio/Oceania). |
| **Custom IR** | Carrega e envia códigos salvos em arquivos **.ir** da pasta `/BruceIR`. |
| **IR Read** | Grava (captura) um código IR de um controle remoto e salva. |
| **IR Jammer** | Jammer de infravermelho (frequência ajustável). |

**Config (Infrared → Config):** `Ir TX Pin`, `Ir RX Pin`, `Ir TX Repeats`.

**Fluxo típico de clonar um controle:**
1. **IR Read** → aponte o controle para o receptor IR e aperte o botão a capturar.
2. Dê um nome ao sinal e salve (vai para `/BruceIR`).
3. Depois use **Custom IR** para reenviar o código salvo.

**Onde ficam os códigos:** pasta **`/BruceIR/`** (arquivos `.ir`).

---

## 8. Ethernet (rede cabeada)

Caminho: **menu principal → Ethernet**. Requer módulo **W5500**.

| Item | O que faz |
|------|-----------|
| **Scan Hosts** | Varredura ARP dos hosts na rede cabeada. |
| **DHCP Starvation** | Esgota o pool de endereços do servidor DHCP. |
| **MAC Flooding** | Inunda a tabela CAM do switch com MACs falsos. |

Se o W5500 não for encontrado, aparece "W5500 not found".

---

## 9. GPS

Caminho: **menu principal → GPS**. Requer módulo GPS (UART). O título mostra o baudrate.

| Item | O que faz |
|------|-----------|
| **Wardriving** | Mapeia redes com coordenadas GPS. Submodos: **Scan WiFi Networks**, **Scan BLE Devices**, **Scan Both**. |
| **GPS Tracker** | Registra a trilha de posição. |
| **Config** | `Baudrate` e `GPS Pins`. |

**Onde ficam os dados:** pasta **`/BruceWardriving/`**, em arquivos CSV
(`<timestamp>_wardriving.csv`) no formato WiGLE, prontos para upload no wigle.net.

---

## 10. RFID

Caminho: **menu principal → RFID**. Suporta módulos PN532, RC522, ST25R3916, M5 RFID2, etc. O
título mostra o módulo em uso.

| Item | O que faz |
|------|-----------|
| **Read tag** | Lê uma tag 13.56 MHz (Tag-O-Matic). |
| **Read EMV** | Lê dados de cartão EMV (contactless). |
| **Read 125kHz** | Lê tags de baixa frequência (125 kHz). |
| **Scan tags** | Modo de varredura contínua de tags. |
| **Load file** | Carrega um dump de tag salvo. |
| **Erase data** | Apaga dados de uma tag gravável. |
| **Emulate NDEF** | Emula uma mensagem NDEF (módulos com emulação). |
| **Write NDEF** | Grava uma mensagem NDEF em tag. |
| **Amiibolink** | Ferramenta para Amiibo. |
| **Chameleon** | Integração com dispositivos Chameleon. |
| **PN532 BLE** / **PN532 UART** | Interface com leitores PN532 externos. |
| **SRIX Tool** | Ferramenta SRIX (quando PN532 em modo I2C). |

**Config (RFID → Config):** `RFID Module` (seleciona o hardware) e `Add MIF Key` (adiciona chave
Mifare).

---

## 11. Files (gerenciador de arquivos)

Caminho: **menu principal → Files**.

| Item | O que faz |
|------|-----------|
| **SD Card** | Navega nos arquivos do cartão microSD. |
| **LittleFS** | Navega no armazenamento interno (flash). |
| **WebUI** | Sobe uma interface web para gerenciar arquivos pelo navegador. |
| **Connect** | Transferência entre dispositivos Bruce: Send/Recv File, Send/Recv Cmds. |
| **Mass Storage** | Expõe o cartão como pendrive USB (onde suportado). |

---

## 12. Scripts (JavaScript)

Caminho: **menu principal → Scripts**.

- Lista os scripts **.js** disponíveis e a opção **Load...** para abrir um arquivo.
- O firmware traz um interpretador JavaScript com APIs para Wi-Fi, BLE, IR, RF, GPIO, display,
  teclado, armazenamento, etc. (permite automatizar tarefas e criar ferramentas próprias).

---

## 13. Clock (Relógio)

Caminho: **menu principal → Clock**.

- Mostra um relógio em tela cheia. `` ` `` sai.
- `Enter` abre o submenu com **Timer** (temporizador), **Back to Clock** e **Exit**.

---

## 14. Others (diversos)

Caminho: **menu principal → Others**.

| Item | O que faz |
|------|-----------|
| **QRCodes** | Gera e exibe QR Codes. |
| **Megalodon** | Ferramenta de monitor/captura Wi-Fi ("shark"). |
| **Microphone** | Submenu: **Spectrum** (visualiza áudio) e **Record** (grava). |
| **BadUSB & HID** | Submenu de BadUSB e HID USB (ver abaixo). |
| **iButton** | Lê/emula chaves iButton (1-Wire). |

### 14.1 BadUSB (scripts Ducky)

Caminho: **Others → BadUSB & HID → BadUSB** (via USB) ou **Bluetooth → Bad BLE** (via HID BLE).

O BadUSB executa **scripts Ducky** que fazem o Cardputer se passar por um teclado e digitar
comandos no computador alvo.

**Como rodar:**
1. Abra **Others → BadUSB & HID → BadUSB**.
2. Escolha a origem do script: **SD Card** ou **LittleFS** (ou **Main Menu** para cancelar).
3. Selecione o arquivo de script na lista.
4. O firmware prepara o HID USB; conecte o Cardputer ao computador alvo via USB.
5. Pressione `Enter` (OK) para iniciar a execução.
6. Ao final aparece "Finished"; `Enter` reinicia o mesmo script.

**De onde:** os scripts ficam no **cartão SD** (ou no LittleFS interno). Você pode organizá-los
em subpastas.

**Formato do arquivo:** texto comum, tipicamente com extensão **.txt** (também se usa `.ducky`).
É a sintaxe Ducky Script, uma instrução por linha. Comandos básicos comuns:

| Comando | Função |
|---------|--------|
| `REM comentário` | Comentário (ignorado). |
| `STRING texto` | Digita o texto. |
| `DELAY 1000` | Espera (em milissegundos). |
| `ENTER`, `TAB`, `ESC`, `GUI r`, `CTRL ALT DEL` | Teclas e combinações. |
| `REPEAT n` | Repete a linha anterior n vezes. |

Outros itens do submenu **BadUSB & HID**: **USB Keyboard** (teclado USB manual), **USB Clicker** e
**USB U2F** (quando o build tem `USB_as_HID`).

---

## 15. Wire Tools

Caminho: **menu principal → Wire Tools**.

| Item | O que faz |
|------|-----------|
| **UART Terminal** | Terminal serial UART. |
| **I2C Bus** | Scanner/ferramentas de barramento I2C. |
| **SPI Bus** | Ferramentas de barramento SPI. |
| **JTAG Scanner** | Localiza pinos JTAG. |
| **Multimeter** | Testador de continuidade / multímetro simples. |

---

## 16. FM

Caminho: **menu principal → FM**. Requer módulo transmissor FM (Si4713).

| Item | O que faz |
|------|-----------|
| **Brdcast std** | Transmite FM em frequência padrão. |
| **Brdcast rsvd** | Transmite em frequência reservada. |
| **Brdcast stop** | Para a transmissão. |
| **FM Spectrum** | Espectro FM. |
| **Hijack TA** | Manipula o bit TA (Traffic Announcement) do RDS. |

---

## 17. LoRa

Caminho: **menu principal → LoRa**. Requer módulo LoRa.

| Item | O que faz |
|------|-----------|
| **Chat** | Chat de texto ponto a ponto via LoRa. |
| **Change username** | Define seu nome de usuário no chat. |
| **Change Frequency** | Ajusta a frequência LoRa. |

---

## 18. Config (configurações)

Caminho: **menu principal → Config**.

### 18.1 Display & UI
| Item | O que faz |
|------|-----------|
| **Brightness** | Brilho da tela. |
| **Dim Time** | Tempo para escurecer a tela (economia). |
| **Orientation** | Rotação da tela. |
| **Menu Layout** | Estilo do menu (lista/grade). |
| **UI Color** | Cor da interface. |
| **UI Theme** | Tema visual (o Agent Smith usa o tema verde). |

### 18.2 LED Config (se houver LED RGB)
Cor, efeito, brilho e **LED Blink: ON/OFF** do LED.

### 18.3 Audio Config
**Sound: ON/OFF** e **Sound Volume** (quando há alto-falante).

### 18.4 System Config
| Item | O que faz |
|------|-----------|
| **InstaBoot: ON/OFF** | Pula a animação de boot. |
| **WiFi Startup: ON/OFF** | Liga o Wi-Fi ao iniciar. |
| **Startup App** | App a abrir no boot. |
| **Hide/Show Apps** | Oculta/mostra categorias no menu. |
| **Clock** | Ajusta o relógio. |
| **Keyboard Language** | Idioma do teclado. |
| **Advanced** | Ver abaixo. |

**Advanced:** `Set Device pins` (pinos de todos os módulos: CC1101, NRF24, LoRa, SDCard, I2C,
UART, GPS, BadUSB, speaker/buzzer, mic), `Toggle BLE API`, `BadUSB/BLE`, `BLE name`, `Network
Creds` e **Factory Reset** (com confirmação — apaga tudo).

### 18.5 Power
**Deep Sleep**, **Sleep**, **Restart** e **Power Off** (com confirmação).

### 18.6 Outros em Config
**Install App Store** (instala a loja de apps JS), **Dev Mode** (modo desenvolvedor: alterna
serial USB/UART) e **About** (informações do dispositivo).

### 18.7 Pinos de IR e RF (resumo rápido)

- **Pinos de IR:** **Infrared → Config** (`Ir TX Pin`, `Ir RX Pin`, `Ir TX Repeats`).
- **Pinos de RF:** **RF → Config** (`RF TX Pin`, `RF RX Pin`, `RF Module`, `RF Frequency`).
- **Pinos dos módulos SPI/I2C/UART:** **Config → System Config → Advanced → Set Device pins**.

---

## 19. Onde ficam os arquivos no cartão SD

O firmware grava automaticamente em pastas próprias no **microSD** (ou no **LittleFS** interno
quando não há cartão). Principais locais:

| Pasta / arquivo | Conteúdo |
|-----------------|----------|
| **`/BruceEvilCreds/`** | Credenciais capturadas pelo Evil Portal (`<ap>_creds.csv`). |
| **`/BrucePCAP/`** | Capturas do sniffer Wi-Fi (`.pcap`). |
| **`/BrucePCAP/handshakes/`** | Handshakes WPA/WPA2 capturados (`HS_<MAC>_<SSID>.pcap`). |
| **`/BruceRF/`** | Sinais Sub-GHz capturados (`.sub`). Auto-salvos em `/BruceRF/autoSaved/`. |
| **`/BruceIR/`** | Códigos de infravermelho gravados/carregados (`.ir`). |
| **`/BruceWardriving/`** | Logs de wardriving em CSV (formato WiGLE). |
| **Scripts** (`.js`) | Ficam no cartão e aparecem em **Scripts**. |
| **BadUSB** (`.txt`/`.ducky`) | Ficam no cartão/LittleFS; escolhidos em **Others → BadUSB & HID → BadUSB**. |
| **Templates do Evil Portal** (`.html`) | No cartão; selecionados em **Evil Portal → Custom Html**. |
| **Configurações** | O firmware salva as preferências em arquivo de config próprio no armazenamento. |

**Dicas de uso do cartão:**
- Use um microSD formatado em FAT32.
- Faça backup regular das pastas `/BruceEvilCreds`, `/BrucePCAP` e `/BruceRF` antes de testes.
- Os `.pcap` de handshake são analisados em um PC; os `.sub` e `.ir` podem ser reenviados pelo
  próprio dispositivo.

---

## Lembrete final

Use apenas em **ambiente autorizado**. Documente o escopo e a permissão antes de qualquer teste.
A responsabilidade pelo uso é inteiramente sua.
