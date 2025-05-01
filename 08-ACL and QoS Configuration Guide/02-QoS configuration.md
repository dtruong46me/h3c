
# QoS overview

In data
communications, Quality of Service (QoS) provides differentiated service
guarantees for diversified traffic in terms of bandwidth, delay, jitter, and
drop rate, all of which can affect QoS.

QoS manages network resources and
prioritizes traffic to balance system resources.

The following section describes typical QoS
service models and widely used QoS techniques.

## QoS service models

This section describes several typical QoS
service models. 

### Best-effort service model

The best-effort model is a single-service
model. The best-effort model is not as reliable as other models and does not
guarantee delay-free delivery.

The best-effort service model is the
default model for the Internet and applies to most network applications. It uses
the First In First Out (FIFO) queuing mechanism.

### IntServ model

The integrated service (IntServ) model is a
multiple-service model that can accommodate diverse QoS requirements. This
service model provides the most granularly differentiated QoS by identifying
and guaranteeing definite QoS for each data flow.

In the IntServ model, an application must
request service from the network before it sends data. IntServ signals the
service request with the RSVP. All nodes receiving the request reserve
resources as requested and maintain state information for the application flow.


The IntServ model demands high storage and
processing capabilities because it requires all nodes along the transmission
path to maintain resource state information for each flow. This model is
suitable for small-sized or edge networks. However, it is not suitable for
large-sized networks, for example, the core layer of the Internet, where
billions of flows are present.

### DiffServ model

The differentiated service (DiffServ) model
is a multiple-service model that can meet
diverse QoS requirements. It is easy to implement and extend. DiffServ does not
signal the network to reserve resources before sending data, as IntServ does.

## QoS techniquesin a network

The QoS techniques include the following features:

·     Traffic classification.

·     Traffic policing.

·     Traffic shaping.

·     Rate limit.

·     Congestion management.

·     Congestion avoidance. 

The following section briefly introduces these
QoS techniques.

All QoS techniques in this document are
based on the DiffServ model.

Figure 1 Position of the QoS techniques in a
network

![](https://resource.h3c.com/en/202407/12/20240712_11705196_x_Img_x_png_0_2216057_294551_0.png)

 

As shown in Figure 1, traffic classification, traffic shaping, traffic policing,
congestion management, and congestion avoidance mainly implement the following
functions:

·     Traffic classification—Uses match criteria to assign packets with the same characteristics
to a traffic class. Based on traffic classes, you can provide differentiated
services.

·     Traffic policing—Polices flows and imposes penalties to prevent aggressive use of
network resources. You can apply traffic policing to both incoming and outgoing
traffic of a port.

·     Traffic shaping—Adapts the output rate of traffic to the network resources
available on the downstream device to eliminate packet drops. Traffic shaping
usually applies to the outgoing traffic of a port.

·     Congestion management—Provides a resource scheduling policy to determine the packet
forwarding sequence when congestion occurs. Congestion management usually applies
to the outgoing traffic of a port.

·     Congestion avoidance—Monitors the network resource usage. It is usually applied to the
outgoing traffic of a port. When congestion worsens, congestion avoidance reduces
the queue length by dropping packets.

## QoS processing flow in a device

Figure 2 briefly describes how the QoS module processes traffic.

**1\.**Traffic classifier identifies and classifies
traffic for subsequent QoS actions.

**2\.**The QoS module takes various QoS actions on
classified traffic as configured, depending on the traffic processing phase and
network status. For example, you can configure the QoS module to perform the
following operations:

¡     Traffic
policing for incoming traffic.

¡     Traffic
shaping for outgoing traffic.

¡     Congestion
avoidance before congestion occurs.

¡     Congestion
management when congestion occurs.

Figure 2 QoS processingflow

![](https://resource.h3c.com/en/202407/12/20240712_11705197_x_Img_x_png_1_2216057_294551_0.png)

 

## QoS configuration approaches

You can configure QoS by using the MQC
approach or non-MQC approach. 

In the modular QoS configuration (MQC)
approach, you configure QoS service parameters by using QoS policies. A QoS
policy defines QoS actions to take on different classes of traffic and can be
applied to an object (such as an interface) to control traffic.

In the non-MQC approach, you configure QoS
service parameters without using a QoS policy. For example, you can use the
rate limit feature to set a rate limit on an interface without using a QoS
policy.

 

