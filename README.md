# SSH-Tunneling

SSH tunneling also called as SSH port forwarding is a method of creating encrypted SSH connection between a client and a server machine. It creates a tunnel between two hosts so that data from some other protocol is encapsulated in the SSH header and transferred as SSH data between those two hosts.

There are three types of SSH tunneling.

1. Local Port Forwarding (SSH tunneling)
2. Remote Port Forwarding (Reverse SSH tunneling)
3. Dynamic Port Forwarding (Dynamic SSH tunneling/Socks Proxy)


## Local Port Forwarding

This is one of the simplest form of SSH tunneling. Local port forwarding forwards any traffic from the port on the client to the port of the server machine.

Consider a scenario where there is a website `internal.com`. This website is only available within a private network and you cannot access it because you are working from home and only port `22` of the server is opened for you. Since port `22` is opened for you, with the help of SSH tunneling it is possible to access `internal.com` from your local machine itself. Let’s see how!

Suppose you have SSH access to the server where `internal.com` is hosted. Let’s assume webserver is using port `80` on the remote server. Now let’s create a SSH tunnel using the below command.

`ssh -L 8080:localhost:80 user@internal.com`

The above command will forward any traffic on client machine port `8080` to the port `80` of the server internal.com . Now to access `internal.com`, you just have to open your browser and open http://localhost:8080 .


![image](https://user-images.githubusercontent.com/37524392/173363815-3546f2db-c605-47ba-8d2f-5f314cf7e24d.png)


From security perspective, it is highly recommend that direct SSH access to the web server should not be allowed. So there will be a common SSH server which all the clients will have SSH access and SSH tunneling to this server will help us to access internal.com . Let’s see how this is implemented.

![image](https://user-images.githubusercontent.com/37524392/173363910-5b8c65d3-239d-41b2-aabc-713c64fd0586.png)

Here, we have a common SSH server. All the clients are given SSH access to this server. This server sits in the same network of `internal.com` server and will be able to access port `80` of the internal.com server.

`ssh -L 8080:internal.com:80 user@sshserver`

Above command creates a SSH tunnel to the common SSH server. Since SSH server is able to access port `80` of `internal.com` server, http://locahost:8080 in browser of the client laptop will open `internal.com`.

## Remote Port Forwarding

Remote Port forwarding is just opposite of Local Port forwarding. In local port forwarding, we forward the local machine port to the port of a remote machine, however in remote port forwarding the port of the remote machine is forwarded to the port of the local machine.

Let me explain in detail. Suppose you are developing an application locally and it is running in your laptop at a port `4000`. Now you want this application to be accessed by your co-worker to test the functionality. Since the application is in your local laptop, how would you make it available to others? Remote Port forwarding is the answer.

![image](https://user-images.githubusercontent.com/37524392/173364219-cc5442b4-0110-42e5-a7fd-e831f9f63bd5.png)

So, the requirement here is there should be a SSH server that you and your co-worker should have access to. You should have access to port `22` of the ssh server and your co-worker should be able to access the port on SSH server which will be forwarded.

`ssh -R 5000:localhost:4000 user@sshserver`

Here, any traffic on `5000` port of the ssh server will be forwarded to your local machine port `4000`. Thus your co-worker can access your app via `http://sshserver:5000`.


## Dynamic Port Forwarding

One of the use case of Dynamic Port Forwarding is to access restricted websites. This can be achieved by local port forwarding. However if we are accessing multiple websites we will have to create multiple tunnels. This is where Dynamic Port Forwarding is helpful.

Dynamic port forwarding leverages SOCKS protocol and the tunnel acts as a SOCKS proxy.

`ssh -D 9999 user@sshserver`

Here the SSH server will act as proxy server, where all the requests to port `9999` on the local machine will be forwarded to the SSH server. SSH server will do the rest of the requests on the behalf of your local machine.


![image](https://user-images.githubusercontent.com/37524392/173364544-7939e843-4f63-43d3-87e1-39fe6d53b53d.png)

Suppose the Socks proxy is configured on the browser eg: mozilla firefox. Then when we hit a URL like https:/google.com , request will be sent to port `9999` and is forwarded to the SSH server via the tunnel. Then SSH server will make the request on your behalf.
