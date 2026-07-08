## Execução com Docker Compose

Este projeto também pode ser executado utilizando Docker Compose, sem necessidade de instalação manual do Python, Node.js ou PostgreSQL.

### Arquitetura

A solução foi organizada com os seguintes serviços:

NGINX: responsável por servir a aplicação React, atuar como proxy reverso e realizar o balanceamento de carga entre as instâncias do backend.
Backend 1 (Flask): primeira instância da aplicação Python.
Backend 2 (Flask): segunda instância da aplicação Python utilizada para balanceamento de carga.
PostgreSQL: banco de dados utilizado para armazenamento das informações do jogo em volume persistente.

O armazenamento do banco de dados é realizado em um volume Docker dedicado, garantindo persistência mesmo após a recriação dos containers.

### Execução

Na raiz do projeto, execute:

docker compose up --build

Após a inicialização dos serviços, a aplicação estará disponível em:

http://localhost
Atualização dos Componentes

A estrutura foi organizada para permitir a atualização independente dos componentes apenas alterando a versão da imagem correspondente.

Exemplos:

Atualizar o Python modificando a imagem base do Dockerfile-back.
Atualizar o Node.js ou o NGINX modificando a imagem base do Dockerfile-front.
Atualizar o PostgreSQL alterando a tag da imagem no docker-compose.yml.

Essa abordagem evita alterações no código da aplicação e facilita futuras manutenções.

### Decisões de Projeto

As seguintes decisões foram adotadas durante a implementação da infraestrutura:

A aplicação original não foi modificada.
O NGINX é responsável por servir os arquivos estáticos do React e realizar o proxy reverso para o backend.
O balanceamento de carga é realizado utilizando o algoritmo Round Robin padrão do NGINX entre duas instâncias do backend.
O PostgreSQL utiliza um volume persistente para armazenamento dos dados.
A configuração da aplicação é realizada por variáveis de ambiente definidas no docker-compose.yml, preservando o código-fonte original da aplicação.
O script start-backend.sh foi mantido inalterado. Durante a execução com Docker Compose, a inicialização do backend é realizada diretamente pelo container, utilizando as variáveis de ambiente fornecidas pelo Compose, evitando alterações na aplicação original.

-------------------------------------------------------------------------------------------------------------------------------------

# Jogo de Adivinhação com Flask

Este é um simples jogo de adivinhação desenvolvido utilizando o framework Flask. O jogador deve adivinhar uma senha criada aleatoriamente, e o sistema fornecerá feedback sobre o número de letras corretas e suas respectivas posições.

## Funcionalidades

- Criação de um novo jogo com uma senha fornecida pelo usuário.
- Adivinhe a senha e receba feedback se as letras estão corretas e/ou em posições corretas.
- As senhas são armazenadas  utilizando base64.
- As adivinhações incorretas retornam uma mensagem com dicas.
  
## Requisitos

- Python 3.8+ - 3.12
- Flask
- Um banco de dados local (ou um mecanismo de armazenamento configurado em `current_app.db`)
- node 18.17.0

## Instalação

1. Clone o repositório:

   ```bash
   git clone https://github.com/fams/guess_game.git
   cd guess-game
   ```

2. Crie um ambiente virtual e ative-o:

   ```bash
   python3 -m venv venv
   source venv/bin/activate  # Linux/Mac
   venv\Scripts\activate  # Windows
   ```

3. Instale as dependências:

   ```bash
   pip install -r requirements.txt
   ```

4. Configure o banco de dados com as variáveis de ambiente no arquivo start-backend.sh
    1. Para sqlite

        ```bash
            export FLASK_APP="run.py"
            export FLASK_DB_TYPE="sqlite"            # Use SQLITE
            export FLASK_DB_PATH="caminho/db.sqlite" # caminho do banco
        ```

    2. Para Postgres

        ```bash
            export FLASK_APP="run.py"
            export FLASK_DB_TYPE="postgres"       # Use postgres
            export FLASK_DB_USER="postgres"       # Usuário do banco
            export FLASK_DB_NAME="postgres"       # Nome do Banco
            export FLASK_DB_PASSWORD="secretpass" # Senha do banco
            export FLASK_DB_HOST="localhost"      # Hostname
            export FLASK_DB_PORT="5432"           # Porta
        ```

    3. Para DynamoDB

        ```bash
        export FLASK_APP="run.py"
        export FLASK_DB_TYPE="dynamodb"       # Use postgres
        export AWS_DEFAULT_REGION="us-east-1" # AWS region
        export AWS_ACCESS_KEY_ID="FAKEACCESSKEY123456" 
        export AWS_SECRET_ACCESS_KEY="FakeSecretAccessKey987654321"
        export AWS_SESSION_TOKEN="FakeSessionTokenABCDEFGHIJKLMNOPQRSTUVXYZ1234567890"
        ```

5. Execute o backend

   ```bash
   ./start-backend.sh &
   ```

6. Cuidado! verifique se o seu linux está lendo o arquivo .sh com fim de linha do windows CRLF. Para verificar utilize o vim -b start-backend.sh

## Frontend
No diretorio de frontend

1. Instale o node com o nvm. Se não tiver o nvm instalado, siga o [tutorial](https://github.com/nvm-sh/nvm?tab=readme-ov-file#installing-and-updating)

    ```bash
    nvm install 18.17.0
    nvm use 18.17.0
    # Habilite o yarn
    corepack enable
    ```

2. Instale as dependências do node com o npm:

    ```bash
    npm install
    ```

3. Exporte a url onde está executando o backend e execute o backend.

   ```bash
    export REACT_APP_BACKEND_URL=http://localhost:5000
    yarn start
   ```

## Como Jogar

### 1. Criar um novo jogo

Acesse a url do frontend http://localhost:3000

Digite uma frase secreta

Envie

Salve o game-id


### 2. Adivinhar a senha

Acesse a url do frontend http://localhost:3000

Vá para o endponint breaker

entre com o game_id que foi gerado pelo Creator

Tente adivinhar

## Estrutura do Código

### Rotas:

- **`/create`**: Cria um novo jogo. Armazena a senha codificada em base64 e retorna um `game_id`.
- **`/guess/<game_id>`**: Permite ao usuário adivinhar a senha. Compara a adivinhação com a senha armazenada e retorna o resultado.

### Classes Importantes:

- **`Guess`**: Classe responsável por gerenciar a lógica de comparação entre a senha e a tentativa do jogador.
- **`WrongAttempt`**: Exceção personalizada que é levantada quando a tentativa está incorreta.



## Melhorias Futuras

- Implementar autenticação de usuário para salvar e carregar jogos.
- Adicionar limite de tentativas.
- Melhorar a interface de feedback para as tentativas de adivinhação.

## Licença

Este projeto está licenciado sob a [MIT License](LICENSE).

