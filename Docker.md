### Docker
docker 部署demo

#### rquirements生成
```shell
# 安装
python3 -m pip install pipreqs
# 在当前目录生成
pipreqs . --encoding=utf8 --force
```

#### dockerfile生成
```shell
touch Dockerfile.dockerfile
```
然后在里面写入
```shell
FROM python:3.8

WORKDIR /usr/src/app

COPY requirements.txt ./

RUN pip install -r requirements.txt -i

COPY . .

CMD ["gunicorn", "app:app", "-c", "./gunicorn.conf.py"]
```