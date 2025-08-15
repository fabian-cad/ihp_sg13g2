# Tutorial de instalação do PDK IHP 130 nm no Ubuntu 24.04
**Autor:** Prof. Fabián Olivera  

Este tutorial foi testado no **Ubuntu 24.04 (na [WSL](https://learn.microsoft.com/pt-br/windows/wsl/))** desde o primeiro acesso como usuário com permissões de `sudo`. Ele deveria funcionar também no **Ubuntu nativo** ou em **máquinas virtuais Ubuntu** (VirtualBox, VMware), podendo haver a necessidade de instalar bibliotecas adicionais conforme versão do ambiente. No caso de não estar usando WSL, começar o tutorial no Passo 2.

## 1. Instalação da WSL e distribuição Ubuntu
No **PowerShell** do Windows, instale o Ubuntu 24.04 na WSL 2:

```powershell
wsl --install -d Ubuntu-24.04
```

Durante a instalação, será solicitado criar **nome de usuário** e **senha** no Linux.  

Para abrir o Ubuntu diretamente no **home** do usuário:

```powershell
wsl ~ -d Ubuntu-24.04
```

---

## 2. Instalação das ferramentas dentro do Ubuntu

Agora dentro do Ubuntu (WSL, nativo ou VM), execute os comandos abaixo.

### 2.1 Atualização e instalação de dependências
```bash
sudo apt update
sudo apt install -y gedit build-essential flex bison libx11-dev \
libxpm-dev libxext-dev libxft-dev tcl-dev tk-dev autoconf libtool \
libxaw7-dev libreadline-dev xterm libqt5designer5 libqt5multimedia5 \
libqt5opengl5t64 libqt5multimediawidgets5 libqt5printsupport5t64 \
libqt5sql5t64 libqt5xmlpatterns5 ruby ruby-dev libgit2-dev python3-venv vim-gtk3
```
Prepara ambiente Python virtual para ferramentas
```bash
python3 -m venv $HOME/.venv
echo "if [ -f "$HOME/.venv/bin/activate" ]; then" >> ~/.bashrc
echo "source "$HOME/.venv/bin/activate"" >> ~/.bashrc
echo "fi" >> ~/.bashrc
source ~/.bashrc
pip install --upgrade pip
pip install psutil
```
Crie o diretório para as ferramentas de microeletrônica:
```bash
mkdir ~/cad
```

---

### 2.2 Instalação e configuração do **PDK IHP 130 nm**
```bash
cd ~/cad
git clone --recursive https://github.com/IHP-GmbH/IHP-Open-PDK.git
cd IHP-Open-PDK
git checkout dev
echo "export PDK_ROOT=\$HOME/cad/IHP-Open-PDK" >> ~/.bashrc
echo "export PDK=ihp-sg13g2" >> ~/.bashrc
echo "export KLAYOUT_PATH=\"\$HOME/.klayout:\$PDK_ROOT/\$PDK/libs.tech/klayout\"" >> ~/.bashrc
echo "export KLAYOUT_HOME=\$HOME/.klayout" >> ~/.bashrc
source ~/.bashrc
```

---

### 2.3 Instalação do **OpenVAF**
```bash
cd ~/cad
wget https://openva.fra1.cdn.digitaloceanspaces.com/openvaf_23_5_0_linux_amd64.tar.gz
tar -xzf openvaf_23_5_0_linux_amd64.tar.gz
sudo mv openvaf /usr/local/bin/
sudo chmod +x /usr/local/bin/openvaf
rm openvaf_23_5_0_linux_amd64.tar.gz
```

---

### 2.4 Instalação do **Xschem**
```bash
cd ~/cad
git clone https://github.com/StefanSchippers/xschem.git
cd xschem
./configure --prefix=/usr/local
make
sudo make install
cd $PDK_ROOT/ihp-sg13g2/libs.tech/xschem/
python3 install.py
cd ~/cad
rm -rf xschem
mkdir -p ~/.xschem
cp $PDK_ROOT/$PDK/libs.tech/xschem/xschemrc ~/.xschem/xschemrc
```
> **Para abrir o Xschem:**
> ```bash
> xschem &
> ```
---

### 2.5 Instalação do **Ngspice**
```bash
cd ~/cad
git clone https://git.code.sf.net/p/ngspice/ngspice ngspice
cd ngspice
./autogen.sh
./configure --enable-osdi --prefix=/usr/local
make
sudo make install
cd ..
rm -rf ngspice
```

---

### 2.6 Instalação do **Magic**
```bash
cd ~/cad
git clone git://opencircuitdesign.com/magic
cd magic
./configure --prefix=/usr/local
make
sudo make install
cd ..
rm -rf magic
```
> **Para abrir o Magic:**
> ```bash
> magic -rcfile $PDK_ROOT/$PDK/libs.tech/magic/$PDK.magicrc &
> ```

---

### 2.7 Instalação do **KLayout**
```bash
cd ~/cad
wget https://www.klayout.org/downloads/Ubuntu-24/klayout_0.30.3-1_amd64.deb
sudo dpkg -i klayout_0.30.3-1_amd64.deb
rm klayout_0.30.3-1_amd64.deb
```
> **Para abrir o KLayout:**
> ```bash
> klayout -e &
> ```

---

## 3. Instalação de ferramentas complementares

### 3.1 Instalação do **Visual Studio Code**
Vscode pode ser utilizado para implementar scripts de projeto e otimização de circuitos
microeletrônicos. No terminal, execute os seguintes comando para instalar a última versão
do vscode.
```bash
cd ~/cad
wget -O vscode.deb 'https://code.visualstudio.com/sha/download?build=stable&os=linux-deb-x64'
sudo dpkg -i vscode.deb
rm vscode.deb
```
> **Para abrir o Visual Studio Code:**
> ```bash
> code &
> ```

