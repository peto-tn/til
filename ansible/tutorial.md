## Ansibleとは
構成管理ツール。ChefやPuppetのようにシステムの構成を自動化できる。
Chef等との最大の違いはsshを使用して対象のシステム上でコマンドを実行するため、構成対象のシステムに何かをインストールしておく必要がない。

## 使い方
### Ansibleのインストール(mac)
```
brew install ansible
```

### inventryファイル
defaultで以下のファイルに書かれたホストに対して実行する。
```
/etc/ansible/hosts
```

書式はini。以下のようにすればグループ化できる
```
[dev]
192.168.0.1

[prod]
192.168.1.1
192.168.1.2
192.168.1.3
```

### PlayBook
Chefでいうレシピ。ansible-playbookコマンドであらかじめ定義しておいた処理が実行できる。ansibleコマンドは単一コマンドのみの実行。  
playbookファイルはymlで記述する
- 例:zshのインストール
```
---
- hosts:dev 
  sudo: true
  user: hoge
  tasks:
    - name: install packages zsh
      apt: name=zsh update_cache=yes
```

### vagrantでの実行
vagrantのprovisionerを利用して、vagrant up時にansibleを実行したりもできる。  
Vagrantfileに以下のように記述する
```
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "playbookのパス"
    ansible.inventory_path = "inventoryファイルのパス"
    ansible.limit = 'all'
  end
```

上記設定しおけば、vagrant provisionコマンドでもansibleの実行が可能
