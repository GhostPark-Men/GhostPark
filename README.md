# Atualizar Termux e instalar dependências
apt update -y && apt upgrade -y
pkg install -y git python-pip termux-api
pkg install -y sendemail

# Criar script Ghost Park
cat << 'EOF' > ghostpark.sh
#!/bin/bash

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

# Tornar o script executável
chmod +x ghostpark.sh

# Executar o script
./ghostpark.sh
