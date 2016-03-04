## 変数について
変数はいくつかの方法があり、terraform公式ではterraform.tfvars に変数を記述することが推奨されている。
しかし、この方法ではtfファイルで改めて変数の宣言が必要であり、冗長になってしまう。
同ディレクトリにvariable.tf 等でtfファイルとして別で作成し、そこに変数の設定を集約させたほうが使い勝手がよい。またdefaultに複数の値を記載できるためIPのまとまったリストなども作成しやすい。

### Ex.
``` ファイル構成
terraform.tf
variable.tf
```

``` variable.tf
variable "region" {
    default = "ap-northeast-1a"
}
variable "host_ip" {
    default = {
        app = "172.16.1.4"
        db  = "172.16.1.5"
    }
}
```

``` terraform.tf
provider "aws" {
    region = "${var.region}"
}

resource "aws_instance" "web" {
    ami = "ami-408c7f28"
    instance_type = "t1.micro"
    private_ip = "${var.host_ip.app}"
    tags {
        Name = "HelloWorld"
    }
}
```
