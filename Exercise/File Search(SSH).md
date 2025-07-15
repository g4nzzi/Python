```python
import paramiko
import csv

sshCon = paramiko.SSHClient()
sshCon.set_missing_host_key_policy(paramiko.AutoAddPolicy)
sshCon.connect("192.168.11.15", username="lab", password="lab01") # ssh 연결

print('SSH connected.\n')
 
f = open('file.csv', 'r', encoding='cp949')  # 목록 파일
w = open('out.txt', 'w')  # 결과 파일
fname = csv.reader(f)

for line in fname:
    stdin,stdout,stderr = sshCon.exec_command("find " + line[1] + " -name " + line[0]) # find 명령으로 검색
    ret = stdout.readlines()
    
    if not ret:
        w.write(line[1] + line[0] + '\n')  # 검색된 경로, 파일명 쓰기
    
print('done.')

f.close()
w.close()
sshCon.close()
```
