# ReachKart Walkthrough

## Scenario
ReachKart is an e-commerce website running on Ehtereum blockchain technology, to support rapid development without disrupting the live environment, the company implemented a "production mirror" strategy, creating an exact replica of the production environment for development and testing purposes. The mirror includes replicated databases, services and blockchain nodes, but it appears that customer data has not been anonymized. Customer support is now receiving emails from sellers complaining that their wallets are missing ether. You have to investigate and understand if and how an intrusion has occurred.

### Task 1
What was the vulnerable endpoint that allowed the attacker to leak files?

- [ ] To answer this question we can go to Statistics -> Conversations and we filter by the top talkers, and we can for "HTTP/1/1 200 OK" we see the user trying to access sensetive file "GET /user/getOrderBill?template=../../../../etc/passwd HTTP/1.1" which can be classified as **Path Traversal Attack**
<img width="1797" height="959" alt="1" src="https://github.com/user-attachments/assets/a561df5b-000b-490c-94cd-9e6314aad4df" />

### Answer
- [x] /user/getOrderBill

### Task 2
When was the first successful exploitation of the vulnerable endpoint by the attacker (time in UTC)?
 -  [ ] For this make sure you'he converted the display time to UTC  go to: "View -> Time Display Format -> UTC Date and Time of Day"
<img width="1046" height="942" alt="2" src="https://github.com/user-attachments/assets/791f63ac-8788-4a05-b584-96648ce65a77" />

### Answer
- [x] 2025-03-01 04:09:22

### Task 3
Which version of Express is currently being used on the server?
- [ ] This one is tricky, it is said that developer usually obfuscate their server version "to prevent attackers from gaining insights that could help exploit known vulnerabilities". looking at the above packet we see that the sever is "X-Powered-By: Express" and no further details
- [ ] A quick google search on multiple ways to get a server version
- [ ] You can also use the **(http.request.uri contains "package.json")** or **(http.request.uri contains "app.js")** or **(http.request.uri contains "server.js")** wireshark filters etc
<img width="1031" height="948" alt="3" src="https://github.com/user-attachments/assets/02e03c9f-fc12-4759-82a4-b1f9729a9fb3" />

### Answer
- [x] 4.21.2

### Task 4
Which Ethereum compatible development smart contract network is running on the server? (Format: name@version)
- [ ] Okay still on the server information from the above packet we can see that their "eCommerce Web App" is run on the "start-node:"npx hardhat node". Scrolling down we see observer "hardhat":"^2.22.18"
<img width="1760" height="668" alt="200" src="https://github.com/user-attachments/assets/5c53cec9-af20-40c9-aa3b-f98453f5262f" />

### Answer
hardhat@2.22.18

### Task 5
What is the signing key used by the server to sign JSON Web Tokens (JWT)?
- [ ] Taking notes we know that the sever is signed usung "jsonwebtoken": "^9.0.2",
- [ ] Here we can look for **##source code** or **#Configs** and foloow the TCP_Stream
- [ ] Use this filter for configs ("jsonwebtoken") or you can use (http.request.uri contains "configs")
- [ ] You're basically hunting for exposed internal files:
  - [ ] config.js
  - [ ] rk-logging.js
  - [ ] .env
  - [ ] default.json
  - [ ] Anything showing jwt.sign(...) or SECRET_KEY
  <img width="1901" height="889" alt="8" src="https://github.com/user-attachments/assets/37223798-ca00-4444-8602-bccba039ca3e" />

### Answer
- [x] SuperSecretPassword

### Task 6
The attacker was able to generate a JWT from the signing key and log in to the admin panel. What is the JWT value?
- [ ] We are still using the "http" filter, from here you can just navigate the packets and look for "GET /admin/home HTTP/1.1" and the follow the TCP_Stream to the code "200 OK"
<img width="1791" height="942" alt="9" src="https://github.com/user-attachments/assets/e111c8dc-7dbe-46f1-b95e-d3fafc9603ab" />

### Answer
 - [ ] eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJuYW1lIjoiRGFydGggVmFkZXIiLCJlbWFpbCI6ImRhcnRodmFkZXJAZW1waXJlLmNvbSIsImFjY291bnRfdHlwZSI6ImFkbWluIiwiaWF0IjoxNzQwODAyNzM0LCJleHAiOjE3NDA4ODkxMzR9.RBmMEC7IYmkzz1LsT-kP_JkLCUb7-hsJKX4IdjE91TE

### Task 7
Decode the token and find the email used by the attacker to log in to the admin panel.

- [ ] You can copy the JWT to CyberChef.io or any tool of your choice to decode the base64
- [ ] "{"alg":"HS256","typ":"JWT"}{"name":"Darth Vader","email":"darthvader@empire.com","account_type":"admin","iat":1740802734,"exp":1740889134}D....Èbi3ÏRìNCÉ.°.o¸l$¥ø!ØÄ÷TÄ"
<img width="1103" height="738" alt="10" src="https://github.com/user-attachments/assets/ee18bd9f-85f6-41ef-b52a-99110a4db9d4" />

### Answer
- [x] darthvader@empire.com

### Task 8
The admin panel uses WebSocket to send and receive terminal input. What port is being used?
- [ ] Again we can use the 'http contains "conf"' just so we filter to configuration packet, follow TCP_Stram and then CRTL + F to filter for the word "socket", scroll a little bit down and look for the work port, you can also use it for the filter I guess. You come across the admin port 8888.

- [ ] <img width="986" height="1007" alt="11-1" src="https://github.com/user-attachments/assets/bdb7397c-902d-4103-b403-525cf42de393" />
<img width="1744" height="1002" alt="11" src="https://github.com/user-attachments/assets/47dc5aee-caf7-4a66-956b-121e32b323ea" />

### Answer
 - [x] 8888

### Task 9
The attacker then was able to retrieve a sensitive file. When did the attacker get the file (UTC)?
 - [ ] Following the other packets after just after the attacker logged in, just below "GET  /logs/rk-logging HTTP1.1" the attacker ws able to navigate to the data directory "GET /data/reachkart.db HTTP/1.1" and got a hold of the Website's database.
<img width="1907" height="913" alt="11" src="https://github.com/user-attachments/assets/fa1b4d11-1fa6-49d8-8519-5ab0c8a80c64" />

### Answer
 - [x] 2025-03-01 04:22:01

### Task 10
What is the SHA-256 hash of the file that the attacker downloaded ?
- [ ] Simply go to File -> Export Objects -> HTTP, you will see a bunch files just filter for ".db" click save and open PowerShell or a terminal of your choice.
- [ ] Navigate to the folder where you dowloaded the file and run the following command:
- [ ] Get-FileHash .\reachkart.db -Algorithm SHA256
<img width="992" height="976" alt="12" src="https://github.com/user-attachments/assets/d9e4c89e-2b35-48fa-a242-020de30635c1" />
<img width="750" height="547" alt="12-1" src="https://github.com/user-attachments/assets/7bdbb551-37d6-45d8-a8ac-f7c888b48189" />
<img width="920" height="1011" alt="12-2" src="https://github.com/user-attachments/assets/e23aea37-feb5-485f-a8eb-11a99396af9d" />

### Answer
 - [x] FABE3234BB709EE5E5C5C2789C891A9A49368FFA520B23D60F6BE2F2CA81BAC6

### Task 11
How many sellers are there in the e-commerce website?
 - [ ] **PowerShell** and **SQLite** came in handy here, by running the following command I was able to get the number of sellers
 - [ ] PS C:\Windows\system32> sqlite3 "C:\Users\Fessa10\Desktop\ReachKart\reachkart.db"
 - [ ] sqlite> .tables
 - [ ] sqlite> .schema users
 - [ ] sqlite> SELECT COUNT(*) FROM users WHERE account_type = 'seller';
<img width="954" height="292" alt="13" src="https://github.com/user-attachments/assets/531944da-8206-4aee-bd90-4d9d0dcddc68" />

### Answer
 - [x] 8

### Task 12
The attacker started sending Ether from all identified sellers' wallets. What is the hash of the first transaction?
### Answer
******************************************************************

### Task 13
What was the total amount of Ether stolen by the attacker? (1 Eth = 10^18 wei)
### Answer
**.*

### Task 14
What is the block number of the last transaction in which Ether was stolen? (Decimal)
### Answer
number, such as 3, 17, or 4567

### Task 15
After the attacker stole the Ether, what was the balance in their wallet? (Ignore the trailing zeros)
### Answer
