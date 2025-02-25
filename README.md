# GZCTF-BOT-QQ 
## 介绍
GZCTF对接的qq机器人，基于NapCat+Nonebot框架  
3血播报，赛题新提示播报，新赛题上线播报，新公告播报，自动封禁作弊者及播报等等...  
注: 赛事不要使用相同的比赛名以及相同的赛题名，机器人没有考虑这个情况，可能会有各种问题  
所有功能只在已开放的赛事运作，未开放的赛事无法运作  
目前已经全量完成功能！  
有想要的功能或者有bug可以提issues，提供详细错误日志  

## 适配须知
在gz的老版本中，比赛中的公屏事件键名为`content`，新版本中键名为`value`, 本机器人始终适配新版本，如果需要适配老版本，请自行修改  

2024.9.12后的某次更新中，赛题的类型键名由`tag`改为`category`, 本机器人适配新版本，如果需要适配老版本，请自行修改

## 开发计划
| 功能          | 状态  |
|-------------|-----|
| 3血播报        | 已完成 |
| 赛题新提示播报     | 已完成 |
| 新赛题上线播报     | 已完成 |
| 新公告播报       | 已完成 |
| 自动封禁作弊者及播报  | 已完成 |
| 用户自助解锁队伍    | 已完成 |
| 排行榜查询       | 已完成 |
| 队伍当前排名及分数查询 | 已完成 |
| 题目当前解出信息查询  | 已完成 |
| 用户查询所有开放赛题  | 已完成 |
| 查询队伍        | 已完成 |
| 管理员查看所有赛题   | 已完成 |
| 管理员ban队伍    | 已完成 |
| 管理员重置用户密码   | 已完成 |
| 管理员开放/关闭赛题  | 已完成 |
| 管理员添加赛题提示   | 已完成 |
| 管理员添加赛事公告   | 已完成 |
| 分群分赛事播报     | 已完成 |
## 使用方法
环境: Ubuntu 22.04
### docker compose安装机器人
```yaml
version: "3.7"
services:
  napcat:
    image: mlikiowa/napcat-docker:latest
    restart: always
    ports:
      - "6099:6099"
    environment:
      - "ACCOUNT=your_account"  #机器人的qq号
      - "WSR_ENABLE=true"
      - "WS_URLS=\"ws://bot:8988/onebot/v11/ws/\""
    volumes:
      - "./napcat/app:/usr/src/app/napcat"
      - "./napcat/config:/usr/src/app/napcat/config"
    depends_on:
      - bot

  bot:
    image: registry.cn-hangzhou.aliyuncs.com/moran233/nn:GZBOT
    restart: always
    environment:
      - "SEND_LIST=123456,1234567"  #监听qq群号
      - "GAME_LIST=\"123\",\"1234\""  #监听赛事名
      - "GZCTF_URL=http://xx.xx.xx.xx/"  #GZCTF网址
      - "GZ_USER=your_admin"  #GZCTF管理员用户名
      - "GZ_PASS=your_password"  #GZCTF管理员密码
      - "SUPER=\"123123123\",\"234234234\""  #机器人管理员qq号
```
将上述内容保存为`docker-compose.yml`文件  
```bash
docker compose up -d
```
随后使用下列命令扫码登录
```bash
docker compose logs napcat
```
如果提示失效，请再次输入上述命令查看更新后的二维码  
或者通过`napcat/app/qrcode.png`扫码登录  
或者打开网站`http://[此处替换为本机IP]:6099/webui`扫码登录  
初始登录`token`在`napcat/config/webui.json`中
#### 更新
```bash
docker compose down
docker compose pull
docker compose up -d
```
随后再次执行登录操作
### 手动安装:
#### Docker 容器安装机器人
仅启动机器人，不包含NapCatQQ  
将下文修改好的 `config.py` 和 `.env` 文件复制到同一目录下，并进入目录，执行如下命令  
```bash
docker run -d \
-p 8988:8988 \
-e SEND_LIST=123456,1234567 \
-e GAME_LIST="'123','1234'" \
-e GZCTF_URL=http://xx.xx.xx.xx/ \
-e GZ_USER=your_admin \
-e GZ_PASS=your_password \
-e SUPER='"123123123","234234234"' \
--name gzctf-bot-qq \
--restart=always \
registry.cn-hangzhou.aliyuncs.com/moran233/nn:GZBOT
```
或者  
下载项目并解压，进入项目文件夹
#### 1. 安装python3.10
```bash
sudo apt install software-properties-common
sudo add-apt-repository ppa:deadsnakes/ppa
apt update
apt install python3.10
```
#### 2. 安装依赖  
建议使用虚拟环境
```bash
sudo apt install python3.10-venv
sudo apt install libgbm1 libasound2
python3 -m venv bot
source bot/bin/activate
cd gz-bot
pip install -r requirements.txt
nb plugin install nonebot_plugin_apscheduler
```
`pip`下不动的换源   
```bash
pip install -i https://mirrors.ustc.edu.cn/pypi/web/simple -r requirements.txt
```
#### 3. 安装Docker
```bash
apt install apt-transport-https ca-certificates
curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository -y "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get -y update
sudo DEBIAN_FRONTEND=noninteractive apt-get -y --no-install-recommends install docker-ce docker-compose-plugin
```
##### docker换源
```bash
vim /etc/docker/daemon.json
```
填入以下内容，保存  
```json
{
    "registry-mirrors": [
        "https://hub.docker-alhk.dkdun.com/"
    ]
}
```
重启docker
```bash
sudo systemctl daemon-reload && sudo systemctl restart docker
```
#### 4. 安装NapCatQQ并登录  
[NapCatQQ官网](https://napneko.github.io/zh-CN/guide/getting-started "NapCatQQ官网")  
在用户目录新建文件夹`napcat`并进入
```bash
docker run -d \
-e ACCOUNT=3766745185 \
-e WSR_ENABLE=true \
-e WS_URLS='["ws://[此处替换为本机IP]:8988/onebot/v11/ws/"]' \
-v ./napcat/app:/usr/src/app/napcat \
-v ./napcat/config:/usr/src/app/napcat/config \
-p 6099:6099 \
--name napcat \
--restart=always \
mlikiowa/napcat-docker:latest
```
随后使用下列命令扫码登录
```bash
docker logs napcat
```
如果提示失效，请再次输入上述命令查看更新后的二维码  
或者通过`napcat/app/qrcode.png`扫码登录  
或者打开网站`http://[此处替换为本机IP]:6099/webui`扫码登录  
初始登录`token`在`napcat/config/webui.json`中  
如果全部失败，删除`docker`容器重新执行上述命令  
```bash
docker stop napcat
docker rm napcat
```
请确保登录成功  
  
##### 配置`ACCESS_TOKEN`:  
编辑`napcat/config/onebot11_[你的QQ号].json`  
找到`token`字段, 如下所示:  
```json
{
  ............
  "token": "",
  ............
}
```
填入一段`token`值(什么都行随便填), 例如:
```json
{
  ............
  "token": "GZCTFBOT xsaFFAFSaaxa",
  ............
}
```
保存`token`值  
#### 5. 配置机器人
配置文件在`gzctf-bot/plugins/gzctf_bot_qq/config.py`中  
```python
CONFIG: dict = {
        "SEND_LIST": [],
        "GAME_LIST": [],
        "GZCTF_URL": "",
        "GZ_USER": "",
        "GZ_PASS": "",
    }
```
`SEND_LIST`存放监听/发送消息的群号  
示例:
```python
SEND_LIST = [123456789, 987654321]
```
`GAME_LIST`存放需要监听的比赛名称，为空则监控所有比赛  
示例:
```python
GAME_LIST = ["GZCTF2022","GZCTF2023"]
```
`GZCTF_URL`存放GZCTF的网站URL  
示例:
```python
GZCTF_URL = "http://gzctf.cn"
```
`GZ_USER`和`GZ_PASS`存放GZCTF的管理员账号密码  
示例:
```python
GZ_USER = "admin"
GZ_PASS = "Test123."
```
机器人管理员权限配置文件为`.env`文件
```
//.env
HOST=0.0.0.0
PORT=8988
COMMAND_START=["/"]
COMMAND_SEP=["."]
ONEBOT_ACCESS_TOKEN=XXX
SUPERUSERS='[""]'
```
管理员qq号填入`SUPERUSERS`中  
示例:
```
SUPERUSERS='["123456789","987654321"]'
```
将步骤4中自行设置的`token`填入`ONEBOT_ACCESS_TOKEN`中, 例如:
```
ONEBOT_ACCESS_TOKEN=GZCTFBOT xsaFFAFSaaxa
```
保存`.env`文件  
#### 6. 启动机器人
```bash
nohup python3 bot.py >bot.log 2>&1 &
```
机器人的日志将会存放到`bot.log`文件中
### 更新
#### napcatQQ更新
保存好`napcat/config/onebot11_[你的QQ号].json`文件  
删除容器
```bash
docker stop napcat && docker rm napcat
```
重新执行上方安装步骤  
然后将`onebot11_[你的QQ号].json`文件复制到`napcat/config/`目录下覆盖  
最后登录QQ  
#### 机器人更新
保存好`gzctf-bot/plugins/gzctf_bot_qq/config.py`和`.env`文件  
重新拉取项目压缩包并解压  
然后将`config.py`和`.env`文件覆盖  
然后进入虚拟环境重新启动bot  
```bash
source bot/bin/activate
nohup python3 bot.py >bot.log 2>&1 &
```
## 赞助鸣谢
### DKDUN
<img src="https://cdn.moran233.xyz/https://raw.githubusercontent.com/MoRan23/moran/main/QQ%E5%9B%BE%E7%89%8720240630210148.png" alt="DKDUN 图标" width="150" height="150">
官网：https://www.dkdun.cn/  

ctf专区群: 727077055  
<img src="https://cdn.moran233.xyz/https://raw.githubusercontent.com/MoRan23/moran/main/20240630210630.png" alt="DKDUN-CTF QQ群" width="150" height="150">  
公众号：DK盾
  
dkdun 推出 ctfer 赞助计划  
为各位热爱 ctf 的师傅提供优惠服务器  
详情查看：https://mp.weixin.qq.com/s/38xWMM1Z0sO6znfg9TIklw
  
更多服务器优惠请入群查看！