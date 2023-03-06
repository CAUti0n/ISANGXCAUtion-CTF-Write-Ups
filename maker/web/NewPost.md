# New Post

![Untitled](https://user-images.githubusercontent.com/37824335/223042971-24ae5844-eee4-4166-9f1a-c5d29bec4ecd.png)

# # 문제 파악

![Untitled 1](https://user-images.githubusercontent.com/37824335/223042990-b0854c1b-abd9-4be8-854f-911750c4063b.png)

문제 페이지 접속 시 위와 같은 문구를 확인할 수 있다. 

```python
from flask import Flask, request

FLAG = 'IxC{???}'

app = Flask(__name__)

@app.route('/', methods=['GET', 'POST'])
def index():

    if request.method == 'GET':
        return 'Hello IxC participant!'

    return f'Flag is {FLAG}'

if __name__ == '__main__':
    app.run(host="0.0.0.0", port=5000)
```

문제와 함께 주어진 `app.py`를 확인해보면 간단한 Flask 프로그램을 볼 수 있다.

GET 요청시 페이지에 보이는 문자열이 보이는 것으로 보인다.

GET 요청 말고 다른 메소드로 요청을 보내보자.

# # Exploit

리눅스의 `cURL` 라이브러리를 이용하여 간단하게 POST 요청을 보내보자

```bash
curl -X POST http://web.isangxcaution.xyz:20476
```

![Untitled 2](https://user-images.githubusercontent.com/37824335/223043025-857bf189-0b96-4199-b12e-52c3b02dc54d.png)

간단하게 FLAG를 획득할 수 있다.
