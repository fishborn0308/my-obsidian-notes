### 1. **バックアップとリストアの手順書**

手順書をGitで管理する場合、以下のようなポイントを抑えておくと効果的です：

* **構成ファイルの管理**: `.zshrc` や `~/.tmux.conf`、`~/.bashrc` などの個別の設定ファイルをGitで管理すると、設定が変更された際に履歴として残り、いつでも復元できます。さらに、必要なソフトウェアパッケージ（`zoxide`、`fzf`、`tmux` など）やインストール手順を`README.md`や専用のスクリプトファイルにまとめておくと便利です。

  **例:**

  * `~/configs/zsh/.zshrc`
  * `~/configs/tmux/.tmux.conf`
  * `~/configs/git/.gitconfig`

* **バックアップの対象**:

  * 必要な設定ファイルやツール（`~/.zshrc`, `~/Tools/`, `~/Vault/`, `~/Workbench/`）をリスト化し、それらをバックアップ対象として管理します。
  * また、リストア時に必要なパッケージや依存関係（Kali Linuxの必要なツール、`apt install` でインストールするツールなど）も記載しておくとさらに完璧です。

* **Gitフロー**:

  * バックアップ用のリポジトリを作成し、その中に手順書を格納します。Git管理下に置くことで、設定変更履歴を追跡でき、環境の同期も容易になります。
  * リモートリポジトリ（例: GitHub、GitLab）にバックアップすることで、ローカルの環境が壊れても復元できる点が安心です。

### 2. **初期構成スクリプト**

初期構成をスクリプト化することは、再セットアップや環境移行を非常にスムーズにします。以下の点に気をつけて、スクリプトを作成してみましょう。

* **依存ツールのインストール**

  * 必要なパッケージやツール（`zsh`、`tmux`、`fzf` など）を一括インストールするスクリプトを作成します。例えば、以下のようにパッケージをインストールするスクリプト：

    ```bash
    #!/bin/bash
    # 必要なツールをインストール
    sudo apt update
    sudo apt install -y zsh tmux fzf zoxide flameshot zsh-autosuggestions zsh-syntax-highlighting
    sudo apt install -y seclists nmap dirb gobuster john hashcat
    ```

* **設定ファイルのコピー**

  * `.zshrc` や `.tmux.conf` などの設定ファイルを所定の場所にコピーするスクリプトも有効です。例えば、Gitからこれらをクローンして、`~/.zshrc` に配置するコマンドなどです。

    ```bash
    #!/bin/bash
    # Gitリポジトリから設定ファイルをクローンして配置
    git clone https://github.com/your-username/your-configs.git ~/configs
    cp ~/configs/zsh/.zshrc ~/.zshrc
    cp ~/configs/tmux/.tmux.conf ~/.tmux.conf
    ```

* **ディレクトリ構成の自動作成**

  * あなたが作成したディレクトリ構成を自動で作成するスクリプトも役立ちます。特に、`~/Vault/`, `~/Tools/`, `~/Workbench/` などの作成をスクリプトで行えば、どのマシンでも同じ構成を再現できます。

    ```bash
    #!/bin/bash
    # 必要なディレクトリを作成
    mkdir -p ~/Vault/Target/{assets,result,log}
    mkdir -p ~/Tools/{Git,Python,C#,Powershell}
    mkdir -p ~/Workbench/{Recon,AD/enumeration,AD/attacks,Web,Exploit,Wordlists}
    mkdir -p ~/Transfer/{RevShell,PrivEsc,PostEx,Pivoting}/{Linux,Windows}
    ```

* **tmuxセッションの設定**

  * tmuxのセッションを自動で作成するスクリプトを作るのも良いアイデアです。例えば、`tmux` セッションを自動で作成し、必要なウィンドウを構成するコマンドをスクリプト化します。

    ```bash
    #!/bin/bash
    # tmuxセッション作成
    tmux new-session -d -s "OSCP"
    tmux new-window -t OSCP:1 -n "Recon"
    tmux new-window -t OSCP:2 -n "Exploit"
    tmux new-window -t OSCP:3 -n "PrivEsc"
    tmux attach -t OSCP
    ```

* **スクリプトを実行するための手順**

  * 初期設定をスムーズに実行できるように、すべてのスクリプトを一つにまとめたランチャースクリプト（`setup.sh`）を作成し、あらかじめ必要な準備を整えておくと非常に便利です。

    ```bash
    #!/bin/bash
    # 初期構成スクリプトを一括実行
    ./install_tools.sh
    ./setup_config.sh
    ./create_directories.sh
    ./setup_tmux.sh
    ```

### 3. **Git運用のポイント**

* **コミットメッセージ**: 設定や環境の変更をGitで管理する際、コミットメッセージには「何を」「どのように」変更したのかを明記すると、後で追いやすくなります。

  例:

  * `Added zsh-autosuggestions plugin to .zshrc`
  * `Updated tmux configuration to include new logging path`
* **クローンの運用**: Git管理にすることで、必要に応じて他のマシンにも同じ環境を作成できます。例えば、`git clone https://github.com/your-repo/configs.git` を他のマシンで実行するだけで、環境の再現が可能です。

### 4. **初期構成スクリプトの実行順**

* **インストール系**: ツールや依存パッケージのインストールを最初に行います。
* **設定ファイルの適用**: 必要な`.zshrc`、`.tmux.conf`、`~/.bashrc` などを適用します。
* **ディレクトリ構成の作成**: 作業に必要なディレクトリやファイルを自動で作成します。
* **tmuxセッションの自動化**: 作業を始める前にtmuxセッションを作成する部分を配置します。

これらの手順をスクリプト化し、Gitで管理することで、再インストールや新しいマシンへの環境移行が非常に楽になります。また、設定ファイルのバックアップも取りやすくなるので、どんな作業中でも安心です。

もし、スクリプト化で進める部分やGit運用に関してさらに詳しくアドバイスが欲しければ、遠慮なく聞いてください！


ターゲット情報やスクリーンショット保存先を自動で表示させるtmuxセッション設定は、作業中の効率化を図るために非常に有用なアイデアです。特にOSCPのように多くのターゲットを扱う際には、どのターゲットに関連する情報かを常に把握しておくことが重要です。

以下は、tmuxセッションを開始する際に、ターゲットのIPやスクリーンショット保存先を表示させる方法の一例です。

### 1. **ターゲット情報をtmuxステータスバーに表示**

tmuxの**ステータスバー**は、セッションごとにカスタマイズ可能で、ターゲット情報やスクリーンショット保存先を表示するのに最適な場所です。`tmux`のステータスバーにターゲット情報を自動で表示させるには、`target`関数で設定した環境変数を利用します。

まずは、以下のように`~/.tmux.conf`にターゲット情報を表示する設定を追加します。

### 2. **ターゲット情報をステータスバーに表示**

tmuxの`status-left`や`status-right`を使って、ターゲットのIPやスクリーンショット保存先を表示できます。

#### tmuxのステータスバーにターゲット情報を追加

```bash
# ~/.tmux.conf

# ターゲットのIPアドレスとスクリーンショット保存先を表示する
set -g status-left "#[fg=green]Target: #(echo $TARGET_IP)"
set -g status-right "#[fg=yellow]Screenshot Dir: #(echo $SCREENSHOT_DIR)"
```

* `status-left` と `status-right` は、tmuxのステータスバーの左側と右側に表示される内容です。ターゲットIPとスクリーンショットのディレクトリを表示しています。
* `#[fg=green]` や `#[fg=yellow]` は、文字の色を設定するためのtmuxコマンドです。

### 3. **tmuxセッション作成スクリプトの変更**

ターゲット情報が設定されている場合に、それを自動的にステータスバーに表示できるように、tmuxセッション作成時にターゲット設定を反映するスクリプトを作成します。例えば、ターゲット情報を引き渡すことで、tmuxセッション開始時にターゲット情報を表示させることができます。

以下は、そのスクリプトの一例です：

```bash
#!/bin/bash
# tmuxセッション作成スクリプト

# ターゲットIPとスクリーンショットディレクトリを環境変数として設定
export TARGET_IP=$1  # 1番目の引数にターゲットIPを指定
export SCREENSHOT_DIR="$HOME/Vault/Target/$TARGET_IP/Screenshot"  # スクリーンショット保存先

# tmuxセッション作成
tmux new-session -d -s "OSCP" -n "Recon"  # Reconウィンドウを作成
tmux new-window -t OSCP:1 -n "Exploit"    # Exploitウィンドウを作成
tmux new-window -t OSCP:2 -n "PrivEsc"    # PrivEscウィンドウを作成

# ターゲット情報とスクリーンショットディレクトリを表示するように設定
tmux set-option -g status-left "#[fg=green]Target: #(echo $TARGET_IP)"
tmux set-option -g status-right "#[fg=yellow]Screenshot Dir: #(echo $SCREENSHOT_DIR)"

# セッションに接続
tmux attach -t OSCP
```

### 4. **実行方法**

上記スクリプトを実行する際に、ターゲットIPを引数として渡すと、指定したターゲットIPがステータスバーに表示されます。以下のように実行します：

```bash
./create_tmux_session.sh 192.168.1.100
```

このコマンドを実行すると、`tmux`セッションが作成され、その中でターゲットIP（`192.168.1.100`）とスクリーンショット保存先（`~/Vault/Target/192.168.1.100/Screenshot`）がステータスバーに表示されます。

### 5. **ターゲット情報の動的更新**

セッションを開始した後でターゲット情報が変更された場合、以下のコマンドを使って`tmux`のステータスバーを動的に更新することもできます：

```bash
tmux set-option -g status-left "#[fg=green]Target: #(echo $NEW_TARGET_IP)"
tmux set-option -g status-right "#[fg=yellow]Screenshot Dir: #(echo $NEW_SCREENSHOT_DIR)"
```

この方法で、ターゲット情報やスクリーンショットの保存先を動的に変更できます。

### 6. **カスタム関数を利用してターゲット切り替え**

ターゲットIPやスクリーンショット保存先を手動で変更したい場合、例えば、`sw`関数を使ってtmuxセッション内でも簡単にターゲットを切り替えることができます。以下のように、`sw`関数をtmux内でも利用可能にします。

```bash
# targetを指定する関数
sw() {
    local target="$1"
    if [ -z "$target" ]; then
        echo "Usage: sw <Target IP>"
        return 1
    fi

    export TARGET_IP="$target"
    export SCREENSHOT_DIR="$HOME/Vault/Target/$TARGET_IP/Screenshot"

    # ステータスバーを更新
    tmux set-option -g status-left "#[fg=green]Target: #(echo $TARGET_IP)"
    tmux set-option -g status-right "#[fg=yellow]Screenshot Dir: #(echo $SCREENSHOT_DIR)"
    echo "ターゲットを変更しました: $TARGET_IP"
}
```

これにより、tmuxセッション内で`sw <Target IP>`を実行すると、ターゲット情報とスクリーンショット保存先が更新されます。

### まとめ

* **tmuxステータスバーにターゲット情報を表示する**ことで、作業中にどのターゲットを扱っているか、またスクリーンショットの保存先が常に視覚的に確認できるようになります。
* **tmuxセッション作成時にターゲット情報を反映させるスクリプト**を作成することで、毎回のセットアップが効率化され、作業がスムーズに進みます。
* **ターゲット切り替えの自動化**により、作業中にターゲットが変わった場合でも簡単に情報を更新でき、常に最新の情報を保持できます。

この方法を取り入れることで、OSCP試験やペネトレーションテストの際に作業の進行状況やターゲット情報を手軽に把握できるようになります。


確かに、`zshrc` にターゲット情報を表示させる設定と、tmuxのステータスバーにターゲット情報を表示させる設定が重複している場合、情報が二重に表示されることになります。これを避けるためには、どちらか一方に情報を表示させるようにするか、表示のタイミングや場所を工夫する必要があります。

以下の方法で、二重表示を回避する方法を提案します。

### 1. **`zshrc`とtmuxのステータスバーの表示を分ける**

`zshrc`にターゲット情報を表示させるのはターミナル起動時の状態としては便利ですが、tmuxのステータスバーに表示させることで作業中にターゲット情報が目立つようにするという意図があるかと思います。これらが二重になるのを避けるためには、次のように表示を分ける方法があります。

#### 方法1: **`zshrc` での表示をオフにする**

もし、ターミナル起動時に表示する必要がない場合は、`zshrc` での表示をオフにするか、コメントアウトすることができます。具体的には、ターゲット情報を表示する部分を一時的に無効化します。

例えば、`zshrc` で次のようにターゲット情報を表示している場合:

```zsh
# .zshrc にターゲット情報を表示
export TARGET_IP="192.168.1.100"
export SCREENSHOT_DIR="$HOME/Vault/Target/$TARGET_IP/Screenshot"

# zshのプロンプトに表示
PROMPT="%F{cyan}[Target: $TARGET_IP]%f %F{green}[Screenshot Dir: $SCREENSHOT_DIR]%f $ "
```

この部分を、tmux用に表示を移行する場合は、`zshrc`から削除するか、コメントアウトして無効化します。

```zsh
# PROMPT="%F{cyan}[Target: $TARGET_IP]%f %F{green}[Screenshot Dir: $SCREENSHOT_DIR]%f $ "
```

これで、`zshrc`のプロンプト部分にはターゲット情報は表示されなくなり、tmuxのステータスバーに表示されるようになります。

#### 方法2: **`zshrc` と `tmux` で異なる情報を表示**

もし、`zshrc` と `tmux` で別々に表示したい場合は、例えば、`zshrc` ではターゲットIPだけ、tmux ではスクリーンショットの保存先など、異なる情報を表示させることで、二重に情報が表示されるのを回避できます。

例えば：

* **`zshrc`** ではターゲットのIPのみ表示：

  ```zsh
  export TARGET_IP="192.168.1.100"
  PROMPT="%F{cyan}[Target: $TARGET_IP]%f $ "
  ```

* **`tmux`** ではスクリーンショット保存先を表示：

  ```bash
  set -g status-right "#[fg=yellow]Screenshot Dir: #(echo $SCREENSHOT_DIR)"
  ```

これにより、`zshrc` ではターゲットIPを、`tmux` ではスクリーンショットディレクトリを表示する形で、情報が重複せずに異なる場所に表示されます。

#### 方法3: **tmuxのステータスバーのカスタマイズ**

`tmux` のステータスバーで表示される内容をさらにカスタマイズし、`zshrc` の情報と同じものを表示しないようにすることができます。例えば、`tmux` で表示する情報をターゲット情報やスクリーンショット保存先のみで統一するなど、表示内容をシンプルに保つ方法です。

### 2. **表示をオン・オフするトグル式にする**

もし`zshrc`とtmux両方で表示したいけれど、トグルで切り替えたい場合には、例えば環境変数を使って表示をオン・オフできるようにする方法もあります。

以下はその一例です：

```zsh
# zshrcにターゲット情報を表示するかどうかの環境変数を設定
export SHOW_TARGET_INFO=true

# ターゲット情報を表示する設定
if [ "$SHOW_TARGET_INFO" = true ]; then
    PROMPT="%F{cyan}[Target: $TARGET_IP]%f $ "
else
    PROMPT="$ "
fi
```

このように、`SHOW_TARGET_INFO` が `true` のときのみターゲット情報を表示することができます。後から `SHOW_TARGET_INFO=false` に設定すれば、表示がオフになります。これにより、`zshrc` と `tmux` の設定を柔軟に変更できるようになります。

### 3. **`tmux` セッションごとの設定**

さらに、`tmux` のセッションに応じてターゲット情報を表示する設定を切り替えることもできます。例えば、セッション名に基づいて表示を変更するなどの方法もあります。

```bash
# セッション名に基づいてターゲット情報を表示
if [ "$(tmux display-message -p '#S')" = "OSCP" ]; then
    tmux set-option -g status-left "#[fg=green]Target: #(echo $TARGET_IP)"
    tmux set-option -g status-right "#[fg=yellow]Screenshot Dir: #(echo $SCREENSHOT_DIR)"
fi
```

これにより、`OSCP` セッションでのみターゲット情報が表示されるようにできます。

### 結論

* **二重表示の回避**は、`zshrc` と `tmux` のどちらかにのみターゲット情報を表示させることで簡単に解決できます。`tmux`に絞ることで作業中の情報が目立ち、不要な表示を避けられます。
* **表示を切り替えたい場合**は、環境変数やセッションごとの設定を使って、柔軟に調整できます。

これで、ターゲット情報が二重に表示される問題を解決し、作業中に必要な情報を視覚的に整理できます。
