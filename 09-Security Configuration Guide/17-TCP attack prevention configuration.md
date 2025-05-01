
# Configuring TCP attack prevention

## About TCP attack prevention

TCP attack prevention can detect and
prevent attacks that exploit the TCP connection establishment process.

## Configuring Naptha attack prevention

#### About this task

Naptha is a DDoS attack that targets
operating systems. It exploits the resources consuming vulnerability in TCP/IP
stack and network application process. The attacker establishes a large number
of TCP connections in a short period of time and leaves them in certain states
without requesting any data. These TCP connections starve the victim of system
resources, resulting in a system breakdown.

After you enable Naptha attack prevention,
the device periodically checks the number of TCP connections in each state (CLOSING,
ESTABLISHED, FIN\_WAIT\_1, FIN\_WAIT\_2, and LAST\_ACK). If the number of TCP
connections in a state exceeds the limit, the device will accelerate the aging
of the TCP connections in that state to mitigate the Naptha attack.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable Naptha attack prevention.

tcp anti-naptha enable

By default, Naptha attack prevention is
disabled.

**3\.**(Optional.) Set the maximum number of TCP connections
in a state.

tcp state { closing \| established \| fin-wait-1 \| fin-wait-2 \| last-ack }
connection-limit number

By default, the maximum number of TCP
connections in each state (CLOSING, ESTABLISHED, FIN\_WAIT\_1, FIN\_WAIT\_2, and LAST\_ACK)
is 50\.

To disable the device from accelerating
the aging of the TCP connections in a state, set the value to 0\.

**4\.**(Optional.) Set the interval for checking
the number of TCP connections in each state.

tcp check-state interval interval

By default, the interval for checking the
number of TCP connections in each state is 30 seconds.

 

