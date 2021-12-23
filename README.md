# Домашнее задание к занятию "15.1. Организация сети"

Домашнее задание будет состоять из обязательной части, которую необходимо выполнить на провайдере Яндекс.Облако и дополнительной части в AWS по желанию. Все домашние задания в 15 блоке связаны друг с другом и в конце представляют пример законченной инфраструктуры.  
Все задания требуется выполнить с помощью Terraform, результатом выполненного домашнего задания будет код в репозитории. 

Перед началом работ следует настроить доступ до облачных ресурсов из Terraform используя материалы прошлых лекций и [ДЗ](https://github.com/netology-code/virt-homeworks/tree/master/07-terraform-02-syntax ). А также заранее выбрать регион (в случае AWS) и зону
---
## Вариант с Яндекс.Облако. (Обязательная часть)

1. Создать VPC.
- Создать пустую VPC. Выбрать зону.
2. Публичная подсеть.
- Создать в vpc subnet с названием public, сетью 192.168.10.0/24.
- Создать в этой подсети NAT-инстанс, присвоив ему адрес 192.168.10.254. В качестве image_id использовать fd80mrhj8fl2oe87o4e1
- Создать в этой публичной подсети виртуалку с публичным IP и подключиться к ней, убедиться что есть доступ к интернету.
3. Приватная подсеть.
- Создать в vpc subnet с названием private, сетью 192.168.20.0/24.
- Создать route table. Добавить статический маршрут, направляющий весь исходящий трафик private сети в NAT-инстанс
- Создать в этой приватной подсети виртуалку с внутренним IP, подключиться к ней через виртуалку, созданную ранее и убедиться что есть доступ к интернету

Resource terraform для ЯО
- [VPC subnet](https://registry.terraform.io/providers/yandex-cloud/yandex/latest/docs/resources/vpc_subnet)
- [Route table](https://registry.terraform.io/providers/yandex-cloud/yandex/latest/docs/resources/vpc_route_table)
- [Compute Instance](https://registry.terraform.io/providers/yandex-cloud/yandex/latest/docs/resources/compute_instance)

Манифесты terraform: [yandex_cloud](https://github.com/murzinvit/15.1_cloud_vpc/tree/main/yandex) </br>
Рультат выполнения манифеста: </br>
![](https://github.com/murzinvit/screen_1/blob/d182afba22863c57b642f7e1aca02900352592e1/YC_terraform_init_ok.jpg) </br>

Подключение из внешнего мира к хосту в сети public: </br>
![](https://github.com/murzinvit/screen_1/blob/d182afba22863c57b642f7e1aca02900352592e1/YC_login_in_vm2_ok.jpg) </br>

Подключение с хоста из сети public, к хосту в сети private(через scp скопировал туда id_rsa.pub и id_rsa в .ssh): </br>
![](https://github.com/murzinvit/screen_1/blob/17e43f2b190007a6a3fd7a4d65d089ec75d6afd1/YC_login_in_vm3_ok.jpg) </br>

Создал маршрут и закрепил для сети private: </br>
Манифест сети: [networks.tf](https://github.com/murzinvit/15.1_cloud_vpc/blob/main/yandex/networks.tf) </br>
![](https://github.com/murzinvit/screen_1/blob/e66fe5197cf9fcd9ec28cc08a56e1ab85fdded5c/YC_static_route_private.jpg) </br>
![](https://github.com/murzinvit/screen_1/blob/f2dd4ad07716b50b75c5c7c8709d1f61133aef84/YC_route_in_net_private.jpg) </br>

Traceroute mail.ru, с хоста в сети private: </br>
![](https://github.com/murzinvit/screen_1/blob/b818440611936d4f34ceab0d6de61f86b6576b66/YC_traceroute_in_vm3.jpg) </br>

После открепления статического маршрута от сети private, пинга mail.ru блокирван: </br>
![](https://github.com/murzinvit/screen_1/blob/d23796a39e446b4696a962d24521cf760bf4fc9d/YC_after_delete_static_route.jpg) </br>


---
## Вариант с  AWS. (Дополнительная часть)

1. Создать VPC.
- Cоздать пустую VPC с подсетью 10.10.0.0/16.
2. Публичная подсеть.
- Создать в vpc subnet с названием public, сетью 10.10.1.0/24
- Разрешить в данной subnet присвоение public IP по-умолчанию. 
- Создать Internet gateway 
- Добавить в таблицу маршрутизации маршрут, направляющий весь исходящий трафик в Internet gateway.
- Создать security group с разрешающими правилами на SSH и ICMP. Привязать данную security-group на все создаваемые в данном ДЗ виртуалки
- Создать в этой подсети виртуалку и убедиться, что инстанс имеет публичный IP. Подключиться к ней, убедиться что есть доступ к интернету.
- Добавить NAT gateway в public subnet.
3. Приватная подсеть.
- Создать в vpc subnet с названием private, сетью 10.10.2.0/24
- Создать отдельную таблицу маршрутизации и привязать ее к private-подсети
- Добавить Route, направляющий весь исходящий трафик private сети в NAT.
- Создать виртуалку в приватной сети.
- Подключиться к ней по SSH по приватному IP через виртуалку, созданную ранее в публичной подсети и убедиться, что с виртуалки есть выход в интернет.

Resource terraform
- [VPC](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/vpc)
- [Subnet](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/subnet)
- [Internet Gateway](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/internet_gateway)

---

### work notes:
Start work yc: https://cloud.yandex.ru/docs/solutions/infrastructure-management/terraform-quickstart </br>
Install terraform: https://learn.hashicorp.com/tutorials/terraform/install-cli </br>
yc install: https://cloud.yandex.ru/docs/cli/operations/install-cli </br>
yc resources: https://registry.terraform.io/providers/yandex-cloud/yandex/latest/docs </br>
Resourses FAQ: https://cloud.yandex.ru/docs/cli/cli-ref/managed-services/compute/instance/ </br>
Static routes: https://cloud.yandex.ru/docs/vpc/concepts/static-routes#internet-routes </br>
SSH:  https://cloud.yandex.ru/docs/managed-kubernetes/operations/node-connect-ssh#key-format </br>
