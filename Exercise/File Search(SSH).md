import paramiko
import csv

sshCon = paramiko.SSHClient()
sshCon.set_missing_host_key_policy(paramiko.AutoAddPolicy)
sshCon.connect("192.168.11.15", username="lab", password="lab01")

print('SSH connected.\n')
 
f = open('file.csv', 'r', encoding='cp949')
w = open('out.txt', 'w')
fname = csv.reader(f)

for line in fname:
    stdin,stdout,stderr = sshCon.exec_command("find " + line[1] + " -name " + line[0])
    ret = stdout.readlines()
    
    if not ret:
        w.write(line[1] + line[0] + '\n')
    
print('done.')

f.close()
w.close()
sshCon.close()
