# 在Dify中添加自定义工具
## 准备工作
### 本地化部署Dify
参照Dify官方指导使用Docker Compose部署
```
https://docs.dify.ai/v/zh-hans/getting-started/install-self-hosted/docker-compose
```
**Step1** Clone Dify 源码
在合适的位置，文件管理器上方地址栏输入`cmd`，运行
```
git clone https://github.com/langgenius/dify.git
```
**Step2** Start Dify
进入dify的docker目录
```
cd dify/docker
```
一键启动
```
docker compose up -d
```
**Upgrade** 
```
cd dify/docker
git pull origin main
docker compose down
docker compose pull
docker compose up -d 
```

### 使用python编写功能并暴露API
不过多赘述，可以使用FLASK或FASTAPI进行API暴露
```python
from flask import Flask, request, jsonify
import random
import pymysql

app = Flask(__name__)

def connect():
    conn = pymysql.connect(
        host='localhost',
        user=' your user name ',
        password='your mySQL password',
        database=' database name you want to use ',
        charset='utf8'
    )
    if conn.open:
        print('connected to database')
    else:
        print('failed to connect to database')
    return conn

@app.route('/execute', methods=['POST'])
def execute_sql():
    sql = request.json.get('sql', None)
    if not sql:
        return jsonify({'error': 'SQL query is required'}), 400
    conn = connect()
    try:
        cursor = conn.cursor()
        cursor.execute(sql)
        conn.commit()
        backstr = cursor.fetchall()
        print(backstr)
        # Convert the result to a list of dictionaries
        return jsonify({'ans': backstr})
    except pymysql.Error as e:
        return jsonify({'error': str(e)}), 500
    finally:
        conn.close()

if __name__ == '__main__':
    app.run(host='127.0.0.1', port=8001)
```

### 使用ngrok进一步公示地址
这一步是因为本地化docker中部署Dify无法访问内网API，需要使用ngrok进行暴露。
进入ngrok官网，**注册**并**下载**，注册后可以拿到账户鉴权密文，按照官网指示进行一次本地化配置即可。
最后使用 
```
ngrok.exe http [需要暴露的端口]
```
即可完成端口暴露
## 正式在Dify中创建自定义工具
### openai schema
这部分可以参考给出的样例格式，直接让任意大模型（GPT-4、千问已测试可以完成）根据你的程序给出openai schema格式即可，最后将url地址换为ngrok暴露地址。
在下方可用工具中可以进行测试。
## 完成 感谢