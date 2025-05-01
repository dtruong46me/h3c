
# Configuring attack detection and prevention

## Overview

Attack detection and prevention enables a
device to detect attacks by inspecting arriving packets, and to take prevention
actions (such as packet dropping) to protect a private network.

## Attacks that the device can prevent

This section describes the attacks that the device can detect and
prevent.

### TCP fragment attack

An attacker launches TCP fragment attacks by
sending attack TCP fragments defined in RFC 1858:

·     First fragments in which the TCP header is
smaller than 20 bytes.

·     Non-first fragments with a fragment offset of 8
bytes (FO\=1).

Typically, packet filter detects the source
and destination IP addresses, source and destination ports, and transport layer
protocol of the first fragment of a TCP packet. If the first fragment passes
the detection, all subsequent fragments of the TCP packet are allowed to pass through.

Because the first fragment of attack TCP
packets does not hit any match in the packet filter, the subsequent fragments
can all pass through. After the receiving host reassembles the fragments, a TCP
fragment attack occurs.

To prevent TCP fragment attacks, enable TCP
fragment attack prevention to drop attack TCP fragments.

### Login dictionary attack

The login dictionary attack is an automated
process to attempt to log in by trying all possible passwords from a
pre-arranged list of values (the dictionary). Multiple login attempts can occur
in a short period of time.

You can configure the login delay feature to
slow down the login dictionary attacks. This feature enables the device to delay
accepting another login request after detecting a failed login attempt for a
user.

## Configuring TCP fragment attack prevention

The TCP fragment attack prevention feature detects
the length and fragment offset of received TCP fragments and drops attack TCP
fragments. The device supports verifying only TCP fragments forwarded through
the CPU.

To configure TCP fragment attack
prevention:

**1\.**Enter system view.

system-view

**2\.**Enable TCP fragment attack prevention.

attack-defense tcp fragment enable

By default, TCP fragment attack
prevention is enabled.

## Enabling the login delay

The login delay feature delays the device from
accepting a login request from a user after the user fails a login attempt. This
feature can slow down login dictionary attacks.

To enable the login delay:

**1\.**Enter system view.

system-view

**2\.**Enable the login delay feature.

attack-defense login reauthentication-delay seconds

By default, the login delay feature is
disabled. The device does not delay accepting a login request from a user who
has failed a login attempt.

 

