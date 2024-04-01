# regra_firewall

1. Libere qualquer tráfego para interface de loopback no firewall
 
iptable -t filter -A INPUT -i LOOPBACK -j ACCEPT
 
iptable -t filter -A OUTPUT -o LOOPBACK -j ACCEPT
 
 
2. Estabeleça a política DROP (restritiva) para as chains INPUT e FORWARD da tabela filter
 
iptable -t filter -A INPUT -P DROP
 
iptable -t filter -A FORWARD -P DROP
 
3. Possibilite que usuários da rede interna possam acessar o serviço WWW, tanto na porta (TCP) 80 como na 443. Não esqueça de realizar NAT já que os usuários internos não possuem um endereço IP válido
 
iptable -t filter -A INPUT  -j ACCEPT
 
???
 
5. Bloqueie acesso para qualquer usuário ao site www.jogosonline.com.br, exceto para seu chefe, que possui o endereço IP 10.1.1.100.
 
iptables -A FORWARD -d www.jogosonline.com.br -s ! 10.1.1.100 -j DROP

6. Permita que o firewall receba pacotes do tipo ICMP echo-request (ping), porém, limite a 5 pacotes por segundo.

iptables -A INPUT -p icmp --icmp-type echo-request -m limit --limit 5/second -j ACCEPT
