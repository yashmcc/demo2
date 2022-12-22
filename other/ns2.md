ALGORITHM:
1.	START
2.	Create a simulator object
3.	Create and open a trace file to execute code
4.	Create required number of nodes
5.	Set a .nam file for animation
6.	Set orientation for all nodes
7.	Create UDP source and destination
8.	Create TCP source and destination
9.	Create necessary traffic and specify start and stop time
10.	Run the program
11.	Display output
12.	STOP

CODE:

Creating nodes and links:
set ns [new Simulator]
set tr [open out.tr w]
$ns trace-all $tr
set namtr [open out.nam w]
$ns nametrace-all $namtr
set n0 [$ns node]
set n1 [$ns node]
set n2 [$ns node]
set n3 [$ns node]
$ns duplex-link $n0 $n1 10Mb 5ms Droptail
$ns duplex-link $n2 $n0 10Mb 5ms Droptail
$ns duplex-link $n3 $n0 10Mb 5ms Droptail
$ns duplex-link-op $n0 $n1 orient right
$ns duplex-link-op $n0 $n2 orient left-up
$ns duplex-link-op $n0 $n3 orient left-down
$ns at 10.0 “$ns halt”
$ns run

TCP Data Traffic:
 set ns [new Simulator]
set nf [open out.nam w]
$ns namtrace-all $nf
set n0 [$ns node]
set n1 [$ns node]
set n2 [$ns node]
set n3 [$ns node]
set n4 [$ns node]
$ns duplex-link $n0 $n1 2Mb 10ms DropTail
$ns duplex-link $n4 $n1 1.2Mb 10ms DropTail
$ns duplex-link $n1 $n2 2Mb 10ms DropTail
$ns duplex-link $n1 $n3 1.1Mb 10ms DropTail
#$ns duplex-link $n1 $n3 2Mb 10ms DropTail
#$ns duplex-link $n4 $n1 2Mb 10ms DropTail
$ns duplex-link-op $n0 $n1 orient right-down
$ns duplex-link-op $n1 $n2 orient right-up
$ns duplex-link-op $n4 $n1 orient right-up
$ns duplex-link-op $n1 $n3 orient right-down
$ns duplex-link-op $n1 $n3 queuePos 2.5
#wired tcp
set tcp [new Agent/TCP]
$tcp set class_ 2
$ns attach-agent $n0 $tcp
set sink [new Agent/TCPSink]
$ns attach-agent $n3 $sink
$ns connect $tcp $sink
$tcp set fid_ 1
#Setup a FTP over TCP connection
set ftp [new Application/FTP]
$ftp attach-agent $tcp
$ftp set type_ FTP
#Setup a UDP connection
set udp [new Agent/UDP]
$ns attach-agent $n2 $udp
set null [new Agent/Null]
$ns attach-agent $n4 $null
$ns connect $udp $null
$udp set fid_ 2
#Setup a CBR over UDP connection
set cbr [new Application/Traffic/CBR]
$cbr attach-agent $udp
$cbr set type_ CBR
$cbr set packet_size_ 1000
$cbr set rate_ 1mb
$cbr set random_ false
$ns at 0.1 "$cbr start"
$ns at 1.0 "$ftp start"
$ns at 4.0 "$ftp stop"
$ns at 4.5 "$cbr stop"
$ns at 5.0 "finish"
$ns run
