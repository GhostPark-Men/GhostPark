#!/data/data/com.termux/files/usr/bin/bash

# Atualizar pacotes do Termux
echo "[✔] Atualizando Termux..."
apt update -y && apt upgrade -y

# Instalar pacotes necessários
echo "[✔] Instalando dependências..."
pkg install -y git termux-api sendemail

# Clonar o repositório GhostPark
echo "[✔] Baixando arquivos do Ghost Park..."
rm -rf GhostPark
git clone https://github.com/GhostPark-Men/GhostPark.git

# Entrar na pasta
cd GhostPark || { echo "❌ Falha ao acessar diretório GhostPark"; exit 1; }

# Criar script ghostpark.sh dentro da pasta
echo "[✔] Criando o script ghostpark.sh..."

cat << 'EOF' > ghostpark.sh
#!/data/data/com.termux/files/usr/bin/bash

clear

# Banner
echo "╔═══════════════════════╗"
echo "║     GHOST PARK        ║"
echo "╚═══════════════════════╝"
echo

# Entrada de dados
read -p "Digite o Gmail que VAI ENVIAR: " EMAIL_REMETENTE
read -sp "Digite a senha desse Gmail: " SENHA
echo
read -p "Digite o Gmail que VAI RECEBER: " EMAIL_DESTINO
read -p "Digite a mensagem a ser enviada: " MENSAGEM
read -p "Quantas vezes enviar? (máx. 100): " VEZES

# Validação de limite
if [ "$VEZES" -gt 100 ]; then
  echo "Máximo permitido é 100. Usando 100."
  VEZES=100
fi

echo
echo "Enviando mensagens..."
echo

for ((i=1; i<=VEZES; i++))
do
  sendemail -f "$EMAIL_REMETENTE" \
            -t "$EMAIL_DESTINO" \
            -u "Mensagem $i de Ghost Park" \
            -m "$MENSAGEM" \
            -s smtp.gmail.com:587 \
            -o tls=yes \
            -xu "$EMAIL_REMETENTE" \
            -xp "$SENHA"

  echo "Mensagem $i enviada."
done

echo "✔️ Todas mensagens foram enviadas com sucesso."
EOF

# Tornar executável
chmod +x ghostpark.sh

# Rodar script
echo "[✔] Iniciando Ghost Park..."
./ghostpark.sh
