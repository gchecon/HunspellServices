# HunspellServices
Criação de Serviços para Análise de Qualidade da Escrita em Português

### 1. Instalar e Configurar Hunspell na Máquina Virtual

1. **Instale o Hunspell**:
   - Normalmente em distribuições Linux você pode instalar facilmente usando o gerenciador de pacotes, como `sudo apt-get install hunspell`.

2. **Instale os Dicionários Necessários**:
   - Baixe e instale os dicionários para os idiomas que você deseja verificar, como o português brasileiro.

### 2. Criar um Servidor Hunspell

1. **Crie um Script de Servidor**:
   - Escreva um script em Python ou outra linguagem que inicie um servidor (por exemplo, um servidor HTTP ou um servidor de socket simples) que aceita textos para verificação ortográfica e retorna os resultados.
   - Este servidor usará o Hunspell localmente na máquina virtual para realizar as verificações.

### 3. Conectar-se ao Servidor Hunspell da Máquina Windows

1. **Descubra o Endereço IP da Máquina Virtual**:
   - Na máquina virtual, você pode usar comandos como `ifconfig` (Linux) para descobrir o endereço IP.

2. **Crie um Script Cliente no Windows**:
   - No seu sistema Windows, escreva um script Python que se conecta ao servidor Hunspell na máquina virtual usando o endereço IP encontrado.
   - Este script enviará textos para verificação e receberá os resultados.

### Exemplo de um Simples Servidor Hunspell

Aqui está um exemplo básico de como um servidor Hunspell pode ser implementado em Python:

```python
from http.server import BaseHTTPRequestHandler, HTTPServer
import hunspell
import json

class RequestHandler(BaseHTTPRequestHandler):
    def do_POST(self):
        content_length = int(self.headers['Content-Length'])
        post_data = self.rfile.read(content_length)
        text = json.loads(post_data)

        # Substitua pelos caminhos corretos para os arquivos de dicionário
        h = hunspell.HunSpell('/path/to/dic/file.dic', '/path/to/aff/file.aff')
        results = {'corrections': {}}

        for palavra in text.split():
            if not h.spell(palavra):
                results['corrections'][palavra] = h.suggest(palavra)

        self.send_response(200)
        self.send_header('Content-type', 'application/json')
        self.end_headers()
        self.wfile.write(json.dumps(results).encode('utf-8'))

# Substitua '0.0.0.0' pelo IP da máquina virtual se necessário
server_address = ('0.0.0.0', 8080)
httpd = HTTPServer(server_address, RequestHandler)
httpd.serve_forever()
