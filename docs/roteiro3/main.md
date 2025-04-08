Objetivo: Criar uma Private Cloud com OpenStack

Para iniciar o roteiro 3, precisamos criar novamente as bridges, dentro do maas, para que as máquinas virtuais possam receber IPs.

Como no roteiro passado, vamos criar um controller na máquina 1, para isso criamos uma tag `juju` que referencia a máquina 1.

``` bash
juju bootstrap --bootstrap-series=jammy --constraints tags=juju maas-one maas-controller
```

Definimos o modelo openstack que vai ser utilizado para fazer o deploy.
``` bash
juju add-model --config default-series=jammy openstack
```
``` bash
juju switch maas-controller:openstack
```

Criamos o dashboard do juju dentro de uma máquina virtual em cima da máquina 0, para isso utilizamos o controller criado anteriormente. Foram também integrados o juju-dashboard com o controller e exposto para a rede.

``` bash
juju deploy juju-dashboard --to lxd:0
```

``` bash
juju integrate juju-dashboard controller
```

``` bash
juju expose juju-dashboard
```

Instalação do Ceph OSD que funciona como um software que unificará todas os HDs das máquinas para criar um storage distribuído. Esse software é justamente o que vai permitir que os usuários consigam ver apenas uma unidade de armazenamento, mesmo que na verdade sejam várias máquinas com vários HDs. Para isso, utilizamos o charm do ceph-osd.

Para isso criamos tags específicas para três máquinas que vão ser utilizadas para o ceph, no caso as máquinas 3, 4 e 5. Essas tags são compute.

``` bash
juju deploy -n 3 --channel quincy/stable --config ceph-osd.yaml --constraints tags=compute ceph-osd
```

