
# Configuring time ranges

## About time ranges

You can implement a service based on the
time of the day by applying a time range to it. A time-based service takes
effect only in time periods specified by the time range. For example, you can
implement time-based ACL rules by applying a time range to them. 

The following basic types of time ranges
are available:

·     Periodic time range—Recurs periodically on a day or days of the week.

·     Absolute time range—Represents only a period of time and does not recur.

The active period of a time range is
calculated as follows:

**1\.**Combining all periodic statements.

**2\.**Combining all absolute statements.

**3\.**Taking the intersection of the two statement
sets as the active period of the time range.

## Restrictions and guidelines: Time range configuration

When you configure the ACL hardware mode,
follow these restrictions and guidelines:

·     If a time range does not exist, the service
based on the time range does not take effect.

·     You can create a maximum of 1024 time ranges,
each with a maximum of 32 periodic statements and 12 absolute statements.

## Procedure

**1\.**Enter system view.

system-view

**2\.**Create or edit a time range.

time-range time-range-name { start-time to end-time days \[ from time1 date1 ] \[ to time2 date2 ] \| from time1 date1 \[ to time2 date2 ] \| to time2 date2 }

If an existing time range name is
provided, this command adds a statement to the time range.

## Display and maintenance commands for time ranges

Execute the display command in any view.

 

| Task | Command |
| --- | --- |
| Display time range configuration and status. | display time-range { time-range-name \| all } |

 

## Time range configuration examples

### Example: Configuring a time range

#### Network configuration

As shown in[Figure 1](#_Ref303698632),
configure an ACL on the device to allow Host A to access the server only during
8:00 and 18:00 on working days from June 2015 to the end of the year.

Figure 1 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705237_x_Img_x_png_0_2216059_294551_0.png)

‌

#### Procedure

\# Create a periodic time range during 8:00 and
18:00 on working days from June 2015 to the end of the year. 

\<Device\> system-view

\[Device] time-range work 8:0 to 18:0
working-day from 0:0 6/1/2015 to 24:00 12/31/2015

\# Create an IPv4 basic ACL numbered 2001,
and configure a rule in the ACL to permit packets only from 192.168.1.2/32
during the time range work.

\[Device] acl basic 2001

\[Device-acl-ipv4-basic-2001] rule
permit source 192.168.1.2 0 time-range work

\[Device-acl-ipv4-basic-2001] rule
deny source any time-range work

\[Device-acl-ipv4-basic-2001] quit

\# Apply IPv4 basic ACL 2001 to filter
outgoing packets on Ten-GigabitEthernet 1/0/2.

\[Device] interface ten-gigabitethernet 1/0/2

\[Device-Ten-GigabitEthernet1/0/2] packet-filter 2001
outbound

\[Device-Ten-GigabitEthernet1/0/2] quit

#### Verifying the configuration

\# Verify that the time range work is active on the device.

\[Device] display time-range all

Current time is 13:58:35 6/19/2015 Friday

 

Time-range : work (Active)

 08:00 to 18:00 working-day

 from 00:00 6/1/2015 to 00:00 1/1/2016

