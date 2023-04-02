import requests
import json
import time
from PIL import Image
import os
from datetime import datetime

# 输入apikey
apikey = input('请输入你的Astrometry.net账号的apikey:')

# 通过apikey获取session
R = requests.post('http://nova.astrometry.net/api/login', data={'request-json': json.dumps({
    "apikey": apikey
})})
session = R.json()["session"]

# 输入需解析的图像文件路径
file_path = input('需要解析的图像文件路径：')

# 上传文件
    # 请求参数
request_data = {
    "publicly_visible": "y",
    "allow_modifications": "d",
    "session": session,
    "allow_commercial_use": "d"
}

    # 将请求参数转为JSON字符串
request_json = json.dumps(request_data)

    # 读取文件
with open(file_path, 'rb') as file_data:
    # 创建multipart/form-data表单
    form_data = {
        'request-json': (None, request_json, 'text/plain'),
        'file': (file_path, file_data, 'application/octet-stream')
    }

    # 发送POST请求
    R = requests.post('http://nova.astrometry.net/api/upload', files=form_data)

    # 检查响应是否成功
if R.status_code == 200:
    print("文件上传成功")
    print(R.json())
else:
    print("文件上传失败")
    print(R.status_code, R.text)

# 上传文件后等待60s，让接口解析图片
time.sleep(60)

# 通过"subid"，获取"jobs"
SUBID = R.json()["subid"]
R = requests.get('http://nova.astrometry.net/api/submissions/'+str(SUBID))
print(R.json())

# 获取"jobs"，赋值给JOBID
JOBID = R.json()["jobs"][0]

# 通过JOBID，获取job status
R = requests.get('http://nova.astrometry.net/api/jobs/'+str(JOBID))
print(R.json())

# 通过JOBID，获取job results: calibration
R = requests.get('http://nova.astrometry.net/api/jobs/'+str(JOBID)+'/calibration/')
print(R.json())

# 通过JOBID，获取job results: tagged objects in your image
R = requests.get('http://nova.astrometry.net/api/jobs/'+str(JOBID)+'/tags/')
print(R.json())

# 通过JOBID，获取job results: tagged objects in your image(machine_tags)
R = requests.get('http://nova.astrometry.net/api/jobs/'+str(JOBID)+'/machine_tags/')
print(R.json())

# 通过JOBID，获取job results: known objects in your image
R = requests.get('http://nova.astrometry.net/api/jobs/'+str(JOBID)+'/objects_in_field/')
print(R.json())

# 通过JOBID，获取job results: known objects in your image, with coordinates
R = requests.get('http://nova.astrometry.net/api/jobs/'+str(JOBID)+'/annotations/')
print(R.json())

# 通过JOBID，获取job results
R = requests.get('http://nova.astrometry.net/api/jobs/'+str(JOBID)+'/info/')
print(R.json())

# 获取解析后图像文件，按照时间命名，且保存在桌面下同样以时间命名的文件夹中 
R = requests.get('https://nova.astrometry.net/annotated_full/'+str(JOBID))
if R.status_code == 200:
    image_content = R.content
else:
    print("检索图像内容时出错")


timestamp = datetime.now().strftime("%Y-%m-%d_%H-%M-%S")
folder_path = os.path.expanduser("~/Desktop/" + timestamp)
os.makedirs(folder_path)


if 'image_content' in locals():
    image_name = datetime.now().strftime("%Y-%m-%d_%H-%M-%S") + ".jpg"
    image_path = os.path.join(folder_path, image_name)
    with open(image_path, "wb") as f:
        f.write(image_content)
else:
    print("保存图像时出错：未定义image_content变量")

print("解析后图像下载成功！")
