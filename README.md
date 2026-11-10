# linux-resources

# netcat (nc)

 >nc -l -k -p 9000 &

This command starts a tiny TCP service on port 9000, so you have something extra to find with nmap and to block with firewall rules.

### Breaking it down

nc	
- netcat, a tool for reading and writing data over network connections. It’s often called the “Swiss Army knife” of networking

\-l
- Listen mode: wait for incoming connections instead of making one

\-k
- Keep listening after a client disconnects. Without it, nc accepts one connection and then exits

\-p 9000
- The port to listen on

\&
- Run the command in the background, so you get your prompt back

### About the -p flag

I should have given you the more portable form. With the OpenBSD netcat that Ubuntu installs, nc -l -k 9000 is the standard syntax, with the port as a plain argument. Ubuntu’s build also tolerates -l -p 9000, but the OpenBSD manual documents -p as a source-port option, so don’t be surprised if it complains on another system. Either way, ss -tlnp will show whether it’s listening.

### What it does

nc opens port 9000 and waits. When a client connects, anything the client sends is printed to the terminal, and whatever you type on the server’s side goes back to the client. It doesn’t speak any protocol, so it’s just a bare TCP listener. That’s why it’s useful in a lab: it’s an “open port” you can create and destroy in a second.

# socket stats (ss)

> ss -tlnp

### Breaking it down

ss 
- (“socket statistics”) lists network sockets on the machine. It’s the modern replacement for netstat. The -tlnp part is five single-letter flags combined:

\-t	
- Show TCP sockets only (use -u for UDP)

\-l	
- Show only listening sockets, meaning services waiting for incoming connections
  
\-n	
- Numeric output: show port numbers (22) and IP addresses instead of looking up names (ssh). It’s faster and unambiguous
  
\-p	
- Show the process that owns each socket
- sudo is needed for -p. Without it, you’ll see the listening ports but not the process names for sockets owned by other users, such as nginx running as root.

### Output

How to read it

- Local Address:Port is where the service is listening.

  - 0.0.0.0:80 means listening on port 80 on all IPv4 interfaces, so other machines can reach it.
  
  -  127.0.0.1:80 would mean localhost only. Other machines couldn’t connect even with no firewall at all.
   
  - [::]:22 or *:22 is the IPv6 equivalent of “all interfaces.”

- Peer Address:Port is * for listeners because there’s no remote side yet.

- Process shows the program name and process ID.

- Recv-Q / Send-Q are queue sizes. For listening sockets, Send-Q is the maximum backlog of pending connections. You can ignore them for now.

### Why it matters for firewalls

ss -tlnp tells you what’s listening, which is what a firewall can protect or expose. When you scan the server with nmap from the client, the open ports should match this list. If a port shows up in ss but nmap reports it as filtered, a firewall is in the way. If it’s missing from ss, no service is listening, so nmap will report it closed.

You can also try sudo ss -tunap (adds UDP and shows all sockets, including active connections) to see the difference.
