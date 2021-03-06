Files and documentation to modify retail Sega Saturn XBAND games to be direct dial capable

Background

The XBAND Sega Saturn games, also known as SEGANET games released in Japan used a matching server to connect players over the phone network for head to head game matches. Players connected to the server. The server matched two players requesting to play the same game, assigned one player as master and the other as slave, passed the slave phone number to the master and disconnected the line. The slave player's modem went into listening mode waiting for a call. The master player's modem dialed the phone number of the slave player.
  
Although ultimately the XBAND connection is peer to peer direct dial, the initial server call cannot be bypassed. The North American Netlink games do not use a matching server and the players themselves choose the master/slave role and manually enter the phone number of their opponent. Because of this, retail Netlink games can be played as long as there is a way to connect the two callers together. Currently working methods include POTS landline, VOIP, or a local phone line simulator. The retail Japanese XBAND games cannot currently be played because the matching server(s) no longer exist. 
  
The Japanese XBAND OS and libraries were created before the Netlink OS and libraries. In their final form, the two are incompatible with each other. However, an early version of the Netlink OS and libraries was provided to developers on the November 1996 DTS CD. According to the documentation, these are largely translated versions of the Japanese OS and libraries. Further, because these versions were provided for developers to test game functionality in a local environment, they do not need a matching server and can direct dial. This early Netlink OS and library is compatible with the Japanese games and can be substituted into a re-built version of the games.
  
The necessary files to substitute for each game :
  
Creating a more efficient tunnel for online play
  
The best method currently available for online play uses VOIP and direct IP dialing. However, this solution introduces significant latency and jitter. Modem audio must be encoded, packetized, sent over the internet, buffered, decoded, and passed to the far side modem. This is on top of the latency already introduced by the analog modem modulation/demodulation. A more efficient approach would demodulate the modem data locally, pass the decoded data over the internet, then modulate the data on the far end for playback to the other modem. Additional error correction or timing manipulation could occur with the decoded data. 
    
Enter the Dreampi

A common solution for Dreamcast online play uses a custom dial-in server on a Raspberry pi with the Dreamcast dial-up modem connected to a USB dial-up modem connected to the RPi (commonly referred to as Dreampi). A similar setup could be used for the master side of Netlink/XBAND. The Pi could detect the calling modem, connect and tunnel the data. However, connecting to the slave side is not as easy. The slave side is looking for a phone call. It does this by detecting an AC voltage of a specific frequency; the ring voltage. A modem has no way of generating this, therefore the far side Raspberry Pi cannot call the Saturn modem. Without a ring signal, the saturn modem will not answer the line.
    
The solution: manipulate modem init strings

By changing how the slave side modem initializes, the problem of detecting a ring can be eliminated. The slave side can be made to dial out and establish a connection to the local raspberry pi upon initialization. The game remains unaware of this behavior and interprets any data sent over this connection as coming from the Saturn modem. Normally, upon detecting ring voltage, the modem passes the response RING. The game looks for this string to determine when to answer the call. With the Saturn modem already connected to the Raspberry Pi, the program on the Pi simply has to send the string RING. The game will "answer" the call and wait for a CONNECT response. The Pi can then send that as well. At this point, the Saturn modem will happily send out data intended for the opponent's modem. The tunnel program can facilitate passing data to and from the internet.
