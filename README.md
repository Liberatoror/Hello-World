# Hello-World
The First Repository
import time
import pandas as pd
import os
import json
import requests
from urllib.request import quote,unquote
import time
import random
import traceback
from fake_useragent import UserAgent
ua=UserAgent()
os.chdir('D:\\舆情分析')
token='1627230491'
cookie='RK=sDjp7BqJGz; ptcz=757deb62fb303271d02dd6b1cfcbd37ed31701632375da633b1469d0eed8acf3; pgv_pvi=2974212096; pgv_pvid=8610608852; pac_uid=0_5de4bc1a4438f; ua_id=cgmwYaWRdSRK5mwxAAAAAGn2MnyAg6j4BPPbwOBWb7Q=; ptui_loginuin=616059859; noticeLoginFlag=1; remember_acct=616059859%40qq.com; mm_lang=zh_CN; pgv_si=s4588013568; uuid=0d6dae5cf4aca6acccdf68662b0cd285; bizuin=3205937962; ticket=8cd9a31b1f6bccdb401552aa1b8725239f2eb790; ticket_id=gh_8c66739a9527; cert=z148NYfYD3ho63LgKHF0xhhvPdmJDtKA; data_bizuin=3205937962; data_ticket=nSbRBmEncvPdl/NJaDQp+X93rzdpDIFLjf0rXi7VS98Mgf5OwgjPM3WtLRBreq9/; slave_sid=MVpyUHE0TGVRZlNtVl9ZMm5xdlhZSXFuUVdhZl9lUlpEOTRFeExYWWFuWDk3QmdlcXBQYUVZNUsxSlJ1aGd6dmVkTmM1eEx2QTZwcTZ4SmdMOGFxZWVMYVBlNDRObW1KZUdnUmZsVnlGNkYwS0tCcHVqdTRsYmF3WnlEUXZBc0FKQ24wVTR6emFDMkRraEd5; slave_user=gh_8c66739a9527; xid=6f4ea9cc2a973eb1b9804d41dfa95b99; openid2ticket_oKrsWv6mwvuH2a-8YrdJy6vdlCHU=FcbFybLlSC7ZaO+VZ85R3Q0AtOS3UrAabvWtRiw0xts=; rewardsn=; wxtokenkey=777'
begin=time.time()
file=pd.read_excel('公众号id.xlsx')
item_list=[]
for i in range(file.shape[0]):
    try:
        data={
            'action':'list_ex',
            'begin':'0',
            'count':'10',
            'fakeid':file['fakeid'].iloc[i],
            'token':token,
            'type':'9',
            'lang':'zh_CN',
            'f':'json',
            'ajax':'1'}
        headers={
            'User-Agent':ua.random,
            'Cookie':cookie}
        res=requests.get('https://mp.weixin.qq.com/cgi-bin/appmsg',params=data,headers=headers)
        info=json.loads(res.text)
        info=info['app_msg_list']
        print(info[0])
        for item in info:
            result = {
            'title':item['title'],
            'digest':item['digest'],
            'content_url':item['link'],
            'datetime':time.strftime("%Y-%m-%d",time.localtime(item['update_time']))
            }
            if result['datetime']==time.strftime("%Y-%m-%d",time.localtime()):
                sub_result = {
                    "公众号": '<br/>' +file['微信公众号中文名'].iloc[i],
                    "题目+超链接": "[" + result['title'] + "]" + "(" +result['content_url'] + ")",
                    "日期": result['datetime'] + "</br>",
                    "摘要": result['digest'],
                    "超链接": result['content_url']}
                item_list.append(sub_result)
        time.sleep(random.randint(7,15))
    except:
        traceback.print_exc()
        print(res.text)
        break
end=time.time()
print("跑完需要时间" + str(round((end - begin) / 60, 1)) + "分钟")
filedir = os.getcwd() + "/环保舆情日报"
if not os.path.exists(filedir):
    os.mkdir(filedir)
dd = pd.DataFrame(item_list)
filename = "/舆情日报" + time.strftime("%Y%m%d",time.localtime()) + ".xlsx"
#if filename:
    #filename =  "/舆情日报" + time.strftime("%Y%m%d %H-%M-%S",time.localtime()) + ".xls"
dd.to_excel( filedir + filename,columns=["公众号", "题目+超链接", "日期","摘要", "超链接"])
print('完毕')
