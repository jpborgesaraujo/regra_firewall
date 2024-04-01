# regra_firewall

1. Libere qualquer tráfego para interface de loopback no firewall
 
iptable -t filter -A INPUT -i LOOPBACK -j ACCEPT
 
iptable -t filter -A OUTPUT -o LOOPBACK -j ACCEPT
 
 
2. Estabeleça a política DROP (restritiva) para as chains INPUT e FORWARD da tabela filter
 
iptable -t filter -A INPUT -P DROP
 
iptable -t filter -A FORWARD -P DROP
 
3. Possibilite que usuários da rede interna possam acessar o serviço WWW, tanto na porta (TCP) 80 como na 443. Não esqueça de realizar NAT já que os usuários internos não possuem um endereço IP válido
 
iptable -t filter -A INPUT  -j ACCEPT
 
 
5. Bloqueie acesso para qualquer usuário ao site www.jogosonline.com.br, exceto para seu chefe, que possui o endereço IP 10.1.1.100.
 
iptables -A FORWARD -d www.jogosonline.com.br -s ! 10.1.1.100 -j DROP

6. Permita que o firewall receba pacotes do tipo ICMP echo-request (ping), porém, limite a 5 pacotes por segundo.

iptables -A INPUT -p icmp --icmp-type echo-request -m limit --limit 5/second -j ACCEPT


7.Permita que tanto a rede interna como a DMZ possam realizar consultas ao DNS externo, bem como, receber os resultados das
mesmas.

Permitir consultas DNS externas da DMZ
iptables -A FORWARD -s <dmz> -p udp --dport 53 -j ACCEPT
iptables -A FORWARD -s <dmz> -p tcp --dport 53 -j ACCEPT

Permitir respostas DNS de saída para a rede interna
iptables -A FORWARD -d <rede_interna> -p udp --sport 53 -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A FORWARD -d <rede_interna> -p tcp --sport 53 -m state --state ESTABLISHED,RELATED -j ACCEPT

Permitir respostas DNS de saída para a DMZ
iptables -A FORWARD -d <dmz> -p udp --sport 53 -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A FORWARD -d <dmz> -p tcp --sport 53 -m state --state ESTABLISHED,RELATED -j ACCEPT

8.Permita o tráfego TCP destinado à máquina 192.168.1.100 (DMZ) na porta 80, vindo de qualquer rede (Interna ou Externa).

# Permitir tráfego TCP na porta 80 para a máquina na DMZ (192.168.1.100)
iptables -A FORWARD -d 192.168.1.100 -p tcp --dport 80 -j ACCEPT

9. Redirecione pacotes TCP destinados ao IP 200.20.5.1 porta 80, para a máquina 192.168.1.100 que está localizado na DMZ.

# Redirecionar tráfego TCP na porta 80 para a máquina na DMZ (192.168.1.100)
iptables -t nat -A PREROUTING -p tcp -d 200.20.5.1 --dport 80 -j DNAT --to-destination 192.168.1.100

10. Faça com que a máquina 192.168.1.100 consiga responder os pacotes TCP recebidos na porta 80 corretamente.

iptables -A INPUT -p tcp --dport 80 -m state --state NEW,ESTABLISHED -j ACCEPT


