# linux-resources

# netcat

## nc -l -k -p 9000 &

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

About the -p flag

I should have given you the more portable form. With the OpenBSD netcat that Ubuntu installs, nc -l -k 9000 is the standard syntax, with the port as a plain argument. Ubuntu’s build also tolerates -l -p 9000, but the OpenBSD manual documents -p as a source-port option, so don’t be surprised if it complains on another system. Either way, ss -tlnp will show whether it’s listening.

What it does

nc opens port 9000 and waits. When a client connects, anything the client sends is printed to the terminal, and whatever you type on the server’s side goes back to the client. It doesn’t speak any protocol, so it’s just a bare TCP listener. That’s why it’s useful in a lab: it’s an “open port” you can create and destroy in a second.
