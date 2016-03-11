## 変数について
変数はいくつかの方法があり、terraform公式ではterraform.tfvars に変数を記述することが推奨されている。  
しかし、この方法ではtfファイルで改めて変数の宣言が必要であり、冗長になってしまう。  
同ディレクトリにvariable.tf 等でtfファイルとして別で作成し、そこに変数の設定を集約させたほうが使い勝手がよい。  
またdefaultに複数の値を記載できるためIPのまとまったリストなども作成しやすい。

### Ex.
- ファイル構成
```
terraform.tf
variable.tf
```

- variable.tf
```
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

- terraform.tf
```
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

## 配列について
variableではstring又はmapのみ対応しておりarrayは作成できない。  
mapだとキーを指定しないと使用できないため、設定数が複数あって数が変動する場合、
使用箇所も修正する必要がでてきてしまう。  
やや力技で、「,」区切りの文字列として定義し、使用側でsplitする方法がある。

```
# foo.tf

resource "aws_instance" "server" {
  ...
  security_groups = "${split(",", var.sec_groups)}"
}


# variables.tf

variable "sec_groups" {
   default = "sg-xx,sg-x,sg-xxx"
   description = "The security groups to instantiate the EC2 instance under."
}

```
