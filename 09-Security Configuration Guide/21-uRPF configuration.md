
# Configuring uRPF

## About uRPF

Unicast Reverse Path Forwarding (uRPF) protects
a network against source address spoofing attacks, such as DoS and DDoS attacks.

### uRPF application scenario

Attackers send packets with a forged source
address to access a system that uses IPv4-based authentication, in the name of authorized
users or even the administrator. Even if the attackers or other hosts cannot
receive any response packets, the attacks are still disruptive to the attacked
target.

Figure 1 Source address spoofing attack

![](https://resource.h3c.com/en/202407/12/20240712_11705487_x_Img_x_png_0_2216082_294551_0.png)

‌

As shown in [Figure 1](#_Ref148066155), an
attacker on Router A sends the server (Router B) requests with a forged source
IP address 2.2.2.1 at a high rate. Router B sends response packets to IP
address 2.2.2.1 (Router C). Consequently, both Router B and Router C are
attacked. If the administrator disconnects Router C by mistake, the network
service is interrupted. 

Attackers can also send packets with
different forged source addresses or attack multiple servers simultaneously to
block connections or even break down the network.

uRPF can prevent these source address
spoofing attacks. It checks whether an interface that receives a packet is the
output interface of the FIB entry that matches the source address of the packet.
If not, uRPF considers it a spoofing attack and discards the packet. 

### uRPF check modes

uRPF supports strict and loose modes.

#### Strict uRPF check

To pass strict uRPF check, the source
address of a packet and the receiving interface must match the destination
address and output interface of a FIB entry. In some scenarios (for example, asymmetrical
routing), strict uRPF might discard valid packets.

Strict uRPF is often deployed between a PE
and a CE.

#### Loose uRPF check

To pass loose uRPF check, the source
address of a packet must match the destination address of a FIB entry. Loose uRPF
can avoid discarding valid packets, but might let go attack packets. 

Loose uRPF is often deployed between ISPs,
especially in asymmetrical routing.

### Network application

As shown in[Figure 2](#_Ref456705042), strict
uRPF check is configured between an ISP network and a customer network. Loose
uRPF check is configured between ISPs.

Figure 2 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705488_x_Img_x_png_1_2216082_294551_0.png)

‌

## Enabling uRPF globally

#### Restrictions and guidelines

Global uRPF takes effect on all interfaces
of the device.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable uRPF globally.

ip urpf { loose \| strict }

By default, uRPF is disabled.

## Display and maintenance commands for uRPF

Execute display
commands in any view.

 

| Task | Command |
| --- | --- |
| Display uRPF configuration. | display ip urpf \[ slot slot-number ] |

‌

 

